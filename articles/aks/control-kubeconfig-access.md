---
title: Limitar o acesso a kubeconfig no AKS (Serviço de Kubernetes do Azure)
description: Aprenda a controlar o acesso ao arquivo de configuração Kubernetes (kubeconfig) para os administradores de cluster e os usuários de cluster
services: container-service
ms.topic: article
ms.date: 05/06/2020
ms.openlocfilehash: 77b9988557106ef460d3b222ef85eb29e08f31c8
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693994"
---
# <a name="use-azure-role-based-access-control-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Use o controle de acesso baseado em função do Azure para definir o acesso ao arquivo de configuração kubernetes no serviço kubernetes do Azure (AKS)

Você pode interagir com um cluster Kubernetes usando a ferramenta `kubectl`. A CLI do Azure fornece uma maneira fácil de obter as credenciais de acesso e informações de configuração para se conectar aos seus clusters do AKS usando o `kubectl`. Para limitar quem pode obter essas informações de configuração de kubernetes (*kubeconfig*) e limitar as permissões que elas têm, você pode usar o controle de acesso baseado em função do Azure (RBAC do Azure).

Este artigo mostra como atribuir funções do Azure que limitam quem pode obter as informações de configuração para um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Este artigo também requer que você esteja executando o CLI do Azure versão 2.0.65 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Permissões de funções de cluster disponíveis

Quando você interage com um cluster do AKS usando a ferramenta `kubectl`, é usado um arquivo de configuração que define as informações de conexão do cluster. Esse arquivo de configuração normalmente é armazenado em *~/.Kube/config*. Vários clusters podem ser definidos neste arquivo *kubeconfig* . Você pode alternar entre clusters usando o comando [kubectl config use-context][kubectl-config-use-context].

O comando [az aks get-credentials][az-aks-get-credentials] permite que você obtenha as credenciais de acesso para um cluster do AKS e mescle-os ao arquivo *kubeconfig*. Você pode usar o controle de acesso baseado em função do Azure (RBAC do Azure) para controlar o acesso a essas credenciais. Essas funções do Azure permitem definir quem pode recuperar o arquivo *kubeconfig* e quais permissões eles têm dentro do cluster.

As duas funções internas são:

* **Função de Administrador do Cluster do Serviço de Kubernetes do Azure**  
  * Permite acesso à chamada à API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Essa chamada à API [lista as credenciais de administrador do cluster][api-cluster-admin].
  * Baixa o *kubeconfig* para a função *clusterAdmin*.
* **Função de Usuário do Cluster do Serviço de Kubernetes do Azure**
  * Permite acesso à chamada à API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Essa chamada à API [lista as credenciais de usuário do cluster][api-cluster-user].
  * Baixa *kubeconfig* para a função *clusterUser*.

Essas funções do Azure podem ser aplicadas a um usuário ou grupo Azure Active Directory (AD).

> [!NOTE]
> Em clusters que usam o Azure AD, os usuários com a função *clusterUser* têm um arquivo *kubeconfig* vazio que solicita um logon. Depois de conectado, os usuários têm acesso com base em suas configurações de usuário ou grupo do Azure AD. Os usuários com a função *clusterAdmin* têm acesso de administrador.
>
> Os clusters que não usam o Azure AD usam apenas a função *clusterAdmin* .

## <a name="assign-role-permissions-to-a-user-or-group"></a>Atribuir permissões de função a um usuário ou grupo

Para atribuir uma das funções disponíveis, você precisa obter a ID de recurso do cluster AKS e a ID da conta de usuário ou grupo do Azure AD. Os seguintes comandos de exemplo:

* Obtenha a ID de recurso de cluster usando o comando [AZ AKs show][az-aks-show] para o cluster chamado *myAKSCluster* no grupo de recursos *MyResource* Group. Fornecem seu próprio cluster e nome de grupo de recursos, conforme necessário.
* Use os comandos [AZ Account show][az-account-show] e [AZ ad User show][az-ad-user-show] para obter sua ID de usuário.
* Por fim, atribua uma função usando o comando [AZ role Assignment Create][az-role-assignment-create] .

O exemplo a seguir atribui a *função de administrador de cluster do serviço kubernetes do Azure* a uma conta de usuário individual:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!IMPORTANT]
> Em alguns casos, o *User.Name* na conta é diferente do *userPrincipalName*, como com os usuários convidados do Azure AD:
>
> ```output
> $ az account show --query user.name -o tsv
> user@contoso.com
> $ az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv
> user_contoso.com#EXT#@contoso.onmicrosoft.com
> ```
>
> Nesse caso, defina o valor de *ACCOUNT_UPN* para o *userPrincipalName* do usuário do Azure AD. Por exemplo, se sua conta *User.Name* for *user \@ contoso.com*:
> 
> ```azurecli-interactive
> ACCOUNT_UPN=$(az ad user list --query "[?contains(otherMails,'user@contoso.com')].{UPN:userPrincipalName}" -o tsv)
> ```

> [!TIP]
> Se você quiser atribuir permissões a um grupo do Azure AD, atualize o `--assignee` parâmetro mostrado no exemplo anterior com a ID de objeto para o *grupo* , em vez de um *usuário*. Para obter a ID de objeto para um grupo, use o comando [AZ ad Group show][az-ad-group-show] . O exemplo a seguir obtém a ID de objeto para o grupo do Azure AD chamado *AppDev*: `az ad group show --group appdev --query objectId -o tsv`

Você pode alterar a atribuição anterior para a *função de usuário de cluster* conforme necessário.

A saída de exemplo a seguir mostra que a atribuição de função foi criada com êxito:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>Obter e verificar as informações de configuração

Com as funções do Azure atribuídas, use o comando [AZ AKs Get-Credentials][az-aks-get-credentials] para obter a definição de *kubeconfig* para o cluster AKs. O exemplo a seguir obtém as credenciais *-- admin*, que funcionarão corretamente se a *função de administrador do cluster* tiver sido concedida ao usuário:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, você pode usar o comando [kubectl config view][kubectl-config-view] para verificar se o *contexto* do cluster mostra que as informações de configuração do administrador foram aplicadas:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Remover permissões de função

Para remover uma atribuição de função, use o comando [az role assignment delete][az-role-assignment-delete]. Especifique a ID da conta e a ID de recurso do cluster, conforme obtido nos comandos anteriores. Se você atribuiu a função a um grupo em vez de a um usuário, especifique a ID de objeto de grupo apropriada em vez da ID de objeto de conta para o `--assignee` parâmetro:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Próximas etapas

Para segurança aprimorada no acesso aos clusters do AKS, [integre a autenticação do Azure Active Directory][aad-integration].

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: ./azure-ad-integration-cli.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
