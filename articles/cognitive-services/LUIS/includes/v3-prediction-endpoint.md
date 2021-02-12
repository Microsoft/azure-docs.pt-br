---
title: Como obter o ponto de extremidade de previsão V3
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 05/05/2020
ms.openlocfilehash: 85fcd0308083350a02afd4eadac2b361337f3b33
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93128108"
---
1. No portal do LUIS, na seção **Gerenciar** (menu à direita superior), na página **Recursos do Azure** (menu à esquerda), na guia **Recursos de Previsão** , copie a **Consulta de Exemplo** na parte inferior da página. A URL tem o ID do aplicativo, a chave e o nome do slot. A URL de ponto de extremidade de previsão V3 tem o seguinte formato: `https://YOUR-RESOURCE-NAME.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/SLOT-NAME/predict?subscription-key=YOUR-PREDICTION-KEY&<optional-name-value-pairs>&query=YOUR_QUERY_HERE`

    :::image type="content" source="../media/prediction-resources-example-query.png" alt-text="exemplo de consulta na seção de recursos de previsão" lightbox="../media/prediction-resources-example-query.png":::
    
    Cole a URL em uma guia do navegador da Web. Se a URL não for exibida, você não terá um recurso de previsão e precisará criar um. 

    

    

