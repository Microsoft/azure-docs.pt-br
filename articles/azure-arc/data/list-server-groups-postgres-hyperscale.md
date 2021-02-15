---
title: Listar os grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc criados em um controlador de dados de arco do Azure
description: Listar os grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc criados em um controlador de dados de arco do Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7577ca4b8a1d7db7ea99aadfef4fd2a445b66425
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90933799"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Listar os grupos de servidores de hiperescala PostgreSQL habilitados para o Azure Arc criados em um controlador de dados de arco do Azure

Este artigo explica como você pode recuperar a lista de grupos de servidores criados em seu controlador de dados de arco.

Para recuperar essa lista, use qualquer um dos métodos a seguir quando estiver conectado ao controlador de dados Arc:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azdata"></a>Da CLI com azdata
O formato geral do comando é:
```console
azdata arc postgres server list
```

Retornará uma saída como:
```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Para obter mais detalhes sobre os parâmetros disponíveis para este comando, execute:
```console
azdata arc postgres server list --help
```

## <a name="from-cli-with-kubectl"></a>Da CLI com kubectl
Execute um dos comandos a seguir.

**Para listar os grupos de servidores, independentemente da versão do Postgres, execute:**
```console
kubectl get postgresqls
```
Retornará uma saída como:
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Para listar os grupos de servidores de uma versão específica do Postgres, execute:**
```console
kubectl get postgresql-12
```

Para listar os grupos de servidores que executam a versão 11 do Postgres, substitua _PostgreSQL-12_ por _PostgreSQL-11_.

## <a name="next-steps"></a>Próximas etapas:

* [Leia o artigo sobre como obter os pontos de extremidade de conexão e formate as cadeias de conexão para se conectar ao seu grupo de servidores](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Leia o artigo sobre como mostrar a configuração de um grupo de servidores de hiperescala do PostgreSQL habilitado para o Azure Arc](show-configuration-postgresql-hyperscale-server-group.md)
