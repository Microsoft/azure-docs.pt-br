---
title: Limites e cotas – Serviço de Visão Personalizada
titlesuffix: Azure Cognitive Services
description: Saiba mais sobre os limites e cotas do Serviço de Visão Personalizada.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: anroth
ms.openlocfilehash: 85abc4a50710629d3485d05115698e59a93fc96e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995589"
---
# <a name="limits-and-quotas"></a>Limites e cotas

Há duas camadas de chaves para o serviço de visão personalizada. Você pode se inscrever para uma assinatura F0 (gratuita) ou S0 (padrão) por meio do portal do Azure. Consulte a [página de preços de serviços cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) correspondente para obter detalhes sobre preços e transações.

Espera-se que o número de imagens de treinamento por projeto e tags por projeto aumente com o tempo para projetos S0.

||**F0**|**S0**|
|-----|-----|-----|
|Projetos|2|100|
|Imagens de treinamento por projeto |5.000|100.000|
|Previsões/mês|10.000 |Ilimitado|
|Marcas/projeto|50|500|
|Iterações |10|10|
|Min rotulado imagens por marca, classificação (mais de 50 recomendado) |5|5|
|Min rotulado imagens por marca, detecção de objetos (mais de 50 recomendado)|15|15|
|Por quanto tempo as imagens de previsão são armazenadas|30 dias|30 dias|
|Operações de [Previsão](https://go.microsoft.com/fwlink/?linkid=865445) com armazenamento (Transações por Segundo)|2|10|
|Operações de [Previsão](https://go.microsoft.com/fwlink/?linkid=865445) sem armazenamento (Transações por Segundo)|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (chamadas à API por Segundo)|2|10|
|[Outras chamadas à API](https://go.microsoft.com/fwlink/?linkid=865446) (Transações por Segundo)|10|10|
|Tamanho máximo da imagem (upload da imagem de treinamento) |6 MB|6 MB|
|Tamanho máximo da imagem (previsão)|4 MB|4 MB|
