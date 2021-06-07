---
title: Referência de esquema de fonte de dados do Azure Sentinel
description: Este artigo lista os esquemas de fonte de dados do Azure e de terceiros com suporte do Azure Sentinel, com links para a documentação de referência.
author: batamig
ms.author: bagol
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.custom: ''
ms.date: 01/14/2021
ms.openlocfilehash: 84d5f1e4035fb730d33b7bc8dd375273610f0bfb
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771933"
---
# <a name="data-source-schema-reference"></a>Referência de esquema de fonte de dados

Este artigo lista esquemas de fonte de dados do Azure e de terceiros com suporte, com links para a documentação de referência.

## <a name="azure-data-sources"></a>Fontes de dados do Azure

| Type                             | Fonte de dados             | Log Analytics TableName | Referência de esquema |
| -------------------------------- | ---------------------- | ---------------------- | ---------------- |
| **Azure**                            | Azure Active Directory | SigninEvents           | [Propriedades de entrada dos relatórios de atividade do Azure AD](/graph/api/resources/signin#properties) |
| **Azure**                            | Azure Active Directory | AuditLogs              | [Referência do Azure Monitor AuditLogs](/azure/azure-monitor/reference/tables/auditlogs) |
| **Azure**                            | Azure Active Directory | AzureActivity          | [Referência do Azure Monitor AzureActivity](/azure/azure-monitor/reference/tables/azureactivity) |
| **Azure**                            | Office                 | OfficeActivity         | Esquemas de API da atividade de gerenciamento do Office 365: <br>- [Esquema comum ](/office/office-365-management-api/office-365-management-activity-api-schema#common-schema)   <br>- [Esquema de administração do Exchange ](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-admin-schema) <br>- [Esquema da caixa de correio do Exchange](/office/office-365-management-api/office-365-management-activity-api-schema#exchange-mailbox-schema)  <br>- [Esquema base do SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-base-schema)   <br>- [Operações de arquivo do SharePoint](/office/office-365-management-api/office-365-management-activity-api-schema#sharepoint-file-operations) |
| **Azure**                            | Cofre de Chave do Azure         | AzureDiagnostics       | [Referência do Azure Monitor AzureDiagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) |
| **Host**                             | Linux                  | syslog                 | [Referência do Azure Monitor syslog](/azure/azure-monitor/reference/tables/syslog) |
| **Rede**                          | Logs IIS               | W3CIISLog              | [Referência do Azure Monitor W3CIISLog](/azure/azure-monitor/reference/tables/w3ciislog) |
| **Rede**                          | VMinsights             | VMConnection           | [Referência do Azure Monitor VMConnection](/azure/azure-monitor/reference/tables/vmconnection) |
| **Rede**                          | Solução de dados de transmissão     | WireData               | [Referência do Azure Monitor WireData](/azure/azure-monitor/reference/tables/wiredata) |
| **Rede**                          | Logs de fluxo do NSG          | AzureNetworkAnalytics  | [Agregação de esquema e dados no Análise de Tráfego](../network-watcher/traffic-analytics-schema.md) |
| | | | |

> [!NOTE]
> Para obter mais informações, consulte toda a [referência de dados de Azure monitor](/azure/azure-monitor/reference/).
>
## <a name="3rd-party-vendor-data-sources"></a>fontes de dados de fornecedores de terceiros

A tabela a seguir lista os fornecedores de terceiros com suporte e sua documentação de mapeamento de syslog ou formato de evento comum (CEF) para vários tipos de log com suporte, que contêm mapeamentos de campo CEF e logs de exemplo para cada tipo de categoria.

| Type |    Fornecedor |    Produto | Log Analytics TableName | Campo CEF – referência de mapeamento  |
| ----- | ----- | ----- | ----- |----- |
| **Rede** | Palo Alto   | PANORÂMICA DO SISTEMA OPERACIONAL    | CommonSecurityLog |   [Guia de integração de formato de evento comum do Pan-OS 9,0](https://docs.paloaltonetworks.com/content/dam/techdocs/en_US/pdf/cef/pan-os-90-cef-configuration-guide.pdf) (pesquise *formatos de log de estilo CEF*) |
| **Rede** | Ponto de Verificação  |ALL   | CommonSecurityLog | [Descrição de campos de log](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk109795)       |
| **Rede** | FortiGate   | ALL   | CommonSecurityLog | [Estrutura do esquema de log](https://docs.fortinet.com/document/fortigate/6.2.3/fortios-log-message-reference/738142/log-schema-structure)         |
| **Rede** | Barracuda | Firewall do Aplicativo Web |  CommonSecurityLog   | [Como configurar syslog e outros logs](https://campus.barracuda.com/product/webapplicationfirewall/doc/4259935/how-to-configure-syslog-and-other-logs/)  |
| **Rede** | Cisco | ASA | CommonSecurityLog | [Mensagens do syslog da Cisco ASA Series](https://www.cisco.com/c/en/us/td/docs/security/asa/syslog/b_syslog/about.html)    |
| **Rede** | Cisco | Potência   | CommonSecurityLog | [Mensagens do syslog de defesa contra ameaças do Cisco potência](https://www.cisco.com/c/en/us/td/docs/security/firepower/Syslogs/b_fptd_syslog_guide.pdf)    |
| **Rede** | Cisco   | Termo  | Tabela de logs personalizados  | [Formatos de log e controle de versão](https://docs.umbrella.com/deployment-umbrella/docs/log-formats-and-versioning)   |
| **Rede**   | Cisco | Meraki    | CommonSecurityLog |   [Tipos de eventos de syslog e amostras de log](https://documentation.meraki.com/zGeneral_Administration/Monitoring_and_Reporting/Syslog_Event_Types_and_Log_Samples)    |
| **Rede**   | Zscaler | Serviço de streaming do nano (NSS)|   CommonSecurityLog | [Formatando feeds do NSS](https://help.zscaler.com/zia/documentation-knowledgebase/analytics/nss/nss-feeds/formatting-nss-feeds) (somente logs da Web, firewall, DNS e de túnel) |
| **Rede**   |F5 | BigIP LTM|    CommonSecurityLog|  [Mensagens de evento e tipos de ataque](https://techdocs.f5.com/kb/en-us/products/big-ip_ltm/manuals/product/bigip-external-monitoring-implementations-13-0-0/15.html)  |
| **Rede** | F5  | BigIP ASM|    CommonSecurityLog|  [Registrando eventos de segurança do aplicativo](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-13-1-0/14.html)                                                           |
| **Rede** | Citrix  |Firewall do aplicativo Web   | CommonSecurityLog|    [Suporte ao log do formato de evento comum (CEF) no firewall do aplicativo](https://support.citrix.com/article/CTX136146) <br>  [Referência de mensagem de syslog do Netscaler 12,0](https://developer-docs.citrix.com/projects/netscaler-syslog-message-reference/en/12.0/)   |
|**Host** |Symantec | Symantec Endpoint Protection Manager (SEPM) | CommonSecurityLog|[Configurações de log externo e níveis de severidade de evento de log para Endpoint Protection Manager](https://support.symantec.com/us/en/article.tech171741.html)|
|**Host** |Trend Micro |Tudo |CommonSecurityLog | [Mapeamento de conteúdo do syslog-CEF](https://docs.trendmicro.com/en-us/enterprise/control-manager-70/appendices/syslog-mapping-cef.aspx) |
| | | | | |

> [!NOTE]
> Para obter mais informações, consulte também o [mapeamento de campo CEF e CommonSecurityLog](cef-name-mapping.md).
> 
## <a name="next-steps"></a>Próximas etapas

Saiba mais com suporte para conectores do Azure Sentinel, como CEF, syslog, direto, agente e conectores personalizados:

- [Conectar fontes de dados](connect-data-sources.md)

- [Syslog de sentinela do Azure, CEF e outros conectores de terceiros](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-syslog-cef-and-other-3rd-party-connectors-grand/ba-p/803891)