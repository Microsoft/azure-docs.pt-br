---
title: Consultar bancos de dados fragmentados
description: Execute consultas entre fragmentos usando a biblioteca de cliente de banco de dados elástico.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: how-to
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 5a0dd12efb9d94bda264b3bd04b05cdc3df917e5
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786625"
---
# <a name="multi-shard-querying-using-elastic-database-tools"></a>Consulta de múltiplos fragmentos usando ferramentas do banco de dados elástico
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

## <a name="overview"></a>Visão geral

Com as [ferramentas de Banco de Dados Elástico](elastic-scale-introduction.md), você pode criar soluções de banco de dados fragmentado. **Consulta de vários fragmentos** é usada para tarefas, como coleta/relatórios de dados que exigem a execução de uma consulta que se estende por vários fragmentos. (Compare-a com o [roteamento dependente de dados](elastic-scale-data-dependent-routing.md), que executa todo o trabalho em um único fragmento).

1. Obtenha um **RangeShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) ou **ListShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) usando o método **TryGetRangeShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)), **TryGetListShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)) ou **GetShardMap** ( [Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)). Consulte [Construindo um ShardMapManager](elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [Obter um RangeShardMap ou ListShardMap](elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Criar um **MultiShardConnection** (objeto [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardconnection), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection)).
3. Criar um **MultiShardStatement ou MultiShardCommand** ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
4. Defina a **propriedade CommandText** ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)) para um comando T-SQL.
5. Execute o comando chamando o método **ExecuteQueryAsync ou ExecuteReader** ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardstatement.executeQueryAsync), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand)).
6. Veja os resultados usando a classe **MultiShardResultSet ou MultiShardDataReader** ( [Java](/java/api/com.microsoft.azure.elasticdb.query.multishard.multishardresultset), [.NET](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader)).

## <a name="example"></a>Exemplo

O código a seguir ilustra o uso da consulta de vários fragmentos usando um determinado **ShardMap** chamado *myShardMap* .

```csharp
using (MultiShardConnection conn = new MultiShardConnection(myShardMap.GetShards(), myShardConnectionString))
{
    using (MultiShardCommand cmd = conn.CreateCommand())
    {
        cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable";
        cmd.CommandType = CommandType.Text;
        cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn;
        cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults;

        using (MultiShardDataReader sdr = cmd.ExecuteReader())
        {
            while (sdr.Read())
            {
                var c1Field = sdr.GetString(0);
                var c2Field = sdr.GetFieldValue<int>(1);
                var c3Field = sdr.GetFieldValue<Int64>(2);
            }
        }
    }
}
```

Uma diferença importante é a construção de fragmentos várias conexões. Quando **SqlConnection** opera em um banco de dados individual, o **MultiShardConnection** usa uma **_coleção de fragmentos_*_ como sua entrada. Popular a coleção de fragmentos de um mapa de fragmentos. Em seguida, a consulta é executada na coleção de fragmentos usando a semântica _* Union All** para montar um único resultado geral. Como alternativa, o nome do fragmento que origina a linha pode ser adicionado à saída usando a propriedade **ExecutionOptions** no comando.

Observe a chamada para **myShardMap.GetShards()** . Esse método recupera todos os fragmentos do mapa de fragmentos e fornece uma maneira fácil de executar uma consulta em todos os bancos de dados relevantes. A coleção de fragmentos para uma consulta de vários fragmento pode ser refinada ainda mais executando uma consulta LINQ sobre a coleção retornada da chamada para **myShardMap.GetShards()** . Em combinação com a política de resultados parciais, a funcionalidade atual de consultas do fragmento vários foi projetado para funcionar bem para dezenas, centenas de fragmentos.

Uma limitação com as consultas de vários fragmentos no momento é a ausência de validação de fragmentos e shardlets são consultados. Enquanto o roteamento dependentes de dados verifica que um determinado fragmento faz parte do mapa do fragmento no momento da consulta, consultas de vários fragmentos não executam essa verificação. Isso pode levar à execução de consultas em vários fragmentos em bancos de dados que já foram removidos do mapa de fragmentos.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Consultas de vários fragmentos e operações de divisão/mesclagem

Consultas de vários fragmentos não verificam se shardlets do banco de dados consultado estão participando de operações de divisão/mesclagem em andamento. (Consulte [dimensionamento usando a ferramenta de divisão/mesclagem do banco de dados elástico](elastic-scale-overview-split-and-merge.md).) Isso pode levar a inconsistências em que as linhas do mesmo shardlet mostram para vários bancos de dados na mesma consulta de vários fragmentos. Lembre-se dessas limitações e considere drenar as operações de divisão/mesclagem em execução e alterações no mapa de fragmentos ao executar consultas de vários fragmentos.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]