---
title: Integrar o Splunk usando o Azure Monitor | Microsoft Docs
description: Saiba como integrar logs de Azure Active Directory com o Splunk usando Azure Monitor.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb6a597d4fd58646f56e271cb6027fb46db1e26
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634219"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Como integrar logs de Azure Active Directory com o Splunk usando Azure Monitor

Neste artigo, você aprenderá a integrar os logs do Azure Active Directory (Microsoft Azure AD) ao Splunk usando o Azure Monitor. Primeiro encaminhe os logs para um hub de eventos do Azure e, em seguida, integre o hub de eventos Splunk.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:

- Um hub de eventos do azure contendo logs de atividades do Microsoft Azure AD. Saiba como [enviar seus logs de atividades para um hub de eventos](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  O [complemento Splunk para serviços de Microsoft Cloud](https://splunkbase.splunk.com/app/3110/#/details). 

## <a name="integrate-azure-active-directory-logs"></a>Integrar logs de Azure Active Directory 

1. Abra sua instância do Splunk e clique em **Resumo dos dados**.

    ![Botão "Resumo de dados"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Selecione a guia **Sourcetypes** e, em seguida, selecione **amal: aadal:audit**

    ![Na guia Sourcetypes de resumo de dados](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Você verá que os logs de atividade do Azure Active Directory são mostrados na figura a seguir:

    ![Logs de atividades](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Se você não puder instalar um complemento em sua instância do Splunk (por exemplo, se estiver usando um proxy ou em execução no Splunk Cloud), poderá encaminhar esses eventos para o Coletor de Eventos HTTP. Para fazer isso, use esta [função do Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), que é disparada por novas mensagens no hub de eventos. 
>

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)