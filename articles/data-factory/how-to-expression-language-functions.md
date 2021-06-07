---
title: Como usar parâmetros e expressões no Azure Data Factory
description: Este artigo de instruções fornece informações sobre expressões e funções que você pode usar ao criar data factory entidades.
author: ssabat
ms.author: susabat
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/08/2020
ms.openlocfilehash: c9e1abc5fb6f66981f56bc262319587d9fc4265e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566652"
---
# <a name="how-to-use-parameters-expressions-and-functions-in-azure-data-factory"></a>Como usar parâmetros, expressões e funções no Azure Data Factory

> [!div class="op_single_selector" title1="Selecione a versão do serviço Data Factory que você está usando:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](how-to-expression-language-functions.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Neste documento, nos concentraremos principalmente em conceitos fundamentais de aprendizagem com vários exemplos para explorar a capacidade de criar pipelines de dados com parâmetros dentro de Azure Data Factory. A parametrização e as expressões dinâmicas são adições notáveis ao ADF, pois elas podem economizar bastante tempo e permitir uma solução de extração, transformação, carregamento (ETL) ou extração, carregamento, transformação (ELT) muito mais flexível, o que reduzirá drasticamente o custo da manutenção da solução e acelerará a implementação de novos recursos em pipelines existentes. Esses ganhos são porque a parametrização minimiza a quantidade de codificação rígida e aumenta o número de objetos e processos reutilizáveis em uma solução.

## <a name="azure-data-factory-ui-and-parameters"></a>Interface do usuário data factory do Azure e parâmetros

Se você for novo no Azure data factory o uso de parâmetro na interface do usuário do ADF, examine a [interface de usuário do data Factory para serviços vinculados com parâmetros](./parameterize-linked-services.md#data-factory-ui)  e [a interface de usuário do data Factory para pipeline controlado por metadados com parâmetros](./how-to-use-trigger-parameterization.md#data-factory-ui) para explicação Visual.

## <a name="parameter-and-expression-concepts"></a>Conceitos de parâmetro e expressão 

Você pode usar parâmetros para passar valores externos em pipelines, conjuntos de dados, serviços vinculados e fluxos de dados. Depois que o parâmetro tiver sido passado para o recurso, ele não poderá ser alterado. Ao parametrizar os recursos, você pode reutilizá-los com valores diferentes a cada vez. Os parâmetros podem ser usados individualmente ou como parte de expressões. Os valores JSON na definição podem ser literais ou expressões que são avaliadas no runtime.

Por exemplo:  
  
```json
"name": "value"
```

 ou  
  
```json
"name": "@pipeline().parameters.password"
```

As expressões podem aparecer em qualquer lugar em um valor de cadeia de caracteres JSON e sempre retornam outro valor JSON. Aqui, *password* é um parâmetro de pipeline na expressão. Se um valor JSON for uma expressão, o corpo da expressão será extraído removendo a arroba (\@). Se for necessária uma cadeia de caracteres literal que começa \@, seu escape deverá ser feito usando \@\@. Os exemplos a seguir mostram como as expressões são avaliadas.  
  
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

## <a name="examples-of-using-parameters-in-expressions"></a>Exemplos de como usar parâmetros em expressões 

### <a name="complex-expression-example"></a>Exemplo de expressão complexa
O exemplo abaixo mostra um exemplo complexo que faz referência a um subcampo profundo da saída da atividade. Para fazer referência a um parâmetro de pipeline que é avaliado como um subcampo, use a sintaxe [] em vez do operador ponto (.) (como no caso de subfield1 e subfield2)

`@activity('*activityName*').output.*subfield1*.*subfield2*[pipeline().parameters.*subfield3*].*subfield4*`

### <a name="dynamic-content-editor"></a>Editor de conteúdo dinâmico

O editor de conteúdo dinâmico escapa automaticamente os caracteres no seu conteúdo quando você conclui a edição. Por exemplo, o conteúdo a seguir no editor de conteúdo é uma interpolação de cadeia de caracteres com duas funções de expressão. 

```json
{ 
  "type": "@{if(equals(1, 2), 'Blob', 'Table' )}",
  "name": "@{toUpper('myData')}"
}
```

O editor de conteúdo dinâmico converte o conteúdo acima em expressão `"{ \n  \"type\": \"@{if(equals(1, 2), 'Blob', 'Table' )}\",\n  \"name\": \"@{toUpper('myData')}\"\n}"` . O resultado dessa expressão é uma cadeia de caracteres de formato JSON mostrada abaixo.

```json
{
  "type": "Table",
  "name": "MYDATA"
}
```

### <a name="a-dataset-with--parameters"></a>Um DataSet com parâmetros

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

### <a name="a-pipeline-with--parameters"></a>Um pipeline com parâmetros

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

  
## <a name="calling-functions-within-expressions"></a>Chamando funções dentro de expressões 

Você pode chamar funções dentro de expressões. As seções a seguir fornecem informações sobre as funções que podem ser usados em uma expressão.  

### <a name="string-functions"></a>Funções de cadeia de caracteres  

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

### <a name="collection-functions"></a>Funções de coleção

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

### <a name="logical-functions"></a>Funções lógicas  

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
  
### <a name="conversion-functions"></a>Funções de conversão  

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

### <a name="math-functions"></a>Funções matemáticas  
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
  
### <a name="date-functions"></a>Funções de data  

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

## <a name="detailed-examples-for-practice"></a>Exemplos detalhados de prática

### <a name="detailed-azure-data-factory-copy-pipeline-with-parameters"></a>Pipeline de cópia de data factory do Azure detalhado com parâmetros 

Este [tutorial de passagem de parâmetro do pipeline de cópia do Azure data Factory](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-data-factory-passing-parameters/Azure%20data%20Factory-Whitepaper-PassingParameters.pdf) orienta você sobre como passar parâmetros entre um pipeline e uma atividade, bem como entre as atividades.

### <a name="detailed--mapping-data-flow-pipeline-with-parameters"></a>Pipeline de fluxo de dados de mapeamento detalhado com parâmetros 

Siga [mapear fluxo de dados com parâmetros](./parameters-data-flow.md) para obter um exemplo abrangente de como usar parâmetros no fluxo de dados.

### <a name="detailed-metadata-driven-pipeline-with-parameters"></a>Pipeline controlado por metadados detalhado com parâmetros

Siga o [pipeline controlado por metadados com parâmetros](./how-to-use-trigger-parameterization.md) para saber mais sobre como usar parâmetros para criar pipelines controlados por metadados. Esse é um caso de uso popular para parâmetros.


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de variáveis de sistema que você pode usar em expressões, consulte [Variáveis do sistema](control-flow-system-variables.md).