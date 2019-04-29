---
title: Níveis de coerência no Azure Cosmos DB
description: O Azure Cosmos DB tem cinco níveis de consistência que ajudam a equilibrar prós e contras de consistência eventual, disponibilidade e latência.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: 836d36cc6f220bb544e0c7723506c624c5f9fc39
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896510"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de coerência no Azure Cosmos DB

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos comercialmente disponíveis perguntar aos desenvolvedores escolher entre os dois modelos de consistência extreme: *forte* consistência e *eventual* consistência. O  [transação atômica](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) ou o modelo de coerência forte é o padrão ouro de programação de dados. Mas ele adiciona um preço maior latência (no estado estável) e redução de disponibilidade (durante falhas). Por outro lado, a consistência eventual oferece maior disponibilidade e melhor desempenho, mas torna difícil a programação de aplicativos. 

O Azure Cosmos DB aborda a coerência de dados como um espectro de opções, em vez dos dois extremos. Consistência forte e consistência eventual são nas extremidades do espectro, mas há muitas opções de consistência, juntamente com o espectro. Os desenvolvedores podem usar essas opções para fazer escolhas precisas e granulares vantagens e desvantagens em relação à alta disponibilidade e desempenho. 

Com o Azure Cosmos DB, os desenvolvedores pode escolher a partir de cinco modelos de consistência bem definidos no espectro de consistência. Do mais forte ao mais relaxadas, os modelos incluem *forte*, *desatualização*, *sessão*, *prefixo consistente*e *eventual* consistência. Os modelos são bem definidas e intuitiva e podem ser usados para cenários específicos do mundo real. Cada modelo fornece [compensações de desempenho e disponibilidade](consistency-levels-tradeoffs.md) e com o respaldo de SLAs. A imagem a seguir mostra os níveis de consistência diferentes como um espectro.

![Consistência como um espectro](./media/consistency-levels/five-consistency-levels.png)

Os níveis de consistência são independentes de região e são garantidos para todas as operações, independentemente da região da qual as leituras e gravações são atendidas, o número de regiões associadas à sua conta do Cosmos do Azure, ou se sua conta está configurada com um único ou em várias regiões de gravação.

## <a name="scope-of-the-read-consistency"></a>Escopo da coerência de leitura

A consistência de leitura se aplica a uma única operação de leitura no escopo dentro de um intervalo de chave de partição ou uma partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência padrão

Você pode configurar o nível de coerência padrão em sua conta do Azure Cosmos a qualquer momento. O nível de consistência padrão configurado em sua conta se aplica a todos os bancos de dados Cosmos do Azure e contêineres na conta. Todas as leituras e consultas emitidas em um contêiner ou banco de dados usam o nível de consistência especificado por padrão. Para obter mais informações, consulte [configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas a níveis de coerência

Os SLAs abrangentes fornecidos pelo Azure Cosmos DB garantem que 100% de solicitações de leitura atendam a garantia de consistência para qualquer nível de consistência que você escolher. Uma solicitação de leitura atende ao SLA de consistência, se todas as garantias de consistência associadas ao nível de consistência forem atendidas. As definições de precisas de as cinco níveis de consistência no Azure Cosmos DB usando o [linguagem de especificação de TLA +](https://lamport.azurewebsites.net/tla/tla.html) são fornecidos no [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) repositório do GitHub. 

A semântica dos cinco níveis de coerência é descrita aqui:

- **Strong**: Coerência forte oferece uma garantia de [linearizabilidade](https://aphyr.com/posts/313-strong-consistency-models). As leituras são garantidas para retornar a versão mais recente de um item. Um cliente nunca vê uma gravação não comprometida ou parcial. Os usuários sempre terão a garantia de ler a última gravação confirmada.

- **Bounded staleness**: As leituras têm a garantia de honrar a garantia de prefixo consistente. As leituras podem não acompanhar as gravações no máximo *"K"* versões (ou seja, "atualizações") de um item ou pelo *"T"* intervalo de tempo. Em outras palavras, quando você escolher desatualização limitada, "staleness" pode ser configurado de duas maneiras: 

  * O número de versões (*K*) do item
  * O intervalo de tempo (*T*) pelo qual as leituras podem não acompanhar as gravações 

  A desatualização limitada oferece ordem global total, exceto na "janela de desatualização". Há garantias de leitura monotônica em uma região tanto dentro quanto fora da janela de desatualização. Coerência forte tem a mesma semântica que o oferecido por desatualização limitada. A janela de desatualização limitada é igual a zero. A desatualização limitada também é referida como linearização retardada no tempo. Quando um cliente executa operações de leitura em uma região que aceite gravações, as garantias fornecidas pelo consistência de desatualização são idênticas a essas garantias pela coerência forte.

- **Session**: As leituras têm a garantia de honrar o prefixo consistente (assumindo uma seção de “gravador” única), leituras monotônicas, gravações monótonas, leituras de suas gravações, garantias de gravação de seguidas leituras. A coerência de sessão engloba uma sessão de cliente.

- **Prefixo Coerente**: Atualizações que são retornadas contêm algum prefixo de todas as atualizações, sem intervalos. Nível de consistência de prefixo consistente garante que as leituras nunca vejam gravações fora de ordem.

- **Eventual**: Não há nenhuma garantia de ordenação para leituras. Na ausência de qualquer gravação adicional, as réplicas eventualmente convergem.

## <a name="consistency-levels-explained-through-baseball"></a>Níveis de consistência explicados através do beisebol

Vejamos um cenário de jogo de beisebol como exemplo. Imagine uma sequência de gravações que representam a pontuação de um jogo de beisebol. A pontuação da linha entrada-por-entrada é descrita no papel de [Consistência de dados replicados através do beisebol](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf). Este jogo de beisebol hipotético está atualmente no meio da sétima entrada. É a transferência de sétima entrada. Os visitantes estão por trás com uma pontuação de 2 a 5 conforme mostrado abaixo:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Execuções** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Visitantes** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Página Inicial** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Um contêiner do Azure Cosmos mantém os totais de execução para os visitantes e equipes iniciais. Enquanto o jogo estiver em andamento, diferentes garantias de leitura podem resultar em clientes lerem diferentes pontuações. A tabela a seguir lista o conjunto completo de pontuações que podem ser retornadas ao ler as pontuações do visitante e da casa com cada uma das cinco garantias de coerência. A pontuação dos visitantes é listada primeiro. Diferentes valores retornados possíveis são separados por vírgulas.

| **Nível de coerência** | **Pontuações (visitantes, Home)** |
| - | - |
| **Forte** | 2 a 5 |
| **Desatualização Limitada** | As pontuações são no máximo uma entrada de data: 2-3, 2-4, 2-5 |
| **Sessão** | <ul><li>Para o gravador: 2 a 5</li><li> Para qualquer outra pessoa que não seja o gravador: 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>Depois de ler 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Prefixo coerente** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Eventual** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Leitura adicional

Para saber mais sobre conceitos de coerência, leia os artigos a seguir:

- [Especificações de TLA+ de alto nível para os cinco níveis de coerência oferecidos pelo Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Explicação de Coerência de Dados Replicados por meio do beisebol (vídeo) por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Explicação de Coerência de Dados Replicados por meio do beisebol (whitepaper) por Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Session Guarantees for Weakly Consistent Replicated Data](https://dl.acm.org/citation.cfm?id=383631)
- [Consistency tradeoffs in modern distributed database systems design: (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos): CAP é apenas parte da história](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
- [Eventualmente Coerente – Revisado](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os níveis de coerência no Azure Cosmos DB, leia os artigos a seguir:

* [Escolher o nível certo de coerência para o seu aplicativo](consistency-levels-choosing.md)
* [Níveis de coerência entre as APIs do Azure Cosmos DB](consistency-levels-across-apis.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Substituir o nível de consistência padrão](how-to-manage-consistency.md#override-the-default-consistency-level)

