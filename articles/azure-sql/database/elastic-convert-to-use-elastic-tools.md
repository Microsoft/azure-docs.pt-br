---
title: Migrar bancos de dados existentes para escalar horizontalmente
description: Converter bancos de dados fragmentados para usar ferramentas de banco de dados elástico criando um Gerenciador de mapa de fragmentos
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c6ad8b4c80f4b9c2fdb3c1a14209dcf0febc89e9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92787135"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bancos de dados existentes para escalar horizontalmente
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Gerencie facilmente seus bancos de dados fragmentados existentes em escala horizontal usando ferramentas (como a [biblioteca de cliente do banco de dados elástico](elastic-database-client-library.md)). Primeiro converta um conjunto existente de bancos de dados para usar o [gerenciador de mapa de fragmentos](elastic-scale-shard-map-management.md).

## <a name="overview"></a>Visão geral

Para migrar um banco de dados fragmentado existente:

1. Prepare o [banco de dados do gerenciador de mapa de fragmentos](elastic-scale-shard-map-management.md).
2. Criar o mapa de fragmentos.
3. Preparar os fragmentos individuais.  
4. Adicione os mapeamentos ao mapa de fragmentos.

Essas técnicas podem ser implementadas usando a [biblioteca de cliente do .NET Framework](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)ou os scripts do PowerShell encontrados no [banco de dados SQL do Azure-scripts de ferramentas de banco de dados elástico](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Os exemplos aqui usam os scripts do PowerShell.

Para saber mais sobre o ShardMapManager, confira [Gerenciamento de mapa de fragmentos](elastic-scale-shard-map-management.md). Para obter uma visão geral das ferramentas de banco de dados elástico, consulte [visão geral dos recursos do banco de dados elástico](elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar o banco de dados do gerenciador de mapa de fragmentos

O gerenciador de mapa de fragmentos é um banco de dados especial que contém os dados para gerenciar bancos de dados escalonados horizontalmente. Você pode usar um banco de dados existente ou criar um novo banco de dados. Um banco de dados que age como o gerenciador de mapa de fragmentos não deve ser o mesmo banco de dados que o do fragmento. O script do PowerShell não cria o banco de dados para você.

## <a name="step-1-create-a-shard-map-manager"></a>Etapa 1: criar um Gerenciador de mapa de fragmentos

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>Para recuperar o gerenciador de mapa de fragmentos

Após a criação, você pode recuperar o gerenciador de mapa de fragmentos com este cmdlet. Esta etapa será necessária toda vez que você precisar usar o objeto ShardMapManager.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>Etapa 2: criar o mapa de fragmentos

Selecione o tipo de mapa de fragmentos a ser criado. A escolha depende da arquitetura do banco de dados:

1. Um locatário único por banco de dados (para termos, consulte o [glossário](elastic-scale-glossary.md).)
2. Vários locatários por banco de dados (dois tipos):
   1. Mapeamento de lista
   2. Mapeamento de intervalo

Para um modelo de locatário único, crie um mapa de fragmentos de **mapeamento de lista** . O modelo de locatário único atribui um banco de dados por locatário. Esse é um modelo eficaz para desenvolvedores de SaaS, pois simplifica o gerenciamento.

![Mapeamento de lista][1]

O modelo multilocatário atribui vários locatários a um banco de dados individual (e você pode distribuir grupos de locatários entre vários bancos de dados). Use esse modelo quando você esperar que cada locatário tenha necessidades de dados pequenas. Nesse modelo, atribua um intervalo de locatários para um banco de dados usando o **mapeamento intervalo** .

![Mapeamento de intervalo][2]

Ou então, você pode implementar um modelo de banco de dados multilocatário usando um *mapeamento de lista* para atribuir vários locatários a um banco de dados individual. Por exemplo, DB1 é usado para armazenar informações sobre a ID do locatário 1 e 5, e o DB2 armazena dados do locatário 7 e do locatário 10.

![Vários locatários em um único banco de dados][3]

**Com base na sua escolha, escolha uma destas opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: criar um mapa de fragmentos para um mapeamento de lista

Crie um mapa de fragmentos usando o objeto ShardMapManager.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: criar um mapa de fragmentos para um mapeamento de intervalo

Para utilizar esse padrão de mapeamento, os valores de ID do locatário precisam ser intervalos contínuos. É possível ter lacunas nos intervalos, pulando o intervalo ao criar os bancos de dados.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>Opção 3: Mapeamentos de lista em um banco de dados individual

A configuração desse padrão também exige a criação de um mapa de lista conforme mostrado na etapa 2, opção 1.

## <a name="step-3-prepare-individual-shards"></a>Etapa 3: Preparar os fragmentos individuais

Adicione cada fragmento (banco de dados)ao gerenciador de mapa de fragmentos. Isso prepara os bancos de dados individuais para armazenar informações de mapeamento. Execute esse método em cada fragmento.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>Etapa 4: Adicionar mapeamentos

A adição de mapeamentos depende do tipo de mapa de fragmentos criado. Se tiver criado um mapa de lista, você adicionará mapeamentos de lista. Se tiver criado um mapa de intervalo, você adicionará mapeamentos de intervalo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: mapear os dados para um mapeamento de lista

Mapeie os dados adicionando um mapeamento de lista para cada locatário.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: mapear os dados para um mapeamento de intervalo

Adicione os mapeamentos de intervalo de todas as associações entre intervalo de ID do locatário e banco de dados:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>Etapa 4 Option 3: mapear os dados para vários locatários em um banco de dados individual

Para cada locatário, execute o Add-ListMapping (opção 1).

## <a name="checking-the-mappings"></a>Verificando os mapeamentos

Informações sobre os fragmentos existentes e os mapeamentos associados a eles podem ser consultadas usando os seguintes comandos:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Resumo

Após ter concluído a configuração, você pode começar a usar a biblioteca de cliente do Banco de Dados Elástico. Você também pode usar o [roteamento dependente de dados](elastic-scale-data-dependent-routing.md) e a [consulta de vários fragmentos](elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Próximas etapas

Obtenha os scripts do PowerShell do [Azure SQL Database-Elastic scripts de ferramentas de banco de dados](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas também estão no GitHub: [Azure/elastic-db-tools](https://github.com/Azure/elastic-db-tools).

Use a ferramenta de divisão e mesclagem para mover dados de/para um modelo multilocatário de/para um modelo de locatário único. Consulte [Ferramenta de divisão e mesclagem](elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre os padrões comuns da arquitetura de dados dos aplicativos do banco de dados SaaS (software como serviço) multilocatário, consulte [Padrões de Design para Aplicativos SaaS multilocatário com o Banco de Dados SQL do Azure](saas-tenancy-app-design-patterns.md).

## <a name="questions-and-feature-requests"></a>Perguntas e solicitações de recursos

Em caso de dúvidas, use a [página de perguntas e respostas da Microsoft sobre o Banco de Dados SQL](/answers/topics/azure-sql-database.html), e para solicitações de recurso, adicione-as ao [Fórum de comentários do Banco de Dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/elastic-convert-to-use-elastic-tools/multipleonsingledb.png