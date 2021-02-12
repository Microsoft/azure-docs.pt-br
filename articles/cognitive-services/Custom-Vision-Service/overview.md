---
title: O que é Visão Personalizada?
titleSuffix: Azure Cognitive Services
description: Aprenda a usar o serviço de Visão Personalizada do Azure para criar detectores de objetos e classificadores de imagem de IA personalizada na nuvem do Azure.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconhecimento de imagem, identificador de imagem, aplicativo de reconhecimento de imagem, visão personalizada
ms.openlocfilehash: 12877f2d43f9b8f864871e5a5ab050aa0eeb61e2
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814594"
---
# <a name="what-is-custom-vision"></a>O que é Visão Personalizada?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

A Visão Personalizada do Azure é um serviço de reconhecimento de imagem que permite criar, implantar e aprimorar os próprios identificadores de imagem. Um identificador de imagem aplica rótulos (que representam classes ou objetos) a imagens, de acordo com as características visuais delas. Ao contrário do serviço [Pesquisa Visual Computacional](../computer-vision/overview.md), a Visão Personalizada permite que você especifique os rótulos e treine modelos personalizados para detectá-los.

## <a name="what-it-does"></a>O que faz

O Serviço de Visão Personalizada usa um algoritmo de machine learning para analisar imagens. Você, o desenvolvedor, envia grupos de imagens que tenham ou não as características em questão. Você mesmo rotula as imagens no momento do envio. Em seguida, o algoritmo treina com esses dados e calcula a própria precisão se testando com essas mesmas imagens. Depois que o algoritmo é treinado, você pode testá-lo, retreiná-lo e, eventualmente, usá-lo em seu aplicativo de reconhecimento de imagem para classificar novas imagens. Você também pode exportar o próprio modelo para uso offline.

### <a name="classification-and-object-detection"></a>Classificação e detecção de objetos

A funcionalidade de Visão Personalizada pode ser dividida em dois recursos. A **classificação de imagens** aplica um ou mais rótulos a uma imagem. A **detecção de objetos** é semelhante, mas também retorna as coordenadas na imagem em que os rótulos aplicados podem ser encontrados.

### <a name="optimization"></a>Optimization

O Serviço de Visão Personalizada foi otimizado para reconhecer rapidamente as principais diferenças entre imagens, de modo que você possa começar a criar protótipos do modelo com uma pequena quantidade de dados. Geralmente, 50 imagens por marca são um bom começo. No entanto, o serviço não é ideal para detectar diferenças sutis em imagens (por exemplo, detectar pequenas rachaduras ou depressões em cenários de garantia de qualidade).

Além disso, você pode escolher entre diversas variedades do algoritmo da Visão Personalizada que são otimizadas para determinados assuntos, por exemplo, pontos de referência ou itens de varejo. Para obter mais informações, confira os guias [Criar um classificador](getting-started-build-a-classifier.md) ou [Criar um detector de objetos](get-started-build-detector.md).

## <a name="what-it-includes"></a>O que inclui

O Serviço de Visão Personalizada está disponível como um conjunto de SDKs nativos e por meio de uma interface baseada na Web no [site da Visão Personalizada](https://customvision.ai/). Você pode criar, testar e treinar um modelo por uma dessas interfaces ou usar ambas conjuntamente.

![Site da Visão Personalizada em uma janela do navegador Chrome](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Visão Personalizada devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Siga o guia [Criar um classificador](getting-started-build-a-classifier.md) para começar a usar a Visão Personalizada no portal da Web ou conclua um [início rápido](quickstarts/image-classification.md) para implementar os cenários básicos no código.