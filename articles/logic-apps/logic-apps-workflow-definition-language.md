---
title: Referência de esquema para a linguagem de definição de fluxo de trabalho - aplicativos lógicos do Azure
description: Guia de referência para o esquema de linguagem de definição de fluxo de trabalho em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 04/30/2018
ms.openlocfilehash: d80ffa862546f56e93a338a7a1db031e2cb55990
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60845726"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referência de esquema para linguagem de definição de fluxo de trabalho nos Aplicativos Lógicos do Azure

Quando você cria um aplicativo lógico no [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), seu aplicativo lógico tem uma definição de fluxo de trabalho subjacente que descreve a lógica real que é executado em seu aplicativo lógico. Essa definição de fluxo de trabalho usa [JSON](https://www.json.org/) e segue uma estrutura que é validada pelo esquema de linguagem de definição de fluxo de trabalho. Esta referência fornece uma visão geral sobre essa estrutura e como o esquema define os elementos em sua definição de fluxo de trabalho.

## <a name="workflow-definition-structure"></a>Estrutura da definição de fluxo de trabalho

Uma definição de fluxo de trabalho sempre inclui um gatilho para criar uma instância de seu aplicativo lógico, além de uma ou mais ações que são executados após o gatilho é acionado.

Esta é a estrutura de alto nível de uma definição de fluxo de trabalho:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "contentVersion": "<workflow-definition-version-number>",
  "parameters": { "<workflow-parameter-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" },
  "actions": { "<workflow-action-definitions>" },
  "outputs": { "<workflow-output-definitions>" }
}
```

| Elemento | Obrigatório | DESCRIÇÃO |
|---------|----------|-------------|
| definição | Sim | O elemento inicial da definição de fluxo de trabalho |
| $schema | Somente ao referenciar uma definição de fluxo de trabalho externamente | O local do arquivo de esquema JSON que descreve a versão da Linguagem de Definição de Fluxo de Trabalho, que pode ser encontrado aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| contentVersion | Não  | O número de versão da definição de fluxo de trabalho, que é "1.0.0.0" por padrão. Para ajudar a identificar e confirmar a definição correta ao implantar um fluxo de trabalho, especifique um valor a ser usado. |
| parameters | Não  | As definições de um ou mais parâmetros que passam dados para o fluxo de trabalho <p><p>Máximo de parâmetros: 50 |
| gatilhos | Não  | As definições de um ou mais gatilhos que criam uma instância do fluxo de trabalho. É possível definir mais de um gatilho, mas apenas com a Linguagem de Definição de Fluxo de Trabalho, e não visualmente por meio do Designer de Aplicativos Lógicos. <p><p>Máximo da gatilhos: 10 |
| Ações | Não  | As definições de uma ou mais ações a serem executadas no tempo de execução do fluxo de trabalho <p><p>Máximo de ações: 250 |
| outputs | Não  | As definições das saídas retornadas de uma execução do fluxo de trabalho <p><p>Máximo de saídas: 10 |
||||

## <a name="parameters"></a>parâmetros

No `parameters` seção, defina todos os parâmetros de fluxo de trabalho que sua definição de fluxo de trabalho usa na implantação para aceitar entradas. Declarações de parâmetro e valores de parâmetros são necessários na implantação. Antes de usar esses parâmetros em outras seções do fluxo de trabalho, não deixe de declarar todos os parâmetros nessas seções. 

Esta é a estrutura geral de uma definição de parâmetro:

```json
"parameters": {
  "<parameter-name>": {
    "type": "<parameter-type>",
    "defaultValue": "<default-parameter-value>",
    "allowedValues": [ <array-with-permitted-parameter-values> ],
    "metadata": {
      "key": {
        "name": "<key-value>"
      }
    }
  }
},
```

| Elemento | Obrigatório | Type | DESCRIÇÃO |
|---------|----------|------|-------------|
| Tipo | Sim | int, float, string, securestring, bool, array, objeto JSON, secureobject <p><p>**Observação**: Para todas as senhas, chaves e segredos, use os tipos `securestring` e `secureobject`, porque a operação `GET` não retorna esses tipos. Para obter mais informações sobre como proteger parâmetros, consulte [proteger seu aplicativo lógico](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | O tipo do parâmetro |
| defaultValue | Sim | O mesmo que `type` | O valor de parâmetro padrão quando nenhum valor é especificado ao criar uma instância do fluxo de trabalho |
| allowedValues | Não  | O mesmo que `type` | Uma matriz com valores que o parâmetro pode aceitar |
| metadata | Não  | Objeto JSON | Quaisquer outros detalhes de parâmetros, por exemplo, o nome ou uma descrição legível para seu aplicativo lógico, fluxo ou os dados de tempo de design usados pelo Visual Studio ou outras ferramentas |
||||

## <a name="triggers-and-actions"></a>Gatilhos e ações

Em uma definição de fluxo de trabalho, as seções `triggers` e `actions` definem as chamadas que ocorrem durante a execução do fluxo de trabalho. Para conhecer a sintaxe e obter mais informações sobre essas seções, confira [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

## <a name="outputs"></a>outputs

Na seção `outputs`, defina os dados que o fluxo de trabalho pode retornar quando terminar sua execução. Por exemplo, para rastrear um valor ou status específico em cada execução, especifique que a saída do fluxo de trabalho retorne esses dados.

> [!NOTE]
> Ao responder a solicitações de entrada da API REST de um serviço, não use `outputs`. Em vez disso, use o tipo de ação `Response`. Para obter mais informações, consulte [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

Esta é a estrutura geral de uma definição de saída:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Elemento | Obrigatório | Type | DESCRIÇÃO |
|---------|----------|------|-------------|
| <*key-name*> | Sim | Cadeia de caracteres | O nome da chave do valor retornado da saída |
| Tipo | Sim | int, float, string, securestring, bool, array, objeto JSON | O tipo do valor retornado da saída |
| value | Sim | O mesmo que `type` | O valor retornado da saída |
|||||

Para obter a saída de um fluxo de trabalho executar, examine o histórico de execução e os detalhes no portal do Azure do seu aplicativo lógico ou usar o [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Você também pode passar a saída para sistemas externos, por exemplo, o Power BI para que você possa criar painéis.

<a name="expressions"></a>

## <a name="expressions"></a>Expressões

Com JSON, é possível ter valores literais existentes no tempo de design, por exemplo:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

Também é possível ter valores que não existem até o tempo de execução. Para representar esses valores, é possível usar *expressões*, que são avaliadas em tempo de execução. Uma expressão é uma sequência que pode conter uma ou mais [funções](#functions), [operadores](#operators), constantes, valores explícitos ou variáveis. Na definição de fluxo de trabalho, é possível usar uma expressão em qualquer lugar em um valor de cadeia de caracteres JSON, prefixando a expressão com o sinal de arroba (\@). Ao avaliar uma expressão que representa um valor JSON, o corpo da expressão é extraído removendo o caractere \@ e sempre resultará em outro valor JSON.

Por exemplo, para a propriedade `customerName` definida anteriormente, você pode obter o valor da propriedade usando a função [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) em uma expressão de função e pode atribuir esse valor à propriedade `accountName`:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

A *interpolação de cadeia de caracteres* também permite usar várias expressões dentro de cadeias de caracteres que são encapsuladas pelo caractere \@ e por chaves ({}). Esta é a sintaxe:

```json
@{ "<expression1>", "<expression2>" }
```

O resultado sempre é uma cadeia de caracteres, tornando essa funcionalidade semelhante à função `concat()`, por exemplo: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

Se você tiver uma cadeia de caracteres literal que inicia com o caractere \@, prefixe o caractere \@ com outro caractere \@ como um caractere de escape: \@\@

Estes exemplos mostram como as expressões são avaliadas:

| Valor JSON | Result |
|------------|--------|
| "Sophia Owen" | Retorna estes caracteres: 'Sophia Owen' |
| "array[1]" | Retornar estes caracteres: 'array[1]' |
| "\@\@" | Retornar esses caracteres como uma cadeia de caracteres com um caractere: '\@' |
| " \@" | Retornar esses caracteres como uma cadeia de caracteres com dois caracteres: ' \@' |
|||

Para esses exemplos, suponha que você defina "myBirthMonth" como "January" e "myAge" igual ao número 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Estes exemplos mostram como as expressões a seguir são avaliadas:

| Expressão JSON | Result |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Retorna esta cadeia de caracteres: "January" |
| "\@{parameters('myBirthMonth')}" | Retorna esta cadeia de caracteres: "January" |
| "\@parameters('myAge')" | Retorna este número: 42 |
| "\@{parameters('myAge')}" | Retorna este número como uma cadeia de caracteres: "42" |
| "My age is \@{parameters('myAge')}" | Retorna esta cadeia de caracteres: "My age is 42" |
| "\@concat('My age is ', string(parameters('myAge')))" | Retorna esta cadeia de caracteres: "My age is 42" |
| "My age is \@\@{parameters('myAge')}" | Retorna esta cadeia de caracteres, que inclui a expressão: "My age is \@{parameters('myAge')}` |
|||

Quando está trabalhando visualmente no Designer de Aplicativos Lógicos, você pode criar expressões usando o Construtor de Expressões, por exemplo:

![Designer de Aplicativos Lógicos > Construtor de Expressões](./media/logic-apps-workflow-definition-language/expression-builder.png)

Quando você terminar, a expressão será exibida para a propriedade correspondente em sua definição de fluxo de trabalho, por exemplo, a propriedade `searchQuery` aqui:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="operators"></a>

## <a name="operators"></a>Operadores

No caso de [expressões](#expressions) e [funções](#functions), os operadores desempenham tarefas específicas, como referenciar uma propriedade ou um valor em uma matriz.

| Operador | Tarefa |
|----------|------|
| ' | Para usar um literal de cadeia de caracteres como entrada ou em expressões e funções, encapsule a cadeia de caracteres somente com aspas simples, por exemplo, `'<myString>'`. Não use aspas duplas (""), que entram em conflito com a formatação JSON ao redor de uma expressão inteira. Por exemplo:  <p>**Sim**: length('Hello') </br>**Não**: length("Hello") <p>Quando passa matrizes ou números, você não precisa de pontuação de encapsulamento. Por exemplo:  <p>**Sim**: length([1, 2, 3]) </br>**Não**: length("[1, 2, 3]") |
| [] | Para referenciar um valor em uma posição específica (índice) em uma matriz, use colchetes. Por exemplo, para obter o segundo item de uma matriz: <p>`myArray[1]` |
| . | Para referenciar uma propriedade em um objeto, use o operador de ponto. Por exemplo, para obter a propriedade `name` para um objeto JSON `customer`: <p>`"@parameters('customer').name"` |
| ? | Para referenciar propriedades nulas em um objeto sem erro de tempo de execução, use o operador de ponto de interrogação. Por exemplo, você pode usar esta expressão para tratar as saídas nulas de um gatilho: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões obtêm seus valores de ações de tempo de execução que não podem existir ainda quando sua definição de fluxo de trabalho começa a ser executado. Para referenciar ou trabalhar com esses valores em expressões, você pode usar as [*funções*](../logic-apps/workflow-definition-language-functions-reference.md) fornecidas pela linguagem de definição de fluxo de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Workflow Definition Language actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md) (Ações e gatilhos da Linguagem de Definição de Fluxo de Trabalho)
* Saiba mais sobre como criar e gerenciar Aplicativos Lógicos de forma programática com a [API REST de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
