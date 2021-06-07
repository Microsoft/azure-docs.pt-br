---
title: incluir arquivo
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95997878"
---
Para acessar os logs de console gerados dentro do código do aplicativo no Serviço de Aplicativo, ative o log de diagnóstico executando o seguinte comando no [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Os valores possíveis para `--level` são `Error`, `Warning`, `Info` e `Verbose`. Cada nível seguinte inclui o anterior. Por exemplo: `Error` inclui apenas mensagens de erro e `Verbose` inclui todas as mensagens.

Depois que o log de diagnósticos estiver ativado, execute o seguinte comando para ver o fluxo de logs:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

> [!NOTE]
> Você também pode inspecionar os arquivos de log do navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Para interromper o streaming de log a qualquer momento, digite `Ctrl`+`C`.
