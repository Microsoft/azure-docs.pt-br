---
title: Estimar a capacidade de cargas de trabalho de consulta e índice
titleSuffix: Azure Cognitive Search
description: Ajuste os recursos de computador da partição e da réplica no Azure Pesquisa Cognitiva, em que cada recurso é cobrado nas unidades de pesquisa cobráveis.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d848c1ed1ab9d4cb24dec9423d93ec62ab45633b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537214"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Estimar e gerenciar a capacidade de um serviço de Pesquisa Cognitiva do Azure

Antes de [provisionar um serviço de pesquisa](search-create-service-portal.md) e bloqueá-lo em um tipo de preço específico, reserve alguns minutos para entender como a capacidade funciona e como você pode ajustar réplicas e partições para acomodar a flutuação de carga de trabalho.

A capacidade é uma função da [camada de serviço](search-sku-tier.md), estabelecendo o armazenamento máximo por serviço, por partição e os limites máximos no número de objetos que você pode criar. A camada básica foi projetada para aplicativos com requisitos de armazenamento modestos (apenas uma partição), mas com a capacidade de executar em uma configuração de alta disponibilidade (3 réplicas). Outras camadas são projetadas para cargas de trabalho ou padrões específicos, como multilocação. Internamente, os serviços criados nessas camadas se beneficiam do hardware que ajuda esses cenários.

A arquitetura de escalabilidade no Azure Pesquisa Cognitiva baseia-se em combinações flexíveis de réplicas e partições para que você possa variar a capacidade dependendo se precisa de mais poder de consulta ou de indexação. Depois que um serviço é criado, você pode aumentar ou diminuir o número de réplicas ou partições de forma independente. Os custos aumentarão com cada recurso físico adicional, mas assim que grandes cargas de trabalho forem concluídas, você poderá reduzir a escala para reduzir sua fatura. Dependendo da camada e do tamanho do ajuste, adicionar ou reduzir a capacidade pode levar de 15 minutos a várias horas.

Ao modificar a alocação de réplicas e partições, é recomendável usar o portal do Azure. O portal impõe limites para combinações permitidas que ficam abaixo dos limites máximos de uma camada. No entanto, se você precisar de uma abordagem de provisionamento baseada em script ou em código, o [Azure PowerShell](search-manage-powershell.md) ou a [API REST de gerenciamento](/rest/api/searchmanagement/services) são soluções alternativas.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Conceitos: unidades de pesquisa, réplicas, partições, fragmentos

A capacidade é expressa *em unidades de pesquisa* que podem ser alocadas em combinações de *partições* e *réplicas*, usando um mecanismo de *fragmentação* subjacente para dar suporte a configurações flexíveis:

| Conceito  | Definição|
|----------|-----------|
|*Unidade de pesquisa* | Um único incremento da capacidade total disponível (36 unidades). Também é a unidade de cobrança para um serviço de Pesquisa Cognitiva do Azure. É necessário um mínimo de uma unidade para executar o serviço.|
|*Réplica* | Instâncias do serviço de pesquisa, usadas principalmente para equilibrar a carga das operações de consulta. Cada réplica hospeda uma cópia de um índice. Se você alocar três réplicas, terá três cópias de um índice disponível para atender às solicitações de consulta.|
|*Partição* | Armazenamento físico e e/s para operações de leitura/gravação (por exemplo, ao recompilar ou atualizar um índice). Cada partição tem uma fatia do índice total. Se você alocar três partições, o índice será dividido em terços. |
|*Fragmentos* | Um bloco de um índice. O Azure Pesquisa Cognitiva divide cada índice em fragmentos para tornar o processo de adição de partições mais rápido (movendo fragmentos para novas unidades de pesquisa).|

O diagrama a seguir mostra a relação entre réplicas, partições, fragmentos e unidades de pesquisa. Ele mostra um exemplo de como um único índice é estendido em quatro unidades de pesquisa em um serviço com duas réplicas e duas partições. Cada uma das quatro unidades de pesquisa armazena apenas metade dos fragmentos do índice. As unidades de pesquisa na coluna esquerda armazenam a primeira metade dos fragmentos, que compõem a primeira partição, enquanto aquelas na coluna direita armazenam a segunda metade dos fragmentos, que compõem a segunda partição. Como há duas réplicas, há duas cópias de cada fragmento de índice. As unidades de pesquisa na linha superior armazenam uma cópia, que inclui a primeira réplica, enquanto aquelas na linha inferior armazenam outra cópia, compostando a segunda réplica.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Os índices de pesquisa são fragmentados entre partições.":::

O diagrama acima é apenas um exemplo. Muitas combinações de partições e réplicas são possíveis, até um máximo de 36 unidades de pesquisa de total.

No Pesquisa Cognitiva, o gerenciamento de fragmentos é um detalhe de implementação e não configurável, mas saber que um índice é fragmentado ajuda a entender as anomalias ocasionais em classificação e comportamentos de preenchimento automático:

+ A classificação de anomalias: as pontuações de pesquisa são calculadas primeiro no nível do fragmento e, em seguida, agregadas em um único conjunto de resultados. Dependendo das características do conteúdo do fragmento, as correspondências de um fragmento podem ter uma classificação maior do que as correspondências em outra. Se você observar classificações intuitivas de contador nos resultados da pesquisa, provavelmente ocorrerá devido aos efeitos da fragmentação, especialmente se os índices forem pequenos. Você pode evitar essas anomalias de classificação escolhendo [calcular pontuações globalmente em todo o índice](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), mas isso incorrerá em uma penalidade de desempenho.

+ Anomalias de preenchimento automático: consultas de preenchimento automático, em que as correspondências são feitas nos primeiros vários caracteres de um termo parcialmente inserido, aceitam um parâmetro difuso que Forgives pequenos desvios na grafia. Para preenchimento automático, a correspondência difusa é restrita a termos dentro do fragmento atual. Por exemplo, se um fragmento contiver "Microsoft" e um termo parcial de "Micor" for inserido, o mecanismo de pesquisa corresponderá em "Microsoft" nesse fragmento, mas não em outros fragmentos que contenham as partes restantes do índice.

## <a name="how-to-evaluate-capacity-requirements"></a>Como avaliar os requisitos de capacidade

A capacidade e os custos de executar o serviço são disponibilizados em mãos. As camadas impõem limites em dois níveis: armazenamento e conteúdo (uma contagem de índices em um serviço, por exemplo). É importante considerar ambos porque o limite que você atingir primeiro é o limite efetivo.

Quantidades de índices e outros objetos geralmente são ditados por requisitos de negócios e de engenharia. Por exemplo, você pode ter várias versões do mesmo índice para desenvolvimento, teste e produção ativos.

As necessidades de armazenamento são determinadas pelo tamanho dos índices que você espera criar. Não há heurística sólida ou Generalidades que ajudam com estimativas. A única maneira de determinar o tamanho de um índice é [Build One](search-what-is-an-index.md). Seu tamanho será baseado em dados importados, na análise de texto e na configuração de índice, como se você habilitar sugestores, filtragem e classificação.

Para pesquisa de texto completo, a estrutura de dados primária é uma estrutura de [índice invertida](https://en.wikipedia.org/wiki/Inverted_index) , que tem características diferentes dos dados de origem. Para um índice invertido, o tamanho e a complexidade são determinados pelo conteúdo, não necessariamente pela quantidade de dados que você feedu nele. Uma fonte de dados grande com alta redundância pode resultar em um índice menor do que um conjunto de dado menor que contém conteúdo altamente variável. Portanto, raramente é possível inferir o tamanho do índice com base no tamanho do conjunto de datas original.

> [!NOTE] 
> Mesmo que a estimativa de necessidades futuras de índices e armazenamento possa parecer uma tarefa de adivinhação, vale a pena fazer isso. Se a capacidade de uma camada for muito baixa, você precisará provisionar um novo serviço em uma camada mais alta e, em seguida, [recarregar os índices](search-howto-reindex.md). Não há nenhuma atualização in-loco de um serviço de uma camada para outra.
>

### <a name="estimate-with-the-free-tier"></a>Estimar com a camada gratuita

Uma abordagem para calcular a capacidade é iniciar com a camada Livre. Lembre-se de que o serviço gratuito oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Pode ser desafiador estimar um tamanho de índice projetado com essas restrições, mas estas são as etapas:

+ [Crie um serviço gratuito](search-create-service-portal.md).
+ Prepare um DataSet pequeno e representativo.
+ Crie um índice e carregue seus dados. Se o conjunto de dados puder ser hospedado em uma fonte de dado do Azure com suporte de indexadores, você poderá usar o [Assistente de importação de dados no portal](search-get-started-portal.md) para criar e carregar o índice. Caso contrário, você deve usar REST e [postmaster](search-get-started-rest.md) ou [Visual Studio Code](search-get-started-vs-code.md) para criar o índice e enviar os dados por push. O modelo de push requer que os dados estejam na forma de documentos JSON, em que os campos no documento correspondem aos campos no índice.
+ Coletar informações sobre o índice, como tamanho. Os recursos e atributos têm um impacto no armazenamento. Por exemplo, a adição de sugestores (consultas de pesquisa conforme o tipo) aumentará os requisitos de armazenamento. 

  Usando o mesmo conjunto de dados, você pode tentar criar várias versões de um índice, com atributos diferentes em cada campo, para ver como os requisitos de armazenamento variam. Para obter mais informações, consulte ["implicações de armazenamento" em criar um índice básico](search-what-is-an-index.md#index-size).

Com uma estimativa aproximada em mãos, você pode dobrar esse valor para o orçamento de dois índices (desenvolvimento e produção) e, em seguida, escolher a camada adequadamente.

### <a name="estimate-with-a-billable-tier"></a>Estimar com uma camada Faturável

Os recursos dedicados podem acomodar grandes períodos de amostragem e processamento para estimativas mais realistas de quantidade de índice, tamanho e volumes de consulta durante o desenvolvimento. Alguns clientes saltam diretamente com uma camada Faturável e, em seguida, reavaliam à medida que o projeto de desenvolvimento amadurece.

1. [Examine os limites de serviço em cada camada](./search-limits-quotas-capacity.md#index-limits) para determinar se as camadas inferiores podem dar suporte ao número de índices de que você precisa. Nas camadas básica, S1 e S2, os limites de índice são 15, 50 e 200, respectivamente. A camada de armazenamento otimizado tem um limite de 10 índices porque ele foi projetado para dar suporte a um número baixo de índices muito grandes.

1. [Criar um serviço em uma camada faturável](search-create-service-portal.md):

    + Comece de baixo, em básico ou S1, se você não tiver certeza sobre a carga projetada.
    + Comece de alta, em S2 ou mesmo S3, se o teste incluir a indexação em larga escala e cargas de consulta.
    + Comece com o armazenamento otimizado, em L1 ou L2, se você estiver indexando uma grande quantidade de dados e a carga de consulta for relativamente baixa, assim como com um aplicativo de negócios interno.

1. [Criar um índice inicial](search-what-is-an-index.md) para determinar como a fonte de dados traduz para um índice. Essa é a única maneira de estimar o tamanho do índice.

1. [Monitorar armazenamento, limites de serviço, volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra as consultas por segundo, as consultas limitadas e a latência de pesquisa. Todos esses valores podem ajudá-lo a decidir se selecionou a camada certa.

1. Adicione réplicas se você precisar de alta disponibilidade ou se tiver um desempenho de consulta lento.

   Não há diretrizes sobre quantas réplicas são necessárias para acomodar cargas de consulta. O desempenho da consulta depende da complexidade da consulta e das cargas de trabalho concorrentes. Embora a adição de réplicas definitivamente adicione escala e desempenho, o resultado não é estritamente linear: a adição de três réplicas não garante o triplo da taxa de transferência. Para obter orientação sobre como estimar o QPS para sua solução, consulte [escala para desempenho](search-performance-optimization.md)e [consultas de monitor](search-monitor-queries.md).

> [!NOTE]
> Os requisitos de armazenamento podem ser informados se você incluir dados que nunca serão pesquisados. O ideal é que os documentos contenham apenas os dados de que você precisa para a experiência de pesquisa. Os dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de BLOBs ou de tabelas do Azure). Em seguida, um campo deve ser adicionado no índice para manter uma referência de URL aos dados externos. O tamanho máximo de um documento de pesquisa individual é 16 MB (ou menos se você estiver carregando vários documentos em massa em uma solicitação). Para obter mais informações, consulte [limites de serviço no Azure pesquisa cognitiva](search-limits-quotas-capacity.md).
>

**Considerações sobre volume de consultas**

Consultas por segundo (QPS) é uma métrica importante durante o ajuste de desempenho, mas geralmente é apenas uma consideração de camada se você espera um alto volume de consulta no início.

As camadas padrão podem fornecer um equilíbrio entre réplicas e partições. Você pode aumentar o retorno da consulta adicionando réplicas para balanceamento de carga ou adicionar partições para processamento paralelo. Em seguida, você pode ajustar o desempenho depois que o serviço for provisionado.

Se você espera grandes volumes de consulta sustentados desde o início, considere as camadas mais altas Standard, apoiadas por hardware mais potente. Você pode pegar partições e réplicas offline ou até mesmo alternar para um serviço de camada inferior, se esses volumes de consulta não ocorrerem. Para obter mais informações sobre como calcular a taxa de transferência de consulta, consulte [desempenho e otimização do Azure pesquisa cognitiva](search-performance-optimization.md).

As camadas de armazenamento otimizadas são úteis para cargas de trabalho de dados grandes, dando suporte a armazenamento de índice mais geral disponível para quando os requisitos de latência de consulta são menos importantes. Você ainda deve usar réplicas adicionais para balanceamento de carga e partições adicionais para processamento paralelo. Em seguida, você pode ajustar o desempenho depois que o serviço for provisionado.

**Contratos de nível de serviço**

Os recursos de camada gratuita e visualização não fornecem [contratos de nível de serviço (SLAs)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Você precisa ter duas ou mais réplicas para SLAs de consulta (leitura). Você precisa ter três ou mais réplicas para os SLAs de consulta e indexação (leitura/gravação). O número de partições não afeta os SLAs.

## <a name="tips-for-capacity-planning"></a>Dicas para planejamento de capacidade

+ Permitir que as métricas sejam compiladas em consultas e coletar dados sobre padrões de uso (consultas durante o horário comercial, indexação fora do horário de pico). Use esses dados para informar as decisões de provisionamento do serviço. Embora não seja prático em uma cadência de hora ou diária, você pode ajustar dinamicamente as partições e os recursos para acomodar as alterações planejadas nos volumes de consulta. Você também pode acomodar alterações não planejadas, mas sustentadas, se os níveis tiverem tempo suficiente para garantir a tomada de ações.

+ Lembre-se de que a única desvantagem do no provisionamento é que você pode precisar subdividir um serviço se os requisitos reais forem maiores do que suas previsões. Para evitar a interrupção do serviço, você deve criar um novo serviço em uma camada superior e executá-lo lado a lado até que todos os aplicativos e solicitações tenham como destino o novo ponto de extremidade.

## <a name="when-to-add-partitions-and-replicas"></a>Quando adicionar partições e réplicas

Inicialmente, um serviço é alocado um nível mínimo de recursos que consistem em uma partição e uma réplica.

Um único serviço deve ter recursos suficientes para manipular todas as cargas de trabalho (indexação e consultas). Nenhuma carga de trabalho é executada em segundo plano. Você pode agendar a indexação para tempos em que as solicitações de consulta são naturalmente menos frequentes, mas o serviço não priorizará uma tarefa em vez de outra. Além disso, uma certa quantidade de redundância simplifica o desempenho da consulta quando os serviços ou nós são atualizados internamente.

Como regra geral, os aplicativos de pesquisa tendem a precisar de mais réplicas do que as partições, especialmente quando as operações de serviço são tendenciosas em relação a cargas de trabalho de consulta. A seção sobre [alta disponibilidade](#HA) , explica o motivo.

A [camada escolhida](search-sku-tier.md) determina o tamanho e a velocidade da partição, e cada camada é otimizada em um conjunto de características que se ajustam a vários cenários. Se você escolher uma camada de extremidade superior, poderá precisar de menos partições do que se você estiver com S1. Uma das perguntas que você precisará responder por meio de testes autodirecionados é se uma partição maior e mais cara produz um desempenho melhor do que duas partições mais baratas em um serviço provisionado em uma camada mais baixa.

Aplicativos de pesquisa que exigem atualização de dados quase em tempo real precisarão proporcionalmente de mais partições de réplicas. A adição de partições distribui as operações de leitura/gravação em uma quantidade maior de recursos de computação. Também oferece mais espaço em disco para armazenar documentos e índices adicionais.

Índices maiores levam mais tempo para consultar. Assim, você poderá perceber que cada aumento incremental em partições requer um aumento proporcional, mas menor, em réplicas. A complexidade de suas consultas e seu volume influenciarão a rapidez com que a execução da consulta é retornada.

> [!NOTE]
> Adicionar mais réplicas ou partições aumenta o custo da execução do serviço e pode introduzir pequenas variações de como os resultados são ordenados. Certifique-se de verificar a [calculadora de preços](https://azure.microsoft.com/pricing/calculator/) para entender as implicações de cobrança da adição de mais nós. O [gráfico abaixo](#chart) pode ajudá-lo a fazer referência cruzada do número de unidades de pesquisa necessárias para uma configuração específica. Para obter mais informações sobre como as réplicas adicionais afetam o processamento da consulta, consulte [Ordering Results](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Como alocar réplicas e partições

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o serviço de pesquisa.

1. Em **configurações**, abra a página **escala** para modificar réplicas e partições. 

   A captura de tela a seguir mostra um padrão básico provisionado com uma réplica e partição. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas (1). Se o preço unitário era $100 (não um preço real), o custo mensal da execução desse serviço seria de $100 em média.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Página escala mostrando os valores atuais" border="true":::

1. Use o controle deslizante para aumentar ou diminuir o número de partições. A fórmula na parte inferior indica quantas unidades de pesquisa estão sendo usadas. Clique em **Salvar**.

   Este exemplo adiciona uma segunda réplica e partição. Observe a contagem de unidades de pesquisa; Agora, ele é quatro porque a fórmula de cobrança é réplicas multiplicada por partições (2 x 2). Dobrar a capacidade mais do que o dobro do custo da execução do serviço. Se o custo da unidade de pesquisa fosse $100, a nova fatura mensal agora será $400.

   Para os custos por unidade atuais de cada camada, visite a [página de preços](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Adicionar réplicas e partições" border="true":::

1. Depois de salvar, você pode verificar as notificações para confirmar se a ação foi bem-sucedida.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Salvar alterações" border="true":::

   As alterações na capacidade podem levar até várias horas para serem concluídas. Não é possível cancelar quando o processo foi iniciado e não há monitoramento em tempo real para os ajustes de réplica e partição. No entanto, a mensagem a seguir permanece visível enquanto as alterações estão em andamento.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Mensagem de status no portal" border="true":::

> [!NOTE]
> Depois que um serviço é provisionado, ele não pode ser atualizado para uma camada superior. Você precisará criar um serviço de pesquisa no novo tipo e recarregar os índices. Consulte [criar um serviço de pesquisa cognitiva do Azure no portal](search-create-service-portal.md) para obter ajuda com o provisionamento de serviço.
>
> Além disso, partições e réplicas são gerenciadas exclusivamente e internamente pelo serviço. Não há nenhum conceito de afinidade de processador ou atribuição de uma carga de trabalho a um nó específico.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Combinações de partição e réplica

Um serviço Básico pode ter exatamente uma partição e até três réplicas, para o limite máximo de três SUs. O único recurso ajustável são as réplicas. É necessário um mínimo de duas réplicas para alta disponibilidade em consultas.

Todos os serviços de pesquisa padrão e de armazenamento otimizados podem assumir as combinações de réplicas e partições a seguir, sujeitas ao limite de 36-SU permitido para essas camadas. 

|   | **1 partição** | **2 partições** | **3 partições** | **4 partições** | **6 partições** | **12 partições** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 réplica** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 réplicas** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 réplicas** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 réplicas** |4 SU |8 SU |12 SU |16 SU |24 SU |N/D |
| **5 réplicas** |5 SU |10 SU |15 SU |20 SU |30 SU |N/D |
| **6 réplicas** |6 SU |12 SU |18 SU |24 SU |36 SU |N/D |
| **12 réplicas** |12 SU |24 SU |36 SU |N/D |N/D |N/D |

SUs, preço e capacidade são explicados detalhadamente no site do Azure. Para obter mais informações, consulte [detalhes de preços](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> O número de réplicas e partições divide de maneira uniforme em 12 (especificamente, 1, 2, 3, 4, 6 e 12). Isso ocorre porque o Azure Pesquisa Cognitiva divide cada índice em 12 fragmentos para que ele possa ser distribuído em partes iguais em todas as partições. Por exemplo, se o serviço tiver três partições e você criar um índice, cada partição conterá quatro fragmentos do índice. Como o Azure Pesquisa Cognitiva fragmenta um índice é um detalhe de implementação, sujeito a alterações em versões futuras. Embora o número seja 12 hoje, você não deve esperar que ele seja sempre 12 no futuro.
>

<a id="HA"></a>

## <a name="high-availability"></a>Alta disponibilidade

Uma vez que é relativamente fácil e rápido escalar verticalmente, recomendamos começar com uma partição e uma ou duas réplicas e então escalar verticalmente conforme os volumes de consulta se acumulam. As cargas de trabalho de consulta são executadas principalmente em réplicas. Se precisar de mais taxa de transferência ou alta disponibilidade, provavelmente, você precisará de mais réplicas.

Recomendações gerais para alta disponibilidade são:

+ Duas réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)

+ Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas e indexação à medida que documentos individuais são adicionados, atualizados ou excluídos)

Os contratos de nível de serviço (SLA) para o Azure Pesquisa Cognitiva são direcionados em operações de consulta e em atualizações de índice que consistem em Adicionar, atualizar ou excluir documentos.

Camada Básico alcança o topo em uma partição e três réplicas. Se você quiser flexibilidade para responder imediatamente a flutuações na demanda por taxa de transferência de indexação e consulta, considere uma das camadas Standard.  Se você achar que seus requisitos de armazenamento estão crescendo muito mais rapidamente do que a taxa de transferência de consulta, considere uma das camadas de armazenamento otimizado.

## <a name="about-queries-per-second-qps"></a>Sobre consultas por segundo (QPS)

Devido ao grande número de fatores que entram no desempenho da consulta, a Microsoft não publica números esperados de QPS. As estimativas de QPS devem ser desenvolvidas de forma independente por cada cliente usando a camada de serviço, a configuração, o índice e as construções de consulta que são válidas para seu aplicativo. Tamanho do índice e complexidade, o tamanho da consulta e complexidade e a quantidade de tráfego são determinantes principais de QPS. Não é possível oferecer estimativas significativas quando esses fatores são desconhecidos.

As previsões são mais previsíveis quando calculada em serviços em execução em recursos dedicados (camadas Básico e Standard). No nível padrão, é possível estimar melhor o QPS, porque você tem controle sobre mais parâmetros. Para obter orientação sobre como a estimativa da abordagem, consulte [Desempenho e otimização do Azure Cognitive Search](search-performance-optimization.md).

Para as camadas de Otimizado para armazenamento (L1 e L2), você deve esperar uma taxa de transferência de consulta inferior e uma latência mais alta do que as camadas Standard.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como estimar e gerenciar custos](search-sku-manage-costs.md)