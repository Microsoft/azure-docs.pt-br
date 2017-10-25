---
title: Log e auditoria do Azure | Microsoft Docs
description: "Saiba mais sobre como você pode usar os dados de log para obter informações detalhadas sobre seu aplicativo."
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: TomSh
ms.openlocfilehash: 9e5c929251259a86944121e504dc033bc99e3bc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-logging-and-auditing"></a>Log e auditoria do Azure
## <a name="introduction"></a>Introdução
### <a name="overview"></a>Visão geral
Para ajudar os clientes atuais e potenciais do Azure a entender e usar as várias funcionalidades relacionadas à segurança disponíveis na Plataforma Azure e em suas proximidades, a Microsoft desenvolveu uma série de white papers, visões gerais de segurança, melhores práticas e listas de verificação. Os tópicos variam em termos de abrangência e profundidade e são atualizados periodicamente. Este documento integra uma série, conforme resumido na próxima seção Resumo.
### <a name="azure-platform"></a>Plataforma Azure
O Azure é uma plataforma de serviço de nuvem aberta e flexível que dá suporte a mais ampla seleção de sistemas operacionais, linguagens de programação, estruturas, ferramentas, bancos de dados e dispositivos.

Por exemplo, você pode:
-   Executar contêineres do Linux com a integração com o Docker.

-   Criar aplicativos com JavaScript, Python, .NET, PHP, Java e Node.js

-   Criar back-ends para dispositivos iOS, Android e Windows.

Os serviços de nuvem pública do Azure dão suporte às mesmas tecnologias com as quais milhões de desenvolvedores e profissionais de TI já contam e confiam.

Ao se basear ou migrar ativos de TI para um provedor de nuvem, você está confiando na capacidade dessa organização de proteger seus aplicativos e dados com os serviços e os controles que ela oferece para gerenciar a segurança de seus ativos baseados em nuvem.

A infraestrutura do Azure foi projetada desde a instalação até os aplicativos para hospedar simultaneamente milhões de clientes e fornecer uma base confiável com a qual as empresas podem atender às suas necessidades de segurança. Além disso, o Azure oferece uma ampla variedade de opções de segurança configuráveis e a capacidade de controlá-las, para que você possa personalizar a segurança de forma a atender aos requisitos específicos de suas implantações. Este documento ajuda você a atender a esses requisitos.

### <a name="abstract"></a>Resumo
A auditoria e o log de eventos relacionados à segurança, bem como alertas relacionados, são componentes importantes em uma estratégia de proteção de dados eficaz. Os logs e os relatórios de segurança fornecem um registro eletrônico das atividades suspeitas e ajudam a detectar padrões que podem indicar uma tentativa de penetração externa ou uma penetração externa com êxito da rede, bem como ataques internos. Você pode usar a auditoria para monitorar as atividades do usuário, a conformidade regulatória de documentos, executar análise forense e muito mais. Os alertas fornecem uma notificação imediata quando ocorrem eventos de segurança.

Os serviços e produtos do Microsoft Azure fornecem opções configuráveis de auditoria e log para ajudá-lo a identificar lacunas em suas políticas e seus mecanismos de segurança e corrigi-las, a fim de ajudar a prevenir violações de segurança. Os serviços da Microsoft oferecem algumas (e, em alguns casos, todas) as seguintes opções: monitoramento centralizado, log e sistemas de análise para fornecer visibilidade contínua, alertas em tempo hábil e relatórios para ajudá-lo a gerenciar a grande quantidade de informações gerada por dispositivos e serviços.

Os dados de logs do Microsoft Azure podem ser exportados para sistemas SIEM (Gerenciamento de Incidentes e Eventos de Segurança) para análise e são integrados com soluções de auditoria de terceiros.

Este white paper fornece uma introdução para a geração, coleta e análise dos logs de segurança de serviços hospedados no Azure e pode ajudá-lo a obter informações de segurança sobre as implantações do Azure. O escopo deste white paper é limitado a aplicativos e serviços criados e implantados no Azure.

> [!Note]
> Algumas recomendações contidas neste documento podem resultar no aumento do uso de dados, de rede ou dos recursos de computação e aumentar os custos da licença ou da assinatura.

## <a name="types-of-logs-in-azure"></a>Tipos de logs no Azure
Os aplicativos em nuvem são complexos com muitas partes móveis. Os logs fornecem dados para garantir que seu aplicativo permanece ativo e em execução em um estado íntegro. Ele também ajuda a afastar os problemas potenciais ou solucionar problemas antigos. Além disso, você pode usar os dados de log para obter informações detalhadas sobre seu aplicativo. Esse conhecimento pode ajudá-lo a melhorar o desempenho ou a capacidade de manutenção do aplicativo ou automatizar ações que normalmente exigiriam intervenção manual.

O Azure produz um log abrangente para cada um de seus serviço. Esses logs são categorizados por esses tipos principais:
-   Os **logs de controle/derenciamento** fornecem visibilidade das operações CREATE, UPDATE e DELETE do Azure Resource Manager. Os [Logs de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) são um exemplo desse tipo de log.

-   Os **logs do plano de dados** oferecem visibilidade dos eventos acionados como parte do uso de um recurso do Azure. Exemplos desse tipo de log são os logs de eventos do Sistema, de Segurança e de Aplicativo do Windows em uma máquina virtual e os [Logs de Diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) configurados por meio do Azure Monitor


-   Os **eventos processados** fornecem informações sobre os eventos/alertas analisados que foram processados em seu nome. Exemplos desse tipo são os [Alertas da Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts), nos quais a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) processou e analisou sua assinatura e fornece alertas de segurança concisos

A tabela a seguir lista os tipos mais importantes de logs disponíveis no Azure.

| Categoria do Log | Tipo de Log | Usos | Integração |
| ------------ | -------- | ------ | ----------- |
|[Logs de Atividades](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|Eventos de plano de controle nos recursos do Azure Resource Manager| Fornecem informações sobre as operações executadas em recursos de sua assinatura.| API REST e [Azure Monitor](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)|
|[Logs de Diagnóstico do Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|dados frequentes sobre a operação de recursos do Azure Resource Manager na assinatura|   Fornecem informações sobre as operações que o recurso executou por conta própria| Azure Monitor, [Stream](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)|
|[Relatórios do AAD](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-azure-portal)|Logs e relatórios|Atividades de conexão do usuário e informações de atividades do Sistema sobre gerenciamento de usuários e grupos|[API gráfica](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-graph-api-quickstart)|
|[Máquina Virtual e Serviços de Nuvem](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-diagnostics-storage)|Log de eventos do Windows e syslog do Linux|  Capturam dados do sistema e dados de logs nas máquinas virtuais e transferem os dados para uma conta de armazenamento de sua escolha.|   Windows com [WAD](https://docs.microsoft.com/en-us/azure/azure-diagnostics) (armazenamento do Diagnóstico do Microsoft Azure) e Linux no Azure Monitor|
|[Análise de Armazenamento](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/storage-analytics)|Log de armazenamento; fornece dados de métrica de uma conta de armazenamento|Fornece informações das solicitações de rastreamento, analisa tendências de uso e diagnostica problemas com a conta de armazenamento.|  API REST ou [biblioteca de cliente](https://msdn.microsoft.com/en-us/library/azure/mt347887.aspx)|
|[Logs de fluxo do NSG (Grupo de Segurança de Rede)](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-nsg-flow-logging-overview)|Formato JSON; mostra os fluxos de entrada e saída por regra|Exibe informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede|[Observador de Rede](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview)|
|[Application Insights](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview)|Logs, exceções e diagnóstico personalizado|  Serviço APM (Gerenciamento de Desempenho de Aplicativos) para desenvolvedores da Web em várias plataformas.| API REST, [Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-azure-and-power-bi/)|
|Dados do processo/alerta de segurança| Alerta da Central de Segurança do Azure, alerta do OMS| Informações de segurança e alertas.|   APIs REST, JSON|

### <a name="activity-log"></a>Log de Atividade
O [Log de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) fornece informações sobre as operações executadas em recursos de sua assinatura. O Log de Atividades era conhecido como “Logs de Auditoria” ou “Logs Operacionais”, pois relata [eventos de plano de controle](https://driftboatdave.com/2016/10/13/azure-auditing-options-for-your-custom-reporting-needs/) de suas assinaturas. Com o Log de Atividades, você pode determinar dados básicos como “o que, quem e quando” em operações de gravação (PUT, POST, DELETE) executadas nos recursos de sua assinatura. Também é possível compreender o status da operação e outras propriedades relevantes. O Log de Atividades não inclui operações de leitura (GET).

Aqui, PUT, POST e DELETE referem-se a todas as operações de gravação que o log de atividades contém nos recursos. Por exemplo, é possível usar os logs de atividades para encontrar um erro ao resolver problemas ou para monitorar como um usuário de sua organização modificou um recurso.

![Log de Atividade](./media/azure-log-audit/azure-log-audit-fig1.png)


Você pode recuperar eventos do Log de Atividades usando o portal do Azure, a [CLI](https://docs.microsoft.com/azure/storage/storage-azure-cli), cmdlets do PowerShell e a [API REST do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-rest-api-walkthrough). Os logs de atividades têm um período de retenção de dados de 19 dias.

Cenários de integração
-   [Criar um alerta de email ou webhook que dispara um evento do Log de Atividades.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-auditlog-to-webhook-email)

-   [Transmiti-lo para um Hub de Eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o Power BI.

-   Analisá-lo no Power BI usando o [pacote de conteúdo do Power BI.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)

-   [Salvá-lo em uma Conta de Armazenamento para arquivamento ou inspeção manual.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-activity-log) É possível especificar o tempo de retenção (em dias) usando Perfis de Log.

-   Consultar e exibi-lo no portal do Azure.

-   Consultar por meio de Cmdlet do PowerShell, da CLI ou da API REST.

-   Exportar o Log de Atividades com Perfis de Log para o [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview).

É possível usar uma conta de armazenamento ou um [namespace de hub de eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-resource-manager-namespace-event-hub-enable-archive) que não está na mesma assinatura para emitir logs. O usuário que define a configuração deve ter o devido acesso [RBAC](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) a ambas as assinaturas
### <a name="azure-diagnostic-logs"></a>Logs de Diagnóstico do Azure
Os Logs de Diagnóstico do Azure são emitidos por um recurso que fornece dados avançados e frequentes sobre a operação do recurso. O conteúdo desses logs varia por tipo de recurso (por exemplo, os [logs do sistema de eventos do Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) são uma categoria de Logs de Diagnóstico para VMs e [logs de blobs, tabelas e filas](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) são categorias de Logs de Diagnóstico para contas de armazenamento) e difere do Log de Atividades, que fornece informações sobre as operações executadas nos recursos de sua assinatura.

![Logs de Diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig2.png)

Os Logs de Diagnóstico do Azure oferecem várias opções de configuração, ou seja, o portal do Azure, usando o PowerShell, a CLI (Interface de Linha de Comando) e a API REST.

**Cenários de integração**
-   Salve-os em uma [Conta de Armazenamento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs) para inspeção manual ou de auditoria. É possível especificar o tempo de retenção (em dias) usando as Configurações de Diagnóstico.

-   [Transmiti-los para os Hubs de Eventos](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs) para ingestão por um serviço de terceiros ou uma solução de análise personalizada, como o [Power BI.](https://powerbi.microsoft.com/documentation/powerbi-azure-and-power-bi/)

-   Analisá-los com o [Log Analytics do OMS.](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)

**Serviços com suporte, esquema dos Logs de Diagnóstico e categorias de logs com suporte por tipo de recurso**


| O Barramento de | Esquema e Documentos | Tipo de recurso | Categoria |
| ------- | ------------- | ------------- | -------- |
|Balanceador de carga| [Análise de log para o Balanceador de Carga do Azure (Preview)](https://docs.microsoft.com/en-us/azure/load-balancer/load-balancer-monitor-log)|Microsoft.Network/loadBalancers|  LoadBalancerAlertEvent|
|||Microsoft.Network/loadBalancers| LoadBalancerProbeHealthStatus
|Grupos de segurança de rede|[Análise de logs para NSGs (grupos de segurança de rede)](https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-nsg-manage-log)|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|
|||Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|
|Gateways do Aplicativo|[Log de diagnóstico do Application Gateway](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-diagnostics)|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|
|||Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|
|Cofre da Chave|[Logs do Cofre da Chave do Azure](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-logging)|Microsoft.KeyVault/vaults|AuditEvent|
|Pesquisa do Azure|[Habilitação e uso da análise de tráfego de pesquisa](https://docs.microsoft.com/en-us/azure/search/search-traffic-analytics)|Microsoft.Search/searchServices|OperationLogs|
|Repositório Data Lake|[Acessando os logs de diagnóstico do Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-diagnostic-logs)|Microsoft.DataLakeStore/accounts|Audit|
|Análises Data Lake|[Acessando os logs de diagnóstico do Azure Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs)|Microsoft.DataLakeAnalytics/accounts|Audit|
|||Microsoft.DataLakeAnalytics/accounts|Solicitações|
|||Microsoft.DataLakeStore/accounts|Solicitações|
|Aplicativos Lógicos|[Esquema de controle personalizado dos Aplicativos Lógicos B2B](https://docs.microsoft.com/en-us/azure/logic-apps/logic-apps-track-integration-account-custom-tracking-schema)|Microsoft.Logic/workflows|WorkflowRuntime|
|||Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|
|Lote do Azure|[Logs de diagnóstico do Lote do Azure](https://docs.microsoft.com/en-us/azure/batch/batch-diagnostics)|Microsoft.Batch/batchAccounts|ServiceLog|
|Automação do Azure|[Análise de log para automação do Azure](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|Microsoft.Automation/automationAccounts|JobLogs|
|||Microsoft.Automation/automationAccounts|JobStreams|
|Hubs de Eventos|[Logs de diagnóstico dos Hubs de Eventos do Azure](https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-diagnostic-logs)|Microsoft.EventHub/namespaces|ArchiveLogs|
|||Microsoft.EventHub/namespaces|OperationalLogs|
|Stream Analytics|[Logs de diagnóstico do trabalho](https://docs.microsoft.com/en-us/azure/stream-analytics/stream-analytics-job-diagnostic-logs)|Microsoft.StreamAnalytics/streamingjobs|Execução|
|||Microsoft.StreamAnalytics/streamingjobs|Criação|
|Barramento de Serviço|[Logs de diagnóstico do Barramento de Serviço do Azure](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-diagnostic-logs)|Microsoft.ServiceBus/namespaces|OperationalLogs|

### <a name="azure-active-directory-reporting"></a>Relatórios do Azure Active Directory
O Active Directory do Azure (Azure AD) inclui relatórios de auditoria, atividade e segurança para seu diretório. O [Relatório de Auditoria do Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-guide) ajuda os clientes a identificar as ações com privilégios que ocorreram em seu Azure Active Directory. Ações com privilégios incluem alterações de elevação (por exemplo, criação de funções ou redefinições de senha), alteração de configurações de política (por exemplo, políticas de senha) ou alterações na configuração de diretório (por exemplo, as alterações às configurações de federação de domínio).

Os relatórios fornecem o registro de auditoria para o nome do evento, o ator que executou a ação, o recurso de destino afetado pela alteração e a data e hora (em UTC). Os clientes podem recuperar a lista dos eventos de auditoria do Azure Active Directory por meio do [portal do Azure](https://portal.azure.com/), conforme descrito em [Exibir os Logs de Auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). Aqui está uma lista dos relatórios incluídos:

| Relatórios de segurança | Relatórios de atividades | Relatórios de auditoria |
| :--------------- | :--------------- | :------------ |
|Entradas de fontes desconhecidas| Uso do aplicativo: resumo| Relatório de auditoria de diretório|
|Entradas após várias falhas|  Uso do aplicativo: detalhado||
|Entradas de várias geografias|    Painel do aplicativo||
|Entradas de endereços IP com atividade suspeita|   Erros de provisionamento de conta||
|Atividades de entrada irregulares|    Dispositivos de usuário individual||
|Entradas de dispositivos possivelmente infectados|   Atividade de usuário individual||
|Usuários com atividade de entrada anômala| Relatório de atividade de grupos||
||Relatório de atividade de registro de redefinição de senha||
||Atividade de redefinição de senha|||

Os dados desses relatórios podem ser úteis para os aplicativos, como sistemas SIEM, auditoria e ferramentas de business intelligence. As APIs de relatório do Azure AD fornecem acesso programático aos dados através de um conjunto de APIs baseadas em REST. Você pode chamar essas [APIs](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started) em várias ferramentas e linguagens de programação.

Os eventos no relatório de auditoria do Azure AD são mantidos por 180 dias.

> [!Note]
> Para obter mais informações sobre a retenção de relatórios, consulte [Políticas de retenção de relatórios do Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-retention)

Para clientes interessados no armazenamento de seus eventos de auditoria por períodos de retenção mais longos, a API de Relatório pode ser usada regularmente para efetuar pull dos [eventos de auditoria](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-audit-events) em um armazenamento de dados separado.

### <a name="virtual-machine-logs-using-azure-diagnostics"></a>Logs da Máquina Virtual que usam o Diagnóstico do Azure
O [Diagnóstico do Azure](https://docs.microsoft.com/azure/azure-diagnostics) é a funcionalidade do Azure que habilita a coleta de dados de diagnóstico em um aplicativo implantado. Você pode usar a extensão de diagnóstico de várias fontes diferentes. As que têm suporte no momento são as [Funções de Trabalho ou Web do Serviço de Nuvem do Azure](https://docs.microsoft.com/azure/cloud-services/cloud-services-choose-me),

![Logs da Máquina Virtual que usam o Diagnóstico do Azure](./media/azure-log-audit/azure-log-audit-fig3.png)

[Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/) que executam o Microsoft Windows e o [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview).

É possível habilitar o Diagnóstico do Azure em uma máquina virtual usando os seguintes:

-   Se estiver usando o Visual Studio, consulte [Usar o Visual Studio para rastrear Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/vs-azure-tools-debug-cloud-services-virtual-machines)

-   [Configurar o Diagnóstico do Azure em uma Máquina Virtual do Azure remotamente](https://docs.microsoft.com/azure/virtual-machines-dotnet-diagnostics)

-   [Usar o PowerShell para configurar o diagnóstico nas Máquinas Virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

-   [Criar uma máquina virtual do Windows com monitoramento e diagnóstico usando o modelo do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="storage-analytics"></a>Análise de Armazenamento
A [Análise de Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics) executa registro em log e fornece dados de métrica para uma conta de armazenamento. Você pode usar esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento. O log do Storage Analytics está disponível para os [serviços Blob, Fila e Tabela.](https://docs.microsoft.com/azure/storage/storage-introduction) A análise de armazenamento registra informações detalhadas sobre solicitações bem-sucedidas e com falha para um serviço de armazenamento.

Essas informações podem ser usadas para monitorar solicitações individuais e diagnosticar problemas com um serviço de armazenamento. As solicitações são registradas em uma base de melhor esforço. As entradas de log são criadas somente se há solicitações feitas no ponto de extremidade de serviço. Por exemplo, se uma conta de armazenamento tiver atividades em seu ponto de extremidade Blob, mas não em seus pontos de extremidade Tabela ou Fila, somente os logs pertencentes ao serviço Blob serão criados.

Para usar a análise de armazenamento, você deve habilitá-la separadamente para cada serviço que você deseja monitorar. Você pode habilitá-la no [portal do Azure](https://portal.azure.com/); para obter detalhes, consulte [Monitorar uma conta de armazenamento no portal do Azure.](https://docs.microsoft.com/azure/storage/storage-monitor-storage-account) Você também pode habilitar a análise de armazenamento programaticamente por meio da API REST ou da biblioteca de cliente. Use a operação Definir Propriedades do Serviço para habilitar o Storage Analytics individualmente em cada serviço.

Os dados agregados são armazenados em um blob conhecido (para registro em log) e em tabelas conhecidas (para métricas), que podem ser acessados usando os serviços de Blob e APIs do serviço de tabela

O Storage Analytics tem um limite de 20 TB na quantidade de dados armazenados, que não depende do limite total da conta de armazenamento. Todos os logs são armazenados em [blobs de blocos](https://docs.microsoft.com/azure/storage/storage-analytics) em um contêiner chamado $logs, que é criado automaticamente quando o Storage Analytics é habilitado em uma conta de armazenamento.

> [!Note]
> Para obter mais informações sobre cobrança e políticas de retenção de dados, consulte [Storage Analytics e cobrança.](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-and-billing)
>
> [!Note]
> Para obter mais informações sobre os limites da conta de armazenamento, consulte [Escalabilidade e metas de desempenho do Armazenamento do Azure.](https://docs.microsoft.com/azure/storage/storage-scalability-targets)

Os tipos de solicitações autenticadas e anônimas a seguir são registrados.



| Autenticada  | Anônima|
| :------------- | :-------------|
| Solicitações bem-sucedidas | Solicitações bem-sucedidas |
|Solicitações com falha, incluindo tempo limite, limitação, rede, autorização e outros erros | Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado), incluindo solicitações bem-sucedidas e com falha |
| Solicitações que usam uma SAS (Assinatura de Acesso Compartilhado), incluindo solicitações bem-sucedidas e com falha |Erros de tempo limite para o cliente e o servidor |
|   Solicitações de dados de análise |    Solicitações GET com falha com o código de erro 304 (Não Modificado) |
| As solicitações feitas pela própria análise de armazenamento, como criação de log ou exclusão, não estão conectadas. Uma lista completa dos dados registrados está documentada nos tópicos [Storage Analytics Logged Operations and Status Messages](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) (Mensagens de operações e status registradas do Storage Analytics) e [Storage Analytics Log Format](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format) (Formato do log do Storage Analytics). | Todas as outras solicitações anônimas com falha não estão conectadas. Uma lista completa dos dados registrados está documentada em [Mensagens de operações e status registradas do Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-logged-operations-and-status-messages) e [Formato do log do Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics-log-format). |

### <a name="azure-networking-logs"></a>Logs de rede do Azure
O log e monitoramento de rede no Azure é completo e abrange duas categorias amplas:

-   [Observador de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-watcher) – o monitoramento de rede baseado em cenário é fornecido com os recursos do Observador de Rede. Esse serviço inclui a captura de pacotes, próximo salto, verificação do fluxo de IP, exibição do grupo de segurança e logs de fluxo de NSG. O monitoramento no nível do cenário fornece uma exibição completa dos recursos de rede em contraste com o monitoramento de recursos de rede individual.

-   [Monitoramento de recursos](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-resource-level-monitoring) – o monitoramento no nível do recurso é composto por quatro recursos: logs de diagnóstico, métricas, solução de problemas e integridade de recursos. Todos esses recursos são compilados no nível do recurso da rede.

![Logs de rede do Azure](./media/azure-log-audit/azure-log-audit-fig4.png)

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

**Log de fluxo do NSG** – os logs de fluxo dos Grupos de Segurança de Rede permitem capturar logs relacionados ao tráfego que são permitidos ou negados pelas regras de segurança no grupo. Esses logs de fluxo são escritos no formato JSON e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

### <a name="network-security-group-flow-logging"></a>Log de fluxo do Grupo de Segurança de Rede

Os [logs de fluxo do Grupo de Segurança de Rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) são um recurso do Observador de Rede que permite exibir informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede. Esses logs de fluxo são escritos no formato JSON e mostram os fluxos de entrada e saída por regra, a NIC à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

Embora os logs de fluxo sejam destinados aos Grupos de Segurança da Rede, eles não são exibidos como os outros logs. Os logs de fluxo são armazenados apenas em uma conta de armazenamento.

As mesmas políticas de retenção, como visto em outros logs, aplicam-se aos logs de fluxo. Os logs têm uma política de retenção que pode ser definida desde 1 dia até 365 dias. Se uma política de retenção não for definida, os logs serão mantidos para sempre.

**Logs de diagnóstico**

Os eventos periódicos e espontâneas são criados pelos recursos da rede e registrados nas contas de armazenamento, enviados para um Hub de Eventos ou Log Analytics. Esses logs fornecem informações sobre a integridade de um recurso. Esses logs podem ser visualizados em ferramentas como o Power BI e o Log Analytics. Para saber como exibir os logs de diagnóstico, acesse [Log Analytics.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics)

![Logs de diagnóstico](./media/azure-log-audit/azure-log-audit-fig5.png)

Os logs de diagnóstico estão disponíveis para o [Balanceador de Carga](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [Grupos de Segurança da Rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), Rotas e o [Gateway de Aplicativo.](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

O Observador de Rede fornece uma exibição dos logs de diagnóstico. Essa exibição contém todos os recursos de rede que oferecem suporte ao log de diagnóstico. Nessa exibição, você pode habilitar e desabilitar os recursos de rede de modo rápido e prático.


Além das funcionalidades de log acima, atualmente, o Observador de Rede conta com as seguintes funcionalidades:
- [Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) – fornece uma exibição no nível da rede que mostra as diversas interconexões e associações entre os recursos de rede em um grupo de recursos.

- [Captura de Pacote Variável](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) – captura dados do pacote dentro e fora de uma máquina virtual. As opções avançadas de filtragem e controles ajustados como a capacidade de definir o tempo e as limitações de tamanho fornecem versatilidade. Os dados do pacote podem ser armazenados em um repositório de blobs ou no disco local no formato .cap.

-   [Verificações do fluxo de IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) – verifica se um pacote é permitido ou negado com base nos parâmetros do pacote com cinco tuplas das informações do fluxo (IP de Destino, IP de Origem, Porta de Destino, Porta de Origem e Protocolo). Se o pacote for negado por um grupo de segurança, a regra e o grupo que negaram o pacote serão retornados.

-   [Próximo salto](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) – determina o próximo salto para os pacotes encaminhados no Azure Network Fabric, permitindo diagnosticar as rotas definidas pelo usuário configuradas incorretamente.

-   [Exibição do grupo de segurança](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) – obtém as regras de segurança efetivas e aplicadas que são aplicadas em uma VM.

-   [Solução de problemas de Gateway de Rede Virtual e Conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) – fornece a capacidade de resolver problemas de Gateways de Rede Virtual e Conexões.

-   [Limites de assinatura da rede](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#network-subscription-limits) – permite exibir o uso de recursos de rede em relação aos limites.

### <a name="application-insight"></a>Application Insights

O [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-overview) é um serviço APM (Gerenciamento de Desempenho de Aplicativos) extensível para desenvolvedores da Web em várias plataformas. Use-o para monitorar seu aplicativo Web online. Ele detecta anomalias de desempenho automaticamente. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo.

 Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

 Ele funciona com aplicativos em uma ampla variedade de plataformas incluindo .NET e J2EE, hospedados localmente ou na nuvem. Ele é integrado ao seu processo de DevOps e tem pontos de conexão para várias ferramentas de desenvolvimento.

![Application Insights](./media/azure-log-audit/azure-log-audit-fig6.png)

O Application Insights é indicado para a equipe de desenvolvimento, para ajudá-lo a compreender como está o desempenho de seu aplicativo e como ele está sendo usado. Ele monitora:

-   **Taxas de solicitação, tempos de resposta e taxas de falha** - descubra quais páginas estão mais populares, em que momentos do dia, e onde os usuários estão. Confira as páginas que têm melhor desempenho. Se as taxas de falha e os tempos de resposta ficam altos quando há mais solicitações, possivelmente você tem um problema de alocação de recursos.

-   **Taxas de dependência, tempos de resposta e taxas de falha** - descubra se os serviços externos estão atrasando você.

-   **Exceções** – analise as estatísticas agregadas ou escolha instâncias específicas e faça uma busca detalhada no rastreamento de pilha e nas solicitações relacionadas. A maioria das exceções de navegador e servidor são relatadas.

-   **Exibições de página e o desempenho de carregamento** - relatados por navegadores dos usuários.

-   **Chamadas AJAX** de páginas da web - taxas, tempos de resposta e taxas de falha.

-   **Contagens de seção e usuários**.

-   **Contadores de desempenho** de suas máquinas de servidor Linux ou Windows server, como CPU, memória e uso da rede.

-   **Diagnósticos de host** do Docker ou do Azure.

-   **Logs de rastreamento de diagnóstico** do seu aplicativo - para que você possa correlacionar eventos de rastreamento com solicitações.

-   **Métricas e eventos personalizados** que você escreve em código de cliente ou servidor, para acompanhar os eventos de negócios, como itens vendidos ou vitórias.

**Lista de cenários de integração e descrição:**

| Cenários de integração | Descrição |
| --------------------- | :---------- |
|[Mapa do aplicativo](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-app-map)|Os componentes de seu aplicativo, com as principais métricas e alertas.||
|[Pesquisa de diagnóstico para dados da instância](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-diagnostic-search)| pesquise e filtre eventos como solicitações, exceções, chamadas de dependência, rastreamentos de log e exibições de página.||
|[Metrics Explorer para dados agregados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-metrics-explorer)|explore, filtre e segmente dados agregados, como taxas de solicitações, falhas e exceções; tempos de resposta e tempos de carregamento de página.||
|[Painéis](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-dashboards#dashboards)|faça um mashup de dados de vários recursos e compartilhe com outras pessoas. Excelente para aplicativos com vários componentes e para exibição contínua no ambiente de equipe.||
|[Live Metrics Stream](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-live-stream)|quando implantar um novo build, acompanhe esses indicadores de desempenho quase em tempo real para verificar se tudo está funcionando conforme esperado.||
|[Analytics](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-analytics)|responda perguntas difíceis sobre o desempenho e o uso do seu aplicativo usando essa poderosa linguagem de consulta.||
|[Alertas automáticos e manuais](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-alerts)|alertas automáticos se adaptam aos padrões normais de telemetria do seu aplicativo e são disparados quando há algo fora do padrão normal. Você também pode definir alertas em níveis específicos de métricas padrão ou personalizadas.||
|[Visual Studio](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio)|Veja dados de desempenho no código. Vá até o código dos rastreamentos de pilha.||
|[Power BI](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-power-bi)|Integre as métricas de uso com outro business intelligence.||
|[API REST](https://dev.applicationinsights.io/)|Escreva o código para executar consultas em suas métricas e dados brutos.||
|[Exportação contínua](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-export-telemetry)|Exportação em massa de dados brutos para armazenamento no momento de sua chegada.||

### <a name="azure-security-center-alerts"></a>Alertas da Central de Segurança do Azure
A [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) coleta, analisa e integra automaticamente os dados de logs de seus recursos do Azure, da rede e das soluções de parceiros conectadas, como firewall e soluções de Endpoint Protection, a fim de detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque.

A detecção de ameaças da Central de Segurança funciona coletando informações de segurança de seus recursos do Azure, de rede e de soluções de parceiros conectados automaticamente. Ele analisa essas informações geralmente correlacionando informações de várias fontes para identificar ameaças. Os alertas de segurança são priorizados na Central de Segurança, juntamente com recomendações sobre como corrigir a ameaça.

![Central de Segurança do Azure](./media/azure-log-audit/azure-log-audit-fig7.png)

A Central de Segurança emprega análise de segurança avançada, que vai além das abordagens baseadas em assinatura. Inovações em tecnologias de dados grandes e [aprendizado de máquina](https://azure.microsoft.com/blog/machine-learning-in-azure-security-center/) são aplicadas para avaliar os eventos em toda a malha de nuvem – detectando ameaças que seriam impossíveis de identificar usando abordagens manuais e prevendo a evolução dos ataques. Essas análises de segurança incluem:

-   **Inteligência em ameaças integrada:** procura atores mal-intencionados conhecidos aplicando a inteligência global em ameaças de produtos e serviços da Microsoft, da DCU (Unidade de Crimes Digitais) da Microsoft, do MSRC (Microsoft Security Response Center) e de feeds externos.

-   **Análise comportamental:** aplica padrões conhecidos para descobrir comportamentos mal-intencionados.

-   **Detecção de anomalias:** usa estatísticas de criação de perfil para criar uma linha de base histórica. Ela o alertará sobre desvios das linhas de base estabelecidas em conformidade com um vetor de possível ataque.


Muitas operações de segurança e equipes de resposta a incidentes contam com uma solução SIEM (Gerenciamento de eventos e informações de segurança) como ponto de partida para separação e investigação de alertas de segurança. Com a integração de log do Azure, os clientes podem sincronizar alertas da Central de Segurança e eventos de segurança da máquina virtual, coletados pelo Diagnóstico do Azure e pelos Logs de Auditoria do Azure, com suas soluções de análise de log e SIEM quase em tempo real.


## <a name="log-analytics"></a>Log Analytics

O Log Analytics é um serviço do [OMS (Operations Management Suite)](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) que ajuda a coletar e analisar dados gerados pelos recursos em seus ambientes local e de nuvem. Ele fornece informações em tempo real usando a pesquisa integrada e painéis personalizados para analisar prontamente milhões de registros em todas as suas cargas de trabalho e todos os seus servidores, independentemente de sua localização física.

![Log Analytics](./media/azure-log-audit/azure-log-audit-fig8.png)

No centro no Log Analytics está o repositório do OMS, que está hospedado na nuvem do Azure. Os dados são coletados para o repositório de fontes conectadas configurando fontes de dados e adicionando soluções à sua assinatura. Fontes de dados e soluções criarão diferentes tipos de registro que têm seus próprios conjuntos de propriedades, mas ainda podem ser analisados juntos em consultas no repositório. Isso permite usar as mesmas ferramentas e métodos para trabalhar com diferentes tipos de dados coletados por fontes diferentes.

Fontes conectadas são os computadores e outros recursos que geram dados coletados pelo Log Analytics. Isso pode incluir os agentes instalados em computadores [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) e [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) que se conectam diretamente ou agentes em um [grupo de gerenciamento conectado do System Center Operations Manager.](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents) O Log Analytics também pode coletar dados do [armazenamento do Azure.](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)

[Fontes de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources) são os diferentes tipos de dados coletados de cada fonte conectada. Isso inclui eventos e [dados de desempenho](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters) de agentes [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-windows-events) e Linux, além de fontes como [logs do IIS](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-iis-logs) e [logs de texto personalizado.](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-custom-logs) Você configura cada fonte de dados que deseja coletar e a configuração é fornecida automaticamente para cada fonte conectada.

Há quatro maneiras diferentes de [coletar logs e métricas para os serviços do Azure:](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-storage)
1.  Direcionamento do diagnóstico do Azure para o Log Analytics (Diagnóstico na tabela a seguir)

2.  Diagnóstico do Azure para o armazenamento do Azure e o Log Analytics (Armazenamento na tabela a seguir)

3.  Conectores para serviços do Azure (Conectores na tabela a seguir)

4.  Scripts para coletar e postar dados para o Log Analytics (espaços em branco na tabela a seguir e para os serviços que não estão listados)

| O Barramento de | Tipo de recurso | Logs | Métricas | Solução |
| :------ | :------------ | :--- | :------ | :------- |
|Application gateways|  Microsoft.Network/<br>applicationGateways|  Diagnostics|Diagnostics|    [Análise de Gateway de](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics) [Aplicativo do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-application-gateway-analytics-solution-in-log-analytics)|
|Application insights||     Conector|  Conector|  [Conector do Application](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/) [Insights (Visualização)](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)|
|Contas de automação|   Microsoft.Automation/<br>AutomationAccounts|    Diagnostics||       [Mais informações](https://docs.microsoft.com/en-us/azure/automation/automation-manage-send-joblogs-log-analytics)|
|Contas do Lote|    Microsoft.Batch/<br>batchAccounts|  Diagnostics|    Diagnostics||
|Serviços de Nuvem clássicos||       Armazenamento||       [Mais informações](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-storage-iis-table)|
|Serviços cognitivos|    Microsoft.CognitiveServices/<br>accounts|       Diagnostics|||
|Data Lake Analytics|   Microsoft.DataLakeAnalytics/<br>accounts|   Diagnostics|||
|Data Lake Store|   Microsoft.DataLakeStore/<br>accounts|   Diagnostics|||
|Namespace do Hub de Eventos|   Microsoft.EventHub/<br>namespaces|  Diagnostics|    Diagnostics||
|Hubs IoT|  Microsoft.Devices/<br>IotHubs||     Diagnostics||
|Cofre da Chave| Microsoft.KeyVault/<br>vaults|  Diagnostics  || [KeyVault Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-key-vault)|
|Balanceadores de Carga|    Microsoft.Network/<br>loadBalancers|    Diagnostics|||
|Aplicativos Lógicos|    Microsoft.Logic/<br>workflows|  Diagnostics|    Diagnostics||
||Microsoft.Logic/<br>integrationAccounts||||
|Grupos de segurança de rede|   Microsoft.Network/<br>networksecuritygroups|Diagnostics||   [Análise de Grupo de Segurança de Rede do Azure](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-networking-analytics#azure-network-security-group-analytics-solution-in-log-analytics)|
|Cofres de recuperação|   Microsoft.RecoveryServices/<br>vaults|||[Análise dos Serviços de Recuperação do Azure (Visualização)](https://github.com/krnese/AzureDeploy/blob/master/OMS/MSOMS/Solutions/recoveryservices/)|
|Serviços de pesquisa|   Microsoft.Search/<br>searchServices|    Diagnostics|    Diagnostics||
|Namespace do Barramento de Serviço| Microsoft.ServiceBus/<br>namespaces|    Diagnostics|Diagnostics|    [Análise do Barramento de Serviço (Visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-servicebus-solution)|
|Service Fabric||       Armazenamento||    [Análise do Service Fabric (visualização)](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-service-fabric)|
|SQL (v12)| Microsoft.Sql/<br>servers/<br>databases||       Diagnostics||
||Microsoft.Sql/<br>servers/<br>elasticPools||||
|Armazenamento|||         Script| [Análise do Azure Storage (Visualização)](https://github.com/Azure/azure-quickstart-templates/tree/master/oms-azure-storage-analytics-solution)|
|Máquinas Virtuais|  Microsoft.Compute/<br>virtualMachines|  Extensão|  Extensão||
||||Diagnostics||
|Conjuntos de dimensionamento de Máquinas Virtuais|   Microsoft.Compute/<br>virtualMachines    ||Diagnostics||
||Microsoft.Compute/<br>virtualMachineScaleSets/<br>virtualMachines||||
|Farms do servidor Web|Microsoft.Web/<br>serverfarms||   Diagnostics
|Sites| Microsoft.Web/<br>sites ||      Diagnostics|    [Mais informações](https://github.com/Azure/azure-quickstart-templates/tree/master/101-webappazure-oms-monitoring)|
||Microsoft.Web/<br>sites/<br>slots|||||


## <a name="log-integration-with-on-premises-siem-systems"></a>Integração de log com sistemas SIEM locais
A [integração de log do Azure](https://www.microsoft.com/download/details.aspx?id=53324) permite que você integre logs brutos de recursos do Azure em sistemas **SIEM (Segurança da Informação e Gerenciamento de Evento)** locais.

![Integração de log](./media/azure-log-audit/azure-log-audit-fig9.png)

A integração de log do Azure coleta o Diagnóstico do Azure das máquinas virtuais do Windows (WAD), dos Logs de Atividades do Azure, dos alertas da Central de Segurança do Azure e dos logs do Provedor de Recursos do Azure. Esta integração fornece um painel unificado para todos os seus ativos, locais ou na nuvem, para que você possa agregar, correlacionar, analisar e emitir alertas de eventos de segurança.



Atualmente, a integração de log do Azure dá suporte à integração de Logs de Atividades do Azure, do log de Eventos do Windows de máquinas virtuais Windows em sua assinatura do Azure, de alertas da Central de Segurança do Azure, de logs de Diagnóstico do Azure e logs de auditoria do Azure Active Directory.

| Tipo de log | Log Analytics que dá suporte a JSON (Splunk, ArcSight, Qradar) |
| :------- | :-------------------------------------------------------- |
|Logs de auditoria do AAD|    sim|
|Logs de atividade| Sim|
|Alertas do ASC |Sim|
|Logs de diagnóstico (logs de recurso)|  Sim|
|Logs da VM|   Sim, por meio de eventos encaminhados e não por meio do JSON|


A tabela a seguir explica a categoria de Log e os detalhes de integração do SIEM.

[Introdução à integração de log do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-get-started) – esse tutorial explica as etapas de instalação da integração de log do Azure, bem como a integração de logs do armazenamento WAD do Azure, Logs de Atividades do Azure, alertas da Central de Segurança do Azure e logs de auditoria do Azure Active Directory.

Cenários de integração

-   [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/) – esta postagem de blog mostra a você como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar.

-   [Perguntas frequentes sobre o log de integração do Azure](https://docs.microsoft.com/azure/security/security-azure-log-integration-faq) – encontre as respostas para as perguntas frequentes sobre a integração de log do Azure.

-   [Integração dos alertas da Central de Segurança com a Integração de Log do Azure](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration) – este documento mostra como sincronizar os alertas da Central de Segurança, juntamente com os eventos de segurança de máquina virtual coletados pelo Diagnóstico do Azure e pelos Logs de Auditoria do Azure, com o Log Analytics ou com a solução SIEM.

## <a name="next-steps"></a>Próximas etapas

- [Auditoria e log](https://www.microsoft.com/trustcenter/security/auditingandlogging)

Proteger dados, mantendo a visibilidade e respondendo rapidamente aos alertas de segurança em tempo hábil

- [Log de segurança e coleta de logs de auditoria no Azure](https://azure.microsoft.com/resources/videos/security-logging-and-audit-log-collection/)

Quais configurações você precisa impor para ter certeza de que as instâncias do Azure estão coletando os logs de Segurança e Auditoria corretos.

- [Definir as configurações de auditoria de um conjunto de sites](https://support.office.com/article/Configure-audit-settings-for-a-site-collection-A9920C97-38C0-44F2-8BCB-4CF1E2AE22D2?ui=&rs=&ad=US)

Como administrador do conjunto de sites, uma pessoa pode recuperar o histórico das ações executadas por um usuário específico e o histórico das ações executadas em determinado intervalo de datas. 

- [Pesquisar o log de auditoria no Centro de Conformidade e Segurança do Office 365](https://support.office.com/article/Search-the-audit-log-in-the-Office-365-Security-Compliance-Center-0d4d0f35-390b-4518-800e-0c7ec95e946c?ui=&rs=&ad=US)

Uma pessoa pode usar o Centro de Conformidade e Segurança do Office 365 para pesquisar o log de auditoria unificado a fim de exibir as atividades de usuário e do administrador em sua organização do Office 365.


