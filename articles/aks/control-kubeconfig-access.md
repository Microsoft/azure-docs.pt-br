---
title: Limitar o acesso a kubeconfig no AKS (Serviço de Kubernetes do Azure)
description: Aprenda a controlar o acesso ao arquivo de configuração Kubernetes (kubeconfig) para os administradores de cluster e os usuários de cluster
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: cbc653b86ed83f9d6a7348d39f51dc7cd49c6892
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615667"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Usar os controles de acesso baseado em função do Azure para definir o acesso ao arquivo de configuração do Kubernetes no AKS (Serviço de Kubernetes do Azure)

Você pode interagir com um cluster Kubernetes usando a ferramenta `kubectl`. A CLI do Azure fornece uma maneira fácil de obter as credenciais de acesso e informações de configuração para se conectar aos seus clusters do AKS usando o `kubectl`. Para limitar quem pode obter essas informações de configuração do Kubernetes (*kubeconfig*) e para limitar as permissões que essas pessoas têm, você pode usar RBACs (controles de acesso baseado em função) do Azure.

Este artigo mostra como atribuir funções RBAC que limitam quem pode obter as informações de configuração para um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar um cluster do AKS, consulte o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Este artigo também requer que você está executando a CLI do Azure versão 2.0.65 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Permissões de funções de cluster disponíveis

Quando você interage com um cluster do AKS usando a ferramenta `kubectl`, é usado um arquivo de configuração que define as informações de conexão do cluster. Esse arquivo de configuração normalmente é armazenado em *~/.kube/config*. Vários clusters podem ser definidos nesse arquivo *kubeconfig*. Você pode alternar entre clusters usando o [contexto de uso de configuração de kubectl][kubectl-config-use-context] comando.

O [az aks get-credentials][az-aks-get-credentials] comando permite que você obtenha as credenciais de acesso para um cluster do AKS e mescla-los para o *kubeconfig* arquivo. Você pode usar RBACs (controles de acesso baseado em função) do Azure para controlar o acesso a essas credenciais. Essas funções de RBAC do Azure permitem que você defina quem pode recuperar o arquivo *kubeconfig* e, se alguém o fizer, quais permissões esse alguém terá dentro do cluster.

As duas funções internas são:

* **Função de Administrador do Cluster do Serviço de Kubernetes do Azure**  
    * Permite acesso à chamada à API *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action*. Essa chamada à API [lista as credenciais de administrador de cluster][api-cluster-admin].
    * Baixa o *kubeconfig* para a função *clusterAdmin*.
* **Função de Usuário do Cluster do Serviço de Kubernetes do Azure**
    * Permite acesso à chamada à API *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action*. Essa chamada à API [lista as credenciais de usuário de cluster][api-cluster-user].
    * Baixa *kubeconfig* para a função *clusterUser*.

Essas funções RBAC podem ser aplicadas a um grupo ou usuário do Azure Active Directory (AD).

## <a name="assign-role-permissions-to-a-user-or-group"></a>Atribuir permissões de função a um usuário ou grupo

Para atribuir uma das funções disponíveis, você precisa obter a ID do recurso de cluster do AKS e a ID da conta de usuário do Azure AD ou grupo. Os comandos de exemplo a seguir:

* Obter a ID de recurso de cluster usando o [show do az aks][az-aks-show] comando para o cluster chamado *myAKSCluster* no *myResourceGroup* grupo de recursos. Fornecem seu próprio cluster e nome de grupo de recursos, conforme necessário.
* Usa o [show do az conta][az-account-show] and [az ad user show][az-ad-user-show] comandos para obter sua ID de usuário.
* Por fim, atribui uma função usando o [atribuição de função az criar][az-role-assignment-create] comando.

O exemplo a seguir atribui a *função de administrador de Cluster de serviço do Azure Kubernetes* a uma conta de usuário individual:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --upn-or-object-id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Se você deseja atribuir permissões a um grupo do AD do Azure, atualize o `--assignee` parâmetro mostrado no exemplo anterior com a ID de objeto para o *grupo* em vez de uma *usuário*. Para obter a ID de objeto para um grupo, use o [Mostrar de grupo do ad az][az-ad-group-show] comando. O exemplo a seguir obtém a ID de objeto para o grupo do AD do Azure chamado *appdev*: `az ad group show --group appdev --query objectId -o tsv`

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

Com as funções RBAC atribuídas, use o [az aks get-credentials][az-aks-get-credentials] comando para obter o *kubeconfig* definição para o cluster do AKS. O exemplo a seguir obtém as credenciais *-- admin*, que funcionarão corretamente se a *função de administrador do cluster* tiver sido concedida ao usuário:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Em seguida, você pode usar o [modo de exibição de configuração de kubectl][kubectl-config-view] comando para verificar se o *contexto* para o cluster mostra que as informações de configuração do administrador foi aplicadas:

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

Para remover as atribuições de função, use o [Excluir atribuição de função az][az-role-assignment-delete] comando. Especifique a ID da conta e ID de recurso de cluster, conforme obtidas nos comandos anteriores. Se você atribuiu a função a um grupo em vez de um usuário, especifique o objeto de grupo apropriado ID em vez da ID de objeto de conta para o `--assignee` parâmetro:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Próximas etapas

Para aumentar a segurança no acesso aos clusters AKS [integrar a autenticação do Azure Active Directory][aad-integration].

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
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
