---
title: 'API REST: Sincronizar entre vários bancos de dados'
description: Use um script de exemplo da API REST para sincronizar entre vários bancos de dados.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: REST API
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: d3ff8114c11b224a0bdbb0bd2d0e5686a7e57b55
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565872"
---
# <a name="use-rest-api-to-sync-data-between-multiple-databases"></a>Usar a API REST para sincronizar dados entre vários bancos de dados 

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Este exemplo da API REST configura a Sincronização de Dados SQL para sincronizar dados entre vários bancos de dados.

Para obter uma visão geral da Sincronização de Dados SQL, consulte [Sincronizar dados entre vários bancos de dados locais e de nuvem com a Sincronização de Dados SQL no Azure](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> No momento, a Sincronização de Dados SQL não dá suporte à Instância Gerenciada de SQL do Azure.

## <a name="create-sync-group"></a>Criar grupo de sincronização

Use o modelo [criar ou atualizar](/rest/api/sql/syncgroups/createorupdate) para criar um grupo de sincronização.
 
Ao criar um grupo de sincronização, não transmita o esquema de sincronização (tabela\coluna) nem masterSyncMemberName, porque, no momento, o grupo de sincronização ainda não tem informações de tabela\coluna.

Exemplo de solicitação para criar um grupo de sincronização: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Exemplo de resposta para criar um grupo de sincronização:

Código de status: 200
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

Código de status: 201
```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

## <a name="create-sync-member"></a>Criar membro de sincronização

Use o modelo [criar ou atualizar](/rest/api/sql/syncmembers/createorupdate) para criar um membro de sincronização.

Exemplo de solicitação para criar um membro de sincronização:

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```
Exemplo de resposta para criar um membro de sincronização:

Código de status: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Código de status: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="refresh-schema"></a>Atualizar esquema

Após o grupo de sincronização ser criado com êxito, atualize o esquema usando os modelos a seguir.

Use o modelo [atualizar esquema de hub](/rest/api/sql/syncgroups/refreshhubschema)  para atualizar o esquema do banco de dados hub. 

Exemplo de solicitação para atualizar um esquema de banco de dados hub: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/refreshHubSchema?api-version=2015-05-01-preview
```

Exemplo de resposta para atualizar um esquema de banco de dados hub: 

Código de status: 200

Código de status: 202

Use o modelo [listar esquemas de hub](/rest/api/sql/syncgroups/listhubschemas) para listar o esquema de banco de dados hub. 

Use o modelo [atualizar esquema de membro](/rest/api/sql/syncmembers/refreshmemberschema) para atualizar o esquema de banco de dados membro. 

Use o modelo [listar esquema de membro](/rest/api/sql/syncmembers/listmemberschemas) para listar o esquema de banco de dados membro. 

Vá para a próxima etapa apenas quando o esquema for atualizado com êxito. 

## <a name="update-sync-group"></a>Atualizar grupo de sincronização 

Use o modelo [criar ou atualizar](/rest/api/sql/syncgroups/createorupdate) para atualizar o grupo de sincronização.

Atualize o grupo de sincronização especificando o esquema de sincronização. Inclua o esquema e masterSyncMemberName, que é o nome que contém o esquema que você deseja usar. 

Exemplo de solicitação para atualizar o grupo de sincronização: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser"
  }
}
```

Exemplo de resposta para atualizar o grupo de sincronização: 

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```

```json
{
  "properties": {
    "interval": -1,
    "lastSyncTime": "0001-01-01T08:00:00Z",
    "conflictResolutionPolicy": "HubWin",
    "syncDatabaseId": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328",
    "hubDatabaseUserName": "hubUser",
    "syncState": "NotReady"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-3521/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187",
  "name": "syncgroupcrud-3187",
  "type": "Microsoft.Sql/servers/databases/syncGroups"
}
```
## <a name="update-sync-member"></a>Atualizar membro de sincronização

Use o modelo [criar ou atualizar](/rest/api/sql/syncmembers/createorupdate) para atualizar o membro de sincronização.

Exemplo de solicitação para atualizar um membro de sincronização: 

```http
PUT https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879?api-version=2015-05-01-preview
```

```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  }
}
```

Exemplo de resposta para atualizar um membro de sincronização: 

Código de status: 200
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

Código de status: 201
```json
{
  "properties": {
    "databaseType": "AzureSqlDatabase",
    "serverName": "syncgroupcrud-3379.database.windows.net",
    "databaseName": "syncgroupcrud-7421",
    "userName": "myUser",
    "syncDirection": "Bidirectional",
    "syncState": "UnProvisioned"
  },
  "id": "/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/syncMembers/syncgroupcrud-4879",
  "name": "syncgroupcrud-4879",
  "type": "Microsoft.Sql/servers/databases/syncGroups/syncMembers"
}
```

## <a name="trigger-sync"></a>Disparar sincronização

Use o modelo [disparar sincronização](/rest/api/sql/syncgroups/triggersync) para disparar uma operação de sincronização.

Exemplo de solicitação para disparar a operação de sincronização: 

```http
POST https://management.azure.com/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/syncgroupcrud-65440/providers/Microsoft.Sql/servers/syncgroupcrud-8475/databases/syncgroupcrud-4328/syncGroups/syncgroupcrud-3187/triggerSync?api-version=2015-05-01-preview
```

Exemplo de resposta para disparar a operação de sincronização: 

Código de status: 200

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../powershell-script-content-guide.md).

Para obter mais informações sobre a Sincronização de Dados SQL, consulte:

- Visão geral – [Sincronize dados em vários bancos de dados locais e na nuvem com a Sincronização de Dados SQL no Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Configurar sincronização de dados
    - Usar o portal do Azure – [Tutorial: Configurar a Sincronização de Dados SQL para sincronizar dados entre o Banco de Dados SQL do Azure e SQL Server](../sql-data-sync-sql-server-configure.md)
    - Usar o PowerShell – [Usar o PowerShell para sincronizar dados entre um banco de dados no Banco de Dados SQL do Azure e um SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync Agent – [Data Sync Agent para Sincronização de Dados SQL no Azure](../sql-data-sync-agent-overview.md)
- Melhores práticas – [Melhores práticas para a Sincronização de Dados SQL no Azure](../sql-data-sync-best-practices.md)
- Monitor – [monitore a Sincronização de Dados SQL com logs do Azure Monitor](../monitor-tune-overview.md)
- Solucionar problemas – [Solucionar problemas com a Sincronização de Dados SQL no Azure](../sql-data-sync-troubleshoot.md)
- Atualizar o esquema de sincronização
    - Usar Transact-SQL – [Automatizar a replicação de alterações de esquema da Sincronização de Dados SQL no Azure](../sql-data-sync-update-sync-schema.md)
    - Usar o PowerShell – [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](update-sync-schema-in-sync-group.md)

Para saber mais sobre Bancos de Dados SQL, confira:

- [Visão geral do Banco de Dados SQL](../sql-database-paas-overview.md)
- [Gerenciamento de ciclo de vida do banco de dados](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))