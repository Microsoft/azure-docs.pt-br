---
title: "Considerações de desempenho e otimização do Azure Search | Microsoft Docs"
description: Ajustar o desempenho da Pesquisa do Azure e configurar o dimensionamento ideal
services: search
documentationcenter: 
author: LiamCavanagh
manager: pablocas
editor: 
ms.assetid: 4d3cd864-29d2-4921-be0d-a3f1a819de46
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: f4e371fc16bc57e6963f1ec51c0ea864fa568f0c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-search-performance-and-optimization-considerations"></a>Considerações de desempenho e otimização da Pesquisa do Azure
Uma boa experiência de pesquisa é essencial para o sucesso de muitos aplicativos móveis e da Web. De imóveis a feiras de carros usados até catálogos online, uma pesquisa rápida e resultados relevantes afetarão a experiência do cliente. Este documento tem como objetivo ajuda a descobrir as práticas recomendadas para obter o máximo da Pesquisa do Azure, especialmente para cenários avançados com requisitos sofisticados de escalabilidade, suporte a vários idiomas ou classificação personalizada.  Além disso, este documento descreve componentes internos e cobre abordagens que funcionam bem em aplicativos reais de clientes.

## <a name="performance-and-scale-tuning-for-search-services"></a>Ajuste de desempenho e escala para serviços de pesquisa
Estamos todos acostumados com mecanismos de pesquisa como o Bing e o Google e com o ótimo desempenho que eles oferecem.  Como resultado, quando os clientes usam seu aplicativo móvel ou da Web habilitado para pesquisa, eles esperam características de desempenho semelhantes.  Ao otimizar o desempenho da pesquisa, uma das abordagens recomendadas é se concentrar na latência, que é o tempo que uma consulta demora para ser concluída e retornar resultados.  Ao otimização a latência de pesquisa é importante:

1. Escolher uma latência de destino (ou a quantidade máxima de tempo) que uma solicitação de pesquisa normal deve demorar para ser concluída.
2. Criar e testar uma carga de trabalho real com o serviço de pesquisa com um conjunto de dados realista a fim de medir essas taxas de latência.
3. Comece com um número baixo de QPS (consultas por segundo) e continue a aumentar o número executado no teste de até que a latência da consulta fique abaixo da latência de destino definida.  Este é um parâmetro de comparação importante para ajudar você a planejar a escala à medida que o uso de seu aplicativo cresce.
4. Sempre que possível, reutilize as conexões HTTP.  Se você estiver usando o SDK .NET da Pesquisa do Azure, significa que você deve reutilizar uma instância ou instância do [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) , e se você estiver usando a API REST, reutilize um único HttpClient.

Ao criar essas cargas de trabalho de teste, tenha em mente algumas características da Pesquisa do Azure:

1. É possível obter tantas consultas de pesquisa ao mesmo tempo, que os recursos disponíveis em seu serviço de Pesquisa do Azure ficarão sobrecarregados.  Quando isso acontecer, você verá códigos de resposta HTTP 503.  Por esse motivo, é melhor começar com vários intervalos de solicitações de pesquisa para ver as diferenças nas taxas de latência conforme você adiciona mais solicitações de pesquisa.
2. O carregamento de conteúdo na Pesquisa do Azure afetará o desempenho geral e a latência do serviço de Pesquisa do Azure.  Se você pretende enviar dados enquanto os usuários estão executando pesquisas, é importante levar em conta essa carga de trabalho em seus testes.
3. Nem todas as consultas de pesquisa serão executadas com os mesmos níveis de desempenho.  Por exemplo, uma sugestão de pesquisa ou pesquisa de documento normalmente será executada mais rápido do que uma consulta com um número considerável de facetas e filtros.  É melhor levar em conta as diversas consultas que você espera ver ao criar seus testes.  
4. A variação das solicitações de pesquisa é importante, pois se você executar continuamente as mesmas solicitações de pesquisa, o armazenamento de dados em cache começará a fazer o desempenho parecer melhor que pode ser com um conjunto de consultas mais diferentes.

> [!NOTE]
> O [Teste de Carga no Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release) é uma boa maneira de executar os testes de parâmetros de comparação que permitem a você executar solicitações HTTP da forma necessária para executar consultas no Azure Search e permite a paralelização de solicitações.
> 
> 

## <a name="scaling-azure-search-for-high-query-rates-and-throttled-requests"></a>Dimensionamento da Pesquisa do Azure para taxas de consulta altas e solicitações limitadas
Quando você estiver recebendo muitas solicitações limitadas ou ultrapassar suas taxas de latência de destino de uma carga de consulta maior, você pode diminuir essas taxas de latência usando uma dentre duas maneiras:

1. **Aumentar as Réplicas:** uma réplica é semelhante a uma cópia dos seus dados, permitindo que o Azure Search balanceie a carga de solicitações em várias cópias.  Todo o balanceamento de carga e a replicação de dados entre as réplicas é gerenciado pela Pesquisa do Azure, e você pode alterar o número de réplicas alocadas para o serviço a qualquer momento.  É possível alocar até 12 réplicas em um serviço de pesquisa Standard e três réplicas em um serviço de pesquisa Básico. As réplicas podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).
2. **Aumentar a Camada de Pesquisa:** o Azure Search vem em [várias camadas](https://azure.microsoft.com/pricing/details/search/), e cada uma dessas camadas oferece níveis diferentes de desempenho.  Em alguns casos, você pode ter tantas consultas que a camada na qual você está não consegue fornecer taxas latência suficientemente baixas, mesmo quando as réplicas são maximizadas.  Nesse caso, convém utilizar uma das camadas mais altas de pesquisa, como a camada S3 da Pesquisa do Azure, que é adequada para cenários com uma grande quantidade de documentos e cargas de trabalho extremamente altas.

## <a name="scaling-azure-search-for-slow-individual-queries"></a>Dimensionamento da Pesquisa do Azure para consultas individuais lentas
Outro motivo de as taxas de latência serem lentas é uma única consulta demorar muito para ser concluída.  Nesse caso, a adição de réplicas não melhorará as taxas de latência.  Para esses casos, há duas opções disponíveis:

1. **Aumentar Partições** Uma partição é um mecanismo para dividir os dados entre recursos extras.  Por esse motivo, quando você adiciona uma segunda partição, seus dados são divididos em dois.  Uma terceira partição divide o índice em três etc.  Isso também tem o efeito de, em alguns casos, as consultas lentas serem executadas com mais rapidez devido à paralelização da computação.  Há alguns exemplos nos quais temos visto essa paralelização funcionar muito bem com consultas de baixa seletividade.  Essas são formadas por consultas que correspondam a muitos documentos, ou quando facetas precisam fornecer contagens de uma grande quantidade de documentos.  Como há muita computação envolvida para pontuar a relevância dos documentos ou para contar o número de documentos, a adição de partições extras pode ajudar a fornecer essa computação adicional.  
   
   Pode haver no máximo 12 partições no serviço de pesquisa Standard, e uma partição no serviço de pesquisa Básico.  As partições podem ser ajustadas no [Portal do Azure](search-create-service-portal.md) ou [PowerShell](search-manage-powershell.md).
2. **Limitar Campos de Alta Cardinalidade :** um campo de alta cardinalidade é composto por um campo facetável ou filtrável que tem um número considerável de valores exclusivos e, como resultado, usa muitos recursos para calcular os resultados.   Por exemplo, a configuração de um campo de ID do Produto ou Descrição como facetável/filtrável permitira a alta cardinalidade, pois a maioria dos valores de documento a documento é exclusiva. Sempre que for possível, limite o número de campos de alta cardinalidade.
3. **Aumentar a Camada de Pesquisa:** mudar para um nível mais alto do Azure Search pode ser outra maneira de melhorar o desempenho de consultas lentas.  Cada camada superior também fornece uma CPU mais rápida e mais memória, o que pode ter um impacto positivo no desempenho da consulta.

## <a name="scaling-for-availability"></a>Dimensionamento para disponibilidade
As réplicas não apenas ajudam a reduzir a latência da consulta, mas também permitem a alta disponibilidade.  Com uma única réplica, você deve esperar um tempo de inatividade periódico devido à reinicialização do servidor após atualizações de software ou a outros eventos de manutenção.  Como resultado, é importante considerar se seu aplicativo exige alta disponibilidade de pesquisas (consultas), bem como gravações (eventos de indexação).  A Pesquisa do Azure oferece opções de SLA em todas as ofertas de pesquisa pagas, com os seguintes atributos:

* Duas réplicas para alta disponibilidade das cargas de trabalho somente leitura (consultas)
* Três ou mais réplicas para alta disponibilidade das cargas de trabalho de leitura/gravação (consultas e indexação)

Para obter mais detalhes sobre isso, visite o [Contrato de Nível de Serviço da Pesquisa do Azure](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Como réplicas são cópias de seus dados, ter várias réplicas permite que a Pesquisa do Azure execute reinicializações e manutenção da máquina em uma réplica por vez, permitindo que as consultas continuem a ser executadas em outras réplicas.  Por esse motivo, você também precisa considerar como esse tempo de inatividade pode afetar as consultas que precisam ser executadas em uma cópia a menos dos dados.

## <a name="scaling-geo-distributed-workloads-and-provide-geo-redundancy"></a>Dimensionamento de cargas de trabalho distribuídas geograficamente e fornecimento de redundância geográfica
Para cargas de trabalho distribuídas geograficamente, você perceberá que os usuários localizados longe do data center no qual o serviço da Pesquisa do Azure está hospedado terão taxas mais altas de latência.  Por esse motivo, geralmente é importante ter vários serviços de pesquisa em regiões que estão mais próximas a esses usuários.  No momento, a Pesquisa do Azure não fornece um método automatizado de replicação geográfica de índices da Pesquisa do Azure entre regiões, mas há algumas técnicas que podem ser usadas para tornar esse processo simples de implementar e gerenciar. Elas serão descritas nas próximas seções.

O objetivo de um conjunto de serviços de pesquisa distribuídos geograficamente é ter dois ou mais índices disponíveis em duas ou mais regiões. Nesse caso, o usuário será encaminhado para o serviço de Pesquisa do Azure com a menor latência, como visto neste exemplo:

   ![Tabela de referência cruzada de serviços por região][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>Manter os dados sincronizados em vários serviços de Pesquisa do Azure
Há duas opções para manter seus serviços de pesquisa distribuídos em sincronia, que são o uso do [Indexador do Azure Search](search-indexer-overview.md) ou a API Push (também conhecida como a [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/)).  

### <a name="azure-search-indexers"></a>Indexadores na Pesquisa do Azure
Se você estiver usando o Indexador do Azure Search, já estará importando as alterações de um armazenamento de dados central, como o Banco de Dados SQL do Azure ou o Azure Cosmos DB. Quando você cria um novo Serviço de pesquisa, você simplesmente cria também um novo Indexador de Pesquisa do Azure para esse serviço que aponta para o mesmo repositório de dados. Dessa forma, sempre que novas alterações entrarem no repositório de dados, elas serão indexadas pelos vários Indexadores.  

Veja um exemplo de como seria a aparência dessa arquitetura.

   ![Única fonte de dados com o indexador distribuído e combinações de serviço][2]

### <a name="push-api"></a>API Push
Se você estiver usando a API Push da Pesquisa do Azure para [Atualizar conteúdo em seu índice da Pesquisa do Azure](https://docs.microsoft.com/rest/api/searchservice/update-index), você pode manter em sincronia seus vários serviços de pesquisa enviando por push as alterações para todos os serviços de pesquisa, sempre que uma atualização for necessária.  Ao fazer isso é importante lidar com casos nos quais uma atualização para um serviço de pesquisa falha e uma ou mais atualizações são bem-sucedidas.

## <a name="leveraging-azure-traffic-manager"></a>Aproveitando o Gerenciador de Tráfego do Azure
[Gerenciador de Tráfego do Azure](../traffic-manager/traffic-manager-overview.md) permite que você encaminhe solicitações para vários sites localizados geograficamente que recebem suporte de vários Serviços de Pesquisa do Azure.  Uma vantagem do Gerenciador de Tráfego é que ele pode investigar a Pesquisa do Azure para garantir sua disponibilidade e encaminhar os usuários aos serviços de pesquisa alternativos em caso de inatividade.  Além disso, se você estiver encaminhando solicitações de pesquisa por meio dos Sites do Azure, o Gerenciador de Tráfego do Azure permitirá o balanceamento de carga de casos em que o site está ativo, mas não a Pesquisa do Azure.  Veja um exemplo de uma arquitetura que aproveita o Gerenciador de Tráfego.

   ![Tabela de referência cruzada de serviços por região, com o Gerenciador de tráfego central][3]

## <a name="monitoring-performance"></a>Monitoramento de desempenho
A Pesquisa do Azure oferece a capacidade de analisar e monitorar o desempenho do serviço por meio da [STA (Análise de Tráfego de Pesquisa)](search-traffic-analytics.md). Com a STA, você também pode registrar operações de pesquisa individuais, bem como as métricas agregadas para uma conta de armazenamento do Azure que pode ser processada para análise ou visualizada no Power BI.  Usando as métricas de STA, você pode examinar as estatísticas de desempenho, como o número médio de consultas ou tempos de resposta da consulta.  Além disso, o registro em log das operações permite a análise de detalhes das operações de pesquisa específicas.

STA é uma ferramenta valiosa para compreender as taxas de latência sob essa perspectiva da Pesquisa do Azure.  Como as métricas de desempenho de consulta registradas têm base no tempo que uma consulta leva para ser completamente processada na Pesquisa do Azure (desde o momento da solicitação até quando é enviada), você poderá usá-las para determinar se os problemas de latência estão no lado do serviço de Pesquisa do Azure, ou fora do serviço, como a latência de rede.  

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre os limites de serviços e os tipos de preço para cada um, confira [Limites do serviço de Pesquisa do Azure](search-limits-quotas-capacity.md).

Visite [Planejamento de capacidade](search-capacity-planning.md) para saber mais sobre combinações de partição e de réplica.

Para obter dados mais detalhados sobre o desempenho e para ver algumas demonstrações de como implementar as otimizações discutidas neste artigo, assista ao vídeo a seguir:

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
