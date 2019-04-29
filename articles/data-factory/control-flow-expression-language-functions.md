---
title: Expressão e funções no Azure Data Factory | Microsoft Docs
description: Este artigo fornece informações sobre expressões e funções que você pode usar na criação de entidades do data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 8a2a080ee87d48d25d7d793ca0aca463f25e52eb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60808833"
---
# <a name="expressions-and-functions-in-azure-data-factory"></a>Expressão e funções no Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-functions-variables.md)
> * [Versão atual](control-flow-expression-language-functions.md)

Este artigo fornece detalhes sobre expressões e funções suportadas pelo Azure Data Factory. 

## <a name="introduction"></a>Introdução
Os valores JSON na definição podem ser literais ou expressões que são avaliadas no tempo de execução. Por exemplo:   
  
```json
"name": "value"
```

 (ou)  
  
```json
"name": "@pipeline().parameters.password"
```

## <a name="expressions"></a>Expressões  
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
  
### <a name="examples"></a>Exemplos

#### <a name="a-dataset-with-a-parameter"></a>Um conjunto de dados com um parâmetro
No exemplo a seguir, o BlobDataset usa um parâmetro denominado **path**. Seu valor é usado para definir um valor para a propriedade **folderPath** usando as seguintes expressões: `@{dataset().path}`. 

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

#### <a name="a-pipeline-with-a-parameter"></a>Um pipeline com um parâmetro
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
  
## <a name="functions"></a>Funções  
 Você pode chamar funções dentro de expressões. As seções a seguir fornecem informações sobre as funções que podem ser usados em uma expressão.  

## <a name="string-functions"></a>Funções de cadeia de caracteres  
 As funções a seguir se aplicam somente a cadeias de caracteres. Você também pode usar um número das funções de coleção em cadeias de caracteres.  
  
|Nome da função|DESCRIÇÃO|  
|-------------------|-----------------|  
|concat|Combina qualquer número de cadeias de caracteres. Por exemplo, se parameter1 for `foo,`, a expressão a seguir retornará `somevalue-foo-somevalue`: `concat('somevalue-',pipeline().parameters.parameter1,'-somevalue')`<br /><br /> **Número do parâmetro**: 1 ... *n*<br /><br /> **Nome**: Cadeia de caracteres *n*<br /><br /> **Descrição**: Obrigatório. As cadeias de caracteres para combinar em uma única cadeia de caracteres.|  
|substring|Retorna um subconjunto de caracteres de uma cadeia de caracteres. Por exemplo, a expressão a seguir:<br /><br /> `substring('somevalue-foo-somevalue',10,3)`<br /><br /> retorna:<br /><br /> `foo`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres original da qual é obtida a subcadeia de caracteres.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Índice inicial<br /><br /> **Descrição**: Obrigatório. O índice de onde a subcadeia de caracteres começa no parâmetro 1.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Comprimento<br /><br /> **Descrição**: Obrigatório. Comprimento da subcadeia de caracteres.|  
|substituir|Substitui uma cadeia de caracteres por uma determinada sequência de caracteres. Por exemplo, a expressão:<br /><br /> `replace('the old string', 'old', 'new')`<br /><br /> retorna:<br /><br /> `the new string`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: cadeia de caracteres<br /><br /> **Descrição**: Obrigatório.  Se parâmetro 2 for encontrado no parâmetro 1, a cadeia de caracteres pesquisada para o parâmetro 2 e atualizada com o parâmetro 3.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres antiga<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres para substituir o parâmetro 3, quando uma correspondência é encontrada no parâmetro 1<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Nova cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres usada para substituir a cadeia de caracteres no parâmetro 2, quando uma correspondência é encontrada no parâmetro 1.|  
|GUID| Gera uma cadeia de caracteres global exclusiva (também conhecida como guid). Por exemplo, a saída a seguir pode ser gerada `c2ecc88d-88c8-4096-912c-d6f2e2b138ce`:<br /><br /> `guid()`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Formatar<br /><br /> **Descrição**: Opcional. Um especificador de formato único que indica [como formatar o valor desse Guid](https://msdn.microsoft.com/library/97af8hh4%28v=vs.110%29.aspx). O parâmetro de formato pode ser "N", "D", "B", "P" ou "X". Se o formato não for fornecido, "D" é usado.|  
|toLower|Converte uma cadeia de caracteres em letras minúsculas. Por exemplo, o seguinte retorna `two by two is four`: `toLower('Two by Two is Four')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres a ser convertida em letras minúsculas. Se um caractere na cadeia de caracteres não tiver um equivalente em letras minúsculas, ele será incluído inalterado na cadeia de caracteres retornada.|  
|toUpper|Converte uma cadeia de caracteres em letras maiúsculas. Por exemplo, a expressão a seguir retorna `TWO BY TWO IS FOUR`: `toUpper('Two by Two is Four')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres a ser convertida em letras maiúsculas. Se um caractere na cadeia de caracteres não tiver um equivalente em letras maiúsculas, ele será incluído inalterado na cadeia de caracteres retornada.|  
|indexof|Encontra o índice de um valor dentro de uma cadeia de caracteres sem considerar letras maiúsculas e minúsculas. Por exemplo, a expressão a seguir retorna `7`: `indexof('hello, world.', 'world')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. O valor a ter o índice pesquisado.|  
|lastindexof|Encontra o último índice de um valor dentro de uma cadeia de caracteres sem considerar letras maiúsculas e minúsculas. Por exemplo, a expressão a seguir retorna `3`: `lastindexof('foofoo', 'foo')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. O valor a ter o índice pesquisado.|  
|startswith|Verifica se a cadeia de caracteres começa com um valor sem considerar letras maiúsculas e minúsculas. Por exemplo, a expressão a seguir retorna `true`: `startswith('hello, world', 'hello')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. O possível valor inicial da cadeia de caracteres.|  
|endswith|Verifica se a cadeia de caracteres termina com um valor sem considerar letras maiúsculas e minúsculas. Por exemplo, a expressão a seguir retorna `true`: `endswith('hello, world', 'world')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres que pode conter o valor.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. O possível valor final da cadeia de caracteres.|  
|split|Divide a cadeia de caracteres usando um separador. Por exemplo, a expressão a seguir retorna `["a", "b", "c"]`: `split('a;b;c',';')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres que será dividida.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. O separador.|  
  
  
## <a name="collection-functions"></a>Funções de coleção  
 Essas funções operam em coleções como matrizes, cadeias de caracteres e, às vezes, dicionários.  
  
|Nome da função|DESCRIÇÃO|  
|-------------------|-----------------|  
|contains|Retorna true se o dicionário contém uma chave, se a lista contém um valor ou a cadeia de caracteres contém uma subcadeia de caracteres. Por exemplo, a expressão a seguir retorna `true:``contains('abacaba','aca')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Dentro da coleção<br /><br /> **Descrição**: Obrigatório. A coleção a ser pesquisada.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Localizar o objeto<br /><br /> **Descrição**: Obrigatório. O objeto a ser encontrado dentro de **Na coleção**.|  
|length|Retorna o número de elementos em uma matriz ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `3`: `length('abc')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Obrigatório. A coleção para obter o comprimento.|  
|empty|Retornará true se o objeto, matriz ou cadeia de caracteres estiverem vazios. Por exemplo, a expressão a seguir retorna `true`:<br /><br /> `empty('')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Obrigatório. A coleção que será verificada para saber se está vazia.|  
|interseção|Retorna uma única matriz ou objeto com os elementos comuns entre as matrizes ou objetos passados para ele. Por exemplo, essa função retornará `[1, 2]`:<br /><br /> `intersection([1, 2, 3], [101, 2, 1, 10],[6, 8, 1, 2])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma mistura destes). Se houver dois objetos com o mesmo nome, o último objeto com esse nome aparece no objeto final.<br /><br /> **Número do parâmetro**: 1 ... *n*<br /><br /> **Nome**: Coleção *n*<br /><br /> **Descrição**: Obrigatório. As coleções a serem avaliadas. Um objeto deve estar em todas as coleções passadas para aparecer no resultado.|  
|union|Retorna uma única matriz ou objeto com todos os elementos que estão em uma matriz ou objeto passado para ela. Por exemplo, essa função retorna `[1, 2, 3, 10, 101]:`<br /><br /> :  `union([1, 2, 3], [101, 2, 1, 10])`<br /><br /> Os parâmetros para a função podem ser um conjunto de objetos ou um conjunto de matrizes (não uma mistura destes). Se houver dois objetos com o mesmo nome na saída final, o último objeto com esse nome aparece no objeto final.<br /><br /> **Número do parâmetro**: 1 ... *n*<br /><br /> **Nome**: Coleção *n*<br /><br /> **Descrição**: Obrigatório. As coleções a serem avaliadas. Um objeto que aparece em qualquer uma das coleções aparece no resultado.|  
|first|Retorna o primeiro elemento da matriz ou da cadeia de caracteres passada. Por exemplo, essa função retornará `0`:<br /><br /> `first([0,2,3])`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Obrigatório. A coleção que possui o primeiro objeto a ser retirado.|  
|last|Retorna o último elemento da matriz ou da cadeia de caracteres passada. Por exemplo, essa função retornará `3`:<br /><br /> `last('0123')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Obrigatório. A coleção que possui o último objeto a ser retirado.|  
|take|Retorna os primeiros elementos **Count** da matriz ou da cadeia de caracteres passada, por exemplo, essa função retorna `[1, 2]`: `take([1, 2, 3, 4], 2)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Obrigatório. A coleção da qual obter os primeiros objetos **Count**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Contagem<br /><br /> **Descrição**: Obrigatório. O número de objetos a serem retirados da **Coleção**. Deve ser um número inteiro positivo.|  
|skip|Retorna os elementos da matriz começando pelo índice **Count**, por exemplo, essa função retorna `[3, 4]`:<br /><br /> `skip([1, 2 ,3 ,4], 2)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção<br /><br /> **Descrição**: Obrigatório. A coleção onde serão ignorados os primeiros objetos da **Contagem**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Contagem<br /><br /> **Descrição**: Obrigatório. O número de objetos a serem removidos da frente da **Coleção**. Deve ser um número inteiro positivo.|  
  
## <a name="logical-functions"></a>Funções lógicas  
 Essas funções são úteis em condições e podem ser usadas para avaliar qualquer tipo de lógica.  
  
|Nome da função|DESCRIÇÃO|  
|-------------------|-----------------|  
|equals|Retorna true se dois valores são iguais. Por exemplo, se parameter1 for foo, a expressão a seguir retornará `true`: `equals(pipeline().parameters.parameter1), 'foo')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Obrigatório. O objeto a ser comparado com o **Objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Obrigatório. O objeto a ser comparado ao **Objeto 1**.|  
|less|Retorna true se o primeiro argumento for menor do que o segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `true`: `less(10,100)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é menor que o **Objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é maior que o **Objeto 1**.|  
|lessOrEquals|Retorna true se o primeiro argumento for menor ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `true`: `lessOrEquals(10,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é menor ou igual ao **Objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é maior ou igual ao **Objeto 1**.|  
|greater|Retorna true se o primeiro argumento for maior do que o segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `false`: `greater(10,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é maior que o **Objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é menor que o **Objeto 1**.|  
|greaterOrEquals|Retorna true se o primeiro argumento for maior ou igual ao segundo. Observe que os valores só podem ser do tipo inteiro, float ou cadeia de caracteres. Por exemplo, a expressão a seguir retorna `false`: `greaterOrEquals(10,100)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Objeto 1<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é maior ou igual ao **Objeto 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Objeto 2<br /><br /> **Descrição**: Obrigatório. O objeto para verificar se é menor ou igual ao **Objeto 1**.|  
|e|Retornará true se ambos os parâmetros forem verdadeiros. Ambos os argumentos precisam ser boolianos. O seguinte retorna `false`: `and(greater(1,10),equals(0,0))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Booliano 1<br /><br /> **Descrição**: Obrigatório. O primeiro argumento deve ser `true`.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Booliano 2<br /><br /> **Descrição**: Obrigatório. O segundo argumento deve ser `true`.|  
|ou o|Retornará true se um dos parâmetros for verdadeiros. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`: `or(greater(1,10),equals(0,0))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Booliano 1<br /><br /> **Descrição**: Obrigatório. O primeiro argumento pode ser `true`.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Booliano 2<br /><br /> **Descrição**: Obrigatório. O segundo argumento pode ser `true`.|  
|não|Retornará true se o parâmetro for `false`. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`: `not(contains('200 Success','Fail'))`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Boolean<br /><br /> **Descrição**: Retornará true se o parâmetro for `false`. Ambos os argumentos precisam ser boolianos. O seguinte retorna `true`: `not(contains('200 Success','Fail'))`|  
|if|Retornará um valor especificado com base em se a expressão forneceu resultados em `true` ou `false`.  Por exemplo, o seguinte retorna `"yes"`: `if(equals(1, 1), 'yes', 'no')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Expression<br /><br /> **Descrição**: Obrigatório. Um valor booliano que determina qual valor é retornado pela expressão.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: True<br /><br /> **Descrição**: Obrigatório. O valor a ser retornado se a expressão for `true`.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Falso<br /><br /> **Descrição**: Obrigatório. O valor a ser retornado se a expressão for `false`.|  
  
## <a name="conversion-functions"></a>Funções de conversão  
 Essas funções são usadas para converter entre cada um dos tipos nativos no idioma:  
  
-   string  
  
-   inteiro  
  
-   flutuante  
  
-   boolean  
  
-   matrizes  
  
-   dicionários  
  
|Nome da função|DESCRIÇÃO|  
|-------------------|-----------------|  
|int|Converta o parâmetro em um inteiro. Por exemplo, a expressão a seguir retorna 100 como um número, em vez de como uma cadeia de caracteres: `int('100')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Obrigatório. O valor que é convertido em um inteiro.|  
|string|Converta o parâmetro em uma cadeia de caracteres. Por exemplo, a expressão a seguir retorna `'10'`:  `string(10)` Você também pode converter um objeto em uma cadeia de caracteres, por exemplo, se o parâmetro **foo** for um objeto com uma propriedade `bar : baz`, o seguinte retornará `{"bar" : "baz"}` `string(pipeline().parameters.foo)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Obrigatório. O valor que é convertido em uma cadeia de caracteres.|  
|json|Converter o parâmetro para um valor de tipo JSON. É o oposto de cadeia de caracteres(). Por exemplo, a expressão a seguir retorna `[1,2,3]` como uma matriz, em vez de uma cadeia de caracteres:<br /><br /> `json('[1,2,3]')`<br /><br /> Da mesma forma, você pode converter uma cadeia de caracteres em um objeto. Por exemplo, `json('{"bar" : "baz"}')` retorna:<br /><br /> `{ "bar" : "baz" }`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres que é convertida em um valor de tipo nativo.<br /><br /> A função json dá suporte a entrada xml também. Por exemplo, o valor do parâmetro de:<br /><br /> `<?xml version="1.0"?> <root>   <person id='1'>     <name>Alan</name>     <occupation>Engineer</occupation>   </person> </root>`<br /><br /> é convertido para o json a seguir:<br /><br /> `{ "?xml": { "@version": "1.0" },   "root": {     "person": [     {       "@id": "1",       "name": "Alan",       "occupation": "Engineer"     }   ]   } }`|  
|flutuante|Converte o argumento do parâmetro para um número de ponto flutuante. Por exemplo, a expressão a seguir retorna `10.333`: `float('10.333')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Obrigatório. O valor é convertido em um número de ponto flutuante.|  
|bool|Converte o parâmetro em um booliano. Por exemplo, a expressão a seguir retorna `false`: `bool(0)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Obrigatório. O valor que é convertido em um booliano.|  
|coalesce|Retorna o primeiro objeto não nulos nos argumentos passados. Observação: uma cadeia de caracteres vazia não é nula. Por exemplo, se os parâmetros 1 e 2 não forem definidos, isso retornará `fallback`: `coalesce(pipeline().parameters.parameter1', pipeline().parameters.parameter2 ,'fallback')`<br /><br /> **Número do parâmetro**: 1 ... *n*<br /><br /> **Nome**: Objeto*n*<br /><br /> **Descrição**: Obrigatório. Os objetos a serem verificados em relação a `null`.|  
|base64|Retorna a representação base64 da cadeia de caracteres de entrada. Por exemplo, a expressão a seguir retorna `c29tZSBzdHJpbmc=`: `base64('some string')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres 1<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres para codificar em uma representação base64.|  
|base64ToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em base64. Por exemplo, a expressão a seguir retorna a representação binária de alguma cadeia de caracteres: `base64ToBinary('c29tZSBzdHJpbmc=')`.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres codificada em base64.|  
|base64ToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada em base64. Por exemplo, a expressão a seguir retorna alguma cadeia de caracteres: `base64ToString('c29tZSBzdHJpbmc=')`.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres codificada em base64.|  
|Binário|Retorna uma representação binária de um valor.  Por exemplo, a expressão a seguir retorna uma representação binária de alguma cadeia de caracteres: `binary(‘some string’).`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Obrigatório. O valor que é convertido em binário.|  
|dataUriToBinary|Retorna uma representação binária de um URI de dados. Por exemplo, a expressão a seguir retorna a representação binária de alguma cadeia de caracteres: `dataUriToBinary('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. Os URI dos dados a serem convertidos para representação binária.|  
|dataUriToString|Retorna uma representação de cadeia de caracteres de um URI de dados. Por exemplo, a expressão a seguir retorna alguma cadeia de caracteres: `dataUriToString('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br />**Descrição**: Obrigatório. Os URI dos dados a serem convertidos para representação em cadeia de caracteres.|  
|dataUri|Retorna um URI de dados de um valor. Por exemplo, a expressão a seguir retorna dados: `text/plain;charset=utf8;base64,c29tZSBzdHJpbmc=: dataUri('some string')`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Value<br /><br />**Descrição**: Obrigatório. O valor a ser convertido em URI de dados.|  
|decodeBase64|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres com entrada baseada em base64. Por exemplo, a expressão a seguir retorna `some string`: `decodeBase64('c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres com entrada baseada em base64.|  
|encodeUriComponent|Codifica a URL para a cadeia de caracteres que é passada. Por exemplo, a expressão a seguir retorna `You+Are%3ACool%2FAwesome`: `encodeUriComponent('You Are:Cool/Awesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres para codificação de caracteres não seguros de URL.|  
|decodeUriComponent|Descodifica a URL para a cadeia de caracteres que é passada. Por exemplo, a expressão a seguir retorna `You Are:Cool/Awesome`: `encodeUriComponent('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. A cadeia de caracteres para descodificação de caracteres não seguros de URL.|  
|decodeDataUri|Retorna uma representação binária de uma cadeia de caracteres de URI de dados de entrada. Por exemplo, a expressão a seguir retorna a representação binária de `some string`: `decodeDataUri('data:;base64,c29tZSBzdHJpbmc=')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br /> **Descrição**: Obrigatório. O dataURI a ser decodificado para uma representação binária.|  
|uriComponent|Retorna uma representação codificada de URI de um valor. Por exemplo, a expressão a seguir retorna `You+Are%3ACool%2FAwesome: uriComponent('You Are:Cool/Awesome ')`<br /><br /> Detalhes do parâmetro: Número: 1, Nome: Cadeia de caracteres, Descrição: Obrigatório. A cadeia de caracteres a ser codificada para URI.|  
|uriComponentToBinary|Retorna uma representação binária de uma cadeia de caracteres codificada em URI. Por exemplo, a expressão a seguir retorna uma representação binária de `You Are:Cool/Awesome`: `uriComponentToBinary('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Cadeia de caracteres<br /><br />**Descrição**: Obrigatório. Cadeia de caracteres codificada em URI.|  
|uriComponentToString|Retorna uma representação de cadeia de caracteres de uma cadeia de caracteres codificada em URI. Por exemplo, a expressão a seguir retorna `You Are:Cool/Awesome`: `uriComponentToString('You+Are%3ACool%2FAwesome')`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Cadeia de caracteres<br /><br />**Descrição**: Obrigatório. Cadeia de caracteres codificada em URI.|  
|Xml|Retorna uma representação XML do valor. Por exemplo, a expressão a seguir retorna um conteúdo xml representado por `'\<name>Alan\</name>'`: `xml('\<name>Alan\</name>')`. A função xml também dá suporte à entrada de objeto JSON. Por exemplo, o parâmetro `{ "abc": "xyz" }` é convertido em um conteúdo xml `\<abc>xyz\</abc>`<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Value<br /><br />**Descrição**: Obrigatório. O valor a ser convertido para XML.|  
|xpath|Retorna uma matriz de nós xml que corresponde à expressão xpath de um valor que avalia a expressão xpath.<br /><br />  **Exemplo 1**<br /><br /> Suponha que o valor do parâmetro ‘p1’ é uma representação do XML a seguir:<br /><br /> `<?xml version="1.0"?> <lab>   <robot>     <parts>5</parts>     <name>R1</name>   </robot>   <robot>     <parts>8</parts>     <name>R2</name>   </robot> </lab>`<br /><br /> 1. Esse código: `xpath(xml(pipeline().parameters.p1), '/lab/robot/name')`<br /><br /> retornaria<br /><br /> `[ <name>R1</name>, <name>R2</name> ]`<br /><br /> enquanto<br /><br /> 2. Esse código: `xpath(xml(pipeline().parameters.p1, ' sum(/lab/robot/parts)')`<br /><br /> retornaria<br /><br /> `13`<br /><br /> <br /><br /> **Exemplo 2**<br /><br /> Dado o conteúdo XML a seguir:<br /><br /> `<?xml version="1.0"?> <File xmlns="http://foo.com">   <Location>bar</Location> </File>`<br /><br /> 1.  Esse código: `@xpath(xml(body('Http')), '/*[name()=\"File\"]/*[name()=\"Location\"]')`<br /><br /> ou o<br /><br /> 2. Esse código: `@xpath(xml(body('Http')), '/*[local-name()=\"File\" and namespace-uri()=\"http://foo.com\"]/*[local-name()=\"Location\" and namespace-uri()=\"\"]')`<br /><br /> retorna<br /><br /> `<Location xmlns="http://foo.com">bar</Location>`<br /><br /> e<br /><br /> 3. Esse código: `@xpath(xml(body('Http')), 'string(/*[name()=\"File\"]/*[name()=\"Location\"])')`<br /><br /> retorna<br /><br /> ``bar``<br /><br /> **Número do parâmetro**: 1<br /><br />**Nome**: Xml<br /><br />**Descrição**: Obrigatório. O XML onde é avaliada a expressão XPath.<br /><br /> **Número do parâmetro**: 2<br /><br />**Nome**: XPath<br /><br />**Descrição**: Obrigatório. A expressão XPath a ser avaliada.|  
|matriz|Converte o parâmetro em uma matriz.  Por exemplo, a expressão a seguir retorna `["abc"]`: `array('abc')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Value<br /><br /> **Descrição**: Obrigatório. O valor que é convertido em uma matriz.|
|createArray|Cria uma matriz de parâmetros.  Por exemplo, a expressão a seguir retorna `["a", "c"]`: `createArray('a', 'c')`<br /><br /> **Número do parâmetro**: 1 ... n<br /><br /> **Nome**: Qualquer n<br /><br /> **Descrição**: Obrigatório. Os valores a serem combinados em uma matriz.|

## <a name="math-functions"></a>Funções matemáticas  
 Essas funções podem ser usadas para qualquer um dos tipos de números: **inteiros** e **floats**.  
  
|Nome da função|DESCRIÇÃO|  
|-------------------|-----------------|  
|Adicionar|Retorna o resultado da adição de dois números. Por exemplo, essa função retorna `20.333`: `add(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Parcela 1<br /><br /> **Descrição**: Obrigatório. O número a ser adicionar à **Soma 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Parcela 2<br /><br /> **Descrição**: Obrigatório. O número a ser adicionar à **Soma 1**.|  
|sub|Retorna o resultado da subtração de dois números. Por exemplo, essa função retorna: `-0.333`:<br /><br /> `sub(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Minuendo<br /><br /> **Descrição**: Obrigatório. O número de onde é removido o **Subtraendo**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Subtraendo<br /><br /> **Descrição**: Obrigatório. O número a ser removido do **Minuendo**.|  
|mul|Retorna o resultado da multiplicação de dois números. Por exemplo, o seguinte retorna `103.33`:<br /><br /> `mul(10,10.333)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Multiplicando 1<br /><br /> **Descrição**: Obrigatório. O número a ser multiplicado pelo **Multiplicando 2**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Multiplicando 2<br /><br /> **Descrição**: Obrigatório. O número a ser multiplicado pelo **Multiplicando 1**.|  
|div|Retorna o resultado da divisão de dois números. Por exemplo, o seguinte retorna `1.0333`:<br /><br /> `div(10.333,10)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Dividendo<br /><br /> **Descrição**: Obrigatório. O número a ser dividido pelo **Divisor**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrição**: Obrigatório. O número para o qual o **Dividendo** será dividido.|  
|mod|Retorna o resultado do resto após a divisão dos dois números (módulo). Por exemplo, a expressão a seguir retorna `2`:<br /><br /> `mod(10,4)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Dividendo<br /><br /> **Descrição**: Obrigatório. O número a ser dividido pelo **Divisor**.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Divisor<br /><br /> **Descrição**: Obrigatório. O número para o qual o **Dividendo** será dividido. Após a divisão, o resto é obtido.|  
|Min|Há dois padrões diferentes para chamar essa função: `min([0,1,2])` Aqui min obtém uma matriz. Essa expressão retorna `0`. Como alternativa, essa função pode conter uma lista separada por vírgulas de valores:  `min(0,1,2)` Essa função também retorna 0. Observação: todos os valores devem ser números, portanto, se o parâmetro for uma matriz, ele deverá ter apenas números.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção ou Valor<br /><br /> **Descrição**: Obrigatório. Pode ser uma matriz de valores para encontrar o valor mínimo ou o primeiro valor de um conjunto.<br /><br /> **Número do parâmetro**: 2 ... *n*<br /><br /> **Nome**: Valor *n*<br /><br /> **Descrição**: Opcional. Se o primeiro parâmetro é um Valor, você pode passar valores adicionais e o mínimo de todos os valores passados é retornado.|  
|max|Há dois padrões diferentes para chamar essa função: `max([0,1,2])`<br /><br /> Aqui max obtém uma matriz. Essa expressão retorna `2`. Como alternativa, essa função pode conter uma lista separada por vírgulas de valores:  `max(0,1,2)` Essa função retorna 2. Observação: todos os valores devem ser números, portanto, se o parâmetro for uma matriz, ele deverá ter apenas números.<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Coleção ou Valor<br /><br /> **Descrição**: Obrigatório. Pode ser uma matriz de valores para encontrar o valor máximo ou o primeiro valor de um conjunto.<br /><br /> **Número do parâmetro**: 2 ... *n*<br /><br /> **Nome**: Valor *n*<br /><br /> **Descrição**: Opcional. Se o primeiro parâmetro é um valor, você pode passar valores adicionais e o máximo de todos os valores passados é retornado.|  
|range| Gera uma matriz de inteiros de um determinado número e você define o comprimento da matriz retornada. Por exemplo, essa função retornará `[3,4,5,6]`:<br /><br /> `range(3,4)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Índice inicial<br /><br /> **Descrição**: Obrigatório. É o primeiro inteiro na matriz.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Contagem<br /><br /> **Descrição**: Obrigatório. O número de inteiros existentes na matriz.|  
|rand| Gera um inteiro aleatório dentro do intervalo especificado (incluindo ambas as extremidades). Por exemplo, isso poderia retornar `42`:<br /><br /> `rand(-1000,1000)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Mínimo<br /><br /> **Descrição**: Obrigatório. O menor inteiro que poderia ser retornado.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Máximo<br /><br /> **Descrição**: Obrigatório. O maior inteiro que poderia ser retornado.|  
  
## <a name="date-functions"></a>Funções de data  
  
|Nome da função|DESCRIÇÃO|  
|-------------------|-----------------|  
|utcnow|Retorna o carimbo de data/hora atual como uma cadeia de caracteres. Por exemplo `2015-03-15T13:27:36Z`:<br /><br /> `utcnow()`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Formatar<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addseconds|Adiciona um número inteiro de segundos a um carimbo de data/hora de cadeia de caracteres passado. O número de segundos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-03-15T13:27:00Z`:<br /><br /> `addseconds('2015-03-15T13:27:36Z', -36)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Segundos<br /><br /> **Descrição**: Obrigatório. O número de segundos a serem adicionados. Pode ser negativo para subtrair segundos.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formatar<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addminutes|Adiciona um número inteiro de minutos a um carimbo de data/hora de cadeia de caracteres passado. O número de minutos pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-03-15T14:00:36Z`:<br /><br /> `addminutes('2015-03-15T13:27:36Z', 33)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: minutos<br /><br /> **Descrição**: Obrigatório. O número de minutos a serem adicionados. Pode ser negativo para subtrair minutos.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formatar<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|addhours|Adiciona um número inteiro de horas a um carimbo de data/hora de cadeia de caracteres passado. O número de horas pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-03-16T01:27:36Z`:<br /><br /> `addhours('2015-03-15T13:27:36Z', 12)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Horas<br /><br /> **Descrição**: Obrigatório. O número de horas a serem adicionadas. Pode ser negativo para subtrair horas.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formatar<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|adddays|Adiciona um número inteiro de dias a um carimbo de data/hora de cadeia de caracteres passado. O número de dias pode ser positivo ou negativo. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `adddays('2015-03-15T13:27:36Z', -20)`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Timestamp<br /><br /> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a hora.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Dias<br /><br /> **Descrição**: Obrigatório. O número de dias a serem adicionados. Pode ser negativo para subtrair dias.<br /><br /> **Número do parâmetro**: 3<br /><br /> **Nome**: Formatar<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  
|formatDateTime|Retorna uma cadeia de caracteres no formato de data. O resultado é uma cadeia de caracteres no formato ISO 8601 ("o") por padrão, a menos que um especificador de formato seja fornecido. Por exemplo `2015-02-23T13:27:36Z`:<br /><br /> `formatDateTime('2015-03-15T13:27:36Z', 'o')`<br /><br /> **Número do parâmetro**: 1<br /><br /> **Nome**: Data<br /><br /> **Descrição**: Obrigatório. Uma cadeia de caracteres que contém a data.<br /><br /> **Número do parâmetro**: 2<br /><br /> **Nome**: Formatar<br /><br /> **Descrição**: Opcional. Um [caractere do especificador de formato único](https://msdn.microsoft.com/library/az4se3k1%28v=vs.110%29.aspx) ou um [padrão de formato personalizado](https://msdn.microsoft.com/library/8kb3ddd4%28v=vs.110%29.aspx) que indica como formatar o valor desse carimbo de data/hora. Se o formato não for fornecido, o formato ISO 8601 ("o") será usado.|  

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de variáveis de sistema que você pode usar em expressões, consulte [Variáveis do sistema](control-flow-system-variables.md).
