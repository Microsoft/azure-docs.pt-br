---
title: Formato de dados - API de Serviço de Exploração de Conhecimento
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre o formato de dados na API de KES (Serviço de Exploração de Conhecimento).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: bba257c61509d862bb3161625750f05a86af8770
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60815096"
---
# <a name="data-format"></a>Formato de Dados

O arquivo de dados descreve a lista de objetos a serem indexados.
Cada linha no arquivo especifica os valores de atributos de um objeto no [formato JSON](https://json.org/) com codificação UTF-8.
Além dos atributos definidos no [esquema](SchemaFormat.md), cada objeto possui um atributo "logprob" opcional que especifica a probabilidade de log relativa entre os objetos.
Quando o serviço retorna objetos em ordem de probabilidade decrescente, é possível usar "logprob" para indicar a ordem de retorno dos objetos correspondentes.
Dada uma probabilidade *p* entre 0 e 1, a probabilidade de log correspondente pode ser calculada como log(*p*), onde log() é a função de log natural.
Quando nenhum valor for especificado para logprob, será usado o valor padrão 0.

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

Para os atributos String, GUID e Blob, o valor é representado como uma cadeia de caracteres JSON entre aspas.  Para atributos numéricos (Int32, Int64, Double), o valor é representado como um número JSON.  Para atributos compostos, o valor é um objeto JSON que especifica os valores de sub-atributo.  Para compilações de índice mais rápidas, classifique previamente os objetos, diminuindo a probabilidade de log.

Em geral, um atributo pode ter 0 ou mais valores.  Se um atributo não tiver valor, simplesmente remova-o do JSON.  Se um atributo tiver 2 ou mais valores, é possível repetir o par de valor do atributo no objeto JSON.  Como alternativa, é possível atribuir uma matriz JSON contendo os vários valores para o atributo.

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
