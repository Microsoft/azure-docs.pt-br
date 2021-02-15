---
title: incluir arquivo
description: incluir arquivo
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 34388fe975df359c695b9358cafcd3ae711af0cf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900765"
---
Após aplicar as marcas aos recursos, será possível exibir os custos dos recursos com essas marcas. Demora um tempo para que a análise de custo mostre o uso mais recente, portanto, talvez ainda não seja possível exibir os custos. Quando os custos estiverem disponíveis, você poderá visualizá-los nos grupos de recursos em sua assinatura. Para visualizar os custos, os usuários deverão ter [acesso no nível da assinatura para informações de cobrança](../articles/cost-management-billing/manage/manage-billing-access.md).

Para exibir custos por marca no portal, selecione sua assinatura e, em seguida, **Análise de Custo**.

![Análise de custo](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Em seguida, filtre pelo valor de marca e selecione **Aplicar**.

![Exibir custo por marca](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Também é possível usar a [Visão geral da API de consumo do Azure](../articles/cost-management-billing/manage/consumption-api-overview.md) para exibir os custos de maneira programática.
