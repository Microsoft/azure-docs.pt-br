---
title: Criar ou unir ramificações paralelas para ações em fluxos de trabalho
description: Saiba como criar ou mesclar ramificações paralelas em execução para ações de fluxo de trabalho independentes nos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/10/2018
ms.openlocfilehash: 3514ce966e1de9af1741de6b966964aca2599610
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269226"
---
# <a name="create-or-join-parallel-branches-for-workflow-actions-in-azure-logic-apps"></a>Criar ou unir branches paralelos para ações de fluxo de trabalho nos Aplicativos Lógicos do Azure

Por padrão, suas ações nos fluxos de trabalho de aplicativo lógico são executadas sequencialmente. Para executar ações independentes ao mesmo tempo, você pode criar [Branches paralelos](#parallel-branches) e, em seguida, [unir esses branches](#join-branches) posteriormente no seu fluxo. 

> [!TIP] 
> Se você tiver um gatilho que recebe uma matriz e deseja executar um fluxo de trabalho para cada item de matriz, é possível fazer *debatch* dessa matriz com o [**SplitOn** da propriedade de gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="parallel-branches"></a>

## <a name="add-parallel-branch"></a>Adicionar branch paralelo

Para executar etapas independentes ao mesmo tempo, você pode adicionar branches paralelos próximos a uma etapa existente. 

![Executar as etapas em paralelo](media/logic-apps-control-flow-branches/parallel.png)

Seu aplicativo lógico espera que todos os branches estejam concluídos antes de continuar o fluxo de trabalho. Os branches em paralelo são executados somente quando seus valores de propriedade `runAfter` corresponderem ao status da etapa pai concluída. Por exemplo, `branchAction1` e `branchAction2` estão definidos para serem executados apenas quando `parentAction` tiver concluído com o status `Succeeded`.

> [!NOTE]
> Antes de começar, seu aplicativo lógico já deve ter uma etapa onde você possa adicionar branches paralelos.

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a>, abra o aplicativo lógico no Designer do Aplicativo Lógico.

1. Mova o ponteiro sobre a seta acima da etapa em que você deseja adicionar ramificações paralelas. Escolha o **plus** sinal ( **+** ) que aparece e, em seguida, escolha **adicionar uma ramificação paralela**. 

   ![Adicionar branch paralelo](media/logic-apps-control-flow-branches/add-parallel-branch.png)

1. Na caixa de pesquisa, encontre e selecione a ação desejada.

   ![Captura de tela que mostra a janela "escolher uma ação" no designer do aplicativo lógico.](media/logic-apps-control-flow-branches/find-select-parallel-action.png)

   Sua ação selecionada agora aparece na ramificação paralela, por exemplo:

   ![Localize e selecione a ação que você deseja](media/logic-apps-control-flow-branches/added-parallel-branch.png)

1. Agora, em cada ramificação paralela, adicione as etapas desejadas. Para adicionar outra ação a uma ramificação, mova seu ponteiro para a ação em que você deseja adicionar uma ação sequencial. Escolha o sinal **mais** ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

   ![Adicionar ação sequencial ao branch paralelo](media/logic-apps-control-flow-branches/add-sequential-action.png)

1. Na caixa de pesquisa, encontre e selecione a ação desejada.

   ![Captura de tela que mostra a janela "escolher uma ação" e a caixa de pesquisa no designer do aplicativo lógico.](media/logic-apps-control-flow-branches/find-select-sequential-action.png)

   A ação selecionada agora aparece dentro da ramificação atual, por exemplo:

   ![Encontre e selecione a ação sequencial](media/logic-apps-control-flow-branches/added-sequential-action.png)

Para mesclar de volta os branches, [una seus branches paralelos](#join-branches). 

<a name="parallel-json"></a>

## <a name="parallel-branch-definition-json"></a>Definição de branch paralelo (JSON)

Se você estiver trabalhando na exibição de código, será possível definir uma estrutura paralela na definição JSON do aplicativo lógico, por exemplo:

``` json
{
  "triggers": {
    "myTrigger": {}
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": {},
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

<a name="join-branches"></a>

## <a name="join-parallel-branches"></a>Unir branches paralelos

Para mesclar branches paralelos, adicione uma etapa na parte inferior abaixo de todos os branches. Esta etapa é executada após todos os branches paralelos terem concluído a execução.

![Unir branches paralelos](media/logic-apps-control-flow-branches/join.png)

1. No [Portal do Azure](https://portal.azure.com), encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico. 

1. Sob as ramificações paralelas que você deseja unir, escolha **Nova etapa**. 

   ![Adicionar etapa ingressar](media/logic-apps-control-flow-branches/add-join-step.png)

1. Na caixa de pesquisa, localize e selecione a ação que deseja como a etapa que una os branches.

   ![Localize e selecione a ação que una os branches paralelos](media/logic-apps-control-flow-branches/join-steps.png)

   Seus branches paralelos são mesclados.

   ![Ramificações unidas](media/logic-apps-control-flow-branches/joined-branches.png)

<a name="join-json"></a>

## <a name="join-definition-json"></a>Definição de união (JSON)

Se você estiver trabalhando na exibição de código, será possível definir uma estrutura de união na definição JSON do aplicativo lógico, por exemplo:

``` json
{
  "triggers": {
    "myTrigger": { }
  },
  "actions": {
    "parentAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {}
    },
    "branchAction1": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "branchAction2": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "parentAction": [
          "Succeeded"
        ]
      }
    },
    "joinAction": {
      "type": "<action-type>",
      "inputs": { },
      "runAfter": {
        "branchAction1": [
          "Succeeded"
        ],
        "branchAction2": [
          "Succeeded"
        ]
      }
    }
  },
  "outputs": {}
}
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite a [página de perguntas e respostas da Microsoft sobre os Aplicativos Lógicos do Azure](/answers/topics/azure-logic-apps.html).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar e repetir (loops)](../logic-apps/logic-apps-control-flow-loops.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
