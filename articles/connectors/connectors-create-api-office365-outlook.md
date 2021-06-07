---
title: Integre-se ao Office 365 Outlook
description: Automatizar tarefas e fluxos de trabalho que gerenciam email, contatos e calendários no Office 365 Outlook usando aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: article
ms.date: 11/13/2020
tags: connectors
ms.openlocfilehash: 87a9c8a38ede7008d658068329c8c72e7e3c4117
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095456"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gerenciar emails, contatos e calendários no Outlook do Office 365 usando os Aplicativos Lógicos do Azure

Com os [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e o [conector do Outlook para Office 365](/connectors/office365connector/), você pode criar tarefas automatizadas e fluxos de trabalho que gerenciam sua conta corporativa ou de estudante criando aplicativos lógicos. Por exemplo, você pode automatizar estas tarefas:

* Obter, enviar e responder a emails.
* Agende reuniões no seu calendário.
* Adicionar e editar contatos.

Você pode usar qualquer gatilho para iniciar o fluxo de trabalho, por exemplo, quando um novo email chegar, quando um item de calendário for atualizado ou quando um evento ocorrer em um serviço de diferença, como o Salesforce. Você pode usar ações que respondem ao evento de gatilho, por exemplo, enviar um email ou criar um novo evento de calendário.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do Outlook na qual você entra com uma [conta corporativa ou de estudante](https://www.office.com/). Se você tiver uma @outlook.com @hotmail.com conta do ou do, use o [conector do Outlook.com](../connectors/connectors-create-api-outlook.md) em vez disso. Para se conectar ao Outlook com uma conta de usuário diferente, como uma conta de serviço, consulte [conectar-se usando outras contas](#connect-using-other-accounts).

* Uma conta e uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* O aplicativo lógico no qual você deseja acessar sua conta do Outlook. Para iniciar o fluxo de trabalho com um gatilho do Outlook do Office 365, você precisa ter um [aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para adicionar uma ação do Outlook do Office 365 ao seu fluxo de trabalho, seu aplicativo lógico precisa já ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um gatilho

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho em seu aplicativo lógico. Esse aplicativo lógico de exemplo usa um gatilho de "sondagem" que verifica qualquer evento de calendário atualizado em sua conta de email, com base no intervalo e na frequência especificados.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico em branco no designer de aplicativo lógico.

1. Na caixa de pesquisa, insira `office 365 outlook` como o filtro. Este exemplo seleciona **quando um evento futuro é iniciado em breve**.
   
   ![Selecione o gatilho para iniciar seu aplicativo lógico](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se você não tiver uma conexão ativa com sua conta do Outlook, você será solicitado a entrar e criar essa conexão. Para se conectar ao Outlook com uma conta de usuário diferente, como uma conta de serviço, consulte [conectar-se usando outras contas](#connect-using-other-accounts). Caso contrário, forneça as informações para as propriedades do gatilho.

   > [!NOTE]
   > Sua conexão não expirará até ser revogada, mesmo se você alterar suas credenciais de entrada. Para obter mais informações, consulte [Vida útil do token configurável no Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Este exemplo seleciona o calendário que o gatilho verifica, por exemplo:

   ![Configurar as propriedades do gatilho](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. No gatilho, defina os valores de **frequência** e **intervalo** . Para adicionar outras propriedades de gatilho disponíveis, como **fuso horário**, selecione essas propriedades na lista **Adicionar novo parâmetro** .

   Por exemplo, se você quiser que o gatilho Verifique o calendário a cada 15 minutos, defina a **frequência** como **minuto** e defina o **intervalo** como `15` . 

   ![Definir a frequência e o intervalo para o gatilho](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Selecione **Salvar** na barra de ferramentas do designer.

Agora, adicione uma ação que é executada Depois que o gatilho é acionado. Por exemplo, você pode adicionar a ação **Enviar mensagem** twilio, que envia um texto quando um evento de calendário é iniciado em 15 minutos.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é executada pelo fluxo de trabalho em seu aplicativo lógico. Este aplicativo lógico de exemplo cria um novo contato no Office 365 Outlook. Você pode usar a saída de outro gatilho ou ação para criar o contato. Por exemplo, suponha que seu aplicativo lógico use o gatilho do Dynamics 365, **quando um registro é criado**. Você pode adicionar a ação **criar contato** do Office 365 Outlook e usar as saídas do gatilho do Salesforce para criar o novo contato.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Para adicionar uma ação como a última etapa no fluxo de trabalho, selecione **nova etapa**. 

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o sinal mais ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira `office 365 outlook` como o filtro. Este exemplo seleciona **criar contato**.

   ![Selecione a ação a ser executada em seu aplicativo lógico](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se você não tiver uma conexão ativa com sua conta do Outlook, você será solicitado a entrar e criar essa conexão. Para se conectar ao Outlook com uma conta de usuário diferente, como uma conta de serviço, consulte [conectar-se usando outras contas](#connect-using-other-accounts). Caso contrário, forneça as informações para as propriedades da ação.

   > [!NOTE]
   > Sua conexão não expirará até ser revogada, mesmo se você alterar suas credenciais de entrada. Para obter mais informações, consulte [Vida útil do token configurável no Azure Active Directory](../active-directory/develop/active-directory-configurable-token-lifetimes.md).

   Este exemplo seleciona a pasta contatos na qual a ação cria o novo contato, por exemplo:

   ![Configurar as propriedades da ação](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Para adicionar outras propriedades de ação disponíveis, selecione essas propriedades na lista **Adicionar novo parâmetro** .

1. Selecione **Salvar** na barra de ferramentas do designer.

<a name="connect-using-other-accounts"></a>

## <a name="connect-using-other-accounts"></a>Conectar-se usando outras contas

Se você tentar se conectar ao Outlook usando uma conta diferente daquela atualmente conectada ao Azure, você poderá obter erros de [SSO (logon único)](../active-directory/manage-apps/what-is-single-sign-on.md) . Esse problema ocorre quando você entra no portal do Azure com uma conta, mas usa uma conta diferente para criar a conexão. O designer espera que você use a conta que está conectada ao portal do Azure. Para resolver esse problema, você tem estas opções:

* Configure a outra conta com a função **colaborador** no grupo de recursos do aplicativo lógico.

  1. No menu do grupo de recursos do aplicativo lógico, selecione **controle de acesso (iam)**. Configure a outra conta com a função **colaborador** . 
  
     Para obter mais informações, confira [Atribuir funções do Azure usando o portal do Azure](../role-based-access-control/role-assignments-portal.md).

  1. Depois de configurar essa função, entre no portal do Azure com a conta que agora tem permissões de colaborador. Agora você pode usar essa conta para criar a conexão com o Outlook.

* Configure a outra conta para que sua conta corporativa ou de estudante tenha permissões "enviar como".

   Se você tiver permissões de administrador, na caixa de correio da conta de serviço, configure sua conta corporativa ou de estudante com o **Enviar como** ou **enviar em nome de** permissões. Para obter mais informações, consulte [conceder permissões de caixa de correio para outra ajuda de administrador do usuário](/microsoft-365/admin/add-users/give-mailbox-permissions-to-another-user). Em seguida, você pode criar a conexão usando sua conta corporativa ou de estudante. Agora, em gatilhos ou ações em que você pode especificar o remetente, você pode usar o endereço de email da conta de serviço.

   Por exemplo, a ação **enviar um email** tem um parâmetro opcional, **de (enviar como)**, que você pode adicionar à ação e usar o endereço de email da sua conta de serviço como o remetente. Para adicionar esse parâmetro, siga estas etapas:

   1. Na ação **enviar um email** , abra a lista **Adicionar um parâmetro** e selecione o parâmetro **from (enviar como)** .

   1. Depois que o parâmetro aparecer na ação, insira o endereço de email da conta de serviço.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre esse conector, como gatilhos, ações e limites, conforme descrito pelo arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/office365/). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
