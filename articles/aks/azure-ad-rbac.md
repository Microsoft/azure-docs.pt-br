---
title: Usar o Azure AD e o kubernetes RBAC para clusters
titleSuffix: Azure Kubernetes Service
description: Saiba como usar Azure Active Directory Associação de grupo para restringir o acesso a recursos de cluster usando o controle de acesso baseado em função do kubernetes (kubernetes RBAC) no serviço kubernetes do Azure (AKS)
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: 89bf785a082ff479dcd824a773123fcd061dc22d
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576104"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Controlar o acesso a recursos de cluster usando o controle de acesso baseado em função kubernetes e identidades de Azure Active Directory no serviço kubernetes do Azure

O AKS (Serviço de Kubernetes do Azure) pode ser configurado para usar o Azure Active Directory (AD) para autenticação do usuário. Nessa configuração, você entra em um cluster AKS usando um token de autenticação do Azure AD. Você também pode configurar o controle de acesso baseado em função do kubernetes (kubernetes RBAC) para limitar o acesso a recursos de cluster com base na identidade de um usuário ou na associação de grupo.

Este artigo mostra como usar a associação de grupo do Azure AD para controlar o acesso a namespaces e recursos de cluster usando o RBAC kubernetes em um cluster AKS. Os grupos de exemplo e os usuários são criados no Azure AD, e as funções e RoleBindings são criadas no cluster AKS para conceder as permissões apropriadas para criar e exibir recursos.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tenha um cluster AKS existente habilitado com a integração do Azure AD. Se você precisar de um cluster AKS, consulte [integrar Azure Active Directory com AKs][azure-ad-aks-cli].

Você precisará da CLI do Azure versão 2.0.61 ou posterior instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Criar grupos de demonstração no Azure AD

Neste artigo, vamos criar duas funções de usuário que podem ser usadas para mostrar como o kubernetes RBAC e o Azure AD controlam o acesso aos recursos de cluster. As duas funções de exemplo a seguir são usadas:

* **Desenvolvedor de aplicativos**
    * Um usuário chamado *aksdev* que faz parte do grupo *AppDev* .
* **Engenheiro de confiabilidade do site**
    * Um usuário chamado *akssre* que faz parte do grupo *opssre* .

Em ambientes de produção, você pode usar usuários e grupos existentes em um locatário do Azure AD.

Primeiro, obtenha a ID de recurso do seu cluster AKS usando o comando [AZ AKs show][az-aks-show] . Atribua a ID de recurso a uma variável chamada *AKS_ID* para que ela possa ser referenciada em comandos adicionais.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Crie o primeiro grupo de exemplo no Azure AD para os desenvolvedores de aplicativos usando o comando [AZ ad Group Create][az-ad-group-create] . O exemplo a seguir cria um grupo chamado *AppDev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Agora, crie uma atribuição de função do Azure para o grupo *AppDev* usando o comando [AZ role Assignment Create][az-role-assignment-create] . Essa atribuição permite que qualquer membro do grupo `kubectl` seja usado para interagir com um cluster AKs concedendo a *função de usuário de cluster do serviço kubernetes do Azure*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Se você receber um erro como `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , aguarde alguns segundos para a ID de objeto do grupo do Azure ad se propagar por meio do diretório e depois repita o `az role assignment create` comando.

Crie um segundo grupo de exemplo, este para SREs chamado *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Novamente, crie uma atribuição de função do Azure para conceder aos membros do grupo a *função de usuário de cluster do serviço kubernetes do Azure*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Criar usuários de demonstração no Azure AD

Com dois grupos de exemplo criados no Azure AD para nossos desenvolvedores de aplicativos e SREs, agora vamos criar dois usuários de exemplo. Para testar a integração do RBAC kubernetes no final do artigo, você entra no cluster AKS com essas contas.

Crie a primeira conta de usuário no Azure AD usando o comando [AZ ad User Create][az-ad-user-create] .

O exemplo a seguir cria um usuário com o nome de exibição *AKs dev* e o UPN (nome principal do usuário) do `aksdev@contoso.com` . Atualize o UPN para incluir um domínio verificado para seu locatário do Azure AD (substitua *contoso.com* por seu próprio domínio) e forneça sua própria `--password` credencial segura:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Agora, adicione o usuário ao grupo *AppDev* criado na seção anterior usando o comando [AZ ad Group Member Add][az-ad-group-member-add] :

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Crie uma segunda conta de usuário. O exemplo a seguir cria um usuário com o nome de exibição *AKs SRE* e o UPN (nome principal do usuário) do `akssre@contoso.com` . Novamente, atualize o UPN para incluir um domínio verificado para seu locatário do Azure AD (substitua *contoso.com* por seu próprio domínio) e forneça sua própria `--password` credencial segura:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Criar os recursos de cluster AKS para o aplicativo desenvolvedores

Os grupos e usuários do Azure AD agora são criados. As atribuições de função do Azure foram criadas para que os membros do grupo se conectem a um cluster AKS como um usuário normal. Agora, vamos configurar o cluster AKS para permitir que esses diferentes grupos acessem recursos específicos.

Primeiro, obtenha as credenciais de administrador do cluster usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Em uma das seções a seguir, você obtém as credenciais de cluster de *usuário* regular para ver o fluxo de autenticação do Azure AD em ação.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Crie um namespace no cluster AKS usando o comando [kubectl Create namespace][kubectl-create] . O exemplo a seguir cria um nome de namespace *dev*:

```console
kubectl create namespace dev
```

No kubernetes, as *funções* definem as permissões a serem concedidas e *RoleBindings* as aplicam a usuários ou grupos desejados. Essas atribuições podem ser aplicadas a um determinado namespace ou em todo o cluster. Para obter mais informações, consulte [usando a autorização do RBAC kubernetes][rbac-authorization].

Primeiro, crie uma função para o namespace *dev* . Essa função concede permissões completas para o namespace. Em ambientes de produção, você pode especificar permissões mais granulares para diferentes usuários ou grupos.

Crie um arquivo chamado `role-dev-namespace.yaml` e cole o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
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

Crie a função usando o comando [kubectl Apply][kubectl-apply] e especifique o nome de arquivo do manifesto YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Em seguida, obtenha a ID do recurso para o grupo *AppDev* usando o comando [AZ ad Group show][az-ad-group-show] . Esse grupo é definido como o assunto de uma Rolebinding na próxima etapa.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Agora, crie uma Rolebinding para o grupo *AppDev* para usar a função criada anteriormente para acesso de namespace. Crie um arquivo chamado `rolebinding-dev-namespace.yaml` e cole o manifesto YAML a seguir. Na última linha, substitua *groupObjectId*  pela saída de ID de objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
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

Crie a Funçãobinding usando o comando [kubectl Apply][kubectl-apply] e especifique o nome de arquivo do manifesto do YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Criar os recursos de cluster AKS para SREs

Agora, repita as etapas anteriores para criar um namespace, uma função e uma Rolebinding para o SREs.

Primeiro, crie um namespace para *SRE* usando o comando [kubectl Create namespace][kubectl-create] :

```console
kubectl create namespace sre
```

Crie um arquivo chamado `role-sre-namespace.yaml` e cole o seguinte manifesto YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
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

Crie a função usando o comando [kubectl Apply][kubectl-apply] e especifique o nome de arquivo do manifesto YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Obtenha a ID do recurso para o grupo *opssre* usando o comando [AZ ad Group show][az-ad-group-show] :

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Crie uma Rolebinding para o grupo *opssre* para usar a função criada anteriormente para acesso de namespace. Crie um arquivo chamado `rolebinding-sre-namespace.yaml` e cole o manifesto YAML a seguir. Na última linha, substitua *groupObjectId*  pela saída de ID de objeto de grupo do comando anterior:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
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

Crie a Funçãobinding usando o comando [kubectl Apply][kubectl-apply] e especifique o nome de arquivo do manifesto do YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interagir com recursos de cluster usando identidades do Azure AD

Agora, vamos testar se as permissões esperadas funcionam quando você cria e gerencia recursos em um cluster AKS. Nesses exemplos, você agenda e exibe pods no namespace atribuído do usuário. Em seguida, você tenta agendar e exibir pods fora do namespace atribuído.

Primeiro, redefina o contexto *kubeconfig* usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] . Em uma seção anterior, você define o contexto usando as credenciais de administrador do cluster. O usuário administrador ignora os prompts de entrada do Azure AD. Sem o `--admin` parâmetro, o contexto do usuário é aplicado para exigir que todas as solicitações sejam autenticadas usando o Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Agende um pod NGINX básico usando o comando [kubectl Run][kubectl-run] no namespace *dev* :

```console
kubectl run nginx-dev --image=nginx --namespace dev
```

Como o prompt de entrada, insira as credenciais para sua própria `appdev@contoso.com` conta criada no início do artigo. Depois que você entrar com êxito, o token da conta será armazenado em cache para futuros `kubectl` comandos. O NGINX é agendado com êxito, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl run nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Agora, use o comando [kubectl Get pods][kubectl-get] para exibir pods no namespace do *dev* .

```console
kubectl get pods --namespace dev
```

Conforme mostrado na saída de exemplo a seguir, o Pod NGINX é *executado* com êxito:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Criar e exibir recursos de cluster fora do namespace atribuído

Agora, tente exibir pods fora do namespace do *dev* . Use o comando [kubectl Get pods][kubectl-get] novamente, desta vez para ver `--all-namespaces` da seguinte maneira:

```console
kubectl get pods --all-namespaces
```

A associação de grupo do usuário não tem uma função kubernetes que permita essa ação, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Da mesma forma, tente agendar um pod em um namespace diferente, como o namespace *SRE* . A associação de grupo do usuário não se alinha com uma função kubernetes e Rolebinding para conceder essas permissões, conforme mostrado na seguinte saída de exemplo:

```console
$ kubectl run nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testar o acesso do SRE aos recursos de cluster do AKS

Para confirmar que a associação de grupo do Azure AD e o RBAC kubernetes funcionam corretamente entre diferentes usuários e grupos, tente os comandos anteriores quando estiver conectado como o usuário do *opssre* .

Redefina o contexto *kubeconfig* usando o comando [AZ AKs Get-Credentials][az-aks-get-credentials] que limpa o token de autenticação armazenado em cache anteriormente para o usuário *aksdev* :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Tente agendar e exibir pods no namespace *SRE* atribuído. Quando solicitado, entre com suas próprias `opssre@contoso.com` credenciais criadas no início do artigo:

```console
kubectl run nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Conforme mostrado na saída de exemplo a seguir, você pode criar e exibir com êxito o pods:

```console
$ kubectl run nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Agora, tente exibir ou agendar pods fora do namespace SRE atribuído:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=nginx --namespace dev
```

Esses `kubectl` comandos falham, conforme mostrado na saída de exemplo a seguir. A associação de grupo do usuário e a função kubernetes e RoleBindings não concedem permissões para criar ou gerentes de recursos em outros namespaces:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Limpar recursos

Neste artigo, você criou recursos no cluster AKS e usuários e grupos no Azure AD. Para limpar todos esses recursos, execute os seguintes comandos:

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

Para obter mais informações sobre como proteger clusters do kubernetes, consulte [Opções de acesso e identidade para AKs)][rbac-authorization].

Para obter as práticas recomendadas sobre identidade e controle de recursos, consulte [práticas recomendadas para autenticação e autorização no AKs][operator-best-practices-identity].

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
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
