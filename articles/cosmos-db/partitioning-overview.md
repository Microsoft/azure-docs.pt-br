---
title: Particionamento e escala horizontal no Azure Cosmos DB
description: Saiba mais sobre particionamento, partições lógicas e físicas em Azure Cosmos DB, práticas recomendadas ao escolher uma chave de partição e como gerenciar partições lógicas
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: b91c846b5a79125c1cee9c36ce81b5c3d3229ba9
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627758"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Particionamento e escala horizontal no Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB usa o particionamento para dimensionar contêineres individuais em um banco de dados para atender às necessidades de desempenho do seu aplicativo. No particionamento, os itens em um contêiner são divididos em subconjuntos distintos chamados *partições lógicas*. As partições lógicas são formadas com base no valor de uma *chave de partição* associada a cada item em um contêiner. Todos os itens em uma partição lógica têm o mesmo valor de chave de partição.

Por exemplo, um contêiner contém itens. Cada item tem um valor exclusivo para a `UserID` propriedade. Se `UserID` serve como a chave de partição para os itens no contêiner e há 1.000 valores exclusivos `UserID` , 1.000 partições lógicas são criadas para o contêiner.

Além de uma chave de partição que determina a partição lógica do item, cada item em um contêiner tem uma *ID de item* (exclusiva em uma partição lógica). A combinação da chave de partição e da *ID do item* cria o *índice* do item, que identifica exclusivamente o item. [Escolher uma chave de partição](#choose-partitionkey) é uma decisão importante que afetará o desempenho do aplicativo.

Este artigo explica a relação entre partições lógicas e físicas. Ele também aborda as práticas recomendadas para particionamento e fornece uma visão detalhada de como funciona o dimensionamento horizontal em Azure Cosmos DB. Não é necessário entender esses detalhes internos para selecionar sua chave de partição, mas nós as cobrimos para que você tenha uma clareza de como Azure Cosmos DB funciona.

## <a name="logical-partitions"></a>Partições lógicas

Uma partição lógica consiste em um conjunto de itens que têm a mesma chave de partição. Por exemplo, em um contêiner que contém dados sobre alimentos nutrição, todos os itens contêm uma `foodGroup` propriedade. Você pode usar `foodGroup` como a chave de partição para o contêiner. Grupos de itens que têm valores específicos para `foodGroup` , como, `Beef Products` `Baked Products` e `Sausages and Luncheon Meats` , formam partições lógicas distintas. Você não precisa se preocupar em excluir uma partição lógica quando os dados subjacentes são excluídos.

Uma partição lógica também define o escopo das transações de banco de dados. Você pode atualizar itens em uma partição lógica usando uma [transação com isolamento de instantâneo](database-transactions-optimistic-concurrency.md). Quando novos itens são adicionados a um contêiner, novas partições lógicas são criadas de forma transparente pelo sistema.

Não há nenhum limite para o número de partições lógicas em seu contêiner. Cada partição lógica pode armazenar até 20 GB de dados. Boas opções de chave de partição têm uma ampla variedade de valores possíveis. Por exemplo, em um contêiner em que todos os itens contêm uma `foodGroup` propriedade, os dados dentro da `Beef Products` partição lógica podem crescer até 20 GB. A [seleção de uma chave de partição](#choose-partitionkey) com uma ampla variedade de valores possíveis garante que o contêiner seja capaz de dimensionar.

## <a name="physical-partitions"></a>Partições físicas

Um contêiner é dimensionado distribuindo dados e taxa de transferência entre partições físicas. Internamente, uma ou mais partições lógicas são mapeadas para uma única partição física. Normalmente, contêineres menores têm muitas partições lógicas, mas exigem apenas uma única partição física. Ao contrário das partições lógicas, as partições físicas são uma implementação interna do sistema e são totalmente gerenciadas pelo Azure Cosmos DB.

O número de partições físicas no contêiner depende do seguinte:

* O número de taxa de transferência provisionado (cada partição física individual pode fornecer uma taxa de transferência de até 10.000 unidades de solicitação por segundo).
* O armazenamento de dados total (cada partição física individual pode armazenar até 50 GB de dados).

> [!NOTE]
> As partições físicas são uma implementação interna do sistema e são totalmente gerenciadas pelo Azure Cosmos DB. Ao desenvolver suas soluções, não se concentre em partições físicas porque não é possível controlá-las. Em vez disso, concentre-se nas chaves de partição. Se você escolher uma chave de partição que distribua uniformemente o consumo de taxa de transferência entre partições lógicas, você garantirá que o consumo de taxa de transferência entre partições físicas seja balanceado.

Não há nenhum limite para o número total de partições físicas em seu contêiner. À medida que a produtividade provisionada ou o tamanho dos dados crescer, Azure Cosmos DB criará automaticamente novas partições físicas, dividindo as existentes. As divisões de partição física não afetam a disponibilidade do aplicativo. Após a divisão da partição física, todos os dados em uma única partição lógica ainda serão armazenados na mesma partição física. Uma divisão de partição física simplesmente cria um novo mapeamento de partições lógicas para partições físicas.

A taxa de transferência provisionada para um contêiner é dividida uniformemente entre as partições físicas. Um design de chave de partição que não distribui solicitações uniformemente pode resultar em muitas solicitações direcionadas para um pequeno subconjunto de partições que se tornam "quentes". As partições quentes levam ao uso ineficiente de produtividade provisionada, o que pode resultar em limitação de taxa e custos mais altos.

Você pode ver as partições físicas do contêiner na seção **armazenamento** da **folha métricas** do portal do Azure:

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="Exibindo o número de partições físicas" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

Na captura de tela acima, um contêiner tem `/foodGroup` como a chave de partição. Cada uma das três barras no grafo representa uma partição física. Na imagem, o **intervalo de chaves de partição** é o mesmo que uma partição física. A partição física selecionada contém as três principais partições lógicas de tamanho mais significativas: `Beef Products` , `Vegetable and Vegetable Products` e `Soups, Sauces, and Gravies` .

Se você provisionar uma taxa de transferência de 18.000 unidades de solicitação por segundo (RU/s), cada uma das três partições físicas poderá utilizar 1/3 da taxa de transferência total provisionada. Na partição física selecionada, as chaves de partição lógica `Beef Products` , `Vegetable and Vegetable Products` e `Soups, Sauces, and Gravies` podem, coletivamente, utilizar as 6.000 de ru/s provisionadas da partição física. Como a taxa de transferência provisionada é dividida uniformemente nas partições físicas do contêiner, é importante escolher uma chave de partição que distribua uniformemente o consumo da taxa de transferência, [escolhendo a chave de partição lógica correta](#choose-partitionkey). 

## <a name="managing-logical-partitions"></a>Gerenciando partições lógicas

Azure Cosmos DB gerencia de forma transparente e automática o posicionamento de partições lógicas em partições físicas para atender com eficiência às necessidades de escalabilidade e desempenho do contêiner. À medida que os requisitos de taxa de transferência e armazenamento de um aplicativo aumentam, Azure Cosmos DB move partições lógicas para espalhar automaticamente a carga em um número maior de partições físicas. Você pode saber mais sobre [partições físicas](partitioning-overview.md#physical-partitions).

O Azure Cosmos DB usa o particionamento baseado em hash para distribuir partições lógicas entre partições físicas. Azure Cosmos DB hash do valor de chave de partição de um item. O resultado com hash determina a partição física. Em seguida, Azure Cosmos DB aloca o espaço de chave dos hashes de chave de partição uniformemente entre as partições físicas.

As transações (em procedimentos armazenados ou gatilhos) são permitidas somente em itens em uma única partição lógica.

Você pode saber mais sobre [como Azure Cosmos DB gerencia partições](partitioning-overview.md). (Não é necessário entender os detalhes internos para criar ou executar seus aplicativos, mas adicionados aqui para um leitor curioso.)

## <a name="replica-sets"></a>Conjuntos de réplicas

Cada partição física consiste em um conjunto de réplicas, também conhecido como um [*conjunto de réplicas*](global-dist-under-the-hood.md). Cada conjunto de réplicas hospeda uma instância do mecanismo de banco de dados. Um conjunto de réplicas torna os dados armazenados na partição física durável, altamente disponível e consistente. Cada réplica que compõe a partição física herda a cota de armazenamento da partição. Todas as réplicas de uma partição física dão suporte coletiva à taxa de transferência alocada para a partição física. Azure Cosmos DB gerencia automaticamente os conjuntos de réplicas.

Normalmente, contêineres menores exigem apenas uma única partição física, mas eles ainda terão pelo menos 4 réplicas.

A imagem a seguir mostra como as partições lógicas são mapeadas para partições físicas distribuídas globalmente:

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="Uma imagem que demonstra Azure Cosmos DB particionamento" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>Escolhendo uma chave de partição

Uma chave de partição tem dois componentes: **caminho de chave de partição** e valor de chave de **partição**. Por exemplo, considere um item {"userId": "Andrew", "worksFor": "Microsoft"} se você escolher "userId" como a chave de partição, estes são os dois componentes de chave de partição:

* O caminho da chave de partição (por exemplo: "/userid"). O caminho da chave de partição aceita caracteres alfanuméricos e sublinhados (_). Você também pode usar objetos aninhados usando a notação de caminho padrão (/).

* O valor da chave de partição (por exemplo: "Andrew"). O valor da chave de partição pode ser de tipos de cadeia de caracteres ou numéricos.

Para saber mais sobre os limites de taxa de transferência, armazenamento e comprimento da chave de partição, consulte o artigo [Azure Cosmos DB cotas de serviço](concepts-limits.md) .

A seleção de sua chave de partição é uma opção de design simples, mas importante no Azure Cosmos DB. Depois de selecionar a chave de partição, não é possível alterá-la in-loco. Se você precisar alterar sua chave de partição, mova seus dados para um novo contêiner com a nova chave de partição desejada.

Para **todos os** contêineres, sua chave de partição deve:

* Ser uma propriedade que tem um valor que não é alterado. Se uma propriedade for sua chave de partição, você não poderá atualizar o valor dessa propriedade.

* Tenha uma alta cardinalidade. Em outras palavras, a propriedade deve ter uma ampla variedade de valores possíveis.

* Armazenamento de dados e consumo de RU (unidade de solicitação de espalhamento) uniformemente em todas as partições lógicas. Isso garante o consumo até a RU e a distribuição de armazenamento em suas partições físicas.

Se você precisar de [Transações ACID com vários itens](database-transactions-optimistic-concurrency.md#multi-item-transactions) no Azure Cosmos DB, será necessário usar [procedimentos armazenados ou gatilhos](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures). Todos os procedimentos armazenados e gatilhos baseados em JavaScript têm como escopo uma única partição lógica.

## <a name="partition-keys-for-read-heavy-containers"></a>Chaves de partição para contêineres de leitura intensa

Para a maioria dos contêineres, os critérios acima são tudo o que você precisa considerar ao escolher uma chave de partição. No entanto, para grandes contêineres pesados de leitura, convém escolher uma chave de partição que aparece com frequência como um filtro em suas consultas. As consultas podem ser [roteadas de forma eficiente para apenas as partições físicas relevantes](how-to-query-container.md#in-partition-query) , incluindo a chave de partição no predicado de filtro.

Se a maioria das solicitações de carga de trabalho for consultas e a maioria das suas consultas tiver um filtro de igualdade na mesma propriedade, essa propriedade poderá ser uma boa opção de chave de partição. Por exemplo, se você executar com frequência uma consulta que filtra em `UserID` e, em seguida, selecionar `UserID` como a chave de partição reduziria o número de [consultas entre partições](how-to-query-container.md#avoiding-cross-partition-queries).

No entanto, se o contêiner for pequeno, provavelmente você não terá partições físicas suficientes para precisar se preocupar com o impacto no desempenho de consultas entre partições. A maioria dos contêineres pequenos no Azure Cosmos DB requer apenas uma ou duas partições físicas.

Se o contêiner puder aumentar para mais de algumas partições físicas, você deverá selecionar uma chave de partição que minimize as consultas entre partições. Seu contêiner exigirá mais do que algumas partições físicas quando qualquer uma das seguintes opções for verdadeira:

* Seu contêiner terá mais de 30.000 RU provisionados

* Seu contêiner armazenará mais de 100 GB de dados

## <a name="using-item-id-as-the-partition-key"></a>Usando a ID do item como a chave de partição

Se o contêiner tiver uma propriedade que tenha uma ampla gama de valores possíveis, provavelmente será uma ótima opção de chave de partição. Um exemplo possível de tal propriedade é a *ID do item*. Para contêineres de leitura intensa pequenos ou contêineres de gravação pesada de qualquer tamanho, a *ID do item* é naturalmente uma ótima opção para a chave de partição.

A ID do *Item* de Propriedade do sistema existe em cada item em seu contêiner. Você pode ter outras propriedades que representam uma ID lógica do seu item. Em muitos casos, essas também são ótimas opções de chave de partição pelos mesmos motivos da *ID do item*.

A *ID do item* é uma ótima opção de chave de partição pelos seguintes motivos:

* Há uma grande variedade de valores possíveis (uma ID de *Item* exclusiva por item).
* Como há uma ID de *Item* exclusiva por item, a *ID do item* faz um ótimo trabalho em balanceamento uniforme de consumo e armazenamento de dados em ru.
* Você pode facilmente fazer leituras de ponto eficientes, pois você sempre saberá a chave de partição de um item se souber sua *ID de item*.

Algumas coisas a serem consideradas ao selecionar a *ID do item* como a chave de partição incluem:

* Se a *ID do item* for a chave de partição, ela se tornará um identificador exclusivo em todo o seu contêiner. Você não poderá ter itens com uma *ID de item* duplicada.
* Se você tiver um contêiner de leitura intensa que tenha muitas [partições físicas](partitioning-overview.md#physical-partitions), as consultas serão mais eficientes se tiverem um filtro de igualdade com a ID do *Item*.
* Você não pode executar procedimentos armazenados ou gatilhos em várias partições lógicas.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [taxa de transferência provisionada no Azure Cosmos DB](request-units.md).
* Saiba mais sobre a [distribuição global no Azure Cosmos DB](distribute-data-globally.md).
* Saiba como [provisionar taxa de transferência em um contêiner do Azure Cosmos DB](how-to-provision-container-throughput.md).
* Saiba como [provisionar taxa de transferência em um banco de dados do Azure Cosmos DB](how-to-provision-database-throughput.md).
