---
title: Níveis de coerência no Azure Cosmos DB
description: O Azure Cosmos DB tem cinco níveis de consistência que ajudam a equilibrar prós e contras de consistência eventual, disponibilidade e latência.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: a480c8f2dfdda0ce7a1eb879554fb79c96adbe1e
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347805"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de coerência no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Os bancos de dados distribuídos que dependem da replicação para alta disponibilidade, baixa latência ou ambos, devem fazer uma compensação fundamental entre a consistência de leitura, a disponibilidade, a latência e a taxa de transferência, conforme definido pelo [PACLC teorema](https://en.wikipedia.org/wiki/PACELC_theorem). O transação atômica do modelo de consistência forte é o padrão ouro de programação de dados. Mas ele adiciona um preço alto de latências de gravação maiores devido a dados que têm de ser replicados e confirmados em grandes distâncias. A consistência forte também pode ser afetada da disponibilidade reduzida (durante as falhas) porque os dados não podem ser replicados e confirmados em todas as regiões. A consistência eventual oferece maior disponibilidade e melhor desempenho, mas é mais difícil programar aplicativos, pois os dados podem não ser totalmente consistentes em todas as regiões.

A maioria dos bancos de dados NoSQL distribuídos comercialmente disponíveis no mercado atualmente fornece uma consistência forte e eventual. O Azure Cosmos DB oferece cinco níveis bem definidos. Do mais forte ao mais fraco, os níveis são:

- *Forte*
- *Desatualização limitada*
- *Sessão*
- *Prefixo consistente*
- *Iminente*

Cada nível fornece compensações de desempenho e disponibilidade. A imagem a seguir mostra os diferentes níveis de consistência como um espectro.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Consistência como um espectro" border="false" :::

Os níveis de consistência são independentes de região e são garantidos para todas as operações, independentemente da região da qual as leituras e gravações são atendidas, o número de regiões associadas à sua conta do Azure Cosmos ou se sua conta está configurada com uma ou várias regiões de gravação.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

O Azure Cosmos DB fornece suporte nativo para APIs compatíveis com protocolo de conexão para bancos de dados populares. Isso inclui o MongoDB, o Apache Cassandra, o Gremlin e o armazenamento de tabelas do Azure. Ao usar a API Gremlin e API de Tabela, o nível de consistência padrão configurado na conta do Azure cosmos é usado. Para obter detalhes sobre o mapeamento de nível de consistência entre API do Cassandra ou a API para o MongoDB e os níveis de consistência do Azure Cosmos DB, consulte [API do Cassandra mapeamento de consistência](cassandra-consistency.md) e [API para mapeamento de consistência do MongoDB](mongodb-consistency.md).

## <a name="scope-of-the-read-consistency"></a>Escopo da coerência de leitura

A consistência de leitura se aplica a uma única operação de leitura com escopo definido em uma partição lógica. A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configure-the-default-consistency-level"></a>Configurar o nível de consistência padrão

Você pode configurar o nível de coerência padrão em sua conta do Azure Cosmos a qualquer momento. O nível de consistência padrão configurado em sua conta se aplica a todos os bancos de dados e contêineres do Azure Cosmos sob essa conta. Todas as leituras e consultas emitidas em um contêiner ou banco de dados usam o nível de consistência especificado por padrão. Para obter mais informações, consulte [configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level). Você também pode substituir o nível de consistência padrão para uma solicitação específica, para saber mais, consulte como [substituir o artigo de nível de consistência padrão](how-to-manage-consistency.md?#override-the-default-consistency-level) .

> [!IMPORTANT]
> É necessário recriar qualquer instância do SDK depois de alterar o nível de consistência padrão. Isso pode ser feito reiniciando o aplicativo. Isso garante que o SDK use o novo nível de consistência padrão.

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas a níveis de coerência

Azure Cosmos DB garante que 100 por cento das solicitações de leitura atendam à garantia de consistência para o nível de consistência escolhido. As definições precisas dos cinco níveis de consistência no Azure Cosmos DB usando a linguagem de especificação TLA + são fornecidas no repositório GitHub [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) .

A semântica dos cinco níveis de coerência é descrita aqui:

- **Forte**: a consistência forte oferece uma garantia transação atômica. Transação atômica refere-se ao fornecimento de solicitações simultaneamente. As leituras são garantidas para retornar a versão mais recente de um item. Um cliente nunca vê uma gravação não comprometida ou parcial. Os usuários sempre terão a garantia de ler a última gravação confirmada.

  O gráfico a seguir ilustra a forte consistência com notas musicais. Depois que os dados são gravados na região "oeste dos EUA 2", ao ler os dados de outras regiões, você obtém o valor mais recente:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Ilustração do nível de consistência forte":::

- Desatualização **limitada**: as leituras têm a garantia de honrar a garantia de prefixo consistente. As leituras podem atrasar por trás das gravações por no máximo *"K"* versões (ou seja, "Atualizações") de um item ou por um intervalo de tempo *"T"* , o que for atingido primeiro. Em outras palavras, quando você escolhe a desatualização limitada, a "desatualização" pode ser configurada de duas maneiras:

- O número de versões (*K*) do item
- As leituras de intervalo de tempo (*T*) podem atrasar por trás das gravações

Para uma única conta de região, o valor mínimo de *K* e *T* é de 10 operações de gravação ou 5 segundos. Para contas de várias regiões, o valor mínimo de *K* e *T* é de 100.000 operações de gravação ou 300 segundos.

A desatualização limitada oferece a ordem global total fora da "janela de desatualização". Quando um cliente executa operações de leitura em uma região que aceita gravações, as garantias fornecidas pela consistência de desatualização limitada são idênticas às garantias pela consistência forte. À medida que a janela de desatualização se aproxima da hora ou das atualizações, o que for mais próximo, o serviço limitará novas gravações para permitir que a replicação acompanhe e obedeça à garantia de consistência.

Dentro da janela de desatualização, a desatualização limitada fornece as seguintes garantias de consistência:

- Consistência para clientes na mesma região para uma conta com uma única região de gravação = forte
- Consistência para clientes em regiões diferentes para uma conta com uma única região de gravação = prefixo consistente
- Consistência para clientes que gravam em uma única região para uma conta com várias regiões de gravação = prefixo consistente
- Consistência para clientes que gravam em regiões diferentes para uma conta com várias regiões de gravação = eventual

  A desatualização limitada é frequentemente escolhida por aplicativos distribuídos globalmente que esperam latências de baixa gravação, mas exigem garantia de ordem global total. A desatualização limitada é excelente para aplicativos que apresentam colaboração e compartilhamento de grupos, cotação de ações, publicação/assinatura/enfileiramento, etc. O gráfico a seguir ilustra a consistência de desatualização limitada com notas musicais. Depois que os dados são gravados na região "oeste dos EUA 2", as regiões "leste dos EUA 2" e "leste da Austrália" lêem o valor escrito com base no tempo de retardo máximo configurado ou no máximo de operações:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Ilustração do nível de consistência de desatualização limitada":::

- **Sessão**: em uma única sessão de cliente, as leituras têm a garantia de honrar as leituras de prefixo consistente, de monotônico, gravações de monotônico, leitura e gravação, e as garantias de Write-seguir-leituras. Isso pressupõe uma única sessão de "gravador" ou o compartilhamento do token de sessão para vários gravadores.

Os clientes fora da sessão que executam gravações verão as seguintes garantias:

- Consistência para clientes na mesma região para uma conta com uma única região de gravação = prefixo consistente
- Consistência para clientes em regiões diferentes para uma conta com uma única região de gravação = prefixo consistente
- Consistência para clientes que gravam em uma única região para uma conta com várias regiões de gravação = prefixo consistente
- Consistência para clientes que gravam em várias regiões para uma conta com várias regiões de gravação = eventual

  A consistência da sessão é o nível de consistência mais amplamente usado para a região única, bem como para aplicativos distribuídos globalmente. Ele fornece latências de gravação, disponibilidade e taxa de transferência de leitura comparável à de consistência eventual, mas também fornece as garantias de consistência que atendem às necessidades dos aplicativos escritos para operar no contexto de um usuário. O gráfico a seguir ilustra a consistência da sessão com notas musicais. O "gravador oeste dos EUA 2" e o "leitor oeste dos EUA 2" estão usando a mesma sessão (sessão A) para que ambos leiam os mesmos dados ao mesmo tempo. Enquanto a região "leste da Austrália" está usando a "sessão B", ela recebe dados mais tarde, mas na mesma ordem que as gravações.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Ilustração do nível de consistência da sessão":::

- **Prefixo consistente**: as atualizações que são retornadas contêm algum prefixo de todas as atualizações, sem intervalos. O nível de consistência de prefixo consistente garante que as leituras nunca vejam gravações fora de ordem.

Se as gravações foram executadas na ordem `A, B, C` , um cliente vê `A` , `A,B` ou `A,B,C` , mas nunca permutações fora de ordem, como `A,C` ou `B,A,C` . O prefixo consistente fornece latências de gravação, disponibilidade e taxa de transferência de leitura comparáveis à de consistência eventual, mas também fornece as garantias de ordem que atendem às necessidades dos cenários em que a ordem é importante.

Abaixo estão as garantias de consistência para o prefixo consistente:

- Consistência para clientes na mesma região para uma conta com uma única região de gravação = prefixo consistente
- Consistência para clientes em regiões diferentes para uma conta com uma única região de gravação = prefixo consistente
- Consistência para clientes que gravam em uma única região para uma conta com várias regiões de gravação = prefixo consistente
- Consistência para clientes que gravam em várias regiões para uma conta com várias regiões de gravação = eventual

O gráfico a seguir ilustra a consistência do prefixo de consistência com notas musicais. Em todas as regiões, as leituras nunca veem gravações fora de ordem:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Ilustração de um prefixo consistente":::

- **Eventual**: não há garantia de classificação para leituras. Na ausência de qualquer gravação adicional, as réplicas eventualmente convergem.  
A consistência eventual é a forma mais fraca de consistência, pois um cliente pode ler os valores mais antigos do que aqueles que tinha lido anteriormente. A consistência eventual é ideal quando o aplicativo não requer nenhuma garantia de ordenação. Os exemplos incluem a contagem de retweets, curtidos ou comentários não-threaded. O gráfico a seguir ilustra a consistência eventual com notas musicais.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="viIllustration de consistência eventual":::

## <a name="consistency-guarantees-in-practice"></a>Garantias de consistência na prática

Na prática, muitas vezes você pode obter garantias de consistência mais fortes. Garantias de consistência para uma operação de leitura correspondem à atualização e ordenação do estado do banco de dados que você solicita. Consistência de leitura é vinculada ao pedido e a propagação das operações de gravação/atualização.  

Se não houver nenhuma operação de gravação no banco de dados, uma operação de leitura com níveis de consistência **eventual**, de **sessão** ou de **prefixo consistente** provavelmente produzirá os mesmos resultados que uma operação de leitura com um nível de consistência forte.

Se sua conta do Azure Cosmos estiver configurada com um nível de consistência diferente da consistência forte, você poderá descobrir a probabilidade de que seus clientes possam obter leituras fortes e consistentes para suas cargas de trabalho examinando a métrica do PBS (desatualização limitada) do *Probabilistic* . Essa métrica é exposta no portal do Azure, para obter mais informações, consulte [métrica Monitor Probabilistic Bounded Staleness (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

A desatualização limitada probabilística mostra o quão eventual é a sua coerência eventual. Essa métrica fornece uma percepção da frequência com que você pode obter uma consistência mais forte do que o nível de consistência configurado atualmente em sua conta do Azure Cosmos. Em outras palavras, você pode ver a probabilidade (medida em milissegundos) de obter leituras fortemente consistentes para uma combinação de regiões de gravação e leitura.

## <a name="consistency-levels-and-latency"></a>Níveis de coerência e latência

A latência de leitura para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência média de leitura, no 50 º percentil, normalmente é de 4 milissegundos ou menos.

A latência de gravação para todos os níveis de consistência sempre é garantida para menos de 10 milissegundos no 99 º percentil. A latência média de gravação, 50 º percentil, geralmente é 5 milissegundos ou menos. Contas do Cosmos do Azure que abrangem várias regiões e são configuradas com coerência forte são uma exceção a essa garantia.

### <a name="write-latency-and-strong-consistency"></a>Latência de gravação e consistência forte

Para contas do Azure Cosmos configuradas com consistência forte com mais de uma região, a latência de gravação é igual a duas vezes a RTT (tempo de ida e volta) entre qualquer uma das duas regiões mais distantes, mais 10 milissegundos no 99 º percentil. O RTT de rede alto entre as regiões será convertido em latência mais alta para solicitações de Cosmos DB, uma vez que a consistência forte conclui uma operação somente depois de garantir que ela tenha sido confirmada em todas as regiões de uma conta.

A latência RTT é uma função de distância à velocidade da luz e a topologia de rede exata do Azure. A rede do Azure não fornece SLAs de latência para o RTT entre duas regiões do Azure, no entanto, publica [Estatísticas de latência de ida e volta da rede do Azure](../networking/azure-network-latency.md). Para sua conta do Azure Cosmos, latências de replicação são exibidas no portal do Azure. Você pode usar o portal do Azure (vá para a folha métricas, selecione a guia consistência) para monitorar as latências de replicação entre várias regiões associadas à sua conta do Azure Cosmos.

> [!IMPORTANT]
> A consistência forte para contas com regiões que abrangem mais de 5000 milhas (8000 quilômetros) é bloqueada por padrão devido à alta latência de gravação. Para habilitar esse recurso, entre em contato com o suporte.

## <a name="consistency-levels-and-throughput"></a>Níveis de coerência e taxa de transferência

- Para uma desatualização forte e limitada, as leituras são feitas em duas réplicas em um conjunto de quatro réplicas (quorum minoritário) para fornecer garantias de consistência. A sessão, o prefixo consistente e a eventualidade fazem leituras de réplica única. O resultado é que, para o mesmo número de unidades de solicitação, a taxa de transferência de leitura para forte e a desatualização limitada é a metade dos outros níveis de consistência.

- Para um determinado tipo de operação de gravação, como inserir, substituir, submeter, excluir, a taxa de transferência de gravação para unidades de solicitação é idêntica para todos os níveis de consistência.

|**Nível de coerência**|**Leituras de quorum**|**Gravações de quorum**|
|--|--|--|
|**Forte**|Minoria local|Maioria global|
|**Desatualização limitada**|Minoria local|Maioria local|
|**Sessão**|Réplica única (usando o token de sessão)|Maioria local|
|**Prefixo consistente**|Réplica única|Maioria local|
|**Iminente**|Réplica única|Maioria local|

> [!NOTE]
> O custo da RU/s de leituras de leituras minoritárias locais é duas vezes maior que os níveis de consistência mais fracos, pois as leituras são feitas de duas réplicas para fornecer garantias de consistência para a forte e a desatualização limitada.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Níveis de consistência e durabilidade dos dados

Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre a durabilidade dos dados e o nível de consistência no caso de uma interrupção em toda a região. À medida que você vai desenvolvendo o plano de continuidade dos negócios, precisará saber qual é o tempo máximo aceitável antes que o aplicativo se recupere completamente após um evento de interrupção. O tempo necessário para que um aplicativo se recupere totalmente é conhecido como **RTO**( **objetivo de tempo de recuperação** ). Também é necessário saber o período máximo de atualizações de dados recentes que o aplicativo pode perder sem maiores problemas durante a recuperação após um evento de interrupção. O período de tempo das atualizações que você pode perder é conhecido como **RPO**( **objetivo de ponto de recuperação** ).

A tabela a seguir define a relação entre o modelo de consistência e a durabilidade dos dados na presença de uma interrupção em toda a região. É importante observar que, em um sistema distribuído, mesmo com uma consistência forte, é impossível ter um banco de dados distribuído com RPO e RTO de zero devido a [teoremas de ponta](https://en.wikipedia.org/wiki/CAP_theorem).

|**Região (ões)**|**Modo de replicação**|**Nível de consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Uma ou várias regiões de gravação|Qualquer nível de consistência|< 240 minutos|< 1 semana|
|> 1|Região de gravação única|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|> 1|Região de gravação única|Desatualização limitada|*K*  &  *T*|< 15 minutos|
|> 1|Região de gravação única|Forte|0|< 15 minutos|
|> 1|Várias regiões de gravação|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|> 1|Várias regiões de gravação|Desatualização limitada|*K*  &  *T*|0|

*K* = o número de versões *"K"* (ou seja, atualizações) de um item.

*T* = o intervalo de tempo *"T"* desde a última atualização.

Para uma única conta de região, o valor mínimo de *K* e *T* é de 10 operações de gravação ou 5 segundos. Para contas de várias regiões, o valor mínimo de *K* e *T* é de 100.000 operações de gravação ou 300 segundos. Isso define o RPO mínimo de dados ao usar a desatualização limitada.

## <a name="strong-consistency-and-multiple-write-regions"></a>Consistência forte e várias regiões de gravação

As contas do cosmos configuradas com várias regiões de gravação não podem ser configuradas para uma consistência forte, pois não é possível que um sistema distribuído forneça um RPO de zero e um RTO igual a zero. Além disso, não há benefícios de latência de gravação sobre o uso de consistência forte com várias regiões de gravação, pois uma gravação em qualquer região deve ser replicada e confirmada em todas as regiões configuradas na conta. Isso resulta na mesma latência de gravação que uma única conta de região de gravação.

## <a name="additional-reading"></a>Leituras adicionais

Para saber mais sobre conceitos de coerência, leia os artigos a seguir:

- [Especificações de TLA+ de alto nível para os cinco níveis de coerência oferecidos pelo Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Consistência de dados replicada explicada por meio de beisebol (vídeo) por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Consistência de dados replicada explicada por meio de beisebol (White Paper) por Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Session Guarantees for Weakly Consistent Replicated Data](https://dl.acm.org/citation.cfm?id=383631)
- [Equilíbrio de coerência no design do banco de dados distribuído moderno: CAP é só uma parte da história](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
- [Eventualmente Coerente – Revisado](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os níveis de coerência no Azure Cosmos DB, leia os artigos a seguir:

- [Configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Substituir o nível de consistência padrão](how-to-manage-consistency.md#override-the-default-consistency-level)
- [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
