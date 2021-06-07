---
title: Transmitir logs para SumoLogic usando Azure Monitor | Microsoft Docs
description: Saiba como integrar logs de Azure Active Directory com o SumoLogic usando Azure Monitor.
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
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 51e1f45c787c319c32358e7f310108131647d60e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "91335826"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrar logs de Azure Active Directory com o SumoLogic usando Azure Monitor

Neste artigo, você aprenderá como integrar logs do Microsoft Azure Active Directory (Azure AD) ao SumoLogic usando o Monitor do Azure. Você primeiro encaminhar os logs para um hub de eventos do Azure e, em seguida, integre o hub de eventos com o SumoLogic.

## <a name="prerequisites"></a>Pré-requisitos

Para usar esse recurso, você precisa de:
* Um hub de eventos do azure contendo logs de atividades do Microsoft Azure AD. Saiba como [enviar seus logs de atividades para um hub de eventos](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 
* Um SumoLogic o logon único assinatura habilitada.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Etapas para integrar os logs do Azure AD com o SumoLogic 

1. Primeiro, [transmitir os logs do Azure AD para um hub de eventos do Azure](./tutorial-azure-monitor-stream-logs-to-event-hub.md).
2. Configurar a instância de SumoLogic [coletar logs do Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Instalar o aplicativo do Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) para usar os painéis pré-configurados que fornecem uma análise em tempo real de seu ambiente.

   ![Painel](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Próximas etapas

* [Interpretar o esquema de logs de auditoria no Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretar o esquema de logs de entrada no Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Perguntas frequentes e problemas conhecidos](concept-activity-logs-azure-monitor.md#frequently-asked-questions)