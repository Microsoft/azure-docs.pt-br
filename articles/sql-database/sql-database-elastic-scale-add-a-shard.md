---
title: Adicionando um fragmento usando ferramentas de banco de dados elástico | Microsoft Docs
description: Como usar APIs de Escala Elástica para adicionar novos fragmentos para um fragmento de conjunto.
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
ms.openlocfilehash: dda3c34dccfdaa041cf9f547244d5529482a3138
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60585805"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionando um fragmento usando ferramentas do Banco de Dados Elástico

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar um fragmento a um novo intervalo ou uma chave

Geralmente, os aplicativos precisam adicionar novos fragmentos para lidar com os dados que são esperados de novas chaves ou intervalos de chaves para um mapa do fragmento que já existe. Por exemplo, um aplicativo fragmentado por ID de locatário talvez tenha provisionar um novo fragmento para um novo locatário ou dados mensalmente fragmentados talvez precisem de um novo fragmento provisionado antes do início de cada novo mês.

Se o novo intervalo de valores de chave já não faz parte de um mapeamento existente, é simples adicionar o novo fragmento e associar a nova chave ou o intervalo para esse fragmento.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionar um fragmento e seu intervalo a um mapa de fragmentos existente

Este exemplo usa os métodos TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) a CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) e cria uma instância da classe ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)). No exemplo a seguir, um banco de dados denominado **sample_shard_2** e todos os objetos de esquema necessários dentro dele foram criados para conter o intervalo [300, 400).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added.
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
}

// Create the mapping and associate it with the new shard
sm.CreateRangeMapping(new RangeMappingCreationInfo<long>
                            (new Range<long>(300, 400), shard2, MappingStatus.Online));
```

Para a versão do .NET, você pode também usar o PowerShell como alternativa a criar um novo Gerenciador de Mapa de Fragmentos. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para adicionar um fragmento de uma parte vazia de um intervalo existente

Em algumas circunstâncias, você já mapeou um intervalo para um fragmento e parcialmente preencheu-o com dados, mas agora deseja que os dados futuros sejam direcionados para um fragmento diferente. Por exemplo, você fragmentou o intervalo por dia e já tem 50 dias alocados para um fragmento, mas no dia 24, você deseja que os dados futuros encaixem em um fragmento diferente. A [ferramenta de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) do banco de dados elástico pode executar essa operação, mas, se a movimentação de dados não for necessária (por exemplo, dados para o intervalo de dias [25, 50), ou seja, incluindo o dia 25, mas não o dia 50, ainda não existem), você poderá executar tudo isso usando as APIs de Gerenciamento de Mapa de Fragmentos diretamente.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: dividir um intervalo e atribuir a parte vazia a um fragmento adicionado recentemente

Um banco de dados chamado "sample_shard_2" e todos os objetos de esquema necessários dentro dele foram criados.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move
Shard shard2 = null;

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2))
{
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
}

// Split the Range holding Key 25
sm.SplitMapping(sm.GetMappingForKey(25), 25);

// Map new range holding [25-50) to different shard:
// first take existing mapping offline
sm.MarkMappingOffline(sm.GetMappingForKey(25));

// now map while offline to a different shard and take online
RangeMappingUpdate upd = new RangeMappingUpdate();
upd.Shard = shard2;
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd));
```

**Importante**:  Use essa técnica somente se você tiver certeza de que o intervalo para o mapeamento atualizado está vazio.  Os métodos anteriores não verificam os dados para o intervalo que está sendo movido, portanto, é melhor incluir verificações em seu código.  Se existirem linhas no intervalo que está sendo movido, a distribuição de dados real não corresponderá ao mapa do fragmento atualizado. Use a [ferramenta de divisão/mesclagem](sql-database-elastic-scale-overview-split-and-merge.md) para executar a operação nesses casos.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
