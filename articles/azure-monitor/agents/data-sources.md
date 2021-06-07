---
title: Fontes de dados no Azure Monitor | Microsoft Docs
description: Descreve os dados disponíveis para monitorar a integridade e desempenho de seus recursos do Azure e dos aplicativos executados neles.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/06/2020
ms.openlocfilehash: 537163b4a78954443eeb7799dc624837ba03e2bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036615"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Fontes de dados de monitoramento para Azure Monitor
O Azure Monitor se baseia em uma [plataforma de dados de monitoramento comum](../data-platform.md) que inclui [logs](../logs/data-platform-logs.md) e [métricas](../essentials/data-platform-metrics.md). Coletar dados nessa plataforma permite que os dados de vários recursos sejam analisados juntos usando um conjunto comum de ferramentas no Azure Monitor. Os dados de monitoramento também podem ser enviados para outros locais para dar suporte a determinados cenários, e alguns recursos podem gravar em outros locais antes que possam ser coletados em logs ou métricas.

Este artigo descreve as diferentes fontes de monitoramento de dados coletadas por Azure Monitor além dos dados de monitoramento criados pelos recursos do Azure. São fornecidos links para informações detalhadas sobre a configuração necessária para coletar esses dados em locais diferentes.

## <a name="application-tiers"></a>Camadas de aplicativo

Fontes de dados de monitoramento de aplicativos do Azure podem ser organizadas em camadas, as camadas mais altas sendo seu próprio aplicativo e as camadas inferiores são componentes da plataforma do Azure. O método de acesso a dados de cada camada varia. As camadas de aplicativo são resumidas na tabela abaixo, e as fontes de dados de monitoramento em cada camada são apresentadas nas seções a seguir. Consulte [monitorando locais de dados no Azure](../monitor-reference.md) para obter uma descrição de cada local de dados e como você pode acessar seus dados.


![Camadas de monitoramento](../media/overview/overview.png)


### <a name="azure"></a>Azure
A tabela a seguir descreve brevemente as camadas de aplicativo que são específicas para o Azure. Seguindo o link para obter mais detalhes sobre cada uma das seções abaixo.

| Camada | Descrição | Método de coleta |
|:---|:---|:---|
| [Locatário do Azure](#azure-tenant) | Dados sobre a operação de serviços do Azure no nível do locatário, como o Azure Active Directory. | Exiba os dados do AAD no portal ou configure a coleção para Azure Monitor usando uma configuração de diagnóstico de locatário. |
| [Assinatura do Azure](#azure-subscription) | Dados relacionados à integridade e ao gerenciamento de serviços entre recursos em sua assinatura do Azure, como o Resource Manager e a integridade do serviço. | Exibir no portal ou configurar a coleta para Azure Monitor usando um perfil de log. |
| [Recursos do Azure](#azure-resources) |  Dados sobre a operação e o desempenho de cada recurso do Azure. | Métricas coletadas automaticamente, exiba em Metrics Explorer.<br>Defina as configurações de diagnóstico para coletar logs em Azure Monitor.<br>Monitoramento de soluções e informações disponíveis para o monitoramento mais detalhado de tipos de recursos específicos. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, outra nuvem ou local 
A tabela a seguir descreve brevemente as camadas de aplicativo que podem estar no Azure, em outra nuvem ou no local. Seguindo o link para obter mais detalhes sobre cada uma das seções abaixo.

| Camada | Descrição | Método de coleta |
|:---|:---|:---|
| [Sistema operacional (convidado)](#operating-system-guest) | Dados sobre o sistema operacional em recursos de computação. | Instale o agente de Log Analytics para coletar fontes de dados do cliente no Azure Monitor e no agente de dependência para coletar dependências que dão suporte a informações de VM.<br>Para máquinas virtuais do Azure, instale a extensão de diagnóstico do Azure para coletar logs e métricas em Azure Monitor. |
| [Código do aplicativo](#application-code) | Dados sobre o desempenho e a funcionalidade do aplicativo e do código reais, incluindo rastreamentos de desempenho, logs de aplicativos e telemetria de usuários. | Instrumente seu código para coletar dados em Application Insights. |
| [Fontes personalizadas](#custom-sources) | Dados de serviços externos ou outros componentes ou dispositivos. | Coletar dados de log ou métricas em Azure Monitor de qualquer cliente REST. |

## <a name="azure-tenant"></a>Locatário do Azure
A telemetria relacionada ao seu locatário do Azure é coletada de serviços de todos os locatários como o Azure Active Directory.

![Coleção de locatário do Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Logs de Auditoria do Azure Active Directory
Os [relatórios do Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md) contêm o histórico de atividade de entrada e a trilha de auditoria das alterações feitas em um locatário específico. 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Configure os logs do Azure AD a serem coletados no Azure Monitor para analisá-los com outros dados de monitoramento. | [Integrar logs do Azure AD com os logs do Azure Monitor (versão prévia)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Armazenamento do Azure | Exportar logs do Azure AD para o armazenamento do Azure para arquivamento. | [Tutorial: Arquivar logs do Azure AD em uma conta de armazenamento do Azure (versão prévia)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Hub de evento | Transmita logs do Azure AD para outros locais usando os hubs de eventos. | [Tutorial: transmitir logs de Azure Active Directory para um hub de eventos do Azure (versão prévia)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Assinatura do Azure
Telemetria relacionada à integridade e operação de sua assinatura do Azure.

![Assinatura do Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Log de Atividades do Azure 
O [log de atividades do Azure](../essentials/platform-logs-overview.md) inclui registros de integridade do serviço juntamente com registros em qualquer alteração de configuração feita aos recursos em sua assinatura do Azure. O Log de Atividades está disponível para todos os recursos do Azure e representa a exibição _externa_ desses recursos.

| Destino | Descrição | Referência |
|:---|:---|
| Log de atividades | O log de atividades é coletado em seu próprio armazenamento de dados que você pode exibir no menu Azure Monitor ou usar para criar alertas do log de atividades. | [Consultar o log de atividades no portal do Azure](../essentials/activity-log.md#view-the-activity-log) |
| Logs do Azure Monitor | Configure os logs de Azure Monitor para coletar o log de atividades para analisá-lo com outros dados de monitoramento. | [Coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](../essentials/activity-log.md) |
| Armazenamento do Azure | Exporte o log de atividades para o armazenamento do Azure para arquivamento. | [Log de atividades de arquivamento](../essentials/resource-logs.md#send-to-azure-storage)  |
| Hubs de Eventos | Transmitir o log de atividades para outros locais usando os hubs de eventos | [Transmita o log de atividades para o Hub de eventos](../essentials/resource-logs.md#send-to-azure-event-hubs). |

### <a name="azure-service-health"></a>Integridade do Serviço do Azure
[A Integridade do Serviço do Azure](../../service-health/service-health-overview.md) fornece informações sobre a integridade dos serviços do Azure na assinatura dos quais o aplicativo e os recursos dependem.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Log de atividades<br>Logs do Azure Monitor | Os registros de integridade do serviço são armazenados no log de atividades do Azure, para que você possa exibi-los no portal do Azure ou executar outras atividades que podem ser executadas com o log de atividades. | [Exibir as notificações de integridade do serviço usando o Portal do Azure](../../service-health/service-notifications.md) |


## <a name="azure-resources"></a>Recursos do Azure
Métricas e logs de recursos fornecem informações sobre a operação _interna_ dos recursos do Azure. Eles estão disponíveis para a maioria dos serviços do Azure, e as soluções e informações de monitoramento coletam dados adicionais para serviços específicos.

![Coleção de recursos do Azure](media/data-sources/data-source-azure-resources.svg)


### <a name="platform-metrics"></a>Métricas de plataforma 
A maioria dos serviços do Azure enviará [métricas de plataforma](../essentials/data-platform-metrics.md) que refletem seu desempenho e operação diretamente no banco de dados de métricas. As [métricas específicas variam para cada tipo de recurso](../essentials/metrics-supported.md). 

| Destino | Descrição | Referência |
|:---|:---|:---|
| Métricas do Azure Monitor | As métricas de plataforma serão gravadas no banco de dados de métricas Azure Monitor sem configuração. Acesse as métricas de plataforma de Metrics Explorer.  | [Introdução ao Azure Metrics Explorer](../essentials/metrics-getting-started.md)<br>[Métricas compatíveis com o Azure Monitor](../essentials/metrics-supported.md) |
| Logs do Azure Monitor | Copie métricas de plataforma para os logs para tendência e outras análises usando Log Analytics. | [Direcionar o diagnóstico do Azure para o Log Analytics](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Hubs de Eventos | Transmita métricas para outros locais usando os hubs de eventos. |[Transmitir dados de monitoramento do Azure para um hub de eventos para consumo por uma ferramenta externa](../essentials/stream-monitoring-data-event-hubs.md) |

### <a name="resource-logs"></a>Logs de recursos
[Os logs de recursos](../essentials/platform-logs-overview.md) fornecem informações sobre a operação _interna_ de um recurso do Azure.  Os logs de recursos são criados automaticamente, mas você deve criar uma configuração de diagnóstico para especificar um destino para que eles sejam coletados para cada recurso.

Os requisitos de configuração e o conteúdo dos logs de recursos variam por tipo de recurso e nem todos os serviços ainda os criam. Consulte [serviços, esquemas e categorias com suporte para logs de recursos do Azure](../essentials/resource-logs-schema.md) para obter detalhes sobre cada serviço e links para os procedimentos de configuração detalhados. Se o serviço não estiver listado neste artigo, esse serviço não criará logs de recursos no momento.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Envie logs de recursos para Azure Monitor logs para análise com outros dados de log coletados. | [Coletar logs de recursos do Azure no espaço de trabalho Log Analytics no Azure Monitor](../essentials/resource-logs.md#send-to-azure-storage) |
| Armazenamento | Envie logs de recursos para o armazenamento do Azure para arquivamento. | [Arquivar logs de recursos do Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) |
| Hubs de Eventos | Transmita logs de recursos para outros locais usando os hubs de eventos. |[Transmitir logs de recursos do Azure para um hub de eventos](../essentials/resource-logs.md#send-to-azure-event-hubs) |

## <a name="operating-system-guest"></a>Sistema operacional (convidado)
Recursos de computação no Azure, em outras nuvens e localmente têm um sistema operacional convidado para monitorar. Com a instalação de um ou mais agentes, você pode reunir a telemetria do convidado em Azure Monitor para analisá-lo com as mesmas ferramentas de monitoramento que os próprios serviços do Azure.

![Coleta de recursos de computação do Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensão de diagnóstico do Azure
Habilitar a extensão de Diagnóstico do Azure para máquinas virtuais do Azure permite coletar logs e métricas do sistema operacional convidado dos recursos de computação do Azure, incluindo funções de trabalho e Web do serviço de nuvem do Azure (clássico), máquinas virtuais, conjuntos de dimensionamento de máquinas virtuais e Service Fabric.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Armazenamento | A extensão de diagnóstico do Azure sempre grava em uma conta de armazenamento do Azure. | [Instalar e configurar a extensão WAD (Diagnóstico do Windows Azure)](./diagnostics-extension-windows-install.md)<br>[Usar a Extensão de Diagnóstico do Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md) |
| Métricas do Azure Monitor | Quando você configura a extensão de diagnóstico para coletar contadores de desempenho, eles são gravados no banco de dados de métricas Azure Monitor. | [Enviar métricas do sistema operacional convidado para o armazenamento de métrica Azure Monitor usando um modelo do Resource Manager para uma máquina virtual do Windows](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) |
| Hubs de Eventos | Configure a extensão de diagnóstico para transmitir os dados para outros locais usando os hubs de eventos.  | [Streaming de dados de Diagnóstico do Azure usando os hubs de eventos](./diagnostics-extension-stream-event-hubs.md)<br>[Use a Extensão de Diagnóstico Linux para monitorar as métricas e os logs](../../virtual-machines/extensions/diagnostics-linux.md) |
| Logs de Application Insights | Colete logs e contadores de desempenho do recurso de computação que dá suporte ao seu aplicativo para ser analisado com outros dados de aplicativo. | [Enviar dados de diagnóstico do Serviço de Nuvem, da máquina virtual ou do Service Fabric ao Application Insights](./diagnostics-extension-to-application-insights.md) |


### <a name="log-analytics-agent"></a>Agente do Log Analytics 
Instale o agente de Log Analytics para monitoramento e gerenciamento abrangentes de suas máquinas virtuais Windows ou Linux. A máquina virtual pode estar em execução no Azure, em outra nuvem ou localmente.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | O agente de Log Analytics se conecta a Azure Monitor diretamente ou por meio do System Center Operations Manager e permite coletar dados de fontes de dados que você configura ou de soluções de monitoramento que fornecem informações adicionais sobre aplicativos em execução na máquina virtual. | [Fontes de dados do agente no Azure Monitor](../agents/agent-data-sources.md)<br>[Conectar Operations Manager ao Azure Monitor](./om-agents.md) |
| Armazenamento de VM | O Revisions de VM usa o agente de Log Analytics para armazenar informações de estado integridade em um local personalizado. Para obter mais informações, confira a próxima seção.  |


### <a name="vm-insights"></a>Insights da VM 
O [virtual insights](../vm/vminsights-overview.md) fornece uma experiência de monitoramento Personalizada para máquinas virtuais que fornecem recursos além da funcionalidade de Azure monitor básica. Ele requer um Dependency Agent em máquinas virtuais Windows e Linux que se integre com o agente Log Analytics para coletar dados descobertos sobre processos em execução na máquina virtual e dependências de processo externas.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Armazena dados sobre processos e dependências no agente. | [Usando o mapa de informações de VM (versão prévia) para entender os componentes do aplicativo](../vm/vminsights-maps.md) |



## <a name="application-code"></a>Código do aplicativo
O monitoramento detalhado de aplicativos no Azure Monitor é feito com [Application insights](/azure/application-insights/) que coleta dados de aplicativos em execução em uma variedade de plataformas. O aplicativo pode estar em execução no Azure, em outra nuvem ou localmente.

![Coleta de dados do aplicativo](media/data-sources/applications.png)


### <a name="application-data"></a>Dados do aplicativo
Quando você habilita o Application Insights para um aplicativo por meio da instalação de um pacote de instrumentação, ele coleta as métricas e logs relacionados ao desempenho e à operação do aplicativo. O Application Insights armazena os dados coletados na mesma plataforma de dados Azure Monitor usada por outras fontes de dados. Ele inclui ferramentas abrangentes para a análise desses dados, mas você também pode analisá-los com dados de outras fontes usando ferramentas como Metrics Explorer e Log Analytics.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Dados operacionais sobre seu aplicativo, incluindo exibições de página, solicitações de aplicativo, exceções e rastreamentos. | [Analisar dados de log no Azure Monitor](../logs/log-query-overview.md) |
|                    | Informações de dependência entre componentes de aplicativo para dar suporte ao mapa do aplicativo e à correlação de telemetria. | [Correlação de telemetria no Application Insights](../app/correlation.md) <br> [Mapa do aplicativo](../app/app-map.md) |
|            | Resultados de testes de disponibilidade que testam a disponibilidade e a capacidade de resposta do seu aplicativo de locais diferentes na Internet pública. | [Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web](../app/monitor-web-app-availability.md) |
| Métricas do Azure Monitor | Application Insights coleta métricas que descrevem o desempenho e a operação do aplicativo, além das métricas personalizadas que você define em seu aplicativo no banco de dados de métricas de Azure Monitor. | [Métricas baseadas em log e pré-agregadas no Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API do Application Insights para métricas e eventos personalizados](../app/api-custom-events-metrics.md) |
| Armazenamento do Azure | Envie dados de aplicativo para o armazenamento do Azure para arquivamento. | [Exportar telemetria do Application Insights](../app/export-telemetry.md) |
|            | Os detalhes dos testes de disponibilidade são armazenados no armazenamento do Azure. Use Application Insights no portal do Azure para baixar para análise local. Os resultados dos testes de disponibilidade são armazenados em logs de Azure Monitor. | [Monitorar a disponibilidade e a capacidade de resposta de qualquer site da Web](../app/monitor-web-app-availability.md) |
|            | Os dados de rastreamento do profiler são armazenados no armazenamento do Azure. Use Application Insights no portal do Azure para baixar para análise local.  | [Aplicativos de produção de perfil no Azure com o Application Insights](../app/profiler-overview.md) 
|            | Os dados de instantâneo de depuração capturados para um subconjunto de exceções são armazenados no armazenamento do Azure. Use Application Insights no portal do Azure para baixar para análise local.  | [Como os instantâneos funcionam](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluções e ideias de monitoramento
O [monitoramento de soluções](../insights/solutions.md) e [informações](../monitor-reference.md) coleta dados para fornecer informações adicionais sobre a operação de um serviço ou aplicativo específico. Eles podem tratar recursos em diferentes camadas de aplicativo e até mesmo várias camadas.

### <a name="monitoring-solutions"></a>Soluções de monitoramento

| Destino | Descrição | Referência
|:---|:---|:---|
| Logs do Azure Monitor | As soluções de monitoramento coletam dados em logs de Azure Monitor onde podem ser analisados usando a linguagem de consulta ou [exibições](../visualize/view-designer.md) que normalmente são incluídas na solução. | [Detalhes da coleta de dados para soluções de monitoramento no Azure](../monitor-reference.md) |


### <a name="container-insights"></a>Insights do contêiner
As [informações de contêiner](../containers/container-insights-overview.md) fornecem uma experiência de monitoramento Personalizada para o [AKs (serviço kubernetes do Azure)](../../aks/index.yml). Ele coleta dados adicionais sobre esses recursos descritos na tabela a seguir.

| Destino | Descrição | Referência |
|:---|:---|:---|
| Logs do Azure Monitor | Armazena dados de monitoramento para AKS, incluindo inventário, logs e eventos. Os dados de métrica também são armazenados em logs a fim de aproveitar sua funcionalidade de análise no Portal. | [Entender o desempenho do cluster AKS com informações de contêiner](../containers/container-insights-analyze.md) |
| Métricas do Azure Monitor | Os dados de métrica são armazenados no banco de dado de métrica para impulsionar a visualização e os alertas. | [Exibir métricas de contêiner no Metrics Explorer](../containers/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Serviço de Kubernetes do Azure | Fornece acesso direto aos logs de contêiner do AKS (serviço kubernetes do Azure) (stdout/stderr), eventos e métricas de Pod no Portal. | [Como exibir logs de kubernetes, eventos e métricas de pod em tempo real ](../containers/container-insights-livedata-overview.md) |

### <a name="vm-insights"></a>Insights da VM
As [informações de VM](../vm/vminsights-overview.md) fornecem uma experiência personalizada para monitorar máquinas virtuais. Uma descrição dos dados coletados pelo VM insights está incluída na seção do [sistema operacional (convidado)](#operating-system-guest) acima.

## <a name="custom-sources"></a>Fontes personalizadas
Além das camadas padrão de um aplicativo, você precisa monitorar outros recursos que têm a telemetria que não pode ser coletada com outras fontes de dados. Para esses recursos, grave esses dados em métricas ou logs usando uma API Azure Monitor.

![Coleção personalizada](media/data-sources/custom.png)

| Destino | Método | Descrição | Referência |
|:---|:---|:---|:---|
| Logs do Azure Monitor | API do Coletor de Dados | Coletar dados de log de qualquer cliente REST e armazenar em Log Analytics espaço de trabalho. | [Enviar dados de log para o Azure Monitor com a API do Coletor de Dados HTTP (visualização pública)](../logs/data-collector-api.md) |
| Métricas do Azure Monitor | API de métricas personalizadas | Coletar dados de métrica de qualquer cliente REST e armazenar em Azure Monitor banco de dados de métricas. | [Enviar métricas personalizadas de um recurso do Azure para o repositório de métricas do Azure Monitor usando uma API REST](../essentials/metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Outros serviços
Outros serviços no Azure gravam dados na plataforma de dados Azure Monitor. Isso permite que você analise os dados coletados por esses serviços com os dados coletados por Azure Monitor e aproveite as mesmas ferramentas de análise e visualização.

| Serviço | Destino | Descrição | Referência |
|:---|:---|:---|:---|
| [Central de Segurança do Azure](../../security-center/index.yml) | Logs do Azure Monitor | A central de segurança do Azure armazena os dados de segurança coletados em um espaço de trabalho Log Analytics que permite que ele seja analisado com outros dados de log coletados pelo Azure Monitor.  | [Coleta de dados na Central de Segurança do Azure](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../../sentinel/index.yml) | Logs do Azure Monitor | O Azure Sentinel armazena os dados coletados de diferentes fontes de dados em um Log Analytics espaço de trabalho que permite que ele seja analisado com outros dados de log coletados pelo Azure Monitor.  | [Conectar fontes de dados](../../sentinel/quickstart-onboard.md) |


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre os [tipos de dados de monitoramento coletados pelo Azure Monitor](../data-platform.md) e como exibir e analisar esses dados.
- Liste os [diferentes locais em que os recursos do Azure armazenam dados](../monitor-reference.md) e como você pode acessá-los.