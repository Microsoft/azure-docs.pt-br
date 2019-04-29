---
title: Conectar-se ao Outlook.com – Aplicativos Lógicos do Azure | Microsoft Docs
description: Gerenciar email, contatos e calendários com as APIs REST do Outlook.com e os Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: fd6836451a73551487b8f97903594154a2efc894
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105795"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Gerenciar email, contatos e calendários no Outlook.com com os Aplicativos Lógicos do Azure

Este artigo mostra como você pode criar e gerenciar sua conta do Outlook.com dentro de um aplicativo lógico com o conector do Box. Desse modo, você pode criar aplicativos lógicos que automatizam tarefas e fluxos de trabalho para a conta do Outlook.com, por exemplo:

* Enviar email. 
* Agendar reuniões.
* Adicionar contatos. 

Se ainda não estiver familiarizado com o Aplicativo Lógico, veja [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Outlook.com](https://outlook.live.com/owa/)

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

* O aplicativo lógico no qual você deseja acessar a conta do Outlook.com. Para iniciar o aplicativo lógico com um gatilho do Outlook, é necessário um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-outlookcom"></a>Conectar ao Outlook.com

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de Swagger do conector, confira a [página de referência do conector](/connectors/outlook/). 

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)