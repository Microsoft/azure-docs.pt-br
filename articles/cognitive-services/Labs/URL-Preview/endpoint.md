---
title: Ponto de extremidade de Visualização de URL de Projeto
titlesuffix: Azure Cognitive Services
description: Resumo sobre ponto de extremidade de Visualização de URL.
services: cognitive-services
author: mikedodaro
manager: nitinme
ms.service: cognitive-services
ms.subservice: url-preview
ms.topic: reference
ms.date: 03/29/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: 7cc52493ec0e2b9c81d52da4bb22102c2c7e5e5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712468"
---
# <a name="project-url-preview-endpoint"></a>Ponto de extremidade de Visualização de URL de Projeto

A API de Visualização de URL inclui um ponto de extremidade.

## <a name="endpoint"></a>Ponto de extremidade
Para obter uma Visualização de URL, envie uma solicitação para o ponto de extremidade a seguir. Use os cabeçalhos e parâmetros de URL para outras especificações.

GET:
```
https://api.labs.cognitive.microsoft.com/urlpreview/v7.0/search?q=https://swiftkey.com

```

### <a name="query-parameters"></a>Parâmetros de consulta
|NOME|Value|Type|Obrigatório|  
|----------|-----------|----------|--------------|  
|q|URL para visualização|Cadeia de caracteres |Sim|
|safeSearch|Conteúdo adulto ilegal ou pirateado, é bloqueado com código de erro 400 e o sinalizador *isFamilyFriendly* não é retornado. <p>Para obter conteúdo legal para adulto, veja abaixo o comportamento. O código de status retorna 200 e o sinalizador *isFamilyFriendly* é definido como false.<ul><li>safeSearch=strict: Título, descrição, URL e imagem não serão retornados.</li><li>safeSearch=moderate; Obtém o título, a URL e a descrição, mas não a imagem descritiva.</li><li>safeSearch=off; Obtém todos os elementos/objetos de resposta – título, URL, descrição e imagem.</li></ul> |Cadeia de caracteres|Não obrigatório. </br> Usa como padrão safeSearch=strict.| 

## <a name="response-object"></a>Objeto de resposta

A resposta inclui os cabeçalhos HTTP e o objeto WebPage com atributos, como mostrado no exemplo a seguir: `name`, `url`, `description`, `isFamilyFriendly` e `primaryImageOfPage`.

```
BingAPIs-TraceId: 15AFE52A97AA422F960433A94803F6CE
BingAPIs-SessionId: 40587764F42142D3A8BA99F66B2B3BB6
X-MSEdge-ClientID: 0389E3EDED106B5E1424E82FEC436A56
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 15AFE52A97AA422F960433A94803F6CE Ref B: PAOEDGE0418 Ref C: 2018-03-30T16:36:27Z
{
  "_type": "WebPage",
  "name": "SwiftKey - Smart prediction technology for easier mobile ...",
  "url": "https://swiftkey.com/",
   "description": "Discover the best Android and iPhone and iPad apps for faster, easier typing with emoji, colorful themes and more - download SwiftKey Keyboard free today.",
  "isFamilyFriendly": true,
  "primaryImageOfPage": {
    "contentUrl": "https://swiftkey.com/images/og/default.jpg"
  }
}

```

## <a name="next-steps"></a>Próximas etapas
- [Início Rápido do C#](csharp.md)
- [Início Rápido do Java](java-quickstart.md)
- [Início Rápido do JavaScript](javascript.md)
- [Início Rápido do Node](node-quickstart.md)
- [Início Rápido do Python](python-quickstart.md)
