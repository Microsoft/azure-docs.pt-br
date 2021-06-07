---
title: Conectar-se ao SendGrid pelos Aplicativos Lógicos do Azure
description: Automatize tarefas e fluxos de trabalho que enviam emails e gerenciam listas de endereçamento no SendGrid usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87290442"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Envie emails e gerencie listas de endereçamento no SendGrid usando os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do SendGrid, você pode criar fluxos de trabalho e tarefas automatizadas que enviam emails e gerenciam listas de destinatários, por exemplo:

* Enviar email.
* Adicionar destinatários a listas.
* Obter, adicionar e gerenciar a supressão global.

É possível usar ações do SendGrid em seus aplicativos lógicos para executar essas tarefas. Também é possível fazer com que outras ações usem a saída das ações do SendGrid. 

Esse conector fornece apenas ações, portanto, para iniciar seu aplicativo lógico, use um gatilho separado, como um gatilho de **Recorrência**. Por exemplo, se adiciona destinatários a suas listas regularmente, você poderá enviar emails sobre destinatários e listas usando o conector do Office 365 Outlook ou do Outlook.com.
Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Uma [conta do SendGrid](https://www.sendgrid.com/) e uma [chave de API do SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Sua chave de API autoriza o aplicativo lógico a criar uma conexão e a acessar sua conta do SendGrid.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta do SendGrid. Para usar uma ação do SendGrid, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **Recorrência**.

## <a name="connect-to-sendgrid"></a>Conectar-se ao SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Na última etapa em que você deseja adicionar uma ação, escolha **Nova etapa**. 

     -ou-

   * Entre as etapas em que você deseja adicionar uma ação, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "sendgrid" como filtro. Na lista de ações, selecione a ação desejada.

1. Forneça um nome para a conexão. 

1. Insira sua chave de API do SendGrid e, em seguida, escolha **Criar**.

1. Forneça os detalhes necessários para a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/sendgrid/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite a [página de perguntas e respostas da Microsoft sobre os Aplicativos Lógicos do Azure](/answers/topics/azure-logic-apps.html).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
