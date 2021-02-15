---
title: Dimensionamento em razão de desempenho
titleSuffix: Azure Cognitive Search
description: Aprenda técnicas e práticas recomendadas para ajustar o desempenho de Pesquisa Cognitiva do Azure e configurar a escala ideal.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: b7c71524dc40f7eabd5ff86ee21c8197acfae1a3
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102284"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Escala para desempenho no Azure Pesquisa Cognitiva

Este artigo descreve as práticas recomendadas para cenários avançados com requisitos sofisticados de escalabilidade e disponibilidade.

## <a name="start-with-baseline-numbers"></a>Iniciar com números de linha de base

Antes de realizar um esforço de implantação maior, certifique-se de que você sabe o que é uma carga de consulta típica. As diretrizes a seguir podem ajudá-lo a chegar em números de consulta de linha de base.

1. Escolher uma latência de destino (ou a quantidade máxima de tempo) que uma solicitação de pesquisa normal deve demorar para ser concluída.

1. Crie e teste uma carga de trabalho real em relação ao serviço de pesquisa com um conjunto de dados realista para medir essas taxas de latência.

1. Comece com um número baixo de consultas por segundo (QPS) e, em seguida, Aumente gradualmente o número executado no teste até que a latência da consulta fique abaixo do destino predefinido. Este é um parâmetro de comparação importante para ajudar você a planejar a escala à medida que o uso de seu aplicativo cresce.

1. Sempre que possível, reutilize as conexões HTTP. Se você estiver usando o SDK do .NET Pesquisa Cognitiva do Azure, isso significa que você deve reutilizar uma instância do ou [SearchClient](/dotnet/api/azure.search.documents.searchclient) e, se estiver usando a API REST, deverá reutilizar um único HttpClient.

1. Varie a substância de solicitações de consulta para que a pesquisa ocorra em diferentes partes do índice. A variação é importante porque, se você executar continuamente as mesmas solicitações de pesquisa, o cache de dados começará a fazer com que o desempenho seja melhor do que pode com um conjunto de consultas mais distintas.

1. Varie a estrutura das solicitações de consulta para que você obtenha diferentes tipos de consultas. Nem todas as consultas de pesquisa são executadas no mesmo nível. Por exemplo, uma pesquisa de documento ou sugestão de pesquisa é normalmente mais rápida do que uma consulta com um número significativo de facetas e filtros. A composição de teste deve incluir várias consultas, em aproximadamente as mesmas proporções que você esperaria na produção.  

Ao criar essas cargas de trabalho de teste, há algumas características do Azure Pesquisa Cognitiva para ter em mente:

+ É possível sobrecarregar seu serviço enviando por push muitas consultas de pesquisa de uma só vez. Quando isso acontecer, você verá códigos de resposta HTTP 503. Para evitar um 503 durante o teste, comece com vários intervalos de solicitações de pesquisa para ver as diferenças nas taxas de latência à medida que você adiciona mais solicitações de pesquisa.

+ O Azure Pesquisa Cognitiva não executa tarefas de indexação em segundo plano. Se o seu serviço tratar as cargas de trabalho de consulta e indexação simultaneamente, leve isso em conta introduzindo trabalhos de indexação em seus testes de consulta ou explorando as opções para executar trabalhos de indexação fora do horário de pico.

> [!Tip]
> Você pode simular uma carga de consulta realista usando ferramentas de teste de carga. Experimente o [teste de carga com o Azure DevOps](/azure/devops/test/load-test/get-started-simple-cloud-load-test) ou use uma dessas [alternativas](/azure/devops/test/load-test/overview#alternatives).

## <a name="scale-for-high-query-volume"></a>Escala para alto volume de consulta

Um serviço é sobrecarregado quando as consultas levam muito tempo ou quando o serviço inicia a remoção de solicitações. Se isso acontecer, você poderá resolver o problema de uma das duas maneiras:

+ **Adicionar réplicas**  

  Cada réplica é uma cópia dos seus dados, permitindo que o serviço carregue solicitações de balanceamento em relação a várias cópias.  Todo o balanceamento de carga e a replicação de dados são gerenciados pelo Azure Pesquisa Cognitiva e você pode alterar o número de réplicas alocadas para o serviço a qualquer momento. É possível alocar até 12 réplicas em um serviço de pesquisa Standard e três réplicas em um serviço de pesquisa Básico. As réplicas podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).

+ **Criar um novo serviço em uma camada superior**  

  O Pesquisa Cognitiva do Azure vem em [várias camadas](https://azure.microsoft.com/pricing/details/search/) e cada uma delas oferece níveis diferentes de desempenho. Em alguns casos, você pode ter tantas consultas que a camada em que você está em não pode fornecer um retorno suficiente, mesmo quando as réplicas são maximizadas. Nesse caso, considere mudar para uma camada de desempenho superior, como a camada Standard S3, projetada para cenários com grandes quantidades de documentos e cargas de trabalho de consulta extremamente altas.

## <a name="scale-for-slow-individual-queries"></a>Dimensionar para consultas individuais lentas

Outro motivo para taxas de alta latência é uma única consulta demorando muito para ser concluída. Nesse caso, a adição de réplicas não ajudará. Duas opções possíveis que podem ajudar incluem o seguinte:

+ **Aumentar partições**

  Uma partição divide os dados em recursos de computação extras. Duas partições dividem os dados na metade, uma terceira partição divide-os em terços e assim por diante. Um efeito colateral positivo é que as consultas mais lentas às vezes são executadas mais rapidamente devido à computação paralela. Observamos a paralelização em consultas de baixa seletividade, como consultas que correspondem a muitos documentos ou facetas que fornecem contagens em um grande número de documentos. Como uma computação significativa é necessária para pontuar a relevância dos documentos ou para contar os números de documentos, adicionar partições extras ajuda as consultas a serem concluídas com mais rapidez.  
   
  Pode haver no máximo 12 partições no serviço de pesquisa padrão e uma partição no serviço de pesquisa básico. As partições podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).

+ **Limitar campos de cardinalidade alta**

  Um campo de alta cardinalidade consiste em um campo de facetable ou filtrável que tem um número significativo de valores exclusivos e, como resultado, consome recursos significativos ao calcular os resultados. Por exemplo, definir uma ID do produto ou um campo de descrição como facetable/filtrável conta como alta cardinalidade porque a maioria dos valores de documento para documento são exclusivos. Sempre que for possível, limite o número de campos de alta cardinalidade.

+ **Aumentar a camada de pesquisa**  

  Mover para uma camada mais alta de Pesquisa Cognitiva do Azure pode ser outra maneira de melhorar o desempenho de consultas lentas. Cada camada mais alta fornece CPUs mais rápidas e mais memória, as quais têm um impacto positivo no desempenho da consulta.

## <a name="scale-for-availability"></a>Escala para disponibilidade

As réplicas não apenas ajudam a reduzir a latência de consulta, mas também podem permitir alta disponibilidade. Com uma única réplica, você deve esperar um tempo de inatividade periódico devido à reinicialização do servidor após atualizações de software ou a outros eventos de manutenção. Como resultado, é importante considerar se seu aplicativo exige alta disponibilidade de pesquisas (consultas), bem como gravações (eventos de indexação). O Azure Pesquisa Cognitiva oferece opções de SLA em todas as ofertas de pesquisa pagas com os seguintes atributos:

+ Duas réplicas para alta disponibilidade de cargas de trabalho somente leitura (consultas)

+ Três ou mais réplicas para alta disponibilidade de cargas de trabalho de leitura/gravação (consultas e indexação)

Para obter mais detalhes sobre isso, visite o [contrato de nível de serviço de pesquisa cognitiva do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Como as réplicas são cópias de seus dados, ter várias réplicas permite que o Azure Pesquisa Cognitiva faça reinicializações e manutenção do computador em uma réplica, enquanto a execução da consulta continua em outras réplicas. Por outro lado, se você tirar as réplicas de longe, incorrerá em degradação do desempenho da consulta, supondo que essas réplicas fossem um recurso subutilizado.

<a name="availability-zones"></a>

### <a name="availability-zones"></a>Zonas de Disponibilidades

[Zonas de disponibilidade](https://docs.microsoft.com/azure/availability-zones/az-overview) dividir os data centers de uma região em grupos de locais físicos distintos para fornecer alta disponibilidade, dentro da mesma região. Por Pesquisa Cognitiva, as réplicas individuais são as unidades para atribuição de zona. Um serviço de pesquisa é executado dentro de uma região; suas réplicas são executadas em diferentes zonas.

Você pode utilizar Zonas de Disponibilidade com o Pesquisa Cognitiva do Azure adicionando duas ou mais réplicas ao serviço de pesquisa. Cada réplica será colocada em uma zona de disponibilidade diferente dentro da região. Se você tiver mais réplicas do que Zonas de Disponibilidade, as réplicas serão distribuídas entre Zonas de Disponibilidade o mais uniforme possível.

Atualmente, o Azure Pesquisa Cognitiva dá suporte a Zonas de Disponibilidade para serviços de camada Standard ou de pesquisa mais alta que foram criados em uma das seguintes regiões:

+ Leste da Austrália (criado em 30 de janeiro de 2021 ou posterior)
+ Centro do Canadá (criado em 30 de janeiro de 2021 ou posterior)
+ EUA Central (criado em 4 de dezembro de 2020 ou mais recente)
+ Leste dos EUA 2 (criado em 30 de janeiro de 2021 ou posterior)
+ França central (criado em 23 de outubro de 2020 ou posterior)
+ Leste do Japão (criado em 30 de janeiro de 2021 ou posterior)
+ Europa Setentrional (criado em 28 de janeiro de 2021 ou posterior)
+ Ásia Oriental do Sul (criado em 31 de janeiro de 2021 ou mais recente)
+ Sul do Reino Unido (criado em 30 de janeiro de 2021 ou posterior)
+ Europa Ocidental (criado em 29 de janeiro de 2021 ou posterior)
+ Oeste dos EUA 2 (criado em 30 de janeiro de 2021 ou posterior)

Zonas de Disponibilidade não têm impacto sobre o [contrato de nível de serviço de pesquisa cognitiva do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/). Você ainda precisa de três ou mais réplicas para a alta disponibilidade da consulta.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>Dimensionar para cargas de trabalho distribuídas geograficamente e redundância geográfica

Para cargas de trabalho distribuídas geograficamente, os usuários que estão localizados longe da data center do host terão taxas de latência mais altas. Uma mitigação é provisionar vários serviços de pesquisa em regiões com mais proximidade com esses usuários.

Atualmente, o Azure Pesquisa Cognitiva não fornece um método automatizado de replicação geográfica de índices de Pesquisa Cognitiva do Azure entre regiões, mas há algumas técnicas que podem ser usadas para tornar esse processo simples de implementar e gerenciar. Elas serão descritas nas próximas seções.

O objetivo de um conjunto de serviços de pesquisa distribuído geograficamente é ter dois ou mais índices disponíveis em duas ou mais regiões, em que um usuário é encaminhado para o serviço de Pesquisa Cognitiva do Azure que fornece a menor latência, como visto neste exemplo:

   ![Tabela de referência cruzada de serviços por região][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>Manter os dados sincronizados em vários serviços

Há duas opções para manter os serviços de pesquisa distribuída sincronizados, que consistem em usar o [indexador pesquisa cognitiva do Azure](search-indexer-overview.md) ou a API de envio por push (também conhecida como [API REST do Azure pesquisa cognitiva](/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Usar indexadores para atualizar o conteúdo em vários serviços

Se você já estiver usando o indexador em um serviço, poderá configurar um segundo indexador em um segundo serviço para usar o mesmo objeto de fonte de dados, obtendo dados do mesmo local. Cada serviço em cada região tem seu próprio indexador e um índice de destino (o índice de pesquisa não é compartilhado, o que significa que os dados estão duplicados), mas cada indexador faz referência à mesma fonte de dados.

Aqui está um Visual de alto nível da aparência dessa arquitetura.

   ![Única fonte de dados com o indexador distribuído e combinações de serviço][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Usar APIs REST para enviar atualizações de conteúdo por push em vários serviços

Se você estiver usando a API REST do Azure Pesquisa Cognitiva para [enviar conteúdo por push em seu índice de pesquisa cognitiva do Azure](/rest/api/searchservice/update-index), você poderá manter seus vários serviços de pesquisa sincronizados enviando alterações a todos os serviços de pesquisa sempre que uma atualização for necessária. Em seu código, certifique-se de lidar com casos em que uma atualização para um serviço de pesquisa falha, mas é bem-sucedida para outros serviços de pesquisa.

## <a name="leverage-azure-traffic-manager"></a>Aproveite o Gerenciador de tráfego do Azure

O [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md) permite que você encaminhe solicitações para vários sites geograficamente localizados que são então apoiados por vários serviços de pesquisa. Uma vantagem do Gerenciador de tráfego é que ele pode investigar o Pesquisa Cognitiva do Azure para garantir que ele esteja disponível e rotear os usuários para serviços de pesquisa alternativos em caso de tempo de inatividade. Além disso, se você estiver roteando solicitações de pesquisa por meio de sites do Azure, o Gerenciador de tráfego do Azure permite balancear a carga de casos em que o site está ativo, mas não o Azure Pesquisa Cognitiva. Veja um exemplo de uma arquitetura que aproveita o Gerenciador de Tráfego.

   ![Tabela de referência cruzada de serviços por região, com o Gerenciador de tráfego central][3]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os tipos de preço e limites de serviços para cada um, confira [limites de serviço](search-limits-quotas-capacity.md). Consulte [planejar a capacidade](search-capacity-planning.md) para saber mais sobre combinações de partição e réplica.

Para obter uma discussão sobre o desempenho e as demonstrações das técnicas discutidas neste artigo, Assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png