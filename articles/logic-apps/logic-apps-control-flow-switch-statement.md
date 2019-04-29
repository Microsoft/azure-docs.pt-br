---
title: Adicionar instruções switch a fluxos de trabalho – Aplicativos Lógicos do Azure | Microsoft Docs
description: Como criar instruções switch que controlam as ações de fluxo de trabalho com base em valores específicos nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 2a3f8ee5cba3110d392555fad78c1cb2513b5d4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60683062"
---
# <a name="create-switch-statements-that-run-workflow-actions-based-on-specific-values-in-azure-logic-apps"></a>Criar instruções switch que executam ações de fluxo de trabalho com base em valores específicos nos Aplicativos Lógicos do Azure

Para executar ações específicas com base nos valores de objetos, expressões ou tokens, adicione uma instrução *switch*. Essa estrutura avalia o objeto, a expressão ou o token, escolhe o caso que coincide com o resultado e executa ações específicas apenas para esse caso. Apenas um caso deve corresponder ao resultado quando a instrução switch for executada.

Por exemplo, suponhamos que você deseje um aplicativo lógico que execute etapas diferentes com base em uma opção selecionada no e-mail. Neste exemplo, o aplicativo lógico verifica o feed de RSS de um site para o novo conteúdo. Quando um novo item aparece no feed de RSS, o aplicativo lógico envia e-mail a um aprovador. Baseado em se o aprovador seleciona "Aprovar" ou "Rejeitar", o aplicativo lógico segue etapas diferentes.

> [!TIP]
> Assim como ocorre em todas as linguagens de programação, as instruções switch só dão suporte a operadores de igualdade. Se precisar de outros operadores relacionais, como “maior que”, use uma [instrução condicional](../logic-apps/logic-apps-control-flow-conditional-statement.md).
> Para garantir o comportamento de execução determinística, casos devem conter um valor exclusivo e estático em vez de expressões ou tokens dinâmicos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Para seguir o exemplo neste artigo, [crie esse aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou Office 365 Outlook.

  1. Quando você adiciona a ação para enviar email, localize e selecione esta ação em vez disso: **Enviar um email de aprovação**

     ![Selecione "Enviar um e-mail de aprovação"](./media/logic-apps-control-flow-switch-statement/send-approval-email-action.png)

  1. Forneça os campos obrigatórios, como o endereço de e-mail da pessoa que obtém o e-mail de aprovação. 
  Em **Opções do usuário**, insira “Aprovar, Rejeitar”.

     ![Insira os detalhes de e-mail](./media/logic-apps-control-flow-switch-statement/send-approval-email-details.png)

## <a name="add-switch-statement"></a>Adicionar instrução switch

1. Para este exemplo, adicione uma instrução switch no final do fluxo de trabalho de amostra. Após a última etapa, escolha **Nova etapa**.

   Quando você deseja adicionar uma instrução de comutação entre as etapas, mova o ponteiro sobre a seta onde você deseja adicionar a instrução de comutação. Escolha o **sinal de adição** (**+**) que aparece e, em seguida, escolha **Adicionar uma ação**.

1. Na caixa de pesquisa, digite “switch” como filtro. Selecione esta ação: **Comutador - controle**

   ![Adicionar switch](./media/logic-apps-control-flow-switch-statement/add-switch-statement.png)

   Uma instrução de comutação aparece com um caso e um caso padrão. 
   Por padrão, uma instrução switch precisa de, pelo menos, um caso além do caso padrão. 

   ![Instrução switch padrão vazia](./media/logic-apps-control-flow-switch-statement/empty-switch.png)

1. Clique dentro da caixa **Em** para que seja exibida a lista de conteúdo dinâmica. Desta lista, selecione o campo **SelectedOption** cuja saída determina a ação a ser executada. 

   ![Selecione “SelectedOption”](./media/logic-apps-control-flow-switch-statement/select-selected-option.png)

1. Para lidar com casos onde o aprovador selecione `Approve` ou `Reject`, adicione outro caso entre **Caso** e **Padrão**. 

   ![Adicionar outro caso](./media/logic-apps-control-flow-switch-statement/switch-plus.png)

1. Adicione essas ações para os casos correspondentes:

   | Nº do caso | **SelectedOption** | Ação |
   |--------|--------------------|--------|
   | Caso 1 | **Aprovar** | Adicionar a ação do Outlook **Enviar um e-mail** para enviar detalhes sobre o item de RSS somente quando o aprovador selecionado **Aprovar**. |
   | Caso 2 | **Rejeitar** | Adicione a ação do Outlook **Enviar um e-mail** para notificar outros aprovadores que o item de RSS foi rejeitado. |
   | Padrão | Nenhum | Nenhuma ação é necessária. Neste exemplo, o caso **Padrão** está vazio porque **SelectedOption** tem apenas duas opções. |
   |||

   ![Instrução switch finalizada](./media/logic-apps-control-flow-switch-statement/finished-switch.png)

1. Salve seu aplicativo lógico. 

   Para testar manualmente este exemplo, escolha **Executar** até que o aplicativo lógico encontre um novo item de RSS e envie um e-mail de aprovação. 
   Selecione **Aprovar** para observar os resultados.

## <a name="json-definition"></a>Definição JSON

Agora que você criou um aplicativo lógico usando uma instrução switch, vamos examinar a definição de código de alto nível por trás da instrução switch.

``` json
"Switch": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {}
         },
         "case" : "Approve"
      },
      "Case_2": {
         "actions": {
           "Send_an_email_2": {}
         },
         "case": "Reject"
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

| Rótulo | DESCRIÇÃO |
|-------|-------------|
| `"Switch"`         | O nome da instrução de comutação, que pode ser renomeada para facilitar a leitura |
| `"type": "Switch"` | Especifica que a ação é uma instrução de comutação |
| `"expression"`     | Neste exemplo, especifica a opção selecionada pelo aprovador que é avaliada em cada caso declarado posteriormente na definição |
| `"cases"` | Define qualquer quantidade de casos. Para cada caso, `"Case_*"` é o nome padrão do caso, que pode ser renomeado para facilitar a leitura |
| `"case"` | Especifica o valor do caso, que deve ser um valor constante e exclusivo que a instrução do comutador usa para comparação. Se nenhuma ocorrência corresponder ao resultado da expressão de comutação, as ações na seção `"default"` são executadas. | 
| | | 

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar e repetir (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
