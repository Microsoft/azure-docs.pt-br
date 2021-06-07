---
title: Otimizando o custo de suas solicitações no Azure Cosmos DB
description: Este artigo explica como otimizar os custos ao emitir solicitações em Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 36ecef007e10f9a090dbabc8b5a91fd473930141
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633845"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Otimizar o custo da solicitação no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Este artigo descreve como as solicitações de leitura e gravação são transvertidas em [unidades de solicitação](request-units.md) e como otimizar o custo dessas solicitações. As operações de leitura incluem leituras de ponto e consultas. As operações de gravação incluem inserir, substituir, excluir e Upsert de itens.

O Azure Cosmos DB oferece um conjunto avançado de operações de banco de dados que operam nos itens de um contêiner. O custo associado a cada uma dessas operações varia com base na CPU, E/S e memória necessárias para concluir a operação. Em vez de pensar e gerenciar recursos de hardware, você pode pensar em uma RU (unidade de solicitação) como uma medida única para os recursos necessários para executar várias operações de banco de dados para atender a uma solicitação.

## <a name="measuring-the-ru-charge-of-a-request"></a>Medindo a cobrança de RU de uma solicitação

É importante medir a cobrança de RU de suas solicitações para entender seu custo real e também avaliar a eficácia de suas otimizações. Você pode buscar esse custo usando o portal do Azure ou inspecionando a resposta enviada de Azure Cosmos DB por meio de um dos SDKs. Consulte [localizar o encargo de unidade de solicitação em Azure Cosmos DB](find-request-unit-charge.md) para obter instruções detalhadas sobre como fazer isso.

## <a name="reading-data-point-reads-and-queries"></a>Lendo dados: leituras de ponto e consultas

As operações de leitura em Azure Cosmos DB normalmente são ordenadas de forma mais rápida/mais eficiente para mais lenta/menos eficiente em termos de consumo de RU da seguinte maneira:  

* Leituras de ponto (pesquisa de chave/valor em uma única ID de item e chave de partição).
* Consulta com uma cláusula de filtro em uma chave de partição única.
* Consulta sem uma cláusula de filtro de igualdade ou intervalo em qualquer propriedade.
* Consulta sem filtros.

### <a name="role-of-the-consistency-level"></a>Função do nível de consistência

Ao usar os [níveis de consistência](consistency-levels.md) **forte** ou de desatualização **limitada** , o custo de ru de qualquer operação de leitura (ponto de leitura ou consulta) é duplicado.

### <a name="point-reads"></a>Leituras de ponto

O único fator que afeta a carga de RU de um ponto lido (além do nível de consistência usado) é o tamanho do item recuperado. A tabela a seguir mostra o custo de RU de leituras de ponto para itens que são de 1 KB e 100 KB de tamanho.

| **Tamanho do item** | **Custo de uma leitura de ponto** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RUs |

Como as leituras de ponto (pesquisas de chave/valor na ID do item) são o tipo mais eficiente de leitura, você deve verificar se a ID do item tem um valor significativo para que você possa buscar seus itens com um ponto de leitura (em vez de uma consulta) quando possível.

### <a name="queries"></a>Consultas

As unidades de solicitação para consultas dependem de uma série de fatores. Por exemplo, o número de itens do Azure Cosmos carregados/retornados, o número de pesquisas em relação ao índice, o tempo de compilação da consulta, etc. detalhes. O Azure Cosmos DB garante que a mesma consulta, quando executada nos mesmos dados, sempre consumirá o mesmo número de unidades de solicitação, mesmo com execuções repetidas. O perfil de consulta usando métricas de execução de consulta dá uma boa ideia de como as unidades de solicitação são gastas.  

Em alguns casos, você poderá ver uma sequência de 200 e 429 respostas, e unidades de solicitação de variável em uma execução paginada de consultas, porque as consultas serão executadas o mais rápido possível com base nas RUs disponíveis. Você poderá ver uma execução de consulta se desmembrar em várias páginas/viagens de ida e volta entre servidor e cliente. Por exemplo, 10.000 itens poderão ser retornados como várias páginas, cada uma cobrada com base no cálculo executado para a página. Quando você soma essas páginas, deve obter o mesmo número de RUs que receberia de toda a consulta.

#### <a name="metrics-for-troubleshooting-queries"></a>Métricas para solução de problemas de consultas

O desempenho e a taxa de transferência consumida por consultas (incluindo funções definidas pelo usuário) dependem principalmente do corpo da função. A maneira mais fácil de descobrir quanto tempo a execução da consulta é gasta no UDF e o número de RUs consumidas, é habilitando as métricas de consulta. Se você usar o SDK do .NET, aqui estão as métricas de consulta de exemplo retornadas pelo SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>Melhores práticas para otimizar consultas pelo custo 

Considere as seguintes melhores práticas ao otimizar consultas pelo custo:

* **Colocar vários tipos de entidade**

   Tente colocar vários tipos de entidade em um único contêiner ou na menor quantidade possível. Esse método gera benefícios não apenas em termos de preço, mas também em termos de transações e execução da consulta. As consultas têm como escopo um único contêiner, e as transações atômicas em vários registros por meio de procedimentos armazenados/gatilhos têm como escopo uma chave de partição em um único contêiner. A colocação de entidades no mesmo contêiner pode reduzir o número de viagens de ida e volta na rede para resolver relacionamentos entre registros. Assim, ela aumenta o desempenho de ponta a ponta, permite transações atômicas em vários registros em um conjunto de dados maior e, como resultado, reduz os custos. Se a colocação de vários tipos de entidade em um único contêiner (ou poucos) costuma ser difícil em seu cenário, talvez porque você esteja migrando um aplicativo existente e não deseje fazer alterações de código, considere provisionar a taxa de transferência no nível do banco de dados.  

* **Medir e ajustar para o uso mais baixo de unidades/segundo da solicitação**

   A complexidade de uma consulta afeta a quantidade de RUs (unidades de solicitação) consumida para uma operação. O número de predicados, a natureza dos predicados, o número de UDFs e o tamanho do conjunto de dados de origem. Todos esses fatores influenciam o custo das operações de consulta. 

Azure Cosmos DB fornece desempenho previsível em termos de taxa de transferência e latência usando um modelo de taxa de transferência provisionado. A taxa de transferência provisionada é representada em termos de [Unidades de Solicitação](request-units.md) por segundo ou RU/s. Uma Unidade de Solicitação (RU) é uma abstração lógica de recursos de computação como CPU, memória, e/s, etc. que são necessárias para executar uma solicitação. Taxa de transferência (RUs) é reservada e dedicada ao contêiner ou banco de dados para fornecer latência e taxa de transferência previsível. Taxa de transferência provisionada permite que o Azure Cosmos DB forneça um desempenho previsível e consistente, a garantia de baixa latência e alta disponibilidade em qualquer escala. Unidades de solicitação representam a moeda normalizada que simplifica o raciocínio sobre quantos recursos um aplicativo precisa.

A carga de solicitação retornada no cabeçalho da solicitação indica o custo de uma determinada consulta. Por exemplo, se uma consulta retorna 1.000 itens de 1 KB, o custo da operação é 1.000. Assim, em um segundo, o servidor mantém apenas duas dessas solicitações antes de limitar as solicitações subsequentes. Para saber mais, consulte o artigo [Unidades de solicitação](request-units.md) e a calculadora de unidades de solicitação.

## <a name="writing-data"></a>Gravação de dados

O custo de RU de escrever um item depende de:

- O tamanho do item.
- O número de propriedades cobertas pela [política de indexação](index-policy.md) e que precisaram ser indexadas.

Inserindo um item de 1 KB sem indexação de custos aproximadamente 5,5 RUs. Substituir um item custa duas vezes o encargo necessário para inserir o mesmo item.

### <a name="optimizing-writes"></a>Otimizando gravações

A melhor maneira de otimizar o custo de RU das operações de gravação é asrightar os itens e o número de propriedades que são indexadas.

- Armazenar itens muito grandes em Azure Cosmos DB resulta em encargos de RU altos e pode ser considerado um antipadrão. Em particular, não armazene conteúdo binário ou grandes partes de texto que você não precisa consultar. Uma prática recomendada é colocar esse tipo de dados no [armazenamento de BLOBs do Azure](../storage/blobs/storage-blobs-introduction.md) e armazenar uma referência (ou link) no blob no item que você escreve para Azure Cosmos DB.
- Otimizar a política de indexação para indexar apenas as propriedades em que seu filtro de consultas pode fazer uma grande diferença no RUs consumido por suas operações de gravação. Ao criar um novo contêiner, a política de indexação padrão indexa cada e todas as propriedades encontradas em seus itens. Embora esse seja um bom padrão para atividades de desenvolvimento, é altamente recomendável reavaliar e [personalizar sua política de indexação](how-to-manage-indexing-policy.md) ao passar para a produção ou quando a carga de trabalho começar a receber tráfego significativo.

Ao executar a ingestão em massa de dados, também é recomendável usar o [Azure Cosmos DB biblioteca de executores em massa](bulk-executor-overview.md) , pois ele foi projetado para otimizar o consumo de ru dessas operações. Opcionalmente, você também pode usar [Azure data Factory](../data-factory/introduction.md) que é criado na mesma biblioteca.

## <a name="next-steps"></a>Próximas etapas

A seguir, você poderá saber mais sobre a otimização de custos no Azure Cosmos DB nos seguintes artigos:

* Saiba mais sobre [Otimizando para desenvolvimento e teste](optimize-dev-test.md)
* Saiba mais sobre [Entender sua cobrança do Azure Cosmos DB](understand-your-bill.md)
* Saiba mais sobre [Otimizando o custo da taxa de transferência](optimize-cost-throughput.md)
* Saiba mais sobre [Otimizando o custo de armazenamento](optimize-cost-storage.md)
* Saiba mais sobre [Otimizando o custo de contas do Azure Cosmos em várias regiões](optimize-cost-regions.md)
* Saiba mais sobre [Azure Cosmos DB capacidade reservada](cosmos-db-reserved-capacity.md)
