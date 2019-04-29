---
title: Redefinir as credenciais para um cluster do AKS (Serviço de Kubernetes do Azure)
description: Saiba como atualizar ou redefinir as credenciais da entidade de serviço de um cluster no AKS (Serviço de Kubernetes do Azure)
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 01/30/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: d880615d0d132403c935fe39e8478d7b3fc48dbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029344"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>Atualizar ou girar as credenciais para uma entidade de serviço no AKS (Serviço de Kubernetes do Azure)

Por padrão, os clusters do AKS são criados com uma entidade de serviço que tem um tempo de término de um ano. Conforme você se aproxima da data do término, pode redefinir as credenciais para estender a entidade de serviço por um período adicional. Talvez você também deseje atualizar ou girar as credenciais como parte de uma política de segurança definida. Este artigo fornece detalhes de como atualizar essas credenciais para um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisará da CLI do Azure versão 2.0.56 ou posterior instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure][install-azure-cli].

## <a name="choose-to-update-or-create-a-service-principal"></a>Optar por atualizar ou criar uma entidade de serviço

Quando desejar atualizar as credenciais para um cluster do AKS, você poderá optar por:

* atualizar as credenciais para a entidade de serviço existente usadas pelo cluster; ou
* criar uma entidade de serviço e atualizar o cluster para usar essas novas credenciais.

Caso deseje criar uma entidade de serviço e, em seguida, atualizar o cluster do AKS, ignore o restante das etapas desta seção e vá para [Criar uma entidade de serviço](#create-a-service-principal). Caso deseje atualizar as credenciais para a entidade de serviço existente usadas pelo cluster do AKS, continue com as etapas desta seção.

### <a name="get-the-service-principal-id"></a>Obter a ID da entidade de serviço

Para atualizar as credenciais para a entidade de serviço existente, obtenha a ID da entidade de serviço do cluster usando o comando [az aks show][az-aks-show]. O exemplo a seguir obtém a ID do cluster chamado *myAKSCluster* no grupo de recursos *myResourceGroup*. A ID da entidade de serviço é definida como uma variável para uso em comando adicional.

```azurecli
SP_ID=$(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>Atualizar as credenciais da entidade de serviço

Com um conjunto de variáveis que contém a ID da entidade de serviço, redefina agora as credenciais usando [az ad sp credential reset][az-ad-sp-credential-reset]. O exemplo a seguir permite que a plataforma Azure gere um novo segredo seguro para a entidade de serviço. Esse novo segredo seguro também é armazenado como uma variável.

```azurecli
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Agora vá para [Atualizar o cluster do AKS com novas credenciais](#update-aks-cluster-with-new-credentials).

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Se você optar por atualizar as credenciais da entidade de serviço existente na seção anterior, ignore esta etapa. Vá para [Atualizar o cluster do AKS com novas credenciais](#update-aks-cluster-with-new-credentials).

Para criar uma entidade de serviço e, em seguida, atualizar o cluster do AKS para usar essas novas credenciais, use o comando [az ad sp create-for-rbac][az-ad-sp-create]. No exemplo abaixo, o parâmetro `--skip-assignment` impede que outras atribuições padrão sejam atribuídas:

```azurecli
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo: Anote seu próprio `appId` e `password`. Esses valores serão usados na próxima etapa.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Agora defina as variáveis para a ID da entidade de serviço e o segredo do cliente usando a saída do próprio comando [az ad sp create-for-rbac][az-ad-sp-create], conforme mostrado no exemplo a seguir. A *SP_ID* é sua *appId* e o *SP_SECRET* é sua *password*:

```azurecli
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>Atualizar o cluster do AKS com novas credenciais

Independentemente se você optou por atualizar as credenciais da entidade de serviço existente ou criar uma entidade de serviço, agora você atualizará o cluster do AKS com suas novas credenciais usando o comando [az aks update-credentials][az-aks-update-credentials]. As variáveis para *--service-principal* e *--client-secret* são usadas:

```azurecli
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Serão necessários alguns instantes para que as credenciais da entidade de serviço sejam atualizadas no AKS.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, a entidade de serviço do próprio cluster do AKS foi atualizada. Para obter mais informações sobre como gerenciar a identidade para cargas de trabalho em um cluster, confira [Melhores práticas de autenticação e autorização no AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-aks-update-credentials]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: https://docs.azure.cn/zh-cn/cli/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: https://docs.azure.cn/zh-cn/cli/ad/sp/credential?view=azure-cli-latest#az-ad-sp-credential-reset