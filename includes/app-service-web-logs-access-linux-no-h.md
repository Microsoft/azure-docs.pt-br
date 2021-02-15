---
title: arquivo de inclusão
description: incluir arquivo
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743804"
---
Você pode acessar os logs do console gerados de dentro do contêiner.

Primeiro, ative o log do contêiner executando o seguinte comando:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Substitua `<app-name>` e `<resource-group-name>` pelos nomes apropriados para seu aplicativo Web.

Depois que o log do contêiner estiver ativado, execute o seguinte comando para ver o fluxo de log:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Se você não vir os logs do console imediatamente, verifique novamente após 30 segundos.

Para interromper o streaming de log a qualquer momento, digite **Ctrl**+**C** .

Você também pode inspecionar os arquivos de log em um navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
