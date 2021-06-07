---
title: Obter imagens da exibição personalizada – Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Visão geral de alto nível sobre o uso da Pesquisa Personalizada do Bing para obter imagens de sua exibição personalizada da Web.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 5025a68030f5dc3aec07d33af3f98370c8d64b87
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "96338462"
---
# <a name="get-images-from-your-custom-view"></a>Obter imagens do modo de exibição personalizado

> [!WARNING]
> As APIs de Pesquisa do Bing estão migrando dos Serviços Cognitivos para os Serviços de Pesquisa do Bing. A partir de **30 de outubro de 2020**, todas as novas instâncias da Pesquisa do Bing precisam ser provisionadas seguindo o processo documentado [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> As APIs de Pesquisa do Bing provisionadas por meio dos Serviços Cognitivos terão suporte nos próximos três anos ou até o final do seu Contrato Enterprise, o que ocorrer primeiro.
> Para obter instruções sobre a migração, confira [Serviços de Pesquisa do Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

A Pesquisa de Imagens Personalizadas do Bing permite que você aprimore sua experiência de pesquisa personalizada com imagens. Semelhante aos resultados da Web, a pesquisa personalizada oferece suporte à pesquisa de imagens na lista de sites da sua instância. Você pode obter as imagens usando a API de pesquisa de imagens personalizadas do Bing ou por meio do recurso de interface do usuário hospedada. Usar o recurso da interface do usuário do host é simples de usar e recomendado para colocar sua experiência de pesquisa em funcionamento rapidamente.  Para obter informações sobre como configurar sua UI hospedada para incluir imagens, consulte [Configure sua experiência de UI hospedada](hosted-ui.md).

Se você quiser mais controle sobre a exibição dos resultados da pesquisa, poderá usar a API de pesquisa de imagens personalizadas do Bing. Como chamar a API é semelhante a chamar a API de pesquisa de imagens do Bing, confira [Pesquisa de Imagens do Bing](../Bing-Image-Search/overview.md) para obter exemplos chamando a API. Mas, antes disso, familiarize-se com o [conteúdo da referência da API de pesquisa de imagens personalizadas](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference). As principais diferenças são os parâmetros de consulta com suporte (você deve incluir o parâmetro de consulta customConfig) e o ponto de extremidade para o qual envia solicitações.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->