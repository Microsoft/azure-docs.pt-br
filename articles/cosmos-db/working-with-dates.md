---
title: Trabalhando com datas no Azure Cosmos DB
description: Saiba como trabalhar com datas no Azure Cosmos DB.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 5b482d00990add365a90f36bf6e8d047dc50efc0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467828"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Trabalhando com datas no Azure Cosmos DB
O Azure Cosmos DB fornece flexibilidade de esquema e indexação avançada por meio de um modelo de dados [JSON](https://www.json.org) nativo. Todos os recursos do Azure Cosmos DB, incluindo bancos de dados, contêineres, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como um requisito para ser portátil, o JSON (e o Azure Cosmos DB) dá suporte apenas a um conjunto pequeno de tipos básicos: Cadeia de caracteres, número, booliano, matriz, objeto e nulo. No entanto, o JSON é flexível e permite aos desenvolvedores e estruturas representar tipos mais complexos usando esses primitivos e compondo-os como objetos ou matrizes. 

Além dos tipos básicos, muitos aplicativos precisam do tipo [DateTime](https://msdn.microsoft.com/library/system.datetime(v=vs.110).aspx) para representar datas e carimbos de data/hora. Este artigo descreve como os desenvolvedores podem armazenar, recuperar e consultar datas no Azure Cosmos DB usando o SDK do .NET.

## <a name="storing-datetimes"></a>Armazenando DateTimes
Por padrão, o [SDK do Azure Cosmos DB](sql-api-sdk-dotnet.md) serializa os valores de DateTime como cadeias de caracteres [ISO 8601](https://www.iso.org/iso/catalogue_detail?csnumber=40874). A maioria dos aplicativos pode usar a representação de cadeia de caracteres padrão para DateTime pelos seguintes motivos:

* As cadeias de caracteres podem ser comparadas e a ordenação relativa dos valores de DateTime é preservada quando eles são transformados em cadeias de caracteres. 
* Essa abordagem não exige qualquer código personalizado ou atributos para conversão de JSON.
* As datas, conforme armazenadas no JSON, são legíveis para humanos.
* Essa abordagem pode aproveitar o índice do Azure Cosmos DB para um desempenho rápido de consulta.

Por exemplo, o snippet a seguir armazena um objeto `Order` que contém duas propriedades DateTime — `ShipDate` e `OrderDate` como um documento usando o SDK do .NET:

    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await client.CreateDocumentAsync("/dbs/orderdb/colls/orders", 
        new Order 
        { 
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14), 
            Total = 113.39
        });

Esse documento é armazenado no Azure Cosmos DB da seguinte maneira:

    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
    

Como alternativa, você pode armazenar DateTimes como carimbos de data/hora do Unix, ou seja, como um número que representa o número de segundos decorridos desde 1º de janeiro de 1970. A propriedade Timestamp (`_ts`) interna do Cosmos DB segue essa abordagem. Você pode usar a classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) para serializar DateTimes como números. 

## <a name="indexing-datetimes-for-range-queries"></a>Indexando DateTimes para consultas de intervalo
As consultas de intervalo são comuns com valores de DateTime. Por exemplo, se precisar localizar todos os pedidos criados desde ontem ou todos os pedidos enviados nos últimos cinco minutos, você precisará executar consultas de intervalo. Para executar essas consultas de modo eficiente, você deve configurar sua coleção para indexação de Intervalo em cadeias de caracteres.

    DocumentCollection collection = new DocumentCollection { Id = "orders" };
    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    await client.CreateDocumentCollectionAsync("/dbs/orderdb", collection);

Saiba mais sobre como configurar as políticas de indexação em [Políticas de indexação do Azure Cosmos DB](index-policy.md).

## <a name="querying-datetimes-in-linq"></a>Consultando DateTimes no LINQ
O SDK do .NET para SQL dá suporte automaticamente à consulta de dados armazenados no Azure Cosmos DB via LINQ. Por exemplo, o snippet a seguir mostra uma consulta do LINQ que filtra pedidos que foram enviados nos últimos três dias.

    IQueryable<Order> orders = client.CreateDocumentQuery<Order>("/dbs/orderdb/colls/orders")
        .Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
          
    // Translated to the following SQL statement and executed on Azure Cosmos DB
    SELECT * FROM root WHERE (root["ShipDate"] >= "2016-12-18T21:55:03.45569Z")

Saiba mais sobre a linguagem de consulta SQL do Azure Cosmos DB e o provedor LINQ em [Consultas ao Cosmos DB](how-to-sql-query.md).

Neste artigo, analisamos como armazenar, indexar e consultar DateTimes no Azure Cosmos DB.

## <a name="next-steps"></a>Próximas etapas
* Baixe e execute os [Exemplos de código no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Saiba mais sobre [consultas SQL](how-to-sql-query.md)
* Saiba mais sobre as [Políticas de indexação do Azure Cosmos DB](index-policy.md)
