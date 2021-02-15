---
title: Trocar mensagens para cenários de integração corporativa B2B
description: Receber e enviar mensagens B2B entre parceiros comerciais em aplicativos lógicos do Azure usando o Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/02/2020
ms.openlocfilehash: e16cc8934407a5c54c84fd045c99e28116e656c9
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93310446"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Receber e confirmar mensagens AS2 de B2B usando os aplicativos lógicos do Azure e Enterprise Integration Pack

Quando você tem uma conta de integração que define parceiros e contratos comerciais, você pode criar um fluxo de trabalho de B2B (Business to Business) automatizado que troca mensagens entre parceiros comerciais usando [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) com o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Os aplicativos lógicos do Azure funcionam com conectores que dão suporte aos protocolos de padrão do setor AS2, X12, EDIFACT e RosettaNet. Você também pode combinar esses conectores com outros [conectores disponíveis nos aplicativos lógicos](../connectors/apis-list.md), por exemplo, Salesforce e Office 365 Outlook.

Este artigo mostra como criar um aplicativo lógico que recebe uma solicitação HTTP usando um gatilho de solicitação, decodifica o conteúdo da mensagem usando as ações AS2 e X12 e, em seguida, retorna uma resposta usando a ação de resposta.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você ainda não tiver uma assinatura, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Um aplicativo lógico em branco para que você possa criar o fluxo de trabalho B2B usando o gatilho de [solicitação](../connectors/connectors-native-reqres.md) seguido por estas ações:

  * [Decodificar AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Condição](../logic-apps/logic-apps-control-flow-conditional-statement.md), que envia uma [resposta](../connectors/connectors-native-reqres.md) com base em se a ação de decodificação AS2 é bem-sucedida ou falha

  * [Decodificar mensagem X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Se você não estiver familiarizado com os aplicativos lógicos, examine [o que é o aplicativo lógico do Azure?](../logic-apps/logic-apps-overview.md) e o [início rápido: Crie seu primeiro aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Uma [conta de integração](./logic-apps-enterprise-integration-create-integration-account.md) associada à sua assinatura do Azure e vinculada ao seu aplicativo lógico. O aplicativo lógico e a conta de integração devem existir no mesmo local ou região do Azure.

* Pelo menos dois [parceiros comerciais](../logic-apps/logic-apps-enterprise-integration-partners.md) que você já definiu em sua conta de integração, juntamente com os [contratos AS2 e X12](logic-apps-enterprise-integration-agreements.md) para esses parceiros.

## <a name="add-the-request-trigger"></a>Adicionar o gatilho de Solicitação

Este exemplo usa o designer de aplicativo lógico no portal do Azure, mas você pode seguir etapas semelhantes para o designer de aplicativo lógico no Visual Studio.

1. No [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico em branco no designer de aplicativo lógico.

1. Na caixa de pesquisa, insira `when a http request` e selecione **quando uma solicitação HTTP é recebida** para usar como o gatilho.

   ![Selecione o gatilho de solicitação para iniciar o fluxo de trabalho do aplicativo lógico](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Deixe a caixa **esquema JSON do corpo da solicitação** vazia porque a mensagem X12 é um arquivo simples.

   ![Deixe "esquema JSON do corpo da solicitação" vazio](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

   Esta etapa gera a **URL http post** a ser usada para enviar a solicitação que dispara o aplicativo lógico. Para copiar essa URL, selecione o ícone de cópia ao lado da URL.

   ![URL gerada para o gatilho de solicitação receber chamadas](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-the-as2-decode-action"></a>Adicionar a ação de decodificação AS2

Agora, adicione as ações B2B que você deseja usar. Este exemplo usa ações AS2 e X12.

1. No gatilho, selecione **Nova etapa**. Para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Adicionar outra etapa ao fluxo de trabalho do aplicativo lógico](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. Em **escolher uma ação** , na caixa de pesquisa, insira `as2 decode` e selecione **decodificação AS2 (v2)**.

   ![Localize e selecione "decodificação AS2 (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Para a propriedade **mensagem a ser decodificada** , insira a entrada que você deseja que a ação AS2 decodifique, que é o `body` conteúdo recebido pelo gatilho de solicitação HTTP. Você tem várias maneiras de especificar esse conteúdo como entrada, seja na lista de conteúdo dinâmico ou como uma expressão:

   * Para selecionar em uma lista que mostra as saídas de gatilho disponíveis, clique dentro da caixa de **mensagem para decodificar** . Depois que a lista de conteúdo dinâmico aparecer, em **quando uma solicitação HTTP for recebida** , selecione o valor da propriedade **Body** , por exemplo:

     ![Selecionar o valor de "corpo" do gatilho](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Para inserir uma expressão que faz referência à saída do gatilho `body` , clique dentro da caixa de **mensagem a ser decodificada** . Depois que a lista de conteúdo dinâmico for exibida, selecione **expressão**. No editor de expressão, insira a expressão aqui e selecione **OK** :

     `triggerOutputs()['body']`

     Ou, na caixa **mensagem a ser decodificada** , insira diretamente esta expressão:

     `@triggerBody()`

     A expressão é resolvida para o token de **corpo** .

     ![Saída de corpo resolvida do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Para a propriedade **cabeçalhos de mensagem** , insira os cabeçalhos necessários para a ação AS2, que são descritos pelo `headers` conteúdo recebido pelo gatilho de solicitação HTTP.

   1. Para inserir uma expressão que faz referência à saída do gatilho `headers` , selecione **alternar cabeçalhos de mensagem para o modo de texto**.

      ![Captura de tela que mostra "alternar cabeçalhos de mensagem para o modo de texto" selecionado.](./media/logic-apps-enterprise-integration-b2b/as2-decode-switch-text-mode.png)

   1. Clique dentro da caixa **cabeçalhos de mensagem** . Depois que a lista de conteúdo dinâmico for exibida, selecione **expressão**. No editor de expressão, insira a expressão aqui e selecione **OK** :

      `triggerOutputs()['Headers']`

      Na ação de decodificação AS2, a expressão agora aparece como um token:

      ![Captura de tela que mostra o @triggerOutputs token "() [' cabeçalhos ']" na caixa "cabeçalhos de mensagem".](./media/logic-apps-enterprise-integration-b2b/as2-decode-message-header-expression.png)

   1. Para obter o token de expressão a ser resolvido no token de **cabeçalhos** , alterne entre o designer e o modo de exibição de código. Após essa etapa, a ação de decodificação AS2 é semelhante a este exemplo:

      ![Saída de cabeçalhos resolvida do gatilho](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Adicionar ação de resposta para notificação de confirmação de mensagem

Para notificar o parceiro comercial de que a mensagem foi recebida, você pode retornar uma resposta que contém uma MDN (notificação de disposição de mensagens AS2) usando a ação de **resposta** . Ao adicionar essa ação imediatamente após a ação de **decodificação AS2** , se essa ação falhar, o aplicativo lógico não continuará processando.

1. Na ação de **decodificação AS2** , selecione **nova etapa**.

1. Em **escolher uma ação** , na caixa de pesquisa, selecione **interno**. Na caixa de pesquisa, insira `condition`. Na lista **Ações** , selecione **Condição**.

   ![Adicionar a ação "condição"](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Agora, a forma de condição é exibida, incluindo os caminhos para se a condição é atendida ou não.

   ![Captura de tela mostra a forma da condição com caminhos vazios.](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Agora, especifique a condição a ser avaliada. Na caixa **escolher um valor** , insira esta expressão:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Na caixa do meio, verifique se a operação de comparação está definida como `is equal to` . Na caixa do lado direito, insira o valor `Expected` . Para obter a expressão a ser resolvida como esse token, alterne entre o designer e o modo de exibição de código.

   ![Captura de tela mostra a forma da condição com uma condição adicionada.](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Agora, especifique as respostas para retornar se a ação de **decodificação AS2** for bem sucedido ou não.

   1. Para o caso em que a ação de **decodificação AS2** for realizada com sucesso, na forma **If true** , selecione **Adicionar uma ação**. Em **escolher uma ação** , na caixa de pesquisa, digite `response` e selecione **resposta**.

      ![Localizar e selecionar a ação "resposta"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Para acessar o AS2 MDN da saída da ação de **decodificação AS2** , especifique estas expressões:

      * Na propriedade **cabeçalhos** da ação de **resposta** , insira esta expressão:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Na propriedade **corpo** da ação de **resposta** , insira esta expressão:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Para obter as expressões a serem resolvidas como tokens, alterne entre o designer e a exibição de código:

      ![Expressão resolvida para acessar o AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Para o caso em que a ação de **decodificação AS2** falhar, na forma **If false** , selecione **Adicionar uma ação**. Em **escolher uma ação** , na caixa de pesquisa, digite `response` e selecione **resposta**. Configure a ação de **resposta** para retornar o status e o erro que você deseja.

1. Salve seu aplicativo lógico.

## <a name="add-decode-x12-message-action"></a>Adicionar ação decodificar mensagem X12

1. Agora, adicione a ação **decodificar mensagem X12** . Na ação **resposta** , selecione **Adicionar uma ação**.

1. Em **escolher uma ação** , na caixa de pesquisa, insira `x12 decode` e selecione **decodificar mensagem X12**.

   ![Localize e selecione a ação "decodificar mensagem de X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Se a ação X12 solicitar informações de conexão, forneça o nome para a conexão, selecione a conta de integração que você deseja usar e, em seguida, selecione **criar**.

   ![Criar conexão X12 para a conta de integração](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Agora, especifique a entrada para a ação X12. Este exemplo usa a saída da ação AS2, que é o conteúdo da mensagem, mas observe que esse conteúdo está no formato de objeto JSON e é codificado em base64. Portanto, você precisa converter este conteúdo em uma cadeia de caracteres.

   Na caixa **mensagem de arquivo simples X12 para decodificação** , insira esta expressão para converter a saída AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Para obter a expressão a ser resolvida como esse token, alterne entre o designer e o modo de exibição de código.

    ![Converter conteúdo codificado em base64 em uma cadeia de caracteres](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Salve seu aplicativo lógico.

   Se você precisar de etapas adicionais para esse aplicativo lógico, por exemplo, para decodificar o conteúdo da mensagem e gerar esse conteúdo no formato de objeto JSON, continue criando seu aplicativo lógico.

Agora você concluiu a configuração do seu aplicativo lógico B2B. Em um aplicativo do mundo real, talvez você queira armazenar os dados decodificados do X12 em um aplicativo de linha de negócios (LOB) ou armazenamento de dados. Por exemplo, consulte estes artigos:

* [Conectar aos sistemas SAP a partir do Aplicativos Lógicos do Azure](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitore, crie e gerencie arquivos SFTP usando SSH e os Aplicativos Lógicos do Azure](../connectors/connectors-sftp-ssh.md)

Para conectar seus próprios aplicativos LOB e usar essas APIs em seu aplicativo lógico, você pode adicionar mais ações ou [gravar APIs personalizadas](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Próximas etapas

* [Receber e responder a chamadas HTTPS de entrada](../connectors/connectors-native-reqres.md)
* [Trocar mensagens AS2 para a integração corporativa B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Trocar mensagens X12 para integração corporativa B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Saiba mais sobre o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
