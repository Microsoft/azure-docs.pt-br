---
title: Registrar o provedor de recursos da Solução VMware no Azure
description: Etapas para registrar o provedor de recursos da Solução VMware no Azure.
ms.topic: include
ms.date: 12/24/2020
ms.openlocfilehash: 7d24ce86f24c941c7d48d3b73576dcdfda120f51
ms.sourcegitcommit: 489ce69c0ff3f5188889ecfef5ffa76f7121e0d3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/24/2020
ms.locfileid: "97770802"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Para usar a Solução VMware no Azure, você precisa primeiro registrar o provedor de recursos com sua assinatura.  

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