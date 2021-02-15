---
title: Adicionar loops a ações de repetição
description: Criar loops que repetem ações de fluxo de trabalho ou processam matrizes nos Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/05/2019
ms.openlocfilehash: aa4be5852b4f8af00346a3ea9a86b13a85f99824
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358449"
---
# <a name="create-loops-that-repeat-workflow-actions-or-process-arrays-in-azure-logic-apps"></a>Criar loops que repetem ações de fluxo de trabalho ou processam matrizes nos Aplicativos Lógicos do Azure

Para processar uma matriz em seu aplicativo lógico, você pode criar um [loop "Foreach"](#foreach-loop). Esse loop repete uma ou mais ações em cada item na matriz. Para obter o limite do número de itens de matriz que um loop "foreach" pode processar, consulte [limites de simultaneidade, looping e debatching](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Para repetir ações até que uma condição seja atendida ou um estado seja alterado, você poderá criar uma [loop "Until"](#until-loop). O aplicativo lógico primeiro executa todas as ações dentro do loop e, em seguida, verifica a condição ou o estado. Se a condição é atendida, o loop para. Caso contrário, o loop repete. Para os limites padrão e máximo no número de loops de "until" que uma execução de aplicativo lógico pode ter, consulte [os limites de simultaneidade, looping e debatching](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

> [!TIP]
> Se você tiver um gatilho que recebe uma matriz e deseja executar um fluxo de trabalho para cada item de matriz, é possível fazer *debatch* dessa matriz com o [**SplitOn** da propriedade de gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch).

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="foreach-loop"></a>

## <a name="foreach-loop"></a>Loop "Foreach"

Um loop "foreach" repete uma ou mais ações em cada item de matriz e funciona apenas em matrizes. Aqui estão algumas considerações ao usar loops "Foreach":

* O loop "foreach" pode processar um número limitado de itens de matriz. Para esse limite, consulte [limites de simultaneidade, looping e debatching](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* Por padrão, as iterações em um loop "foreach" são executadas ao mesmo tempo ou em paralelo. Esse comportamento difere da [ativação de energia **para cada** loop](/power-automate/apply-to-each) em que as iterações são executadas uma de cada vez ou sequencialmente. No entanto, você pode [Configurar iterações de loop "foreach" sequenciais](#sequential-foreach-loop). Por exemplo, se você quiser pausar a próxima iteração em um loop "foreach" usando a [ação de atraso](../connectors/connectors-native-delay.md), será necessário definir o loop para ser executado em sequência.

  A exceção ao comportamento padrão são loops aninhados em que as iterações sempre são executadas em sequência, não em paralelo. Para executar operações em paralelo para itens em um loop aninhado, crie e [chame um aplicativo lógico filho](../logic-apps/logic-apps-http-endpoint.md).

* Para obter resultados previsíveis de operações em variáveis durante cada iteração do loop, execute esses loops sequencialmente. Por exemplo, quando um looping em execução simultânea termina, o incremento, a diminuição e o acréscimo às operações de variável retornam resultados previsíveis. No entanto, durante cada iteração no loop em execução simultânea, essas operações podem retornar resultados imprevisíveis. 

* As ações em um loop "Foreach" usem a expressão [`@item()`](../logic-apps/workflow-definition-language-functions-reference.md#item) 
para fazer referência e processar cada item na matriz. Se você especificar dados que não estejam em uma matriz, o fluxo de trabalho do aplicativo lógico falhará. 

Este exemplo de aplicativo lógico envia um resumo diário para um RSS feed de um site. O aplicativo usa um loop "Foreach" que envia um email para cada novo item.

1. [Crie este aplicativo lógico de exemplo](../logic-apps/quickstart-create-first-logic-app-workflow.md) com uma conta do Outlook.com ou uma conta corporativa ou de estudante.

2. Entre o gatilho RSS e enviar ação de email, adicione um loop "Foreach". 

   1. Para adicionar um loop entre as etapas, mova o ponteiro sobre a seta entre essas etapas. 
   Escolha o sinal **mais** ( **+** ) que aparece e selecione **Adicionar uma ação**.

      ![Selecione "Adicionar uma ação"](media/logic-apps-control-flow-loops/add-for-each-loop.png)

   1. Na caixa de pesquisa, escolha **Tudo**. Na caixa de pesquisa, digite "for each" como filtro. Na lista de ações, selecione esta ação: **For each – Controle**

      ![Adicionar o loop "for each"](media/logic-apps-control-flow-loops/select-for-each.png)

3. Agora, compile o loop. Em **Selecionar uma saída das etapas anteriores** após a lista **Adicionar conteúdo dinâmico** aparecer, selecione a matriz de **Links de feed** , que é emitida pelo gatilho RSS. 

   ![Selecionar a partir da lista de conteúdo dinâmico](media/logic-apps-control-flow-loops/for-each-loop-dynamic-content-list.png)

   > [!NOTE] 
   > É possível selecionar *apenas* saídas de matriz da etapa anterior.

   A matriz selecionada agora aparece aqui:

   ![Selecionar matriz](media/logic-apps-control-flow-loops/for-each-loop-select-array.png)

4. Para executar uma ação em cada item de matriz, arraste a ação **Enviar um email** para o loop. 

   O aplicativo lógico pode ser semelhante a este exemplo:

   ![Adicionar etapas para loop "Foreach"](media/logic-apps-control-flow-loops/for-each-loop-with-step.png)

5. Salve seu aplicativo lógico. Para testar manualmente o aplicativo lógico, na barra de ferramentas do Designer, escolha **Executar**.

<a name="for-each-json"></a>

## <a name="foreach-loop-definition-json"></a>Definição de loop "Foreach" (JSON)

Se você estiver trabalhando na exibição de código para o aplicativo lógico, será possível definir o loop `Foreach` na definição JSON do aplicativo lógico, por exemplo:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": {
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "@{item()}",
                  "Subject": "New CNN post @{triggerBody()?['publishDate']}",
                  "To": "me@contoso.com"
               },
               "host": {
                  "api": {
                     "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/office365"
                  },
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {}
   }
}
```

<a name="sequential-foreach-loop"></a>

## <a name="foreach-loop-sequential"></a>Loop "Foreach": Sequencial

Por padrão, os ciclos em um loop "Foreach" são executados em paralelo. Para executar cada ciclo sequencialmente, defina a opção **Sequencial** do loop. Os loops "Foreach" devem ser executados sequencialmente quando você tem loops aninhados ou variáveis dentro de loops em que você espera resultados previsíveis. 

1. No canto superior direito do loop, escolha **reticências** ( **...** ) > **Configurações**.

   ![No loop "Foreach" loop, escolha "..." > "Configurações"](media/logic-apps-control-flow-loops/for-each-loop-settings.png)

1. Em **Controle de Simultaneidade** , defina a configuração **Controle de Simultaneidade** como **Ativado**. Mova o controle deslizante **Grau de Paralelismo** para **1** e escolha **Concluído**.

   ![Ativar o controle de simultaneidade](media/logic-apps-control-flow-loops/for-each-loop-sequential-setting.png)

Se você estiver trabalhando com a definição de JSON do aplicativo lógico, você pode usar a opção `Sequential` adicionando o parâmetro `operationOptions`, por exemplo:

``` json
"actions": {
   "myForEachLoopName": {
      "type": "Foreach",
      "actions": {
         "Send_an_email": { }
      },
      "foreach": "@triggerBody()?['links']",
      "runAfter": {},
      "operationOptions": "Sequential"
   }
}
```

<a name="until-loop"></a>

## <a name="until-loop"></a>Loop "Until"
  
Para executar e repetir ações até que uma condição seja atendida ou um estado seja alterado, coloque essas ações em um loop "Until". O aplicativo lógico primeiro executa toda e qualquer ação dentro do loop e, em seguida, verifica a condição ou o estado. Se a condição é atendida, o loop para. Caso contrário, o loop repete. Para os limites padrão e máximo no número de loops de "until" que uma execução de aplicativo lógico pode ter, consulte [os limites de simultaneidade, looping e debatching](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

Aqui estão alguns cenários comuns em que é possível utilizar um loop "Until":

* Chamar um ponto de extremidade até obter a resposta desejada.

* Criar um registro em um banco de dados. Aguardar até que um campo específico nesse registro seja aprovado. Continuar o processamento. 

Começando às 8h00 todos os dias, esse aplicativo lógico incrementa uma variável até que o valor da variável seja igual a 10. Em seguida, o aplicativo lógico envia um email que confirma o valor atual. 

> [!NOTE]
> Essas etapas usam o Outlook do Office 365, mas você pode usar qualquer provedor de email que seja compatível com Aplicativos Lógicos. 
> [Veja a lista de conectores aqui](/connectors/). Se você usar uma outra conta de email, as etapas gerais serão as mesmas, mas a interface do usuário poderá parecer um pouco diferente. 

1. Criar um aplicativo lógico em branco. No Designer de Aplicativos Lógicos, na caixa de pesquisa, escolha **Tudo**. Pesquise por "recorrência". 
   Na lista de gatilhos, selecione este gatilho: **Recorrência – Agenda**

   ![Adicionar o gatilho "Recorrência – Agendamento"](./media/logic-apps-control-flow-loops/do-until-loop-add-trigger.png)

1. Especifique quando o gatilho será disparado, configurando o intervalo, a frequência e a hora do dia. Para definir a hora, escolha **Mostrar opções avançadas**.

   ![Configurar o agendamento de recorrência](./media/logic-apps-control-flow-loops/do-until-loop-set-trigger-properties.png)

   | Propriedade | Valor |
   | -------- | ----- |
   | **Intervalo** | 1 | 
   | **Frequência** | Dia |
   | **A estas horas** | 8 |
   ||| 

1. No gatilho, escolha **Nova etapa**. 
   Pesquise por "variáveis" e selecione esta ação: **Inicializar variável – Variáveis**

   ![Adicionar a ação Inicializar variável – Variáveis](./media/logic-apps-control-flow-loops/do-until-loop-add-variable.png)

1. Configure a variável com estes valores:

   ![Definir propriedades variáveis](./media/logic-apps-control-flow-loops/do-until-loop-set-variable-properties.png)

   | Propriedade | Valor | Descrição |
   | -------- | ----- | ----------- |
   | **Nome** | Limite | Nome da variável | 
   | **Tipo** | Integer | Tipo de dados da variável | 
   | **Valor** | 0 | Valor inicial de variável | 
   |||| 

1. Na ação **Inicializar variável** , escolha **Nova etapa**. 

1. Na caixa de pesquisa, escolha **Tudo**. Pesquise por "until" e selecione esta ação: **Until – Controle**

   ![Adicionar o loop "Until"](./media/logic-apps-control-flow-loops/do-until-loop-add-until-loop.png)

1. Compile a condição de saída do loop, selecionando a variável **Limite** e o operador **é igual**. 
   Insira **10** como o valor da comparação.

   ![Compile a condição de saída para parar o loop](./media/logic-apps-control-flow-loops/do-until-loop-settings.png)

1. Dentro do loop, escolha **Adicionar uma ação**. 

1. Na caixa de pesquisa, escolha **Tudo**. Pesquise por "variáveis" e selecione esta ação: **Incrementar variável – Variáveis**

   ![Adicionar ação para incrementar a variável](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable.png)

1. Para **Nome** , selecione a variável **Limite**. Para **Valor** , insira "1". 

     ![Incrementar "Limite" por 1](./media/logic-apps-control-flow-loops/do-until-loop-increment-variable-settings.png)

1. Fora e abaixo do loop, escolha **Nova etapa**. 

1. Na caixa de pesquisa, escolha **Tudo**. 
     Encontre e adicione uma ação que envia email, por exemplo: 

     ![Adicionar ação que envia email](media/logic-apps-control-flow-loops/do-until-loop-send-email.png)

1. Se solicitado, entre em sua conta de email.

1. Defina as propriedades da ação do email. Adicione a variável **Limite** para o assunto. Dessa forma, é possível confirmar se o valor atual da variável atende à condição especificada, por exemplo:

      ![Configurar propriedades de email](./media/logic-apps-control-flow-loops/do-until-loop-send-email-settings.png)

      | Propriedade | Valor | Descrição |
      | -------- | ----- | ----------- | 
      | **Para** | *\<email-address\@domain>* | O endereço de email do destinatário. Para testes, use seu próprio endereço de email. | 
      | **Assunto** | O valor atual para "Limit" é **Limite** | Especifique o assunto do email. Para esse exemplo, certifique-se de incluir a variável **Limite**. | 
      | **Corpo** | <*email-conteúdo*> | Especifique o conteúdo da mensagem de email que deseja enviar. Para esse exemplo, insira o texto de sua escolha. | 
      |||| 

1. Salve seu aplicativo lógico. Para testar manualmente o aplicativo lógico, na barra de ferramentas do Designer, escolha **Executar**.

      Após a lógica iniciar a execução, você receberá um email com o conteúdo especificado:

      ![Email recebido](./media/logic-apps-control-flow-loops/do-until-loop-sent-email.png)

<a name="prevent-endless-loops"></a>

## <a name="prevent-endless-loops"></a>Impedir loops infinitos

O loop "until" interrompe a execução com base nessas propriedades, portanto, certifique-se de definir seus valores adequadamente:

* **Contagem** : esse valor é o número mais alto de loops executados antes de o loop ser encerrado. Para os limites padrão e máximo no número de loops de "until" que uma execução de aplicativo lógico pode ter, consulte [os limites de simultaneidade, looping e debatching](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

* **Tempo limite** : esse valor é a maior quantidade de tempo que o loop é executado antes de sair e é especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Para obter os limites padrão e máximo do valor de **tempo limite** , consulte [os limites de simultaneidade, looping e debatching](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits).

  O valor de tempo limite é avaliado para cada ciclo de loop. Se qualquer ação no loop demorar mais do que o tempo limite, o ciclo atual não parará. No entanto, o próximo ciclo será iniciado porque a condição de limite não foi atendida.

Para alterar esses limites, na ação de loop, selecione **alterar limites**.

<a name="until-json"></a>

## <a name="until-definition-json"></a>Definição "Until" (JSON)

Se você estiver trabalhando na exibição de código para o aplicativo lógico, será possível definir um loop `Until` na definição JSON do aplicativo lógico, por exemplo:

``` json
"actions": {
   "Initialize_variable": {
      // Definition for initialize variable action
   },
   "Send_an_email": {
      // Definition for send email action
   },
   "Until": {
      "type": "Until",
      "actions": {
         "Increment_variable": {
            "type": "IncrementVariable",
            "inputs": {
               "name": "Limit",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "expression": "@equals(variables('Limit'), 10)",
      // To prevent endless loops, an "Until" loop 
      // includes these default limits that stop the loop. 
      "limit": { 
         "count": 60,
         "timeout": "PT1H"
      },
      "runAfter": {
         "Initialize_variable": [
            "Succeeded"
         ]
      }
   }
}
```

Neste exemplo, o loop "Until" chama um ponto de extremidade HTTP, que cria um recurso. O loop para quando o corpo da resposta HTTP retorna com o status `Completed`. Para impedir loops infinitos, o loop também irá parar se alguma destas condições ocorrer:

* O loop foi executado 10 vezes, conforme especificado pelo atributo `count`. O padrão é 60 vezes. 

* O loop foi executado durante duas horas, conforme especificado pelo atributo `timeout` no formato ISO 8601. O padrão é uma hora.
  
``` json
"actions": {
   "myUntilLoopName": {
      "type": "Until",
      "actions": {
         "Create_new_resource": {
            "type": "Http",
            "inputs": {
               "body": {
                  "resourceId": "@triggerBody()"
               },
               "url": "https://domain.com/provisionResource/create-resource",
               "body": {
                  "resourceId": "@triggerBody()"
               }
            },
            "runAfter": {},
            "type": "ApiConnection"
         }
      },
      "expression": "@equals(triggerBody(), 'Completed')",
      "limit": {
         "count": 10,
         "timeout": "PT2H"
      },
      "runAfter": {}
   }
}
```

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite a [página de perguntas e respostas da Microsoft sobre os Aplicativos Lógicos do Azure](/answers/topics/azure-logic-apps.html).
* Para enviar ou votar em recursos e sugestões, visite o [site de comentários do usuário de Aplicativos Lógicos do Azure](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Executar etapas baseadas em uma condição (instruções condicionais)](../logic-apps/logic-apps-control-flow-conditional-statement.md)
* [Executar etapas baseadas em valores diferentes (instruções de comutador)](../logic-apps/logic-apps-control-flow-switch-statement.md)
* [Executar ou mesclar etapas paralelas (branches)](../logic-apps/logic-apps-control-flow-branches.md)
* [Executar etapas baseadas no status da ação agrupada (escopos)](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md)
