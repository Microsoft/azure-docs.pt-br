---
title: Tutorial do Kubernetes no Azure - Implantar um cluster
description: Neste tutorial do Serviço de Kubernetes do Azure (AKS), você cria um cluster AKS e usa o kubectl para conectar-se ao nó mestre do Kubernetes.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 27ec77e15d1289742fa40320631684d37c9660a1
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614265"
---
# <a name="tutorial-deploy-an-azure-kubernetes-service-aks-cluster"></a>Tutorial: Implantar um cluster do AKS (Serviço de Kubernetes do Azure)

Kubernetes fornece uma plataforma distribuída para aplicativos em contêineres. Com o AKS, você pode criar rapidamente um cluster do Kubernetes pronto para produção. Neste tutorial, a terceira parte de sete, um cluster Kubernetes é implantado no AKS. Você aprenderá como:

> [!div class="checklist"]
> * Criar uma entidade de serviço para interações de recursos
> * Implantar um cluster AKS Kubernetes
> * Instalar a CLI Kubernetes (kubectl)
> * Configurar o kubectil para conectar-se ao cluster AKS

Em tutoriais adicionais, o aplicativo Azure Vote será implantado no cluster, dimensionado e atualizado.

## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, uma imagem de contêiner foi criada e carregada em uma instância do Registro de Contêiner do Azure. Se você ainda não executou essas etapas e deseja continuar acompanhando, comece no [Tutorial 1 – Criar imagens de contêiner][aks-tutorial-prepare-app].

Este tutorial exige a execução da CLI do Azure versão 2.0.53 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para permitir a interação de um cluster AKS com outros recursos do Azure, usamos uma entidade de serviço do Azure Active Directory. Essa entidade de serviço pode ser criada automaticamente pelo portal ou pela CLI do Azure, ou você pode criar previamente um e atribuir permissões adicionais. Neste tutorial, você cria uma entidade de serviço, concede acesso à instância do ACR (Registro de Contêiner do Azure) criada no tutorial anterior e, em seguida, cria um cluster AKS.

Use o comando [az ad sp create-for-rbac][] para criar uma entidade de serviço. O parâmetro `--skip-assignment` limita a atribuição de outras permissões. Por padrão, essa entidade de serviço é válida por um ano.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo:

```
{
  "appId": "e7596ae3-6864-4cb8-94fc-20164b1588a9",
  "displayName": "azure-cli-2018-06-29-19-14-37",
  "name": "http://azure-cli-2018-06-29-19-14-37",
  "password": "52c95f25-bd1e-4314-bd31-d8112b293521",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anote a *appId* e a *senha*. Esses valores serão usados nas próximas etapas.

## <a name="configure-acr-authentication"></a>Configurar a autenticação do ACR

Para acessar as imagens armazenadas no ACR, conceda à entidade de serviço AKS os direitos corretos para efetuar pull de imagens do ACR.

Primeiro, obtenha a ID de recurso do ACR usando [az acr show][]. Atualize o nome de registro `<acrName>` da sua instância do ACR e o grupo de recursos no qual a instância do ACR está localizada.

```azurecli
az acr show --resource-group myResourceGroup --name <acrName> --query "id" --output tsv
```

Para permitir o acesso correto para o cluster do AKS efetuar pull das imagens armazenadas no ACR, atribua a função `AcrPull` usando o comando [az role assignment create][]. Substitua `<appId`> e `<acrId>` pelos valores coletados nas duas etapas anteriores.

```azurecli
az role assignment create --assignee <appId> --scope <acrId> --role acrpull
```

## <a name="create-a-kubernetes-cluster"></a>Criar um cluster Kubernetes

Os cluster AKS podem usar os controles de acesso baseado em função (RBAC) do Kubernetes. Esses controles permitem que você defina o acesso a recursos com base em funções atribuídas aos usuários. As permissões são combinadas se um usuário recebe várias funções e podem ter o escopo definido para um único namespace ou todo o cluster. Por padrão, a CLI do Azure habilita o RBAC automaticamente quando você cria um cluster AKS.

Crie um cluster AKS usando [az aks create][]. O exemplo abaixo cria um cluster chamado *myAKSCluster* no grupo de recursos chamado *myResourceGroup*. Esse grupo de recursos foi criado no [tutorial anterior][aks-tutorial-prepare-acr]. Forneça seus próprios `<appId>` e `<password>` da etapa anterior na qual a entidade de serviço foi criada.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --service-principal <appId> \
    --client-secret <password> \
    --generate-ssh-keys
```

Após alguns minutos, a implantação será concluída e retornará informações no formato JSON sobre a implantação do AKS.

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

Para verificar a conexão com o cluster, execute o comando [kubectl get nodes][kubectl-get]:

```
$ kubectl get nodes

NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-28993262-0   Ready    agent   3m18s   v1.9.11
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, um cluster Kubernetes foi implantado no AKS e você configurou o `kubectl` para conectar-se a ele. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma entidade de serviço para interações de recursos
> * Implantar um cluster AKS Kubernetes
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
