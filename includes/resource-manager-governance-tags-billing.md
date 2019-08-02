---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: e7fcb72cecbfad2de80b844ed5281267d5e4c0c3
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67171854"
---
Após aplicar as marcas aos recursos, será possível exibir os custos dos recursos com essas marcas. Demora um tempo para que a análise de custo mostre o uso mais recente, portanto, talvez ainda não seja possível exibir os custos. Quando os custos estiverem disponíveis, você poderá visualizá-los nos grupos de recursos em sua assinatura. Para visualizar os custos, os usuários deverão ter [acesso no nível da assinatura para informações de cobrança](../articles/billing/billing-manage-access.md).

Para exibir custos por marca no portal, selecione sua assinatura e, em seguida, **Análise de Custo**.

![Análise de custo](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Em seguida, filtre pelo valor de marca e selecione **Aplicar**.

![Exibir custo por marca](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Também é possível usar as [APIs de Cobrança do Azure](../articles/billing/billing-usage-rate-card-overview.md) para exibir os custos de forma programática.
