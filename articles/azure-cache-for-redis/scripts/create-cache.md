---
title: Exemplo de Script da CLI do Azure – Criar um Cache para Redis| Microsoft Docs
description: Exemplo de Script da CLI do Azure – Criar um Cache para Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
tags: azure-service-management
ms.assetid: afd7f6e0-9297-4c98-a95e-597be939cef7
ms.service: cache
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: f4900242c9b6e5eecd5364d74bfcd8e2bc4efb2b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60607753"
---
# <a name="create-an-azure-cache-for-redis"></a>Criar um Cache do Azure para Redis

Neste cenário, você aprende a criar um Azure Cache para Redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos para criar um grupo de recursos e um Azure Cache para Redis. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az redis create](https://docs.microsoft.com/cli/azure/redis) | Criar instância de Cache do Azure para Redis. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos adicionais de scripts da CLI do Cache Redis do Azure podem ser encontrados na [Documentação do Cache Redis do Azure](../cli-samples.md).
