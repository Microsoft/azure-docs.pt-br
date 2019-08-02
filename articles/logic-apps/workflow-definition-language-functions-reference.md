---
title: Referência para funções na linguagem de definição de fluxo de trabalho – aplicativos lógicos do Azure e Microsoft Flow
description: Guia de referência para funções em expressões criadas com a linguagem de definição de fluxo de trabalho para aplicativos lógicos do Azure e Microsoft Flow
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: reference
ms.date: 08/15/2018
ms.openlocfilehash: 8ad7d7497a0fbe6fcf20ad5b50ef4e6cac9ed51f
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868896"
---
# <a name="functions-reference-for-workflow-definition-language-in-azure-logic-apps-and-microsoft-flow"></a>Referência de funções para linguagem de definição de fluxo de trabalho em aplicativos lógicos do Azure e Microsoft Flow

Para definições de fluxo de trabalho em [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e [Microsoft Flow](https://docs.microsoft.com/flow/getting-started), algumas [expressões](../logic-apps/logic-apps-workflow-definition-language.md#expressions) obtêm seus valores de ações de tempo de execução que talvez ainda não existam quando o fluxo de trabalho começa a ser executado. Para fazer referência a esses valores ou processar os valores nessas expressões, você pode usar *funções* fornecidas pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md). 

> [!NOTE]
> Essa página de referência se aplica aos aplicativos lógicos do Azure e Microsoft Flow, mas aparece na documentação dos aplicativos lógicos do Azure. Embora essa página se refira especificamente aos aplicativos lógicos, essas funções funcionam tanto para os fluxos quanto para os aplicativos lógicos. Para obter mais informações sobre funções e expressões no Microsoft Flow, consulte [usar expressões em condições](https://docs.microsoft.com/flow/use-expressions-in-conditions).

Por exemplo, você pode calcular valores usando funções matemáticas, como a [função Add ()](../logic-apps/workflow-definition-language-functions-reference.md#add), quando você deseja a soma de inteiros ou floats. Aqui estão algumas outras tarefas de exemplo que você pode executar com o Functions:

| Tarefa | Sintaxe da função | Resultado |
| ---- | --------------- | ------ |
| Retornar uma cadeia de caracteres em letras minúsculas. | toLower('<*texto*>') <p>Por exemplo: toLower('Hello') | "hello" |
| Retornar um GUID (identificador global exclusivo). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Para localizar funções [com base em sua finalidade geral](#ordered-by-purpose), examine as tabelas a seguir. Ou, para obter informações detalhadas sobre cada função, consulte a [lista alfabética](#alphabetical-list).

> [!NOTE]
> Na sintaxe das definições de parâmetro, um ponto de interrogação (?) exibido após um parâmetro significa que ele é opcional.
> Por exemplo, consulte [getFutureTime()](#getFutureTime).

## <a name="functions-in-expressions"></a>Funções em expressões

Para mostrar como usar uma função em uma expressão, este exemplo demonstra como é possível obter o valor do `customerName` parâmetro e atribuir esse valor à propriedade `accountName` usando a função [parameters()](#parameters) em uma expressão:

```json
"accountName": "@parameters('customerName')"
```

Estas são algumas outras maneiras de usar funções em expressões:

| Tarefa | Sintaxe da função em uma expressão |
| ---- | -------------------------------- |
| Executar o trabalho com um item passando esse item para uma função. | "\@<*functionName*>(<*item*>)" |
| 1. Obter o valor de *parameterName* usando a função `parameters()` aninhada. </br>2. Executar o trabalho com o resultado passando esse valor para *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Obter o resultado da função interna aninhada *functionName*. </br>2. Passar o resultado para a função externa *functionName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1. Obter o resultado de *functionName*. </br>2. Dado que o resultado é um objeto com a propriedade *propertyName*, obter o valor dessa propriedade. | "\@<*functionName*>(<*item*>).<*propertyName*>" |
|||

Por exemplo, a função `concat()` pode usar dois ou mais valores de cadeia de caracteres como parâmetros. Essa função combina essas cadeias de caracteres em uma única cadeia de caracteres.
Você pode transmitir literais de cadeia de caracteres, por exemplo, "Sophia" e "Owen", para obter uma cadeia de caracteres combinada, "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

Ou pode obter valores de cadeia de caracteres usando parâmetros. Este exemplo usa a função `parameters()` em cada parâmetro `concat()` e os parâmetros `firstName` e `lastName`. Em seguida, passe as cadeias de caracteres resultantes para a função `concat()` para obter uma cadeia de caracteres combinada, por exemplo, "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

De qualquer forma, os dois exemplos atribuem o resultado à propriedade `customerName`.

Aqui são apresentadas as funções disponíveis ordenadas pelo uso geral ou é possível navegar pelas funções com base na [ordem alfabética](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funções de cadeia de caracteres

Para trabalhar com cadeias de caracteres, você pode usar essas funções de cadeia de caracteres e algumas [funções de coleta](#collection-functions).
Funções de cadeia de caracteres só funcionam com cadeias de caracteres.

| Função de cadeia de caracteres | Tarefa |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combinar duas ou mais cadeias de caracteres e retornar a cadeia de caracteres combinada. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verificar se uma cadeia de caracteres termina com a subcadeia de caracteres especificada. |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Gerar um GUID (identificador global exclusivo) como uma cadeia de caracteres. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Retornar a posição inicial de uma subcadeia de caracteres. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Retorna a posição inicial da última ocorrência de uma subcadeia de caracteres. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Substituir uma subcadeia de caracteres pela cadeia de caracteres especificada e retornar a cadeia de caracteres atualizada. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Retorna uma matriz contendo subcadeias de caracteres, separadas por vírgulas, de uma cadeia de caracteres maior baseada em um caractere delimitador especificado na cadeia de caracteres original. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verificar se uma cadeia de caracteres começa com uma subcadeia de caracteres específica. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Retornar caracteres de uma cadeia de caracteres, começando na posição especificada. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Retornar uma cadeia de caracteres em letras minúsculas. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Retornar uma cadeia de caracteres em letras maiúsculas. |
| [trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, matrizes em geral, cadeias de caracteres e, às vezes, dicionários, você pode usar estas funções de coleção.

| Função de coleção | Tarefa |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verificar se uma coleção tem um item específico. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifique se uma coleção está vazia. |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Retornar o primeiro item de uma coleção. |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Retornar uma coleção que tem *somente* os itens comuns entre as coleções especificadas. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando está dentro de uma ação repetida em uma matriz, retornar o item atual na matriz durante a iteração atual da ação. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Retornar uma cadeia de caracteres que tem *todos* os itens de uma matriz, separados pelo caractere especificado. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Retornar o último item de uma coleção. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Retornar o número de itens em uma cadeia de caracteres ou matriz. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Remover itens do início de uma coleção e retornar *todos os outros* itens. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Retornar itens do início de uma coleção. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Retornar uma coleção que tem *todos* os itens das coleções especificadas. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funções de comparação lógica

Para trabalhar com condições, comparar valores e resultados de expressão ou avaliar vários tipos de lógica, é possível usar essas funções de comparação lógica.
Para a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verificar se todas as expressões são verdadeiras. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verificar se os dois valores são equivalentes. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verificar se o primeiro valor é maior que o segundo valor. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verificar se o primeiro valor é superior ou igual ao segundo. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verificar se uma expressão é verdadeira ou falsa. Com base no resultado, retornar um valor especificado. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verificar se o primeiro valor é menor que o segundo valor. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verificar se o primeiro valor é inferior ou igual ao segundo valor. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verificar se uma expressão é falsa. |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verificar se pelo menos uma expressão é verdadeira. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funções de conversão

Para alterar o tipo ou o formato de um valor, você pode usar estas funções de conversão.
Por exemplo, é possível alterar um valor de booliano para inteiro.
Para obter mais informações sobre como os aplicativos lógicos tratam tipos de conteúdo durante a conversão, consulte [manipular tipos de conteúdo](../logic-apps/logic-apps-content-type.md).
Para a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Retornar uma matriz de uma única entrada especificada. Para várias entradas, confira [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Retornar a versão codificada em Base64 de uma cadeia de caracteres. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Retornar a versão binária de uma cadeia de caracteres codificada em Base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em Base64. |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Retornar a versão binária de um valor de entrada. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Retornar a versão booliana de um valor de entrada. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Retornar uma matriz de várias entradas. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Retornar o URI de dados de um valor de entrada. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Retornar a versão binária de um URI de dados. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Retornar a versão de cadeia de caracteres de um URI de dados. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em Base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Retornar a versão binária de um URI de dados. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres de escape por versões decodificadas. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres não seguros para URL por caracteres de escape. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Retornar um número de ponto flutuante correspondente a um valor de entrada. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Retornar a versão em inteiros de uma cadeia de caracteres. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Retornar o objeto ou valor de tipo JSON (JavaScript Object Notation) correspondente a uma cadeia de caracteres ou XML. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Retornar a versão de cadeia de caracteres de um valor de entrada. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Retornar a versão codificada para URI correspondente a um valor de entrada substituindo caracteres não seguros para URL por caracteres de escape. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Retornar a versão binária de uma cadeia de caracteres codificada como URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Retornar a versão de cadeia de caracteres correspondente a uma cadeia de caracteres codificada como URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Retornar a versão XML de uma cadeia de caracteres. |
|||

<a name="math-functions"></a>

## <a name="math-functions"></a>Funções matemáticas

Para trabalhar com números inteiros e floats, você pode usar estas funções matemáticas.
Para a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função matemática | Tarefa |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Retornar o resultado da adição de dois números. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Retornar o resultado da divisão de dois números. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Retornar o valor mais alto de um conjunto de números em uma matriz. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Retornar o valor mais baixo de um conjunto de números ou de uma matriz. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Retornar o resto da divisão de dois números. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Retornar o produto da multiplicação de dois números. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Retornar um número inteiro aleatório de um intervalo especificado. |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Retornar uma matriz de inteiros que começa em um inteiro especificado. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Retornar o resultado da subtração do segundo número do primeiro. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funções de data e hora

Para trabalhar com datas e horas, você pode usar estas funções de data e hora.
Para a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Adicionar um número de dias a um carimbo de data/hora. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Adicionar um número de horas a um carimbo de data/hora. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Adicionar um número de minutos a um carimbo de data/hora. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Adicionar um número de segundos a um carimbo de data/hora. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Adicionar um número de unidades de tempo a um carimbo de data/hora. Confira também [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converter um carimbo de data/hora de UTC (Tempo Universal Coordenado) no fuso horário de destino. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converter um carimbo de data/hora do fuso horário de origem no fuso horário de destino. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converter um carimbo de data/hora do fuso horário de origem em UTC (Tempo Universal Coordenado). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Retornar o componente de dia do mês de um carimbo de data/hora. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Retornar o componente de dia da semana de um carimbo de data/hora. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Retornar o componente de dia do ano de um carimbo de data/hora. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Retornar a data de um carimbo de data/hora. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. Confira também [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Retornar o carimbo de data/hora atual menos as unidades de tempo especificadas. Confira também [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Retornar o início do dia de um carimbo de data/hora. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Retornar o início da hora de um carimbo de data/hora. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Retornar o início do mês de um carimbo de data/hora. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Subtrair um número de unidades de tempo de um carimbo de data/hora. Confira também [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Retornar o valor de propriedade `ticks` de um carimbo de data/hora especificado. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Retornar o carimbo de data/hora atual como uma cadeia de caracteres. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funções de fluxo de trabalho

Essas funções de fluxo de trabalho podem ajudar você a:

* Obter detalhes sobre uma instância de fluxo de trabalho em tempo de execução.
* Trabalhe com as entradas usadas para criar uma instância de aplicativos lógicos ou fluxos.
* Referenciar as saídas de gatilhos e ações.

Por exemplo, você pode referenciar as saídas de uma ação e usar esses dados em uma ação posterior.
Para a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de fluxo de trabalho | Tarefa |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Retornar a saída da ação atual em tempo de execução ou os valores de outros pares de nome e valor JSON. Confira também [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Retornar a saída `body` de uma ação em tempo de execução. Confira também [body](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Retornar a saída de uma ação em tempo de execução. Confira [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Retornar a saída de uma ação em tempo de execução ou os valores de outros pares de nome e valor JSON. Confira também [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [body](#body) | Retornar a saída `body` de uma ação em tempo de execução. Confira também [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Criar uma matriz com os valores correspondentes a um nome de chave nas saídas da ação *form-data* ou *form-encoded*. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Retornar um único valor correspondente a um nome de chave na saída *form-data* ou *form-encoded* de uma ação. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando está dentro de uma ação repetida em uma matriz, retornar o item atual na matriz durante a iteração atual da ação. |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando dentro de um loop foreach ou until, retorna o item atual do loop especificado.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Quando dentro de um loop Until, retorna o valor de índice para a iteração atual. Você pode usar essa função dentro de loops aninhados until. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Retornar a "URL de retorno de chamada" que chama um gatilho ou uma ação. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Retornar o corpo de uma parte específica na saída de uma ação que tem várias partes. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Retorne o valor para um parâmetro descrito em sua definição de fluxo de trabalho. |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Retornar a saída de um gatilho em tempo de execução ou de outros pares de nome e valor JSON. Confira também [triggerOutputs](#triggerOutputs) e [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Retornar a saída `body` de um gatilho em tempo de execução. Confira [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Retornar um único valor correspondente a um nome de chave nas saídas dos gatilhos *form-data* ou *form-encoded*. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Retornar o corpo de uma parte específica na saída de várias partes de um gatilho. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Criar uma matriz com valores correspondentes a um nome de chave nas saídas de gatilho *form-data* ou *form-encoded*. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Retornar a saída de um gatilho em tempo de execução ou os valores de outros pares de nome e valor JSON. Confira [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [variáveis](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Retornar o valor de uma variável especificada. |
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Retornar todos os detalhes sobre o próprio fluxo de trabalho durante o tempo de execução. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funções de análise de URI

Para trabalhar com URIs (Uniform Resource Identifier) e obter vários valores das propriedades desses URIs, você pode usar estas funções de análise de URI.
Para a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de análise de URI | Tarefa |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Retornar o valor `host` de um URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Retornar o valor `path` de um URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Retornar os valores `path` e `query` de um URI (Uniform Resource Identifier). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Retornar o valor `port` de um URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Retornar o valor `query` de um URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Retornar o valor `scheme` de um URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funções de manipulação: JSON & XML

Para trabalhar com objetos JSON e nós XML, você pode usar estas funções de manipulação.
Para a referência completa sobre cada função, consulte a [lista alfabética](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Função de manipulação | Tarefa |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Adicionar uma propriedade e seu valor, ou par nome-valor, a um objeto JSON e retornar o objeto atualizado. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Retornar o primeiro valor não nulo de um ou mais parâmetros. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Remover uma propriedade de um objeto JSON e retornar o objeto atualizado. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Definir o valor da propriedade de um objeto JSON e retornar o objeto atualizado. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Verificar o XML em busca de nós ou valores correspondentes a uma expressão XPath (Linguagem XPath) e retornar esses nós ou valores. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Todas as funções – lista alfabética

Esta seção lista todas as funções disponíveis em ordem alfabética.

<a name="action"></a>

### <a name="action"></a>action

Retorne a saída da ação *atual* em tempo de execução ou os valores de outros pares de nome e valor JSON, que podem ser atribuídos a uma expressão.
Por padrão, essa função referencia todo o objeto de ação, mas você pode especificar uma propriedade cujo valor você deseja.
Consulte também [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

É possível usar a função `action()` apenas nestes lugares:

* A propriedade `unsubscribe` de uma ação de webhook para que você possa acessar o resultado da solicitação `subscribe` original
* A propriedade `trackedProperties` de uma ação
* A condição de loop `do-until` de uma ação

```
action()
action().outputs.body.<property>
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*property*> | Não | Cadeia de caracteres | O nome da propriedade do objeto de ação cuo valor você deseja: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** e **clientTrackingId**. No portal do Azure, é possível localizar essas propriedades revisando os detalhes de um histórico de execuções específico. Para obter mias informações, consulte [REST API – Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get) (API REST – Ações de execução de fluxo de trabalho). |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | -----| ----------- |
| <*action-output*> | Cadeia de caracteres | A saída da ação ou da propriedade atual |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Retornar a saída `body` de uma ação em tempo de execução.
Abreviação de `actions('<actionName>').outputs.body`.
Consulte [body()](#body) e [actions()](#actions).

```
actionBody('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | Cadeia de caracteres | O nome da saída `body` da ação desejada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | -----| ----------- |
| <*action-body-output*> | Cadeia de caracteres | A saída `body` da ação especificada |
||||

*Exemplo*

Esse exemplo obtém a saída `body` da ação de Twitter `Get user`:

```
actionBody('Get_user')
```

E retorna este resultado:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Retornar a saída de uma ação em tempo de execução.
Abreviação de `actions('<actionName>').outputs`.
Consulte [actions()](#actions).

```
actionOutputs('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | Cadeia de caracteres | O nome da saída da ação desejada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | -----| ----------- |
| <*output*> | Cadeia de caracteres | A saída da ação especificada |
||||

*Exemplo*

Esse exemplo obtém a saída da ação de Twitter `Get user`:

```
actionOutputs('Get_user')
```

E retorna este resultado:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>Ações

Retornar a saída da ação em tempo de execução ou os valores de outros pares de nome e valor JSON, que podem ser atribuídos a uma expressão. Por padrão, a função referencia todo o objeto de ação, mas você pode especificar uma propriedade cujo valor você deseja.
Para versões abreviadas, consulte [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) e [body()](#body).
Para a ação atual, consulte [action()](#action).

> [!NOTE]
> Anteriormente, era possível usar a função `actions()` ou o elemento `conditions` ao especificar que uma ação foi executada com base na saída de outra ação. No entanto, para declarar explicitamente dependências entre ações, agora é necessário usar a propriedade `runAfter` da ação dependente.
> Para saber mais sobre a propriedade `runAfter`, confira [Detectar e lidar com falhas com a propriedade runAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | Cadeia de caracteres | O nome do objeto de ação cuja saída você deseja  |
| <*property*> | Não | Cadeia de caracteres | O nome da propriedade do objeto de ação cuo valor você deseja: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** e **clientTrackingId**. No portal do Azure, é possível localizar essas propriedades revisando os detalhes de um histórico de execuções específico. Para obter mias informações, consulte [REST API – Workflow Run Actions](https://docs.microsoft.com/rest/api/logic/workflowrunactions/get) (API REST – Ações de execução de fluxo de trabalho). |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | -----| ----------- |
| <*action-output*> | Cadeia de caracteres | A saída da ação ou propriedade especificada |
||||

*Exemplo*

Esse exemplo obtém o valor de propriedade `status` da ação de Twitter `Get user` em tempo de execução:

```
actions('Get_user').outputs.body.status
```

E retorna este resultado: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>Adicionar

Retornar o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Sim | Integer, Float ou misto | Os números a serem adicionados |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | -----| ----------- |
| <*result-sum*> | Integer ou Float | O resultado da adição dos números especificados |
||||

*Exemplo*

Esse exemplo adiciona os números especificados:

```
add(1, 1.5)
```

E retorna este resultado: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Adicionar um número de dias a um carimbo de data/hora.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*days*> | Sim | Inteiro | O número positivo ou negativo de dias a ser adicionado |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora mais o número de dias especificado  |
||||

*Exemplo 1*

Esse exemplo adiciona 10 dias ao carimbo de data/hora especificado:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E retorna este resultado: `"2018-03-25T00:00:0000000Z"`

*Exemplo 2*

Esse exemplo subtrai cinco dias do carimbo de data/hora especificado:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E retorna este resultado: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Adicionar um número de horas a um carimbo de data/hora.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*hours*> | Sim | Inteiro | O número positivo ou negativo de horas a ser adicionado |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora mais o número de horas especificado  |
||||

*Exemplo 1*

Esse exemplo adiciona 10 horas ao carimbo de data/hora especificado:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E retorna este resultado: `"2018-03-15T10:00:0000000Z"`

*Exemplo 2*

Esse exemplo subtrai cinco horas do carimbo de data/hora especificado:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E retorna este resultado: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Adicionar um número de minutos a um carimbo de data/hora.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*minutes*> | Sim | Inteiro | O número positivo ou negativo de minutos a ser adicionado |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora mais o número de minutos especificado |
||||

*Exemplo 1*

Esse exemplo adiciona 10 minutos ao carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E retorna este resultado: `"2018-03-15T00:20:00.0000000Z"`

*Exemplo 2*

Esse exemplo subtrai cinco minutos do carimbo de data/hora especificado:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E retorna este resultado: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Adicionar uma propriedade e seu valor, ou par nome-valor, a um objeto JSON e retornar o objeto atualizado. Se o objeto já existir em tempo de execução, a função gerará um erro.

```
addProperty(<object>, '<property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | Objeto | O objeto JSON em que você deseja adicionar uma propriedade |
| <*property*> | Sim | Cadeia de caracteres | O nome da propriedade a ser adicionada |
| <*value*> | Sim | Any | O valor da propriedade |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado com a propriedade especificada |
||||

*Exemplo*

Esse exemplo adiciona a propriedade `accountNumber` ao objeto `customerProfile`, convertido em JSON com a função [JSON()](#json).
A função atribui um valor gerado pela função [guid()](#guid) e retorna o objeto atualizado:

```
addProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adicionar um número de segundos a um carimbo de data/hora.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*seconds*> | Sim | Inteiro | O número positivo ou negativo de segundos a ser adicionado |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora mais o número de segundos especificado  |
||||

*Exemplo 1*

Esse exemplo adiciona 10 segundos ao carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E retorna este resultado: `"2018-03-15T00:00:10.0000000Z"`

*Exemplo 2*

Esse exemplo subtrai cinco segundos ao carimbo de data/hora especificado:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E retorna este resultado: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Adicionar um número de unidades de tempo a um carimbo de data/hora.
Consulte também [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | Inteiro | O número de unidades de tempo especificadas a ser adicionado |
| <*timeUnit*> | Sim | Cadeia de caracteres | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora mais o número de unidades de tempo especificado  |
||||

*Exemplo 1*

Este exemplo adiciona um dia ao carimbo de data/hora especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E retorna este resultado: `"2018-01-02T00:00:00.0000000Z"`

*Exemplo 2*

Este exemplo adiciona um dia ao carimbo de data/hora especificado:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E retorna o resultado usando o formato "D" opcional: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verificar se todas as expressões são verdadeiras.
Retornará true quando todas as expressões forem true ou retornar false quando pelo menos uma expressão é false.

```
and(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Sim | Boolean | As expressões a serem verificadas |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | -----| ----------- |
| true ou false | Boolean | Retornará true quando todas as expressões forem true. Retornará false quando pelo menos uma expressão for false. |
||||

*Exemplo 1*

Esses exemplos verificam se os valores boolianos são todos true:

```
and(true, true)
and(false, true)
and(false, false)
```

E retorna estes resultados:

* Primeiro exemplo: Ambas as expressões são true, então retorna `true`.
* Segundo exemplo: Uma expressão é false, portanto retorna `false`.
* Terceiro exemplo: Ambas as expressões são false, portanto retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são todas true:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E retorna estes resultados:

* Primeiro exemplo: Ambas as expressões são true, então retorna `true`.
* Segundo exemplo: Uma expressão é false, portanto retorna `false`.
* Terceiro exemplo: Ambas as expressões são false, portanto retorna `false`.

<a name="array"></a>

### <a name="array"></a>array

Retornar uma matriz de uma única entrada especificada.
Para várias entradas, consulte [createArray()](#createArray).

```
array('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres para criar uma matriz |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | Uma matriz que contém a única entrada especificada |
||||

*Exemplo*

Esse exemplo cria uma matriz da cadeia de caracteres "hello":

```
array('hello')
```

E retorna este resultado: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Retornar a versão codificada em Base64 de uma cadeia de caracteres.

```
base64('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres de entrada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*base64-string*> | Cadeia de caracteres | A versão codificada em Base64 da cadeia de caracteres de entrada |
||||

*Exemplo*

Esse exemplo converte a cadeia de caracteres "hello" em uma cadeia de caracteres codificada em Base64:

```
base64('hello')
```

E retorna este resultado: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Retornar a versão binária de uma cadeia de caracteres codificada em Base64.

```
base64ToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres codificada em Base64 a ser convertida |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | Cadeia de caracteres | A versão binária da cadeia de caracteres codificada em Base64 |
||||

*Exemplo*

Esse exemplo converte a cadeia de caracteres codificada em Base64 "aGVsbG8=" em uma cadeia de caracteres binária:

```
base64ToBinary('aGVsbG8=')
```

E retorna este resultado:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Retorne a versão da cadeia de caracteres para uma cadeia de caracteres codificada em Base64, decodificando com eficiência a cadeia de caracteres Base64.
Use essa função em vez de [decodeBase64()](#decodeBase64).
Embora as funções funcionem da mesma forma, `base64ToString()` é preferencial.

```
base64ToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres codificada em Base64 a ser decodificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Cadeia de caracteres | A versão da cadeia de caracteres de uma cadeia de caracteres codificada em Base64 |
||||

*Exemplo*

Esse exemplo converte a cadeia de caracteres codificada em Base64 "aGVsbG8=" em apenas uma cadeia de caracteres:

```
base64ToString('aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>binary

Retornar a versão binária de uma cadeia de caracteres.

```
binary('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | Cadeia de caracteres | A versão binária para a cadeia de caracteres especificada |
||||

*Exemplo*

Esse exemplo converte a cadeia de caracteres "hello" em uma cadeia de caracteres binária:

```
binary('hello')
```

E retorna este resultado:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>body

Retornar a saída `body` de uma ação em tempo de execução.
Abreviação de `actions('<actionName>').outputs.body`.
Consulte [actionBody()](#actionBody) e [actions()](#actions).

```
body('<actionName>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | Cadeia de caracteres | O nome da saída `body` da ação desejada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | -----| ----------- |
| <*action-body-output*> | Cadeia de caracteres | A saída `body` da ação especificada |
||||

*Exemplo*

Esse exemplo obtém a saída `body` da ação de Twitter `Get user`:

```
body('Get_user')
```

E retorna este resultado:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Retornar a versão booliana de um valor.

```
bool(<value>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Any | O valor a ser convertido |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | A versão booliana do valor especificado |
||||

*Exemplo*

Esses exemplos convertem os valores especificados em valores boolianos:

```
bool(1)
bool(0)
```

E retorna estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Retornar o primeiro valor não nulo de um ou mais parâmetros.
Cadeias de caracteres vazias, matrizes vazias e objetos vazios não são nulos.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Sim | Qualquer um, pode misturar tipos | Um ou mais itens para verificar se são nulos |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Any | O primeiro item ou valor não nulo. Se todos os parâmetros forem nulos, essa função retornará null. |
||||

*Exemplo*

Esses exemplos retornarão o primeiro valor não nulo dos valores especificados ou null quando todos os valores forem nulos:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E retorna estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `"hello"`
* Terceiro exemplo: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combinar duas ou mais cadeias de caracteres e retornar a cadeia de caracteres combinada.

```
concat('<text1>', '<text2>', ...)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Sim | Cadeia de caracteres | Pelo menos duas cadeias de caracteres a serem combinadas |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Cadeia de caracteres | A cadeia de caracteres criada das cadeias de caracteres de entrada combinadas |
||||

*Exemplo*

Esse exemplo combina as cadeias de caracteres "Olá" e "Mundo":

```
concat('Hello', 'World')
```

E retorna este resultado: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Verificar se uma coleção tem um item específico.
Retornará true quando o item for localizado ou false quando não for localizado.
Essa função diferencia maiúsculas de minúsculas.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

Especificamente, essa função funciona nestes tipos de coleção:

* Uma *cadeia de caracteres* para localizar uma *subcadeia de caracteres*
* Uma *matriz* para localizar um *valor*
* Um *dicionário* para localizar uma *chave*

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String, Array ou Dictionary | A coleção a ser verificada |
| <*value*> | Sim | String, Array ou Dictionary, respectivamente | O item a ser localizado |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando o item for localizado. Retorna false quando não localizada. |
||||

*Exemplo 1*

Esse exemplo verifica se há a subcadeia de caracteres "mundo" na cadeia de caracteres "olá, mundo" e retorna true:

```
contains('hello world', 'world')
```

*Exemplo 2*

Esse exemplo verifica se há a subcadeia de caracteres "universo" na cadeia de caracteres "olá, mundo" e retorna false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converter um carimbo de data/hora de UTC (Tempo Universal Coordenado) no fuso horário de destino.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*destinationTimeZone*> | Sim | Cadeia de caracteres | O nome do fuso horário de destino. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Cadeia de caracteres | O carimbo de data/hora convertido na região do fuso horário |
||||

*Exemplo 1*

Esse exemplo converte um carimbo de data/hora no fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Esse exemplo converte um carimbo de data/hora no fuso horário e no formato especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converter um carimbo de data/hora do fuso horário de origem no fuso horário de destino.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | Cadeia de caracteres | O nome do fuso horário de origem. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*destinationTimeZone*> | Sim | Cadeia de caracteres | O nome do fuso horário de destino. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Cadeia de caracteres | O carimbo de data/hora convertido na região do fuso horário |
||||

*Exemplo 1*

Esse exemplo converte o fuso horário de origem no fuso horário de destino:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T00:00:00.0000000"`

*Exemplo 2*

Esse exemplo converte um fuso horário no fuso horário e no formato especificado:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Converter um carimbo de data/hora do fuso horário de origem em UTC (Tempo Universal Coordenado).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | Cadeia de caracteres | O nome do fuso horário de origem. Para obter mais informações, consulte [IDs de fuso horário](https://docs.microsoft.com/previous-versions/windows/embedded/gg154758(v=winembedded.80)). |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Cadeia de caracteres | O carimbo de data/hora convertido no UTC |
||||

*Exemplo 1*

Esse exemplo converte um carimbo de data/hora em UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T08:00:00.0000000Z"`

*Exemplo 2*

Esse exemplo converte um carimbo de data/hora em UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Retornar uma matriz de várias entradas.
Para matrizes de entrada únicas, consulte [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Sim | Qualquer um, mas não misto | Pelo menos dois itens para criar a matriz |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | A matriz criada com base em todos os itens de entrada |
||||

*Exemplo*

Esse exemplo cria uma matriz com base nestas entradas:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E retorna este resultado: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Retornar um URI (Uniform Resource Identifier) de dados para uma cadeia de caracteres.

```
dataUri('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*data-uri*> | Cadeia de caracteres | O URI dos dados da cadeia de caracteres de entrada |
||||

*Exemplo*

Esse exemplo cria um URI de dados para a cadeia de caracteres "hello":

```
dataUri('hello')
```

E retorna este resultado: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Retorne a versão binária de um URI (Uniform Resource Identifier) de dados.
Use esta função em vez de [decodeDataUri()](#decodeDataUri).
Embora as funções funcionem da mesma forma, `dataUriBinary()` é preferencial.

```
dataUriToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | O URI de dados a ser convertido |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Cadeia de caracteres | A versão binária do URI de dados |
||||

*Exemplo*

Esse exemplo cria uma versão binária para esse URI de dados:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Retornar a versão de cadeia de caracteres de um URI (Uniform Resource Identifier) de dados.

```
dataUriToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | O URI de dados a ser convertido |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | Cadeia de caracteres | A versão de cadeia de caracteres do URI de dados |
||||

*Exemplo*

Esse exemplo cria uma cadeia de caracteres para esse URI de dados:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Retorna o dia do mês de um carimbo de data/hora.

```
dayOfMonth('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Inteiro | O dia do mês do carimbo de data/hora especificado |
||||

*Exemplo*

Esse exemplo retorna o número do dia do mês desse carimbo de data/hora:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E retorna este resultado: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Retornar o dia da semana de um carimbo de data/hora.

```
dayOfWeek('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Inteiro | O dia da semana do carimbo de data/hora especificado em que domingo é 0, segunda-feira é 1 e assim por diante |
||||

*Exemplo*

Esse exemplo retorna o número do dia da semana desse carimbo de data/hora:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E retorna este resultado: `3`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Retorna o dia do ano de um carimbo de data/hora.

```
dayOfYear('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Inteiro | O dia do ano do carimbo de data/hora especificado |
||||

*Exemplo*

Esse exemplo retorna o número do dia do ano desse carimbo de data/hora:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E retorna este resultado: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64"></a>decodeBase64

Retorne a versão da cadeia de caracteres para uma cadeia de caracteres codificada em Base64, decodificando com eficiência a cadeia de caracteres Base64.
Considere usar [base64ToString()](#base64ToString), em vez de `decodeBase64()`.
Embora as funções funcionem da mesma forma, `base64ToString()` é preferencial.

```
decodeBase64('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres codificada em Base64 a ser decodificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Cadeia de caracteres | A versão da cadeia de caracteres de uma cadeia de caracteres codificada em Base64 |
||||

*Exemplo*

Esse exemplo cria uma cadeia de caracteres para uma cadeia de caracteres codificada em Base64:

```
decodeBase64('aGVsbG8=')
```

E retorna este resultado: `"hello"`

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Retorne a versão binária de um URI (Uniform Resource Identifier) de dados.
Considere usar [dataUriToBinary()](#dataUriToBinary), em vez de `decodeDataUri()`.
Embora as funções funcionem da mesma forma, `dataUriToBinary()` é preferencial.

```
decodeDataUri('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres do URI de dados a ser decodificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | Cadeia de caracteres | A versão binária de uma cadeia de caracteres do URI de dados |
||||

*Exemplo*

Esse exemplo retorna a versão binária para este URI de dados:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E retorna este resultado:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Retornar uma cadeia de caracteres que substitui caracteres de escape por versões decodificadas.

```
decodeUriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres com os caracteres de escape a ser decodificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Cadeia de caracteres | A cadeia de caracteres atualizada com os caracteres de escape decodificados |
||||

*Exemplo*

Esse exemplo substitui os caracteres de escape nesta cadeia de caracteres por versões decodificadas:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Retornar o resultado inteiro da divisão de dois números.
Para obter o resultado restante, consulte [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | Integer ou Float | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | Integer ou Float | O número que divide o *dividendo*, mas não pode ser 0 |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Inteiro | O resultado inteiro da divisão do primeiro número pelo segundo |
||||

*Exemplo*

Os dois exemplos dividem o primeiro número pelo segundo:

```
div(10, 5)
div(11, 5)
```

E retornam este resultado: `2`

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Retornar uma versão codificada para URI (Uniform Resource Identifier) para uma cadeia de caracteres substituindo caracteres desprotegidos de URL por caracteres de escape.
Considere usar [uriComponent()](#uriComponent), em vez de `encodeUriComponent()`.
Embora as funções funcionem da mesma forma, `uriComponent()` é preferencial.

```
encodeUriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser convertida em um formato codificado para URI |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Cadeia de caracteres | A cadeia de caracteres codificada para URI com caracteres de escape |
||||

*Exemplo*

Esse exemplo cria uma versão codificada para URI para esta cadeia de caracteres:

```
encodeUriComponent('https://contoso.com')
```

E retorna este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Verifique se uma coleção está vazia.
Retornará true quando a coleção estiver vazia ou false quando não estiver vazia.

```
empty('<collection>')
empty([<collection>])
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String, Array ou Object | A coleção a ser verificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando a coleção estiver vazia. Retornará false quando não estiver vazia. |
||||

*Exemplo*

Esses exemplos verificam se as coleções especificadas estão vazias:

```
empty('')
empty('abc')
```

E retorna estes resultados:

* Primeiro exemplo: Passa uma cadeia de caracteres vazia para que a função retorne `true`.
* Segundo exemplo: Passa uma cadeia de caracteres "abc, para que a função retorne `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verificar se uma cadeia de caracteres termina com uma subcadeia de caracteres específica.
Retornará true quando a subcadeia de caracteres for localizada ou retornará false quando não for localizada.
Essa função não diferencia maiúsculas de minúsculas.

```
endsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | Cadeia de caracteres | A subcadeia de caracteres final a ser localizada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false  | Boolean | Retornará true quando a subcadeia de caracteres final for localizada. Retorna false quando não localizada. |
||||

*Exemplo 1*

Esse exemplo verifica se a cadeia de caracteres "olá, mundo" termina com a cadeia de caracteres "mundo":

```
endsWith('hello world', 'world')
```

E retorna este resultado: `true`

*Exemplo 2*

Esse exemplo verifica se a cadeia de caracteres "olá, mundo" termina com a cadeia de caracteres "universo":

```
endsWith('hello world', 'universe')
```

E retorna este resultado: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Verificar se os valores, expressões ou objetos são equivalentes.
Retornará true quando ambos forem equivalentes ou retornará false quando não forem equivalentes.

```
equals('<object1>', '<object2>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Sim | Vários | Os valores, expressões ou objetos a serem comparados |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando ambos forem equivalentes. Retornará false quando não forem equivalentes. |
||||

*Exemplo*

Esses exemplos verificam se as entradas especificadas são equivalentes.

```
equals(true, 1)
equals('abc', 'abcd')
```

E retorna estes resultados:

* Primeiro exemplo: Ambos os valores são equivalentes, então a função retorna `true`.
* Segundo exemplo: Ambos os valores não são equivalentes, então a função retorna `false`.

<a name="first"></a>

### <a name="first"></a>first

Retornar o primeiro item de uma cadeia de caracteres ou de uma matriz.

```
first('<collection>')
first([<collection>])
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção na qual localizar o primeiro item |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Any | O primeiro item na coleção |
||||

*Exemplo*

Esses exemplos localizam o primeiro item nestas coleções:

```
first('hello')
first(createArray(0, 1, 2))
```

E retornam estes resultados:

* Primeiro exemplo: `"h"`
* Segundo exemplo: `0`

<a name="float"></a>

### <a name="float"></a>float

Converter uma versão de cadeia de caracteres para um número de ponto flutuante em um número de ponto flutuante real.
Você pode usar essa função somente ao passar parâmetros personalizados para um aplicativo, por exemplo, um aplicativo lógico ou um fluxo.

```
float('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres que tem um número de ponto flutuante válido a ser convertido |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | O número de ponto flutuante para a cadeia de caracteres especificada |
||||

*Exemplo*

Esse exemplo cria uma versão de cadeia de caracteres para este número de ponto flutuante:

```
float('10.333')
```

E retorna este resultado: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Retornar um carimbo de data/hora no formato especificado.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | Cadeia de caracteres | O carimbo de data/hora atualizado no formato especificado |
||||

*Exemplo*

Esse exemplo converte um carimbo de data/hora no formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E retorna este resultado: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Retornar uma matriz com valores correspondentes a um nome de chave na saída de *dados de formulário* ou *codificada para formulário* de uma ação.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | Cadeia de caracteres | A ação cuja saída tem o valor de chave desejado |
| <*key*> | Sim | Cadeia de caracteres | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Uma matriz com todos os valores correspondentes à chave especificada |
||||

*Exemplo*

Esse exemplo cria uma matriz do valor da chave "Assunto" na saída de dados de formulário ou codificada para formulário de uma ação:

```
formDataMultiValues('Send_an_email', 'Subject')
```

E retorna o texto do assunto em uma matriz, por exemplo: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Retornar um único valor correspondente a um nome de chave na saída *form-data* ou *form-encoded* de uma ação.
Se a função localizar mais de uma correspondência, a função gerará um erro.

```
formDataValue('<actionName>', '<key>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | Cadeia de caracteres | A ação cuja saída tem o valor de chave desejado |
| <*key*> | Sim | Cadeia de caracteres | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*key-value*> | Cadeia de caracteres | O valor na chave especificada  |
||||

*Exemplo*

Esse exemplo cria uma cadeia de caracteres do valor da chave "Assunto" na saída de dados de formulário ou codificada para formulário de uma ação:

```
formDataValue('Send_an_email', 'Subject')
```

E retorna o texto do assunto como uma cadeia de caracteres, por exemplo: `"Hello world"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Inteiro | O número de unidades de tempo especificadas a serem subtraídas |
| <*timeUnit*> | Sim | Cadeia de caracteres | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora atual mais o número de unidades de tempo especificado |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora é "2018-03-01T00:00:00.0000000Z".
Esse exemplo adiciona cinco dias a esse carimbo de data/hora:

```
getFutureTime(5, 'Day')
```

E retorna este resultado: `"2018-03-06T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o carimbo de data/hora é "2018-03-01T00:00:00.0000000Z".
Esse exemplo adiciona cinco dias e converte o resultado no formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E retorna este resultado: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Retornar o carimbo de data/hora atual menos as unidades de tempo especificadas.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Inteiro | O número de unidades de tempo especificadas a serem subtraídas |
| <*timeUnit*> | Sim | Cadeia de caracteres | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora atual menos o número de unidades de tempo especificado |
||||

*Exemplo 1*

Suponha que o carimbo de data/hora é "2018-02-01T00:00:00.0000000Z".
Esse exemplo subtrai cinco dias desse carimbo de data/hora:

```
getPastTime(5, 'Day')
```

E retorna este resultado: `"2018-01-27T00:00:00.0000000Z"`

*Exemplo 2*

Suponha que o carimbo de data/hora é "2018-02-01T00:00:00.0000000Z".
Esse exemplo subtrai cinco dias e converte o resultado no formato "D":

```
getPastTime(5, 'Day', 'D')
```

E retorna este resultado: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verificar se o primeiro valor é maior que o segundo valor.
Retornará true quando o primeiro valor for maior ou false quando for menor.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é maior do que o segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando o primeiro valor for maior que o segundo. Retornará false quando o primeiro valor for igual ou menor que o segundo. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é maior que o segundo:

```
greater(10, 5)
greater('apple', 'banana')
```

E retornam estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verificar se o primeiro valor é superior ou igual ao segundo.
Retornará true quando o primeiro valor for superior ou igual ou retornará false quando o primeiro valor for inferior.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é superior ou igual ao segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando o primeiro valor for superior ou igual ao segundo. Retornará false quando o primeiro valor for menor que o segundo. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é superior ou igual ao segundo:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E retornam estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="guid"></a>

### <a name="guid"></a>GUID

Gere um GUID (identificador global exclusivo) como uma cadeia de caracteres, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, é possível especificar um formato diferente para o GUID que não seja o formato padrão, "D", que é de 32 dígitos separados por hifens.

```
guid('<format>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | Cadeia de caracteres | Um único [especificador de formato](https://msdn.microsoft.com/library/97af8hh4) para o GUID retornado. Por padrão, o formato é "D", mas é possível usar "N", "D", "B", "P" ou "X". |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*GUID-value*> | Cadeia de caracteres | Um GUID gerado aleatoriamente |
||||

*Exemplo*

Esse exemplo gera o mesmo GUID, mas como 32 dígitos, separado por hifens e entre parênteses:

```
guid('P')
```

E retorna este resultado: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Verificar se uma expressão é verdadeira ou falsa.
Com base no resultado, retornar um valor especificado.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Boolean | A expressão a ser verificada |
| <*valueIfTrue*> | Sim | Any | O valor a ser retornado quando a expressão for verdadeira |
| <*valueIfFalse*> | Sim | Any | O valor a ser retornado quando a expressão for falsa |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Any | O valor especificado retornado dependendo se a expressão é verdadeira ou falsa |
||||

*Exemplo*

Esse exemplo retorna `"yes"`, porque a expressão especificada retorna true.
Caso contrário, o exemplo retorna `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Retornar o valor de índice ou a posição inicial para uma subcadeia de caracteres.
Essa função não diferencia maiúsculas de minúsculas, e os índices começam com o número 0.

```
indexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | Cadeia de caracteres | A subcadeia de caracteres a ser localizada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*index-value*>| Inteiro | O valor de índice ou a posição inicial da subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for localizada, retornará o número -1. |
||||

*Exemplo*

Esse exemplo localiza o valor de índice inicial para a subcadeia de caracteres "mundo" na cadeia de caracteres "olá, mundo":

```
indexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="int"></a>

### <a name="int"></a>int

Retornar a versão em inteiros de uma cadeia de caracteres.

```
int('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser convertida |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*integer-result*> | Inteiro | A versão em inteiros da cadeia de caracteres especificada |
||||

*Exemplo*

Esse exemplo cria uma versão em inteiros da cadeia de caracteres "10":

```
int('10')
```

E retorna este resultado: `10`

<a name="item"></a>

### <a name="item"></a>item

Quando usado dentro de uma ação repetida em uma matriz, retornar o item atual na matriz durante a iteração atual da ação.
Também é possível obter os valores das propriedades desse item.

```
item()
```

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Any | O item atual na matriz da iteração atual da ação |
||||

*Exemplo*

Esse exemplo obtém o elemento `body` da mensagem atual para a ação "Enviar_um_email" dentro de uma iteração atual do loop for-each:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

Retornar o item atual de cada ciclo em um loop for-each.
Use esta função dentro do loop for-each.

```
items('<loopName>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Sim | Cadeia de caracteres | O nome do loop for-each |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*item*> | Any | O item do ciclo atual no loop for-each especificado |
||||

*Exemplo*

Esse exemplo obtém o item atual do loop for-each especificado:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Retorna o valor de índice para a iteração atual dentro de um loop Until. Você pode usar essa função dentro de loops aninhados until. 

```
iterationIndexes('<loopName>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Sim | Cadeia de caracteres | O nome do loop Until | 
||||| 

| Valor de retorno | Tipo | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| <*index*> | Inteiro | O valor de índice para a iteração atual dentro do loop Until especificado | 
|||| 

*Exemplo* 

Este exemplo cria uma variável de contador e incrementa essa variável por uma durante cada iteração em um loop Until até que o valor do contador atinja cinco. O exemplo também cria uma variável que controla o índice atual para cada iteração. No loop Until, durante cada iteração, o exemplo incrementa o contador e, em seguida, atribui o valor do contador ao valor do índice atual e, em seguida, incrementa o contador. A qualquer momento, você pode determinar o número de iteração atual recuperando o valor do índice atual.

```
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5),
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Retornar o objeto ou valor de tipo JSON (JavaScript Object Notation) correspondente a uma cadeia de caracteres ou XML.

```
json('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String ou XML | A cadeia de caracteres ou XML a ser convertido |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Objeto ou tipo JSON nativo | O objeto ou valor de tipo JSON nativo para a cadeia de caracteres ou XML especificado. Se a cadeia de caracteres for nula, a função retornará um objeto vazio. |
||||

*Exemplo 1*

Esse exemplo converte essa cadeia de caracteres no valor JSON:

```
json('[1, 2, 3]')
```

E retorna este resultado: `[1, 2, 3]`

*Exemplo 2*

Esse exemplo converte essa cadeia de caracteres em JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E retorna este resultado:

```
{
  "fullName": "Sophia Owen"
}
```

*Exemplo 3*

Esse exemplo converte esse XML em JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E retorna este resultado:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>interseção

Retornar uma coleção que tem *somente* os itens comuns entre as coleções especificadas.
Para aparecer no resultado, um item deve ser exibido em todas as coleções passadas para essa função.
Se um ou mais itens tiverem o mesmo nome, o último item com o nome será exibido neste resultado.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Sim | Matriz ou objeto, mas não ambos | As coleções da qual você deseja *somente* os itens comuns |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*common-items*> | Array ou Object, respectivamente | Uma coleção que tem somente os itens comuns entre as coleções especificadas |
||||

*Exemplo*

Esse exemplo localiza os itens comuns entre estas matrizes:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E retorna uma matriz com *apenas* estes itens: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Retornar uma cadeia de caracteres que tem todos os itens de uma matriz e tem cada caractere separado por um *delimitador*.

```
join([<collection>], '<delimiter>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | Array | A matriz que tem os itens a serem ingressados |
| <*delimiter*> | Sim | Cadeia de caracteres | O separador exibido entre cada caractere na cadeia de caracteres resultante |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | Cadeia de caracteres | A cadeia de caracteres resultante criada com base em todos os itens na matriz especificada |
||||

*Exemplo*

Esse exemplo cria uma cadeia de caracteres com base em todos os itens nessa matriz com o caractere especificado como o delimitador:

```
join(createArray('a', 'b', 'c'), '.')
```

E retorna este resultado: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

Retornar o último item de uma coleção.

```
last('<collection>')
last([<collection>])
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção na qual localizar o último item |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | String ou Array, respectivamente | O último item na coleção |
||||

*Exemplo*

Esses exemplos localizam o último item nestas coleções:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E retorna estes resultados:

* Primeiro exemplo: `"d"`
* Segundo exemplo: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Retorna a posição inicial ou valor de índice para a última ocorrência de uma subcadeia de caracteres.
Essa função não diferencia maiúsculas de minúsculas, e os índices começam com o número 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | Cadeia de caracteres | A subcadeia de caracteres a ser localizada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Inteiro | A posição inicial ou valor de índice da última ocorrência da subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for localizada, retornará o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor de índice inicial para a última ocorrência da subcadeia de caracteres "mundo" na cadeia de caracteres "olá, mundo":

```
lastIndexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="length"></a>

### <a name="length"></a>length

Retornar o número de itens em uma coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção com os itens a serem contados |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Inteiro | O número de itens na coleção |
||||

*Exemplo*

Esses exemplos contam o número de itens nestas coleções:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E retornam este resultado: `4`

<a name="less"></a>

### <a name="less"></a>less

Verificar se o primeiro valor é menor que o segundo valor.
Retornará true quando o primeiro valor for menor ou retornará false quando o primeiro valor for maior.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é menor do que o segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O item de comparação |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando o primeiro valor for menor que o segundo. Retornará false quando o primeiro valor for igual ou maior que o segundo. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é menor que o segundo.

```
less(5, 10)
less('banana', 'apple')
```

E retornam estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verificar se o primeiro valor é inferior ou igual ao segundo valor.
Retornará true quando o primeiro valor for inferior ou igual ou retornará false quando o primeiro valor for superior.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é inferior ou igual ao segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O item de comparação |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false  | Boolean | Retornará true quando o primeiro valor for inferior ou igual ao segundo valor. Retornará false quando o primeiro valor for maior que o segundo. |
||||

*Exemplo*

Esses exemplos verificam se o primeiro valor é inferior ou igual ao segundo.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E retornam estes resultados:

* Primeiro exemplo: `true`
* Segundo exemplo: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Retornar a "URL de retorno de chamada" que chama um gatilho ou uma ação.
Essa função funciona somente com gatilhos e ações para os tipos de conector **HttpWebhook** e **ApiConnectionWebhook**, mas não os tipos **Manual**, **Recurrence**, **HTTP** e **APIConnection**.

```
listCallbackUrl()
```

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*callback-URL*> | Cadeia de caracteres | A URL de retorno de chamada para um gatilho ou uma ação |
||||

*Exemplo*

Esse exemplo mostra um exemplo de URL de retorno de chamada que esta função pode retornar:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Retornar o maior valor de uma lista ou matriz com números inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | Integer, Float ou ambos | O conjunto de números do qual você deseja o valor mais alto |
| [<*number1*>, <*number2*>, ...] | Sim | Array – Integer, Float ou ambos | A matriz de números da qual você deseja o valor mais alto |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*max-value*> | Integer ou Float | O valor mais alto na matriz ou conjunto de números especificado |
||||

*Exemplo*

Esses exemplos obtêm o valor mais alto do conjunto de números e da matriz:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E retornam este resultado: `3`

<a name="min"></a>

### <a name="min"></a>Min

Retornar o valor mais baixo de um conjunto de números ou de uma matriz.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | Integer, Float ou ambos | O conjunto de números do qual você deseja o valor mais baixo |
| [<*number1*>, <*number2*>, ...] | Sim | Array – Integer, Float ou ambos | A matriz de números da qual você deseja o valor mais baixo |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*min-value*> | Integer ou Float | O valor mais baixo no conjunto de números ou matriz especificado |
||||

*Exemplo*

Esses exemplos obtêm o valor mais baixo no conjunto de números e na matriz:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E retornam este resultado: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Retornar o resto da divisão de dois números.
Para obter o resultado inteiro, consulte [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | Integer ou Float | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | Integer ou Float | O número que divide o *dividendo*, mas não pode ser 0. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Integer ou Float | O resto da divisão do primeiro número pelo segundo |
||||

*Exemplo*

O exemplo divide o primeiro número pelo segundo:

```
mod(3, 2)
```

E retornam este resultado: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Retornar o produto da multiplicação de dois números.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | Integer ou Float | O número a ser multiplicado por *multiplicand2* |
| <*multiplicand2*> | Sim | Integer ou Float | O número que multiplica *multiplicand1* |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*product-result*> | Integer ou Float | O produto da multiplicação do primeiro número pelo segundo |
||||

*Exemplo*

Esses exemplos multiplicam o primeiro número pelo segundo:

```
mul(1, 2)
mul(1.5, 2)
```

E retornam estes resultados:

* Primeiro exemplo: `2`
* Segundo exemplo `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Retornar o corpo de uma parte específica na saída de uma ação que tem várias partes.

```
multipartBody('<actionName>', <index>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sim | Cadeia de caracteres | O nome da ação que tem saída com várias partes |
| <*index*> | Sim | Inteiro | O valor de índice da parte desejada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*body*> | Cadeia de caracteres | O corpo da parte especificada |
||||

<a name="not"></a>

### <a name="not"></a>não

Verificar se uma expressão é falsa.
Retornará true quando a expressão for falsa ou retornará false quando for verdadeira.

```json
not(<expression>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Boolean | A expressão a ser verificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando a expressão for falsa. Retornará false quando a expressão for verdadeira. |
||||

*Exemplo 1*

Esses exemplos verificam se as expressões especificadas são false:

```json
not(false)
not(true)
```

E retornam estes resultados:

* Primeiro exemplo: A expressão é false, então a função retorna `true`.
* Segundo exemplo: A expressão é true, então a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se as expressões especificadas são false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E retornam estes resultados:

* Primeiro exemplo: A expressão é false, então a função retorna `true`.
* Segundo exemplo: A expressão é true, então a função retorna `false`.

<a name="or"></a>

### <a name="or"></a>ou

Verificar se pelo menos uma expressão é verdadeira.
Retornará true quando pelo menos uma expressão for verdadeira ou false quando todas forem falsas.

```
or(<expression1>, <expression2>, ...)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Sim | Boolean | As expressões a serem verificadas |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando pelo menos uma expressão for verdadeira. Retornará false quando todas as expressões forem falsas. |
||||

*Exemplo 1*

Esses exemplos verificam se pelo menos uma expressão é true:

```json
or(true, false)
or(false, false)
```

E retornam estes resultados:

* Primeiro exemplo: No mínimo, uma expressão é true, então a função retorna `true`.
* Segundo exemplo: Ambas as expressões são false, então a função retorna `false`.

*Exemplo 2*

Esses exemplos verificam se pelo menos uma expressão é true:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E retornam estes resultados:

* Primeiro exemplo: No mínimo, uma expressão é true, então a função retorna `true`.
* Segundo exemplo: Ambas as expressões são false, então a função retorna `false`.

<a name="parameters"></a>

### <a name="parameters"></a>parameters

Retorne o valor para um parâmetro descrito em sua definição de fluxo de trabalho.

```
parameters('<parameterName>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Sim | Cadeia de caracteres | O nome do parâmetro cujo valor você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Any | O valor do parâmetro especificado |
||||

*Exemplo*

Suponha que você tenha este valor JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Esse exemplo obtém o valor do parâmetro especificado:

```
parameters('fullName')
```

E retorna este resultado: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Retornar um inteiro aleatório de um intervalo especificado, inclusivo apenas na extremidade inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Inteiro | O menor inteiro no intervalo |
| <*maxValue*> | Sim | Inteiro | O inteiro que segue o inteiro mais alto no intervalo que a função pode retornar |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*random-result*> | Inteiro | O inteiro aleatório retornado do intervalo especificado |
||||

*Exemplo*

Esse exemplo obtém um inteiro aleatório do intervalo especificado, excluindo o valor máximo:

```
rand(1, 5)
```

E retorna um destes números como o resultado: `1`, `2`, `3` ou `4`

<a name="range"></a>

### <a name="range"></a>range

Retornar uma matriz de inteiros que começa em um inteiro especificado.

```
range(<startIndex>, <count>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Inteiro | Um valor inteiro que inicia a matriz como o primeiro item |
| <*count*> | Sim | Inteiro | O número de inteiros na matriz |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | A matriz com inteiros que começa no índice especificado |
||||

*Exemplo*

Esse exemplo cria uma matriz de inteiros que começa no índice especificado e tem o número de inteiros especificado:

```
range(1, 4)
```

E retorna este resultado: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>substituir

Substituir uma subcadeia de caracteres pela cadeia de caracteres especificada e retornar a cadeia de caracteres resultante. Essa função diferencia maiúsculas de minúsculas.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres que tem a subcadeia de caracteres a ser substituída |
| <*oldText*> | Sim | Cadeia de caracteres | A subcadeia de caracteres a ser substituída |
| <*newText*> | Sim | Cadeia de caracteres | A cadeia de caracteres de substituição |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-text*> | Cadeia de caracteres | A cadeia de caracteres atualizada após substituir a subcadeia de caracteres <p>Se a subcadeia de caracteres não for localizada, retorne a cadeia de caracteres original. |
||||

*Exemplo*

Esse exemplo localiza a subcadeia de caracteres "antiga" em "a antiga cadeia de caracteres" e substitui "antiga" por "nova":

```
replace('the old string', 'old', 'new')
```

E retorna este resultado: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Remover uma propriedade de um objeto e retornar o objeto atualizado.

```
removeProperty(<object>, '<property>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | Objeto | O objeto JSON do qual você deseja remover uma propriedade |
| <*property*> | Sim | Cadeia de caracteres | O nome da propriedade a ser removida |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado sem a propriedade especificada |
||||

*Exemplo*

Esse exemplo remove a propriedade `"accountLocation"` de um objeto `"customerProfile"`, convertido em JSON com a função [JSON()](#json) e retorna o objeto atualizado:

```
removeProperty(json('customerProfile'), 'accountLocation')
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Definir o valor da propriedade de um objeto e retornar o objeto atualizado.
Para adicionar uma nova propriedade, é possível usar esta função ou a função [addProperty()](#addProperty).

```
setProperty(<object>, '<property>', <value>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sim | Objeto | O objeto JSON cuja propriedade você deseja definir |
| <*property*> | Sim | Cadeia de caracteres | O nome da propriedade nova ou existente a ser definida |
| <*value*> | Sim | Any | O valor a ser definido para a propriedade especificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-object*> | Objeto | O objeto JSON atualizado cuja propriedade você define |
||||

*Exemplo*

Esse exemplo define a propriedade `"accountNumber"` em um objeto `"customerProfile"`, convertido em JSON com a função [JSON()](#json).
A função atribui um valor gerado pela função [guid()](#guid) e retorna o objeto JSON atualizado:

```
setProperty(json('customerProfile'), 'accountNumber', guid())
```

<a name="skip"></a>

### <a name="skip"></a>skip

Remover itens do início de uma coleção e retornar *todos os outros* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | Array | A coleção cujos itens você deseja remover |
| <*count*> | Sim | Inteiro | Um inteiro positivo para o número de itens a ser removido no início |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | A coleção atualizada depois de remover os itens especificados |
||||

*Exemplo*

Esse exemplo remove um item, o número 0, do início da matriz especificada:

```
skip(createArray(0, 1, 2, 3), 1)
```

E retorna essa matriz com os itens restantes: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Retorna uma matriz que contém subcadeias de caracteres, separadas por vírgulas, com base no caractere delimitador especificado na cadeia de caracteres original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres para separar em subcadeias de caracteres com base no delimitador especificado na cadeia de caracteres original |
| <*delimiter*> | Sim | Cadeia de caracteres | O caractere na cadeia de caracteres original para usar como delimitador |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | Uma matriz contendo subcadeias de caracteres da cadeia de caracteres original, separadas por vírgulas |
||||

*Exemplo*

Este exemplo cria uma matriz com subcadeias de caractere da cadeia de caracteres especificada com base no caractere especificado como o delimitador:

```
split('a_b_c', '_')
```

E retorna essa matriz como resultado: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Retornar o início do dia de um carimbo de data/hora.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora especificado, mas começando na marca de zero hora para o dia |
||||

*Exemplo*

Esse exemplo localiza o início do dia para esse carimbo de data/hora:

```
startOfDay('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Retornar o início da hora de um carimbo de data/hora.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora especificado, mas começando na marca de zero minuto para a hora |
||||

*Exemplo*

Esse exemplo localiza o início da hora para esse carimbo de data/hora:

```
startOfHour('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Retornar o início do mês de um carimbo de data/hora.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora especificado, mas começando no primeiro dia do mês na marca de zero hora |
||||

*Exemplo*

Esse exemplo retorna o início do mês para este carimbo de data/hora:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E retorna este resultado: `"2018-03-01T00:00:00.0000000Z"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verificar se uma cadeia de caracteres começa com uma subcadeia de caracteres específica.
Retornará true quando a subcadeia de caracteres for localizada ou retornará false quando não for localizada.
Essa função não diferencia maiúsculas de minúsculas.

```
startsWith('<text>', '<searchText>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | Cadeia de caracteres | A cadeia de caracteres inicial a ser localizada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| true ou false  | Boolean | Retornará true quando a subcadeia de caracteres inicial for localizada. Retorna false quando não localizada. |
||||

*Exemplo 1*

Esse exemplo verifica se a cadeia de caracteres "olá, mundo" começa com a subcadeia de caracteres "olá":

```
startsWith('hello world', 'hello')
```

E retorna este resultado: `true`

*Exemplo 2*

Esse exemplo verifica se a cadeia de caracteres "olá, mundo" começa com a subcadeia de caracteres "saudações":

```
startsWith('hello world', 'greetings')
```

E retorna este resultado: `false`

<a name="string"></a>

### <a name="string"></a>cadeia de caracteres

Retornar a versão de cadeia de caracteres para um valor.

```
string(<value>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Any | O valor a ser convertido |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*string-value*> | Cadeia de caracteres | A versão de cadeia de caracteres do valor especificado |
||||

*Exemplo 1*

Esse exemplo cria uma versão de cadeia para este número:

```
string(10)
```

E retorna este resultado: `"10"`

*Exemplo 2*

Esse exemplo cria uma cadeia de caracteres para o objeto JSON especificado e usa o caractere de barra invertida (\\) como o caractere de escape para aspas duplas (").

```
string( { "name": "Sophie Owen" } )
```

E retorna este resultado: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Retornar o resultado da subtração do segundo número do primeiro.

```
sub(<minuend>, <subtrahend>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Integer ou Float | O número do qual subtrair o *subtraendo* |
| <*subtrahend*> | Sim | Integer ou Float | O número a ser subtraído do *minuendo* |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*result*> | Integer ou Float | O resultado da subtração do segundo número do primeiro |
||||

*Exemplo*

Esse exemplo subtrai o segundo número do primeiro:

```
sub(10.3, .3)
```

E retorna este resultado: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Retornar caracteres de uma cadeia de caracteres, começando na posição especificada, ou índice.
Os valores de índice começam com o número 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres cujos caracteres você deseja |
| <*startIndex*> | Sim | Inteiro | Um número positivo igual ou maior que 0 que você deseja usar como a posição inicial ou o valor do índice |
| <*length*> | Sim | Inteiro | Um número positivo de caracteres que você deseja na subcadeia de caracteres |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*substring-result*> | Cadeia de caracteres | Uma subcadeia de caracteres com o número de caracteres especificado, começando na posição de índice especificada na cadeia de caracteres de origem |
||||

*Exemplo*

Esse exemplo cria uma subcadeia de caracteres de cinco caracteres com base na cadeia de caracteres especificada, começando no valor de índice 6:

```
substring('hello world', 6, 5)
```

E retorna este resultado: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Subtrair um número de unidades de tempo de um carimbo de data/hora.
Confira também [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | Inteiro | O número de unidades de tempo especificadas a serem subtraídas |
| <*timeUnit*> | Sim | Cadeia de caracteres | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Cadeia de caracteres | O carimbo de data/hora menos o número de unidades de tempo especificado |
||||

*Exemplo 1*

Esse exemplo subtrai um dia desse carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E retorna este resultado: `"2018-01-01T00:00:00:0000000Z"`

*Exemplo 2*

Esse exemplo subtrai um dia desse carimbo de data/hora:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E retorna este resultado usando o formato "D" opcional: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Retornar itens do início de uma coleção.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção cujos itens você deseja |
| <*count*> | Sim | Inteiro | Um inteiro positivo para o número de itens que você deseja no início |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*subset*> ou [<*subset*>] | String ou Array, respectivamente | Uma cadeia de caracteres ou matriz que tem o número de itens especificado extraído do início da coleção original |
||||

*Exemplo*

Esses exemplos obtêm o número de itens especificado no início destas coleções:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E retornam estes resultados:

* Primeiro exemplo: `"abc"`
* Segundo exemplo: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Retornar o valor de propriedade `ticks` de um carimbo de data/hora especificado.
Um *tique* é um intervalo de 100 nanossegundos.

```
ticks('<timestamp>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | Cadeia de caracteres | A cadeia de caracteres de um carimbo de data/hora |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Inteiro | O número de tiques desde o carimbo de data/hora especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retornar uma cadeia de caracteres em letras minúsculas. Se um caractere na cadeia de caracteres não tiver uma versão em minúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toLower('<text>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser retornada no formato em minúsculas |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | Cadeia de caracteres | A cadeia de caracteres original no formato em minúsculas |
||||

*Exemplo*

Esse exemplo converte essa cadeia de caracteres em minúsculas:

```
toLower('Hello World')
```

E retorna este resultado: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Retornar uma cadeia de caracteres em letras maiúsculas. Se um caractere na cadeia de caracteres não tiver uma versão em maiúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toUpper('<text>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser retornada no formato em maiúsculas |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | Cadeia de caracteres | A cadeia de caracteres original no formato em maiúsculas |
||||

*Exemplo*

Esse exemplo converte essa cadeia de caracteres em maiúsculas:

```
toUpper('Hello World')
```

E retorna este resultado: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>gatilho

Retornar a saída de um gatilho em tempo de execução ou os valores de outros pares de nome e valor JSON, que podem ser atribuídos a uma expressão.

* Dentro das entradas de um gatilho, essa função retorna a saída da execução anterior.

* Dentro da condição de um gatilho, essa função retorna a saída da execução atual.

Por padrão, a função referencia todo o objeto de gatilho, mas é possível especificar uma propriedade cujo valor você deseja.
Além disso, essa função tem versões abreviadas disponíveis, consulte [triggerOutputs()](#triggerOutputs) e [triggerBody()](#triggerBody).

```
trigger()
```

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Cadeia de caracteres | A saída de um gatilho em tempo de execução |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Retornar a saída `body` de um gatilho em tempo de execução.
Abreviação de `trigger().outputs.body`.
Consulte [trigger()](#trigger).

```
triggerBody()
```

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | Cadeia de caracteres | A saída `body` do gatilho |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Retornar uma matriz com valores que correspondem a um nome de chave na saída de *dados de formulário* ou *codificada para formulário* de um gatilho.

```
triggerFormDataMultiValues('<key>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*key*> | Sim | Cadeia de caracteres | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Uma matriz com todos os valores correspondentes à chave especificada |
||||

*Exemplo*

Este exemplo cria uma matriz do valor de chave "feedUrl" em uma saída de dados de formulário ou codificada para formulário de um gatilho RSS:

```
triggerFormDataMultiValues('feedUrl')
```

E retorna esta matriz como um resultado de exemplo: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Retornar uma cadeia de caracteres com um único valor que corresponde a um nome de chave na saída de *dados de formulário* ou *codificada para formulário* de um gatilho.
Se a função localizar mais de uma correspondência, a função gerará um erro.

```
triggerFormDataValue('<key>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*key*> | Sim | Cadeia de caracteres | O nome da chave cujo valor você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*key-value*> | Cadeia de caracteres | O valor na chave especificada |
||||

*Exemplo*

Este exemplo cria uma cadeia de caracteres do valor de chave "feedUrl" em uma saída de dados de formulário ou codificada para formulário de um gatilho RSS:

```
triggerFormDataValue('feedUrl')
```

E retorna esta cadeia de caracteres como um resultado de exemplo: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Retornar o corpo de uma parte específica na saída de um gatilho que tem várias partes.

```
triggerMultipartBody(<index>)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*index*> | Sim | Inteiro | O valor de índice da parte desejada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*body*> | Cadeia de caracteres | O corpo da parte especificada na saída de várias partes de um gatilho |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Retornar a saída de um gatilho em tempo de execução ou os valores de outros pares de nome e valor JSON.
Abreviação de `trigger().outputs`.
Consulte [trigger()](#trigger).

```
triggerOutputs()
```

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Cadeia de caracteres | A saída de um gatilho em tempo de execução  |
||||

<a name="trim"></a>

### <a name="trim"></a>trim

Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada.

```
trim('<text>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | Cadeia de caracteres | A cadeia de caracteres que tem o espaço em branco à esquerda e à direita a ser removida |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updatedText*> | Cadeia de caracteres | Uma versão atualizada da cadeia de caracteres original sem espaço em branco à esquerda ou à direita |
||||

*Exemplo*

Esse exemplo remove o espaço em branco à esquerda e à direita da cadeia de caracteres "Olá, Mundo":

```
trim(' Hello World  ')
```

E retorna este resultado: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Retornar uma coleção que tem *todos* os itens das coleções especificadas.
Para aparecer no resultado, um item pode ser exibido em qualquer coleção passada para essa função. Se um ou mais itens tiverem o mesmo nome, o último item com o nome será exibido neste resultado.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Sim | Matriz ou objeto, mas não ambos | As coleções das quais você deseja *todos* os itens |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Array ou Object, respectivamente | Uma coleção com todos os itens das coleções especificadas – sem duplicatas |
||||

*Exemplo*

Esse exemplo obtém *todos* os itens destas coleções:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E retorna este resultado: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Retornar uma versão codificada para URI (Uniform Resource Identifier) para uma cadeia de caracteres substituindo caracteres desprotegidos de URL por caracteres de escape.
Use esta função em vez de [encodeUriComponent()](#encodeUriComponent).
Embora as funções funcionem da mesma forma, `uriComponent()` é preferencial.

```
uriComponent('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres a ser convertida em um formato codificado para URI |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | Cadeia de caracteres | A cadeia de caracteres codificada para URI com caracteres de escape |
||||

*Exemplo*

Esse exemplo cria uma versão codificada para URI para esta cadeia de caracteres:

```
uriComponent('https://contoso.com')
```

E retorna este resultado: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Retornar a versão binária de um componente URI (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres codificada para URI a ser convertida |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | Cadeia de caracteres | A versão binária da cadeia de caracteres codificada para URI. O conteúdo binário é codificado em Base64 e representado por `$content`. |
||||

*Exemplo*

Esse exemplo cria a versão binária para esta cadeia de caracteres codificada para URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Retornar a versão de cadeia de caracteres para uma cadeia de caracteres codificada para URI (Uniform Resource Identifier), decodificando com eficácia a cadeia de caracteres codificada para URI.

```
uriComponentToString('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres codificada para URI a ser decodificada |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Cadeia de caracteres | A versão decodificada para a cadeia de caracteres codificada para URI |
||||

*Exemplo*

Esse exemplo cria a versão de cadeia de caracteres decodificada para esta cadeia de caracteres codificada para URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Retornar o valor `host` de um URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia de caracteres | O URI cujo valor `host` você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*host-value*> | Cadeia de caracteres | O valor `host` do URI especificado |
||||

*Exemplo*

Esse exemplo localiza o valor `host` para este URI:

```
uriHost('https://www.localhost.com:8080')
```

E retorna este resultado: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Retornar o valor `path` de um URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia de caracteres | O URI cujo valor `path` você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*path-value*> | Cadeia de caracteres | O valor `path` do URI especificado. Se `path` não tiver um valor, retorne o caractere "/". |
||||

*Exemplo*

Esse exemplo localiza o valor `path` para este URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Retornar os valores `path` e `query` de um URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia de caracteres | O URI cujos valores `path` e `query` você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*path-query-value*> | Cadeia de caracteres | Os valores `path` e `query` para o URI especificado. Se `path` não especificar um valor, retorne o caractere "/". |
||||

*Exemplo*

Esse exemplo localiza os valores `path` e `query` para este URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Retornar o valor `port` de um URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia de caracteres | O URI cujo valor `port` você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*port-value*> | Inteiro | O valor `port` do URI especificado. Se `port` não especificar um valor, retorne a porta padrão do protocolo. |
||||

*Exemplo*

Esse exemplo retorna o valor `port` deste URI:

```
uriPort('http://www.localhost:8080')
```

E retorna este resultado: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Retornar o valor `query` de um URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia de caracteres | O URI cujo valor `query` você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*query-value*> | Cadeia de caracteres | O valor `query` do URI especificado |
||||

*Exemplo*

Esse exemplo retorna o valor `query` deste URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Retornar o valor `scheme` de um URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sim | Cadeia de caracteres | O URI cujo valor `scheme` você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*scheme-value*> | Cadeia de caracteres | O valor `scheme` do URI especificado |
||||

*Exemplo*

Esse exemplo retorna o valor `scheme` deste URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E retorna este resultado: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Retornar o carimbo de data/hora atual.

```
utcNow('<format>')
```

Ou é possível especificar um formato diferente com o parâmetro <*formato*>.


| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | Cadeia de caracteres | Um [especificador de formato único](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | Cadeia de caracteres | A data e hora atual |
||||

*Exemplo 1*

Suponha que hoje é 15 de abril de 2018, às 13h.
Esse exemplo obtém o carimbo de data/hora atual:

```
utcNow()
```

E retorna este resultado: `"2018-04-15T13:00:00.0000000Z"`

*Exemplo 2*

Suponha que hoje é 15 de abril de 2018, às 13h.
Esse exemplo obtém o carimbo de data/hora atual usando o formato "D" opcional:

```
utcNow('D')
```

E retorna este resultado: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variáveis

Retornar o valor de uma variável especificada.

```
variables('<variableName>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Sim | Cadeia de caracteres | O nome da variável cujo valor você deseja |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*variable-value*> | Any | O valor da variável especificada |
||||

*Exemplo*

Suponha que o valor atual para uma variável "numItems" seja 20.
Esse exemplo obtém o valor inteiro para esta variável:

```
variables('numItems')
```

E retorna este resultado: `20`

<a name="workflow"></a>

### <a name="workflow"></a>fluxo de trabalho

Retornar todos os detalhes sobre o próprio fluxo de trabalho durante o tempo de execução.

```
workflow().<property>
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*property*> | Não | Cadeia de caracteres | O nome da propriedade de fluxo de trabalho cujo valor você deseja <p>Um objeto de fluxo de trabalho tem estas propriedades: **name**, **type**, **id**, **location** e **run**. O valor da propriedade **run** também é um objeto que tem estas propriedades: **name**, **type** e **id**. |
|||||

*Exemplo*

Esse exemplo retorna o nome da execução atual de um fluxo de trabalho:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>xml

Retornar a versão XML de uma cadeia de caracteres que contém um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Cadeia de caracteres | A cadeia de caracteres com o objeto JSON a ser convertido <p>O objeto JSON deve ter apenas uma propriedade raiz, que não pode ser uma matriz. <br>Use o caractere de barra invertida (\\) como um caractere de escape para aspas duplas ("). |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*xml-version*> | Objeto | O XML codificado para a cadeia de caracteres ou objeto JSON especificado |
||||

*Exemplo 1*

Esse exemplo cria a versão XML desta cadeia de caracteres, que contém um objeto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E retorna este XML de resultado:

```xml
<name>Sophia Owen</name>
```

*Exemplo 2*

Suponha que você tem este objeto JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Esse exemplo cria o XML para uma cadeia de caracteres que contém este objeto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E retorna este XML de resultado:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Verificar o XML em busca de nós ou valores correspondentes a uma expressão XPath (Linguagem XPath) e retornar esses nós ou valores. Uma expressão XPath, ou apenas "XPath", ajuda você a navegar por uma estrutura de documento XML para poder selecionar nós ou calcular valores no conteúdo XML.

```
xpath('<xml>', '<xpath>')
```

| Parâmetro | Obrigatório | Tipo | DESCRIÇÃO |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Any | A cadeia de caracteres XML na qual pesquisar nós ou valores correspondentes a um valor de expressão XPath |
| <*xpath*> | Sim | Any | A expressão XPath usada para localizar valores ou nós XML correspondentes |
|||||

| Valor de retorno | Tipo | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Um nó XML quando um único nó corresponde à expressão XPath especificada |
| <*value*> | Any | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>- ou - </br>[<*value1*>, <*value2*>, ...] | Array | Uma matriz com valores ou nós XML correspondentes à expressão XPath especificada |
||||

*Exemplo 1*

Esse exemplo localiza nós correspondentes ao nó `<name></name>` nos argumentos especificados e retorna uma matriz com estes valores de nós:

`xpath(xml(parameters('items')), '/produce/item/name')`

Veja os argumentos:

* A cadeia de caracteres "itens", que contém este XML:

  `"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

  O exemplo usa a função [parameters()](#parameters) para obter a cadeia de caracteres XML do argumento "itens", mas também deve converter a cadeia de caracteres em formato XML usando a função [xml()](#xml).

* Esta expressão XPath, passada como uma cadeia de caracteres:

  `"/produce/item/name"`

Veja a matriz de resultado com os nós correspondentes a `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Exemplo 2*

A seguir, no Exemplo 1, Esse exemplo localiza nós correspondentes ao nó `<count></count>` e adiciona esses valores de nó com a função `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E retorna este resultado: `30`

*Exemplo 3*

Para esse exemplo, as duas expressões localizam nós correspondentes ao nó `<location></location>`, nos argumentos especificados, que incluem o XML com um namespace. As expressões usam o caractere de barra invertida (\\) como um caractere de escape para aspas duplas (").

* *Expressão 1*

  `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`

* *Expressão 2*

  `xpath(xml(body('Http')), '/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]')`

Veja os argumentos:

* Esse XML, que inclui o namespace do documento XML, `xmlns="http://contoso.com"`:

  ```xml
  <?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>
  ```

* A expressão XPath aqui:

  * `/*[name()=\"file\"]/*[name()=\"location\"]`

  * `/*[local-name()=\"file\" and namespace-uri()=\"http://contoso.com\"]/*[local-name()=\"location\"]`

Veja o nó de resultado correspondente ao nó `<location></location>`:

```xml
<location xmlns="https://contoso.com">Paris</location>
```

*Exemplo 4*

A seguir, no Exemplo 3, esse exemplo localiza o valor no nó `<location></location>`:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E retorna este resultado: `"Paris"`

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a [Linguagem de Definição de Fluxo de Trabalho](../logic-apps/logic-apps-workflow-definition-language.md)
