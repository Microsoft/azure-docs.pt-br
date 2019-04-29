---
title: Gerenciamento de credenciais na biblioteca de cliente de banco de dados elástico | Microsoft Docs
description: Como definir o nível certo de credenciais de administrador para somente leitura em aplicativos de banco de dados elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/03/2019
ms.openlocfilehash: e6c7d682d9b16266208baeeff14168b3da157251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584872"
---
# <a name="credentials-used-to-access-the-elastic-database-client-library"></a>Credenciais usadas para acessar a biblioteca de cliente do Banco de Dados Elástico

A [biblioteca de cliente do Banco de Dados Elástico](sql-database-elastic-database-client-library.md) usa três tipos diferentes de credenciais para acessar o [gerenciador de mapa de fragmento](sql-database-elastic-scale-shard-map-management.md). Dependendo da necessidade, use a credencial com o menor nível de acesso possível.

* **Credenciais de gerenciamento**: para criar ou manipular um gerenciador de mapa de fragmentos. (Consulte o [glossário](sql-database-elastic-scale-glossary.md).)
* **Credenciais de acesso**: para acessar um gerenciador de mapa de fragmentos existente para obter informações sobre fragmentos.
* **As credenciais de conexão**: para conectar a fragmentos.

Consulte [Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md)

## <a name="about-management-credentials"></a>Sobre as credenciais de gerenciamento

As credenciais de gerenciamento são usadas para criar um objeto **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)) para aplicativos que lidam com mapas de fragmento. (Por exemplo, consulte [Adicionando um fragmento usando ferramentas do Banco de Dados Elástico](sql-database-elastic-scale-add-a-shard.md) e [Roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md)). O usuário da biblioteca cliente de escala elástica cria os usuários do SQL e os logons do SQL e verifica se cada um recebeu as permissões de leitura/gravação no banco de dados de mapa de fragmentos global e também em todos os bancos de dados de fragmentos. Essas credenciais são usadas para manter o mapa do fragmento globais e os mapas de fragmento local quando as alterações para o mapa do fragmento são executadas. Por exemplo, use as credenciais de gerenciamento para criar o objeto do gerenciador de mapa de fragmentos usando **GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)):

```java
// Obtain a shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmAdminConnectionString,ShardMapManagerLoadPolicy.Lazy);
```

A variável **smmAdminConnectionString** é uma cadeia de conexão que contém as credenciais de gerenciamento. A ID de usuário e a senha fornecem acesso de leitura/gravação ao banco de dados de mapa de fragmentos e aos fragmentos individuais. A cadeia de conexão de gerenciamento também inclui o nome do servidor e o nome do banco de dados para identificar o banco de dados de mapa do fragmento global. Aqui está uma cadeia de conexão típica para essa finalidade:

```java
"Server=<yourserver>.database.windows.net;Database=<yourdatabase>;User ID=<yourmgmtusername>;Password=<yourmgmtpassword>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;”
```

Não use valores no formato "username@server" e, em vez disso, use somente o valor "username".  Isso ocorre porque as credenciais devem funcionar no banco de dados do Gerenciador de mapa do fragmento e fragmentos individuais, que podem estar em servidores diferentes.

## <a name="access-credentials"></a>Credenciais de acesso

Ao criar um gerenciador de mapa de fragmentos em um aplicativo que não administra mapas de fragmentos, use credenciais que tenham permissões somente leitura no mapa de fragmentos global. As informações recuperadas do mapa de fragmentos global com essas credenciais são usadas para o [roteamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e para popular o cache do mapa de fragmentos no cliente. As credenciais são fornecidas por meio do mesmo padrão de chamada para **GetSqlShardMapManager**:

```java
// Obtain shard map manager.
ShardMapManager shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(smmReadOnlyConnectionString, ShardMapManagerLoadPolicy.Lazy);  
```

Observe o uso de **smmReadOnlyConnectionString** para refletir o uso de credenciais diferentes para esse acesso em nome dos usuários **não administradores**; essas credenciais não devem fornecer permissões de gravação ao mapa de fragmentos global.

## <a name="connection-credentials"></a>As credenciais de conexão

Credenciais adicionais são necessárias ao usar o método **OpenConnectionForKey** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)) para acessar um fragmento associado a uma chave de fragmentação. Essas credenciais precisam fornecer permissões para acesso somente leitura às tabelas de mapa de fragmento local que residem no fragmento. Isso é necessário para executar a validação de conexão para roteamento dependentes de dados sobre o fragmento. Este snippet de código permite o acesso a dados no contexto do roteamento dependente de dados:

```csharp
using (SqlConnection conn = rangeMap.OpenConnectionForKey<int>(targetWarehouse, smmUserConnectionString, ConnectionOptions.Validate))
```

Neste exemplo, **smmUserConnectionString** contém a cadeia de conexão para as credenciais do usuário. Para o banco de dados SQL Azure, eis uma cadeia de conexão típica para as credenciais do usuário:

```java
"User ID=<yourusername>; Password=<youruserpassword>; Trusted_Connection=False; Encrypt=True; Connection Timeout=30;”  
```

Assim como acontece com as credenciais de administrador, não é possível valores no formato "username@server". Em vez disso, use "username".  Observe que a cadeia de conexão não contém um nome do servidor e banco de dados. Isso ocorre porque a chamada **OpenConnectionForKey** redireciona automaticamente a conexão para o fragmento correto com base na chave. Portanto, o nome do banco de dados e o nome do servidor não são fornecidos.

## <a name="see-also"></a>Consulte 

[Gerenciamento de bancos de dados e logons no Banco de Dados SQL do Azure](sql-database-manage-logins.md)

[Protegendo o Banco de Dados SQL](sql-database-security-overview.md)

[Introdução a trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-getting-started.md)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
