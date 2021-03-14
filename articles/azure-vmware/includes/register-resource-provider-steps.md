---
title: Registrar o provedor de recursos da Solução VMware no Azure
description: Etapas para registrar o provedor de recursos da Solução VMware no Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750225"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Para usar a Solução VMware no Azure, você precisa primeiro registrar o provedor de recursos com sua assinatura. Para obter mais informações sobre os provedores de recursos, confira [Provedores e tipos de recursos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="azure-cli"></a>CLI do Azure 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Portal do Azure
 
1. Entre no [portal do Azure](https://portal.azure.com).

1. No menu do portal do Azure, selecione **Todos os serviços**.

1. Na caixa **Todos os serviços** , digite **assinatura** e, em seguida, selecione **Assinaturas**.

1. Selecione a assinatura na lista de assinaturas para visualizá-la.

1. Selecione **Provedores de recursos** e insira **Microsoft. AVS** na pesquisa. 
 
1. Caso o provedor de recursos não esteja registrado, clique em **Registrar**.