---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 7c8e9770f2e425c93f45a207a2e49cff25e924d9
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244815"
---
No Cloud Shell, crie um plano do Serviço de Aplicativo com o comando [`az appservice plan create`](/cli/azure/appservice/plan).

<!-- [!INCLUDE [app-service-plan](app-service-plan.md)] -->

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` usando o tipo de preço **Gratuita**:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>
