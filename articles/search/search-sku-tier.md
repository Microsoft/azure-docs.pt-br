---
title: Escolher um tipo de preço ou SKU para o serviço Azure Search - Azure Search
description: 'O Azure Search pode ser provisionado nestes SKUs: Gratuito, Básico e Padrão, sendo que o Padrão está disponível em várias configurações de recursos e níveis de capacidade.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b50d0c0ca9a4000cc0c725453a3ef04b4bed9275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282429"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Escolher um tipo de preço para o Azure Search

No Azure Search, um [recurso é criado](search-create-service-portal.md) em um tipo de preço ou SKU fixo durante o tempo de vida do serviço. Os níveis incluem **livre**, **básica**, **padrão**, ou **otimizados para armazenamento**.  **Standard** e **otimizados para armazenamento** estão disponíveis em várias configurações e capacidades. 

A maioria dos clientes começam com o **gratuito** camada para avaliação e, em seguida, passar gradualmente para uma das camadas pagas mais altas para implantações de desenvolvimento e produção. Você pode concluir todos os guias de início rápido e tutoriais na camada **Gratuita**, incluindo os de pesquisa cognitiva com uso intensivo de recurso.

> [!NOTE]
> As camadas de serviço com otimização de armazenamento estão atualmente disponíveis como uma visualização a um preço com desconto para fins de teste e experimentação com o objetivo de coletar comentários. Preço final será anunciado posteriormente quando essas camadas são geralmente disponíveis. É recomendável usar essas camadas para aplicativos de produção.

As camadas refletem as características do hardware que hospeda o serviço (em vez dos recursos) e são diferenciadas pelo:

+ Número de índices que você pode criar
+ Tamanho e velocidade de partições (armazenamento físico)

Embora todos os níveis, incluindo a camada **Gratuita**, geralmente ofereçam paridade de recursos, cargas de trabalho maiores podem exigir requisitos de níveis mais altos. Por exemplo, [indexação de inteligência Artificial com os serviços Cognitivos](cognitive-search-concept-intro.md) tem habilidades de longa execução que atingir o tempo limite em um serviço gratuito, a menos que o conjunto de dados, por acaso, é pequeno.

> [!NOTE] 
> A exceção à paridade de recursos são os [indexadores](search-indexer-overview.md), que não estão disponíveis no S3HD.
>

Dentro de uma camada, você pode [ajustar os recursos de partição e réplica](search-capacity-planning.md) para aumentar ou diminuir a escala. Você pode começar com uma ou duas de cada e, em seguida, aumentar temporariamente seu poder computacional uma pesada carga de trabalho de indexação. A capacidade de ajustar os níveis de recursos dentro de uma camada adiciona flexibilidade, mas também complica um pouco a análise. Talvez você precise experimentar para verificar se um nível mais baixo com maior de recursos/réplicas oferece melhor desempenho e valor do que um nível superior com menor alocação de recursos. Para saber mais sobre quando e por que você deve ajustar a capacidade, confira [Considerações sobre desempenho e otimização](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Camadas para o Azure Search

A tabela a seguir lista as camadas disponíveis. Incluem outras fontes de informações de camada a [página de preços](https://azure.microsoft.com/pricing/details/search/), [limites de serviço e dados](search-limits-quotas-capacity.md)e a página do portal quando um serviço de provisionamento.

|Camada | Capacity |
|-----|-------------|
|Grátis | Compartilhado com outros assinantes. Não Evolutivo, limitado a 3 índices e 50 MB de armazenamento. |
|Basic | Recursos de computação dedicados para cargas de trabalho de produção em uma escala menor. Uma partição de 2 GB e até três réplicas. |
|1 Standard (S1) | De S1 em Inscrever computadores dedicados, com mais capacidade de armazenamento e processamento em cada nível. Tamanho da partição é 25 GB/partição (máximo de 300 GB por serviço) para S1. |
|Standard 2 (S2) | Semelhante à S1, mas com 100 GB/partições (máximo 1,2 TB por serviço) |
|Standard 3 (S3) | 200 GB/partição (máximo 2,4 TB por serviço) |
|Padrão 3 alta densidade (S3-HD) | Alta densidade é uma *modo de hospedagem* S3. O hardware subjacente é otimizado para um grande número de índices menores, destinados a cenários de multilocação. S3 HD tem o mesmo encargo por unidade, mas o hardware de S3 é otimizado para leituras rápidas de arquivos em um grande número de índices menores.|
|Armazenamento otimizado 1 (L1) | 1 TB/partição (máximo 12 TB por serviço) |
|Armazenamento otimizado 2 (L2) | 2 TB/partição (máximo 24 TB por serviço) |

> [!NOTE] 
> As camadas de armazenamento otimizado oferecem maior capacidade de armazenamento a um preço menor por TB do que as camadas Standard. A desvantagem principal é mais alta latência da consulta, que você deve validar para seus requisitos de aplicativo específico.  Para saber mais sobre as considerações de desempenho desta camada, consulte [considerações de desempenho e otimização](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Como funciona a cobrança

No Azure Search, há três maneiras de incorrer em custos no Azure Search, e há componentes fixas e variáveis. Esta seção examina cada componente de cobrança por vez.

### <a name="1-core-service-costs-fixed-and-variable"></a>1. Custos de serviço principais (fixos e variável)

Para o serviço em si, a taxa mínima é a primeira unidade de pesquisa (partição 1 réplica x 1), e esse valor é fixo para o tempo de vida do serviço porque o serviço não pode ser executado em nada menos do que essa configuração. 

Além do mínimo, você pode adicionar réplicas e partições de forma independente. Por exemplo, você pode adicionar somente réplicas ou apenas as partições. Aumentos incrementais na capacidade por meio de réplicas e partições constituem o componente de custo variável. 

A cobrança é baseada em uma [fórmula (réplicas x partições x taxa)](#search-units). A tarifa cobrada depende do tipo de preço que você selecionar.

Na seguinte captura de tela, por preço por unidade é indicado para gratuita, Basic e S1 (S2, S3, L1 e L2 não são mostrados). Se você tiver criado uma **básicas**, **padrão**, ou **otimizados para armazenamento** serviço, seu custo mensal teria o valor médio que aparece para *preço-1*e *preço 2* , respectivamente. Custos unitários subir para cada camada como a capacidade de armazenamento e energia computacional é maior em cada camada consecutiva. As taxas de Azure Search são publicadas na [página de preços do Azure Search](https://azure.microsoft.com/pricing/details/search/).

![Por preço por unidade](./media/search-sku-tier/per-unit-pricing.png "por preço por unidade")

Quando uma solução de pesquisa de custos, observe que preço e capacidade não estão lineares (duplicar capacidade mais do que o custo de duplicatas). Para obter um exemplo de como funciona a fórmula, consulte ["Como alocar partições e réplicas"](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).


### <a name="2-data-egress-charges-during-indexing"></a>2. Encargos de saída de dados durante a indexação

Uso de [indexadores do Azure Search](search-indexer-overview.md) pode resultar em impacto na cobrança, dependendo de onde os serviços estão localizados. Você pode eliminar encargos de saída de dados totalmente se você criar o serviço Azure Search na mesma região que seus dados. Os pontos a seguir são do [página de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

+ Microsoft não cobra para quaisquer dados de entrada para qualquer serviço do Azure ou por quaisquer dados de saída do Azure Search.

+ Em soluções de vários serviços, não há encargos para cruzar a transmissão quando todos os serviços estão na mesma região de dados.

Encargos se aplicam para dados de saída, se os serviços estiverem em regiões diferentes. Tais encargos não fazem parte de sua fatura do Azure Search por si só, mas eles são mencionados aqui, porque se você estiver usando dados ou indexadores enriquecida com inteligência Artificial para extrair dados de diferentes regiões, você verá esses custos refletidos na sua fatura geral. 

### <a name="3-ai-enriched-indexing-using-cognitive-services"></a>3. IA-indexação aprimorada usando os serviços Cognitivos

Para [indexação de inteligência Artificial com os serviços Cognitivos](cognitive-search-concept-intro.md), você deve planejar na anexação de um recurso de serviços Cognitivos faturável na S0 camada de preços para o processamento de pagamento conforme o uso. Não há nenhum "custo fixo" associado com a anexação de serviços Cognitivos. Você paga apenas pelo processamento que você precisa.

Extração de imagem durante a decodificação de documentos é um encargo de Azure Search, cobrado com base no número de imagens extraídos de seus documentos. A extração de texto atualmente é gratuita. 

Outros aprimoramentos, como o processamento de linguagem natural, se baseiam [habilidades cognitivas internos](cognitive-search-predefined-skills.md) são cobrados em relação a um recurso de serviços Cognitivos, com a mesma taxa como se você executou a tarefa usando os serviços Cognitivos diretamente. Para obter mais informações, consulte [anexar a um recurso de serviços Cognitivos com um conjunto de qualificações](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

### <a name="billing-based-on-search-units"></a>Cobrança com base em unidades de pesquisa

Em operações do Azure Search, o conceito de cobrança mais importante a ser entendido é o de UA (*unidade de pesquisa*). Já que Azure Search depende de réplicas e partições para indexação e consultas, não faz sentido cobrar de apenas uma maneira ou de outra. Em vez disso, a cobrança baseia-se em uma combinação de ambos. 

UA é o produto da *réplica* e das *partições* usadas por um serviço: **`(R X P = SU)`**

Cada serviço começa com uma UA (uma réplica multiplicada por uma partição) como o mínimo. O máximo para qualquer serviço é 36 SUs, o que pode ser obtido de várias maneiras: 6 partições x 6 ou réplicas ou 3 partições x 12 réplicas, para citar alguns. É comum usar menos do que a capacidade total. Por exemplo, um serviço de 3 réplicas e 3 partições cobrado como 9 UAs. Você pode revisar [este gráfico](search-capacity-planning.md#chart) para ver as combinações válidas em um relance.

A taxa de cobrança é **por hora por UA**, e cada nível tem uma taxa maior progressivamente. Os níveis mais altos são fornecidos com partições maiores e mais rápidas, contribuindo para uma taxa horária geral maior para esse nível. As taxas de cada camada podem ser encontradas em [Detalhes de Preço](https://azure.microsoft.com/pricing/details/search/). 

A maioria dos clientes coloca apenas uma parte da capacidade total online, mantendo o restante em reserva. Em termos de cobrança, esse é o número de partições e réplicas que você colocar online, calculado usando a fórmula de UA, que determina o que você realmente paga por hora.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Cobrança para extração de imagens na pesquisa cognitiva

Se você está extraindo imagens de arquivos em um pipeline de indexação de pesquisa cognitiva, você é cobrado por essa operação em sua fatura do Azure Search. Em uma [configuração do indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), o parâmetro que dispara a extração de imagens é **imageAction**. Se **imageAction** é definido como nenhum (padrão), não há encargos para a extração de imagens.

O preço está sujeito a alterações, mas sempre está documentado na página [Detalhes de preços](https://azure.microsoft.com/pricing/details/search/) do Azure Search. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Cobrança de habilidades internas na pesquisa cognitiva

Quando você configura um pipeline de aprimoramento, quaisquer [habilidades internas](cognitive-search-predefined-skills.md) usadas no pipeline baseiam-se em modelos de aprendizado de máquina. Esses modelos são fornecidos pelos Serviços Cognitivos. O uso desses modelos durante a indexação é cobrado com a mesma taxa aplicada se você solicita o recurso diretamente.

Por exemplo, imagine um cenário com um pipeline que consiste em OCR (reconhecimento óptico de caracteres) usado em arquivos JPEG de imagem digitalizada, em que o texto resultante é enviado para um índice de Azure Search para consultas de pesquisa de forma livre. Seu pipeline de indexação incluiria um indexador com a [habilidade de OCR](cognitive-search-skill-ocr.md), a qual seria uma habilidade [anexada a um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md). Quando você executasse o indexador, as cobranças pela execução de OCR apareceriam em sua fatura de Recursos Cognitivos.

## <a name="tips-for-reducing-costs"></a>Dicas para reduzir os custos

Não é possível desligar o serviço para reduzir a fatura. Os recursos dedicados operam 24 horas, 7 dias da semana, alocados para seu uso exclusivo durante o tempo de vida do serviço. A única maneira de reduzir uma fatura é, reduzindo as réplicas e partições para um nível baixo que ainda forneça um desempenho aceitável e [conformidade com o SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

Uma alavanca para reduzir os custos é escolher um nível com uma taxa por hora mais baixa. As tarifas por hora do S1 são menores do que as do S2 ou do S3. Supondo que você provisiona um serviço que visa a extremidade inferior do suas projeções de carga, se você excede a capacidade do serviço, você pode criar um segundo serviço em camadas maiores, recompilar os índices em um segundo serviço e, em seguida, excluir o primeiro. 

Se você já fez planejamento de capacidade para servidores locais, você sabe que é comum "comprar a mais" para poder lidar com o crescimento projetado. Mas, com um serviço de nuvem, você pode buscar uma economia de custo de forma agressiva porque você não está limitado a uma compra específica. Você sempre pode mudar para um serviço de nível superior quando o atual é insuficiente.

### <a name="capacity-drill-down"></a>Capacidade de fazer drill down

No Azure Search, a capacidade é estruturada como *réplicas* e *partições*. 

+ As réplicas são instâncias do serviço de pesquisa, onde cada réplica hospeda uma cópia com balanceamento de carga de um índice. Por exemplo, um serviço com 6 réplicas tem 6 cópias de cada índice carregado no serviço. 

+ As partições armazenam índices e automaticamente dividem os dados pesquisados: duas partições de divisão para o seu índice na metade, três participações em três e assim por diante. Em termos de capacidade, o *tamanho da partição* é o principal recurso de diferenciação em camadas.

> [!NOTE]
> Todos os **Standard** e **otimizados para armazenamento** camadas suporte [partições e réplicas de combinações flexíveis](search-capacity-planning.md#chart) para que você possa [seu sistema para velocidade de peso ou armazenamento](search-performance-optimization.md) alterando o saldo. **Básico** oferece até três réplicas para alta disponibilidade, mas possui apenas uma partição. As camadas **Gratuitas** não fornecem recursos dedicados: os recursos de computação são compartilhados por vários assinantes.

### <a name="more-about-service-limits"></a>Mais informações sobre limites de serviço

Serviços de recursos de host, como índices, indexadores e assim por diante. Cada camada impõe [limites de serviço](search-limits-quotas-capacity.md) na quantidade de recursos que você pode criar. Como tal, uma tampa no número de índices (e outros objetos) é o segundo recurso de diferenciação através de camadas. Ao revisar cada opção no portal, observe os limites no número de índices. Outros recursos, como indexadores, fontes de dados e conhecimentos, são vinculados aos limites de índice.

## <a name="consumption-patterns"></a>Padrões de consumo

A maioria dos clientes começam com o **livre** de serviço, que eles mantêm indefinidamente e, em seguida, escolha uma da **padrão** ou **otimizados para armazenamento** camadas para desenvolvimento sério ou cargas de trabalho de produção. 

![Camadas de pesquisa do Azure](./media/search-sku-tier/tiers.png "camadas de preços de pesquisa do Azure")

Em ambos os lados, **Básico** e **HD S3** existem para padrões de consumo importante mas atípicos. **Básico** é para cargas de trabalho de produção de pequeno porte: oferece SLA, recursos dedicados, alta disponibilidade, mas armazenamento modesto, chegando ao total de 2 GB. Esta camada foi projetada para clientes que estão consistentemente em capacidade disponível utilizada. Na extremidade oposta, o **S3 HD** para cargas de trabalho típica de ISVs, parceiros, [soluções multilocatárias](search-modeling-multitenant-saas-applications.md), ou qualquer configuração de chamada para um grande número de índices pequenos. Geralmente é evidente quando a camada **Básico** ou **S3 HD** é a mais adequada, mas se você desejar confirmar que você pode postar em [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o Azure Support](https://azure.microsoft.com/support/options/) para obter mais orientações.

Mudar o foco para as camadas padrão usadas com mais frequência, **S1-S3** são uma progressão de níveis crescentes de capacidade, com pontos de inflexão no tamanho da partição e os limites máximos em números de índices, indexadores e recursos de resultado:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| tamanho da partição|  25 GB | 100 GB | 200 GB |  |  |  |  |
| limites de índice e indexador| 50 | 200 | 200 |  |  |  |  |

**S1** é uma opção comum quando os recursos dedicados e várias partições se tornam uma necessidade. Com partições de 25 GB para até 12 partições, o limite por serviço em **S1** é 300 GB total que você aumenta partições sobre réplicas (consulte [Alocar partições e réplicas](search-capacity-planning.md#chart) para mais composições balanceadas)

Páginas de portal e preços colocam o foco no tamanho de partição e de armazenamento, mas para cada camada, todos os recursos de computação (capacidade do disco, velocidade, CPUs) crescem de forma linear com preço. Uma réplica **S2** é mais rápida que **S1**, e **S3** é mais rápido que **S2**. As camadas **S3** quebram o padrão de preços de computação geralmente linear com desproporcionalmente mais rápida de E/S. Se você antecipar E/S como o afunilamento, um **S3** fornece muito mais IOPS de níveis mais baixos.

**S3** e **S3 HD** D contam com infraestrutura de alta capacidade idênticas, mas cada um deles atinge seu limite máximo de maneiras diferentes. O **S3** tem como alvo um número menor de índices muito grandes. Como tal, seu limite máximo está vinculado a recursos (2,4 TB para cada serviço). O **S3 HD** destina-se um grande número de índices muito pequenos. Com 1.000 índices, o **S3 HD** atinge os limites na forma de restrições de índice. Se você for um cliente do **S3 HD** que requer mais de 1.000 índices, entre em contato com o Suporte da Microsoft para obter informações sobre como proceder.

> [!NOTE]
> Anteriormente, os limites de documentos eram uma preocupação, mas não são mais aplicáveis para novos serviços. Para obter mais informações sobre as condições sob as quais os limites de documento ainda se aplicam, confira [Limites de serviço: limites de documentos](search-limits-quotas-capacity.md#document-limits).
>

Camadas de armazenamento otimizado, **L1 L2**, são ideais para aplicativos com requisitos de dados grandes, mas um número relativamente baixo de usuários finais em que a minimizar a latência da consulta não é a principal prioridade.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| tamanho da partição|  1 TB | 2 TB |  |  |  |  |  |
| limites de índice e indexador| 10 | 10 |  |  |  |  |  |

*L2* oferece duas vezes a capacidade geral de armazenamento para um *L1*.  Escolha seu tipo com base na quantidade máxima de dados que você acha que precisa de seu índice.  O *L1* camada particiona o dimensionamento para cima em incrementos de 1 TB para um máximo de 12 TB, embora o *L2* aumentar por 2 TBs por partição até um máximo de 24 TB.

## <a name="evaluate-capacity"></a>Avaliação da capacidade

A capacidade e os custos de execução do serviço andam de lado a lado. As camadas limitam-se em dois níveis (armazenamento e recursos), portanto você deve considerar ambos porque qualquer um que você alcançar primeiro é o limite efetivo. 

Requisitos de negócios determinarem normalmente o número de índices, que será necessário. Por exemplo, um índice global para um repositório grande de documentos, ou talvez vários índices com base no setor de negócios, aplicativo ou região.

Para determinar o tamanho de um índice, você precisa [compilar um](search-create-index-portal.md). A estrutura de dados no Azure Search é principalmente um [índice invertido](https://en.wikipedia.org/wiki/Inverted_index), que tem características diferentes dos dados de origem. Para um índice invertido, tamanho e complexidade são determinados pelo conteúdo, não necessariamente a quantidade de dados que você alimenta. Uma fonte de dados grande com redundância massiva pode resultar em um índice menor do que um conjunto de dados menor que contém conteúdo altamente variável. Como tal, é raramente possível inferir o tamanho de índice com base no tamanho do conjunto de dados original.

> [!NOTE] 
> Embora a estimativa das necessidades futuras de armazenamento e de índices envolva um pouco de adivinhação, realmente vale a pena fazê-la. Se a capacidade de uma camada for muito baixa, você precisará provisionar um novo serviço na camada superior e [recarregar os índices](search-howto-reindex.md). Não há nenhuma atualização in-loco do mesmo serviço de uma SKU para outra.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Etapa 1: Desenvolver estimativas aproximadas, usando a camada gratuita

Uma abordagem para calcular a capacidade é iniciar com a camada **Livre**. Lembre-se de que o serviço **Livre** oferece até 3 índices, 50 MB de armazenamento e de indexação de 2 minutos. Ele pode ser um desafio para estimar um tamanho de índice projetado com essas restrições, mas o exemplo a seguir ilustra uma abordagem:

+ [Cria um serviço grátis](search-create-service-portal.md)
+ Preparar um conjunto de dados pequeno, representativo (supondo documentos de cinco il e tamanho de modelo dez por cento)
+ [Criar um índice inicial](search-create-index-portal.md) e anote seu tamanho no portal (supondo que 30 MB)

Supondo que o exemplo foi representante e dez por cento da fonte de dados inteiro, um índice de 30 MB se tornará aproximadamente 300 MB se todos os documentos forem indexados. Armado com esse número preliminar, você pode dobrar esse valor para o orçamento para dois índices (desenvolvimento e produção), para um total de 600 MB em requisitos de armazenamento. Isso é facilmente atendido pela camada **Básico**, portanto você deve iniciar de lá.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Etapa 2: Desenvolver estimativas refinadas usando uma camada faturável

Alguns clientes preferem iniciar com recursos dedicados que podem acomodar amostragem maior e tempos de processamento, e em seguida desenvolve estimativas realistas da quantidade do índice, tamanho e volumes de consulta durante o desenvolvimento. Inicialmente, um serviço é provisionado com base em uma estimativa melhor alternativa e, em seguida, como o projeto de desenvolvimento amadurece, as equipes geralmente sabem se o serviço existente está acima ou abaixo de capacidade para cargas de trabalho de produção projetadas. 

1. [Examine os limites de serviço em cada camada](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) para determinar se os níveis mais baixos podem dar suporte a quantidade de índices que você precisa. Entre as camadas **Básico**-**S1**-**S2**, os limites de índice são 50-15-200, respectivamente.  O **otimizados para armazenamento** camada tem um limite de 10 índices, pois se trata de designer para dar suporte a um número baixo de índices muito grandes.

1. [Criar um serviço em uma camada faturável](search-create-service-portal.md):

    + Iniciar pouca em **Básico** ou **S1** se você estiver no início de sua curva de aprendizado.
    + Iniciar alto, em **S2** ou até mesmo **S3**, se indexação de larga escala e cargas de consulta são auto-confiantes.
    + Armazenamento otimizado, no **L1** ou **L2**, se você estiver indexando uma grande quantidade de dados e carga de consulta é relativamente baixa, como um aplicativo de negócios internos.

1. [Criar um índice inicial](search-create-index-portal.md) para determinar como a fonte de dados traduz para um índice. Essa é a única maneira de estimar o tamanho do índice.

1. [Monitorar armazenamento, limites de serviço, volume de consulta e latência](search-monitor-usage.md) no portal. O portal mostra consultas por segunda, limitadas consultas e latência de pesquisa; todos os quais podem ajudá-lo a decidir se você tiver selecionado a camada certa. Além de métricas de portal, você pode configurar monitoramento profundo, como a análise de clickthrough, habilitando [análise de tráfego de pesquisa](search-traffic-analytics.md). 

O número de índice e o tamanho são igualmente relevantes para sua análise porque os limites máximos são alcançados por meio de utilização total de armazenamento (partições) ou limites máximo de recursos (índices, indexadores e assim por diante), o que ocorrer primeiro. O portal ajuda a manter o controle de ambos, mostrando o uso atual e os limites máximos lado a lado na página Visão geral.

> [!NOTE]
> Os requisitos de armazenamento podem ser excessivos se os documentos contiverem dados estranhos. Idealmente, os documentos contêm apenas os dados necessários para a experiência de pesquisa. Dados binários não são pesquisáveis e devem ser armazenados separadamente (talvez em um armazenamento de blobs ou tabela do Azure) com um campo no índice para manter uma URL de referência aos dados externos. O tamanho máximo de um documento individual é de 16 MB (ou menos, se você estiver carregando em massa vários documentos em uma solicitação). [Os limites de serviço no Azure Search](search-limits-quotas-capacity.md) tem mais informações.
>

**Considerações sobre volume de consultas**

A QPS (consultas por segundo) é uma medida que obtém importância durante o ajuste de desempenho, mas geralmente não é uma consideração de camada, a menos que você espera um volume alto de consulta desde o início.

As camadas padrão podem fornecer um equilíbrio entre réplicas para partições, dando suporte a retorno mais rápido de consulta por meio de réplicas adicionais para carregar as partições adicionais e balanceamento para processamento paralelo. Você pode ajustar o desempenho depois que o serviço é fornecido.

Os clientes que esperam volumes de consulta sustentada forte desde o início devem considerar maior **Standard** camadas, apoiadas por um hardware mais potente. Você pode colocar as partições e réplicas offline ou até mesmo alternar para um serviço de camada inferior, se os volumes de consulta não materializarem. Para obter mais informações sobre como calcular a taxa de transferência de consulta, consulte [desempenho do Azure Search e otimização](search-performance-optimization.md).

O armazenamento com otimização de camadas lean em direção a cargas de trabalho de dados grandes, que dão suporte a mais geral armazenamento de índice disponível, onde os requisitos de latência de consulta são um pouco reduzidos.  Réplicas adicionais ainda devem ser utilizadas para carregar as partições de balanceamento e adicionais para processamento paralelo. Você pode ajustar o desempenho depois que o serviço é fornecido.

**Contratos de nível de serviço**

A camada **Gratuita** não vêm com [SLAs (contratos de nível de serviço)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Para todas as camadas faturáveis, os SLAs entram em vigor quando você provisiona redundância suficiente para o serviço. Duas ou mais réplicas são necessárias para o SLA de consulta (leitura). Três ou mais réplicas são necessárias para consulta e indexação do SLA (leitura-gravação). O número de partições não é uma consideração de SLA. 

## <a name="tips-for-tier-evaluation"></a>Dicas para avaliação de camada

+ Saiba como criar índices eficientes e as metodologias de atualização são o mínimo de impacto. Nós recomendamos [análise de tráfego de pesquisa](search-traffic-analytics.md) para as informações obtidas na atividade de consulta.

+ Permitir que as métricas criem uma solução alternativa para consultas e coletar dados sobre padrões de uso (consultas durante o horário comercial, a indexação de horário de pico) e usar esses dados para informar as decisões de provisionamento de serviço futuro. Embora não seja prático fazê-lo em uma cadência horária ou diária, você pode ajustar dinamicamente partições e recursos para acomodar alterações planejadas em volumes de consulta ou alterações não planejadas, mas prolongadas se os níveis se mantiverem por tempos suficientemente longos para garantir a execução de uma ação.

+ Lembre-se de que a única desvantagem de provisionamento é que talvez você precise subdividir um serviço se os requisitos reais forem maiores que o previsto. Para evitar a interrupção do serviço, crie um novo serviço na mesma assinatura em uma camada superior e execute-o lado a lado até todos os aplicativos e solicitações de novo ponto de extremidade de destino.

## <a name="next-steps"></a>Próximas etapas

Inicie com uma camada **Gratuita** crie um índice inicial com um subconjunto de seus dados para entender suas características. A estrutura de dados no Azure é um índice invertido, onde o tamanho e complexidade de um índice invertido são determinados pelo conteúdo. Lembre-se de que o conteúdo altamente redundante tende a resultar em um índice menor que o conteúdo altamente irregular. Como tal, são as características de conteúdo e não o tamanho do conjunto de dados que determinam os requisitos de armazenamento de índice.

Depois que você tiver uma ideia inicial do tamanho do índice [provisionar um serviço faturável](search-create-service-portal.md) em uma das camadas discutidas neste artigo, tanto **básica**, **padrão**, ou **Otimizados para armazenamento** camada. Relaxe quaisquer restrições artificiais no dimensionamento de dados e [recriar o índice](search-howto-reindex.md) para incluir todos os dados desejados, na verdade, ser pesquisada.

[Alocar partições e réplicas](search-capacity-planning.md) conforme necessário para obter o desempenho e escalabilidade que você precisa.

Se o desempenho e a capacidade são permitidos, termine. Caso contrário, crie novamente um serviço de pesquisa em uma outra camada mais estreitamente alinhada a suas necessidades.

> [!NOTE]
> Para obter ajuda com suas perguntas, postar [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) ou [entre em contato com o Azure Support](https://azure.microsoft.com/support/options/).
