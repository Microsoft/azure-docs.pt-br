---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 12/20/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: f3d558736751d3c50e3c007e3aebb369093ef856
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244533"
---
No Cloud Shell, crie um plano do Serviço de Aplicativo no grupo de recursos com o comando [`az appservice plan create`](/cli/azure/appservice/plan#az-appservice-plan-create).

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

O exemplo a seguir cria um plano do Serviço de Aplicativo denominado `myAppServicePlan` no tipo de preço **Gratuito** (`--sku F1`) e em um contêiner do Linux (`--is-linux`).

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

Quando o Plano do Serviço de Aplicativo for criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  <JSON data removed for brevity.>
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```
