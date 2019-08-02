---
title: Escolher um tipo de preço ou SKU para o serviço Azure Search - Azure Search
description: 'O Azure Search pode ser provisionado nessas SKUs: Gratuita, básica e padrão e Standard estão disponível em várias configurações de recursos e níveis de capacidade.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 892a79f898e2448096ad4b252a18e0713bb32e52
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485302"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Escolher um tipo de preço para o Azure Search

Quando você cria um serviço de Azure Search, um [recurso é criado](search-create-service-portal.md) em um tipo de preço (ou SKU) que é fixo para o tempo de vida do serviço. Níveis incluem gratuito, Basic, Standard e armazenamento otimizado. Padrão e com otimização de armazenamento estão disponíveis com várias configurações e capacidades.

A maioria dos clientes começar com a camada gratuita para que eles podem avaliar o serviço. Pós-avaliação, é comum para criar um segundo serviço em uma das camadas mais altas para implantações de desenvolvimento e produção. Você pode concluir todos os tutoriais e guias de início rápido usando a camada gratuita, incluindo aqueles para os muitos recursos de pesquisa cognitiva.

> [!NOTE]
> A partir de 1º de julho, todas as camadas estão geralmente disponíveis, incluindo a camada otimizada de armazenamento. Todos os preços podem ser encontrados na [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) página.

As camadas refletem as características do hardware que hospeda o serviço (em vez dos recursos) e são diferenciadas pelo:

+ O número de índices, que você pode criar.
+ O tamanho e velocidade de partições (armazenamento físico).

Embora todas as camadas, incluindo a camada gratuita, geralmente oferecem paridade de recursos, maiores cargas de trabalho podem ditar a necessidade de níveis mais altos. Por exemplo, [enriquecimento da inteligência Artificial com os serviços Cognitivos](cognitive-search-concept-intro.md) tem habilidades de longa execução que o tempo limite em um serviço gratuito, a menos que o conjunto de dados é pequeno.

> [!NOTE] 
> A exceção a paridade de recursos é [indexadores](search-indexer-overview.md), que não estão disponível no S3 HD.
>

Dentro de uma camada, você pode [ajustar os recursos de partição e réplica](search-capacity-planning.md) para aumentar ou diminuir a escala. Você pode começar com uma ou duas de cada e, em seguida, aumentar temporariamente seu poder computacional uma pesada carga de trabalho de indexação. A capacidade de ajustar os níveis de recursos dentro de uma camada adiciona flexibilidade, mas também complica um pouco a análise. Talvez você precise experimentar para verificar se um nível mais baixo com mais recursos/réplicas oferece melhor valor e o desempenho do que uma camada superior com menos recursos. Para saber mais sobre quando e por que você deve ajustar a capacidade, confira [Considerações sobre desempenho e otimização](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Camadas para o Azure Search

A tabela a seguir lista as camadas disponíveis. Você pode encontrar mais informações sobre as várias camadas na [página de preços](https://azure.microsoft.com/pricing/details/search/), no [limites de serviço no Azure Search](search-limits-quotas-capacity.md) do artigo e no portal de página quando você está Provisionando um serviço.

|Camada | Capacity |
|-----|-------------|
|Grátis | Compartilhado com outros assinantes. Não é escalonável. Limitado a três índices e 50 MB de armazenamento. |
|Basic | Recursos de computação dedicados para cargas de trabalho de produção em uma escala menor. Uma partição de 2 GB e até três réplicas. |
|1 Standard (S1) | Para S1 e superior, dedicadas máquinas com mais capacidade de armazenamento e processamento em cada nível. Para S1, o tamanho da partição é 25 GB/partição (com um máximo de 300 GB por serviço). |
|Standard 2 (S2) | Semelhante à S1, mas com partições de 100 GB (e um máximo de 1,2 TB por serviço). |
|Standard 3 (S3) | Partições de 200 GB (com um máximo de 2,4 TB por serviço). |
|Alta densidade Standard 3 (S3 HD) | Alta densidade é uma *modo de hospedagem* S3. O hardware subjacente é otimizado para um grande número de índices menores e é destinado a cenários de multilocação. S3 HD tem o mesmo encargo por unidade como S3, mas o hardware é otimizado para leituras rápidas de arquivos em um grande número de índices menores.|
|Armazenamento otimizado 1 (L1) | Partições de 1 TB (com um máximo de 12 TB por serviço). |
|Armazenamento otimizado 2 (L2) | Partições de 2 TB (com um máximo de 24 TB por serviço). |

> [!NOTE] 
> As camadas de armazenamento otimizado oferecem maior capacidade de armazenamento a um preço menor por TB do que as camadas Standard. A desvantagem principal é mais alta latência da consulta, que você deve validar para seus requisitos de aplicativo específico.  Para saber mais sobre as considerações de desempenho desta camada, consulte [considerações de desempenho e otimização](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Como funciona a cobrança

Há três maneiras de incorrer em custos no Azure Search. Esta seção descreve os três componentes de cobrança: 

+ custo de serviço do núcleo
+ encargos de saída (ou largura de banda) de dados
+ Aprimoramentos de inteligência Artificial

### <a name="core-service-costs-fixed-and-variable"></a>Custos de serviço principais (fixos e variável)

Para o serviço em si, o custo mínimo é a primeira unidade de pesquisa (partição 1 réplica x 1). Esse mínimo é fixa para o tempo de vida do serviço porque o serviço não pode ser executado em nada menos do que essa configuração.

Além do mínimo, você pode adicionar réplicas e partições de forma independente. Por exemplo, você pode adicionar somente réplicas ou apenas as partições. Aumentos incrementais na capacidade por meio de réplicas e partições compõem o componente de custo variável.

A cobrança é baseada em uma [fórmula (réplicas x partições x taxa)](#search-units). A tarifa cobrada depende do tipo de preço que você selecionar.

Preço por unidade é indicado na seguinte captura de tela, gratuito, Basic e S1. (S2, S3, L1 e L2 não são exibidos.) Se você criar um serviço básico, seu custo mensal será o valor que aparece para médio *preço 1*. Para um serviço padrão, o custo mensal será média o valor que aparece para *preço 2*. Custos unitários aumentar para cada camada, pois a capacidade de armazenamento e energia computacional é maior em cada camada consecutiva. As taxas de Azure Search estão disponíveis na [página de preços do Azure Search](https://azure.microsoft.com/pricing/details/search/).

![Preço unitário](./media/search-sku-tier/per-unit-pricing.png "preços por unidade")

Quando você estiver estimar o custo de uma solução de pesquisa, mantenha em mente que preço e capacidade não linear. (Mais de dobrar a capacidade de dobra o custo.) Para obter um exemplo de como funciona a fórmula, consulte [como alocar partições e réplicas](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Cobrança com base em unidades de pesquisa

O conceito de cobrança mais importante para entender para operações de Azure Search é a *unidade de pesquisa* (SU). Já que Azure Search depende de réplicas e partições para indexação e consultas, não faz sentido cobrar de apenas uma maneira ou de outra. Em vez disso, a cobrança baseia-se em uma combinação de ambos.

SU é o produto do *réplicas* e *partições* usado por um serviço: **(R x P = SU)** .

Cada serviço começa com uma UA (uma réplica multiplicada por uma partição) como o mínimo. O máximo para qualquer serviço é de 36 SUs. Esse máximo pode ser alcançado de diversas maneiras: réplicas de 6 partições x 6 ou réplicas de 3 partições x 12, por exemplo. É comum usar menos do que a capacidade total (por exemplo, uma réplica de 3, a partição 3 service cobrada como 9 SUs). Consulte a [combinações de partição e réplica](search-capacity-planning.md#chart) gráfico de combinações válidas.

A taxa de cobrança por hora é por SU. Cada camada tem uma taxa maior progressivamente. Níveis mais altos vêm com partições maiores e mais rápidas, e isso contribui para uma taxa horária geral maior para essa camada. Você pode exibir as taxas para cada camada sobre o [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) página.

A maioria dos clientes coloca apenas uma parte da capacidade total online, mantendo o restante em reserva. Para cobrança, o número de partições e réplicas que você faça a conexão, fórmula, calculado com o SU determina o que você paga por hora.

### <a name="data-egress-charges-during-indexing"></a>Encargos de saída de dados durante a indexação

Usando o [indexadores do Azure Search](search-indexer-overview.md) podem afetar a cobrança, dependendo do local de seus serviços. Você pode eliminar encargos de saída de dados totalmente se você criar o serviço Azure Search na mesma região que seus dados. Eis algumas informações sobre o [página de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft não cobra para quaisquer dados de entrada para qualquer serviço do Azure ou por quaisquer dados de saída do Azure Search.

+ Em soluções multisserviço, não há nenhum custo para dados passados durante a transmissão quando todos os serviços estão na mesma região.

Encargos se aplicam para dados de saída, se os serviços estiverem em regiões diferentes. Esses encargos não são, na verdade, parte de sua conta de Azure Search. Eles são mencionados aqui porque se você estiver usando dados ou indexadores enriquecida com inteligência Artificial para extrair dados de diferentes regiões, você verá os custos refletidos na sua fatura geral.

### <a name="ai-enrichments-with-cognitive-services"></a>Aprimoramentos de inteligência Artificial com os serviços Cognitivos

Para [enriquecimento da inteligência Artificial com os serviços Cognitivos](cognitive-search-concept-intro.md), você deve planejar anexar a um recurso faturável de serviços Cognitivos do Azure, na mesma região do Azure Search na camada de preços para o processamento de pagamento conforme o uso S0. Não há nenhum custo fixo associado com a anexação de serviços Cognitivos. Você paga apenas pelo processamento que você precisa.

Extração de imagem durante a decodificação de documentos é um encargo de Azure Search. Ele é cobrado de acordo com o número de imagens extraídos de seus documentos. A extração de texto atualmente é gratuita.

Outros aprimoramentos, como processamento de linguagem natural, se baseiam [habilidades cognitivas internos](cognitive-search-predefined-skills.md) e cobrados em relação a um recurso de serviços Cognitivos. Eles serão cobrados com a mesma taxa como se você executou a tarefa por meio dos serviços Cognitivos diretamente. Para obter mais informações, consulte [anexar a um recurso de serviços Cognitivos com um conjunto de qualificações](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Cobrança para extração de imagens na pesquisa cognitiva

Se você extrair imagens de arquivos em um pipeline de indexação de pesquisa cognitiva, você será cobrado para essa operação em sua fatura do Azure Search. Em um [configuração do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** é o parâmetro que dispara a extração de imagens. Se **imageAction** é definido como "none" (o padrão), você não será cobrado para extração de imagens.

Preço está sujeito a alterações. Ela está documentada na [detalhes de preços](https://azure.microsoft.com/pricing/details/search/) página para o Azure Search.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Cobrança de habilidades internas na pesquisa cognitiva

Quando você configura um pipeline de aprimoramento, quaisquer [habilidades internas](cognitive-search-predefined-skills.md) usadas no pipeline baseiam-se em modelos de aprendizado de máquina. Esses modelos são fornecidos pelos serviços Cognitivos. Se você usar esses modelos durante a indexação, você será cobrado com a mesma taxa como seria de se você solicitou o recurso diretamente.

Por exemplo, digamos que você tem um pipeline que usa o reconhecimento óptico de caracteres (OCR) em relação a arquivos verificados de JPEG e o texto resultante é enviada por push em um índice de Azure Search para consultas de pesquisa de forma livre. Seu pipeline de indexação incluiria um indexador com a [habilidade de OCR](cognitive-search-skill-ocr.md), a qual seria uma habilidade [anexada a um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md). Quando você executar o indexador, os encargos para execução de OCR serão exibidos na sua fatura de recursos Cognitivos.

## <a name="tips-for-reducing-costs"></a>Dicas para reduzir os custos

Não é possível desligar o serviço para reduzir sua fatura. Recursos dedicados são sempre operacionais, alocada para seu uso exclusivo para o tempo de vida do seu serviço. A única maneira de reduzir sua fatura é reduzir as réplicas e partições para um nível que ainda fornece um desempenho aceitável e [conformidade com o SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

É uma maneira de reduzir os custos de escolher uma camada com uma taxa por hora inferior. As tarifas por hora do S1 são menores do que as do S2 ou do S3. Supondo que você provisionar seu serviço no final inferior suas projeções de carga, se você excedem o serviço, você pode criar um segundo serviço em camadas maiores, recriar os índices no serviço de segundo e, em seguida, exclua o primeiro deles.

Se você já fez o planejamento de capacidade para servidores locais, você sabe que é comum a "comprar para cima" para que você possa manipular o crescimento projetado. Com um serviço de nuvem, você pode adotar as economias de custo mais agressivamente porque você não fica limitado a uma compra específica. Você sempre pode alternar para um serviço de camada superior se atual não é suficiente.

### <a name="capacity"></a>Capacity

No Azure Search, a capacidade é estruturada como *réplicas* e *partições*.

+ As réplicas são instâncias do serviço de pesquisa. Cada réplica hospeda uma cópia com balanceamento de carga de um índice. Por exemplo, um serviço com seis réplicas tem seis cópias de cada índice carregadas no serviço.

+ As partições armazenam índices e dividem automaticamente os dados pesquisáveis. Duas partições dividir seu índice na metade, três partições dividi-la em terços e assim por diante. Em termos de capacidade, *tamanho da partição* é o recurso diferenciador principal entre camadas.

> [!NOTE]
> Suportam a todas as camadas Standard e armazenamento otimizado [combinações flexíveis de partições e réplicas](search-capacity-planning.md#chart) para que você possa [otimizar seu sistema de armazenamento ou velocidade](search-performance-optimization.md) alterando o saldo. A camada básica oferece até três réplicas para alta disponibilidade, mas tem apenas uma partição. Camadas gratuitas não fornecem recursos dedicados: computação de recursos são compartilhados por vários assinantes.

### <a name="more-about-service-limits"></a>Mais informações sobre limites de serviço

Recursos dos serviços de host, como índices e indexadores. Cada camada impõe [limites de serviço](search-limits-quotas-capacity.md) no número de recursos que você pode criar. Portanto, o número máximo de índices (e outros objetos) é o segundo recurso diferencial entre camadas. Ao analisar cada opção no portal, observe os limites no número de índices. Outros recursos, como os indexadores, fontes de dados e conjuntos de habilidades, são afixados aos limites de índice.

## <a name="consumption-patterns"></a>Padrões de consumo

A maioria dos clientes começam com o serviço gratuito, o que eles mantêm indefinidamente, e, em seguida, escolha uma das camadas Standard ou armazenamento otimizado para cargas de trabalho de desenvolvimento ou produção graves.

![O Azure Search, tipos de preço](./media/search-sku-tier/tiers.png "tipos de preço do Azure Search")

Nas extremidades baixos e altas, básico e S3 HD são para padrões de consumo importante mas atípicos. Basic é para cargas de trabalho de produção de pequeno porte. Ele oferece alta disponibilidade, recursos dedicados e SLAs, mas ele oferece armazenamento modesto, beirando os total de 2 GB. Essa camada foi projetada para clientes que underutilize consistentemente a capacidade disponível. No high-end, S3 HD é para cargas de trabalho típicas de ISVs, parceiros, [soluções multilocatárias](search-modeling-multitenant-saas-applications.md), ou qualquer configuração que requer um grande número de índices pequenos. Geralmente fica claro quando básica ou S3 HD é a camada certa. Se você quiser confirmação, você pode postar em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/) para obter orientação.

As camadas standard mais comumente usadas, S1 por meio de S3, formam uma progressão de níveis crescentes de capacidade. Há limites em um número de índices, indexadores e corolário recursos e pontos de inflexão no tamanho da partição:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Tamanho da partição|  25 GB | 100 GB | 200 GB |  |  |  |  |
| limites de índice e indexador| 50 | 200 | 200 |  |  |  |  |

S1 é uma opção comum para clientes que precisam de recursos dedicados e várias partições. S1 oferece as partições de 25 GB e até 12 partições, fornecendo um limite por serviço de 300 GB que você maximize as partições em réplicas. (Consulte [alocar partições e réplicas](search-capacity-planning.md#chart) balanceamento de alocações para obter mais informações.)

As páginas de portal e preços colocar o foco em armazenamento e tamanho da partição, mas, para cada camada, todos os recursos (capacidade de disco, velocidade, CPUs) de computação geralmente aumenta linearmente com o preço. Uma réplica de S2 é mais rápido do que a S1 e S3 é mais rápido que S2. Camadas S3 interromper o padrão de preços de computação linear com desproporcionalmente mais rápida e/s. Se você espera de e/s para ser o gargalo, lembre-se de que você pode obter muito mais IOPS com S3 do que você pode começar com os níveis inferiores.

S3 e S3 HD contam com infra-estrutura de alta capacidade idêntica, mas que elas atinjam seus limites máximos de maneiras diferentes. S3 tem como alvo um número menor de índices muito grandes, portanto, seu limite máximo é vinculado a recursos (2,4 TB para cada serviço). O S3 HD destina-se um grande número de índices muito pequenos. Com 1.000 índices, o S3 HD atinge os limites na forma de restrições de índice. Se você for um cliente do S3 HD e precisar de mais de 1.000 índices, entre em contato com o Microsoft Support para obter informações sobre como proceder.

> [!NOTE]
> Limites de documentos foram uma consideração ao mesmo tempo, mas não são mais aplicáveis para novos serviços. Para obter informações sobre as condições em que os limites de documento ainda se aplicam, consulte [limites de documentos](search-limits-quotas-capacity.md#document-limits).
>

Armazenamento otimizado camadas, L1 e L2, são ideais para aplicativos com requisitos de dados grandes, um número relativamente baixo de usuários finais, mas quando minimizando a latência da consulta não é a prioridade mais alta.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Tamanho da partição|  1 TB | 2 TB |  |  |  |  |  |
| limites de índice e indexador| 10 | 10 |  |  |  |  |  |

L2 oferece duas vezes a capacidade de armazenamento geral L1.  Escolha seu tipo com base na quantidade máxima de dados que você acha que precisa de seu índice. As partições de camada de L1 escalar verticalmente em incrementos de 1 TB para um máximo de 12 TB. As partições de L2 aumentam 2 TBs por partição até um máximo de 24 TB.

## <a name="evaluating-capacity"></a>Avaliar a capacidade

Capacidade e os custos de execução do serviço estão diretamente relacionados. As camadas impõem limites em dois níveis: armazenamento e recursos. Você deve pensar sobre ambos porque o limite que você atingir o primeiro é o limite efetivo.

Requisitos de negócios normalmente determinam o número de índices, será necessário. Por exemplo, talvez seja necessário um índice de global para um grande repositório de documentos. Ou, talvez seja necessário vários índices com base no nicho de negócios, aplicativo ou região.

Para determinar o tamanho de um índice, você precisa [compilar um](search-create-index-portal.md). A estrutura de dados no Azure Search é principalmente uma [índice invertido](https://en.wikipedia.org/wiki/Inverted_index) estrutura, que tem características diferentes de dados de origem. Para um índice invertido, tamanho e a complexidade são determinadas pelo conteúdo, não necessariamente pela quantidade de dados que alimentam nele. Uma fonte de dados grandes com alta redundância pode resultar em um índice menor que um conjunto de dados menor que contém o conteúdo altamente variável. Portanto, é praticamente impossível inferir o tamanho do índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Mesmo que a estimativa das necessidades futuras para o armazenamento e de índices pode parecer suposições, vale a pena fazer. Se a capacidade de uma camada acaba sendo muito baixo, você precisará provisionar um novo serviço em uma camada mais alta e, em seguida [recarregar os índices](search-howto-reindex.md). Não há nenhuma atualização in-loco de um serviço de uma SKU para outra.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Etapa 1: Desenvolver estimativas aproximadas, usando a camada gratuita

Uma abordagem para estimar a capacidade é iniciar com a camada gratuita. Lembre-se de que o serviço gratuito oferece até três índices, 50 MB de armazenamento e 2 minutos de tempo de indexação. Ele pode ser um desafio para estimar um tamanho de índice projetado com essas restrições. Aqui está uma abordagem que podem ser executadas:

+ [Criar um serviço gratuito](search-create-service-portal.md).
+ Prepare um dataset pequeno e representativo (por exemplo, 5.000 documentos e tamanho da amostra de 10 por cento).
+ [Criar um índice inicial](search-create-index-portal.md) e anote seu tamanho no portal (por exemplo, 30 MB).

Se for um representante e 10 por cento da fonte de dados inteiro, um índice de 30 MB torna-se em aproximadamente 300 MB se todos os documentos são indexados. Armado com esse número preliminar, você talvez duas vezes essa quantidade para orçar para dois índices (desenvolvimento e produção). Isso lhe dá um total de 600 MB nos requisitos de armazenamento. Esse requisito facilmente é atendido pela camada básica, portanto, você poderia iniciar lá.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Etapa 2: Desenvolver estimativas refinadas por meio de uma camada faturável

Alguns clientes preferem iniciar com recursos dedicados que podem acomodar amostragem maior e tempos de processamento e, em seguida, desenvolver estimativas realistas da quantidade de índice, o tamanho e volumes de consulta durante o desenvolvimento. Inicialmente, um serviço é provisionado com base em uma estimativa melhor estimativa. Em seguida, à medida que o projeto de desenvolvimento amadurece, as equipes geralmente sabem se o serviço existente está acima ou abaixo da capacidade para cargas de trabalho de produção projetada.

1. [Examine os limites de serviço em cada camada](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar se os níveis inferiores podem suportar o número de índices que você precisa. Entre as camadas Basic, S1 e S2, limites de índice são 15, 50 e 200, respectivamente. A camada otimizada de armazenamento tem um limite de 10 índices porque ele foi projetado para dar suporte a um número baixo de índices muito grandes.

1. [Criar um serviço em uma camada faturável](search-create-service-portal.md):

    + Inicie o baixo, em básico ou S1, se você estiver no início de sua curva de aprendizado.
    + Inicie o alto, S2 ou S3 até mesmo, se você souber que você vai ter cargas de consulta e indexação em grande escala.
    + Comece com o armazenamento otimizado em L1 ou L2, se você estiver indexando uma grande quantidade de dados e carga de consulta é relativamente baixa, como com um aplicativo de negócios internos.

1. [Criar um índice inicial](search-create-index-portal.md) para determinar como a fonte de dados traduz para um índice. Essa é a única maneira de estimar o tamanho do índice.

1. [Monitorar armazenamento, limites de serviço, volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra consultas por segunda, limitadas de consultas e de latência de pesquisa. Todos esses valores podem ajudar você a decidir se você tiver selecionado a camada certa. Você também pode configurar monitoramento profundo de valores como análise de clickthrough, permitindo [análise de tráfego de pesquisa](search-traffic-analytics.md).

Número de índice e tamanho são igualmente importantes para sua análise. Isso ocorre porque os limites máximos são atingidos por meio da utilização total de armazenamento (partições) ou por limites máximos de recursos (índices, indexadores e assim por diante), o que vier primeiro. O portal ajuda a manter o controle de ambos, mostrando o uso atual e os limites máximos lado a lado na página Visão geral.

> [!NOTE]
> Requisitos de armazenamento podem ser aumentados se os documentos contiverem dados estranhos. O ideal é que os documentos contêm apenas os dados que você precisa para a experiência de pesquisa. Dados binários não não pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de tabela ou blob do Azure). Em seguida, deve ser adicionado a um campo no índice para manter uma referência URL aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos, se você está carregando vários documentos em uma solicitação em massa). Para obter mais informações, confira [Limites de serviço no Azure Search](search-limits-quotas-capacity.md).
>

**Considerações sobre volume de consultas**

Consultas por segundo (QPS) é uma métrica importante durante o ajuste de desempenho, mas geralmente é apenas uma consideração de camada se você espera que o volume de consulta altas desde o início.

As camadas Standard podem fornecer um equilíbrio de réplicas e partições. Você pode aumentar o tempo de resposta de consulta com a adição de réplicas para balanceamento de carga ou adicionar partições para processamento paralelo. Você pode, em seguida, ajustar o desempenho depois que o serviço é provisionado.

Se você espera grandes sustentada volumes de consulta desde o início, você deve considerar o padrão mais altos, apoiados por um hardware mais potente. Você pode colocar as partições e réplicas offline ou até mesmo mudar para um serviço de nível inferior, se esses volumes de consulta não ocorrem. Para obter mais informações sobre como calcular a taxa de transferência de consulta, consulte [desempenho do Azure Search e otimização](search-performance-optimization.md).

As camadas de armazenamento otimizado são úteis para cargas de trabalho de dados grandes, que dão suporte a mais armazenamento de índice global disponível para quando os requisitos de latência da consulta são menos importantes. Você ainda deve usar réplicas adicionais para as partições adicionais e balanceamento de carga para processamento paralelo. Você pode, em seguida, ajustar o desempenho depois que o serviço é provisionado.

**Contratos de nível de serviço**

Os recursos gratuitos de camada e de visualização não fornecem [contratos de nível de serviço](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Você precisa ter duas ou mais réplicas para SLAs de consulta (leitura). Você precisa ter três ou mais réplicas de consulta e indexação SLAs (leitura-gravação). O número de partições não afeta os SLAs.

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de camada

+ Saiba como criar índices eficazes e saiba quais métodos de atualização têm menos impacto. Use [análise de tráfego de pesquisa](search-traffic-analytics.md) para obter informações sobre a atividade de consulta.

+ Permitir que as métricas compilar em torno de consultas e coletar dados de padrões de uso (consultas durante o horário comercial, horários de pico de indexação). Use esses dados para informar decisões de provisionamento do serviço. Embora não seja prático em uma cadência de hora em hora ou diariamente, você pode ajustar dinamicamente partições e recursos para acomodar alterações planejadas em volumes de consulta. Você também pode acomodar alterações não planejadas, mas prolongadas se níveis mantenha longo o suficiente para justificar a executar uma ação.

+ Lembre-se de que a única desvantagem do underprovisioning é que você precisa destruir um serviço se os requisitos reais são maiores do que suas previsões. Para evitar a interrupção do serviço, crie um novo serviço na mesma assinatura em uma camada superior e execute-o lado a lado até todos os aplicativos e solicitações de novo ponto de extremidade de destino.

## <a name="next-steps"></a>Próximas etapas

Iniciar com uma camada gratuita e criar um índice inicial, usando um subconjunto de seus dados para entender suas características. A estrutura de dados no Azure Search é uma estrutura de índice invertido. O tamanho e a complexidade de um índice invertido é determinado pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar em um índice menor que o conteúdo altamente irregular. Características de modo que o conteúdo em vez do tamanho do conjunto de dados determinam os requisitos de armazenamento de índice.

Depois de ter uma estimativa inicial do seu tamanho de índice [provisionar um serviço faturável](search-create-service-portal.md) em uma das camadas discutidas neste artigo: Basic, Standard ou armazenamento otimizado. Relaxe quaisquer restrições artificiais no dimensionamento de dados e [recriar o índice](search-howto-reindex.md) para incluir todos os dados que você deseja que seja pesquisável.

[Alocar partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e escalabilidade que você precisa.

Se o desempenho e capacidade são tudo bem, você terá concluído. Caso contrário, crie novamente um serviço de pesquisa em uma outra camada mais estreitamente alinhada a suas necessidades.

> [!NOTE]
> Se você tiver dúvidas, poste no [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o suporte do Azure](https://azure.microsoft.com/support/options/).
