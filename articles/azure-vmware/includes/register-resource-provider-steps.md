---
title: Registrar o provedor de recursos da Solução VMware no Azure
description: Etapas para registrar o provedor de recursos da Solução VMware no Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: d2363ca2672f7f668d8f9b3816447f316d8b7347
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555861"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Para usar a Solução VMware no Azure, você precisa primeiro registrar o provedor de recursos com sua assinatura. Para obter mais informações sobre os provedores de recursos, confira [Provedores e tipos de recursos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **Todos os serviços** , digite **assinatura** e, em seguida, selecione **Assinaturas**.

1. Selecione a assinatura na lista de assinaturas para visualizá-la.

1. Selecione **Provedores de recursos** e insira **Microsoft. AVS** na pesquisa. 
 
1. Caso o provedor de recursos não esteja registrado, clique em **Registrar**.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para começar a usar a CLI do Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Entre na assinatura do Azure que você usa para a implantação da Solução VMware no Azure por meio da CLI do Azure. Registre o provedor de recursos de `Microsoft.AVS` com o comando [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Você pode usar o comando [az provider list](/cli/azure/provider#az_provider_list) para ver todos os provedores disponíveis.

---


 
