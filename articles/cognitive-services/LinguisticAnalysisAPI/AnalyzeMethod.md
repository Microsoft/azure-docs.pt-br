---
title: Método de Análise – API de Análise Linguística
titlesuffix: Azure Cognitive Services
description: Como usar o método Analisar na API de Análise Linguística para analisar certas entradas de linguagem natural.
services: cognitive-services
author: RichardSunMS
manager: nitinme
ms.service: cognitive-services
ms.subservice: linguistic-analysis
ms.topic: conceptual
ms.date: 12/13/2016
ms.author: lesun
ROBOTS: NOINDEX
ms.openlocfilehash: 02c41e2510fd77f4bb65143faf62737f0985d2b7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401178"
---
# <a name="analyze-method"></a>Método Analisar

> [!IMPORTANT]
> A versão prévia da Análise Linguística foi encerrada em 9 de agosto de 2018. Recomendamos usar os [módulos de Análise de Texto do Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics) para análise e processamento de texto.

A API REST **Analisar** é usada para analisar uma entrada de linguagem natural determinada.
Que pode envolver a localização a [frases e tokens](Sentences-and-Tokens.md) dentro do que a entrada, localizando a [marcas de parte da fala](POS-tagging.md), ou localizar os [árvore de clientes](Constituency-Parsing.md).
Você pode especificar quais resultados você deseja selecionando os analisadores relevantes.
Para listar todos os analisadores disponíveis, examine os **[analisadores](AnalyzersMethod.md)**.

Observe que você precisa especificar a linguagem da cadeia de caracteres de entrada.

**Ponto de extremidade de REST:**
```
https://westus.api.cognitive.microsoft.com/linguistics/v1.0/analyze
```
<br>

## <a name="request-parameters"></a>Parâmetros da solicitação

NOME | Type | Obrigatório | DESCRIÇÃO
-----|-------|----------|------------
**linguagem**    | string | Sim | As duas letras do código de idioma ISO a ser usado para análise. Por exemplo, inglês é "en".
**analyzerIds** | lista de cadeias de caracteres | Sim | Lista de GUIDs de analisadores para aplicar. Para obter mais informações, confira a documentação do Analisadores.
**text**        | string | Sim | Entrada bruta a ser analisada. Isso pode ser uma cadeia de caracteres curta, como uma palavra ou frase, um período completo, ou um parágrafo completo ou discurso.

## <a name="response-json"></a>Resposta (JSON)

Uma matriz de saídas de análise, uma para cada atributo especificado na solicitação.

Os resultados são semelhantes ao seguinte:

NOME | Type | DESCRIÇÃO
-----|------|--------------
analyzerId | string | GUID do analisador especificado
result | objeto | resultado do analisador

Observe que o tipo do resultado depende do tipo de analisador de entrada.

### <a name="tokens-response-json"></a>Resposta de Tokens (JSON)

NOME | Type | DESCRIÇÃO
-----|------|-------------
result | lista de objetos de frase | limites de frase identificados dentro do texto |
result[x].Offset | int | deslocamento de caractere inicial de cada frase |
result[x].Len | int | comprimento em caracteres de cada frase |
result[x].Tokens | lista de objetos de token | limites de tokens identificados dentro da frase |
result[x].Tokens[y].Offset | int | deslocamento de caractere inicial de token |
result[x].Tokens[y].Len | int | comprimento em caracteres do token |
result[x].Tokens[y].RawToken | string | os caracteres dentro desse token, antes da normalização |
result[x].Tokens[y].NormalizedToken | string | uma forma normalizada de caractere, seguro para uso em uma [árvore de análise](Constituency-Parsing.md); por exemplo, um caractere parêntese de abertura ' (' torna-se '- LRB-' |

Entrada de exemplo: "Este é um teste. Olá.”
Exemplo de resposta JSON:
```json
[
  {
    "Len": 15,
    "Offset": 0,
    "Tokens": [
      {
        "Len": 4,
        "NormalizedToken": "This",
        "Offset": 0,
        "RawToken": "This"
      },
      {
        "Len": 2,
        "NormalizedToken": "is",
        "Offset": 5,
        "RawToken": "is"
      },
      {
        "Len": 1,
        "NormalizedToken": "a",
        "Offset": 8,
        "RawToken": "a"
      },
      {
        "Len": 4,
        "NormalizedToken": "test",
        "Offset": 10,
        "RawToken": "test"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 14,
        "RawToken": "."
      }
    ]
  },
  {
    "Len": 6,
    "Offset": 16,
    "Tokens": [
      {
        "Len": 5,
        "NormalizedToken": "Hello",
        "Offset": 16,
        "RawToken": "Hello"
      },
      {
        "Len": 1,
        "NormalizedToken": ".",
        "Offset": 21,
        "RawToken": "."
      }
    ]
  }
]
```


### <a name="pos-tags-response-json"></a>Resposta de marcas de POS (JSON)

O resultado é uma lista de listas de cadeias de caracteres.
Para cada frase, há uma lista de marcas de POS, uma marca de POS para cada token.
Para localizar o token correspondente para cada marca de POS, você desejará solicitar um objeto de geração de tokens também.

### <a name="constituency-tree-response-json"></a>Resposta de árvore constituency (JSON)

O resultado é uma lista de cadeias de caracteres, uma árvore de análise para cada frase encontrada na entrada.
As árvores de análise são representadas em um formulário entre parênteses.

## <a name="example"></a>Exemplo

`POST /analyze`

Corpo da solicitação: conteúdo JSON
```json
{
  "language": "en",
  "analyzerIds": [
    "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "22A6B758-420F-4745-8A3C-46835A67C0D2" ],
  "text": "Hi, Tom! How are you today?"
}
```

Resposta: JSON
```json
[
  {
    "analyzerId": "4FA79AF1-F22C-408D-98BB-B7D7AEEF7F04",
    "result": [ ["NNP",",","NNP","."], ["WRB","VBP","PRP","NN","."] ]
  },
  {
    "analyzerId": "22A6B758-420F-4745-8A3C-46835A67C0D2",
    "result":["(TOP (S (NNP Hi) (, ,) (NNP Tom) (. !)))","(TOP (SBARQ (WHADVP (WRB How)) (SQ (VP (VBP are)) (NP (PRP you)) (NN today) (. ?))))"]
  }
]
```
