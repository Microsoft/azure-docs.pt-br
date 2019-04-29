---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60768677"
---
Você pode acessar os logs do console gerados de dentro do contêiner. Primeiro, ative o log do contêiner executando o seguinte comando no Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Depois que o log do contêiner estiver ativado, execute o seguinte comando para ver o fluxo de log:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

> [!NOTE]
> Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.
