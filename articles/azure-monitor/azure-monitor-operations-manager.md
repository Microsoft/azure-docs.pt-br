---
title: Azure Monitor para clientes Operations Manager existentes
description: Diretrizes para usuários existentes de Operations Manager fazer a transição do monitoramento de determinadas cargas de trabalho para Azure Monitor como parte de uma transição para a nuvem.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/11/2021
ms.openlocfilehash: 6d92b7c2f01a7e9ef12bc2bb422cfb6ed0076f73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039369"
---
# <a name="azure-monitor-for-existing-operations-manager-customers"></a>Azure Monitor para clientes Operations Manager existentes
Este artigo fornece diretrizes para os clientes que atualmente usam [System Center Operations Manager](/system-center/scom/welcome) e estão planejando uma transição para [Azure monitor](overview.md) à medida que migram aplicativos de negócios e outros recursos para o Azure. Ele pressupõe que seu objetivo final é uma transição completa para a nuvem, substituindo o máximo Operations Manager funcionalidade possível com Azure Monitor, sem comprometer seus requisitos operacionais de ti e de negócios. 

As recomendações específicas feitas neste artigo serão alteradas como Azure Monitor e Operations Manager adicionar recursos. No entanto, a estratégia fundamental permanecerá consistente.

> [!IMPORTANT]
> Há um custo para implementar vários recursos de Azure Monitor descritos aqui, portanto, você deve avaliar seu valor antes de implantar em todo o ambiente.

## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já usa [Operations Manager](/system-center/scom) e, pelo menos, tem uma compreensão básica de [Azure monitor](overview.md). Para obter uma comparação completa entre os dois, consulte [Guia de monitoramento de nuvem: visão geral das plataformas de monitoramento](/azure/cloud-adoption-framework/manage/monitor/platform-overview). Esse artigo detalha as diferenças de recursos específicas entre os dois para ajudá-lo a entender algumas das recomendações feitas aqui. 


## <a name="general-strategy"></a>Estratégia geral
Não há ferramentas de migração para converter ativos de Operations Manager para Azure Monitor, já que as plataformas são fundamentalmente diferentes. Em vez disso, sua migração constituirá uma [implementação de Azure monitor padrão](deploy.md) enquanto você continua a usar Operations Manager. Ao personalizar Azure Monitor para atender aos seus requisitos para diferentes aplicativos e componentes, e como ele obtém mais recursos, você pode começar a desativar diferentes pacotes de gerenciamento e agentes no Operations Manager.

A estratégia geral recomendada neste artigo é a mesma do [Guia de monitoramento de nuvem](/azure/cloud-adoption-framework/manage/monitor/), que recomenda uma estratégia de [monitoramento de nuvem híbrida](/azure/cloud-adoption-framework/manage/monitor/cloud-models-monitor-overview#hybrid-cloud-monitoring) que permite fazer uma transição gradual para a nuvem. Embora alguns recursos possam se sobrepor, essa estratégia permitirá que você mantenha seus processos de negócios existentes à medida que se familiarizar mais com a nova plataforma. Afaste-se da funcionalidade Operations Manager, pois você pode substituí-la por Azure Monitor. O uso de várias ferramentas de monitoramento adiciona complexidade, mas permite que você tire proveito da capacidade do Azure Monitor de monitorar as cargas de trabalho de nuvem da próxima geração e, ao mesmo tempo, manter a capacidade do Operations Manager de monitorar o software do servidor e os componentes da infraestrutura que podem ser locais ou em outras nuvens. 


## <a name="components-to-monitor"></a>Componentes a serem monitorados
Ele ajuda a categorizar os diferentes tipos de cargas de trabalho que você precisa monitorar para determinar uma estratégia de monitoramento distinta para cada uma. [Guia de monitoramento de nuvem: formular uma estratégia de monitoramento](/azure/cloud-adoption-framework/strategy/monitoring-strategy#high-level-modeling) fornece uma análise detalhada das diferentes camadas em seu ambiente que precisam de monitoramento à medida que você progride de aplicativos empresariais herdados para aplicativos modernos na nuvem.

Antes da nuvem, você usou Operations Manager para monitorar todas as camadas. Ao iniciar sua transição com o IaaS (infraestrutura como serviço), você continuará a usar Operations Manager para suas máquinas virtuais, mas começará a usar Azure Monitor para seus recursos de nuvem. Ao fazer uma transição adicional para aplicativos modernos usando PaaS (plataforma como serviço), você pode se concentrar mais em Azure Monitor e começar a desativar Operations Manager funcionalidade.


![Modelos de nuvem](/azure/cloud-adoption-framework/strategy/media/monitoring-strategy/cloud-models.png)

Essas camadas podem ser simplificadas nas categorias a seguir, que são descritas no restante deste artigo. Embora cada carga de trabalho de monitoramento em seu ambiente possa não se ajustar perfeitamente a uma dessas categorias, cada uma delas deve ser próxima de uma determinada categoria para que as recomendações gerais se apliquem.

**Aplicativos de negócios.** Aplicativos que fornecem funcionalidade específica ao seu negócio. Elas podem ser internas ou externas e geralmente são desenvolvidas internamente usando código personalizado. Seus aplicativos herdados normalmente serão hospedados em máquinas virtuais ou físicas que executam o Windows ou o Linux, enquanto seus aplicativos mais recentes serão baseados em serviços de aplicativos no Azure, como aplicativos Web do Azure e Azure Functions.

**Serviços do Azure.** Recursos no Azure que dão suporte a seus aplicativos de negócios que foram migrados para a nuvem. Isso inclui serviços como o armazenamento do Azure, SQL do Azure e IoT do Azure. Isso também inclui máquinas virtuais do Azure, pois elas são monitoradas como outros serviços do Azure, mas os aplicativos e o software em execução no sistema operacional convidado dessas máquinas virtuais exigem mais monitoramento além do host.

**Software do servidor.** Software em execução em máquinas virtuais e físicas que dão suporte a aplicativos de negócios ou aplicativos empacotados que fornecem funcionalidade geral ao seu negócio. Os exemplos incluem o Internet Information Server (IIS), o SQL Server, o Exchange e o SharePoint. Isso também inclui o sistema operacional Windows ou Linux em suas máquinas virtuais e físicas.

**Infraestrutura local.** Componentes específicos do seu ambiente local que exigem monitoramento. Isso inclui recursos como servidores físicos, armazenamento e componentes de rede. Esses são os componentes que são virtualizados quando você muda para a nuvem.

## <a name="sample-walkthrough"></a>Passo a passo de exemplo
Veja a seguir uma explicação hipotética de uma migração de Operations Manager para Azure Monitor. Isso não se destina a representar a complexidade total de uma migração real, mas, pelo menos, fornece as etapas e sequência básicas. As seções a seguir descrevem cada uma dessas etapas mais detalhadamente.

Seu ambiente antes de mover qualquer componente para o Azure é baseado em máquinas virtuais e físicas localizadas localmente ou com um provedor de serviços gerenciado. Ele se baseia em Operations Manager monitorar aplicativos de negócios, software de servidor e outros componentes de infraestrutura em seu ambiente, como servidores físicos e redes. Você usa pacotes de gerenciamento padrão para software de servidor, como IIS, SQL Server e vários softwares de fornecedores, e ajusta esses pacotes de gerenciamento para seus requisitos específicos. Você cria pacotes de gerenciamento personalizados para seus aplicativos de negócios e outros componentes que não podem ser monitorados com os pacotes de gerenciamento existentes e configura Operations Manager para dar suporte aos seus processos de negócios.

Sua migração para o Azure começa com IaaS, movendo máquinas virtuais que dão suporte a aplicativos de negócios no Azure. Os requisitos de monitoramento para esses aplicativos e o software de servidor de que eles dependem não são alterados e você continua usando Operations Manager nesses servidores com seus pacotes de gerenciamento existentes. 

O Azure Monitor está habilitado para seus serviços do Azure assim que você cria uma assinatura do Azure. Ele coleta automaticamente as métricas de plataforma e o log de atividades, e você configura os logs de recursos a serem coletados para que você possa analisar interativamente toda a telemetria disponível usando consultas de log. Você permite que as informações da VM em suas máquinas virtuais analisem os dados de monitoramento em todo o ambiente em conjunto e descubram relações entre máquinas e processos. Você estende o uso de Azure Monitor para suas máquinas físicas e virtuais locais habilitando os servidores habilitados para o Azure Arc neles. 

Você habilita Application Insights para cada um de seus aplicativos de negócios. Ele identifica os diferentes componentes de cada aplicativo, começa a coletar dados de uso e de desempenho e identifica os erros que ocorrem no código. Você cria testes de disponibilidade para testar proativamente seus aplicativos externos e alertá-lo sobre problemas de desempenho ou disponibilidade. Embora Application Insights forneça recursos poderosos que você não tem em Operations Manager, você continua a contar com pacotes de gerenciamento personalizados que você desenvolveu para seus aplicativos de negócios, pois eles incluem cenários de monitoramento ainda não cobertos pelo Azure Monitor. 

Ao se familiarizar com Azure Monitor, você começa a criar regras de alerta que podem substituir algumas funcionalidades do pacote de gerenciamento e começar a desenvolver seus processos de negócios para usar a nova plataforma de monitoramento. Isso permite que você inicie a remoção de computadores e pacotes de gerenciamento do Operations Manager grupo de gerenciamento. Você continua a usar pacotes de gerenciamento para o software de servidor crítico e a infraestrutura local, mas continua a observar novos recursos no Azure Monitor que permitirão que você desative a funcionalidade adicional.

## <a name="monitor-azure-services"></a>Monitorar serviços do Azure
Na verdade, os serviços do Azure exigem Azure Monitor para coletar telemetria e estão habilitados no momento em que você cria uma assinatura do Azure. O [log de atividades](essentials/activity-log.md) é coletado automaticamente para a assinatura, e as [métricas de plataforma](essentials/data-platform-metrics.md) são coletadas automaticamente de quaisquer recursos do Azure que você criar. Você pode começar imediatamente a usar o [Metrics Explorer](essentials/metrics-getting-started.md), que é semelhante às exibições de desempenho no console de operações, mas fornece análise interativa e [agregações avançadas](essentials/metrics-charts.md) de dados. [Crie um alerta de métrica](alerts/alerts-metric.md) para ser notificado quando um valor cruzar um limite ou [Adicionar um gráfico a um painel do Azure](essentials/metrics-charts.md#pinning-to-dashboards) para visibilidade.

[![Metrics Explorer](media/azure-monitor-operations-manager/metrics-explorer.png)](media/azure-monitor-operations-manager/metrics-explorer.png#lightbox)

[Crie uma configuração de diagnóstico](essentials/diagnostic-settings.md) para cada recurso do Azure para enviar métricas e [logs de recursos](essentials/resource-logs.md), que fornecem detalhes sobre a operação interna de cada recurso, para um espaço de trabalho log Analytics. Isso lhe dá toda a telemetria disponível para seus recursos e permite que você use [log Analytics](logs/log-analytics-overview.md) para analisar interativamente dados de desempenho e de log usando uma linguagem de consulta avançada que não tem nenhum equivalente no Operations Manager. Você também pode criar [alertas de consulta de log](alerts/alerts-log-query.md), que podem usar lógica complexa para determinar condições de alerta e correlacionar dados em vários recursos.

[![Análise de logs](media/azure-monitor-operations-manager/log-analytics.png)](media/azure-monitor-operations-manager/log-analytics.png#lightbox)

As [informações](monitor-reference.md) no Azure monitor são semelhantes aos pacotes de gerenciamento, pois fornecem monitoramento exclusivo para um determinado serviço do Azure. As informações estão disponíveis atualmente para vários serviços, incluindo rede, armazenamento e contêineres, e outras estão sendo adicionadas continuamente.

[![Exemplo de Insight](media/azure-monitor-operations-manager/insight.png)](media/azure-monitor-operations-manager/insight.png#lightbox)


As informações são baseadas em [pastas de trabalho](visualize/workbooks-overview.md) do Azure monitor, que combinam métricas e consultas de log em relatórios interativos sofisticados. Crie suas próprias pastas de trabalho para combinar dados de vários serviços de forma semelhante a como você pode criar exibições e relatórios personalizados no console de operações.

### <a name="azure-management-pack"></a>Pacote de gerenciamento do Azure
O [pacote de gerenciamento do Azure](https://www.microsoft.com/download/details.aspx?id=50013) permite que o Operations Manager descubra recursos do Azure e monitore sua integridade com base em um determinado conjunto de cenários de monitoramento. Esse pacote de gerenciamento exige que você execute uma configuração adicional para cada recurso no Azure, mas pode ser útil fornecer alguma visibilidade dos recursos do Azure no console de operações até que você evolua seus processos de negócios para se concentrar em Azure Monitor.

[![Pacote de gerenciamento do Azure](media/azure-monitor-operations-manager/operations-console.png)](media/azure-monitor-operations-manager/operations-console.png#lightbox)

 Você pode optar por usar o pacote de gerenciamento do Azure se quiser ter visibilidade para determinados recursos do Azure no console de operações e para integrar alguns alertas básicos com seus processos existentes. Na verdade, ele usa os dados coletados pelo Azure Monitor. Você deve procurar Azure Monitor no entanto para o monitoramento completo de longo prazo dos recursos do Azure. 


## <a name="monitor-server-software-and-local-infrastructure"></a>Monitorar o software do servidor e a infraestrutura local
Quando você move máquinas para a nuvem, os requisitos de monitoramento para seu software não são alterados. Você não precisa mais monitorar seus componentes físicos, pois eles são virtualizados, mas o sistema operacional convidado e suas cargas de trabalho têm os mesmos requisitos, independentemente de seu ambiente.

O [virtual insights](vm/vminsights-overview.md) é o principal recurso em Azure monitor para monitorar máquinas virtuais e seu sistema operacional convidado e cargas de trabalho. Semelhante à Operations Manager, o VM insights usa um agente para coletar dados do sistema operacional convidado de máquinas virtuais. Esse é o mesmo desempenho e dados de eventos geralmente usados por pacotes de gerenciamento para análise e alertas. Não há regras preexistentes para identificar e alertar sobre problemas para os aplicativos de negócios e o software de servidor em execução nessas máquinas. Você deve criar suas próprias regras de alerta para ser notificado proativamente sobre quaisquer problemas detectados.

[![Desempenho de informações da VM](media/azure-monitor-operations-manager/vm-insights-performance.png)](media/azure-monitor-operations-manager/vm-insights-performance.png#lightbox)

Azure Monitor também não mede a integridade de diferentes aplicativos e serviços em execução em uma máquina virtual. Os alertas de métrica podem ser resolvidos automaticamente quando um valor cai abaixo de um limite, mas Azure Monitor atualmente não tem a capacidade de definir critérios de integridade para aplicativos e serviços em execução no computador, nem fornece a acumulação de integridade para agrupar a integridade dos componentes relacionados.

> [!NOTE]
> Um novo [recurso de integridade de convidado para as informações de VM](vm/vminsights-health-overview.md) está agora em visualização pública e alerta com base no estado de integridade de um conjunto de métricas de desempenho. Isso se limita inicialmente a um conjunto específico de contadores de desempenho relacionados ao sistema operacional convidado, e não a aplicativos ou outras cargas de trabalho em execução na máquina virtual.
> 
> [![Integridade de convidado do insights de VM](media/azure-monitor-operations-manager/vm-insights-guest-health.png)](media/azure-monitor-operations-manager/vm-insights-guest-health.png#lightbox)

O monitoramento do software em seus computadores em um ambiente híbrido normalmente usará uma combinação de informações de VM e Operations Manager, dependendo dos requisitos de cada máquina e da maturidade do desenvolvimento de processos operacionais em todo o Azure Monitor. O agente de gerenciamento da Microsoft (conhecido como agente de Log Analytics no Azure Monitor) é usado por ambas as plataformas para que um único computador possa ser monitorado simultaneamente por ambos.

> [!NOTE]
> No futuro, as informações da VM passarão para o [agente de Azure monitor](agents/azure-monitor-agent-overview.md), que está atualmente em visualização pública. Ele será compatível com a Microsoft Monitoring Agent, de modo que a mesma máquina virtual continuará sendo capaz de ser monitorada por ambas as plataformas.

Continue a usar Operations Manager para a funcionalidade que ainda não pode ser fornecida pelo Azure Monitor. Isso inclui pacotes de gerenciamento para software de servidor crítico, como IIS, SQL Server ou Exchange. Você também pode ter pacotes de gerenciamento personalizados desenvolvidos para a infraestrutura local que não podem ser acessados com Azure Monitor. Além disso, continue a usar Operations Manager se estiver totalmente integrado aos seus processos operacionais até que você possa fazer a transição para modernizar suas operações de serviço em que Azure Monitor e outros serviços do Azure podem aumentar ou substituir. 

Use Azure Monitor fo VMs para aprimorar o monitoramento atual, mesmo que ele não substitua imediatamente Operations Manager. Exemplos de recursos exclusivos do Azure Monitor incluem o seguinte:

- Descubra e monitore relações entre máquinas virtuais e suas dependências externas.
- Exibir dados de desempenho agregados em várias máquinas virtuais em gráficos e pastas de trabalho interativas.
- Use [consultas de log](logs/log-query-overview.md) para analisar interativamente a telemetria de suas máquinas virtuais com dados de outros recursos do Azure.
- Crie [regras de alerta de log](alerts/alerts-log-query.md) com base em lógica complexa em várias máquinas virtuais.

[![Mapa de informações de VM](media/azure-monitor-operations-manager/vm-insights-map.png)](media/azure-monitor-operations-manager/vm-insights-map.png#lightbox)

Além das máquinas virtuais do Azure, as informações de VM podem monitorar máquinas locais e em outras nuvens usando [servidores habilitados para Arc do Azure](../azure-arc/servers/overview.md). Os servidores habilitados para Arc permitem que você gerencie seus computadores Windows e Linux hospedados fora do Azure, em sua rede corporativa ou em outro provedor de nuvem consistente com a maneira como gerencia máquinas virtuais nativas do Azure.



## <a name="monitor-business-applications"></a>Monitorar aplicativos de negócios
Normalmente, você precisa de pacotes de gerenciamento personalizados para monitorar seus aplicativos de negócios com Operations Manager, aproveitando os agentes instalados em cada máquina virtual. Application Insights no Azure Monitor monitora aplicativos baseados na Web se eles estiverem no Azure, em outras nuvens ou locais, para que possam ser usados para todos os seus aplicativos, independentemente de terem sido ou não migrados para o Azure.

Se o monitoramento de um aplicativo de negócios estiver limitado à funcionalidade fornecida pelo [modelo de desempenho do aplicativo .net]() no Operations Manager, você provavelmente poderá migrar para Application insights sem perda de funcionalidade. Na verdade, Application Insights incluirá um número significativo de recursos adicionais, incluindo o seguinte:

- Descubra e monitore automaticamente os componentes do aplicativo.
- Coletar dados de desempenho e de uso detalhados do aplicativo, como tempo de resposta, taxas de falha e taxas de solicitação.
- Coletar dados do navegador, como exibições de página e desempenho de carga.
- Detectar exceções e analisar o rastreamento de pilha e as solicitações relacionadas.
- Executar análise avançada usando recursos como [rastreamento distribuído](app/distributed-tracing.md) e [detecção inteligente](app/proactive-diagnostics.md).
- Use o [Metrics Explorer](essentials/metrics-getting-started.md) para analisar interativamente os dados de desempenho.
- Use [consultas de log](logs/log-query-overview.md) para analisar interativamente a telemetria coletada junto com os dados coletados para os serviços do Azure e as informações de VM.

[![Application Insights](media/azure-monitor-operations-manager/application-insights.png)](media/azure-monitor-operations-manager/application-insights.png#lightbox)

No entanto, há alguns cenários em que talvez seja necessário continuar usando Operations Manager além de Application Insights até que você possa obter a funcionalidade necessária. Os exemplos em que talvez você precise continuar com Operations Manager incluem o seguinte:

-  [Testes de disponibilidade](app/monitor-web-app-availability.md), que permitem monitorar e alertar sobre a disponibilidade e a capacidade de resposta de seus aplicativos exigem solicitações de entrada dos endereços IP dos agentes de teste na Web. Se sua política não permitir esse acesso, talvez seja necessário continuar usando [monitores de disponibilidade de aplicativos Web](/system-center/scom/web-application-availability-monitoring-template) no Operations Manager.
- No Operations Manager você pode definir qualquer intervalo de sondagem para testes de disponibilidade, com muitos clientes verificando a cada 60-120 segundos. Application Insights tem um intervalo de sondagem mínimo de 5 minutos, o que pode ser muito longo para alguns clientes.
- Uma quantidade significativa de monitoramento no Operations Manager é realizada pela coleta de eventos gerados por aplicativos e pela execução de scripts no agente local. Essas não são opções padrão no Application Insights, portanto, você pode exigir trabalho personalizado para atingir seus requisitos de negócios. Isso pode incluir regras de alerta personalizadas usando dados de eventos armazenados em um espaço de trabalho Log Analytics e os scripts iniciados em um convidado de máquinas virtuais usando [Hybrid runbook Worker](../automation/automation-hybrid-runbook-worker.md).
- Dependendo do idioma em que seu aplicativo está escrito, você poderá ser limitado na [instrumentação que você pode usar com Application insights](app/platforms.md).

Seguindo a estratégia básica nas outras seções deste guia, continue a usar Operations Manager para seus aplicativos de negócios, mas aproveite os recursos adicionais fornecidos pelo Application Insights. Como é possível substituir a funcionalidade crítica por Azure Monitor, você pode começar a desativar seus pacotes de gerenciamento personalizados.


## <a name="next-steps"></a>Próximas etapas

- Consulte o [Guia de monitoramento de nuvem](/azure/cloud-adoption-framework/manage/monitor/) para obter uma comparação detalhada de Azure Monitor e System Center Operations Manager e mais detalhes sobre como projetar e implementar um ambiente de monitoramento híbrido.
- Leia mais sobre como [monitorar recursos do Azure no Azure monitor](essentials/monitor-azure-resource.md).
- Leia mais sobre como [monitorar máquinas virtuais do Azure no Azure monitor](vm/monitor-vm-azure.md).
- Leia mais sobre o [VM insights](vm/vminsights-overview.md).
- Leia mais sobre [Application insights](app/app-insights-overview.md).