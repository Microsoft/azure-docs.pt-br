---
title: "Monitorar uso e estatísticas em um serviço do Azure Search | Microsoft Docs"
description: "Acompanhe o consumo de recursos e o tamanho de índice da Pesquisa do Azure, um serviço de pesquisa de nuvem hospedado do Microsoft Azure."
services: search
documentationcenter: 
author: bernitorres
manager: jlembicz
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 05/01/2017
ms.author: betorres
ms.openlocfilehash: 16cb5a1e16a59200f0e731622398efcf24c3f777
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-an-azure-search-service"></a>Criar um serviço do Azure Search

O Azure Search oferece vários recursos para acompanhar o uso e o desempenho de serviços de pesquisa. Ele fornece acesso a recursos de monitoramento estendidos no Power BI, logs, estatísticas de índice e métrica. Este artigo descreve como habilitar as diferentes estratégias de monitoramento e como interpretar os dados resultantes.

## <a name="azure-search-metrics"></a>Métrica do Azure Search
Métricas lhe dão visibilidade praticamente em tempo real de seu serviço de pesquisa e estão disponíveis para todos os serviços, sem nenhuma configuração adicional. Elas permitem controlar o desempenho do seu serviço por até 30 dias.

O Azure Search coleta dados de três métricas diferentes:

* Latência de pesquisa: o tempo que o serviço de pesquisa precisou para processar consultas de pesquisa, agregadas por minuto.
* QPS (consultas de pesquisa por segundo): o número de consultas de pesquisa recebidas por segundo, agregadas por minuto.
* Percentual das consultas de pesquisa limitadas: o percentual de consultas de pesquisa que foram limitadas, agregadas por minuto.

![Captura de tela da atividade de QPS][1]

### <a name="set-up-alerts"></a>Configurar alertas
Da página de detalhes de métrica, você poderá configurar alertas para disparar uma notificação por email ou uma ação automatizada quando uma métrica ultrapassar um limite que você tiver definido.

Para obter mais informações sobre métrica, consulte a documentação completa no Azure Monitor.  

## <a name="how-to-track-resource-usage"></a>Como controlar o uso de recursos
Acompanhar o crescimento do tamanho dos índices e do documento pode ajudar você a ajustar a capacidade de forma proativa antes de atingir o limite superior que você estabeleceu para seu serviço. Você pode fazer isso no portal ou programaticamente usando a API REST.

### <a name="using-the-portal"></a>Usando o portal

Para monitorar o uso de recursos, exiba as contagens e as estatísticas do serviço no [portal](https://portal.azure.com).

1. Entre no [portal](https://portal.azure.com).
2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. É possível encontrar blocos do serviço na Home page, ou você pode navegar até o serviço usando Procurar na Barra de navegação rápida.

A seção Uso inclui um medidor que informa qual parte dos recursos disponíveis estão atualmente em uso. Para obter informações sobre os limites por serviço para índices, documentos e armazenamento, consulte [Limites de serviço](search-limits-quotas-capacity.md).

  ![Bloco Uso][2]

> [!NOTE]
> A captura de tela acima é para o serviço Gratuito, que tem um máximo de uma réplica e uma partição cada, podendo hospedar somente três índices, 10.000 documentos ou 50 MB de dados, o que ocorrer primeiro. Os serviços criados nos tipos de preço Básico ou Standard têm limites de serviço muito maiores. Para obter mais informações sobre como escolher um tipo de preço, consulte [Escolher um tipo de preço ou SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Usando a API REST
A API REST da Pesquisa do Azure e o SDK do .NET fornecem acesso programático às métricas de serviço.  Se você estiver usando [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para carregar um índice do Banco de Dados SQL do Azure ou do Azure Cosmos DB, uma API adicional estará disponível para obter os números dos quais você precisa.

* [Obter estatísticas de índice](/rest/api/searchservice/get-index-statistics)
* [Contar documentos](/rest/api/searchservice/count-documents)
* [Obter o status do indexador](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Como exportar logs e métrica

Você pode exportar os logs de operação para o serviço e os dados brutos para as métricas descritas na seção anterior. Os logs de operação permitem que você saiba como o serviço está sendo usado e podem ser consumidos pelo Power BI quando dados são copiados para uma conta de armazenamento. O Azure Search fornece um pacote de conteúdo de monitoramento do Power BI para essa finalidade.


### <a name="enabling-monitoring"></a>Habilitar o monitoramento
Abra o serviço Azure Search no [Portal do Azure](http://portal.azure.com) sob a opção de Habilitar o Monitoramento.

Escolha os dados que você deseja exportar: Logs, Métricas ou ambos. Você pode copiá-los para uma conta de armazenamento, enviá-los para um hub de eventos ou exportá-los para o Log Analytics.

![Como habilitar o monitoramento no portal][3]

Para habilitar usando o PowerShell ou a CLI do Azure, consulte a documentação [aqui](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Esquemas de métrica e logs
Quando os dados são copiados para uma conta de armazenamento, os dados são formatados como JSON e são colocados em dois contêineres:

* insights-logs-operationlogs: para logs de tráfego de pesquisa
* insights-metrics-pt1m: para métrica

Haverá um blob por hora, por contêiner.

Exemplo de caminho: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Esquema do log
Os blobs de logs contêm seus logs de tráfego do serviço de pesquisa.
Cada blob tem um objeto-raiz chamado **registros** que contém uma matriz de objetos do log.
Cada blob tem registros na operação que ocorrem durante a mesma hora.

| Nome | Tipo | Exemplo | Observações |
| --- | --- | --- | --- |
| tempo real |datetime |"2015-12-07T00:00:43.6872559Z" |Carimbo de data/hora da operação |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Seu ResourceId |
| operationName |string |"Query.Search" |O nome da operação |
| operationVersion |string |"2015-02-28" |A api-version usada |
| categoria |string |"OperationLogs" |constante |
| resultType |string |"Success" |Valores possíveis: Success ou Failure |
| resultSignature |int |200 |Código do resultado HTTP |
| durationMS |int |50 |Duração da operação em milissegundos |
| propriedades |objeto |confira a seguinte tabela |Objeto que contém os dados específicos da operação |

**Esquema de propriedades**
| Nome | Tipo | Exemplo | Observações |
| --- | --- | --- | --- |
| Descrição |string |"GET /indexes('content')/docs" |Ponto de extremidade da operação |
| Consultar |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |Parâmetros da consulta |
| Documentos |int |42 |Número de documentos processados |
| IndexName |string |"testindex" |Nome do índice associado à operação |

#### <a name="metrics-schema"></a>Esquema de métricas
| Nome | Tipo | Exemplo | Observações |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |id do recurso |
| metricName |string |"Latency" |o nome da métrica |
| tempo real |datetime |"2015-12-07T00:00:43.6872559Z" |carimbo de data/hora da operação |
| média |int |64 |O valor médio das amostras brutas no intervalo de agregação da métrica |
| mínimo |int |37 |O valor mínimo das amostras brutas no intervalo de agregação da métrica |
| máximo |int |78 |O valor máximo das amostras brutas no intervalo de agregação da métrica |
| total |int |258 |O valor total das amostras brutas no intervalo de agregação da métrica |
| count |int |4 |O número de amostras brutas usadas para gerar a métrica |
| intervalo de tempo |string |"PT1M" |O intervalo de agregação da métrica no ISO 8601 |

Todas as métricas são reportadas em intervalos de um minuto. Cada métrica expõe valores mínimo, máximo e médios por minuto.

Para a métrica SearchQueriesPerSecond, o valor mínimo é o valor mais baixo para consultas de pesquisa por segundo que foram registradas durante esse minuto. O mesmo se aplica ao valor máximo. O valor médio é a agregação durante todo o minuto.
Considere este cenário durante um minuto, um segundo com uma carga muito alta, que é o valor máximo para SearchQueriesPerSecond, seguido de 58 segundos de carga mediana, e, por fim, um segundo com apenas uma consulta, que será o valor mínimo.

Para ThrottledSearchQueriesPercentage, os valores mínimo, máximo, médio e total possuem o mesmo valor: a porcentagem de consultas de pesquisa que foram limitadas, a partir do no número total de consultas de pesquisa durante um minuto.

## <a name="analyzing-your-data-with-power-bi"></a>Analisar seus dados com o Power BI

Recomendamos o uso do [Power BI](https://powerbi.microsoft.com) para explorar e visualizar seus dados. Você pode conectá-lo facilmente à sua conta de Armazenamento do Azure e começar rapidamente a analisar os dados.

O Azure Search fornece um [pacote de conteúdo do Power BI](https://app.powerbi.com/getdata/services/azure-search) que permite que você monitore e entenda seu tráfego de pesquisa com tabelas e gráficos predefinidos. Ele contém um conjunto de relatórios do Power BI que conectam-se automaticamente a seus dados e fornecem análises visuais sobre o serviço de pesquisa. Para obter mais informações, consulte a [página de ajuda do pacote de conteúdo](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Painel do Power BI para o Azure Search][4]

## <a name="next-steps"></a>Próximas etapas
Examine [Dimensionar réplicas e partições](search-limits-quotas-capacity.md) para obter diretrizes sobre como balancear a alocação de partições e de réplicas de um serviço existente.

Visite [Gerenciar o seu serviço do Search no Microsoft Azure](search-manage.md), para obter mais informações sobre a administração do serviço, ou [Desempenho e otimização](search-performance-optimization.md), para obter diretrizes de ajuste.

Saiba mais sobre como criar relatórios incríveis. Confira [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obter detalhes

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
