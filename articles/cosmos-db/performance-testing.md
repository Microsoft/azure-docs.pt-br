---
title: Teste de desempenho e escala com o Azure Cosmos DB
description: Saiba como realizar testes de desempenho e escala com o Azure Cosmos DB. Também é possível avaliar a funcionalidade do Azure Cosmos DB para cenários de aplicativos de alto desempenho.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 6d2863e39b5f28c56e2b9045513aa83326d8b8c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60926543"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Teste de desempenho e escala com o Azure Cosmos DB

O teste de desempenho e escalabilidade é uma etapa importante no desenvolvimento de aplicativos. Para muitos aplicativos, a camada de banco de dados tem um impacto significativo no desempenho e escalabilidade gerais. Portanto, é um componente crítico do teste de desempenho. O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) foi desenvolvido para escala elástica e desempenho previsível. Esses recursos o tornam ideal para aplicativos que precisam de uma camada de banco de dados de alto desempenho. 

Este artigo é uma referência para desenvolvedores que implementam conjuntos de testes de desempenho para as cargas de trabalho do Azure Cosmos DB. Ele também pode ser usado para avaliar o Azure Cosmos DB para cenários de aplicativos de alto desempenho. Ele se concentra principalmente nos testes de desempenho de banco de dados isolados, mas também inclui práticas recomendadas para aplicativos de produção.

Após ler este artigo, você poderá responder as perguntas a seguir: 

* Onde posso encontrar um aplicativo cliente do .NET de exemplo para testar o desempenho do Azure Cosmos DB? 
* Como fazer para obter altos níveis de produtividade com o Azure Cosmos DB em meu aplicativo cliente?

Para começar a codificar, baixe o projeto na [Amostra de Teste de Desempenho do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> A meta deste aplicativo é demonstrar como obter o melhor desempenho do Azure Cosmos DB com um número pequeno de computadores cliente. A meta da amostra não é atingir a capacidade de produtividade de pico do Azure Cosmos DB (que pode ser dimensionada sem limites).
> 
> 

Se você estiver procurando opções de configuração do lado do cliente para melhorar o desempenho do Cosmos DB, veja [Dicas de desempenho do Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Execute o aplicativo de teste de desempenho
A maneira mais rápida de começar é compilar e executar o exemplo do .NET, conforme descrito nas etapas a seguir. Você também pode examinar o código-fonte e implementar configurações semelhantes nos seus aplicativos clientes.

**Etapa 1:** Baixe o projeto no [Exemplo de teste de desempenho do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) ou bifurque o repositório GitHub.

**Etapa 2:** Modifique as configurações de EndpointUrl, AuthorizationKey, CollectionThroughput e DocumentTemplate (opcional) em App.config.

> [!NOTE]
> Antes de provisionar as coleções com alta produtividade, consulte a [Página de preços](https://azure.microsoft.com/pricing/details/cosmos-db/) para estimar os custos por coleção. O Azure Cosmos DB cobra o armazenamento e a produtividade de forma independente por hora. Você pode reduzir os custos excluindo ou diminuindo a produtividade de suas coleções de Azure Cosmos DB após o teste.
> 
> 

**Etapa 3:** Compile e execute o aplicativo de console na linha de comando. Você verá algo semelhante ao mostrado a seguir:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Etapa 4 (se necessário):** A taxa de transferência (RU/s) da ferramenta deverá ser igual ou maior que a taxa de transferência provisionada da coleção ou de um conjunto de coleções. Caso contrário, o aumento do DegreeOfParallelism em pequenos incrementos pode ajudar você a atingir o limite. Se a produtividade do aplicativo cliente estagnar, inicie várias instâncias do aplicativo em computadores cliente adicionais. Se precisar de ajuda com esta etapa, envie um email para askcosmosdb@microsoft.com ou preencha um tíquete de suporte no [portal do Azure](https://portal.azure.com).

Uma vez que o aplicativo estiver em execução, você poderá experimentar [Políticas de indexação](index-policy.md) e [Níveis de consistência](consistency-levels.md) diferentes para entender seu impacto na produtividade e na latência. Você também pode examinar o código-fonte e implementar configurações semelhantes no seus pacotes de teste ou aplicativos de produção.

## <a name="next-steps"></a>Próximas etapas
Neste artigo, analisamos como você pode realizar testes de desempenho e escala com o Azure Cosmos DB usando um aplicativo de console .NET. Para obter mais informações, consulte os seguintes artigos:

* [Amostra de teste de desempenho do Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Opções de configuração do cliente para melhorar o desempenho do Azure Cosmos DB](performance-tips.md)
* [Particionamento do lado do servidor no Azure Cosmos DB](partition-data.md)


