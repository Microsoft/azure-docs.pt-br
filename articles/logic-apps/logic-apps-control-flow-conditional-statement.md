---
title: Adicionar instruções condicionais a fluxos de trabalho – Aplicativos Lógicos do Azure | Microsoft Docs
description: Como criar condições que controlam ações em fluxos de trabalho nos Aplicativos Lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 10/09/2018
ms.openlocfilehash: 9ee484971e217b0ca4dd7ad855e9e6dc3313e5d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684724"
---
# <a name="create-conditional-statements-that-control-workflow-actions-in-azure-logic-apps"></a>Crie instruções condicionais que controlam ações de fluxo de trabalho nos Aplicativos Lógicos do Azure

Para executar ações específicas em seu aplicativo lógico somente depois de passar uma condição especificada, adicione uma *instrução condicional*. Essa estrutura de controle compara dados no fluxo de trabalho com valores ou campos específicos. Dessa forma, é possível especificar diferentes ações para executar com base em se os dados atendem a condição ou não. É possível aninhar condições dentro de outras.

Por exemplo, suponha que você tenha um aplicativo lógico que envia muitos emails quando novos itens aparecem no feed RSS de um site. Então, é possível adicionar uma declaração condicional para enviar email somente quando o novo item incluir uma cadeia de caracteres específica. 

> [!TIP]
> Para executar diferentes etapas baseadas em diferentes valores específicos, use uma [*instrução de comutador*](../logic-apps/logic-apps-control-flow-switch-statement.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Para seguir o exemplo neste artigo, [crie esse aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou Office 365 Outlook.

## <a name="add-condition"></a>Adicionar condição

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. Adicione uma condição no local que deseja. 

   Para adicionar uma condição entre etapas, mova o ponteiro sobre a seta onde você quer adicionar a condição. Escolha o **sinal de adição** (**+**) que aparece e, em seguida, escolha **Adicionar uma ação**. Por exemplo: 

   ![Adicionar ação entre etapas](./media/logic-apps-control-flow-conditional-statement/add-action.png)

   Quando você quiser adicionar uma condição no final do fluxo de trabalho, na parte inferior do aplicativo lógico, escolha **Nova etapa** > **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "condição" como o filtro. Selecione esta ação: **Condição - controle**

   ![Adicionar condição](./media/logic-apps-control-flow-conditional-statement/add-condition.png)

1. Na caixa **Condição**, compile a condição. 

   1. Na caixa esquerda, especifique os dados ou o campo que deseja comparar.

      Quando você clica dentro da caixa à esquerda, a lista de conteúdo dinâmico é exibida para que você possa selecionar saídas de etapas anteriores em seu aplicativo lógico. 
      Para este exemplo, selecione o resumo do feed RSS.

      ![Compilar a condição](./media/logic-apps-control-flow-conditional-statement/edit-condition.png)

   1. Na caixa intermediária, selecione a operação a ser realizada. 
   Neste exemplo, selecione “**contém**”. 

   1. Na caixa direita, especifique um valor ou campo como o critério. 
   Neste exemplo, especifique essa cadeia de caracteres: **Microsoft**

   Aqui está a condição completa:

   ![Condição completa](./media/logic-apps-control-flow-conditional-statement/edit-condition-2.png)

   Para adicionar outra linha à condição, escolha **Adicionar** > **Adicionar linha**. 
   Para adicionar um grupo com subcondições, escolha **Adicionar** > **Adicionar grupo**. 
   Para agrupar linhas existentes, marque as caixas de seleção dessas linhas, escolha o botão de reticências (...) para qualquer linha e escolha **Fazer grupo**.

1. Em **Se verdadeiro** e **Se falso**, adicione as etapas a serem executadas com base em se a condição é atendida. Por exemplo: 

   ![Condição com caminhos "Se verdadeiro" e "Se falso"](./media/logic-apps-control-flow-conditional-statement/condition-yes-no-path.png)

   > [!TIP]
   > Você pode arrastar ações existentes para os caminhos **Se verdadeiro** e **Se falso**.

1. Salve seu aplicativo lógico.

Esse aplicativo lógico agora enviará emails apenas quando os novos itens no RSS feed atenderem à condição.

## <a name="json-definition"></a>Definição JSON

Aqui está a definição de código de alto nível por trás de uma instrução condicional:

``` json
"actions": {
  "Condition": {
    "type": "If",
    "actions": {
      "Send_an_email": {
        "inputs": {},
        "runAfter": {}
    },
    "expression": {
      "and": [ 
        { 
          "contains": [ 
            "@triggerBody()?['summary']", 
            "Microsoft"
          ]
        } 
      ]
    },
    "runAfter": {}
  }
},
```

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
