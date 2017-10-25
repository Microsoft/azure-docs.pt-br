---
title: "Alternar instrução para diferentes ações no Aplicativo Lógico do Azure | Microsoft Docs"
description: "Escolha diferentes ações a serem executadas nos aplicativos lógicos com base nos valores da expressão usando uma instrução switch"
services: logic-apps
keywords: "Instrução switch"
author: derek1ee
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: 338b6a5b549d7bf81186550295608438ac4aee32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="perform-different-actions-in-logic-apps-with-a-switch-statement"></a>Executar diferentes ações nos aplicativos lógicos com uma instrução switch

Ao criar um fluxo de trabalho, em geral, você precisa executar ações diferentes com base no valor de um objeto ou uma expressão. Por exemplo, talvez você deseje que o aplicativo lógico se comporte de maneira diferente de acordo com o código de status de uma solicitação HTTP ou com uma opção selecionada em um email.

É possível usar uma instrução switch para implementar estes cenários. O aplicativo lógico pode avaliar um token ou uma expressão e escolher o caso com o mesmo valor para executar as ações especificadas. Apenas um caso deve corresponder à instrução switch.

> [!TIP]
> Assim como ocorre em todas as linguagens de programação, as instruções switch só dão suporte a operadores de igualdade. Se precisar de outros operadores relacionais, como “maior que”, use uma instrução de condição.
> Para garantir o comportamento de execução determinística, casos devem conter um valor exclusivo e estático em vez de tokens dinâmicos ou uma expressão.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura ativa do Azure. Se você não tiver uma assinatura ativa do Azure, [criar uma conta gratuita](https://azure.microsoft.com/free/), ou tente [Aplicativos Lógicos gratuitamente](https://tryappservice.azure.com/).
- [Conhecimento básico sobre aplicativos lógicos](logic-apps-what-are-logic-apps.md)

## <a name="add-a-switch-statement-to-your-workflow"></a>Adicionar uma instrução switch ao fluxo de trabalho

Para mostrar como funciona uma instrução switch, este exemplo cria um aplicativo lógico que monitora os arquivos carregados no Dropbox. Quando novos arquivos são carregados, o aplicativo lógico envia um email para um aprovador, que escolhe se deseja transferir os arquivos para o SharePoint. O aplicativo usa uma instrução switch que executa diferentes ações com base no valor selecionado pelo aprovador.

1. Crie um aplicativo lógico e selecione este gatilho: **Dropbox – Quando um arquivo é criado**.

   ![Use Dropbox - quando um arquivo é criado um gatilho](./media/logic-apps-switch-case/dropbox-trigger.jpg)

2. No gatilho, adicione esta ação: **Outlook.com – Enviar email de aprovação**

   > [!TIP]
   > O aplicativos lógicos também dão suporte a cenários de envio de email de aprovação de uma conta do Outlook do Office 365.

   - Caso não tenha uma conexão existente, você deverá criar uma.
   - Preencha os campos obrigatórios. Por exemplo, em **Para**, especifique o endereço de email para envio do email do aprovador.
   - Em **User Options**, digite `Approve, Reject`.

   ![Configurar conexão](./media/logic-apps-switch-case/send-approval-email-action.jpg)

3. Adicione uma instrução switch.

   - Selecione **+ Nova etapa** > **... Mais** > **Adicionar um caso de opção**. 
   - Agora, queremos selecionar a ação a ser executada com base na saída `SelectedOptions` da ação *Enviar email de aprovação*. 
   Você pode encontrar esse campo no seletor **Adicionar conteúdo dinâmico**.
   - Use *Caso 1* para controlar quando o aprovador seleciona `Approve`.
     - Se for aprovado, copie o arquivo original para o SharePoint Online com a ação [**SharePoint Online – Criar arquivo**](../connectors/connectors-create-api-sharepointonline.md).
     - Adicione outra ação dentro do gabinete para notificar os usuários que um novo arquivo está disponível no SharePoint.
   - Adicione outro caso para controlar quando o usuário seleciona `Reject`.
     - Se rejeitada, envie um email de notificação informando que o arquivo será rejeitado e nenhuma ação adicional é necessária a outros aprovadores.
   - `SelectedOptions` fornece apenas duas opções e, portanto, podemos deixar o caso **Padrão** vazio.

   ![Instrução switch](./media/logic-apps-switch-case/switch.jpg)

   > [!NOTE]
   > Uma instrução switch precisa de, pelo menos, um caso além do caso padrão.

4. Após a instrução switch, exclua o arquivo original carregado no Dropbox adicionando esta ação: **Dropbox – Excluir arquivo**

5. Salve seu aplicativo lógico. Teste o aplicativo carregando um arquivo no Dropbox. Em breve, você deverá receber um email de aprovação. Selecione uma opção e observe o comportamento.

   > [!TIP]
   > Confira como [monitorar os aplicativos lógicos](logic-apps-monitor-your-logic-apps.md).

## <a name="understand-the-code-behind-switch-statements"></a>Noções básicas de instruções switch code-behind

Agora que você criou um aplicativo lógico com êxito usando uma instrução switch, vamos examinar a definição de código por trás da instrução switch.

```json
"Switch": {
    "type": "Switch",
    "expression": "@body('Send_approval_email')?['SelectedOption']",
    "cases": {
        "Case 1" : {
            "case" : "Approved",
            "actions" : {}
        },
        "Case 2" : {
            "case" : "Rejected",
            "actions" : {}
        }
    },
    "default": {
        "actions": {}
    },
    "runAfter": {
        "Send_approval_email": [
            "Succeeded"
        ]
    }
}
```

* `"Switch"` é o nome da instrução switch, que pode ser renomeada para facilitar a leitura. 
* `"type": "Switch"` indica que a ação é uma instrução switch. 
* Neste exemplo, `"expression"` é a opção selecionada pelo aprovador e é avaliada em cada caso declarado posteriormente na definição. 
* `"cases"` pode conter qualquer quantidade de casos. Para cada caso, `"Case *"` é o nome padrão do caso, que pode ser renomeado para facilitar a leitura. 
`"case"` especifica o rótulo do caso, que a expressão switch usa para comparação e deve ser uma constante e um valor exclusivo. Se nenhum dos casos corresponder à expressão switch, as ações em `"default"` serão executadas.

## <a name="get-help"></a>Obter ajuda

Para fazer perguntas, responder a perguntas e saber o que os outros usuários dos Aplicativos Lógicos do Azure estão fazendo, visite o [fórum de Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

Para ajudar a melhorar os Aplicativos Lógicos do Azure e conectores, vote ou envie ideias no [site de comentários do usuário dos Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

- Saiba como [adicionar condições](logic-apps-use-logic-app-features.md)
- Saiba mais sobre [tratamento de erro e exceção](logic-apps-exception-handling.md)
- Descubra mais [funcionalidades da linguagem do fluxo de trabalho](logic-apps-author-definitions.md)