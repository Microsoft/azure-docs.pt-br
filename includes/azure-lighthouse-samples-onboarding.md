---
title: incluir arquivo
description: incluir arquivo
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86111636"
---
Fornecemos diferentes modelos para abordar diferentes cenários de integração. Escolha a opção que funciona melhor e modifique o arquivo de parâmetro para refletir seu ambiente. Para obter mais informações sobre como usar esses arquivos em sua implantação, confira [Integrar um cliente ao Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Modelo** | **Descrição** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Integrar a assinatura de um cliente ao Azure Lighthouse. Uma implantação separada deve ser executada para cada assinatura. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Integrar um ou mais dos grupos de recursos do cliente ao Azure Lighthouse. Use **rgDelegatedResourceManagement** para um único grupo de recursos ou **multipleRgDelegatedResourceManagement** para integrar vários grupos de recursos na mesma assinatura. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Se você [publicou uma oferta de serviços gerenciados no Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), pode opcionalmente usar esse modelo para integrar recursos para clientes que aceitaram a oferta. Os valores de marketplace no arquivo de parâmetros devem corresponder aos valores que você usou ao publicar sua oferta. |

Normalmente, uma implantação separada é necessária para cada assinatura que está sendo integrada, mas você também pode implantar modelos em várias assinaturas.

| **Modelo** | **Descrição** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Implanta modelos do Azure Resource Manager entre várias assinaturas. |
