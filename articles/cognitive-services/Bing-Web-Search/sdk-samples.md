---
title: Exemplos do SDK de Pesquisa na Web do Bing
titleSuffix: Azure Cognitive Services
description: Use o SDK de Pesquisa na Web do Bing para adicionar funcionalidades de pesquisa ao seu aplicativo Python, Node.js, C# ou Java.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: f13250bb2d6829cf7f1bf3dd0417974b810a36fe
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96350425"
---
# <a name="bing-web-search-sdk-samples"></a>Exemplos do SDK de Pesquisa na Web do Bing

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

O SDK de Pesquisa na Web do Bing está disponível em Python, Node.js, C# e Java. Exemplos de código, pré-requisitos e instruções de build são fornecidos no GitHub. Os cenários a seguir são abordados:

* Consulte uma única palavra e imprima o nome e a URL do primeiro resultado para páginas da Web, imagens, artigos de notícias e vídeos.
* Consulte uma frase, verifique o número de resultados e imprima o nome e a URL do primeiro resultado.
* Consulte um termo de pesquisa com os filtros de resposta definidos como `news` e imprima os detalhes dos resultados de notícias.
* Consulte um termo de pesquisa com os parâmetros `answerCount` e `promote`, então imprima detalhes dos resultados.

## <a name="sdks-and-libraries"></a>SDKs e bibliotecas

Use estes links para acessar o SDK para seu idioma preferencial.

* Introdução aos [exemplos Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * Consulte também as [bibliotecas do Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch) para obter definições e dependências.
* Introdução aos [exemplos Node.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Veja também [Pesquisa na Web do Node.js](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Introdução aos [exemplos .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [Pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Consulte também as [bibliotecas do .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch) para obter definições e dependências.
* Introdução aos [exemplos Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * Consulte também as [bibliotecas do Java](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch) para obter definições e dependências.