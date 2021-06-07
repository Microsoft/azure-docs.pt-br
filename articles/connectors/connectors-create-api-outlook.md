---
title: Conectar ao Outlook.com
description: Automatizar tarefas e fluxos de trabalho que gerenciam emails, calendários e contatos no Outlook.com usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "75707179"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Gerenciar emails, calendários e contatos no Outlook.com usando o aplicativo lógico do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector do Outlook.com](/connectors/outlook/), você pode criar tarefas automatizadas e fluxos de trabalho que gerenciam sua conta do @outlook.com ou @hotmail.com criando aplicativos lógicos. Por exemplo, você automatiza essas tarefas:

* Obter, enviar e responder a emails.
* Agende reuniões no seu calendário.
* Adicionar e editar contatos.

Você pode usar qualquer gatilho para iniciar o fluxo de trabalho, por exemplo, quando um novo email chega, quando um item de calendário é atualizado ou quando um evento ocorre em um serviço de diferença. Você pode usar ações que respondem ao evento de gatilho, por exemplo, enviar um email ou criar um novo evento de calendário.

> [!NOTE]
> Para automatizar tarefas para uma conta corporativa da Microsoft @fabrikam.onmicrosoft.com , como, use o [conector do Outlook do Office 365](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Outlook.com](https://outlook.live.com/owa/)

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* O aplicativo lógico no qual você deseja acessar a conta do Outlook.com. Para iniciar o fluxo de trabalho com um gatilho Outlook.com, você precisa ter um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para adicionar uma ação Outlook.com ao seu fluxo de trabalho, seu aplicativo lógico precisa já ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um gatilho

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho em seu aplicativo lógico. Esse aplicativo lógico de exemplo usa um gatilho de "sondagem" que verifica se há novos emails em sua conta de email, com base no intervalo e na frequência especificados.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico em branco no designer de aplicativo lógico.

1. Na caixa de pesquisa, insira "outlook.com" como filtro. Para este exemplo, selecione **quando um novo email chegar**.

1. Se você for solicitado a entrar, forneça suas credenciais do Outlook.com para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se a conexão já existir, forneça as informações para as propriedades do gatilho:

1. No gatilho, defina os valores de **frequência** e **intervalo** .

   Por exemplo, se você quiser que o gatilho seja sondado a cada 15 minutos, defina a **frequência** como **minuto** e defina o **intervalo** como **15**.

1. Na barra de ferramentas do designer, selecione **salvar**, que salva o aplicativo lógico.

Para responder ao gatilho, adicione outra ação. Por exemplo, você pode adicionar a ação **Enviar mensagem** twilio, que envia um texto quando um email chega.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é executada pelo fluxo de trabalho em seu aplicativo lógico. Este aplicativo lógico de exemplo envia um email de sua conta do Outlook.com. Você pode usar a saída de outro gatilho para popular a ação. Por exemplo, suponha que seu aplicativo lógico use o gatilho SalesForce **quando um objeto for criado** . Você pode adicionar a ação **enviar um email** do Outlook.com e usar as saídas do gatilho do Salesforce no email.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Para adicionar uma ação como a última etapa no fluxo de trabalho, selecione **nova etapa**. 

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o sinal mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, digite "outlook.com" como filtro. Para este exemplo, selecione **enviar um email**. 

1. Se você for solicitado a entrar, forneça suas credenciais do Outlook.com para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se a conexão já existir, forneça as informações para as propriedades da ação.

1. Na barra de ferramentas do designer, selecione **salvar**, que salva o aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de Swagger do conector, confira a [página de referência do conector](/connectors/outlook/). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
