---
title: Transmitir dados de monitoramento do Azure para o Hub de eventos e parceiros externos
description: Saiba como transmitir seus dados de monitoramento do Azure para um hub de eventos para obter os dados em um SIEM de parceiro ou em uma ferramenta de análise.
services: azure-monitor
author: bwren
ms.author: bwren
ms.topic: conceptual
ms.date: 07/15/2020
ms.subservice: ''
ms.openlocfilehash: 784ecd5c0539ee0dc84c8afd6b85dbc10a154982
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92206876"
---
# <a name="stream-azure-monitoring-data-to-an-event-hub-or-external-partner"></a>Transmitir dados de monitoramento do Azure para um hub de eventos ou parceiro externo

O Azure Monitor fornece uma solução completa de monitoramento de pilha completa para aplicativos e serviços no Azure, em outras nuvens e no local. Além de usar Azure Monitor para analisar os dados e aproveitá-los para diferentes cenários de monitoramento, talvez seja necessário enviá-los para outras ferramentas de monitoramento em seu ambiente. Na maioria dos casos, o método mais eficaz para transmitir dados de monitoramento para ferramentas externas é usar os [hubs de eventos do Azure](../../event-hubs/index.yml). Este artigo fornece uma breve descrição sobre como fazer isso e lista alguns dos parceiros nos quais você pode enviar dados. Alguns têm integração especial com o Azure Monitor e podem ser hospedados no Azure.  

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace dos hubs de eventos

Antes de configurar o streaming para qualquer fonte de dados, você precisa [criar um namespace de hubs de eventos e um hub de eventos](../../event-hubs/event-hubs-create.md). Esse namespace e hub de eventos é o destino para todos os seus dados de monitoramento. Um namespace dos Hubs de Eventos é um agrupamento lógico de hubs de eventos que compartilham a mesma política de acesso, assim como uma conta de armazenamento tem blobs individuais dentro dessa conta de armazenamento. Considere os seguintes detalhes sobre o namespace de hubs de eventos e os hubs de eventos que você usa para streaming de dados de monitoramento:

* O número de unidades de taxa de transferência permite aumentar a escala de taxa de transferência para os hubs de eventos. Em geral, apenas uma unidade de taxa de transferência é necessária. Se precisar escalar verticalmente conforme o uso do log aumentar, você poderá aumentar manualmente o número de unidades de produtividade para o namespace ou habilitar a inflação automática.
* O número de partições permite paralelizar o consumo em muitos consumidores. Uma única partição pode dar suporte a até 20MBps ou aproximadamente 20.000 mensagens por segundo. Dependendo da ferramenta que consome os dados, poderá ou não dar suporte ao consumo de várias partições. Quatro partições são razoáveis para começar se você não tiver certeza sobre o número de partições a serem definidas.
* Você define a retenção de mensagens em seu hub de eventos para pelo menos 7 dias. Se a ferramenta de consumo ficar inativa por mais de um dia, isso garantirá que a ferramenta possa escolher onde parou para eventos de até 7 dias.
* Você deve usar o grupo de consumidores padrão para seu hub de eventos. Não é necessário criar outros grupos de consumidores ou utilizar um grupo de consumidores separado, exceto se você planejar ter duas ferramentas diferentes que consumam os mesmos dados do mesmo hub de eventos.
* Para o log de atividades do Azure, você escolhe um namespace de hubs de eventos e Azure Monitor cria um hub de eventos dentro desse namespace chamado _insights-logs-Operational-logs_. Para outros tipos de log, você pode escolher um hub de eventos existente ou ter Azure Monitor criar um hub de eventos por categoria de log.
* A porta de saída 5671 e 5672 normalmente deve ser aberta no computador ou VNET que consome dados do hub de eventos.

## <a name="monitoring-data-available"></a>Dados de monitoramento disponíveis
[Fontes de dados de monitoramento para Azure monitor](data-sources.md) descreve as diferentes camadas de dados para aplicativos do Azure e os tipos de dados de monitoramento disponíveis para cada um. A tabela a seguir lista cada uma dessas camadas e uma descrição de como esses dados podem ser transmitidos para um hub de eventos. Siga os links fornecidos para obter mais detalhes.

| Camada | Dados | Método |
|:---|:---|:---|
| [Locatário do Azure](data-sources.md#azure-tenant) | Logs de auditoria do Azure Active Directory | Defina uma configuração de diagnóstico de locatário em seu locatário do AAD. Consulte  [tutorial: transmitir Azure Active Directory logs para um hub de eventos do Azure](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) para obter detalhes. |
| [Assinatura do Azure](data-sources.md#azure-subscription) | Log de Atividades do Azure | Crie um perfil de log para exportar eventos do log de atividades para os hubs de eventos.  Consulte [transmitir logs da plataforma Azure para os hubs de eventos do Azure](./resource-logs.md#send-to-azure-event-hubs) para obter detalhes. |
| [Recursos do Azure](data-sources.md#azure-resources) | Métricas de plataforma<br> Logs de recursos |Ambos os tipos de dados são enviados para um hub de eventos usando uma configuração de diagnóstico de recurso. Consulte [transmitir logs de recursos do Azure para um hub de eventos](./resource-logs.md#send-to-azure-event-hubs) para obter detalhes. |
| [Sistema operacional (convidado)](data-sources.md#operating-system-guest) | Máquinas virtuais do Azure | Instale a [extensão de diagnóstico do Azure](diagnostics-extension-overview.md) em máquinas virtuais Windows e Linux no Azure. Consulte [Streaming diagnóstico do Azure dados no Hot Path usando os hubs de eventos](diagnostics-extension-stream-event-hubs.md) para obter detalhes sobre VMs do Windows e [usar a extensão de diagnóstico do Linux para monitorar métricas e logs](../../virtual-machines/extensions/diagnostics-linux.md#protected-settings) para obter detalhes sobre as VMs do Linux. |
| [Código do aplicativo](data-sources.md#application-code) | Application Insights | Application Insights não fornece um método direto para transmitir dados para os hubs de eventos. Você pode [Configurar a exportação contínua](../app/export-telemetry.md) dos dados de Application insights para uma conta de armazenamento e, em seguida, usar um aplicativo lógico para enviar os dados para um hub de eventos, conforme descrito em [streaming manual com aplicativo lógico](#manual-streaming-with-logic-app). |

## <a name="manual-streaming-with-logic-app"></a>Streaming manual com aplicativo lógico
Para dados que você não pode transmitir diretamente para um hub de eventos, você pode gravar no armazenamento do Azure e, em seguida, usar um aplicativo lógico disparado por tempo que [extrai dados do armazenamento de BLOBs](../../connectors/connectors-create-api-azureblobstorage.md#add-action) e [envia-os por push como uma mensagem para o Hub de eventos](../../connectors/connectors-create-api-azure-event-hubs.md#add-action). 


## <a name="partner-tools-with-azure-monitor-integration"></a>Ferramentas de parceiro com integração Azure Monitor

O roteamento dos dados de monitoramento para um hub de eventos com o Azure Monitor permite que você integre facilmente com as ferramentas de monitoramento e SIEM externos. Exemplos de ferramentas com integração de Azure Monitor incluem o seguinte:

| Ferramenta | Hospedado no Azure | Descrição |
|:---|:---| :---|
|  IBM QRadar | Não | O Microsoft Azure DSM e protocolo de Hub de Eventos do Microsoft Azure estão disponíveis para download do [site de suporte da IBM](https://www.ibm.com/support). Você pode saber mais sobre a integração com o Azure em [configuração do QRADAR DSM](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0). |
| Splunk | Não | [Microsoft Azure Add-On para Splunk](https://splunkbase.splunk.com/app/3757/) é um projeto de código-fonte aberto disponível no splunkbase. <br><br> Se você não puder instalar um complemento em sua instância do Splunk, se, por exemplo, estiver usando um proxy ou em execução na nuvem do Splunk, você poderá encaminhar esses eventos para o coletor de eventos HTTP do Splunk usando o [Azure function para Splunk](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é disparado por novas mensagens no Hub de eventos. |
| SumoLogic | Não | As instruções para configurar o SumoLogic para consumir dados de um hub de eventos estão disponíveis em [coletar logs para o aplicativo de auditoria do Azure do hub de eventos](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure-Audit/02Collect-Logs-for-Azure-Audit-from-Event-Hub). |
| ArcSight | Não | O conector inteligente do hub de eventos ArcSight do Azure está disponível como parte da [coleção de conectores inteligentes do ArcSight](https://community.softwaregrp.com/t5/Discussions/Announcing-General-Availability-of-ArcSight-Smart-Connectors-7/m-p/1671852). |
| Servidor syslog | Não | Se você quiser transmitir dados Azure Monitor diretamente para um servidor syslog, poderá usar uma [solução baseada em uma função do Azure](https://github.com/miguelangelopereira/azuremonitor2syslog/).
| LogRhythm | Não| As instruções para configurar o LogRhythm para coletar logs de um hub de eventos estão disponíveis [aqui](https://logrhythm.com/six-tips-for-securing-your-azure-cloud-environment/). 
|Logz.io | Sim | Para obter mais informações, consulte [introdução ao monitoramento e registro em log usando o LOGZ.Io para aplicativos Java em execução no Azure](/azure/developer/java/fundamentals/java-get-started-with-logzio)

Outros parceiros também podem estar disponíveis. Para obter uma lista mais completa de todos os parceiros de Azure Monitor e seus recursos, consulte [integrações de parceiro Azure monitor](partners.md).

## <a name="next-steps"></a>Próximas etapas
* [Arquivar o log de atividades em uma conta de armazenamento](./activity-log.md#legacy-collection-methods)
* [Leia a visão geral do log de atividades do Azure](./platform-logs-overview.md)
* [Configurar um alerta com base em um evento do log de atividades](./alerts-log-webhook.md)

