---
title: Referência de esquema para linguagem de definição de fluxo de trabalho – aplicativos lógicos do Azure
description: Guia de referência para esquema de linguagem de definição de fluxo de trabalho em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 05/13/2019
ms.openlocfilehash: c84791cb30622350b3e6d6356abd4580636c4ddf
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385337"
---
# <a name="schema-reference-for-workflow-definition-language-in-azure-logic-apps"></a>Referência de esquema para linguagem de definição de fluxo de trabalho nos Aplicativos Lógicos do Azure

Quando você cria um aplicativo lógico em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md), seu aplicativo lógico tem uma definição de fluxo de trabalho subjacente que descreve a lógica real que é executada em seu aplicativo lógico. Essa definição de fluxo de trabalho usa [JSON](https://www.json.org/) e segue uma estrutura validada pelo esquema de linguagem de definição de fluxo de trabalho. Essa referência fornece uma visão geral sobre essa estrutura e como o esquema define atributos na sua definição de fluxo de trabalho.

## <a name="workflow-definition-structure"></a>Estrutura da definição de fluxo de trabalho

Uma definição de fluxo de trabalho sempre inclui um gatilho para instanciar seu aplicativo lógico, além de uma ou mais ações executadas depois que o gatilho é acionado.

Esta é a estrutura de alto nível de uma definição de fluxo de trabalho:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Atributo | Necessário | DESCRIÇÃO |
|-----------|----------|-------------|
| `definition` | Sim | O elemento inicial da definição de fluxo de trabalho |
| `$schema` | Somente ao referenciar uma definição de fluxo de trabalho externamente | O local do arquivo de esquema JSON que descreve a versão da Linguagem de Definição de Fluxo de Trabalho, que pode ser encontrado aqui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | Não | As definições para uma ou mais ações a serem executadas no tempo de execução do fluxo de trabalho. Para obter mais informações, consulte [gatilhos e ações](#triggers-actions). <p><p>Máximo de ações: 250 |
| `contentVersion` | Não | O número de versão da definição de fluxo de trabalho, que é "1.0.0.0" por padrão. Para ajudar a identificar e confirmar a definição correta ao implantar um fluxo de trabalho, especifique um valor a ser usado. |
| `outputs` | Não | As definições para as saídas a serem retornadas de uma execução de fluxo de trabalho. Para obter mais informações, consulte [saídas](#outputs). <p><p>Máximo de saídas: 10 |
| `parameters` | Não | As definições para um ou mais parâmetros que passam os valores a serem usados no tempo de execução do aplicativo lógico. Para obter mais informações, consulte [parâmetros](#parameters). <p><p>Máximo de parâmetros: 50 |
| `staticResults` | Não | As definições para um ou mais resultados estáticos retornados por ações como saídas de simulação quando os resultados estáticos são habilitados nessas ações. Em cada definição de ação, `runtimeConfiguration.staticResult.name` o atributo faz referência à definição `staticResults`correspondente dentro de. Para obter mais informações, consulte [resultados estáticos](#static-results). |
| `triggers` | Não | As definições de um ou mais gatilhos que criam uma instância do fluxo de trabalho. É possível definir mais de um gatilho, mas apenas com a Linguagem de Definição de Fluxo de Trabalho, e não visualmente por meio do Designer de Aplicativos Lógicos. Para obter mais informações, consulte [gatilhos e ações](#triggers-actions). <p><p>Máximo da gatilhos: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Gatilhos e ações

Em uma definição de fluxo de trabalho, as seções `triggers` e `actions` definem as chamadas que ocorrem durante a execução do fluxo de trabalho. Para conhecer a sintaxe e obter mais informações sobre essas seções, confira [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parâmetros

O ciclo de vida da implantação geralmente tem ambientes diferentes para desenvolvimento, teste, preparo e produção. Ao implantar aplicativos lógicos em vários ambientes, você provavelmente desejará usar valores diferentes, como cadeias de conexão, com base nas suas necessidades de implantação. Ou você pode ter valores que deseja reutilizar em todo o aplicativo lógico sem codificar ou alterar com frequência. Na `parameters` seção de sua definição de fluxo de trabalho, você pode definir ou editar parâmetros para os valores que seu aplicativo lógico usa no tempo de execução. Você deve definir esses parâmetros primeiro antes de poder referenciar esses parâmetros em outro lugar na sua definição de fluxo de trabalho.

Esta é a estrutura geral de uma definição de parâmetro:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*nome do parâmetro*> | Sim | Cadeia | O nome do parâmetro que você deseja definir |
| <*parameter-type*> | Sim | int, float, String, bool, array, Object, SecureString, secureobject <p><p>**Observação**: Para todas as senhas, chaves e segredos, use os `securestring` tipos `secureobject` ou porque a `GET` operação não retorna esses tipos. Para obter mais informações sobre como proteger os parâmetros, consulte [recomendações de segurança para parâmetros de ação e de entrada](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters). | O tipo do parâmetro |
| <*valor de parâmetro-padrão*> | Sim | O mesmo que `type` | O valor de parâmetro padrão a ser usado se nenhum valor for especificado quando o fluxo de trabalho for instanciado. O `defaultValue` atributo é necessário para que o designer do aplicativo lógico possa mostrar corretamente o parâmetro, mas você pode especificar um valor vazio. |
| <*matriz com-valores-de-parâmetro-permitido*> | Não | Array | Uma matriz com valores que o parâmetro pode aceitar |
| <*parâmetro-Descrição*> | Não | Objeto JSON | Quaisquer outros detalhes de parâmetro, como uma descrição para o parâmetro |
||||

Em seguida, crie um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para sua definição de fluxo de trabalho, defina os parâmetros de modelo que aceitam os valores desejados na implantação, substitua os valores codificados por referências aos parâmetros de definição de modelo ou de fluxo de trabalho como apropriado, e armazene os valores a serem usados na implantação em um [arquivo de parâmetro](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)separado. Dessa forma, você pode alterar esses valores mais facilmente por meio do arquivo de parâmetro sem precisar atualizar e reimplantar seu aplicativo lógico. Para obter informações confidenciais ou que devem ser protegidas, como nomes de usuários, senhas e segredos, você pode armazenar esses valores em Azure Key Vault e fazer com que o arquivo de parâmetros recupere esses valores do cofre de chaves. Para obter mais informações e exemplos sobre como definir parâmetros nos níveis de definição do modelo e [do fluxo de trabalho, consulte Visão geral: Automatize a implantação de aplicativos lógicos com modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Azure Resource Manager.

<a name="static-results"></a>

## <a name="static-results"></a>Resultados estáticos

No atributo, defina a simulação `outputs` de uma ação e `status` a ação retornará quando a configuração de resultado estático da ação estiver ativada. `staticResults` Na definição da ação, o `runtimeConfiguration.staticResult.name` atributo faz referência ao nome da definição de resultado estática dentro. `staticResults` Saiba como você pode [testar aplicativos lógicos com dados fictícios Configurando resultados estáticos](../logic-apps/test-logic-apps-mock-data-static-results.md).

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*static-Result-Definition-Name*> | Sim | String | O nome de uma definição de resultado estático que uma definição de ação pode referenciar por meio de um `runtimeConfiguration.staticResult` objeto. Para obter mais informações, consulte [Configurações de tempo de execução](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>Você pode usar qualquer nome exclusivo que desejar. Por padrão, esse nome exclusivo é acrescentado com um número, que é incrementado conforme necessário. |
| <*output-attributes-and-values-returned*> | Sim | Varia | Os requisitos para esses atributos variam de acordo com as diferentes condições. Por exemplo, quando o `status` é `Succeeded`, o `outputs` atributo inclui atributos e valores retornados como saídas de imitação pela ação. `status` `message` Se for `errors` , o `outputs` atributo incluirá o atributo, que é uma matriz com um ou mais objetos de erro que têm informações de erro. `Failed` |
| <*header-values*> | Não | JSON | Todos os valores de cabeçalho retornados pela ação |
| <*status-code-returned*> | Sim | String | O código de status retornado pela ação |
| <*action-status*> | Sim | String | O status da ação, por exemplo, `Succeeded` ou`Failed` |
|||||

Por exemplo, nessa definição de ação http, o `runtimeConfiguration.staticResult.name` atributo faz `HTTP0` referência dentro `staticResults` do atributo onde as saídas de simulação para a ação são definidas. O `runtimeConfiguration.staticResult.staticResultOptions` atributo especifica que a configuração de resultado estático `Enabled` está na ação http.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

A ação http retorna as saídas na definição `HTTP0` dentro `staticResults`de. Neste exemplo, para o código de status, a saída de simulação `OK`é. Para valores de cabeçalho, a saída de `"Content-Type": "application/JSON"`simulação é. Para o status da ação, a saída da simulação `Succeeded`é.

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

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

| Valor JSON | Resultado |
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

| Expressão JSON | Resultado |
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

<a name="outputs"></a>

## <a name="outputs"></a>Saídas

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

| Atributo | Necessário | Tipo | Descrição |
|-----------|----------|------|-------------|
| <*key-name*> | Sim | String | O nome da chave do valor retornado da saída |
| <*key-type*> | Sim | int, float, string, securestring, bool, array, objeto JSON | O tipo do valor retornado da saída |
| <*key-value*> | Sim | Mesmo que <*key-type*> | O valor retornado da saída |
|||||

Para obter a saída de uma execução de fluxo de trabalho, examine o histórico de execução do aplicativo lógico e os detalhes no portal do Azure ou use a [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Você também pode passar a saída para sistemas externos, por exemplo, o Power BI para que você possa criar painéis.

<a name="operators"></a>

## <a name="operators"></a>Operadores

No caso de [expressões](#expressions) e [funções](#functions), os operadores desempenham tarefas específicas, como referenciar uma propriedade ou um valor em uma matriz.

| Operator | Tarefa |
|----------|------|
| ' | Para usar um literal de cadeia de caracteres como entrada ou em expressões e funções, encapsule a cadeia de caracteres somente com aspas simples, por exemplo, `'<myString>'`. Não use aspas duplas (""), que entram em conflito com a formatação JSON ao redor de uma expressão inteira. Por exemplo: <p>**Sim**: length('Hello') </br>**Não**: length("Hello") <p>Quando passa matrizes ou números, você não precisa de pontuação de encapsulamento. Por exemplo: <p>**Sim**: length([1, 2, 3]) </br>**Não**: length("[1, 2, 3]") |
| [] | Para referenciar um valor em uma posição específica (índice) em uma matriz, use colchetes. Por exemplo, para obter o segundo item de uma matriz: <p>`myArray[1]` |
| . | Para referenciar uma propriedade em um objeto, use o operador de ponto. Por exemplo, para obter a propriedade `name` para um objeto JSON `customer`: <p>`"@parameters('customer').name"` |
| ? | Para referenciar propriedades nulas em um objeto sem erro de tempo de execução, use o operador de ponto de interrogação. Por exemplo, você pode usar esta expressão para tratar as saídas nulas de um gatilho: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funções

Algumas expressões obtêm seus valores de ações de tempo de execução que talvez ainda não existam quando sua definição de fluxo de trabalho começa a ser executada. Para referenciar ou trabalhar com esses valores em expressões, você pode usar as [*funções*](../logic-apps/workflow-definition-language-functions-reference.md) fornecidas pela linguagem de definição de fluxo de trabalho.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Workflow Definition Language actions and triggers](../logic-apps/logic-apps-workflow-actions-triggers.md) (Ações e gatilhos da Linguagem de Definição de Fluxo de Trabalho)
* Saiba mais sobre como criar e gerenciar Aplicativos Lógicos de forma programática com a [API REST de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
