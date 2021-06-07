---
title: Conectar-se ao Slack a partir de Aplicativos Lógicos do Azure
description: Automatize fluxos de trabalho e tarefas que monitoram arquivos e gerenciam canais, grupos e mensagens em sua conta do Slack usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 75f4cea0e5cd2b0bf42c28cbd81e199115834e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283955"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitore e gerencie o Slack com os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Slack, você pode criar fluxos de trabalho e tarefas automatizadas que monitoram arquivos do Slack e gerenciam canais, mensagens e grupos do Slack, entre outros, por exemplo:

* Monitorar quando novos arquivos são criados.
* Criar, listar e unir canais 
* Postar mensagens.
* Criar grupos e configurar “Não perturbar".

Você pode usar gatilhos que obtêm respostas de sua conta do Slack e disponibilizam a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Slack. Você também pode fazer com que outras ações usem a saída de ações do Slack. Por exemplo, quando um novo arquivo é criado, você pode enviar um email com o conector do Office 365 Outlook. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Suas credenciais de usuário e conta do [Slack](https://slack.com/)

  Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta do Slack.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico em que você deseja acessar sua conta do Slack. Para iniciar com um gatilho do Slack, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Slack, inicie seu aplicativo lógico com um gatilho, que pode ser gatilho do Slack ou outro gatilho, como o de **Recorrência**.

## <a name="connect-to-slack"></a>Conectar-se com o Slack

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "slack" como o filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que deseja adicionar uma ação, escolha **Nova etapa**. 
   Na caixa de pesquisa, insira "slack" como filtro. 
   Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se for solicitado que você entre no Slack, entre em seu workspace do Slack. 

   ![Entre no workspace do Slack](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autorize o acesso para seu aplicativo lógico.

   ![Autorize o acesso ao Slack](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Forneça os detalhes necessários para o gatilho ou ação selecionada. Para continuar criando o fluxo de trabalho de seu aplicativo lógico, adicione mais ações.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/slack/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite a [página de perguntas e respostas da Microsoft sobre os Aplicativos Lógicos do Azure](/answers/topics/azure-logic-apps.html).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)

