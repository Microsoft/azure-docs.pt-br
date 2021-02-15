---
title: Criar e gerenciar variáveis para armazenar e passar valores
description: Saiba como armazenar, gerenciar, usar e passar valores usando variáveis em suas tarefas automatizadas e fluxo de trabalho que você cria com os aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: b486b94a74d98f5630bd0bf40ebf0864c2ec5ab8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333895"
---
# <a name="store-and-manage-values-by-using-variables-in-azure-logic-apps"></a>Salvar e gerenciar valores usando variáveis em Aplicativos Lógicos do Azure

Este artigo mostra como criar e trabalhar com variáveis que você usa para armazenar valores em seu aplicativo lógico. Por exemplo, as variáveis podem ajudá-lo a acompanhar o número de vezes que um loop é executado. Para iterar em uma matriz ou verificar uma matriz de um item específico, você pode usar uma variável para fazer referência ao número de índice de cada item da matriz.

Você pode criar variáveis para tipos de dados, como inteiro, float, booliano, cadeia de caracteres, matriz e objeto. Depois de criar uma variável, você pode executar outras tarefas, por exemplo:

* Obter ou consultar o valor da variável.
* Aumentar ou diminuir a variável em um valor constante, também conhecido como *incremento* e *decremento*.
* Atribuir um valor diferente para a variável.
* Inserir ou *acrescentar* o valor da variável como o último item em uma cadeia de caracteres ou matriz.

As variáveis existem e são globais apenas dentro da instância de aplicativo lógico que as cria. Além disso, elas persistem em quaisquer iterações de loop dentro de uma instância de aplicativo lógico. Quando você faz referência a uma variável, use o nome da variável como o token, não o nome da ação, que é a maneira usual de fazer referência a saídas de uma ação.

> [!IMPORTANT]
> Por padrão, os ciclos em um loop "for each" são executados em paralelo. Ao usar variáveis em loops, execute o loop [sequencialmente](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop) para que as variáveis retornem resultados previsíveis.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, [Inscreva-se para obter uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* O aplicativo lógico no qual você deseja criar a variável

  Se você não estiver familiarizado com os aplicativos lógicos, examine [o que é o aplicativo lógico do Azure?](../logic-apps/logic-apps-overview.md) e o [início rápido: Crie seu primeiro aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) como a primeira etapa no seu aplicativo lógico

  Antes de adicionar ações para criar e trabalhar com variáveis, seu aplicativo lógico deve começar com um gatilho.

<a name="create-variable"></a>

## <a name="initialize-variable"></a>Inicializar variável

Você pode criar uma variável e declarar seu tipo de dados e o valor inicial, tudo dentro de uma ação em seu aplicativo lógico. Você só pode declarar variáveis no nível global, não em escopos, condições e loops.

1. No [portal do Azure](https://portal.azure.com) ou no Visual Studio, abra seu aplicativo lógico no designer de aplicativo lógico.

   Este exemplo usa o Portal do Azure e um aplicativo lógico com um gatilho existente.

1. Em seu aplicativo lógico, na etapa em que você deseja adicionar uma variável, execute uma destas etapas: 

   * Para adicionar uma ação na última etapa, selecione **nova etapa**.

     ![Captura de tela que mostra a ação "nova etapa" selecionada na página "designer de aplicativo lógico".](./media/logic-apps-create-variables-store-values/add-action.png)

   * Para adicionar uma ação entre etapas, mova o mouse sobre a seta de conexão para que o sinal de adição ( **+** ) seja exibido. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `variables` como o filtro. Na lista ações, selecione **inicializar variável**.

   ![Ação selecionar](./media/logic-apps-create-variables-store-values/select-initialize-variable-action.png)

1. Forneça essas informações sobre sua variável, conforme descrito abaixo:

   | Propriedade | Obrigatório | Valor |  Descrição |
   |----------|----------|-------|--------------|
   | **Nome** | Sim | <*nome da variável*> | O nome da variável para incrementar |
   | **Tipo** | Sim | <*tipo de variável*> | O tipo de dados para a variável |
   | **Valor** | Não | <*valor inicial*> | O valor inicial da variável <p><p>**Dica**: embora seja opcional, defina esse valor como uma melhor prática para que você sempre saiba o valor inicial da variável. |
   |||||

   Por exemplo:

   ![Inicializar variável](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Agora, continue adicionando as ações que você quer. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

Se você alternar do designer para o editor de modo de exibição de código, aqui está a maneira como a ação **inicializar variável** aparece em sua definição de aplicativo lógico, que está no formato JavaScript Object Notation (JSON):

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

> [!NOTE]
> Embora a ação **inicializar variável** tenha uma `variables` seção estruturada como uma matriz, a ação pode criar apenas uma variável por vez. Cada nova variável requer uma ação de **variável de inicialização** individual.

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

Para recuperar ou fazer referência ao conteúdo de uma variável, também é possível usar a função [variables()](../logic-apps/workflow-definition-language-functions-reference.md#variables) no Designer de Aplicativo Lógico e no editor do modo de exibição de código. Ao fazer referência a uma variável, use o nome da variável como o token, não como o nome da ação, que é a maneira comum de fazer referência as saídas de uma ação.

Por exemplo, essa expressão Obtém os itens da variável de matriz [criada anteriormente neste artigo](#append-value) usando a `variables()` função. A `string()` função retorna o conteúdo da variável no formato de cadeia de caracteres: `"1, 2, 3, red"`

```json
@{string(variables('myArrayVariable'))}
```

<a name="increment-value"></a>

## <a name="increment-variable"></a>Incrementar variável 

Para aumentar ou *incrementar* uma variável por um valor constante, adicione a ação de **variável de incremento** ao seu aplicativo lógico. Essa ação só funciona com variáveis de inteiro e float.

1. No designer de aplicativo lógico, na etapa em que você deseja aumentar uma variável existente, selecione **nova etapa**. 

   Por exemplo, este aplicativo lógico já tem um gatilho e uma ação que criou uma variável. Portanto, adicione uma nova ação sob estas etapas:

   ![Adicionar ação](./media/logic-apps-create-variables-store-values/add-increment-variable-action.png)

   Para adicionar uma ação entre etapas existentes, mova o mouse sobre a seta de conexão para que o sinal de adição (+) apareça. Selecione o sinal de adição e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "integração" como filtro. Na lista ações, selecione **variável de incremento**.

   ![Selecione a ação "Incrementar variable"](./media/logic-apps-create-variables-store-values/select-increment-variable-action.png)

1. Forneça essas informações para incrementar sua variável:

   | Propriedade | Obrigatório | Valor |  Descrição |
   |----------|----------|-------|--------------|
   | **Nome** | Sim | <*nome da variável*> | O nome da variável para incrementar |
   | **Valor** | Não | <*incremento-valor*> | O valor usado para incrementar a variável. O valor padrão é um. <p><p>**Dica**: embora seja opcional, defina esse valor como uma melhor prática para que você sempre saiba o valor específico para incrementar sua variável. |
   ||||

   Por exemplo:

   ![Exemplo de valor de incremento](./media/logic-apps-create-variables-store-values/increment-variable-action-information.png)

1. Quando terminar, selecione **Salvar** na barra de ferramentas do designer.

Se você alternar do designer para o editor de modo de exibição de código, aqui está a maneira como a ação de **variável de incremento** aparece dentro de sua definição de aplicativo lógico, que está no formato JSON:

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

   Este exemplo usa o gatilho do Outlook do Office 365 para **Quando um novo email é recebido**. Você pode configurar esse gatilho para ser acionado somente quando o email tiver anexos. No entanto, você pode usar qualquer conector que verifica a existência de novos emails com anexos, como o conector do Outlook.com.

1. No gatilho, para verificar se há anexos e passá-los para o fluxo de trabalho do aplicativo lógico, selecione **Sim** para essas propriedades:

   * **Tem Anexo**
   * **Incluir Anexos**

   ![Procurar e incluir anexos](./media/logic-apps-create-variables-store-values/check-include-attachments.png)

1. Adicione a [ação **Inicializar variável**](#create-variable). Crie uma variável de inteiro chamada `Count` que tenha um valor de início zero.

   ![Adicione a ação para "Inicializar variável"](./media/logic-apps-create-variables-store-values/initialize-variable.png)

1. Para percorrer cada anexo, adicione um loop *for each* .

   1. Na ação **inicializar variável** , selecione **nova etapa**.

   1. Em **Escolha uma ação**, selecione **Interno**. Na caixa de pesquisa, insira `for each` como seu filtro de pesquisa e selecione **para cada um**.

      ![Adicionar um loop "for each"](./media/logic-apps-create-variables-store-values/add-loop.png)

1. No loop, clique na caixa **Selecionar uma saída de etapas anteriores**. Quando a lista de conteúdo dinâmico for exibida, selecione **Anexos**.

   ![Selecionar "Anexos"](./media/logic-apps-create-variables-store-values/select-attachments.png)

   A propriedade **Attachments** passa uma matriz, que tem os anexos de email da saída do gatilho em seu loop.

1. No loop **for each** , selecione **Adicionar uma ação**.

   ![Selecione "Adicionar uma ação"](./media/logic-apps-create-variables-store-values/add-action-2.png)

1. Na caixa de pesquisa, insira "integração" como filtro. Na lista ações, selecione **variável de incremento**.

   > [!NOTE]
   > Verifique se a ação **variável de incremento** aparece dentro do loop. Se a ação aparecer fora do loop, arraste a ação para o loop.

1. Na ação **Incrementar variável**, na lista **Nome**, selecione a variável **Count**.

   ![Selecione a variável "Count"](./media/logic-apps-create-variables-store-values/add-increment-variable-example.png)

1. No loop, adicione qualquer ação que envia a você o número de anexos. Em sua ação, inclua o valor da variável **Count**, por exemplo:

   ![Adicionar uma ação que envia os resultados](./media/logic-apps-create-variables-store-values/send-email-results.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

### <a name="test-your-logic-app"></a>Como testar o seu aplicativo lógico

1. Se seu aplicativo lógico não estiver habilitado, no menu do aplicativo lógico, selecione **visão geral**. Na barra de ferramentas, selecione **habilitar**.

1. Na barra de ferramentas do designer do aplicativo lógico, selecione **executar**. Essa etapa inicia manualmente o aplicativo lógico.

1. Envie um email com um ou mais anexos para a conta de email usada neste exemplo.

   Esta etapa aciona o gatilho do aplicativo lógico, que cria e executa uma instância do fluxo de trabalho de seu aplicativo lógico. Como resultado, o aplicativo lógico envia uma mensagem ou email mostrando o número de anexos no email enviado por você.

Se você alternar do designer para o editor de modo de exibição de código, aqui está a maneira como o loop **for each** aparece junto com a ação **variável de incremento** dentro de sua definição de aplicativo lógico, que está no formato JSON.

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

Para diminuir ou *decrementar* uma variável por um valor constante, siga as etapas para [aumentar uma variável](#increment-value) , exceto que você encontrar e selecionar a ação **diminuir variável** em vez disso. Essa ação só funciona com variáveis de inteiro e float.

Estas são as propriedades para a ação **Diminuir variável**:

| Propriedade | Obrigatório | Valor |  Descrição |
|----------|----------|-------|--------------|
| **Nome** | Sim | <*nome da variável*> | O nome da variável para diminuir | 
| **Valor** | Não | <*incremento-valor*> | O valor para diminuição da variável. O valor padrão é um. <p><p>**Dica**: embora seja opcional, defina esse valor como uma melhor prática para sempre conhecer o valor específico para diminuir da variável. |
||||| 

Se você alternar do designer para o editor de exibição de código, aqui está a maneira como a ação de **variável decremento** aparece dentro de sua definição de aplicativo lógico, que está no formato JSON.

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

1. Localize e selecione a ação **definir variável** em vez disso.

1. Fornecer o nome da variável e o valor que você deseja atribuir. O novo valor e a variável devem ter o mesmo tipo de dados. O valor é necessário porque essa ação não tem um valor padrão.

Estas são as propriedades para a ação **Definir variável**:

| Propriedade | Obrigatório | Valor |  Descrição |
|----------|----------|-------|--------------|
| **Nome** | Sim | <*nome da variável*> | O nome da variável para alterar |
| **Valor** | Sim | <*novo valor*> | O valor que você quer atribuir à variável. Ambos devem ter o mesmo tipo de dados. |
||||| 

> [!NOTE]
> A menos que você esteja aumentando ou diminuindo variáveis, a alteração de variáveis dentro de loops *pode* criar resultados inesperados, pois os loops são executados em paralelo, ou simultaneamente, por padrão. Para esses casos, tente definir o loop para executar em sequência. Por exemplo, quando você quiser consultar o valor da variável dentro do loop, e espera o mesmo valor no início e no término dessa instância de loop, execute estas etapas para alterar como o loop é executado: 
>
> 1. No canto superior direito do loop, selecione o botão de reticências (**...**) e, em seguida, selecione **configurações**.
> 
> 2. Em **Controle de Simultaneidade**, altere a configuração **Substituir Padrão** para **Ativado**.
>
> 3. Arraste o controle deslizante de **Grau de Paralelismo** para **1**.

Se você alternar do designer para o editor de modo de exibição de código, aqui está a maneira como a ação **definir variável** aparece dentro de sua definição de aplicativo lógico, que está no formato JSON. Este exemplo altera o `Count` valor atual da variável para outro valor.

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

   * **Acrescentar à variável de cadeia de caracteres**
   * **Acrescentar à variável de matriz** 

1. Forneça o valor para acrescentar como o último item na cadeia de caracteres ou matriz. Esse valor é necessário.

Estas são as propriedades para as ações **Acrescentar a...**:

| Propriedade | Obrigatório | Valor |  Descrição |
|----------|----------|-------|--------------|
| **Nome** | Sim | <*nome da variável*> | O nome da variável para alterar |
| **Valor** | Sim | <*acréscimo-valor*> | O valor que você deseja acrescentar, que pode ser de qualquer tipo |
|||||

Se você alternar do designer para o editor de modo de exibição de código, aqui está a maneira como a ação **acrescentar à variável de matriz** aparece dentro de sua definição de aplicativo lógico, que está no formato JSON. Este exemplo cria uma variável de matriz e adiciona outro valor como o último item na matriz. O resultado é uma variável atualizada que contém essa matriz: `[1,2,3,"red"]`

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

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Conectores de Aplicativos Lógicos](../connectors/apis-list.md)
