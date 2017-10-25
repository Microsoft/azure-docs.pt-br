---
title: "Adicionar condições e iniciar fluxos de trabalho – Aplicativo Lógico do Azure | Microsoft Docs"
description: "Controle como os fluxos de trabalho são executados em Aplicativos Lógicos do Azure adicionando parâmetros, gatilhos, ações e lógica condicional."
author: stepsic-microsoft-com
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e4e24de4-049a-4b3a-a14c-3bf3163287a8
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: e632c48ed31e82536db55a9c54438bece0c38fd4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-logic-apps-features"></a>Usar recursos de aplicativos lógicos

Em um [tópico anterior](../logic-apps/logic-apps-create-a-logic-app.md), você criou seu primeiro aplicativo lógico. Para controlar o fluxo de trabalho do aplicativo lógico, você pode especificar diferentes caminhos para o aplicativo lógico executar e como processar dados em matrizes, coleções e lotes. Você pode incluir esses elementos no fluxo de trabalho do aplicativo lógico:

* Condições e [instruções switch](../logic-apps/logic-apps-switch-case.md) permitem que seu aplicativo lógico execute ações diferentes com base em se condições específicas são atendidas.

* [Loops](../logic-apps/logic-apps-loops-and-scopes.md) permitem que seu aplicativo lógico execute etapas repetidamente. Por exemplo, você pode repetir ações em uma matriz quando usa um loop **For_each**. Ou você pode repetir ações até que uma condição seja atendida quando usa um loop **Until**.

* [Escopos](../logic-apps/logic-apps-loops-and-scopes.md) permitem agrupar série de ações, por exemplo, para implementar o tratamento de exceção.

* [Debatching](../logic-apps/logic-apps-loops-and-scopes.md) permite que seu aplicativo lógico inicie fluxos de trabalho separados para itens em uma matriz ao usar o comando **SplitOn**.

Este tópico apresenta outros conceitos para a criação de seu aplicativo lógico:

* Exibição de código para editar um aplicativo lógico existente
* Opções para iniciar um fluxo de trabalho

## <a name="conditions-run-steps-only-after-meeting-a-condition"></a>Condições: executar etapas somente depois de atender a uma condição

Para que seu aplicativo lógico execute etapas somente quando os dados atendem a critérios específicos, você pode adicionar uma condição que compara dados no fluxo de trabalho em valores ou campos específicos.

Por exemplo, suponha que você tem um aplicativo lógico que envia muitos emails para postagens no feed RSS de um site. Você pode adicionar uma condição para que seu aplicativo lógico envie emails somente quando a nova postagem pertencer a uma categoria específica.

1. No [Portal do Azure](https://portal.azure.com), encontre e abra seu aplicativo lógico no Designer de Aplicativo Lógico.

2. Adicione uma condição para a localização do fluxo de trabalho desejado. 

   Para adicionar a condição entre etapas existentes no fluxo de trabalho do aplicativo lógico, mova o ponteiro sobre a seta em que deseja adicionar a condição. 
   Escolha o **sinal de adição** (**+**), em seguida, escolha **Adicionar uma condição**. Por exemplo:

   ![Adicionar condição ao aplicativo lógico](./media/logic-apps-use-logic-app-features/add-condition.png)

   > [!NOTE]
   > Se você quiser adicionar uma condição no final do fluxo de trabalho atual, vá até o final do seu aplicativo lógico e escolha **+ Nova etapa**.

3. Agora defina a condição. Especifique o campo de origem que você deseja avaliar, a operação a ser executada e o valor ou campo de destino. Para adicionar campos existentes à sua condição, escolha na **lista Adicionar conteúdo dinâmico**.

   Por exemplo:

   ![Editar condição no modo básico](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode.png)

   Aqui está a condição completa:

   ![Condição completa](./media/logic-apps-use-logic-app-features/edit-condition-basic-mode-2.png)

   > [!TIP]
   > Para definir a condição no código, escolha **Editar no modo avançado**. Por exemplo:
   > 
   > ![Editar condição no código](./media/logic-apps-use-logic-app-features/edit-condition-advanced-mode.png)

4. Em **SE SIM** e **SE NÃO**, adicione as etapas a serem executadas com base em se a condição é atendida.

   Por exemplo:

   ![Condição com caminhos SIM e NÃO](./media/logic-apps-use-logic-app-features/condition-yes-no-path.png)

   > [!TIP]
   > Você pode arrastar ações existentes para os caminhos **SE SIM** e **SE NÃO**.

5. Quando terminar, salve o aplicativo lógico.

Agora você recebe emails somente quando as postagens atendem à sua condição.

## <a name="repeat-actions-over-a-list-with-foreach"></a>Repita ações em uma lista com forEach

O loop forEach especifica uma matriz para repetir uma ação. Se ela não for uma matriz, o fluxo falhará. Por exemplo, se você tiver action1 que gere uma matriz de mensagens e desejar enviar cada mensagem, você poderá incluir essa instrução forEach nas propriedades da ação: `forEach : "@action('action1').outputs.messages"`

## <a name="edit-the-code-definition-for-a-logic-app"></a>Editar a definição de código para um aplicativo lógico

Embora você tenha o Designer de Aplicativos Lógicos, você pode editar diretamente o código que define um aplicativo lógico.

1. Na barra de comandos, escolha **Exibição de código**.

    Isso abre um editor completo que mostra a definição que você editou.

    ![Visualização do código](media/logic-apps-use-logic-app-features/codeview.png)

    No editor de texto, você pode copiar e colar qualquer número de ações dentro do mesmo aplicativo lógico ou entre os aplicativos lógicos. 
    Você também pode facilmente adicionar ou remover seções inteiras da definição e compartilhar definições com outras pessoas.

2. Para salvar suas edições, escolha **Salvar**.

## <a name="parameters"></a>Parâmetros

Alguns recursos de aplicativos lógicos como parâmetros estão disponíveis apenas na exibição de código. Parâmetros facilitam a reutilização valores ao longo do seu aplicativo lógico. Por exemplo, se você tiver um endereço de email que deseje usar em várias ações, deverá defini-lo como um parâmetro.

Os parâmetros são uma boa maneira de recuperar valores que você provavelmente alterará várias vezes. Eles são especialmente úteis quando você precisa substituir parâmetros em ambientes diferentes. Para saber como substituir parâmetros com base no ambiente, consulte [Criar definições de aplicativo lógico](../logic-apps/logic-apps-author-definitions.md) e a [Documentação da API REST](https://docs.microsoft.com/rest/api/logic).

Este exemplo mostra como atualizar seu aplicativo lógico existente para que você possa usar parâmetros para o termo de consulta.

1. Na exibição de código, localize o objeto `parameters : {}` e adicione um objeto `currentFeedUrl`:

        "currentFeedUrl" : {
            "type" : "string",
            "defaultValue" : "http://rss.cnn.com/rss/cnn_topstories.rss"
        }

2. Vá para a ação `When_a_feed-item_is_published`, localize a seção `queries` e substitua o valor da consulta por: `"feedUrl": "#@{parameters('currentFeedUrl')}"` 

    Para unir duas ou mais cadeias de caracteres, você também pode usar a função `concat`. 
    Por exemplo, `"@concat('#',parameters('currentFeedUrl'))"` funciona como demonstrado acima.

3.  Quando terminar, escolha **Salvar**. 

    Agora você pode alterar o feed RSS do site passando uma URL diferente pelo objeto `currentFeedURL`.

Saiba mais sobre [como criar definições de aplicativo lógico](../logic-apps/logic-apps-author-definitions.md).

## <a name="start-logic-app-workflows"></a>Iniciar fluxos de trabalho de aplicativo lógico

Você tem diferentes opções para iniciar o fluxo de trabalho definido no aplicativo lógico. Você sempre pode iniciar um fluxo de trabalho por demanda no [Portal do Azure].

### <a name="recurrence-triggers"></a>Gatilhos de recorrência

Um gatilho de recorrência é executado em um intervalo especificado por você. Quando o gatilho tem lógica condicional, ele determina se o fluxo de trabalho precisa ou não ser executado. Um gatilho indica que o fluxo de trabalho deve ser executado retornando um código de status `200`. Quando o fluxo de trabalho não precisa ser executado, o gatilho retorna um código de status `202`.

### <a name="callback-using-rest-apis"></a>Retorno de chamada usando APIs REST

Para iniciar um fluxo de trabalho, os serviços podem chamar um ponto de extremidade do aplicativo lógico. Para iniciar esta variante de aplicativo lógico sob demanda, escolha **Executar agora** na barra de comandos. Consulte [Iniciar fluxos de trabalho chamando pontos de extremidade de aplicativo lógico como gatilhos](../logic-apps/logic-apps-http-endpoint.md). 

<!-- Shared links -->
[Portal do Azure]: https://portal.azure.com

## <a name="next-steps"></a>Próximas etapas

* [Instruções switch](../logic-apps/logic-apps-switch-case.md) 
* [Loops, escopos e debatching](../logic-apps/logic-apps-loops-and-scopes.md)
* [Criar definições de aplicativo lógico](../logic-apps/logic-apps-author-definitions.md)