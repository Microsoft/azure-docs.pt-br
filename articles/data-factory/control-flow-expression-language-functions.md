---
title: Expressão e funções no Azure Data Factory
description: Este artigo fornece informações sobre expressões e funções que você pode usar na criação de entidades do data factory.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 997700b27f52af174dab914097ceeef8d20ff148
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385618"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressão e funções no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](control-flow-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo fornece detalhes sobre expressões e funções suportadas pelo Azure Data Factory. 

## <a name="expressions"></a>Expressões

Os valores JSON na definição podem ser literais ou expressões que são avaliadas no runtime. Por exemplo:  
  
```json
"name": "value"
```

 ou  
  
```json
"name": "@pipeline().parameters.password"
```

As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre retornam outro valor JSON. Se um valor JSON for uma expressão, o corpo da expressão será extraído removendo a arroba (\@). Se for necessária uma cadeia de caracteres literal que começa \@, seu escape deverá ser feito usando \@\@. Os exemplos a seguir mostram como as expressões são avaliadas.  
  
|Valor JSON|Result|  
|----------------|------------|  
|"parameters"|Os “parâmetros” dos caracteres são retornados.|  
|"parameters[1]"|Os “parâmetros[1]” dos caracteres são retornados.|  
|"\@\@"|Uma cadeia de caracteres com 1 caractere que contém ' \@' será retornada.|  
|" \@"|Uma cadeia de caracteres com 2 caracteres que contém ' \@' será retornada.|  
  
 As expressões também podem aparecer dentro de cadeias de caracteres usando um recurso chamado *interpolação de cadeia de caracteres* em que as expressões estão encapsuladas em `@{ ... }`. Por exemplo: `"name" : "First Name: @{pipeline().parameters.firstName} Last Name: @{pipeline().parameters.lastName}"`  
  
 Usando interpolação de cadeia de caracteres, o resultado é sempre uma cadeia de caracteres. Digamos que defini `myNumber` como `42` e `myString` como `foo`:  
  
|Valor JSON|Result|  
|----------------|------------|  
|"\@pipeline().parameters.myString}"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@{pipeline().parameters.myString}"| Retorna `foo` como uma cadeia de caracteres.|  
|"\@pipeline().parameters.myNumber"| Retorna `42` como um *número*.|  
|"\@{pipeline().parameters.myNumber}"| Retorna `42` como uma *cadeia de caracteres*.|  
|"A resposta é: @{pipeline().parameters.myNumber}"| Retorna a cadeia de caracteres `Answer is: 42`.|  
|"\@ concat ('Resposta é:', string (pipeline (). Parameters.myNumber))"| Retorna a cadeia de caracteres `Answer is: 42`|  
|"A resposta é: \@\@{pipeline().parameters.myNumber}"| Retorna a cadeia de caracteres `Answer is: @{pipeline().parameters.myNumber}`.|  
  
## <a name="examples"></a>Exemplos

### <a name="complex-expression-example"></a>Exemplo de expressão complexa
O exemplo abaixo mostra um exemplo complexo que faz referência a um subcampo profundo da saída da atividade. Para fazer referência a um parâmetro de pipeline que é avaliado como um subcampo, use a sintaxe [] em vez do operador ponto (.) (como no caso de subfield1 e subfield2)

@activity('*ActivityName*'). saída. *subfield1*. *subfield2*[pipeline (). Parameters.*subfield3*]. *subfield4*

### <a name="a-dataset-with-a-parameter"></a>Um conjunto de dados com um parâmetro
No exemplo a seguir, o BlobDataset usa um parâmetro denominado **path**. Seu valor é usado para definir um valor para a propriedade **folderPath** usando as expressões: `dataset().path`. 

```json
{
    "name": "BlobDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "@dataset().path"
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            }
        }
    }
}
```

### <a name="a-pipeline-with-a-parameter"></a>Um pipeline com um parâmetro
No exemplo a seguir, o pipeline usa os parâmetros **inputPath** e **outputPath**. O **path** para o conjunto de dados de blob com parâmetros é definido usando os valores desses parâmetros. A sintaxe usada aqui é: `pipeline().parameters.parametername`. 

```json
{
    "name": "Adfv2QuickStartPipeline",
    "properties": {
        "activities": [
            {
                "name": "CopyFromBlobToBlob",
                "type": "Copy",
                "inputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.inputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "BlobDataset",
                        "parameters": {
                            "path": "@pipeline().parameters.outputPath"
                        },
                        "type": "DatasetReference"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                }
            }
        ],
        "parameters": {
            "inputPath": {
                "type": "String"
            },
            "outputPath": {
                "type": "String"
            }
        }
    }
}
```
### <a name="tutorial"></a>Tutorial
Este [tutorial](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) orienta sobre como passar parâmetros entre um pipeline e uma atividade, bem como entre as atividades.

  
## <a name="functions"></a>Funções

Você pode chamar funções dentro de expressões. As seções a seguir fornecem informações sobre as funções que podem ser usados em uma expressão.  

## <a name="string-functions"></a>Funções de cadeia de caracteres  

Para trabalhar com cadeias de caracteres, você pode usar essas funções de cadeia de caracteres e algumas [funções de coleta](#collection-functions).
Funções de cadeia de caracteres só funcionam com cadeias de caracteres.

| Função de cadeia de caracteres | Tarefa |
| --------------- | ---- |
| [concat](control-flow-expression-language-functions.md#concat) | Combinar duas ou mais cadeias de caracteres e retornar a cadeia de caracteres combinada. |
| [endsWith](control-flow-expression-language-functions.md#endswith) | Verificar se uma cadeia de caracteres termina com a subcadeia de caracteres especificada. |
| [guid](control-flow-expression-language-functions.md#guid) | Gerar um GUID (identificador global exclusivo) como uma cadeia de caracteres. |
| [indexOf](control-flow-expression-language-functions.md#indexof) | Retornar a posição inicial de uma subcadeia de caracteres. |
| [lastIndexOf](control-flow-expression-language-functions.md#lastindexof) | Retorna a posição inicial da última ocorrência de uma subcadeia de caracteres. |
| [substitui](control-flow-expression-language-functions.md#replace) | Substituir uma subcadeia de caracteres pela cadeia de caracteres especificada e retornar a cadeia de caracteres atualizada. |
| [split](control-flow-expression-language-functions.md#split) | Retorna uma matriz contendo subcadeias de caracteres, separadas por vírgulas, de uma cadeia de caracteres maior baseada em um caractere delimitador especificado na cadeia de caracteres original. |
| [startsWith](control-flow-expression-language-functions.md#startswith) | Verificar se uma cadeia de caracteres começa com uma subcadeia de caracteres específica. |
| [substring](control-flow-expression-language-functions.md#substring) | Retornar caracteres de uma cadeia de caracteres, começando na posição especificada. |
| [toLower](control-flow-expression-language-functions.md#toLower) | Retornar uma cadeia de caracteres em letras minúsculas. |
| [toUpper](control-flow-expression-language-functions.md#toUpper) | Retornar uma cadeia de caracteres em letras maiúsculas. |
| [cortar](control-flow-expression-language-functions.md#trim) | Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada. |

## <a name="collection-functions"></a>Funções de coleção

Para trabalhar com coleções, matrizes em geral, cadeias de caracteres e, às vezes, dicionários, você pode usar estas funções de coleção.

| Função de coleção | Tarefa |
| ------------------- | ---- |
| [contains](control-flow-expression-language-functions.md#contains) | Verificar se uma coleção tem um item específico. |
| [empty](control-flow-expression-language-functions.md#empty) | Verifique se uma coleção está vazia. |
| [first](control-flow-expression-language-functions.md#first) | Retornar o primeiro item de uma coleção. |
| [intersection](control-flow-expression-language-functions.md#intersection) | Retornar uma coleção que tem *somente* os itens comuns entre as coleções especificadas. |
| [join](control-flow-expression-language-functions.md#join) | Retornar uma cadeia de caracteres que tem *todos* os itens de uma matriz, separados pelo caractere especificado. |
| [last](control-flow-expression-language-functions.md#last) | Retornar o último item de uma coleção. |
| [length](control-flow-expression-language-functions.md#length) | Retornar o número de itens em uma cadeia de caracteres ou matriz. |
| [skip](control-flow-expression-language-functions.md#skip) | Remover itens do início de uma coleção e retornar *todos os outros* itens. |
| [take](control-flow-expression-language-functions.md#take) | Retornar itens do início de uma coleção. |
| [union](control-flow-expression-language-functions.md#union) | Retornar uma coleção que tem *todos* os itens das coleções especificadas. | 

## <a name="logical-functions"></a>Funções lógicas  

Essas funções são úteis em condições e podem ser usadas para avaliar qualquer tipo de lógica.  
  
| Função de comparação lógica | Tarefa |
| --------------------------- | ---- |
| [and](control-flow-expression-language-functions.md#and) | Verificar se todas as expressões são verdadeiras. |
| [equals](control-flow-expression-language-functions.md#equals) | Verificar se os dois valores são equivalentes. |
| [greater](control-flow-expression-language-functions.md#greater) | Verificar se o primeiro valor é maior que o segundo valor. |
| [greaterOrEquals](control-flow-expression-language-functions.md#greaterOrEquals) | Verificar se o primeiro valor é superior ou igual ao segundo. |
| [if](control-flow-expression-language-functions.md#if) | Verificar se uma expressão é verdadeira ou falsa. Com base no resultado, retornar um valor especificado. |
| [less](control-flow-expression-language-functions.md#less) | Verificar se o primeiro valor é menor que o segundo valor. |
| [lessOrEquals](control-flow-expression-language-functions.md#lessOrEquals) | Verificar se o primeiro valor é inferior ou igual ao segundo valor. |
| [not](control-flow-expression-language-functions.md#not) | Verificar se uma expressão é falsa. |
| [or](control-flow-expression-language-functions.md#or) | Verificar se pelo menos uma expressão é verdadeira. |
  
## <a name="conversion-functions"></a>Funções de conversão  

 Essas funções são usadas para converter entre cada um dos tipos nativos no idioma:  
-   string
-   inteiro
-   FLOAT
-   booleano
-   matrizes
-   dicionários

| Função de conversão | Tarefa |
| ------------------- | ---- |
| [array](control-flow-expression-language-functions.md#array) | Retornar uma matriz de uma única entrada especificada. Para várias entradas, confira [createArray](control-flow-expression-language-functions.md#createArray). |
| [base64](control-flow-expression-language-functions.md#base64) | Retornar a versão codificada em Base64 de uma cadeia de caracteres. |
| [base64ToBinary](control-flow-expression-language-functions.md#base64ToBinary) | Retornar a versão binária de uma cadeia de caracteres codificada em Base64. |
| [base64ToString](control-flow-expression-language-functions.md#base64ToString) | Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em Base64. |
| [binary](control-flow-expression-language-functions.md#binary) | Retornar a versão binária de um valor de entrada. |
| [bool](control-flow-expression-language-functions.md#bool) | Retornar a versão booliana de um valor de entrada. |
| [coalesce](control-flow-expression-language-functions.md#coalesce) | Retornar o primeiro valor não nulo de um ou mais parâmetros. |
| [createArray](control-flow-expression-language-functions.md#createArray) | Retornar uma matriz de várias entradas. |
| [dataUri](control-flow-expression-language-functions.md#dataUri) | Retornar o URI de dados de um valor de entrada. |
| [dataUriToBinary](control-flow-expression-language-functions.md#dataUriToBinary) | Retornar a versão binária de um URI de dados. |
| [dataUriToString](control-flow-expression-language-functions.md#dataUriToString) | Retornar a versão de cadeia de caracteres de um URI de dados. |
| [decodeBase64](control-flow-expression-language-functions.md#decodeBase64) | Retornar a versão de cadeia de caracteres de uma cadeia de caracteres codificada em Base64. |
| [decodeDataUri](control-flow-expression-language-functions.md#decodeDataUri) | Retornar a versão binária de um URI de dados. |
| [decodeUriComponent](control-flow-expression-language-functions.md#decodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres de escape por versões decodificadas. |
| [encodeUriComponent](control-flow-expression-language-functions.md#encodeUriComponent) | Retornar uma cadeia de caracteres que substitui caracteres não seguros para URL por caracteres de escape. |
| [float](control-flow-expression-language-functions.md#float) | Retornar um número de ponto flutuante correspondente a um valor de entrada. |
| [int](control-flow-expression-language-functions.md#int) | Retornar a versão em inteiros de uma cadeia de caracteres. |
| [json](control-flow-expression-language-functions.md#json) | Retornar o objeto ou valor de tipo JSON (JavaScript Object Notation) correspondente a uma cadeia de caracteres ou XML. |
| [cadeia de caracteres](control-flow-expression-language-functions.md#string) | Retornar a versão de cadeia de caracteres de um valor de entrada. |
| [uriComponent](control-flow-expression-language-functions.md#uriComponent) | Retornar a versão codificada para URI correspondente a um valor de entrada substituindo caracteres não seguros para URL por caracteres de escape. |
| [uriComponentToBinary](control-flow-expression-language-functions.md#uriComponentToBinary) | Retornar a versão binária de uma cadeia de caracteres codificada como URI. |
| [uriComponentToString](control-flow-expression-language-functions.md#uriComponentToString) | Retornar a versão de cadeia de caracteres correspondente a uma cadeia de caracteres codificada como URI. |
| [xml](control-flow-expression-language-functions.md#xml) | Retornar a versão XML de uma cadeia de caracteres. |
| [xpath](control-flow-expression-language-functions.md#xpath) | Verificar o XML em busca de nós ou valores correspondentes a uma expressão XPath (Linguagem XPath) e retornar esses nós ou valores. |

## <a name="math-functions"></a>Funções matemáticas  
 Essas funções podem ser usadas para qualquer um dos tipos de números: **inteiros** e **floats**.  

| Função matemática | Tarefa |
| ------------- | ---- |
| [adicionar](control-flow-expression-language-functions.md#add) | Retornar o resultado da adição de dois números. |
| [div](control-flow-expression-language-functions.md#div) | Retornar o resultado da divisão de dois números. |
| [max](control-flow-expression-language-functions.md#max) | Retornar o valor mais alto de um conjunto de números em uma matriz. |
| [min](control-flow-expression-language-functions.md#min) | Retornar o valor mais baixo de um conjunto de números ou de uma matriz. |
| [mod](control-flow-expression-language-functions.md#mod) | Retornar o resto da divisão de dois números. |
| [mul](control-flow-expression-language-functions.md#mul) | Retornar o produto da multiplicação de dois números. |
| [rand](control-flow-expression-language-functions.md#rand) | Retornar um número inteiro aleatório de um intervalo especificado. |
| [range](control-flow-expression-language-functions.md#range) | Retornar uma matriz de inteiros que começa em um inteiro especificado. |
| [sub](control-flow-expression-language-functions.md#sub) | Retornar o resultado da subtração do segundo número do primeiro. |
  
## <a name="date-functions"></a>Funções de data  

| Função de data ou hora | Tarefa |
| --------------------- | ---- |
| [addDays](control-flow-expression-language-functions.md#addDays) | Adicionar um número de dias a um carimbo de data/hora. |
| [addHours](control-flow-expression-language-functions.md#addHours) | Adicionar um número de horas a um carimbo de data/hora. |
| [addMinutes](control-flow-expression-language-functions.md#addMinutes) | Adicionar um número de minutos a um carimbo de data/hora. |
| [addSeconds](control-flow-expression-language-functions.md#addSeconds) | Adicionar um número de segundos a um carimbo de data/hora. |
| [addToTime](control-flow-expression-language-functions.md#addToTime) | Adicionar um número de unidades de tempo a um carimbo de data/hora. Confira também [getFutureTime](control-flow-expression-language-functions.md#getFutureTime). |
| [convertFromUtc](control-flow-expression-language-functions.md#convertFromUtc) | Converter um carimbo de data/hora de UTC (Tempo Universal Coordenado) no fuso horário de destino. |
| [convertTimeZone](control-flow-expression-language-functions.md#convertTimeZone) | Converter um carimbo de data/hora do fuso horário de origem no fuso horário de destino. |
| [convertToUtc](control-flow-expression-language-functions.md#convertToUtc) | Converter um carimbo de data/hora do fuso horário de origem em UTC (Tempo Universal Coordenado). |
| [dayOfMonth](control-flow-expression-language-functions.md#dayOfMonth) | Retornar o componente de dia do mês de um carimbo de data/hora. |
| [dayOfWeek](control-flow-expression-language-functions.md#dayOfWeek) | Retornar o componente de dia da semana de um carimbo de data/hora. |
| [dayOfYear](control-flow-expression-language-functions.md#dayOfYear) | Retornar o componente de dia do ano de um carimbo de data/hora. |
| [formatDateTime](control-flow-expression-language-functions.md#formatDateTime) | Retorna o carimbo de data/hora como uma cadeia de caracteres no formato opcional. |
| [getFutureTime](control-flow-expression-language-functions.md#getFutureTime) | Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas. Confira também [addToTime](control-flow-expression-language-functions.md#addToTime). |
| [getPastTime](control-flow-expression-language-functions.md#getPastTime) | Retornar o carimbo de data/hora atual menos as unidades de tempo especificadas. Confira também [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime). |
| [startOfDay](control-flow-expression-language-functions.md#startOfDay) | Retornar o início do dia de um carimbo de data/hora. |
| [startOfHour](control-flow-expression-language-functions.md#startOfHour) | Retornar o início da hora de um carimbo de data/hora. |
| [startOfMonth](control-flow-expression-language-functions.md#startOfMonth) | Retornar o início do mês de um carimbo de data/hora. |
| [subtractFromTime](control-flow-expression-language-functions.md#subtractFromTime) | Subtrair um número de unidades de tempo de um carimbo de data/hora. Confira também [getPastTime](control-flow-expression-language-functions.md#getPastTime). |
| [ticks](control-flow-expression-language-functions.md#ticks) | Retornar o valor de propriedade `ticks` de um carimbo de data/hora especificado. |
| [utcNow](control-flow-expression-language-functions.md#utcNow) | Retornar o carimbo de data/hora atual como uma cadeia de caracteres. |

## <a name="function-reference"></a>Referência de função

Esta seção lista todas as funções disponíveis em ordem alfabética.

<a name="add"></a>

### <a name="add"></a>add

Retornar o resultado da adição de dois números.

```
add(<summand_1>, <summand_2>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Sim | Integer, Float ou misto | Os números a serem adicionados |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*days*> | Sim | Integer | O número positivo ou negativo de dias a ser adicionado |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número de dias especificado  |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*hours*> | Sim | Integer | O número positivo ou negativo de horas a ser adicionado |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número de horas especificado  |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*minutes*> | Sim | Integer | O número positivo ou negativo de minutos a ser adicionado |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número de minutos especificado |
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

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Adicionar um número de segundos a um carimbo de data/hora.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*seconds*> | Sim | Integer | O número positivo ou negativo de segundos a ser adicionado |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número de segundos especificado  |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | Integer | O número de unidades de tempo especificadas a ser adicionado |
| <*timeUnit*> | Sim | String | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora mais o número de unidades de tempo especificado  |
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

Verifique se ambas as expressões são verdadeiras.
Retorna true quando ambas as expressões são true ou retorna false quando pelo menos uma expressão é false.

```
and(<expression1>, <expression2>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, <*expression2*> | Sim | Boolean | As expressões a serem verificadas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | -----| ----------- |
| true ou false | Boolean | Retorna true quando ambas as expressões são true. Retornará false quando pelo menos uma expressão for false. |
||||

*Exemplo 1*

Esses exemplos verificam se os valores Boolianos especificados são verdadeiros:

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

Esses exemplos verificam se as expressões especificadas são ambas verdadeiras:

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

### <a name="array"></a>matriz

Retornar uma matriz de uma única entrada especificada.
Para várias entradas, consulte [createArray()](#createArray).

```
array('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres para criar uma matriz |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres de entrada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*base64-string*> | String | A versão codificada em Base64 da cadeia de caracteres de entrada |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres codificada em Base64 a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | String | A versão binária da cadeia de caracteres codificada em Base64 |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres codificada em Base64 a ser decodificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | A versão da cadeia de caracteres de uma cadeia de caracteres codificada em Base64 |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | String | A versão binária para a cadeia de caracteres especificada |
||||

*Exemplo*

Esse exemplo converte a cadeia de caracteres "hello" em uma cadeia de caracteres binária:

```
binary('hello')
```

E retorna este resultado:

`"0110100001100101011011000110110001101111"`

<a name="bool"></a>

### <a name="bool"></a>bool

Retornar a versão booliana de um valor.

```
bool(<value>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Qualquer | O valor a ser convertido |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Sim | Qualquer um, pode misturar tipos | Um ou mais itens para verificar se são nulos |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Qualquer | O primeiro item ou valor não nulo. Se todos os parâmetros forem nulos, essa função retornará null. |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Sim | String | Pelo menos duas cadeias de caracteres a serem combinadas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*text1text2...* > | String | A cadeia de caracteres criada das cadeias de caracteres de entrada combinadas |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String, Array ou Dictionary | A coleção a ser verificada |
| <*value*> | Sim | String, Array ou Dictionary, respectivamente | O item a ser localizado |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário de destino. Para obter nomes de fuso horário, confira [Valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Talvez seja necessário remover alguma pontuação do nome do fuso horário. |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de data/hora convertido na região do fuso horário |
||||

*Exemplo 1*

Esse exemplo converte um carimbo de data/hora no fuso horário especificado:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E retorna este resultado: `"2018-01-01T00:00:00Z"`

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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário de origem. Para obter nomes de fuso horário, confira [Valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Talvez seja necessário remover alguma pontuação do nome do fuso horário. |
| <*destinationTimeZone*> | Sim | String | O nome do fuso horário de destino. Para obter nomes de fuso horário, confira [Valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Talvez seja necessário remover alguma pontuação do nome do fuso horário. |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de data/hora convertido na região do fuso horário |
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
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E retorna este resultado: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Converter um carimbo de data/hora do fuso horário de origem em UTC (Tempo Universal Coordenado).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*sourceTimeZone*> | Sim | String | O nome do fuso horário de origem. Para obter nomes de fuso horário, confira [Valores de índice de fuso horário da Microsoft](https://support.microsoft.com/help/973627/microsoft-time-zone-index-values). Talvez seja necessário remover alguma pontuação do nome do fuso horário. |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | String | O carimbo de data/hora convertido no UTC |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Sim | Qualquer um, mas não misto | Pelo menos dois itens para criar a matriz |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*data-uri*> | String | O URI dos dados da cadeia de caracteres de entrada |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | O URI de dados a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | A versão binária do URI de dados |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | O URI de dados a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | String | A versão de cadeia de caracteres do URI de dados |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Integer | O dia do mês do carimbo de data/hora especificado |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | O dia da semana do carimbo de data/hora especificado em que domingo é 0, segunda-feira é 1 e assim por diante |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | O dia do ano do carimbo de data/hora especificado |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres codificada em Base64 a ser decodificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | String | A versão da cadeia de caracteres de uma cadeia de caracteres codificada em Base64 |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres do URI de dados a ser decodificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | String | A versão binária de uma cadeia de caracteres do URI de dados |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres com os caracteres de escape a ser decodificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | A cadeia de caracteres atualizada com os caracteres de escape decodificados |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | Integer ou Float | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | Integer ou Float | O número que divide o *dividendo*, mas não pode ser 0 |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer | O resultado inteiro da divisão do primeiro número pelo segundo |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres a ser convertida em um formato codificado para URI |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | A cadeia de caracteres codificada para URI com caracteres de escape |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String, Array ou Object | A coleção a ser verificada |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | String | A subcadeia de caracteres final a ser localizada |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Sim | Vários | Os valores, expressões ou objetos a serem comparados |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção na qual localizar o primeiro item |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Qualquer | O primeiro item na coleção |
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

### <a name="float"></a>FLOAT

Converter uma versão de cadeia de caracteres para um número de ponto flutuante em um número de ponto flutuante real.

```
float('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres que tem um número de ponto flutuante válido a ser convertido |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | String | O carimbo de data/hora atualizado no formato especificado |
||||

*Exemplo*

Esse exemplo converte um carimbo de data/hora no formato especificado:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E retorna este resultado: `"2018-03-15T12:00:00"`

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Retornar o carimbo de data/hora atual mais as unidades de tempo especificadas.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Integer | O número de unidades de tempo especificadas a ser adicionado |
| <*timeUnit*> | Sim | String | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora atual mais o número de unidades de tempo especificado |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sim | Integer | O número de unidades de tempo especificadas a serem subtraídas |
| <*timeUnit*> | Sim | String | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora atual menos o número de unidades de tempo especificado |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é maior do que o segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é superior ou igual ao segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O valor de comparação |
|||||

| Valor retornado | Type | Descrição |
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

### <a name="guid"></a>guid

Gere um GUID (identificador global exclusivo) como uma cadeia de caracteres, por exemplo, "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

Além disso, é possível especificar um formato diferente para o GUID que não seja o formato padrão, "D", que é de 32 dígitos separados por hifens.

```
guid('<format>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | String | Um único [especificador de formato](/dotnet/api/system.guid.tostring) para o GUID retornado. Por padrão, o formato é "D", mas é possível usar "N", "D", "B", "P" ou "X". |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*GUID-value*> | String | Um GUID gerado aleatoriamente |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Boolean | A expressão a ser verificada |
| <*valueIfTrue*> | Sim | Qualquer | O valor a ser retornado quando a expressão for verdadeira |
| <*valueIfFalse*> | Sim | Qualquer | O valor a ser retornado quando a expressão for falsa |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Qualquer | O valor especificado retornado dependendo se a expressão é verdadeira ou falsa |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | String | A subcadeia de caracteres a ser localizada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | O valor de índice ou a posição inicial da subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for localizada, retornará o número -1. |
||||

*Exemplo*

Esse exemplo localiza o valor de índice inicial para a subcadeia de caracteres "mundo" na cadeia de caracteres "olá, mundo":

```
indexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="int"></a>

### <a name="int"></a>INT

Retornar a versão em inteiros de uma cadeia de caracteres.

```
int('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | A versão em inteiros da cadeia de caracteres especificada |
||||

*Exemplo*

Esse exemplo cria uma versão em inteiros da cadeia de caracteres "10":

```
int('10')
```

E retorna este resultado: `10`

<a name="json"></a>

### <a name="json"></a>json

Retornar o objeto ou valor de tipo JSON (JavaScript Object Notation) correspondente a uma cadeia de caracteres ou XML.

```
json('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String ou XML | A cadeia de caracteres ou XML a ser convertido |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Sim | Matriz ou objeto, mas não ambos | As coleções da qual você deseja *somente* os itens comuns |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | Array | A matriz que tem os itens a serem ingressados |
| <*delimiter*> | Sim | String | O separador exibido entre cada caractere na cadeia de caracteres resultante |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | String | A cadeia de caracteres resultante criada com base em todos os itens na matriz especificada |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção na qual localizar o último item |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem a subcadeia de caracteres a ser localizada |
| <*searchText*> | Sim | String | A subcadeia de caracteres a ser localizada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | A posição inicial ou valor de índice da última ocorrência da subcadeia de caracteres especificada. <p>Se a cadeia de caracteres não for localizada, retornará o número -1. |
||||

*Exemplo*

Este exemplo encontra o valor de índice inicial para a última ocorrência da subcadeia de caracteres "mundo" na cadeia de caracteres "olá, mundo":

```
lastIndexOf('hello world', 'world')
```

E retorna este resultado: `6`

<a name="length"></a>

### <a name="length"></a>comprimento

Retornar o número de itens em uma coleção.

```
length('<collection>')
length([<collection>])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção com os itens a serem contados |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | O número de itens na coleção |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é menor do que o segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O item de comparação |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Integer, Float ou String | O primeiro valor para verificar se é inferior ou igual ao segundo |
| <*compareTo*> | Sim | Integer, Float ou String, respectivamente | O item de comparação |
|||||

| Valor retornado | Type | Descrição |
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

<a name="max"></a>

### <a name="max"></a>max

Retornar o maior valor de uma lista ou matriz com números inclusivos em ambas as extremidades.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | Integer, Float ou ambos | O conjunto de números do qual você deseja o valor mais alto |
| [<*number1*>, <*number2*>, ...] | Sim | Array – Integer, Float ou ambos | A matriz de números da qual você deseja o valor mais alto |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sim | Integer, Float ou ambos | O conjunto de números do qual você deseja o valor mais baixo |
| [<*number1*>, <*number2*>, ...] | Sim | Array – Integer, Float ou ambos | A matriz de números da qual você deseja o valor mais baixo |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sim | Integer ou Float | O número a ser dividido pelo *divisor* |
| <*divisor*> | Sim | Integer ou Float | O número que divide o *dividendo*, mas não pode ser 0. |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sim | Integer ou Float | O número a ser multiplicado por *multiplicand2* |
| <*multiplicand2*> | Sim | Integer ou Float | O número que multiplica *multiplicand1* |
|||||

| Valor retornado | Type | Descrição |
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

<a name="not"></a>

### <a name="not"></a>não

Verificar se uma expressão é falsa.
Retornará true quando a expressão for falsa ou retornará false quando for verdadeira.

```json
not(<expression>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sim | Boolean | A expressão a ser verificada |
|||||

| Valor retornado | Type | Descrição |
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
Retornar true quando pelo menos uma expressão for true ou retornar false quando ambos forem false.

```
or(<expression1>, <expression2>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*expressão1*>, <*expression2*> | Sim | Boolean | As expressões a serem verificadas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| true ou false | Boolean | Retornará true quando pelo menos uma expressão for verdadeira. Retorna false quando ambas as expressões são false. |
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

<a name="rand"></a>

### <a name="rand"></a>rand

Retornar um inteiro aleatório de um intervalo especificado, inclusivo apenas na extremidade inicial.

```
rand(<minValue>, <maxValue>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sim | Integer | O menor inteiro no intervalo |
| <*maxValue*> | Sim | Integer | O inteiro que segue o inteiro mais alto no intervalo que a função pode retornar |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | O inteiro aleatório retornado do intervalo especificado |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sim | Integer | Um valor inteiro que inicia a matriz como o primeiro item |
| <*count*> | Sim | Integer | O número de inteiros na matriz |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem a subcadeia de caracteres a ser substituída |
| <*oldText*> | Sim | String | A subcadeia de caracteres a ser substituída |
| <*newText*> | Sim | String | A cadeia de caracteres de substituição |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-text*> | String | A cadeia de caracteres atualizada após substituir a subcadeia de caracteres <p>Se a subcadeia de caracteres não for localizada, retorne a cadeia de caracteres original. |
||||

*Exemplo*

Esse exemplo localiza a subcadeia de caracteres "antiga" em "a antiga cadeia de caracteres" e substitui "antiga" por "nova":

```
replace('the old string', 'old', 'new')
```

E retorna este resultado: `"the new string"`

<a name="skip"></a>

### <a name="skip"></a>skip

Remover itens do início de uma coleção e retornar *todos os outros* itens.

```
skip([<collection>], <count>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | Array | A coleção cujos itens você deseja remover |
| <*count*> | Sim | Integer | Um inteiro positivo para o número de itens a ser removido no início |
|||||

| Valor retornado | Type | Descrição |
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

Retorna uma matriz que contém substrings separadas por vírgulas com base no caractere delimitador especificado na cadeia de caracteres original.

```
split('<text>', '<delimiter>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres para separar em subcadeias de caracteres com base no delimitador especificado na cadeia de caracteres original |
| <*delimiter*> | Sim | String | O caractere na cadeia de caracteres original para usar como delimitador |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora especificado, mas começando na marca de zero hora para o dia |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora especificado, mas começando na marca de zero minuto para a hora |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora especificado, mas começando no primeiro dia do mês na marca de zero hora |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser verificada |
| <*searchText*> | Sim | String | A cadeia de caracteres inicial a ser localizada |
|||||

| Valor retornado | Type | Descrição |
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

### <a name="string"></a>string

Retornar a versão de cadeia de caracteres para um valor.

```
string(<value>)
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | Qualquer | O valor a ser convertido |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*string-value*> | String | A versão de cadeia de caracteres do valor especificado |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sim | Integer ou Float | O número do qual subtrair o *subtraendo* |
| <*subtrahend*> | Sim | Integer ou Float | O número a ser subtraído do *minuendo* |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres cujos caracteres você deseja |
| <*startIndex*> | Sim | Integer | Um número positivo igual ou superior a 0 que você deseja usar como a posição inicial ou o valor do índice |
| <*length*> | Sim | Integer | Um número positivo de caracteres que você deseja na subcadeia de caracteres |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*substring-result*> | String | Uma subcadeia de caracteres com o número de caracteres especificado, começando na posição de índice especificada na cadeia de caracteres de origem |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | Uma cadeia de caracteres que contém o carimbo de data/hora |
| <*interval*> | Sim | Integer | O número de unidades de tempo especificadas a serem subtraídas |
| <*timeUnit*> | Sim | String | Unidade de tempo a ser usado com *intervalo*: "Segundo", "Minuto", "Hora", "Dia", "Semana", "Mês", "Ano" |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | String | O carimbo de data/hora menos o número de unidades de tempo especificado |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sim | String ou Array | A coleção cujos itens você deseja |
| <*count*> | Sim | Integer | Um inteiro positivo para o número de itens que você deseja no início |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sim | String | A cadeia de caracteres de um carimbo de data/hora |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | O número de tiques desde o carimbo de data/hora especificado |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Retornar uma cadeia de caracteres em letras minúsculas. Se um caractere na cadeia de caracteres não tiver uma versão em minúsculas, esse caractere permanecerá inalterado na cadeia de caracteres retornada.

```
toLower('<text>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser retornada no formato em minúsculas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | String | A cadeia de caracteres original no formato em minúsculas |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres a ser retornada no formato em maiúsculas |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | String | A cadeia de caracteres original no formato em maiúsculas |
||||

*Exemplo*

Esse exemplo converte essa cadeia de caracteres em maiúsculas:

```
toUpper('Hello World')
```

E retorna este resultado: `"HELLO WORLD"`

<a name="trim"></a>

### <a name="trim"></a>cortar

Remover espaços em branco à esquerda e à direita de uma cadeia de caracteres e retornar a cadeia de caracteres atualizada.

```
trim('<text>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sim | String | A cadeia de caracteres que tem o espaço em branco à esquerda e à direita a ser removida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*updatedText*> | String | Uma versão atualizada da cadeia de caracteres original sem espaço em branco à esquerda ou à direita |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Sim | Matriz ou objeto, mas não ambos | As coleções das quais você deseja *todos* os itens |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres a ser convertida em um formato codificado para URI |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | String | A cadeia de caracteres codificada para URI com caracteres de escape |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres codificada para URI a ser convertida |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | String | A versão binária da cadeia de caracteres codificada para URI. O conteúdo binário é codificado em Base64 e representado por `$content`. |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres codificada para URI a ser decodificada |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | String | A versão decodificada para a cadeia de caracteres codificada para URI |
||||

*Exemplo*

Esse exemplo cria a versão de cadeia de caracteres decodificada para esta cadeia de caracteres codificada para URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E retorna este resultado: `"https://contoso.com"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Retornar o carimbo de data/hora atual.

```
utcNow('<format>')
```

Ou é possível especificar um formato diferente com o parâmetro <*formato*>.

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*format*> | Não | String | Um [especificador de formato único](/dotnet/standard/base-types/standard-date-and-time-format-strings) ou um [padrão de formato personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings). O formato padrão do carimbo de data/hora é ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), compatível com a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e que preserva informações de fuso horário. |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | String | A data e hora atual |
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

<a name="xml"></a>

### <a name="xml"></a>Xml

Retornar a versão XML de uma cadeia de caracteres que contém um objeto JSON.

```
xml('<value>')
```

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sim | String | A cadeia de caracteres com o objeto JSON a ser convertido <p>O objeto JSON deve ter apenas uma propriedade raiz, que não pode ser uma matriz. <br>Use o caractere de barra invertida (\\) como um caractere de escape para aspas duplas ("). |
|||||

| Valor retornado | Type | Descrição |
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

| Parâmetro | Obrigatório | Type | Descrição |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sim | Qualquer | A cadeia de caracteres XML na qual pesquisar nós ou valores correspondentes a um valor de expressão XPath |
| <*xpath*> | Sim | Qualquer | A expressão XPath usada para localizar valores ou nós XML correspondentes |
|||||

| Valor retornado | Type | Descrição |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Um nó XML quando um único nó corresponde à expressão XPath especificada |
| <*value*> | Qualquer | O valor de um nó XML quando apenas um único valor corresponde à expressão XPath especificada |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-ou- </br>[<*value1*>, <*value2*>, ...] | Array | Uma matriz com valores ou nós XML correspondentes à expressão XPath especificada |
||||

*Exemplo 1*

A seguir, no Exemplo 1, Esse exemplo localiza nós correspondentes ao nó `<count></count>` e adiciona esses valores de nó com a função `sum()`:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

E retorna este resultado: `30`

*Exemplo 2*

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

*Exemplo 3*

A seguir, no Exemplo 3, esse exemplo localiza o valor no nó `<location></location>`:

`xpath(xml(body('Http')), 'string(/*[name()=\"file\"]/*[name()=\"location\"])')`

E retorna este resultado: `"Paris"`

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de variáveis de sistema que você pode usar em expressões, consulte [Variáveis do sistema](control-flow-system-variables.md).
