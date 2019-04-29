---
title: Níveis de desempenho do Azure Cosmos DB desativados
description: Saiba mais sobre os níveis de desempenho S1, S2 e S3 disponíveis anteriormente no Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: sngun
ms.openlocfilehash: 06fa98ae4acc2252d8866858ed0e2194ed84ff79
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60928270"
---
# <a name="retiring-the-s1-s2-and-s3-performance-levels"></a>Desativando os níveis de desempenho S1, S2 e S3

> [!IMPORTANT] 
> Os níveis de desempenho S1, S2 e S3 abordados neste artigo estão sendo desativados e não estão mais disponíveis para novas contas do Azure Cosmos DB.
>

Este artigo fornece uma visão geral dos níveis de desempenho S1, S2 e S3 e discute como as coleções que usam esses níveis de desempenho podem ser migrado para o único coleções particionadas. Após ler este artigo, você poderá responder as perguntas a seguir:

- [Quais são os níveis de desempenho S1, S2 e S3 está sendo desativados?](#why-retired)
- [Como as coleções de partição única e as coleções particionadas se comparam com os níveis de desempenho S1, S2 e S3?](#compare)
- [O que é necessário fazer para garantir o acesso ininterrupto aos meus dados?](#uninterrupted-access)
- [Como a minha coleção mudará após a migração?](#collection-change)
- [Como minha cobrança mudará depois que eu migrar para as coleções de partição única?](#billing-change)
- [E se eu precisar de mais de 10 GB de armazenamento?](#more-storage-needed)
- [Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planejada?](#change-before)
- [Como eu migro dos níveis de desempenho S1, S2 e S3 para as coleções de partição única sozinho?](#migrate-diy)
- [Como serei afetado se eu for um cliente EA?](#ea-customer)

<a name="why-retired"></a>

## <a name="why-are-the-s1-s2-and-s3-performance-levels-being-retired"></a>Por que os níveis de desempenho S1, S2 e S3 estão sendo desativados?

Os níveis de desempenho S1, S2 e S3 não oferecem a flexibilidade que a oferta do Azure Cosmos DB padrão fornece. Com os níveis de desempenho S1, S2, S3, a produtividade e a capacidade de armazenamento eram predefinidas e não ofereciam elasticidade. Agora, o Azure Cosmos DB oferece a capacidade de personalizar a produtividade e o armazenamento, oferecendo muito mais flexibilidade em sua capacidade de dimensionar, conforme suas necessidades mudam.

<a name="compare"></a>

## <a name="how-do-single-partition-collections-and-partitioned-collections-compare-to-the-s1-s2-s3-performance-levels"></a>Como as coleções de partição única e as coleções particionadas se comparam com os níveis de desempenho S1, S2 e S3?

A tabela a seguir compara as opções da taxa de transferência e armazenamento disponíveis nas coleções de partição única, coleções particionadas e níveis de desempenho S1, S2 e S3. Aqui está um exemplo para a região no Leste dos EUA 2:

|   |Coleção particionada|Coleção de partição única|S1|S2|S3|
|---|---|---|---|---|---|
|Taxa de transferência máxima|Ilimitado|10 K RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Taxa de transferência mínima|2.5 K RU/s|400 RU/s|250 RU/s|1 K RU/s|2.5 K RU/s|
|Armazenamento máximo|Ilimitado|10 GB|10 GB|10 GB|10 GB|
|Preço (mensal)|Taxa de transferência: $ 6/100 RU/s<br><br>Armazenamento: $ 0,25/GB|Taxa de transferência: $ 6/100 RU/s<br><br>Armazenamento: $ 0,25/GB|$ 25 dólares americanos|$ 50 dólares americanos|$ 100 dólares americanos|

Você é um cliente EA? Se for, consulte [Como serei afetado se eu for um cliente EA?](#ea-customer)

<a name="uninterrupted-access"></a>

## <a name="what-do-i-need-to-do-to-ensure-uninterrupted-access-to-my-data"></a>O que é necessário fazer para garantir o acesso ininterrupto aos meus dados?

Se você tiver uma coleção de S1, S2 ou S3, você deve migrar a coleção para uma coleção de partição única programaticamente [usando o SDK .NET](#migrate-diy). 

<a name="collection-change"></a>

## <a name="how-will-my-collection-change-after-the-migration"></a>Como a minha coleção mudará após a migração?

Se você tiver uma coleção de S1, você pode migrá-los para uma coleção de partição única com taxa de transferência de 400 RU/s. 400 RU/s é a taxa de transferência mais baixa disponível com as coleções de partição única. No entanto, o custo de 400 RU/s em uma coleção de partição única é aproximadamente igual ao que você estava pagando com sua coleção S1 e 250 RU/s – assim, você não está pagando por 150 RU/s extras disponíveis.

Se você tiver uma coleção de S2, você pode migrá-los para uma coleção de partição única com o 1 K RU/s. Você não verá nenhuma alteração no nível da taxa de transferência.

Se você tiver uma coleção de S3, você pode migrá-los para uma coleção de partição única com 2,5 K RU/s. Você não verá nenhuma alteração no nível da taxa de transferência.

Em cada um desses casos, após a migração da coleção, você poderá personalizar o nível de taxa de transferência ou dimensioná-lo para cima e para baixo conforme necessário para fornecer acesso de baixa latência para os usuários. 

<a name="billing-change"></a>

## <a name="how-will-my-billing-change-after-i-migrated-to-the-single-partition-collections"></a>Como minha cobrança será alterado depois que eu migrado para as coleções de partição única?

Supondo que você tem 10 coleções S1, 1 GB de armazenamento para cada uma, na região Leste dos EUA, e migra essas 10 coleções S1 para 10 coleções de partição única em 400 RU/s (o nível mínimo). Se você mantiver as 10 coleções de partição única por um mês inteiro, sua fatura será semelhante ao seguinte:

![Como o preço de S1 para 10 coleções se compara a 10 coleções usando o preço para uma coleção de partição única](./media/performance-levels/s1-vs-standard-pricing.png)

<a name="more-storage-needed"></a>

## <a name="what-if-i-need-more-than-10-gb-of-storage"></a>E se eu precisar de mais de 10 GB de armazenamento?

Se você tiver uma coleção com o nível de desempenho S1, S2 ou S3 ou tem uma coleção de partição única, que têm 10 GB de armazenamento disponível, que você pode usar a ferramenta de migração de dados de banco de dados do Azure Cosmos para migrar os dados para uma coleção particionada com praticamente armazenamento ilimitado. Para obter informações sobre os benefícios de uma coleção particionada, consulte [Particionamento e escala no Azure Cosmos DB](sql-api-partition-data.md). 

<a name="change-before"></a>

## <a name="can-i-change-between-the-s1-s2-and-s3-performance-levels-before-the-planned-migration"></a>Posso mudar entre os níveis de desempenho S1, S2 e S3 antes da migração planejada?

Somente as contas existentes com desempenho S1, S2 e S3 podem ser alteradas e alterar os níveis de desempenho por meio de programação [usando o SDK .NET](#migrate-diy). Se você mudar de S1, S3 ou S3 para uma coleção de partição único, não será possível retornar aos níveis de desempenho S1, S2 ou S3.

<a name="migrate-diy"></a>

## <a name="how-do-i-migrate-from-the-s1-s2-s3-performance-levels-to-single-partition-collections-on-my-own"></a>Como eu migro dos níveis de desempenho S1, S2 e S3 para as coleções de partição única sozinho?

Você pode migrar dos níveis de desempenho S1, S2 e S3 para coleções com uma única partição programaticamente [usando o SDK .NET](#migrate-diy). Você pode fazer isso por conta própria antes da migração planejada para se beneficiar com as opções de taxa de transferência flexíveis disponíveis com coleções com uma única partição.

### <a name="migrate-to-single-partition-collections-by-using-the-net-sdk"></a>Migrar para coleções com uma única partição usando o SDK do .NET

Esta seção aborda apenas a alteração do nível de desempenho da coleção usando nossa [API .NET do SQL](sql-api-sdk-dotnet.md), mas o processo é semelhante para nossos outros SDKs.

Aqui está um snippet de código para mudar a taxa de transferência da coleção para 5.000 unidades de solicitação por segundo:
    
```csharp
    //Fetch the resource to be updated
    Offer offer = client.CreateOfferQuery()
                      .Where(r => r.ResourceLink == collection.SelfLink)    
                      .AsEnumerable()
                      .SingleOrDefault();

    // Set the throughput to 5000 request units per second
    offer = new OfferV2(offer, 5000);

    //Now persist these changes to the database by replacing the original resource
    await client.ReplaceOfferAsync(offer);
```

Visite o [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) para exibir exemplos adicionais e saber mais sobre os métodos oferecidos:

* [**ReadOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readofferasync.aspx)
* [**ReadOffersFeedAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.readoffersfeedasync.aspx)
* [**ReplaceOfferAsync**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.replaceofferasync.aspx)
* [**CreateOfferQuery**](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createofferquery.aspx)

<a name="ea-customer"></a>

## <a name="how-am-i-impacted-if-im-an-ea-customer"></a>Como serei afetado se eu for um cliente EA?

Os clientes EA terão o preço protegido até o final do contrato atual.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os preços e como gerenciar dados com o Azure Cosmos DB, conheça estes recursos:

1.  [Particionando dados no Cosmos DB](sql-api-partition-data.md). Compreenda a diferença entre contêineres de partição única e contêineres particionados, além de obter dicas de como implementar uma estratégia de particionamento para uma escala perfeita.
2.  [Preços do Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/). Saiba mais sobre o custo de provisionar a taxa de transferência e consumir o armazenamento.
3.  [Unidades de solicitação](request-units.md). Compreenda o consumo da taxa de transferência para os diferentes tipos de operação, por exemplo, Leitura, Gravação e Consulta.
