---
title: Tutorial – Transmitir logs para um hub de eventos do Azure | Microsoft Docs
description: Saiba como configurar o Diagnóstico do Azure para enviar logs do Azure Active Directory por push para um hub de eventos
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0443dcb2bf3bd58f2474c507c9f9594fb6d8a7f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89469177"
---
# <a name="tutorial-stream-azure-active-directory-logs-to-an-azure-event-hub"></a>Tutorial: Transmitir logs do Azure Active Directory para um hub de eventos do Azure

Neste tutorial, saiba como definir as configurações de diagnóstico do Azure Monitor para transmitir logs do Azure AD (Azure Active Directory) para um hub de eventos do Azure. Use esse mecanismo para integrar os logs com as ferramentas SIEM (gerenciamento de eventos e informações de segurança) de terceiros, como Splunk e QRadar.

## <a name="prerequisites"></a>Pré-requisitos 

Para usar esse recurso, você precisa de:

* Uma assinatura do Azure. Se não tiver uma assinatura do Azure, você poderá [inscrever-se em uma avaliação gratuita](https://azure.microsoft.com/free/).
* Um locatário do Azure AD.
* Um usuário que seja *administrador global* ou *administrador de segurança* do locatário do Azure AD.
* Um namespace dos Hubs de Eventos e um hub de eventos em sua assinatura do Azure. Saiba como [criar um hub de eventos](../../event-hubs/event-hubs-create.md).

## <a name="stream-logs-to-an-event-hub"></a>Transmitir logs para um hub de eventos

1. Entre no [portal do Azure](https://portal.azure.com). 

2. Selecione **Azure Active Directory** > **Monitoramento** > **Logs de auditoria**. 

3. Selecione **Exportar Configurações**.  
    
4. No painel **Configurações de Diagnóstico**, siga um destes procedimentos:
    * Para alterar as configurações existentes, selecione **Editar configuração**.
    * Para adicionar novas configurações, selecione **Adicionar configuração de diagnóstico**.  
      Você pode ter até três configurações.

      ![Exportar configurações](./media/quickstart-azure-monitor-stream-logs-to-event-hub/ExportSettings.png)

5. Marque a caixa de seleção **Transmitir para um hub de eventos** e selecione **Hub de Eventos/Configurar**.

6. Selecione a assinatura do Azure e o namespace dos Hubs de Eventos para onde você deseja encaminhar os logs.  
    A assinatura e o namespace dos Hubs de Eventos devem estar associados ao locatário do Azure AD de onde os logs estão sendo transmitidos. Você também pode especificar um hub de eventos dentro do namespace dos Hubs de Eventos para os quais os logs devem ser enviados. Se nenhum hub de eventos for especificado, um hub de eventos é criado no namespace com o nome padrão **insights-logs-audit**.

7. Selecione **OK** para sair da configuração do hub de eventos.

8. Siga um destes procedimentos, ou ambos:
    * Para enviar os logs de auditoria para o hub de eventos, marque a caixa de seleção **AuditLogs**. 
    * Para enviar os logs de entrada para o hub de eventos, marque a caixa de seleção **SignInLogs**.

9. Clique em **Salvar** para salvar a configuração.

    ![Configurações de diagnóstico](./media/quickstart-azure-monitor-stream-logs-to-event-hub/DiagnosticSettings.png)

10. Após cerca de 15 minutos, verifique se esses eventos são exibidos no hub de eventos. Para fazer isso, navegue até o hub de eventos no portal e verifique se a contagem de **mensagens de entrada** é maior que zero. 

    ![Logs de auditoria](./media/quickstart-azure-monitor-stream-logs-to-event-hub/InsightsLogsAudit.png)

## <a name="access-data-from-your-event-hub"></a>Acessar dados de seu hub de eventos

Depois que os dados são exibidos no hub de eventos, você pode acessar e ler os dados de duas maneiras:

* **Configurar uma ferramenta de SIEM com suporte**. Para ler dados do hub de eventos, a maioria das ferramentas exige a cadeia de conexão de hub de eventos e determinadas permissões para sua assinatura do Azure. Ferramentas de terceiros que têm integração com o Azure Monitor incluem, dentre outras:
    
    * **ArcSight**: para obter mais informações sobre como integrar os logs do Azure AD ao Splunk, consulte [Integrar logs do Azure Active Directory ao ArcSight usando o Azure Monitor](howto-integrate-activity-logs-with-arcsight.md).
    
    * **Splunk**: para obter mais informações sobre como integrar os logs do Azure AD ao Splunk, consulte [Integrar logs do Azure AD com o Splunk usando o Azure Monitor](./howto-integrate-activity-logs-with-splunk.md).
    
    * **IBM QRadar**: o DSM e o protocolo de Hub de Eventos do Azure estão disponíveis para download no [Suporte da IBM](https://www.ibm.com/support). Para obter mais informações sobre a integração com o Azure, vá para a [Plataforma de Inteligência de Segurança da IBM QRadar 7.3.0](https://www.ibm.com/support/knowledgecenter/SS42VS_DSM/c_dsm_guide_microsoft_azure_overview.html?cp=SS42VS_7.3.0) site.
    
    * **Sumo Logic**: para configurar o Sumo Logic a fim de consumir dados de um hub de eventos, confira [Instalar o aplicativo do Azure AD e exibir os painéis](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards). 

* **Configurar ferramentas personalizadas**. Se o SIEM atual ainda não tem suporte pelo diagnóstico do Azure Monitor, você pode configurar ferramentas personalizadas usando a API dos Hubs de Eventos. Para obter mais informações, consulte a [Introdução ao recebimento de mensagens de um hub de eventos](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).


## <a name="next-steps"></a>Próximas etapas

* [Integrar logs do Azure Active Directory ao ArcSight usando o Azure Monitor](howto-integrate-activity-logs-with-arcsight.md)
* [Integrar logs do Azure AD com o Splunk usando o Azure Monitor](./howto-integrate-activity-logs-with-splunk.md)
* [Integrar logs do Azure AD ao SumoLogic usando o Azure Monitor](howto-integrate-activity-logs-with-sumologic.md)
* [Interpretar o esquema de logs de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
