---
title: Visão Geral do Azure Monitor| Microsoft Docs
description: Visão geral dos serviços e funcionalidades da Microsoft que contribuem para uma estratégia de monitoramento completa para seus serviços e aplicativos do Azure.
author: bwren
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: bwren
ms.openlocfilehash: 836a17051aee4e6a9ac3089f60da30673783e408
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875978"
---
# <a name="azure-monitor-overview"></a>Visão geral do Azure Monitor

O Azure Monitor maximiza a disponibilidade e o desempenho de seus aplicativos fornecendo uma solução abrangente para coletar, analisar e agir em relação a dados telemétricos de seus ambientes locais e de nuvem. Ele ajuda a entender o desempenho de seus aplicativos, além de identificar de maneira proativa os problemas que os estão afetando e os recursos dos quais eles dependem.

> [!VIDEO https://www.youtube.com/embed/_hGff5bVtkM]

## <a name="overview"></a>Visão geral
O diagrama a seguir fornece uma visão geral do Azure Monitor. No centro do diagrama estão os armazenamentos de dados para métricas e logs, que são os dois tipos fundamentais de uso de dados pelo Azure Monitor. À esquerda estão as [fontes de dados de monitoramento](platform/data-sources.md) que populam esses [armazenamentos de dados](platform/data-platform.md). À direita estão as diferentes funções que o Azure Monitor executa com os dados coletados, como análise, alertas e transmissão para sistemas externos.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

![Visão geral do Azure Monitor](media/overview/overview.png)


## <a name="monitoring-data-platform"></a>Plataforma de dados de monitoramento
Todos os dados coletados pelo Azure Monitor se enquadram em um dos dois tipos fundamentais, [métricas e logs](platform/data-platform.md). As [Métricas](platform/data-platform-metrics.md) são valores numéricos que descrevem algum aspecto de um sistema em um ponto específico no tempo. Elas são leves e podem dar suporte a cenários quase em tempo real. Os [Logs](platform/data-platform-logs.md) contêm diferentes tipos de dados organizados em registros com diferentes conjuntos de propriedades para cada um. Os dados telemétricos, como eventos e rastreamentos, são armazenados como logs acrescidos dos dados de desempenho, de modo que possam todos ser combinados para análise.

Para muitos recursos do Azure, você verá os dados coletados pelo Azure Monitor diretamente em sua página de visão geral no portal do Azure. Dê uma olhada em um máquina virtual, por exemplo, e você verá vários gráficos que exibem as métricas de desempenho. Clique em um dos gráficos para abrir os dados no [Metrics Explorer](platform/metrics-charts.md), no portal do Azure, que permite fazer o gráfico dos valores de várias métricas ao longo do tempo.  É possível exibir os gráficos interativamente ou fixá-los em um painel para exibi-los com outras visualizações.

![metrics](media/overview/metrics.png)

Os dados do log coletados pelo Azure Monitor podem ser analisados com [consultas](log-query/log-query-overview.md) que recuperam, consolidam e analisam rapidamente esses dados.  Você pode criar e testar consultas usando [log Analytics](log-query/portals.md) na portal do Azure e, em seguida, analisar diretamente os dados usando essas ferramentas ou salvar consultas para uso com [visualizações](visualizations.md) ou [regras de alerta](platform/alerts-overview.md).

O Azure Monitor usa uma versão da [linguagem de consulta Kusto](/azure/kusto/query/) usada pelo Azure Data Explorer que é adequada para consultas de log simples, mas também inclui funcionalidades avançadas como agregações, junções e análises inteligentes. É possível aprender a linguagem de consulta rapidamente por meio de [várias lições](log-query/get-started-queries.md).  São fornecidas orientações específicas para usuários que já estão familiarizados com [SQL](log-query/sql-cheatsheet.md) e [Splunk](log-query/splunk-cheatsheet.md).

![Logs](media/overview/logs.png)

## <a name="what-data-does-azure-monitor-collect"></a>Quais dados são coletados pelo Azure Monitor?
O Azure Monitor pode coletar dados de várias fontes. Você pode pensar em dados de monitoramento de seus aplicativos em camadas, que vão do aplicativo, de sistema operacional e serviços de que ele depende, até a própria plataforma. O Azure Monitor coleta dados de cada uma dos seguintes camadas:

- **Dados de monitoramento de aplicativo**: Os dados sobre o desempenho e a funcionalidade do código que você gravou, independentemente da plataforma.
- **Dados de monitoramento de SO Convidado**: Dados sobre o sistema operacional no qual seu aplicativo está em execução. Ele pode estar em execução no Azure, em outra nuvem ou localmente. 
- **Dados de monitoramento de recursos do Azure**: Dados sobre a operação de um recurso do Azure.
- **Dados de monitoramento de assinatura do Azure**: Dados sobre a operação e o gerenciamento de uma assinatura do Azure, bem como dados sobre a integridade e a operação do próprio Azure. 
- **Dados de monitoramento do locatário do Azure**: Dados sobre a operação de serviços do Azure no nível de locatário como Azure Active Directory.

Assim que você cria uma assinatura do Azure e começa a adicionar recursos como máquinas virtuais e aplicativos Web, o Azure Monitor começará a coletar dados.  [Os logs de atividade](platform/activity-logs-overview.md) registram quando os recursos são criados ou modificados. As [métricas](platform/data-platform.md) indicam o desempenho do recurso e os recursos que ele está consumindo. 

Estenda os dados que você está coletando para a operação real dos recursos [habilitando o diagnóstico](platform/diagnostic-logs-overview.md) e [adicionando um agente](platform/agent-windows.md) para recursos de computação. Isso colherá dados telemétricos para a operação interna do recurso e permitirá que você configure diferentes [fontes de dados](platform/agent-data-sources.md) para coletar logs e métricas do sistema operacional convidado Windows e Linux. 

Habilite o monitoramento do [aplicativo de serviços de aplicativos](app/azure-web-apps.md) ou da VM e do aplicativo do conjunto de dimensionamento de [máquinas virtuais](app/azure-vm-vmss-apps.md)para permitir que Application insights colete informações detalhadas sobre o aplicativo, incluindo exibições de página, solicitações de aplicativo e exceção. Verifique a disponibilidade do aplicativo configurando um [teste de disponibilidade](app/monitor-web-app-availability.md) para simular o tráfego de usuários.

### <a name="custom-sources"></a>Fontes personalizadas
O Azure Monitor pode coletar dados de log de qualquer cliente REST usando a [API do Coletor de Dados](platform/data-collector-api.md). Isso permite que você crie cenários de monitoramento personalizados e estenda o monitoramento a recursos que não expõem a telemetria por outras fontes.



## <a name="insights"></a>Insights
Os dados de monitoramento só serão úteis se puderem aumentar sua visibilidade em relação ao funcionamento do ambiente de computação. O Azure Monitor inclui vários recursos e ferramentas que fornecem insights valiosos sobre seus aplicativos e outros recursos dos quais eles dependem. [Soluções de monitoramento](insights/solutions.md) e recursos como o [Application Insights](app/app-insights-overview.md) e o [Azure Monitor para contêineres](insights/container-insights-overview.md) fornecem insights aprofundados sobre diferentes aspectos do aplicativo e de serviços específicos do Azure. 

### <a name="application-insights"></a>Application Insights
O [Application Insights](app/app-insights-overview.md) monitora a disponibilidade, o desempenho e o uso do seu aplicativo, seja hospedado na nuvem ou localmente. Ele aproveita a plataforma de análise de dados avançada no Azure Monitor para fornecer informações detalhadas sobre as operações do aplicativo e diagnosticar erros sem esperar que um usuário relate-os. O Application Insights inclui pontos de conexão com uma variedade de ferramentas de desenvolvimento e integra-se ao Visual Studio para dar suporte a seus processos de DevOps.

![Insights de aplicativo](media/overview/app-insights.png)

### <a name="azure-monitor-for-containers"></a>Azure Monitor para contêineres
O [Azure Monitor para contêineres](insights/container-insights-overview.md) é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantadas em clusters do Kubernetes gerenciado hospedados no AKS (Serviço de Kubernetes do Azure). Ela oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. Os logs do contêiner também são coletados.  Após habilitar o monitoramento a partir de clusters do Kubernetes, essas métricas e logs serão coletados automaticamente para você por meio de uma versão em contêiner do agente do Log Analytics para Linux.

![Integridade do contêiner](media/overview/container-insights.png)

### <a name="azure-monitor-for-vms"></a>Azure Monitor para VMs
O [Azure Monitor para VMs](insights/vminsights-overview.md) monitora suas VMs (máquinas virtuais) do Azure em escala analisando o desempenho e a integridade das VMs do Windows e do Linux, incluindo seus diferentes processos e dependências interconectadas em outros recursos e processos externos. A solução inclui suporte para monitorar o desempenho e as dependências de aplicativos nas VMs hospedadas localmente ou em outro provedor de nuvem.  


![VM Insights](media/overview/vm-insights.png)

### <a name="monitoring-solutions"></a>Soluções de monitoramento
As [soluções de gerenciamento](insights/solutions.md) no Azure Monitor são conjuntos de lógica empacotados que fornecem informações para determinado aplicativo ou serviço. Eles incluem lógica para coleção de dados de monitoramento de aplicativo ou serviço, [consultas](log-query/log-query-overview.md) para analisar esses dados e [exibições](../log-analytics/log-analytics-view-designer.md) para visualização. As soluções de gerenciamento são [disponibilizadas pela Microsoft](insights/solutions-inventory.md) e por parceiros para oferecer monitoramento de vários serviços de terceiros e do Azure.

![Soluções de monitoramento](media/overview/solutions-overview.png)

## <a name="responding-to-critical-situations"></a>Respondendo a situações críticas
Além de permitir que você analise interativamente os dados de monitoramento, uma solução de monitoramento eficaz deve ser capaz de responder proativamente a condições críticas identificadas nos dados coletados. Isso pode envolver enviar um texto ou email a um administrador responsável para investigar um problema. Você também pode iniciar um processo automatizado para tentar corrigir uma condição de erro.


### <a name="alerts"></a>Alertas
Os [alertas no Azure Monitor](platform/alerts-overview.md) notificam proativamente sobre condições críticas e podem tentar tomar as medidas corretivas necessárias. As regras de alerta com base nas métricas fornecem alertas quase em tempo real com base em valores numéricos; já as regras com base nos logs permitem uma lógica complexa entre dados de várias fontes.

As regras de alerta no Azure Monitor usam [grupos de ação](platform/action-groups.md), que contêm conjuntos exclusivos de destinatários e ações que podem ser compartilhados entre várias regras. De acordo com suas necessidades, os grupos de ação podem executar ações como usar web hooks para fazer com que os alertas iniciem ações externas ou se integrem às ferramentas ITSM.

![Alertas](media/overview/alerts.png)

### <a name="autoscale"></a>Autoscale
O dimensionamento automático permite ter a quantidade certa de recursos em execução para lidar com a carga em seu aplicativo. Ele permite que você crie regras que usam métricas coletadas pelo Azure Monitor para determinar quando adicionar recursos automaticamente para lidar com aumentos de carga e também economizar dinheiro removendo recursos que estão ociosos. Especifique um número mínimo e máximo de instâncias e a lógica de quando aumentar ou diminuir os recursos.

![Autoscale](media/overview/autoscale.png)

## <a name="visualizing-monitoring-data"></a>Visualizando dados de monitoramento
As [Visualizações](visualizations.md), como tabelas e gráficos, são ferramentas eficientes para resumir dados de monitoramento e apresentá-los para públicos diferentes. O Azure Monitor tem seus próprios recursos para visualizar os dados de monitoramento e aproveita outros serviços do Azure para mostrá-los a públicos diferentes.

### <a name="dashboards"></a>Painéis
Os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) permitem combinar diferentes tipos de dados, incluindo métricas e logs, em um único painel no [portal do Azure](https://portal.azure.com). Você pode compartilhar o painel com outros usuários do Azure. Podem ser adicionados elementos de todo o Azure Monitor a um painel do Azure, além da saída de qualquer gráfico de métricas ou de consulta de log. Por exemplo, é possível criar um painel que combine blocos que mostrem um gráfico de métricas, uma tabela de logs de atividades, um gráfico de uso do Application Insights e a saída de uma consulta de log.

![painel](media/overview/dashboard.png)

### <a name="views"></a>Exibições
Os [Modos de Exibição](../log-analytics/log-analytics-view-designer.md) apresentam os dados de log visualmente no Azure Monitor.  Cada modo de exibição inclui um único bloco que detalha uma combinação de visualizações, como gráficos de barras e de linhas, além de listas que resumem dados críticos.  As soluções de monitoramento incluem exibições que resumem dados para um aplicativo específico e você pode criar seus próprios modos de exibição para apresentar dados de qualquer consulta de log. Assim como outros elementos no Azure Monitor, os modos de exibição podem ser adicionados a painéis do Azure.

![Exibir](media/overview/view.png)

### <a name="power-bi"></a>Power BI
O [Power BI](https://powerbi.microsoft.com) é um serviço de análise de negócios que fornece visualizações interativas em uma variedade de fontes de dados e é um meio eficaz de disponibilizar os dados para outras pessoas dentro e fora da sua organização. Você pode configurar o Power BI para [importar dados de log automaticamente do Azure Monitor](../log-analytics/log-analytics-powerbi.md) a fim de aproveitar essas visualizações adicionais.


![Power BI](media/overview/power-bi.png)


## <a name="integrate-and-export-data"></a>Integrar e exportar dados
Geralmente, você terá o requisito de integrar o Azure Monitor a outros sistemas e de criar soluções personalizadas que usam os dados de monitoramento. Outros serviços do Azure funcionam com o Azure Monitor para fornecer essa integração.

### <a name="event-hub"></a>Hub de evento
Os [Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs) são um serviço de ingestão de eventos e plataforma de streaming que pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Use os hubs de eventos para [transmitir dados de Azure monitor](platform/stream-monitoring-data-event-hubs.md) para o Siem do parceiro e ferramentas de monitoramento.


### <a name="logic-apps"></a>Aplicativos Lógicos
O [Aplicativo Lógico](https://azure.microsoft.com/services/logic-apps) é um serviço que permite automatizar tarefas e processos de negócios usando fluxos de trabalho que se integram a diversos sistemas e serviços. As atividades disponíveis envolvem ler e gravar logs e métricas no Azure Monitor, o que permite a você criar fluxos de trabalho integrando-se a vários outros sistemas.


### <a name="api"></a>API
Várias APIs estão disponíveis para ler e gravar logs e métricas do e para o Azure Monitor, além de acessar os alertas gerados. Você também pode configurar e recuperar alertas. Isso permite possibilidades ilimitadas para criar soluções personalizadas que se integram ao Azure Monitor.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre:

* [Métricas e logs](platform/data-platform.md) para os dados coletados pelo Azure Monitor.
* [Fontes de dados](platform/data-sources.md) para a maneira como os diferentes componentes do seu aplicativo enviam telemetria.
* [Consultas de log](log-query/log-query-overview.md) para analisar dados coletados.