---
title: Criar variáveis para salvar valores - Aplicativos Lógicos do Azure | Microsoft Docs
description: Como salvar e gerenciar valores criando variáveis em Aplicativos Lógicos do Azure
services: logic-apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.topic: article
ms.date: 05/30/2018
ms.service: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: e525e5584e4835b0f2b73203c818c3f799b77cf5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61004383"
---
# <a name="create-variables-for-saving-and-managing-values-in-azure-logic-apps"></a>Criar variáveis para salvar e gerenciar valores nos Aplicativos Lógicos do Azure

Este artigo mostra como você pode armazenar e trabalhar com valores em seu aplicativo lógico criando variáveis. Por exemplo, as variáveis podem ajudar você a contar o número de vezes que um loop é executado. Ao iterar em uma matriz ou verificar uma matriz em busca de um item específico, você pode usar uma variável para fazer referência ao número de índice de cada item da matriz. 

Você pode criar variáveis para tipos de dados, como inteiro, float, booliano, cadeia de caracteres, matriz e objeto. Depois de criar uma variável, você pode executar outras tarefas, por exemplo:

* Obter ou consultar o valor da variável.
* Aumentar ou diminuir a variável em um valor constante, também conhecido como *incremento* e *decremento*.
* Atribuir um valor diferente para a variável.
* Inserir ou *acrescentar* o valor da variável como a última vez em uma cadeia de caracteres ou matriz.

As variáveis existem e são globais apenas dentro da instância de aplicativo lógico que as cria. Além disso, elas persistem em quaisquer iterações de loop dentro de uma instância de aplicativo lógico. Ao fazer referência a uma variável, use o nome da variável como o token, não como o nome da ação, que é a maneira comum de fazer referência as saídas de uma ação. 

> [!IMPORTANT]
> Por padrão, os ciclos em um loop "Foreach" são executados em paralelo. Quando você usa variáveis em loops, execute o loop [sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) para que as variáveis retornem resultados previsíveis. 

Caso você ainda não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se em uma conta gratuita do Azure</a>. 

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este artigo, estes são os itens que você precisa:

* O aplicativo lógico onde você deseja criar uma variável 

  Se ainda não estiver familiarizado com aplicativo lógicos, consulte [O que são os Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Início Rápido: criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico 

  Antes de adicionar ações para criar e trabalhar com variáveis, seu aplicativo lógico deve começar com um gatilho.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializar variável

Você pode criar uma variável e declarar seu tipo de dados e o valor inicial, tudo dentro de uma ação em seu aplicativo lógico. Você só pode declarar variáveis no nível global, não em escopos, condições e loops. 

1. No <a href="https://portal.azure.com" target="_blank">Portal do Azure</a> ou no Visual Studio, abra seu aplicativo lógico no Designer do Aplicativo Lógico. 

   Este exemplo usa o Portal do Azure e um aplicativo lógico com um gatilho existente.

2. Em seu aplicativo lógico, na etapa em que você deseja adicionar uma variável, execute uma destas etapas: 

   * Para adicionar uma ação na última etapa, escolha **Nova etapa** > **Adicionar uma ação**.

     ![Adicionar ação](./media/logic-apps-create-variables-store-values/add-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. 
   Escolha o sinal de adição e depois **Adicionar uma ação**.

3. Na caixa de pesquisa, insira "variáveis" como filtro. Na lista de ações, selecione **Variáveis - Inicializar variável**.

   ![Ação selecionar](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

4. Forneça essas informações para sua variável:

   | Propriedade | Necessário | Value |  DESCRIÇÃO |
   |----------|----------|-------|--------------|
   | Name | Sim | <*variable-name*> | O nome da variável para incrementar | 
   | Type | Sim | <*variable-type*> | O tipo de dados para a variável | 
   | Value | Não  | <*start-value*> | O valor inicial da variável <p><p>**Dica**: embora seja opcional, defina esse valor como uma prática recomendada para que você sempre saiba o valor inicial da variável. | 
   ||||| 

   ![Inicializar variável](./media/logic-apps-create-variables-store-values/initialize-variable.png)

5. Agora, continue adicionando as ações que você quer. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

Se você alternar do editor do modo de exibição de designer para código, veja como a ação **Inicializar variável** aparece dentro da sua definição de aplicativo lógico, que está no formato JSON (JavaScript Object Notation):

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   }
},
```

Confira exemplos de outros tipos de variáveis:

*Variável de cadeia de caracteres*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myStringVariable",
               "type": "String",
               "value": "lorem ipsum"
          } ]
      },
      "runAfter": {}
   }
},
```

*Variável booliana*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myBooleanVariable",
               "type": "Boolean",
               "value": false
          } ]
      },
      "runAfter": {}
   }
},
```

*Matriz com inteiros*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": [1, 2, 3]
          } ]
      },
      "runAfter": {}
   }
},
```

*Matriz com cadeias de caracteres*

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "myArrayVariable",
               "type": "Array",
               "value": ["red", "orange", "yellow"]
          } ]
      },
      "runAfter": {}
   }
},
```

<a name="get-value"></a>

## <a name="get-the-variables-value"></a>Obter o valor da variável

Para recuperar ou fazer referência ao conteúdo de uma variável, também é possível usar a função [variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) no Designer de Aplicativo Lógico e no editor do modo de exibição de código.
Ao fazer referência a uma variável, use o nome da variável como o token, não como o nome da ação, que é a maneira comum de fazer referência as saídas de uma ação. 

Por exemplo, essa expressão obtém os itens a partir da variável de matriz [criada anteriormente neste artigo](#append-value) usando a função **variables()**. A função **string()** retorna o conteúdo da variável no formato de cadeia de caracteres: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Incrementar variável 

Para aumentar ou *incrementar* uma variável com um valor constante, adicione a ação **Variáveis - Incrementar variável** para o seu aplicativo lógico. Essa ação só funciona com variáveis de inteiro e float.

1. No Designer de Aplicativo Lógico, na etapa em que você deseja aumentar uma variável existente, escolha **Nova etapa** > **Adicionar uma ação**. 

   Por exemplo, este aplicativo lógico já tem um gatilho e uma ação que criou uma variável. Portanto, adicione uma nova ação sob estas etapas:

   ![Adicionar ação](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. Escolha o sinal de adição e depois **Adicionar uma ação**.

2. Na caixa de pesquisa, insira "integração" como filtro. Na lista de ações, selecione **Variáveis - Incrementar variável**.

   ![Selecione a ação "Incrementar variable"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

3. Forneça essas informações para incrementar sua variável:

   | Propriedade | Necessário | Value |  DESCRIÇÃO |
   |----------|----------|-------|--------------|
   | Name | Sim | <*variable-name*> | O nome da variável para incrementar | 
   | Value | Não  | <*increment-value*> | O valor usado para incrementar a variável. O valor padrão é um. <p><p>**Dica**: embora seja opcional, defina esse valor como uma prática recomendada para que você sempre saiba o valor específico para incrementar sua variável. | 
   |||| 

   Por exemplo:  
   
   ![Exemplo de valor de incremento](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

4. Quando terminar, selecione **Salvar** na barra de ferramentas do designer. 

Se você alternar do editor do modo de exibição de designer para código, veja como a ação **Incrementar variável** aparece dentro da sua definição de aplicativo lógico, que está no formato JSON:

```json
"actions": {
   "Increment_variable": {
      "type": "IncrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```

## <a name="example-create-loop-counter"></a>Exemplo: criar contador de loops

As variáveis são usadas normalmente para contar o número de vezes que um loop é executado. Este exemplo mostra como criar e usar variáveis para essa tarefa criando um loop que conta os anexos em um email.

1. No Portal do Azure, crie um aplicativo lógico em branco. Adicione um gatilho que verifica se há novos emails e anexos. 

   Este exemplo usa o gatilho do Outlook do Office 365 para **Quando um novo email é recebido**. 
   Você pode configurar esse gatilho para ser acionado somente quando o email tiver anexos.
   No entanto, você pode usar qualquer conector que verifica a existência de novos emails com anexos, como o conector do Outlook.com.

2. No gatilho, escolha **Mostrar opções avançadas**. Para o gatilho verificar se há anexos, e passar esses anexos para o fluxo de trabalho do aplicativo lógico, selecione **Sim** para essas propriedades:
   
   * **Tem Anexo** 
   * **Incluir Anexos** 

   ![Procurar e incluir anexos](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

3. Adicione a [ação **Inicializar variável**](#create-variable). Crie uma variável de inteiro chamada **Contagem** com um valor inicial de zero.

   ![Adicione a ação para "Inicializar variável"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

4. Para percorrer os anexos, adicione um loop *for each* escolhendo **Nova etapa** > **Mais** > **Adicionar um for each**.

   ![Adicionar um loop "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

5. No loop, clique na caixa **Selecionar uma saída de etapas anteriores**. Quando a lista de conteúdo dinâmico for exibida, selecione **Anexos**. 

   ![Selecionar "Anexos"](./media/logic-apps-create-variables-store-values/select-attachments.png)

   O campo **Anexos** passa uma matriz com os anexos de email da saída do gatilho para o seu loop.

6. No loop "for each", selecione **Adicionar uma ação**. 

   ![Selecione "Adicionar uma ação"](./media/logic-apps-create-variables-store-values/add-action-2.png)

7. Na caixa de pesquisa, insira "integração" como filtro. Na lista de ações, selecione **Variáveis - Incrementar variável**.

   > [!NOTE]
   > Verifique se a ação **Incrementar variável** aparece dentro do loop. Se a ação aparecer fora do loop, arraste a ação para o loop.

8. Na ação **Incrementar variável**, na lista **Nome**, selecione a variável **Count**. 

   ![Selecione a variável "Count"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

9. No loop, adicione qualquer ação que envia a você o número de anexos. Em sua ação, inclua o valor da variável **Count**, por exemplo: 

   ![Adicionar uma ação que envia os resultados](./media/logic-apps-create-variables-store-values/send-email-results.png)

10. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer. 

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Se seu aplicativo lógico não estiver habilitado, no menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Habilitar**. 

2. Na barra de ferramentas do Designer do Aplicativo Lógico, escolha **Executar**. Essa etapa inicia manualmente o aplicativo lógico.

3. Envie um email com um ou mais anexos para a conta de email usada neste exemplo.

   Esta etapa aciona o gatilho do aplicativo lógico, que cria e executa uma instância do fluxo de trabalho de seu aplicativo lógico.
   Como resultado, o aplicativo lógico envia uma mensagem ou email mostrando o número de anexos no email enviado por você.

Se você alternar do editor do modo de exibição de designer para código, é assim que o loop "for each" aparecerá com a ação **Incrementar variável** dentro da sua definição de aplicativo lógico, que está no formato JSON.

```json
"actions": {
   "For_each": {
      "type": "Foreach",
      "actions": {
         "Increment_variable": {
           "type": "IncrementVariable",
            "inputs": {
               "name": "Count",
               "value": 1
            },
            "runAfter": {}
         }
      },
      "foreach": "@triggerBody()?['Attachments']",
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="decrement-value"></a>

## <a name="decrement-variable"></a>Diminuir variável

Para reduzir ou *diminuir* uma variável com um valor constante, execute as etapas para [aumentar uma variável](#increment-value), exceto pelo fato de localizar e selecionar a ação **Variáveis - Diminuir variável** em vez disso. Essa ação só funciona com variáveis de inteiro e float.

Estas são as propriedades para a ação **Diminuir variável**:

| Propriedade | Necessário | Value |  DESCRIÇÃO |
|----------|----------|-------|--------------|
| Name | Sim | <*variable-name*> | O nome da variável para diminuir | 
| Value | Não  | <*increment-value*> | O valor para diminuição da variável. O valor padrão é um. <p><p>**Dica**: embora seja opcional, defina esse valor como uma prática recomendada para sempre conhecer o valor específico para diminuir a variável. | 
||||| 

Se você alternar do editor do modo de exibição de designer para código, veja como a ação **Diminuir variável** aparece dentro da sua definição de aplicativo lógico, que está no formato JSON.

```json
"actions": {
   "Decrement_variable": {
      "type": "DecrementVariable",
      "inputs": {
         "name": "Count",
         "value": 1
      },
      "runAfter": {}
   }
},
```


<a name="assign-value"></a>

## <a name="set-variable"></a>Definir variável

Para atribuir um valor diferente a uma variável existente, execute as etapas para [aumentar uma variável](#increment-value), exceto pelo fato de: 

1. Localizar e selecionar a ação **Variáveis - Definir variável** em vez disso. 

2. Fornecer o nome da variável e o valor que você deseja atribuir. O novo valor e a variável devem ter o mesmo tipo de dados.
O valor é necessário porque essa ação não tem um valor padrão. 

Estas são as propriedades para a ação **Definir variável**:

| Propriedade | Necessário | Value |  DESCRIÇÃO | 
|----------|----------|-------|--------------| 
| Name | Sim | <*variable-name*> | O nome da variável para alterar | 
| Value | Sim | <*new-value*> | O valor que você quer atribuir à variável. Ambos devem ter o mesmo tipo de dados. | 
||||| 

> [!NOTE]
> A menos que você esteja aumentando ou diminuindo variáveis, a alteração de variáveis dentro de loops *pode* criar resultados inesperados, pois os loops são executados em paralelo, ou simultaneamente, por padrão. Para esses casos, tente definir o loop para executar em sequência. Por exemplo, quando você quiser consultar o valor da variável dentro do loop, e espera o mesmo valor no início e no término dessa instância de loop, execute estas etapas para alterar como o loop é executado: 
>
> 1. No canto superior direito do loop, escolha o botão de reticências (... ) e, depois, escolha **Configurações**.
> 
> 2. Em **Controle de Simultaneidade**, altere a configuração **Substituir Padrão** para **Ativado**.
>
> 3. Arraste o controle deslizante de **Grau de Paralelismo** para **1**.

Se você alternar do editor do modo de exibição de designer para código, veja como a ação **Definir variável** aparece dentro da sua definição de aplicativo lógico, que está no formato JSON. Este exemplo altera o valor atual da variável "Count" para outro valor. 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
               "name": "Count",
               "type": "Integer",
               "value": 0
          } ]
      },
      "runAfter": {}
   },
   "Set_variable": {
      "type": "SetVariable",
      "inputs": {
         "name": "Count",
         "value": 100
      },
      "runAfter": {
         "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

<a name="append-value"></a>

## <a name="append-to-variable"></a>Acrescentar à variável

Para variáveis que armazenam cadeias de caracteres ou matrizes, você pode inserir ou *acrescentar* o valor de uma variável como o último item nessas cadeias de caracteres ou matrizes. Você pode executar as etapas para [aumentar a uma variável](#increment-value), exceto pelo fato de executar estas etapas no lugar: 

1. Localize e selecionar uma dessas ações com base em se a variável é uma cadeia de caracteres ou uma matriz: 

   * **Variáveis - Acrescentar à variável de cadeia de caracteres**
   * **Variáveis - Acrescentar à variável de matriz** 

2. Forneça o valor para acrescentar como o último item na cadeia de caracteres ou matriz. 
   Esse valor é obrigatório. 

Estas são as propriedades para as ações **Acrescentar a...**:

| Propriedade | Necessário | Value |  DESCRIÇÃO | 
|----------|----------|-------|--------------| 
| Name | Sim | <*variable-name*> | O nome da variável para alterar | 
| Value | Sim | <*append-value*> | O valor que você deseja acrescentar, que pode ser de qualquer tipo | 
|||||  

Se você alternar do editor do modo de exibição de designer para código, veja como a ação **Anexar à variável de matriz** aparece dentro da sua definição de aplicativo lógico, que está no formato JSON.
Este exemplo cria uma variável de matriz e adiciona outro valor como o último item na matriz. O resultado é uma variável atualizada que contém essa matriz: `[1,2,3,"red"]` 

```json
"actions": {
   "Initialize_variable": {
      "type": "InitializeVariable",
      "inputs": {
         "variables": [ {
            "name": "myArrayVariable",
            "type": "Array",
            "value": [1, 2, 3]
         } ]
      },
      "runAfter": {}
   },
   "Append_to_array_variable": {
      "type": "AppendToArrayVariable",
      "inputs": {
         "name": "myArrayVariable",
         "value": "red"
      },
      "runAfter": {
        "Initialize_variable": [ "Succeeded" ]
      }
   }
},
```

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
