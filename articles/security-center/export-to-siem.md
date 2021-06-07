---
title: Transmita seus alertas da central de segurança do Azure para sistemas SIEM (gerenciamento de eventos e informações de segurança) e outras soluções de monitoramento
description: Saiba como transmitir seus alertas de segurança para o Azure Sentinel, SIEMs de terceiros, disparar ou soluções de ITSM
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 10/06/2020
ms.author: memildin
ms.openlocfilehash: 3ddc385b9d489e0c2ab4abf35a6ade011970342b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572949"
---
# <a name="stream-alerts-to-a-siem-soar-or-it-service-management-solution"></a>Transmitir alertas para uma solução SIEM, SOAR ou Gerenciamento de Serviços de TI

A central de segurança do Azure pode transmitir seus alertas de segurança para o SIEM (gerenciamento de eventos e informações de segurança) mais popular, a disparar (resposta automática de orquestração de segurança) e as soluções de ITSM (gerenciamento de serviços de ti).

Há ferramentas nativas do Azure para garantir que você possa exibir seus dados de alerta em todas as soluções mais populares em uso hoje, incluindo:

- **Azure Sentinel**
- **Splunk Enterprise and Splunk Cloud**
- **QRadar da IBM**
- **ServiceNow**
- **ArcSight**
- **Power BI**
- **Palo Alto Networks**

## <a name="stream-alerts-to-azure-sentinel"></a>Transmitir alertas para o Azure Sentinel 

A central de segurança integra-se nativamente com o Azure Sentinel, o SIEM nativo de nuvem do Azure e a solução disparar. 

[Saiba mais sobre o Azure Sentinel](../sentinel/overview.md).

### <a name="azure-sentinels-connectors-for-security-center"></a>Conectores do Azure Sentinel para a central de segurança

O Azure Sentinel inclui conectores internos para a central de segurança do Azure nos níveis de assinatura e locatário:

- [Transmitir alertas para o Azure Sentinel no nível de assinatura](../sentinel/connect-azure-security-center.md)
- [Conectar todas as assinaturas em seu locatário ao Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-security-center-auto-connect-to-sentinel/ba-p/1387539) 

### <a name="configure-ingestion-of-all-audit-logs-into-azure-sentinel"></a>Configurar a ingestão de todos os logs de auditoria no Azure Sentinel 

Outra alternativa para investigar alertas da central de segurança no Azure Sentinel é transmitir seus logs de auditoria para o Azure Sentinel:
    - [Conectar eventos de segurança do Windows](../sentinel/connect-windows-security-events.md)
    - [Coletar dados de fontes baseadas em Linux usando syslog](../sentinel/connect-syslog.md)
    - [Conectar dados do log de atividades do Azure](../sentinel/connect-azure-activity.md)

> [!TIP]
> O Sentinela do Azure é cobrado com base no volume de dados ingeridos para análise no Azure Sentinel e armazenados no espaço de trabalho Azure Monitor Log Analytics. O Azure Sentinel oferece um modelo de preços flexível e previsível. [Saiba mais na página de preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).


## <a name="stream-alerts-with-microsoft-graph-security-api"></a>Transmitir alertas com a API de segurança do Microsoft Graph

A central de segurança tem integração integrada com Microsoft Graph API de segurança. Nenhuma configuração é necessária e não há custos adicionais. 

Você pode usar essa API para transmitir alertas de todo o seu **locatário** (e dados de muitos outros produtos de segurança da Microsoft) para Siems de terceiros e outras plataformas populares:

- **Splunk Enterprise e Splunk Cloud**  -  [Usar a API de segurança do Microsoft Graph Add-On para Splunk](https://splunkbase.splunk.com/app/4564/) 
- **Power bi**  -  [Conecte-se à API de segurança do Microsoft Graph no Power bi desktop](/power-bi/connect-data/desktop-connect-graph-security)
- **ServiceNow**  -  [Siga as instruções para instalar e configurar o aplicativo de API de segurança Microsoft Graph da loja do ServiceNow](https://docs.servicenow.com/bundle/orlando-security-management/page/product/secops-integration-sir/secops-integration-ms-graph/task/ms-graph-install.html)
- **QRadar**  -  [Módulo de suporte de dispositivo da IBM para a central de segurança do Azure via API Microsoft Graph](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/com.ibm.dsm.doc/c_dsm_guide_ms_azure_security_center_overview.html) 
- **Palo Alto Networks**, **anomaliai** **, consulta,** **Inspark** e mais [Microsoft Graph API de segurança](https://www.microsoft.com/security/business/graph-security-api#office-MultiFeatureCarousel-09jr2ji)

[Saiba mais sobre Microsoft Graph API de segurança](https://www.microsoft.com/security/business/graph-security-api).


## <a name="stream-alerts-with-azure-monitor"></a>Transmitir alertas com Azure Monitor 

Para transmitir alertas em **ArcSight**, **Splunk**, **SumoLogic**, servidores syslog, **LogRhythm**, plataforma de observação de desempenho de **nuvem LOGZ.Io** e outras soluções de monitoramento. Conecte a central de segurança com o Azure monitor por meio dos hubs de eventos do Azure:

1. Habilite a [exportação contínua](continuous-export.md) para transmitir alertas da central de segurança em um hub de eventos do Azure dedicado no nível da assinatura. 
    > [!TIP]
    > Para fazer isso no nível do grupo de gerenciamento usando Azure Policy, consulte [criar configurações de automação de exportação contínua em escala](continuous-export.md?tabs=azure-policy#configure-continuous-export-at-scale-using-the-supplied-policies)

1. [Conecte o Hub de eventos do Azure à sua solução preferida usando conectores internos do Azure monitor](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration).

1. Opcionalmente, transmita os logs brutos para o Hub de eventos do Azure e conecte-se à sua solução preferida. Saiba mais em [dados de monitoramento disponíveis](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#monitoring-data-available).

> [!TIP]
> Para exibir os esquemas de eventos dos tipos de dados exportados, visite os [esquemas de eventos do hub de eventos](https://aka.ms/ASCAutomationSchemas).


## <a name="next-steps"></a>Próximas etapas

Esta página explicou como garantir que os dados de alerta da central de segurança do Azure estejam disponíveis na ferramenta SIEM, disparar ou ITSM de sua escolha. Para obter materiais relacionados, confira:

- [O que é o Azure Sentinel?](../sentinel/overview.md)
- [Validação de alerta na central de segurança do Azure](security-center-alert-validation.md) -Verifique se os alertas estão configurados corretamente
- [Exportar continuamente os dados da Central de Segurança](continuous-export.md)