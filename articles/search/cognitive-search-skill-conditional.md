---
title: Habilidade de cognitiva condicional
titleSuffix: Azure Cognitive Search
description: A habilidade condicional no Azure Pesquisa Cognitiva permite a filtragem, a criação de padrões e a mesclagem de valores em uma definição de conconhecimento.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b5f1fc7f877854dd06fbbe09ff82e47208fa12d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "72792041"
---
# <a name="conditional-cognitive-skill"></a>Habilidade de cognitiva condicional

A habilidade **condicional** habilita o Azure pesquisa cognitiva cenários que exigem uma operação booliana para determinar os dados a serem atribuídos a uma saída. Esses cenários incluem filtragem, atribuição de um valor padrão e mesclagem de dados com base em uma condição.

O pseudocódigo a seguir demonstra o que a habilidade condicional realiza:

```
if (condition) 
    { output = whenTrue } 
else 
    { output = whenFalse } 
```

> [!NOTE]
> Essa habilidade não está associada a uma API de serviços cognitivas do Azure e você não é cobrada por usá-la. No entanto, você ainda deve [anexar um recurso de serviços cognitivas](cognitive-search-attach-cognitive-services.md) para substituir a opção de recurso "gratuito" que limita você a um pequeno número de aprimoramentos por dia.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ConditionalSkill


## <a name="evaluated-fields"></a>Campos avaliados

Essa habilidade é especial porque suas entradas são campos avaliados.

Os seguintes itens são valores válidos de uma expressão:

-   Caminhos de anotação (caminhos em expressões devem ser delimitados por "$ (" e ")")
 <br/>
    Exemplos:
    ```
        "= $(/document)"
        "= $(/document/content)"
    ```

-  Literais (cadeias de caracteres, números, true, false, NULL) <br/>
    Exemplos:
    ```
       "= 'this is a string'"   // string (note the single quotation marks)
       "= 34"                   // number
       "= true"                 // Boolean
       "= null"                 // null value
    ```

-  Expressões que usam operadores de comparação (= =,! =, >=, >, <=, <) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en'"
        "= $(/document/sentiment) >= 0.5"
    ```

-   Expressões que usam operadores boolianos (&&, | |,!, ^) <br/>
    Exemplos:
    ```
        "= $(/document/language) == 'en' && $(/document/sentiment) > 0.5"
        "= !true"
    ```

-   Expressões que usam operadores numéricos (+,-, \* ,/,%) <br/>
    Exemplos: 
    ```
        "= $(/document/sentiment) + 0.5"         // addition
        "= $(/document/totalValue) * 1.10"       // multiplication
        "= $(/document/lengthInMeters) / 0.3049" // division
    ```

Como a habilidade condicional dá suporte à avaliação, você pode usá-la em cenários de transformação secundária. Por exemplo, consulte [definição de habilidades 4](#transformation-example).

## <a name="skill-inputs"></a>Entradas de habilidades
As entradas diferenciam maiúsculas de minúsculas.

| Entrada   | Descrição |
|-------------|-------------|
| condition   | Essa entrada é um [campo avaliado](#evaluated-fields) que representa a condição a ser avaliada. Essa condição deve ser avaliada como um valor booliano (*true* ou *false*).   <br/>  Exemplos: <br/> "= true" <br/> "= $ (/Document/Language) = = ' fr '" <br/> "= $ (/Document/Pages/ \* /Language) = = $ (/Document/expectedLanguage)" <br/> |
| whenTrue    | Essa entrada é um [campo avaliado](#evaluated-fields) que representa o valor a ser retornado se a condição for avaliada como *true*. As cadeias de caracteres de constantes devem ser retornadas entre aspas simples (' e '). <br/>Valores de exemplo: <br/> "= ' contrato '"<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/> |
| whenFalse   | Essa entrada é um [campo avaliado](#evaluated-fields) que representa o valor a ser retornado se a condição for avaliada como *false*. <br/>Valores de exemplo: <br/> "= ' contrato '"<br/>"= $ (/document/contractType)" <br/> "= $ (/Document/Entities/ \* )" <br/>

## <a name="skill-outputs"></a>Saídas de habilidades
Há uma única saída que é simplesmente chamada de "saída". Ele retornará o valor *whenFalse* se a condição for false ou *whenTrue* se a condição for true.

## <a name="examples"></a>Exemplos

### <a name="sample-skill-definition-1-filter-documents-to-return-only-french-documents"></a>Exemplo de definição de habilidade 1: filtrar documentos para retornar apenas documentos franceses

A saída a seguir retorna uma matriz de frases ("/document/frenchSentences") se o idioma do documento for francês. Se o idioma não for francês, o valor será definido como *NULL*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == 'fr'" },
        { "name": "whenTrue", "source": "/document/sentences" },
        { "name": "whenFalse", "source": "= null" }
    ],
    "outputs": [ { "name": "output", "targetName": "frenchSentences" } ]
}
```
Se "/document/frenchSentences" for usado como o *contexto* de outra habilidade, essa habilidade só será executada se "/Document/frenchSentences" não estiver definido como *nulo*.


### <a name="sample-skill-definition-2-set-a-default-value-for-a-value-that-doesnt-exist"></a>Exemplo de definição de habilidade 2: definir um valor padrão para um valor que não existe

A saída a seguir cria uma anotação ("/document/languageWithDefault") que é definida como o idioma do documento ou como "es" se o idioma não estiver definido.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document",
    "inputs": [
        { "name": "condition", "source": "= $(/document/language) == null" },
        { "name": "whenTrue", "source": "= 'es'" },
        { "name": "whenFalse", "source": "= $(/document/language)" }
    ],
    "outputs": [ { "name": "output", "targetName": "languageWithDefault" } ]
}
```

### <a name="sample-skill-definition-3-merge-values-from-two-fields-into-one"></a>Exemplo de definição de habilidade 3: mesclar valores de dois campos em um

Neste exemplo, algumas frases têm uma propriedade *frenchSentiment* . Sempre que a propriedade *frenchSentiment* for nula, queremos usar o valor *englishSentiment* . Atribuímos a saída a um membro que é chamado de *sentimentos* ("/Document/Sentiment/*/Sentiment").

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= $(/document/sentences/*/frenchSentiment) == null" },
        { "name": "whenTrue", "source": "/document/sentences/*/englishSentiment" },
        { "name": "whenFalse", "source": "/document/sentences/*/frenchSentiment" }
    ],
    "outputs": [ { "name": "output", "targetName": "sentiment" } ]
}
```

## <a name="transformation-example"></a>Exemplo de transformação
### <a name="sample-skill-definition-4-data-transformation-on-a-single-field"></a>Exemplo de definição de habilidade 4: transformação de dados em um único campo

Neste exemplo, recebemos um *sentimentos* entre 0 e 1. Queremos transformá-lo entre-1 e 1. Podemos usar a habilidade condicional para fazer essa transformação secundária.

Neste exemplo, não usamos o aspecto condicional da habilidade porque a condição é sempre *verdadeira*.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ConditionalSkill",
    "context": "/document/sentences/*",
    "inputs": [
        { "name": "condition", "source": "= true" },
        { "name": "whenTrue", "source": "= $(/document/sentences/*/sentiment) * 2 - 1" },
        { "name": "whenFalse", "source": "= 0" }
    ],
    "outputs": [ { "name": "output", "targetName": "normalizedSentiment" } ]
}
```

## <a name="special-considerations"></a>Considerações especiais
Alguns parâmetros são avaliados, portanto, você precisa ser especialmente cuidadoso para seguir o padrão documentado. As expressões devem começar com um sinal de igual. Um caminho deve ser delimitado por "$ (" e ")". Certifique-se de colocar cadeias de caracteres entre aspas simples. Isso ajuda o avaliador a distinguir entre cadeias de caracteres e os caminhos e os operadores reais. Além disso, certifique-se de colocar o espaço em branco em volta de operadores (por exemplo, um "*" em um caminho significa algo diferente de multiplicação).


## <a name="next-steps"></a>Próximas etapas

+ [Habilidades internas](cognitive-search-predefined-skills.md)
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
