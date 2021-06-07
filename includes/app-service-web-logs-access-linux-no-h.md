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
ms.openlocfilehash: df71f0804b62eb4b17ff8d2f652b076b5c64c959
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
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

Para interromper o streaming de log a qualquer momento, digite **Ctrl**+**C**.

Você também pode inspecionar os arquivos de log em um navegador em `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
