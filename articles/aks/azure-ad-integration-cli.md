---
title: Integrar o Azure Active Directory ao Serviço de Kubernetes do Azure
description: Saiba como usar a CLI do Azure para criar e o cluster de serviço ativado pelo Azure Active Directory do Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: iainfou
ms.openlocfilehash: c403d809aa1fc6cdbb0555eef414f79b02ab5a8a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764129"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Integrar o Azure Active Directory com o serviço de Kubernetes do Azure usando a CLI do Azure

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nessa configuração, você pode registrar em um cluster AKS usando um token de autenticação do AD do Azure. Operadores de cluster também podem configurar Kubernetes controle de acesso baseado em função (RBAC) com base na associação de grupo de diretório ou a identidade do usuário.

Este artigo mostra como criar os necessários componentes do Azure AD, em seguida, implantar um cluster do Azure AD habilitado e criar uma função RBAC básica no cluster AKS. Você também pode [concluir essas etapas usando o portal do Azure][azure-ad-portal].

Para o script de exemplo completo usado neste artigo, consulte [exemplos de CLI do Azure – integração do AKS com o Azure AD][complete-script].

As seguintes limitações se aplicam:

- O Azure AD só pode ser habilitado quando você cria um cluster novo habilitado para RBAC. Não é possível habilitar o Azure AD em um cluster existente do AKS.
- *Convidado* usuários no Azure AD, tais como se você usar uma entrada federada de um diretório diferente, não têm suporte.

## <a name="before-you-begin"></a>Antes de começar

Você precisa da CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

Para manter a consistência e para ajudar a executar os comandos neste artigo, crie uma variável para o nome do cluster AKS desejado. O exemplo a seguir usa o nome *myakscluster*:

```azurecli-interactive
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Visão geral de autenticação do AD do Azure

A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Mais informações sobre OpenID Connect podem ser encontradas na [documentação do Open ID Connect][open-id-connect].

No cluster do Kubernetes, a autenticação de token do Webhook é usada para verificar os tokens de autenticação. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS. Mais informações sobre autenticação de token do Webhook podem ser encontradas na [documentação de autenticação do webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicativos do Azure AD são configurados. Essa operação deve ser concluída por um administrador de locatário do Azure.

## <a name="create-azure-ad-server-component"></a>Criar um componente de servidor do AD do Azure

Para integrar com o AKS, você pode cria e usa um aplicativo do Azure AD que atua como um ponto de extremidade para as solicitações de identidade. O primeiro aplicativo do Azure AD, que você precisa obtém a associação de grupo do AD do Azure para um usuário.

Criar o componente de aplicativo de servidor usando o [criar aplicativo do ad az] [ az-ad-app-create] comando, em seguida, atualizar a associação de grupo de declarações usando o [atualização de aplicativo do ad az] [ az-ad-app-update] comando. O exemplo a seguir usa o *aksname* variável definida na [antes de começar](#before-you-begin) seção e cria uma variável

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Agora, crie uma entidade de serviço para o aplicativo de servidor usando o [criar az ad sp] [ az-ad-sp-create] comando. Essa entidade de serviço é usado para autenticar-se na plataforma Azure. Em seguida, obtenha a entidade de serviço secreto usando o [redefinir credencial do az ad sp] [ az-ad-sp-credential-reset] de comando e atribuir à variável chamada *serverApplicationSecret* para uso em um do seguintes etapas:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

O Azure AD precisa de permissões para executar as seguintes ações:

* Ler dados do diretório
* Entrar e ler o perfil do usuário
* Ler dados do diretório

Atribuir essas permissões usando o [adicionar permissão de aplicativo do ad az] [ az-ad-app-permission-add] comando:

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Por fim, conceda as permissões atribuídas na etapa anterior para o aplicativo de servidor usando o [concessão de permissão do az ad app] [ az-ad-app-permission-grant] comando. Essa etapa falhará se a conta atual não é um administrador de locatário. Você também precisa adicionar permissões de aplicativo do Azure AD solicitar informações que, caso contrário, podem exigir consentimento administrativo usando o [az ad app permissão-consentimento do administrador][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Criar um componente de cliente do AD do Azure

O segundo aplicativo do Azure AD é usado quando um usuário faz logon no cluster AKS com a CLI Kubernetes (`kubectl`). Esse aplicativo cliente usa a solicitação de autenticação do usuário e verifica suas credenciais e permissões. Criar o aplicativo do Azure AD para o componente de cliente usando o [criar aplicativo do ad az] [ az-ad-app-create] comando:

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Criar uma entidade de serviço para o aplicativo cliente usando o [criar az ad sp] [ az-ad-sp-create] comando:

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Obter a ID do oAuth2 para o aplicativo de servidor para permitir que o fluxo de autenticação entre os componentes de dois aplicativo usando o [Mostrar de aplicativo do ad az] [ az-ad-app-show] comando. Essa ID de oAuth2 é usada na próxima etapa.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adicionar as permissões para o aplicativo cliente e componentes de aplicativos de servidor para usar a comunicação de oAuth2 fluem usando o [adicionar permissão de aplicativo do ad az] [ az-ad-app-permission-add] comando. Em seguida, conceda permissões para o aplicativo cliente para a comunicação com o aplicativo de servidor usando o [concessão de permissão do az ad app] [ az-ad-app-permission-grant] comando:

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions $oAuthPermissionId=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Implantar o cluster

Com os dois aplicativos do Azure AD criados, agora crie o cluster do AKS em si. Primeiro, crie um grupo de recursos usando o [criar grupo de az] [ az-group-create] comando. O exemplo a seguir cria o grupo de recursos na *EastUS* região:

Crie um grupo de recursos para o cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Obter a ID de locatário da sua assinatura do Azure usando o [show do az conta] [ az-account-show] comando. Em seguida, crie o cluster AKS usando o [criar az aks] [ az-aks-create] comando. O comando para criar o cluster do AKS fornece o servidor e cliente IDs de aplicativo, o segredo de principal do serviço de aplicativo de servidor e sua ID de locatário:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Por fim, obtenha o cluster de credenciais de administrador usando o [az aks get-credentials] [ az-aks-get-credentials] comando. Em uma das etapas a seguir, você obtém normal *usuário* as credenciais para ver a autenticação do Azure AD de cluster fluxo em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Criar associação do RBAC

Antes que uma conta do Azure Active Directory possa ser usada com o cluster do AKS, uma associação de função ou uma associação de função de cluster precisa ser criada. *Funções* definem as permissões a serem concedidas e *ligações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [Usando a autorização do RBAC][rbac-authorization].

Obter o nome de usuário principal (UPN) para o usuário conectado no momento usando o [Mostrar assinado no usuário do ad az] [ az-ad-signed-in-user-show] comando. Essa conta de usuário está habilitada para a integração do Azure AD na próxima etapa.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se o usuário que você conceder a associação de RBAC para estiver no mesmo locatário do AD do Azure, atribua permissões com base nas *userPrincipalName*. Se o usuário estiver em um Azure AD diferente de locatário, consultar e usar o *objectId* propriedade em vez disso.

Criar um manifesto YAML chamado `basic-azure-ad-binding.yaml` e cole o seguinte conteúdo. Na última linha, substitua *userPrincipalName_or_objectId* com a saída de ID de objeto ou UPN do comando anterior:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Crie o ClusterRoleBinding usando o [aplicar kubectl] [ kubectl-apply] de comando e especifique o nome do arquivo de manifesto do YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Acessar cluster com Azure AD

Agora vamos testar a integração da autenticação do Azure AD para o cluster do AKS. Defina o `kubectl` contexto de configuração para usar credenciais de usuário normal. Nesse contexto passa todas as solicitações de autenticação por meio do Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Agora use o [kubectl get pods] [ kubectl-get] comando para exibir os pods entre todos os namespaces:

```console
kubectl get pods --all-namespaces
```

Você recebe um sinal no prompt para se autenticar usando credenciais do Azure AD usando um navegador da web. Depois de autenticado com êxito, o `kubectl` comando exibe os pods no cluster do AKS, conforme mostrado na saída de exemplo a seguir:

```console
$ kubectl get pods --all-namespaces

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

O token de autenticação recebidas para `kubectl` é armazenado em cache. Você só é solicitado a fornecer entrar quando o token tiver expirado ou o arquivo de configuração do Kubernetes é recriado.

Se você vir uma mensagem de erro de autorização, depois que você entrar com êxito usando um navegador da web, como na seguinte saída de exemplo, verifique os seguintes possíveis problemas:

```console
error: You must be logged in to the server (Unauthorized)
```

* O usuário estiver conectado em não é um *convidado* na instância do AD do Azure (Isso é geralmente o caso se você usar um logon federado de um diretório diferente).
* O usuário não é um membro de mais de 200 grupos.

## <a name="next-steps"></a>Próximas etapas

Para o script completo que contém os comandos mostrados neste artigo, consulte a [repositório de amostras de script de integração do Azure AD no AKS][complete-script].

Para usar grupos e usuários do AD do Azure para controlar o acesso aos recursos do cluster, consulte [controlar o acesso aos recursos de cluster usando o controle de acesso baseado em função e identidades do Azure AD no AKS][azure-ad-rbac].

Para obter mais informações sobre como proteger os clusters Kubernetes, consulte [opções de acesso e identidade para AKS)][rbac-authorization].

Para as práticas recomendadas de controle de identidade e recursos, consulte [práticas recomendadas para autenticação e autorização no AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
