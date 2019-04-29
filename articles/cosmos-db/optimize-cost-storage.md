---
title: Otimizar o custo de armazenamento no Azure Cosmos DB
description: Este artigo explica como gerenciar os custos de armazenamento para os dados armazenados no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: aed32db257ddbc8cb5b8f4af5f27024c23583798
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928906"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Otimizar o custo de armazenamento no Azure Cosmos DB

O Azure Cosmos DB oferece armazenamento ilimitado e taxa de transferência. Ao contrário de taxa de transferência, que você precisa provisionar/configurar em seus contêineres do Cosmos do Azure ou bancos de dados, o armazenamento é cobrado com base em uma base de consumo. Você será cobrado somente para o armazenamento lógico que você consumir e não precisa reservar nenhum armazenamento de antemão. O armazenamento automaticamente aumenta e diminui com base nos dados que você adicionar ou remover um contêiner do Azure Cosmos DB.

## <a name="storage-cost"></a>Custo de armazenamento

O armazenamento é cobrado com a unidade de GBs. Armazenamento baseado em SSD local é usado por seus dados e indexação. O armazenamento total usado é igual para o armazenamento necessário para os dados e os índices usados em todas as regiões em que você está usando o Azure Cosmos DB. Se você replicar globalmente uma conta de Cosmos do Azure em três regiões, você pagará pelo custo total de armazenamento em cada uma dessas três regiões. Para estimar o requisito de armazenamento, consulte a ferramenta [planejador de capacidade](https://www.documentdb.com/capacityplanner). O custo de armazenamento no Azure Cosmos DB é US $0,25 GB/mês, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para obter as atualizações mais recentes. Você pode configurar alertas para determinar o armazenamento usado por seu contêiner do Azure Cosmos, para monitorar seu armazenamento, consulte o artigo [Monitor do Azure Cosmos DB](monitor-accounts.md)).

## <a name="optimize-cost-with-item-size"></a>Otimizar o custo com o tamanho do item

O Azure Cosmos DB espera que o tamanho do item seja 2 MB ou menos para um desempenho ideal e benefícios de custo. Se você precisar de qualquer item para armazenar mais de 2 MB de dados, considere a possibilidade de reformular o esquema de item. No caso raro que você não pode recriar o esquema, você pode dividir o item em subitens e vinculá-los logicamente a um identificador comum (ID). Todos os recursos do Azure Cosmos DB funcionam de maneira consistente com ancoragem para esse identificador lógico.

## <a name="optimize-cost-with-indexing"></a>Otimizar o custo com a indexação

Por padrão, os dados são automaticamente indexados, o que pode aumentar o armazenamento total consumido. No entanto, você pode aplicar políticas de índice personalizadas para reduzir essa sobrecarga. Indexação automática que não foi ajustada por meio da diretiva é cerca de 10 a 20% do tamanho do item. Removendo ou personalizando as políticas de índice, você não paga por gravações de custo extra e não exige a capacidade de taxa de transferência adicional. Consulte [Indexar no Azure Cosmos DB](indexing-policies.md) para configurar políticas de indexação personalizadas. Se você tiver trabalhado com bancos de dados relacionais antes, você pode pensar que "indexar tudo" significa a duplicação de armazenamento ou superior. No entanto, no Azure Cosmos DB, no caso médio, é muito menor. No Azure Cosmos DB, a sobrecarga de armazenamento de índice é normalmente baixa (10 a 20%) mesmo com a indexação automática, porque foi projetado para um volume de armazenamento baixo. Ao gerenciar a política de indexação, você pode controlar a compensação de desempenho de consulta e o espaço de índice de uma maneira mais refinada.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Otimizar o custo com tempo live e o feed de alterações

Depois que você não precisa mais dos dados você pode excluí-los normalmente de sua conta do Azure Cosmos usando [tempo de vida](time-to-live.md), [feed de alterações](change-feed.md) ou você pode migrar os dados antigos para outro armazenamento de dados, como o armazenamento de BLOBs do Azure ou data warehouse do Azure. Com a vida úti" ou TTL, o Azure Cosmos DB fornece a capacidade de excluir itens automaticamente de um contêiner após um determinado período de tempo. Por padrão, é possível definir a Vida Útil no nível do contêiner e substituir o valor em uma base por item. Após definir a Vida Útil em um nível de item ou contêiner, o Azure Cosmos DB removerá automaticamente esses itens após o período de tempo, desde a hora em que foram modificados pela última vez. Ao usar o feed de alterações, você pode migrar dados para qualquer outro contêiner no Azure Cosmos DB ou para um armazenamento de dados externo. A migração leva zero tempo de inatividade e quando você estiver fazendo a migração, você pode excluir ou definir a vida útil para excluir o contêiner do Azure Cosmos de origem.

## <a name="optimize-cost-with-rich-media-data-types"></a>Otimizar o custo com os tipos de dados de mídia avançada 

Se você quiser armazenar tipos de mídia avançada, por exemplo, vídeos, imagens, etc., você tem várias opções no Azure Cosmos DB. Uma opção é armazenar esses tipos de mídia avançada como itens do Azure Cosmos. Há um limite de 2 MB por item e você pode evitar esse limite por meio do encadeamento do item de dados em vários subitens. Ou você pode armazená-los no armazenamento de BLOBs do Azure e usar os metadados para fazer referência a eles de seus itens do Azure Cosmos. Há alguns prós e contras com essa abordagem. A primeira abordagem obtém o melhor desempenho em termos de latência, SLAs de taxa de transferência mais recursos de distribuição global turnkey para os tipos de dados de mídia avançada, além de seus itens regulares do Azure Cosmos. No entanto, o suporte está disponível em um preço mais alto. Ao armazenar a mídia no armazenamento de Blob do Azure, também seria possível reduzir seus custos gerais. Se a latência for crítica, você pode usar o armazenamento premium para arquivos de mídia avançada que são referenciados de itens do Cosmos do Azure. Isso se integra nativamente com a CDN para servir imagens do servidor de borda a custo menor para contornar a restrição geográfica. A desvantagem desse cenério é que você precisa lidar com dois serviços - Azure Cosmos DB e o armazenamento de Blob do Azure, que pode aumentar os custos operacionais. 

## <a name="check-storage-consumed"></a>Verificação do armazenamento consumido

Para verificar o consumo de armazenamento de um contêiner do Azure Cosmos, você pode executar uma solicitação HEAD ou GET no contêiner e inspecionar os cabeçalhos `x-ms-request-quota` e o `x-ms-request-usage`. Como alternativa, ao trabalhar com o SDK do .NET, você pode usar o [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)), e [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) propriedades para obter o armazenamento consumido.

## <a name="using-sdk"></a>Usando o SDK

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entendendo sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais em [Otimizar o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais em [Otimizar o custo de leituras e gravações](optimize-cost-reads-writes.md)
* Saiba mais sobre [Otimizando o custo de consultas](optimize-cost-queries.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)

