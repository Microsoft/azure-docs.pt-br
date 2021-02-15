---
title: Tutorial do Kubernetes no Azure - Implantar um cluster
description: Neste tutorial do Serviço de Kubernetes do Azure (AKS), você cria um cluster AKS e usa o kubectl para conectar-se ao nó mestre do Kubernetes.
services: container-service
ms.topic: tutorial
ms.date: 01/12/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: a8e0ddcd77c26a00cf784fb8c2372734314dc0bb
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2021
ms.locfileid: "98250630"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Implantar um cluster do AKS (Serviço de Kubernetes do Azure)

Kubernetes fornece uma plataforma distribuída para aplicativos em contêineres. Com o AKS, você pode criar rapidamente um cluster do Kubernetes pronto para produção. Neste tutorial, a terceira parte de sete, um cluster Kubernetes é implantado no AKS. Você aprenderá como:

> [!div class="checklist"]
> * Implantar um cluster Kubernetes do AKS que possa se autenticar em um Registro de Contêiner do Azure
> * Instalar a CLI Kubernetes (kubectl)
> * Configurar o kubectil para conectar-se ao cluster AKS

Em tutoriais posteriores, o aplicativo Azure Vote será implantado no cluster, escalado e atualizado.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem de contêiner foi criada e carregada em uma instância do Registro de Contêiner do Azure. Se você ainda não executou essas etapas e deseja continuar acompanhando, comece no [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial exige a execução da CLI do Azure versão 2.0.53 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster do Kubernetes

Os clusters do AKS podem usar o RBAC (controle de acesso baseado em função) do Kubernetes. Esses controles permitem que você defina o acesso a recursos com base em funções atribuídas aos usuários. As permissões são combinadas se um usuário recebe várias funções e podem ter o escopo definido para um único namespace ou todo o cluster. Por padrão, a CLI do Azure habilita o RBAC do Kubernetes automaticamente quando você cria um cluster do AKS.

Crie um cluster AKS usando [az aks create][]. O exemplo abaixo cria um cluster chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. Esse grupo de recursos foi criado no [tutorial anterior][aks-tutorial-prepare-acr] na região *eastus*. O exemplo a seguir não especifica uma região para que o cluster do AKS também seja criado na região *eastus*. Confira [Cotas, restrições de tamanho da máquina virtual e disponibilidade de região no AKS (Serviço de Kubernetes do Azure)][quotas-skus-regions] para obter mais informações sobre limites de recurso e disponibilidade de região do AKS.

Para permitir a interação de um cluster AKS com outros recursos do Azure, uma entidade de serviço do Azure Active Directory é criada automaticamente, já que você não especificou uma. Aqui, a essa entidade de serviço é [concedido o direito de efetuar pull de imagens][container-registry-integration] da instância do ACR (Registro de Contêiner do Azure) que você criou no tutorial anterior. Para executar o comando com êxito, você precisará ter uma função **Proprietário** ou **Administrador da conta do Azure** na assinatura do Azure.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --generate-ssh-keys \
    --attach-acr <acrName>
```

Para evitar a necessidade de uma função **Proprietário** ou **Administrador da conta do Azure**, você também pode configurar manualmente uma entidade de serviço para extrair imagens do ACR. Para obter mais informações, confira [Autenticação do ACR com entidades de serviço](../container-registry/container-registry-auth-service-principal.md) ou [Autenticar do Kubernetes com um segredo de pull](../container-registry/container-registry-auth-kubernetes.md). Como alternativa, você pode usar uma [identidade gerenciada](use-managed-identity.md) em vez de uma entidade de serviço para facilitar o gerenciamento.

Após alguns minutos, a implantação será concluída e retornará informações no formato JSON sobre a implantação do AKS.

> [!NOTE]
> Para verificar se seu cluster opera de maneira confiável, execute pelo menos 2 (dois) nós.

## <a name="install-the-kubernetes-cli"></a>Instalar a CLI Kubernetes

Para se conectar ao cluster Kubernetes no computador local, use o [kubectl][kubectl], o cliente de linha de comando do Kubernetes.

Se você usa o Azure Cloud Shell, o `kubectl` já estará instalado. Se você quiser instalá-lo localmente, use o comando [az aks install-cli][]:

```azurecli
az aks install-cli
```

## <a name="connect-to-cluster-using-kubectl"></a>Conectar-se ao cluster usando o kubectl

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][]. O seguinte exemplo obtém as credenciais para o cluster do AKS chamado *myAKSCluster* no *myResourceGroup*:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, execute o comando [kubectl get nodes][kubectl-get] para retornar uma lista dos nós de cluster:

```
$ kubectl get nodes

NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-37463671-vmss000000   Ready    agent   2m37s   v1.18.10
aks-nodepool1-37463671-vmss000001   Ready    agent   2m28s   v1.18.10
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um cluster Kubernetes foi implantado no AKS e você configurou o `kubectl` para conectar-se a ele. Você aprendeu a:

> [!div class="checklist"]
> * Implantar um cluster Kubernetes do AKS que possa se autenticar em um Registro de Contêiner do Azure
> * Instalar a CLI Kubernetes (kubectl)
> * Configurar o kubectil para conectar-se ao cluster AKS

Siga para o próximo tutorial para saber como implantar um aplicativo ao cluster.

> [!div class="nextstepaction"]
> [Implantar o aplicativo no Kubernetes][aks-tutorial-deploy-app]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-deploy-app]: ./tutorial-kubernetes-deploy-application.md
[aks-tutorial-prepare-acr]: ./tutorial-kubernetes-prepare-acr.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[az ad sp create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az acr show]: /cli/azure/acr#az-acr-show
[az role assignment create]: /cli/azure/role/assignment#az-role-assignment-create
[az aks create]: /cli/azure/aks#az-aks-create
[az aks install-cli]: /cli/azure/aks#az-aks-install-cli
[az aks get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-cli-install]: /cli/azure/install-azure-cli
[container-registry-integration]: ./cluster-container-registry-integration.md
[quotas-skus-regions]: quotas-skus-regions.md
