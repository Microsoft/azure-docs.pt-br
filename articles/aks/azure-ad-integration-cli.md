---
title: Integrar Azure Active Directory com o serviço kubernetes do Azure (Herdado)
description: Saiba como usar o CLI do Azure para criar e o cluster AKS (serviço kubernetes do Azure) habilitado para Azure Active Directory (Herdado)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: 055afe24cb72b331e64e1a2b3d786503ef31a105
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176392"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrar Azure Active Directory com o serviço kubernetes do Azure usando o CLI do Azure (Herdado)

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nessa configuração, você pode fazer logon em um cluster AKS usando um token de autenticação do Azure AD. Os operadores de cluster também podem configurar o controle de acesso baseado em função do kubernetes (kubernetes RBAC) com base na identidade do usuário ou no grupo de diretórios.

Este artigo mostra como criar os componentes necessários do Azure AD e, em seguida, implantar um cluster habilitado para o Azure AD e criar uma função kubernetes básica no cluster AKS.

Para obter o script de exemplo completo usado neste artigo, consulte [CLI do Azure Samples-integração AKs com o Azure ad][complete-script].

> [!Important]
> O AKS tem uma nova experiência aprimorada [do Azure ad gerenciada pelo AKs][managed-aad] que não exige que você gerencie o servidor ou o aplicativo cliente. Se você quiser migrar, siga as instruções [aqui][managed-aad-migrate].

## <a name="the-following-limitations-apply"></a>As seguintes limitações se aplicam:

- O Azure AD só pode ser habilitado no cluster habilitado para RBAC kubernetes.
- A integração herdada do Azure AD só pode ser habilitada durante a criação do cluster.

## <a name="before-you-begin"></a>Antes de começar

Você precisará da CLI do Azure versão 2.0.61 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

Acesse [https://shell.azure.com](https://shell.azure.com) para abrir o Cloud Shell no navegador.

Para fins de consistência e para ajudar a executar os comandos neste artigo, crie uma variável para o nome do cluster AKS desejado. O exemplo a seguir usa o nome *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Visão geral da autenticação do Azure AD

A autenticação do Azure AD é fornecida aos clusters do AKS com OpenID Connect. O OpenID Connect é uma camada de identidade compilada sobre o protocolo OAuth 2.0. Para obter mais informações sobre o OpenID Connect, consulte a [documentação do Open ID Connect][open-id-connect].

No cluster do Kubernetes, a autenticação de token do Webhook é usada para verificar os tokens de autenticação. A autenticação de token do Webhook é configurada e gerenciada como parte do cluster AKS. Mais informações sobre autenticação de token do Webhook podem ser encontradas na [documentação de autenticação do webhook][kubernetes-webhook].

> [!NOTE]
> Ao configurar o Azure AD para autenticação do AKS, dois aplicativos do Azure AD são configurados. Essa operação deve ser concluída por um administrador de locatário do Azure.

## <a name="create-azure-ad-server-component"></a>Criar componente de servidor do Azure AD

Para integrar com o AKS, você cria e usa um aplicativo do Azure AD que atua como um ponto de extremidade para as solicitações de identidade. O primeiro aplicativo do Azure AD de que você precisa Obtém a associação de grupo do Azure AD para um usuário.

Crie o componente de aplicativo do servidor usando o comando [AZ ad app Create][az-ad-app-create] e, em seguida, atualize as declarações de associação de grupo usando o comando [AZ ad app Update][az-ad-app-update] . O exemplo a seguir usa a variável *aksname* definida na seção [antes de começar](#before-you-begin) e cria uma variável

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Agora, crie uma entidade de serviço para o aplicativo de servidor usando o comando [AZ ad SP Create][az-ad-sp-create] . Essa entidade de serviço é usada para se autenticar na plataforma do Azure. Em seguida, obtenha o segredo da entidade de serviço usando o comando [AZ ad SP Credential Reset][az-ad-sp-credential-reset] e atribua à variável chamada *serverApplicationSecret* para uso em uma das seguintes etapas:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

A entidade de serviço do Azure AD precisa de permissões para executar as seguintes ações:

* Ler dados do diretório
* Entrar e ler o perfil do usuário

Atribua essas permissões usando o comando [AZ ad app Permission Add][az-ad-app-permission-add] :

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Por fim, conceda as permissões atribuídas na etapa anterior para o aplicativo de servidor usando o comando [AZ ad app Permission Grant][az-ad-app-permission-grant] . Essa etapa falhará se a conta atual não for um administrador de locatário. Você também precisa adicionar permissões para que o aplicativo do Azure AD solicite informações que, de outra forma, podem exigir consentimento administrativo usando a [permissão do aplicativo AZ ad admin-consentimento][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Criar componente de cliente do Azure AD

O segundo aplicativo do Azure AD é usado quando um usuário faz logon no cluster AKS com a CLI do kubernetes ( `kubectl` ). Esse aplicativo cliente usa a solicitação de autenticação do usuário e verifica suas credenciais e permissões. Crie o aplicativo do Azure AD para o componente cliente usando o comando [AZ ad app Create][az-ad-app-create] :

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Crie uma entidade de serviço para o aplicativo cliente usando o comando [AZ ad SP Create][az-ad-sp-create] :

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Obtenha a ID de oAuth2 para o aplicativo de servidor para permitir o fluxo de autenticação entre os dois componentes de aplicativo usando o comando [AZ ad App Show][az-ad-app-show] . Essa ID de oAuth2 é usada na próxima etapa.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Adicione as permissões para o aplicativo cliente e componentes de aplicativo de servidor para usar o fluxo de comunicação oAuth2 usando o comando [AZ ad app Permission Add][az-ad-app-permission-add] . Em seguida, conceda permissões para o aplicativo cliente se comunicar com o aplicativo de servidor usando o comando [AZ ad app Permission Grant][az-ad-app-permission-grant] :

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Implantar o cluster

Com os dois aplicativos do Azure AD criados, agora crie o próprio cluster AKS. Primeiro, crie um grupo de recursos usando o comando [AZ Group Create][az-group-create] . O exemplo a seguir cria o grupo de recursos na região *eastus* :

Crie um grupo de recursos para o cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Obtenha a ID de locatário da sua assinatura do Azure usando o comando [AZ Account show][az-account-show] . Em seguida, crie o cluster AKS usando o comando [AZ AKs Create][az-aks-create] . O comando para criar o cluster AKS fornece as IDs de aplicativo do servidor e do cliente, o segredo da entidade de serviço de aplicativo do servidor e sua ID de locatário:

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

Por fim, obtenha as credenciais de administrador do cluster usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Em uma das etapas a seguir, você obtém as credenciais de cluster de *usuário* regular para ver o fluxo de autenticação do Azure AD em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Criar Associação de RBAC kubernetes

Antes que uma conta do Azure Active Directory possa ser usada com o cluster do AKS, uma associação de função ou uma associação de função de cluster precisa ser criada. *Funções* definem as permissões a serem concedidas e *ligações* as aplicam aos usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [usando a autorização do RBAC kubernetes][rbac-authorization].

Obtenha o nome UPN do usuário conectado no momento usando o comando [AZ ad logind-User show][az-ad-signed-in-user-show] . Esta conta de usuário está habilitada para a integração do Azure AD na próxima etapa.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Se o usuário para o qual você conceder a associação de RBAC kubernetes estiver no mesmo locatário do Azure AD, atribua permissões com base no *userPrincipalName*. Se o usuário estiver em um locatário do Azure AD diferente, consulte e use a propriedade *ObjectID* em seu lugar.

Crie um manifesto do YAML chamado `basic-azure-ad-binding.yaml` e cole o conteúdo a seguir. Na última linha, substitua *userPrincipalName_or_objectId*  pela saída de ID de objeto ou UPN do comando anterior:

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

Crie o ClusterRoleBinding usando o comando [kubectl Apply][kubectl-apply] e especifique o nome de arquivo do manifesto do YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Acessar cluster com Azure AD

Agora, vamos testar a integração da autenticação do Azure AD para o cluster AKS. Defina o `kubectl` contexto de configuração para usar credenciais de usuário regulares. Esse contexto passa todas as solicitações de autenticação por meio do Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Agora, use o comando [kubectl Get pods][kubectl-get] para exibir pods em todos os namespaces:

```console
kubectl get pods --all-namespaces
```

Você recebe uma solicitação de entrada para autenticar usando as credenciais do Azure AD usando um navegador da Web. Depois de autenticado com êxito, o `kubectl` comando exibe o pods no cluster AKs, conforme mostrado na seguinte saída de exemplo:

```console
kubectl get pods --all-namespaces
```

```output
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

O token de autenticação recebido para `kubectl` é armazenado em cache. Você só será solicitado a entrar quando o token tiver expirado ou o arquivo de configuração kubernetes for recriado.

Se você vir uma mensagem de erro de autorização depois de entrar com êxito usando um navegador da Web como na saída de exemplo a seguir, verifique os seguintes problemas possíveis:

```output
error: You must be logged in to the server (Unauthorized)
```

* Você definiu a ID de objeto ou o UPN apropriado, dependendo de se a conta de usuário está no mesmo locatário do Azure AD ou não.
* O usuário não é um membro de mais de 200 grupos.
* O segredo definido no registro do aplicativo para o servidor corresponde ao valor configurado usando `--aad-server-app-secret`

## <a name="next-steps"></a>Próximas etapas

Para obter o script completo que contém os comandos mostrados neste artigo, consulte o [script de integração do Azure AD no repositório de exemplos do AKS][complete-script].

Para usar os usuários e grupos do Azure AD para controlar o acesso aos recursos de cluster, consulte [controlar o acesso a recursos de cluster usando o controle de acesso baseado em função kubernetes e identidades do Azure AD no AKs][azure-ad-rbac].

Para obter mais informações sobre como proteger clusters do kubernetes, consulte [Opções de acesso e identidade para AKs)][rbac-authorization].

Para obter as práticas recomendadas sobre identidade e controle de recursos, consulte [práticas recomendadas para autenticação e autorização no AKs][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
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
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
