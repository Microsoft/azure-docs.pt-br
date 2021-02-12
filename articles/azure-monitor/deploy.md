---
title: Implantar o Azure Monitor
description: Descreve as diferentes etapas necessárias para uma implementação completa de Azure Monitor monitorar todos os recursos em sua assinatura do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: a0591825bf187648293d5aabc88597b19ab2436e
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491045"
---
# <a name="deploy-azure-monitor"></a>Implantar o Azure Monitor
A habilitação do Azure Monitor para monitorar todos os seus recursos do Azure é uma combinação de configuração de componentes do Azure Monitor e configuração de recursos do Azure para gerar dados de monitoramento para Azure Monitor coletar. Este artigo descreve as diferentes etapas necessárias para uma implementação completa de Azure Monitor usando uma configuração comum para monitorar todos os recursos em sua assinatura do Azure. Descrições básicas para cada etapa são fornecidas com links para outras documentações para requisitos de configuração detalhados.

> [!IMPORTANT]
> Os recursos de Azure Monitor e sua configuração variam de acordo com os requisitos de negócios equilibrados com o custo dos recursos habilitados. Cada etapa abaixo identificará se há custo potencial e você deve avaliar esses custos antes de prosseguir com essa etapa. Consulte [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter detalhes de preços completos.

## <a name="configuration-goals"></a>Metas de configuração
O objetivo de uma implementação completa do Azure Monitor é coletar todos os dados disponíveis de todos os seus recursos e aplicativos de nuvem e habilitar o máximo de recursos em Azure Monitor possível com base nesses dados.

Os dados coletados pelo Azure Monitor são enviados para [Azure monitor métricas](platform/data-platform-metrics.md) ou [logs de Azure monitor](platform/data-platform-logs.md). Cada loja armazena diferentes tipos de dados e permite diferentes tipos de análise e alertas. Consulte [comparar Azure monitor métricas e logs](platform/data-platform.md) para obter uma comparação dos dois e [visão geral dos alertas no Microsoft Azure](platform/alerts-overview.md) para obter uma descrição de diferentes tipos de alertas. 

Alguns dados podem ser enviados para métricas e logs a fim de aproveitá-los usando diferentes recursos. Nesses casos, talvez seja necessário configurar cada separadamente. Por exemplo, os dados de métrica são enviados automaticamente pelos recursos do Azure para métricas, que dão suporte a métricas e alertas de métricas. Você precisa criar uma configuração de diagnóstico para cada recurso para enviar os mesmos dados de métrica aos logs, o que permite que você analise as tendências de desempenho com outros dados de log usando Log Analytics. As seções a seguir identificam onde os dados são enviados e incluem cada etapa necessária para enviar dados a todos os locais possíveis.

Você pode ter requisitos adicionais, como monitoramento de recursos fora do Azure e envio de dados fora do Azure Monitor. Requisitos como esses podem ser obtidos com configuração adicional dos recursos descritos neste artigo. Siga os links para a documentação em cada etapa para obter opções de configuração adicionais.

## <a name="collect-data-from-azure-resources"></a>Coletar dados de recursos do Azure

> [!NOTE]
> Consulte [monitoramento de recursos do Azure com o Azure monitor](insights/monitor-azure-resource.md) para obter um guia completo sobre como monitorar máquinas virtuais com o Azure monitor.

Alguns monitoramentos de recursos do Azure está disponível automaticamente sem nenhuma configuração necessária, enquanto você deve executar etapas de configuração para coletar dados de monitoramento adicionais. A tabela a seguir ilustra as etapas de configuração necessárias para coletar todos os dados disponíveis de seus recursos do Azure, incluindo em quais dados de etapa são enviados para Azure Monitor métricas e Azure Monitor logs. As seções a seguir descrevem cada etapa mais detalhadamente.

[![Implantar o monitoramento ](media/deploy/deploy-azure-resources.png) de recursos do Azure](media/deploy/deploy-azure-resources.png#lightbox)

### <a name="no-configuration"></a>Nenhuma configuração
Os seguintes recursos do Azure Monitor estão habilitados sem nenhuma configuração necessária quando você cria uma assinatura do Azure. Não há nenhum custo associado a esse monitoramento.

[Logs de Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) -fornece histórico de nível de locatário de atividade de entrada e trilha de auditoria de alterações feitas no Azure Active Directory. Confira [relatórios de atividade de auditoria no portal de Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) e nos [relatórios de atividade de entrada no portal de Azure Active Directory](../active-directory/reports-monitoring/concept-sign-ins.md) para obter detalhes dos logs de Azure Active Directory e como exibi-los no portal do Azure.

[Log de atividades](platform/platform-logs-overview.md) -fornece informações sobre o grupo de gerenciamento e eventos de nível de assinatura que ocorreram no Azure. Os eventos são gravados automaticamente no log de atividades quando você cria um novo recurso do Azure, modifica um recurso ou executa uma atividade significativa. Você pode exibir eventos no portal do Azure e criar alertas do log de atividades quando determinados eventos forem criados. Consulte [log de atividades do Azure](platform/activity-log.md) para obter detalhes do log de atividades e como exibi-los no portal do Azure.

[Métricas de plataforma](platform/metrics-supported.md) – coletadas automaticamente dos serviços do Azure para [Azure monitor métricas](platform/data-platform-metrics.md). Esses dados são geralmente apresentados na página **visão geral** do portal do Azure para diferentes serviços. Consulte [introdução ao Azure Metrics Explorer](platform/metrics-getting-started.md) para obter detalhes sobre como analisar métricas de plataforma no portal do Azure. 


### <a name="create-log-analytics-workspace"></a>Criar espaço de trabalho do Log Analytics
Você precisa de pelo menos um espaço de trabalho Log Analytics para habilitar [logs de Azure monitor](platform/data-platform-logs.md), o que é necessário para coletar dados como logs de recursos do Azure, coletar dados do sistema operacional convidado de máquinas virtuais do Azure e para a maioria dos Azure monitor insights. Outros serviços, como o Azure Sentinel e a central de segurança do Azure, também usam um espaço de trabalho Log Analytics e podem compartilhar o mesmo que você usa para Azure Monitor. Você pode começar com um único espaço de trabalho para dar suporte a esse monitoramento, mas consulte  [projetando sua implantação de Azure monitor logs](platform/design-logs-deployment.md) para obter orientação sobre quando usar vários espaços de trabalho.

Não há nenhum custo para criar um espaço de trabalho Log Analytics, mas há uma cobrança em potencial quando você configura os dados a serem coletados nele. Confira [Gerenciar o uso e os custos com os Logs do Azure Monitor](platform/manage-cost-storage.md) para obter detalhes.  

Consulte [criar um log Analytics espaço de trabalho no portal do Azure](learn/quick-create-workspace.md) para criar um espaço de trabalho inicial do log Analytics. Consulte [gerenciar o acesso a dados de log e espaços de trabalho no Azure monitor](platform/manage-access.md) para configurar o acesso. 

### <a name="create-diagnostic-setting-to-collect-tenant-and-subscription-logs"></a>Criar configuração de diagnóstico para coletar logs de locatário e assinatura
Embora os [logs de Azure Active Directory](../active-directory/reports-monitoring/overview-reports.md) para seu locatário e o [log de atividades](platform/platform-logs-overview.md) de sua assinatura sejam coletados automaticamente, enviá-los para um espaço de trabalho log Analytics permite que você analise esses eventos com outros dados de log usando consultas de log em log Analytics. Isso também permite que você crie alertas de consulta de log, que é a única maneira de alertar sobre logs de Azure Active Directory e fornecer lógica mais complexa do que os alertas do log de atividades.

Não há nenhum custo para enviar o log de atividades para um espaço de trabalho, mas há uma ingestão de dados e um encargo de retenção para logs de Azure Active Directory. 

Consulte [integrar logs do Azure AD com logs de Azure monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) e [criar configurações de diagnóstico para enviar logs e métricas de plataforma para destinos diferentes](platform/diagnostic-settings.md) para criar uma configuração de diagnóstico para seu locatário e assinatura para enviar entradas de log para o seu espaço de trabalho log Analytics. 

### <a name="create-diagnostic-setting-to-collect-resource-logs-and-platform-metrics"></a>Criar configuração de diagnóstico para coletar logs de recursos e métricas de plataforma
Os recursos no Azure geram automaticamente [logs de recursos](platform/platform-logs-overview.md) que fornecem detalhes das operações executadas no recurso. No entanto, ao contrário das métricas de plataforma, você precisa configurar os logs de recursos a serem coletados. Crie uma configuração de diagnóstico para enviá-las a um espaço de trabalho Log Analytics para combiná-las com os outros dados usados com os logs de Azure Monitor. A mesma configuração de diagnóstico pode ser usada para também enviar as métricas de plataforma para a maioria dos recursos para o mesmo espaço de trabalho, o que permite que você analise os dados de métrica usando consultas de log com outros dados coletados.

Há um custo para essa coleção, portanto, consulte os [preços de Azure monitor](https://azure.microsoft.com/pricing/details/monitor/) antes de implementar em um número significativo de recursos. Consulte também [gerenciar o uso e os custos com os logs de Azure monitor](platform/manage-cost-storage.md) para obter detalhes sobre como otimizar o custo da sua coleção de logs.

Consulte [criar configuração de diagnóstico para coletar logs de recursos e métricas no Azure](platform/diagnostic-settings.md#create-in-azure-portal) para criar uma configuração de diagnóstico para um recurso do Azure. Como uma configuração de diagnóstico precisa ser criada para cada recurso do Azure, consulte [implantar Azure monitor em escala](deploy-scale.md) para obter detalhes sobre como usar a [política do Azure](../governance/policy/overview.md) para que as configurações sejam criadas automaticamente cada vez que um recurso do Azure for criado.

### <a name="enable-insights-and-solutions"></a>Habilitar informações e soluções
As informações e soluções fornecem monitoramento especializado para um serviço ou uma solução específica. As informações usam recursos mais recentes de Azure Monitor como pastas de trabalho, portanto, você deve usar um insight se estiver disponível para seu serviço. Eles ficam automaticamente disponíveis em todas as assinaturas do Azure, mas podem exigir alguma configuração para funcionalidade completa. Normalmente, eles usarão os logs de recursos e métricas de plataforma que você configurou anteriormente e podem coletar dados adicionais.

As soluções devem ser adicionadas a cada assinatura e funcionam exclusivamente com dados de logs de Azure Monitor e podem coletar dados de log adicionais.

Não há nenhum custo para soluções ou ideias, mas você pode ser cobrado por todos os dados coletados.

Veja [o que é monitorado pelo Azure monitor?](monitor-reference.md) para obter uma lista de informações e soluções disponíveis no Azure monitor. Consulte a documentação para cada uma das informações exclusivas de configuração ou preço. 

## <a name="collect-data-from-virtual-machines"></a>Coletar dados de máquinas virtuais

> [!NOTE]
> Consulte [monitorando máquinas virtuais do Azure com Azure monitor](insights/monitor-vm-azure.md) para obter um guia completo sobre como monitorar máquinas virtuais com o Azure monitor. 

As máquinas virtuais geram dados semelhantes como outros recursos do Azure, mas você precisa de um agente para coletar dados do sistema operacional convidado. Consulte [visão geral dos agentes de Azure monitor](platform/agents-overview.md) para obter uma comparação dos agentes usados pelo Azure monitor. 

[Azure monitor para VMs](insights/vminsights-overview.md) usa o agente de log Analytics e o agente de dependência para coletar dados do sistema operacional convidado de máquinas virtuais, para que você possa implantar esses agentes como parte da implementação dessa percepção. Isso habilita o agente de Log Analytics para outros serviços que o utilizam, como a central de segurança do Azure.


[![Implantar a VM ](media/deploy/deploy-azure-vm.png) do Azure](media/deploy/deploy-azure-vm.png#lightbox)


### <a name="configure-workspace-for-azure-monitor-for-vms"></a>Configurar espaço de trabalho para Azure Monitor para VMs
Azure Monitor para VMs requer um espaço de trabalho Log Analytics, que normalmente será o mesmo criado para coletar dados de outros recursos do Azure. Antes de habilitar qualquer máquina virtual, você deve adicionar a solução necessária para Azure Monitor para VMs ao espaço de trabalho.

Consulte [Configurar o espaço de trabalho log Analytics para Azure monitor para VMs](insights/vminsights-configure-workspace.md) para obter detalhes sobre como configurar seu espaço de trabalho Log Analytics para Azure monitor para VMs.

### <a name="enable-azure-monitor-for-vms-on-each-virtual-machine"></a>Habilitar Azure Monitor para VMs em cada máquina virtual
Depois que um espaço de trabalho tiver sido configurado, você poderá habilitar cada máquina virtual instalando o agente de Log Analytics e o agente de dependência. Há vários métodos para instalar esses agentes, incluindo Azure Policy que permite configurar automaticamente cada máquina virtual à medida que ela é criada. Os dados de desempenho e os detalhes do processo coletados pelo Azure Monitor para VMs são armazenados em logs do Azure Monitor.

Consulte [habilitar Azure monitor para VMs visão geral](insights/vminsights-enable-overview.md) para obter opções para implantar os agentes em suas máquinas virtuais e habilitá-los para monitoramento.

### <a name="configure-workspace-to-collect-events"></a>Configurar espaço de trabalho para coletar eventos
Azure Monitor para VMs coletará dados de desempenho e os detalhes e as dependências dos processos do sistema operacional convidado de cada máquina virtual. O agente de Log Analytics também pode coletar logs do convidado, incluindo o log de eventos do Windows e do syslog do Linux. Ele recupera a configuração para esses logs do espaço de trabalho Log Analytics ao qual ele está conectado. Você só precisa configurar o espaço de trabalho uma vez e sempre que um agente se conecta, ele baixará as alterações de configuração. 

Consulte [fontes de dados do agente no Azure monitor](platform/agent-data-sources.md) para obter detalhes sobre como configurar seu espaço de trabalho do log Analytics para coletar dados adicionais de suas máquinas virtuais do agente.

> [!NOTE]
> Você também pode configurar o espaço de trabalho para coletar contadores de desempenho, mas isso provavelmente será redundante com dados de desempenho coletados pelo Azure Monitor para VMs. Os dados de desempenho coletados pelo espaço de trabalho serão armazenados na tabela *perf* , enquanto os dados de desempenho coletados pelo Azure monitor para VMs são armazenados na tabela *InsightsMetrics* . Configure a coleta de desempenho no espaço de trabalho somente se você precisar de contadores que ainda não foram coletados pelo Azure Monitor para VMs.

### <a name="diagnostic-extension-and-telegraf-agent"></a>Extensão de diagnóstico e agente Telegraf
Azure Monitor para VMs usa o agente de Log Analytics que envia dados de desempenho para um espaço de trabalho Log Analytics, mas não para Azure Monitor métricas. O envio desses dados às métricas permite que ele seja analisado com Metrics Explorer e usado com alertas de métrica. Isso requer a extensão de diagnóstico no Windows e o agente Telegraf no Linux.

Consulte [instalar e configurar a extensão de diagnóstico do Windows Azure (wad)](platform/diagnostics-extension-windows-install.md) e [coletar métricas personalizadas para uma VM do Linux com o agente Telegraf do InfluxData](platform/collect-custom-metrics-linux-telegraf.md) para obter detalhes sobre como instalar e configurar esses agentes.


## <a name="monitor-applications"></a>Monitorar aplicativos
O Azure Monitor monitora seus aplicativos personalizados usando [Application insights](app/app-insights-overview.md), que você deve configurar para cada aplicativo que você deseja monitorar. O processo de configuração irá variar dependendo do tipo de aplicativo que está sendo monitorado e do tipo de monitoramento que você deseja executar. Os dados coletados pelo Application Insights são armazenados em métricas de Azure Monitor, logs de Azure Monitor e armazenamento de BLOBs do Azure, dependendo do recurso. Os dados de desempenho são armazenados em Azure Monitor métricas e Azure Monitor logs sem nenhuma configuração adicional necessária.

### <a name="create-an-application-resource"></a>Criar um recurso de aplicativo
Você deve criar um recurso no Application Insights para cada aplicativo que pretende monitorar. Os dados de log coletados pelo Application Insights são armazenados em logs de Azure Monitor para um aplicativo baseado em espaço de trabalho. Os dados de log para aplicativos clássicos são armazenados separados do seu espaço de trabalho Log Analytics, conforme descrito em [estrutura de dados](platform/data-platform-logs.md#data-structure).

 Ao criar o aplicativo, você deve selecionar se deseja usar o clássico ou o baseado no espaço de trabalho. Consulte [criar um recurso de Application insights](app/create-new-resource.md) para criar um aplicativo clássico. Consulte [recursos de Application insights baseados em espaço de trabalho (versão prévia)](app/create-workspace-resource.md) para criar um aplicativo baseado em espaço de trabalho.

### <a name="configure-codeless-or-code-based-monitoring"></a>Configurar o monitoramento baseado em código ou em código
Para habilitar o monitoramento de um aplicativo, você deve decidir se usará o monitoramento de código ou baseado em código. O processo de configuração variará dependendo dessa decisão e do tipo de aplicativo que você pretende monitorar.

O **monitoramento de código** não é mais fácil de implementar e pode ser configurado após o desenvolvimento de código. Ele não exige nenhuma atualização para seu código. Consulte os recursos a seguir para obter detalhes sobre como habilitar o monitoramento dependendo do seu aplicativo.

- [Aplicativos hospedados em aplicativos Web do Azure](app/azure-web-apps.md)
- [Aplicativos Java](app/java-in-process-agent.md)
- [Aplicativos ASP.NET hospedados no IIS na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure](app/azure-vm-vmss-apps.md)
- [Aplicativos ASP.NET hospedados na VM local do IIS](app/monitor-performance-live-website-now.md)


O **monitoramento baseado em código** é mais personalizável e coleta telemetria adicional, mas requer a adição de uma dependência ao seu código nos pacotes NUGET do SDK Application insights. Consulte os recursos a seguir para obter detalhes sobre como habilitar o monitoramento dependendo do seu aplicativo.

- [Aplicativos ASP.NET](app/asp-net.md)
- [Aplicativo ASP.NET Core](app/asp-net-core.md)
- [Aplicativos de Console .NET](app/console.md)
- [Java](app/java-get-started.md)
- [Node.js](app/nodejs.md)
- [Python](app/opencensus-python.md)
- [Outras plataformas](app/platforms.md)

### <a name="configure-availability-testing"></a>Configurar testes de disponibilidade
Os testes de disponibilidade no Application Insights são testes recorrentes que monitoram a disponibilidade e a capacidade de resposta do seu aplicativo em intervalos regulares de pontos em todo o mundo. Você pode criar um teste de ping simples gratuitamente ou criar uma sequência de solicitações da Web para simular transações de usuário com custo associado. 

Consulte [monitorar a disponibilidade de qualquer site](app/monitor-web-app-availability.md) para obter o resumo dos diferentes tipos de teste e detalhes sobre como criá-los.

### <a name="configure-profiler"></a>Configurar o criador de perfil
O criador de perfil no Application Insights fornece rastreamentos de desempenho para aplicativos .NET. Ele ajuda a identificar o caminho de código "quente" que leva o tempo mais longo quando trata de uma solicitação da Web específica. O processo para configurar o criador de perfil varia dependendo do tipo de aplicativo. 

Consulte [perfil aplicativos de produção no Azure com Application insights](app/profiler-overview.md) para obter detalhes sobre como configurar o profiler.

### <a name="configure-snapshot-debugger"></a>Configurar Depurador de Instantâneos
Depurador de Instantâneos no Application Insights monitora a telemetria de exceção do seu aplicativo .NET e coleta instantâneos em suas exceções de lançamento superior para que você tenha as informações necessárias para diagnosticar problemas na produção. O processo de configuração de Depurador de Instantâneos varia de acordo com o tipo de aplicativo. 

Consulte [depurar instantâneos em exceções em aplicativos .net](app/snapshot-debugger.md) para obter detalhes sobre como configurar depurador de instantâneos.


## <a name="visualize-data"></a>Visualizar dados
As informações e as soluções incluirão suas próprias pastas de trabalho e exibições para a análise de seus dados. Além disso, você pode criar suas próprias [visualizações](visualizations.md) , incluindo pastas de trabalho para Azure monitor dados e painéis, a fim de combinar Azure monitor dados com dados de outros serviços no Azure.


### <a name="create-workbooks"></a>Criar pastas de trabalho
As [pastas de trabalho](platform/workbooks-overview.md) no Azure monitor permitem que você crie relatórios visuais avançados na portal do Azure. Você pode combinar diferentes conjuntos de dados de Azure Monitor métricas e Azure Monitor logs para criar experiências interativas unificadas. Você pode acessar uma galeria de pastas de trabalho na guia **pastas de trabalho** do menu Azure monitor. 

Consulte [Azure monitor pastas de trabalho](platform/workbooks-overview.md) para obter detalhes sobre como criar pastas de trabalho personalizadas.

### <a name="create-dashboards"></a>Crie painéis
Os [painéis do Azure](../azure-portal/azure-portal-dashboards.md) são a principal tecnologia de painel para o Azure e permitem que você combine Azure monitor dados com dados de outros serviços para fornecer um único painel de vidro em sua infraestrutura do Azure. Consulte [criar e compartilhar painéis de dados de log Analytics](learn/tutorial-logs-dashboards.md) para obter detalhes sobre como criar um painel que inclui dados de Logs de Azure monitor. 

Consulte [criar painéis de KPI personalizados usando o aplicativo Azure insights](learn/tutorial-app-dashboards.md) para obter detalhes sobre como criar um painel que inclui dados de Application insights. 

## <a name="alerts"></a>Alertas
Alertas no Azure Monitor notificá-lo proativamente de dados importantes ou padrões identificados em seus dados de monitoramento. Algumas informações irão gerar alertas sem configuração. Para outros cenários, você precisa criar [regras de alerta](platform/alerts-overview.md) que incluam os dados a serem analisados e os critérios para quando gerar um alerta e grupos de ações que definem a ação a ser tomada quando um alerta é gerado. 


### <a name="create-action-groups"></a>Criar grupos de ação
[Grupos de ação](platform/action-groups.md) são uma coleção de preferências de notificação usadas pelas regras de alerta para determinar a ação a ser executada quando um alerta é disparado. Exemplos de ações incluem enviar um email ou texto, chamar um webhook ou enviar dados para uma ferramenta de ITSM. Cada regra de alerta requer pelo menos um grupo de ações e um único grupo de ação pode ser usado por várias regras de alerta.

Consulte [criar e gerenciar grupos de ações no portal do Azure](platform/action-groups.md) para obter detalhes sobre como criar um grupo de ações e uma descrição das diferentes ações que ele pode incluir.


### <a name="create-alert-rules"></a>Criar regras de alerta
Há vários tipos de regras de alerta definidos pelo tipo de dados que eles usam. Cada um tem recursos diferentes e um custo diferente. A estratégia básica que você deve seguir é usar o tipo de regra de alerta com o menor custo que fornece a lógica necessária.

- [Regras do log de atividades](platform/activity-log-alerts.md). Cria um alerta em resposta a um novo evento do log de atividades que corresponde às condições especificadas. Não há nenhum custo para esses alertas, portanto, eles devem ser sua primeira opção. Consulte [criar, exibir e gerenciar alertas do log de atividades usando Azure monitor](platform/alerts-activity-log.md) para obter detalhes sobre como criar um alerta do log de atividades.
- [Regras de alerta de métrica](platform/alerts-metric-overview.md). Cria um alerta em resposta a um ou mais valores de métrica que excedem um limite. Os alertas de métricas são monitorados, o que significa que o alerta será fechado automaticamente quando o valor cair abaixo do limite e só enviará notificações quando o estado for alterado. Há um custo para alertas de métrica, mas isso é significativamente menor do que os alertas de log. Consulte [criar, exibir e gerenciar alertas de métricas usando Azure monitor](platform/alerts-metric.md) para obter detalhes sobre como criar um alerta de métrica.
- [Registrar em log as regras de alerta](platform/alerts-unified-log.md). Cria um alerta quando os resultados de uma consulta de agenda correspondem aos critérios especificados. Elas são as mais caras das regras de alerta, mas permitem os critérios mais complexos. Consulte [criar, exibir e gerenciar alertas de log usando Azure monitor](platform/alerts-log.md) para obter detalhes sobre como criar um alerta de consulta de log.
- Os [alertas de aplicativos](app/monitor-web-app-availability.md) permitem executar testes proativos e de disponibilidade de seu aplicativo Web. Você pode executar um teste de ping simples sem custo, mas há um custo para testes mais complexos. Consulte [monitorar a disponibilidade de qualquer site](app/monitor-web-app-availability.md) para obter uma descrição dos diferentes testes e detalhes sobre como criá-los.


## <a name="next-steps"></a>Próximas etapas

- Consulte [implantar Azure monitor em escala usando Azure Policy](deploy-scale.md).