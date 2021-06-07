---
title: O que é o monitoramento do Azure Active Directory? | Microsoft Docs
description: Fornece uma visão geral do monitoramento do Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763e628183e5f6ad7b7bdbb8ee7ce6db572f44ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100577781"
---
# <a name="what-is-azure-active-directory-monitoring"></a>O que é o monitoramento do Azure Active Directory?

Com o monitoramento do Azure Active Directory (Azure AD), agora você pode rotear sua atividade do Azure AD logs para diferentes pontos de extremidade. Você pode mantê-lo para uso de longo prazo ou integrá-lo com ferramentas de gerenciamento de eventos e informações de segurança (SIEM) de terceiros para obter informações sobre seu ambiente.

No momento, você pode rotear os logs para:

- Uma conta de armazenamento do Azure.
- Um hub de eventos do Azure, para que você possa integrar suas instâncias de Splunk e Sumologic.
- Um espaço de trabalho do Log Analytics do Azure, no qual você pode analisar os dados, criar o painel e alertas para eventos específicos

**Função de pré-requisito**: Administrador global

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenciamento e pré-requisitos para relatórios e monitoramento do Azure AD

Você precisará de uma licença Premium do Azure AD para acessar os logs de entrada do Azure AD.

Para obter informações detalhadas sobre os recursos e o licenciamento, confira o [Guia de preços do Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Para implantar o monitoramento e o relatório do Azure AD, você precisará de um usuário que seja um Administrador global ou um administrador de segurança do locatário do Azure AD.

Dependendo do destino final dos dados de log, você precisará de um dos seguintes:

* Uma conta de armazenamento do Azure para a qual você tem permissões ListKeys. Recomendamos que você use uma conta de armazenamento geral e não uma conta do Armazenamento de blobs. Para saber mais sobre preços do armazenamento, confira a [Calculadora de preços do armazenamento do Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Um namespace dos Hubs de Eventos do Azure para integração a soluções SIEM de terceiros.

* Um espaço de trabalho do Log Analytics do Azure para enviar logs aos logs do Azure Monitor.

## <a name="diagnostic-settings-configuration"></a>Definição das configurações de diagnóstico

Para definir as configurações de monitoramento de logs de atividades do Azure AD, entre primeiro no [portal do Azure](https://portal.azure.com), em seguida, selecione **Azure Active Directory**. A partir daqui, você pode acessar a página de definição de configurações de diagnóstico de duas maneiras:

* Selecione **Configurações de diagnóstico**, na seção **Monitoramento**.

    ![Configurações de diagnóstico](./media/overview-monitoring/diagnostic-settings.png)
    
* Selecione **Logs de auditoria** ou **Entradas**, em seguida, selecione **Exportar configurações**. 

    ![Exportar configurações](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Rotear logs para conta de armazenamento

Ao rotear os logs para uma conta de armazenamento do Azure, você pode mantê-los por um tempo maior que o período de retenção padrão descrito em nossas [políticas de retenção](reference-reports-data-retention.md). Saiba como [encaminhar os dados para sua conta de armazenamento](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Transmitir por streaming logs para um hub de eventos

O roteamento de logs para um hub de eventos do Azure permite que você faça a integração com ferramentas SIEM de terceiros como Sumologic e Splunk. Essa integração permite combinar dados de log de atividades do Azure AD com outros dados gerenciados pelo seu SIEM para fornecer em informações mais avançadas sobre seu ambiente. Saiba como [enviar seus logs para um hub de eventos](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Enviar logs para os logs do Azure Monitor

Os [logs do Azure Monitor](../../azure-monitor/logs/log-query-overview.md) são uma solução que consolida dados de monitoramento de diferentes fontes e fornece um mecanismo de análise e linguagem de consulta que fornece insights sobre a operação de seus aplicativos e seus recursos. Enviando os logs de atividades do Azure AD para os logs do Azure Monitor, você pode recuperar e monitorar os dados coletados rapidamente, além de fornecer alertas sobre eles. Saiba como [enviar dados para os logs do Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md).

Você também pode instalar as exibições criadas previamente para logs de atividades do Azure AD para monitorar cenários comuns que envolvem entradas e eventos de auditoria. Saiba como [instalar e usar as exibições do Log Analytics para logs de atividades do Azure AD](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Próximas etapas

* [Logs de atividades no Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Transmitir por streaming logs para um hub de eventos](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Enviar logs para os logs do Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)
