---
title: Controle de recursos de cluster com o Azure AD no serviço Kubernetes do Azure e o RBAC
description: Saiba como usar a associação de grupo do Active Directory do Azure para restringir o acesso aos recursos de cluster usando o controle de acesso baseado em função (RBAC) no serviço de Kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/16/2019
ms.author: mlearned
ms.openlocfilehash: fba54fd23fefbe0029b9a809b23568490f05b23e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67616158"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controlar o acesso aos recursos de cluster usando o controle de acesso baseado em função e identidades do Active Directory do Azure no serviço Kubernetes do Azure

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nessa configuração, você entra um cluster AKS usando um token de autenticação do AD do Azure. Você também pode configurar Kubernetes o controle de acesso baseado em função (RBAC) para limitar o acesso aos recursos de cluster com base em associação de grupo ou identidade do usuário.

Este artigo mostra como usar a associação de grupo do AD do Azure para controlar o acesso aos namespaces e recursos usando o RBAC do Kubernetes em um cluster AKS do cluster. Usuários e grupos de exemplo são criados no Azure AD e, em seguida, funções e RoleBindings são criados no cluster do AKS para conceder as permissões apropriadas para criar e exibir recursos.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente habilitado com a integração do Azure AD. Se você precisar um cluster do AKS, consulte [integrar o Azure Active Directory com o AKS][azure-ad-aks-cli].

Você precisa da CLI do Azure versão 2.0.61 ou posterior instalado e configurado. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Criar grupos de demonstração no AD do Azure

Neste artigo, vamos criar duas funções de usuário que podem ser usadas para mostrar como o RBAC Kubernetes e o Azure AD controlam o acesso aos recursos do cluster. As seguintes funções de dois exemplo são usadas:

* **Desenvolvedor de aplicativos**
    * Um usuário chamado *aksdev* que faz parte da *appdev* grupo.
* **Engenheiro de confiabilidade de site**
    * Um usuário chamado *akssre* que faz parte da *opssre* grupo.

Em ambientes de produção, você pode usar grupos dentro de um locatário do AD do Azure e os usuários existentes.

Primeiro, obtenha a ID de recurso do seu cluster AKS usando o [show do az aks][az-aks-show] comando. Atribuir a ID de recurso a uma variável chamada *AKS_ID* para que ele pode ser referenciado em comandos adicionais.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Criar o primeiro grupo de exemplo no Azure AD para desenvolvedores de aplicativos usando o [criar grupo do ad az][az-ad-group-create] comando. O exemplo a seguir cria um grupo chamado *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Agora, crie uma atribuição de função do Azure para o *appdev* grupo usando o [criar atribuição de função az][az-role-assignment-create] comando. Essa atribuição permite que qualquer membro do grupo usem `kubectl` para interagir com um cluster do AKS, concedendo a ela o *função de usuário de Cluster de serviço do Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se você receber um erro, como `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`, aguarde alguns segundos para a ID de objeto de grupo do AD do Azure se propagar por meio do diretório, em seguida, tente o `az role assignment create` comando novamente.

Criar um segundo grupo de exemplo, essa outra para SREs denominado *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Novamente, crie uma atribuição de função do Azure para conceder aos membros do grupo de *função de usuário de Cluster de serviço do Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Criar usuários de demonstração no Azure AD

Com dois grupos de exemplo criados no Azure AD para os nossos desenvolvedores de aplicativos e SREs, agora vamos criar dois usuários de exemplo. Para testar a integração de RBAC no final do artigo, você entrar no cluster do AKS com essas contas.

Criar a primeira conta de usuário no Azure AD usando o [criar usuário do ad az][az-ad-user-create] comando.

O exemplo a seguir cria um usuário com o nome de exibição *AKS Dev* e o nome principal do usuário (UPN) do `aksdev@contoso.com`. Atualizar o UPN para incluir um domínio verificado para seu locatário do AD do Azure (substitua *contoso.com* com seu próprio domínio) e forneça seu próprio seguro `--password` credencial:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Agora, adicione o usuário para o *appdev* grupo criado na seção anterior usando o [Adicionar membro do grupo de az ad][az-ad-group-member-add] comando:

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Crie uma segunda conta de usuário. O exemplo a seguir cria um usuário com o nome de exibição *AKS SRE* e o nome principal do usuário (UPN) do `akssre@contoso.com`. Novamente, atualize o UPN para incluir um domínio verificado para seu locatário do AD do Azure (substitua *contoso.com* com seu próprio domínio) e forneça seu próprio seguro `--password` credencial:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Criar os recursos de cluster do AKS para desenvolvedores de aplicativos

Os usuários e grupos do Azure AD agora são criados. As atribuições de função do Azure foram criadas para os membros do grupo para se conectar a um cluster AKS como um usuário normal. Agora, vamos configurar o cluster do AKS para permitir que esses diferentes grupos acesso a recursos específicos.

Primeiro, obtenha o cluster de credenciais de administrador usando o [az aks get-credentials][az-aks-get-credentials] comando. Em uma das seções a seguir, você obtém normal *usuário* as credenciais para ver a autenticação do Azure AD de cluster fluxo em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Criar um namespace no cluster AKS usando o [kubectl criar namespace][kubectl-create] comando. O exemplo a seguir cria um nome de namespace *dev*:

```console
kubectl create namespace dev
```

No Kubernetes, *funções* definem as permissões para conceder, e *RoleBindings* aplicá-las a grupos ou usuários desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [autorização usando RBAC][rbac-authorization].

Primeiro, crie uma função para o *dev* namespace. Essa função concede permissões completas para o namespace. Em ambientes de produção, você pode especificar permissões mais granulares para diferentes usuários ou grupos.

Crie um arquivo chamado `role-dev-namespace.yaml` e cole o manifesto YAML a seguir:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a função usando o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do arquivo de manifesto do YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Em seguida, obtenha a ID de recurso para o *appdev* grupo usando o [Mostrar de grupo do ad az][az-ad-group-show] comando. Esse grupo é definido como o assunto de um RoleBinding na próxima etapa.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Agora, crie um RoleBinding para o *appdev* grupo para usar a função criada anteriormente para acesso ao namespace. Crie um arquivo chamado `rolebinding-dev-namespace.yaml` e cole o manifesto YAML a seguir. Na última linha, substitua *groupObjectId* com a saída de ID de objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Crie o RoleBinding usando o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do arquivo de manifesto do YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Criar os recursos de cluster do AKS para SREs

Agora, repita as etapas anteriores para criar um namespace, função e RoleBinding para os SREs.

Primeiro, crie um namespace para *sre* usando o [kubectl criar namespace][kubectl-create] comando:

```console
kubectl create namespace sre
```

Crie um arquivo chamado `role-sre-namespace.yaml` e cole o manifesto YAML a seguir:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Crie a função usando o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do arquivo de manifesto do YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Obter a ID de recurso para o *opssre* grupo usando o [Mostrar de grupo do ad az][az-ad-group-show] comando:

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Criar um RoleBinding para o *opssre* grupo para usar a função criada anteriormente para acesso ao namespace. Crie um arquivo chamado `rolebinding-sre-namespace.yaml` e cole o manifesto YAML a seguir. Na última linha, substitua *groupObjectId* com a saída de ID de objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Crie o RoleBinding usando o [kubectl aplicar][kubectl-apply] de comando e especifique o nome do arquivo de manifesto do YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagir com recursos de cluster usando identidades do Azure AD

Agora, vamos testar o trabalho de permissões esperadas quando você cria e gerenciar recursos em um cluster AKS. Nestes exemplos, agendar e exibir os pods no namespace atribuída do usuário. Em seguida, você tenta agendar e exibir pods fora do namespace atribuído.

Primeiro, redefina o *kubeconfig* contexto usando o [az aks get-credentials][az-aks-get-credentials] comando. Em uma seção anterior, você deve definir o contexto usando as credenciais de administrador de cluster. O usuário administrador ignora prompts de entrada do Azure AD. Sem o `--admin` parâmetro, o contexto do usuário é aplicado e exige que todas as solicitações sejam autenticados usando o Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Agenda um pod NGINX básico usando o [kubectl executado][kubectl-run] comando o *dev* namespace:

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Como o sinal no prompt, digite as credenciais de sua própria `appdev@contoso.com` conta criada no início do artigo. Quando você estiver entrado com êxito, o token de conta é armazenada em cache para futuro `kubectl` comandos. O NGINX é agendar com êxito, conforme mostrado na saída de exemplo a seguir:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Agora use o [kubectl get pods][kubectl-get] comando para exibir os pods na *dev* namespace.

```console
kubectl get pods --namespace dev
```

Conforme mostrado na seguinte saída de exemplo, o pod NGINX é com êxito *executando*:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Criar e exibir recursos de cluster fora do namespace atribuído

Agora, tente exibir pods fora das *dev* namespace. Use o [kubectl get pods][kubectl-get] comando novamente, desta vez para ver `--all-namespaces` da seguinte maneira:

```console
kubectl get pods --all-namespaces
```

Associação de grupo do usuário não tem uma função do Kubernetes que permite que essa ação, conforme mostrado na saída de exemplo a seguir:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Da mesma forma, tente agendar um pod em um namespace diferente, como o *sre* namespace. Associação de grupo do usuário não se alinha com uma função de Kubernetes e RoleBinding conceder essas permissões, conforme mostrado na saída de exemplo a seguir:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testar o acesso SRE para os recursos de cluster do AKS

Para confirmar que nossa associação de grupo do AD do Azure e o Kubernetes RBAC funcionem corretamente entre diferentes usuários e grupos, repita os comandos anteriores quando conectado como o *opssre* usuário.

Redefinir o *kubeconfig* contexto usando o [az aks get-credentials][az-aks-get-credentials] comando que limpa o token de autenticação armazenadas em cache anteriormente para o *aksdev* usuário:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Tente pods de agendamento e o modo de exibição no atribuído *sre* namespace. Quando solicitado, entre com sua própria `opssre@contoso.com` credenciais criadas no início do artigo:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Conforme mostrado na seguinte saída de exemplo, você pode criar e exibir os pods com êxito:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Agora, tente exibir ou agendar os pods fora do namespace SRE atribuído:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Eles `kubectl` comandos falhar, conforme mostrado no seguinte exemplo de saída. Associação de grupo do usuário e função do Kubernetes e RoleBindings não conceder permissões para criar ou Gerenciador de recursos em outros namespaces:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, você criou recursos no cluster do AKS e os usuários e grupos no Azure AD. Para limpar todos esses recursos, execute os seguintes comandos:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como proteger os clusters Kubernetes, consulte [opções de acesso e identidade para AKS)][rbac-authorization].

Para as práticas recomendadas de controle de identidade e recursos, consulte [práticas recomendadas para autenticação e autorização no AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
