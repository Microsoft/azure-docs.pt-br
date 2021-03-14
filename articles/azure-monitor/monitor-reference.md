---
title: O que é monitorado pelo Azure Monitor
description: Referência de todos os serviços e outros recursos monitorados pelo Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 4bf792dd02e7cddcc40ef868e4a602fdb03ab3c6
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052272"
---
# <a name="what-is-monitored-by-azure-monitor"></a>O que é monitorado pelo Azure Monitor?
Este artigo descreve os diferentes aplicativos e serviços monitorados pelo Azure Monitor. 

## <a name="insights-and-core-solutions"></a>Insights e principais soluções
As principais informações e soluções são consideradas parte do Azure Monitor e seguem os contratos de nível de serviço e suporte para o Azure. Eles têm suporte em todas as regiões do Azure onde o Azure Monitor está disponível.

### <a name="insights"></a>Insights

O Insights fornece uma experiência de monitoramento personalizada para aplicativos e serviços específicos. Eles coletam e analisam os logs e as métricas.

| Insight | Descrição |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | Serviço de APM (gerenciamento de desempenho de aplicativos) extensível para monitorar seu aplicativo Web ativo em qualquer plataforma. |
| [Informações de contêiner](containers/container-insights-overview.md) | Monitora o desempenho das cargas de trabalho de contêineres implantadas nas Instâncias de Contêiner do Azure ou nos clusters Kubernetes gerenciados hospedados no AKS (Serviço de Kubernetes do Azure). |
| [Azure Monitor para Azure Cosmos DB](insights/cosmosdb-insights-overview.md) | Fornece uma exibição do desempenho geral, das falhas, da capacidade e da integridade operacional de todos os seus recursos de Azure Cosmos DB em uma experiência interativa unificada. |
| [Azure Monitor para Redes (versão prévia)](insights/network-insights-overview.md) | Fornece uma visão abrangente da integridade e das métricas para todos os seus recursos de rede. O recurso de pesquisa avançada ajuda a identificar dependências de recursos, permitindo cenários como identificar recursos que hospedam seu site, simplesmente pesquisando o nome do site. |
[Azure Monitor para Grupos de Recursos (versão prévia)](insights/resource-group-insights.md) |  Facilita a triagem e o diagnóstico de quaisquer problemas encontrados pelos recursos individuais, oferecendo contexto sobre a integridade e o desempenho do grupo de recursos como um todo. |
| [Azure Monitor para Armazenamento](insights/storage-insights-overview.md) | Fornece monitoramento abrangente das contas do Armazenamento do Azure proporcionando uma exibição unificada de desempenho, capacidade e disponibilidade dos serviços do Armazenamento. |
| [Informações de VM](vm/vminsights-overview.md) | Monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos. |
| [Azure Monitor para Key Vault (versão prévia)](./insights/key-vault-insights-overview.md) | Fornece monitoramento abrangente de seus cofres de chaves, fornecendo uma exibição unificada de suas solicitações de Key Vault, desempenho, falhas e latência. |
| [Azure Monitor para Cache do Azure para Redis (versão prévia)](insights/redis-cache-insights-overview.md) |  Fornece uma exibição unificada e interativa de desempenho geral, falhas, capacidade e integridade operacional. |


### <a name="core-solutions"></a>Principais soluções

As soluções são baseadas em consultas de log e exibições personalizadas para um determinado aplicativo ou serviço. Eles coletam e analisam somente logs e estão sendo preteridos ao longo do tempo em favor de insights.

| Solução | Descrição |
|:---|:---|
| [Integridade do agente](insights/solution-agenthealth.md) | Analise a integridade e a configuração dos agentes do Log Analytics. |
| [Gerenciamento de alertas](insights/alert-management-solution.md) | Analise os alertas coletados do System Center Operations Manager, Nagios ou Zabbix. |
| [Mapa do Serviço](vm/service-map.md) | Descobre automaticamente os componentes de aplicativo em sistemas Windows e Linux e mapeia a comunicação entre os serviços. |



## <a name="azure-services"></a>Serviços do Azure
A tabela a seguir lista os serviços do Azure e os dados coletados no Azure Monitor. 

- Métricas – o serviço coleta automaticamente as dados em Métricas de Azure Monitor. 
- Logs – o serviço dá suporte a configurações de diagnóstico que podem coletar logs e métricas de plataforma para os logs do Azure Monitor.
- Insight – há um insight disponível para o serviço que fornece uma experiência de monitoramento personalizada para o serviço.

| Serviço | Métricas | Logs | Insight | Observações |
|:---|:---|:---|:---|:---|
|Active Directory | Não | Sim | [Sim](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | Não | Não | Não |  |
|Active Directory Domain Services | Não | Sim | Não |  |
|Log de atividades | Não | Sim | Não | |
|Proteção Avançada contra Ameaças | Não | Não | Não |  |
|Supervisor | Não | Não | Não |  |
|AI Builder | Não | Não | Não |  |
|Serviços de análise | Sim | Sim | Não |  |
|API para FHIR | Não | Não | Não |  |
|Gerenciamento de API | Sim | Sim | Não |  |
|Serviço de Aplicativo | Sim | Sim | Não |  |
|AppConfig | Não | Não | Não |  |
|Gateway de Aplicativo | Sim | Sim | Não |  |
|Serviço do atestado | Não | Não | Não |  |
|Automação | Sim | Sim | Não |  |
|Service Manager do Azure (RDFE) | Não | Não | Não |  |
|Backup | Não | Sim | Não |  |
|Bastion | Não | Não | Não |  |
|Lote | Sim | Sim | Não |  |
|Lote AI | Não | Não | Não |  |
|Blockchain Service | Não | Sim | Não |  |
|Blueprints | Não | Não | Não |  |
|Serviço de Bot | Não | Não | Não |  |
|Serviços de Nuvem | Sim | Sim | Não | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho.  |
|Cloud Shell | Não | Não | Não |  |
|Serviços Cognitivos | Sim | Sim | Não |  |
|Instâncias de Contêiner | Sim | Não | Não |  |
|Registro de Contêiner | Sim | Sim | Não |  |
|CDN (Rede de Distribuição de Conteúdo) | Não | Sim | Não |  |
|Cosmos DB | Sim | Sim | [Sim](insights/cosmosdb-insights-overview.md) |  |
|Gerenciamento de Custos | Não | Não | Não |  |
|Data Box | Não | Não | Não |  |
|Catálogo de Dados Gen2 | Não | Não | Não |  |
|Data Explorer | Sim | Sim | Não |  |
|Data Factory | Sim | Sim | Não |  |
|Data Factory v2 | Não | Sim | Não |  |
|Data Share | Não | Não | Não |  |
|Banco de Dados para MariaDB | Sim | Sim | Não |  |
|Banco de Dados para MySQL | Sim | Sim | Não |  |
|Banco de Dados para PostgreSQL | Sim | Sim | Não |  |
|Serviço de Migração de Banco de Dados | Não | Não | Não |  |
|Databricks | Não | Sim | Não |  |
|Proteção contra DDoS | Sim | Sim | Não |  |
|DevOps | Não | Não | Não |  |
|DNS | Sim | Não | Não |  |
|Nomes de domínio | Não | Não | Não |  |
|DPS | Não | Não | Não |  |
|Dynamics 365 Customer Engagement | Não | Não | Não |  |
|Dynamics 365 Finance and Operations | Não | Não | Não |  |
|Grade de Eventos | Sim | Não | Não |  |
|Hubs de Eventos | Sim | Sim | Não |  |
|ExpressRoute | Sim | Sim | Não |  |
|Firewall | Sim | Sim | Não |  |
|Front Door | Sim | Sim | Não |  |
|Funções | Sim | Sim | Não |  |
|HDInsight | Não | Sim | Não |  |
|HPC Cache | Não | Não | Não |  |
|Proteção de Informações | Não | Sim | Não |  |
|Intune | Não | Sim | Não |  |
|Central da IoT | Não | Não | Não |  |
|Hub IoT | Sim | Sim | Não |  |
|Key Vault | Sim | Sim | [Sim](./insights/key-vault-insights-overview.md) |  |
|AKS (Serviço do Kubernetes) | Não | Não | [Sim](containers/container-insights-overview.md)  |  |
|Load Balancer | Sim | Não | Não |  |
|Aplicativos Lógicos | Sim | Sim | Não |  |
|Serviço do Machine Learning | Não | Não | Não |  |
|Aplicativos gerenciados  | Não | Não | Não |  |
|Mapas  | Não | Não | Não |  |
|Serviços de mídia | Sim | Sim | Não |  |
|Área de Trabalho Gerenciada da Microsoft | Não | Não | Não |  |
|Microsoft PowerApps | Não | Não | Não |  |
|Engajamento social da Microsoft | Não | Não | Não |  |
|Microsoft Stream | Sim | Sim | Não |  |
|Migrar | Não | Não | Não |  |
|Autenticação Multifator | Não | Sim | Não |  |
|Observador de Rede | Sim | Sim | Não |  |
|Hubs de Notificação | Sim | Não | Não |  |
|Conjuntos de dados em aberto | Não | Não | Não |  |
|Política | Não | Não | Não |  |
|Power Automate | Não | Não | Não |  |
|Power BI Embedded | Sim | Sim | Não |  |
|Link Privado | Não | Não | Não |  |
|Plataforma de comunicação do spool do projeto | Não | Não | Não |  |
|Red Hat OpenShift | Não | Não | Não |  |
|Cache Redis | Sim | Sim | [Sim](insights/redis-cache-insights-overview.md) | |
|Resource Graph | Não | Não | Não |  |
|Gerenciador de Recursos | Não | Não | Não |  |
|Pesquisa de varejo – por Bing | Não | Não | Não |  |
|Search | Sim | Sim | Não |  |
|Barramento de Serviço | Sim | Sim | Não |  |
|Service Fabric | Não | Sim | Não | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho.  |
|Portal de inscrição | Não | Não | Não |  |
|Site Recovery | Não | Sim | Não |  |
|Spring Cloud Service | Não | Não | Não |  |
|Azure Synapse Analytics | Sim | Sim | Não |  |
|Banco de Dados SQL | Sim | Sim | Não |  |
|SQL Server Stretch Database | Sim | Sim | Não |  |
|Pilha | Não | Não | Não |  |
|Armazenamento | Sim | Não | [Sim](insights/storage-insights-overview.md) |  |
|Cache de Armazenamento | Não | Não | Não |  |
|Serviços de sincronização de armazenamento | Não | Não | Não |  |
|Stream Analytics | Sim | Sim | Não |  |
|Time Series Insights | Sim | Sim | Não |  |
|TINA | Não | Não | Não |  |
|Gerenciador de Tráfego | Sim | Sim | Não |  |
|Impressão Universal | Não | Não | Não |  |
|Conjuntos de Dimensionamento de Máquinas Virtuais | Não | Sim | [Sim](vm/vminsights-overview.md) | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho. |
|Máquinas Virtuais | Sim | Sim | [Sim](vm/vminsights-overview.md) | Agente necessário para monitorar o sistema operacional convidado e os fluxos de trabalho. |
|Rede Virtual | Sim | Sim | [Sim](insights/network-insights-overview.md) |  |
|Rede virtual – logs de fluxo NSG | Não | Sim | Não |  |
|Gateway de VPN | Sim | Sim | Não |  |
|Área de Trabalho Virtual do Windows | Não | Não | Não |  |

## <a name="virtual-machine-agents"></a>Agentes da máquina virtual
A tabela a seguir lista os agentes que podem coletar dados do sistema operacional convidado de máquinas virtuais e enviar dados para monitorar. Cada agente pode coletar dados diferentes e enviá-los para métricas ou logs em Azure Monitor. 

Consulte [visão geral dos agentes de Azure monitor](agents/agents-overview.md) para obter detalhes sobre os dados que cada agente pode coletar.

| Agente |  Métricas | Logs |
|:---|:---|:---|:---|
| [Agente do Azure Monitor (versão prévia)](agents/azure-monitor-agent-overview.md) | Sim | Sim |
| [Agente do Log Analytics](agents/log-analytics-agent.md) | Não | Sim|
| [Extensão de diagnóstico](agents/diagnostics-extension-overview.md) | Sim | Não |
| [Agente Telegraf](essentials/collect-custom-metrics-linux-telegraf.md) | Sim | Não |
| [Agente de dependência](vm/vminsights-enable-overview.md) | Não | Sim |


## <a name="product-integrations"></a>Integrações de produto
Os serviços e as soluções na tabela a seguir armazenam seus dados em um workspace do Log Analytics para que possam ser analisados com outros dados de log coletados pelo Azure Monitor.

| Produto/Serviço | Descrição |
|:---|:---|
| [Automação do Azure](../automation/index.yml) | Gerenciar atualizações do sistema operacional e controlar alterações em computadores Windows e Linux. Confira [Controle de Alterações](../automation/change-tracking/overview.md) e [Gerenciamento de Atualizações](../automation/update-management/overview.md). |
| [Proteção de Informações do Azure ](/azure/information-protection/) | Classifique e, opcionalmente, proteja documentos e emails. Confira [Relatórios centrais para a Proteção de Informações do Azure](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports). |
| [Central de Segurança do Azure](../security-center/index.yml) | Colete e analise eventos de segurança e execute a análise de ameaças. Confira [Coleta de dados na Central de Segurança do Azure](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | Conecta-se a fontes diferentes, incluindo o Office 365 e o Amazon Web Services CloudTrail. Confira [Conectar fontes de dados](../sentinel/connect-data-sources.md). |
| [Microsoft Intune](/intune/) | Crie uma configuração de diagnóstico para enviar logs para o Azure Monitor. Confira [Enviar dados de log para armazenamento, hubs de eventos ou análise de logs no Intune (versão prévia)](/intune/fundamentals/review-logs-using-azure-monitor).  |
| Rede  | [Monitor de Desempenho de Rede](insights/network-performance-monitor.md) – monitore a conectividade de rede e o desempenho para pontos de extremidade de serviço e aplicativo.<br>[Gateway de Aplicativo do Azure](insights/azure-networking-analytics.md#azure-application-gateway-analytics) – analise os logs e as métricas do Gateway de Aplicativo do Azure.<br>[Análise de Tráfego](../network-watcher/traffic-analytics.md) –analisa os logs de fluxo de NSG (grupo de segurança de rede) do Observador de Rede para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. |
| [Office 365](insights/solution-office-365.md) | Monitore seu ambiente do Office 365. Versão atualizada com integração aprimorada disponível por meio do Azure Sentinel. |
| [Análise de SQL](insights/azure-sql.md) | Monitore o desempenho de bancos de dados SQL do Azure e instâncias gerenciadas do SQL em escala e em várias assinaturas. |
| [Hub de Superfície](insights/surface-hubs.md) | Acompanhe a integridade e o uso de dispositivos Surface Hub. |
| [System Center Operations Manager](/system-center/scom) | Colete dados de agentes do Operations Manager conectando seu grupo de gerenciamento ao Azure Monitor. Confira [Conectar o Operations Manager com o Azure Monitor](agents/om-agents.md)<br> Avalie o risco e a integridade do seu grupo de gerenciamento do System Center Operations Manager com a solução [Avaliação do Operations Manager](insights/scom-assessment.md). |
| [Salas do Microsoft Teams](/microsoftteams/room-systems/azure-monitor-deploy) | Gerenciamento integrado de ponta a ponta de dispositivos de salas do Microsoft Teams. |
| [Visual Studio App Center](/appcenter/) | Crie, teste e distribua aplicativos e, em seguida, monitore seu status e uso. Confira [Começar a analisar seu aplicativo móvel com o App Center e o Application Insights](app/mobile-center-quickstart.md). |
| Windows | [Conformidade de Atualizações do Windows](/windows/deployment/update/update-compliance-get-started) – avalie as atualizações da área de trabalho do Windows.<br>[Análise de Área de Trabalho ](/configmgr/desktop-analytics/overview) – integra-se com o Configuration Manager para oferecer insights e inteligência a fim de tomar decisões mais informadas sobre a preparação para atualização dos clientes do Windows. |



## <a name="other-solutions"></a>Outras soluções
Outras soluções estão disponíveis para monitorar diferentes aplicativos e serviços, mas o desenvolvimento ativo foi interrompido e elas podem não estar disponíveis em todas as regiões. Elas são cobertas pelo contrato de nível de serviço de ingestão de dados do Azure Log Analytics.

| Solução | Descrição |
|:---|:---|
| [Verificação de integridade do Active Directory](insights/ad-assessment.md) | Avalie o risco e a integridade de ambientes do Active Directory. |
| [Status de replicação do Active Directory](insights/ad-replication-status.md) | Monitora regularmente seu ambiente do Active Directory em busca de falhas de replicação. |
| [Análise do Log de Atividades](essentials/activity-log.md#activity-log-analytics-monitoring-solution) | Exiba entradas de log de atividades. |
| [Análise de DNS (versão prévia)](insights/dns-analytics.md) | Coleta, analisa e correlaciona logs de auditoria e analíticos de DNS do Windows, bem como outros dados relacionados de seus servidores DNS. |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | Colete, exiba e analise a integridade do sistema Cloud Foundry e as métricas de desempenho do CF em várias implantações. |
| [Contêineres](containers/containers.md) | Exiba e gerencie hosts de contêiner do Docker e do Windows. |
| [Avaliações sob demanda](/services-hub/health/getting_started_with_on_demand_assessments) | Avalie e otimize a disponibilidade, a segurança e o desempenho de seus ambientes de tecnologia da Microsoft locais, híbridos e na nuvem. |
| [Verificação de Integridade do SQL](insights/sql-assessment.md) | Avalie o risco e a integridade de ambientes do SQL Server.  |
| [Dados durante a transmissão](insights/wire-data.md) | Consolidados de rede e de desempenho coletados de computadores conectados por Windows e conectados por Linux com o agente do Log Analytics. |

## <a name="third-party-integration"></a>Integração de terceiros

| Solução | Descrição |
|:---|:---|
| [ITSM](alerts/itsmc-overview.md) | O ITSMC (Conector de Gerenciamento de Serviços de TI) permite que você conecte o Azure e um produto/serviço de ITSM (Gerenciamento de Serviços de TI) compatível.  |


## <a name="resources-outside-of-azure"></a>Recursos fora do Azure
O Azure Monitor pode coletar dados de recursos fora do Azure usando os métodos listados na tabela a seguir.

| Recurso | Método |
|:---|:---|
| Aplicativos | Monitore aplicativos Web fora do Azure usando o Application Insights. Confira [O que é o Application Insights?](./app/app-insights-overview.md). |
| Máquinas virtuais | Use agentes para coletar dados do sistema operacional convidado de máquinas virtuais em outros ambientes de nuvem ou locais. Consulte [visão geral dos agentes de Azure monitor](agents/agents-overview.md). |
| Cliente da API REST | As APIs separadas estão disponíveis para gravar dados nos logs e nas métricas do Azure Monitor de qualquer cliente da API REST. Confira [Enviar dados de log para o Azure Monitor com a API do coletor de dados HTTP](logs/data-collector-api.md) para logs e [Enviar métricas personalizadas de um recurso do Azure para o repositório de métricas do Azure Monitor usando uma API REST](essentials/metrics-store-custom-rest-api.md) para métricas. |



## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre a [Plataforma de dados do Azure Monitor que armazena os logs e as métricas coletados pelos insights e pelas soluções](data-platform.md).
- Conclua um [tutorial sobre como monitorar um recurso do Azure](essentials/tutorial-resource-logs.md).
- Conclua um [tutorial sobre como gravar uma consulta de log para analisar dados em logs do Azure Monitor](essentials/tutorial-resource-logs.md).
- Conclua um [tutorial sobre como criar um gráfico de métricas para analisar dados nas métricas do Azure Monitor](essentials/tutorial-metrics-explorer.md).

