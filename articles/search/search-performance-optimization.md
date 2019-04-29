---
title: Estratégias de implantação e práticas recomendadas para otimizar o desempenho - Azure Search
description: Aprenda técnicas e práticas recomendadas para ajuste de desempenho do Azure Search e configuração do dimensionamento ideal.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283536"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>Estratégias de implantação e práticas recomendadas para otimizar o desempenho no Azure Search

Este artigo descreve as práticas recomendadas para cenários avançados com requisitos sofisticados de escalabilidade e disponibilidade. 

## <a name="develop-baseline-numbers"></a>Desenvolver os números de linha de base
Ao otimizar o desempenho da pesquisa, você deve se concentrar na redução do tempo de execução de consulta. Para fazer isso, você precisa saber a aparência de uma carga de consulta típica. As diretrizes a seguir podem ajudá-lo a chegar a números de consulta de linha de base.

1. Escolher uma latência de destino (ou a quantidade máxima de tempo) que uma solicitação de pesquisa normal deve demorar para ser concluída.
2. Criar e testar uma carga de trabalho real com o serviço de pesquisa com um conjunto de dados realista a fim de medir essas taxas de latência.
3. Comece com um número baixo de consultas por segundo (QPS) e aumentar gradualmente o número executado no teste até que a latência da consulta fique abaixo a latência de destino definida. Este é um parâmetro de comparação importante para ajudar você a planejar a escala à medida que o uso de seu aplicativo cresce.
4. Sempre que possível, reutilize as conexões HTTP. Se você estiver usando o SDK .NET do Azure Search, significa que você deve reutilizar uma instância ou instância do [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) , e se você estiver usando a API REST, reutilize um único HttpClient.
5. Variar a substância de solicitações de consulta para que a pesquisa ocorre em diferentes partes do seu índice. Variação é importante porque se você executar continuamente as mesmas solicitações de pesquisa, o cache de dados será iniciado tornar o desempenho parecer melhor que ele talvez com uma consulta mais diferentes definido.
6. Variar a estrutura de solicitações de consulta para que você obtenha os diferentes tipos de consultas. Nem toda consulta de pesquisa executa no mesmo nível. Por exemplo, uma sugestão de pesquisa ou pesquisa de documento é normalmente mais rápida do que uma consulta com um número significativo de facetas e filtros. Composição de teste deve incluir várias consultas, aproximadamente as mesmas taxas conforme o esperado na produção.  

Ao criar essas cargas de trabalho de teste, tenha em mente algumas características do Azure Search:

+ É possível sobrecarregar seu serviço por push a um número excessivo de consultas de pesquisa ao mesmo tempo. Quando isso acontecer, você verá códigos de resposta HTTP 503. Para evitar um 503 durante o teste, começar com vários intervalos de solicitações de pesquisa para ver as diferenças nas taxas de latência conforme você adiciona mais solicitações de pesquisa.

+ O Azure Search não executa tarefas de indexação em segundo plano. Se seu serviço manipula a consulta e indexação cargas de trabalho simultaneamente, leve isso em conta, qualquer um dos trabalhos de indexação em seus testes de consulta introduzindo ou Explorando as opções para executar trabalhos de indexação durante horários de pico.

> [!NOTE]
> O [Teste de Carga no Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) é uma boa maneira de executar os testes de parâmetros de comparação que permitem a você executar solicitações HTTP da forma necessária para executar consultas no Azure Search e permite a paralelização de solicitações.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>Dimensionamento para o volume de consulta altas e solicitações limitadas
Quando você está recebendo muitas solicitações limitadas ou ultrapassar suas taxas de latência de destino de uma carga de consulta maior, você pode procurar para diminuir essas taxas de latência de uma das duas maneiras:

1. **Aumentar as réplicas:**  Uma réplica é semelhante a uma cópia dos seus dados, e permite que o Azure Search equilibre as solicitações nas várias cópias.  Todo o balanceamento de carga e a replicação de dados entre as réplicas é gerenciado pelo Azure Search, e você pode alterar o número de réplicas alocadas para o serviço a qualquer momento.  É possível alocar até 12 réplicas em um serviço de pesquisa Standard e três réplicas em um serviço de pesquisa Básico. As réplicas podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).
2. **Aumentar a Camada de Pesquisa:**  O Azure Search vem em [várias camadas](https://azure.microsoft.com/pricing/details/search/), e cada uma dessas camadas oferece níveis diferentes de desempenho.  Em alguns casos, você pode ter tantas consultas que a camada na qual você está não consegue fornecer taxas latência suficientemente baixas, mesmo quando as réplicas são maximizadas. Nesse caso, você talvez queira considerar aproveitar uma das camadas mais altas de pesquisa, como a camada S3 do Azure Search que é adequada para cenários com um grande número de documentos e cargas de trabalho extremamente altas.

## <a name="scaling-for-slow-individual-queries"></a>Colocação em escala para consultas individuais lentas
Outro motivo para taxas de alta latência é uma única consulta demorar muito tempo para concluir. Nesse caso, não ajudará a adição de réplicas. Duas opções possíveis opções que podem ajudar incluem o seguinte:

1. **Aumentar Partições** Uma partição é um mecanismo para dividir os dados entre recursos extras. Adicionar uma segunda partição divide dados em dois, uma terceira partição divide em três e assim por diante. Um efeito colateral positivo é que as consultas mais lentas, às vezes, um desempenho mais rápido devido a computação paralela. Observamos paralelização de consultas de baixa seletividade, como consultas que correspondam a muitos documentos ou facetas para fornecer contagens em um grande número de documentos. Desde que o cálculo significativo é necessária para pontuar a relevância dos documentos ou contar o número de documentos, adição de partições extras ajuda a consultas concluídas mais rapidamente.  
   
   Pode haver no máximo 12 partições no serviço de pesquisa Standard, e uma partição no serviço de pesquisa Básico.  As partições podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).

2. **Limitar campos de alta cardinalidade:** Um campo de alta cardinalidade consiste em um campo facetável ou filtrável que tem um número significativo de valores exclusivos e como resultado, consome recursos significativos ao calcular os resultados. Por exemplo, definir um campo de ID do produto ou descrição como facetável/filtrável é computada como alta cardinalidade porque a maioria dos valores de documento ao documento é exclusiva. Sempre que for possível, limite o número de campos de alta cardinalidade.

3. **Aumentar a Camada de Pesquisa:**  Mudar para uma camada mais alto do Azure Search pode ser outra maneira de melhorar o desempenho de consultas lentas. Cada camada superior fornece o mais rápido CPUs e mais memória, que tem um impacto positivo no desempenho da consulta.

## <a name="scaling-for-availability"></a>Dimensionamento para disponibilidade
As réplicas não apenas ajudam a reduzir a latência da consulta, mas também permitem a alta disponibilidade. Com uma única réplica, você deve esperar um tempo de inatividade periódico devido à reinicialização do servidor após atualizações de software ou a outros eventos de manutenção.  Como resultado, é importante considerar se seu aplicativo exige alta disponibilidade de pesquisas (consultas), bem como gravações (eventos de indexação). O Azure Search oferece opções de SLA em todas as ofertas de pesquisa pagas, com os seguintes atributos:

* Duas réplicas para alta disponibilidade das cargas de trabalho somente leitura (consultas)
* Três ou mais réplicas para alta disponibilidade das cargas de trabalho de leitura/gravação (consultas e indexação)

Para obter mais detalhes sobre isso, visite o [Contrato de Nível de Serviço do Azure Search](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Como as réplicas são cópias de seus dados, ter várias réplicas permite que o Azure Search machine reinicializações e manutenção com base em uma réplica, permitindo que a execução da consulta continuar se houver outras réplicas. Por outro lado, se você usar réplicas imediatamente, você incorrerá em degradação de desempenho de consulta, supondo que as réplicas foram um recurso do subutilizada.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>Colocação em escala para cargas de trabalho distribuídas geograficamente e a redundância geográfica
Para cargas de trabalho distribuídas geograficamente, os usuários que estão localizados longe do data center do Azure Search de hospedagem terão taxas mais altas de latência. Uma mitigação é provisionar vários serviços de pesquisa em regiões com próximas a esses usuários. No momento, o Azure Search não fornece um método automatizado de replicação geográfica de índices do Azure Search entre regiões, mas há algumas técnicas que podem ser usadas para tornar esse processo simples de implementar e gerenciar. Elas serão descritas nas próximas seções.

O objetivo de um conjunto distribuído geograficamente de serviços de pesquisa é ter dois ou mais índices disponíveis em dois ou mais regiões em que um usuário é roteado para o serviço de Azure Search que fornece a menor latência, como visto neste exemplo:

   ![Tabela de referência cruzada de serviços por região][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Manter os dados sincronizados em vários serviços de Azure Search
Há duas opções para manter seus serviços de pesquisa distribuídos em sincronia, que são o uso do [Indexador do Azure Search](search-indexer-overview.md) ou a API Push (também conhecida como a [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>Usar indexadores para atualizar conteúdo em vários serviços

Se você já estiver usando o indexador em um serviço, você pode configurar um indexador de segundo em um segundo serviço para usar o mesmo objeto de fonte de dados, extraindo dados do mesmo local. Cada serviço em cada região tem seu próprio indexador e um índice de destino (o corpus de pesquisa não é compartilhado, que significa que os dados sejam duplicados), mas cada indexador faz referência a mesma fonte de dados.

Aqui está um visual de alto nível de como seria a aparência dessa arquitetura.

   ![Única fonte de dados com o indexador distribuído e combinações de serviço][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>Use as APIs de REST para enviar por push atualizações de conteúdo em vários serviços
Se você estiver usando a API REST do Azure Search para [enviar por push o conteúdo em seu índice de Azure Search](https://docs.microsoft.com/rest/api/searchservice/update-index), você pode manter em sincronia seus vários serviços de pesquisa por push as alterações a todos os serviços de pesquisa, sempre que uma atualização é necessária. Em seu código, certifique-se lidar com casos em que uma atualização para um serviço de pesquisa falha, mas falha por outros serviços de pesquisa.

## <a name="leverage-azure-traffic-manager"></a>Aproveite o Azure Traffic Manager
[Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md) permite que você encaminhe solicitações para vários sites localizados geograficamente que recebem suporte de vários Serviços de Azure Search. Uma vantagem do Gerenciador de Tráfego é que ele pode investigar o Azure Search para garantir sua disponibilidade e encaminhar os usuários aos serviços de pesquisa alternativos em caso de inatividade. Além disso, se você estiver encaminhando solicitações de pesquisa por meio dos Sites do Azure, o Gerenciador de Tráfego do Azure permitirá o balanceamento de carga de casos em que o site está ativo, mas não o Azure Search. Veja um exemplo de uma arquitetura que aproveita o Gerenciador de Tráfego.

   ![Tabela de referência cruzada de serviços por região, com o Gerenciador de tráfego central][3]

## <a name="monitor-performance"></a>Monitorar o desempenho
O Azure Search oferece a capacidade de analisar e monitorar o desempenho de seu serviço por meio [análise de tráfego de pesquisa](search-traffic-analytics.md). Quando você habilitar essa funcionalidade e adicionar instrumentação ao seu aplicativo cliente, você também pode registrar as operações de pesquisa individuais, bem como as métricas agregadas para uma conta de armazenamento do Azure que pode ser processada para análise ou visualizada no Power BI. Captura de métricas dessa maneira fornece estatísticas de desempenho, como o número médio de consultas ou tempos de resposta de consulta. Além disso, o registro em log das operações permite a análise de detalhes das operações de pesquisa específicas.

Análise de tráfego é útil para entender as taxas de latência sob essa perspectiva de Azure Search. Como as métricas de desempenho de consulta registradas têm base no tempo que uma consulta leva para ser completamente processada no Azure Search (desde o momento da solicitação até quando é enviada), você poderá usá-las para determinar se os problemas de latência estão no lado do serviço de Azure Search, ou fora do serviço, como a latência de rede.  

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os limites de serviços e os tipos de preço para cada um, confira [Limites do serviço de Azure Search](search-limits-quotas-capacity.md).

Visite [Planejamento de capacidade](search-capacity-planning.md) para saber mais sobre combinações de partição e de réplica.

Para obter dados mais detalhados sobre o desempenho e para ver algumas demonstrações de como implementar as otimizações discutidas neste artigo, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
