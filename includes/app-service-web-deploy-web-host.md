---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 733fc9620cbd17e5e4d8bb101c54ff646a06d6a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86050292"
---
### <a name="app-service-plan"></a>Plano do Serviço de Aplicativo
Cria o plano de serviço para hospedar o aplicativo Web. Forneça o nome do plano por meio do parâmetro **hostingPlanName** . O local do plano é o mesmo usado para o grupo de recursos. O tipo de preços e o tamanho do trabalhador são especificados nos parâmetros **sku** e **workerSize**

```config
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('hostingPlanName')]",
  "type": "Microsoft.Web/serverfarms",
  "location": "[resourceGroup().location]",
  "sku": {
    "name": "[parameters('sku')]",
    "capacity": "[parameters('workerSize')]"
  },
  "properties": {
    "name": "[parameters('hostingPlanName')]"
  }
},
```
