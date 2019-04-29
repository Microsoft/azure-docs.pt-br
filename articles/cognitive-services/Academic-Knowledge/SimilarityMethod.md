---
title: Método de similaridade – API de Conhecimento Acadêmico
titlesuffix: Azure Cognitive Services
description: Use o Método de similaridade para calcular a similaridade acadêmica de duas cadeias de caracteres.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 7f692c08f8af322bf7e6ab576e2e6f516594a6c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61336510"
---
# <a name="similarity-method"></a>Método de Similaridade

A API REST de **similaridade** é usada para calcular a similaridade acadêmica entre duas cadeia de caracteres. 
<br>

**Ponto de extremidade REST:**
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>Parâmetros de solicitação

Parâmetro        |Tipo de Dados      |Obrigatório | DESCRIÇÃO
----------|----------|----------|------------
**s1**        |Cadeia de caracteres   |Sim  |Cadeia de caracteres * a ser comparada
**s2**        |Cadeia de caracteres   |Sim  |Cadeia de caracteres * a ser comparada

<sub> *As cadeias de caracteres a serem comparadas têm um comprimento máximo de 1 MB. </sub>
<br>

## <a name="response"></a>Response

NOME | DESCRIÇÃO
--------|---------
**SimilarityScore**        |Um valor de ponto flutuante representando a similaridade do cosseno de s1 e s2, com valores mais próximos a 1,0, significando mais semelhante e valores mais próximos a -1,0, significando menos semelhante

<br>

## <a name="successerror-conditions"></a>Condições de erro/êxito

Status HTTP | Motivo | Response
-----------|----------|--------
**200**         |Sucesso | Número de ponto flutuante
**400**         | Solicitação incorreta ou solicitação inválida | Mensagem de erro      
**500**         |Erro interno do servidor | Mensagem de erro
**Tempo Limite**     | Atingiu tempo limite solicitado.  | Mensagem de erro

<br>

## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>Exemplo: Calcular a similaridade de dois abstratos parciais
#### <a name="request"></a>Solicitação:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
Neste exemplo, geramos a pontuação de similaridade entre dois abstratos parciais usando a API de **similaridade**.
#### <a name="response"></a>Resposta:
```
0.520
```
#### <a name="remarks"></a>Comentários:
A pontuação de similaridade é determinada pela avaliação dos conceitos acadêmicos por meio da incorporação de palavras. Neste exemplo, 0,52 significa que os dois abstratos parciais são um pouco semelhantes.
<br>
