---
title: Ponto final da pesquisa na Web
titleSuffix: Azure Cognitive Services
description: Para obter resultados da pesquisa na Web, envie uma `GET` solicitação para o ponto de extremidade a seguir. Os cabeçalhos e parâmetros de URL definem mais especificações.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: aahi
ms.openlocfilehash: c882e3e4d0cd6ba594a700f4fd53c14103a8d1d1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94381245"
---
# <a name="web-search-endpoint"></a>Ponto de extremidade de Pesquisa na Web

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A **API de Pesquisa na Web** retorna páginas da Web, notícias, imagens, vídeos e [entidades](../bing-entities-search/overview.md). Entidades têm informações resumidas sobre uma pessoa, lugar ou tópico.

## <a name="endpoint"></a>Ponto de extremidade

Para obter os resultados de pesquisa na Web usando a API do Bing, envie uma solicitação `GET` para o ponto de extremidade a seguir. Os cabeçalhos e parâmetros de URL definem mais especificações.

**Ponto de extremidade:** retorna resultados Web que são relevantes para a consulta de pesquisa do usuário definida pelo `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/search
```

Ponto de extremidade: Para obter detalhes sobre cabeçalhos, parâmetros, códigos de mercado, os objetos de resposta, erros e muito mais, consulte a referência [API da Web do Bing v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

## <a name="response-json"></a>JSON de resposta

A resposta a uma solicitação de pesquisa na Web inclui todos resultados como objetos JSON. Analisar os resultados requer procedimentos que lidam com elementos de cada tipo. Consulte o [tutorial](./tutorial-bing-web-search-single-page-app.md) e [código-fonte](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Web-Search) para obter exemplos.

## <a name="next-steps"></a>Próximas etapas

As APIs do **Bing** dão suporte a ações de pesquisa que retornam os resultados de acordo com seu tipo. Todos os pontos de extremidade de pesquisa retornam os resultados como objetos de resposta JSON.  Todos os endpoints suportam consultas que retornam um idioma e uma localização específicos por longitude, latitude e raio de pesquisa.

Para obter informações completas sobre os parâmetros compatíveis com cada ponto de extremidade, confira as páginas de referência de cada tipo.
Para obter exemplos de solicitações básicas usando a API de Pesquisa na Web, consulte [Início rápido sobre pesquisa na Web do Bing](./overview.md).