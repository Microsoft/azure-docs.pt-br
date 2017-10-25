---
title: "Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure | Documentos o Microsoft"
description: "Exemplo de script da CLI do Azure – Obter detalhes de um Cache Redis do Azure"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 155924e6-00d5-4a8c-ba99-5189f300464a
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: 0927508117bda67b62a188f89a2d2e151fe02a5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="get-details-of-an-azure-redis-cache"></a>Obter detalhes de um Cache Redis do Azure

Nesse cenário, você aprende como recuperar os detalhes de uma instância de Cache Redis do Azure, incluindo seu status de provisionamento.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Redis Cache")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para recuperar detalhes de uma instância de Cache Redis do Azure. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Recupere os detalhes de uma instância de Cache Redis do Azure. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure/overview).

Exemplos adicionais de scripts da CLI do Cache Redis do Azure podem ser encontrados na [Documentação do Cache Redis do Azure](../cli-samples.md).