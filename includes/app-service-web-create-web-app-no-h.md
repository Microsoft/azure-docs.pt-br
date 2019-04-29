---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 9f7c82a2bd35f06da096fa0dd9d5d1fa4d08011e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60838923"
---
No Cloud Shell, crie um [aplicativo Web](../articles/app-service/overview.md) no plano do Serviço de Aplicativo do `myAppServicePlan`. Você pode fazer isso usando o comando [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create). No exemplo a seguir, substitua  *\<app-name >* com um nome de aplicativo exclusivo globalmente (os caracteres válidos são `a-z`, `0-9`, e `-`).

```azurecli-interactive
az webapp create --name <app-name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

Quando o aplicativo Web tiver sido criado, a CLI do Azure mostrará informações semelhantes ao exemplo a seguir:

```json
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Você criou um aplicativo Web vazio com a implantação do git habilitada.

> [!NOTE]
> A URL do Git remoto é mostrada na propriedade `deploymentLocalGitUrl` com o formato `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Salve essa URL, pois você precisará dela mais tarde.
>

Navegue até o aplicativo Web recém-criado.

```
http://<app-name>.azurewebsites.net
```

Seu novo aplicativo Web deve ficar assim:
