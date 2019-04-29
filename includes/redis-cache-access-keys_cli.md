---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 778151e401624398b70101a242e4cf0be8e0a1b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485285"
---
### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Recuperar o nome de host, portas e chaves de acesso usando a CLI do Azure

Para recuperar o nome do host e portas usando a CLI do Azure que você pode chamar [apresentação de redis az](https://docs.microsoft.com/cli/azure/redis#az_redis_show)e para recuperar as chaves que você pode chamar [az redis listar chaves](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys). O script a seguir chama esses dois comandos e retorna o nome do host, portas e chaves para o console.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Cache for Redis instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Cache for Redis instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

Para obter mais informações sobre esse script, consulte [obter o nome do host, portas e chaves para o Cache do Azure para Redis](../articles/azure-cache-for-redis/scripts/cache-keys-ports.md). Para obter mais informações sobre a CLI do Azure, consulte [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) e [Introdução à CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
