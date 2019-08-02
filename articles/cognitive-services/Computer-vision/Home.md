---
title: O que é a API da Pesquisa Visual Computacional? – Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: O serviço de Pesquisa Visual Computacional fornece aos desenvolvedores o acesso a algoritmos avançados para processar imagens e retornar informações.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4f823b62237e8018f092d7a94fb479f7fbdd38da
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565763"
---
# <a name="what-is-computer-vision"></a>O que é a Pesquisa Visual Computacional?

O serviço de Pesquisa Visual Computacional do Azure fornece aos desenvolvedores acesso a algoritmos avançados que processam imagens e retornam informações. Para analisar uma imagem, você pode carregar uma imagem ou especificar uma URL de imagem. As imagens de algoritmos de processamento podem analisar o conteúdo de várias maneiras diferentes, dependendo dos recursos visuais em que você está interessado. Por exemplo, a Pesquisa Visual Computacional pode determinar se uma imagem apresenta conteúdo erótico ou para adulto ou encontrar todos os rostos humanos em uma imagem.

Você pode usar Pesquisa Visual Computacional em seu aplicativo usando um SDK nativo ou invocando a API REST diretamente. Esta página cobre amplamente o que você pode fazer com a Pesquisa Visual Computacional.

## <a name="analyze-images-for-insight"></a>Analisar imagens quanto a insights

Você pode analisar imagens para detectar e fornecer informações sobre seus recursos visuais e características. Todos os recursos na tabela a seguir são fornecidos pela API [Analisar Imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa).

| Ação | DESCRIÇÃO |
| ------ | ----------- |
|**[Marcar recursos visuais](concept-tagging-images.md)**|Identificar e marcar recursos visuais em uma imagem, de um conjunto de milhares de objetos reconhecíveis, seres vivos, cenários e ações. Quando as marcas forem ambíguas ou não pertencerem a um conhecimento comum, a resposta da API fornecerá dicas para esclarecer o contexto da tag. A marcação não está limitada ao assunto principal, como uma pessoa em primeiro plano, mas também inclui cenário (interno ou externo), móveis, ferramentas, plantas, animais, acessórios, gadgets e outros.|
|**[Detectar objetos](concept-object-detection.md)**| A detecção de objetos é semelhante à marcação, mas a API retorna as coordenadas da caixa delimitadora para cada tag aplicada. Por exemplo, se uma imagem contiver um cachorro, um gato e uma pessoa, a operação Detect listará esses objetos junto com as coordenadas na imagem. Você pode usar essa funcionalidade para processar ainda mais as relações entre os objetos em uma imagem. Também permite que você saiba quando há várias instâncias da mesma tag em uma imagem.|
|**[Detectar marcas](concept-brand-detection.md)**|Identifique as marcas comerciais em imagens ou vídeos de um banco de dados de milhares de logotipos globais. Você pode usar esse recurso, por exemplo, para descobrir quais marcas são mais populares em mídia social ou mais predominantes no posicionamento de produto de mídia.|
|**[Categorizar uma imagem](concept-categorizing-images.md)**|Identifique e categorize uma imagem inteira usando uma [taxonomia de categoria](Category-Taxonomy.md) com hierarquias hereditárias de pai/filho. As categorias podem ser usadas autonomamente ou com nossos novos modelos de marcação.<br/>Atualmente, o inglês é o único idioma com suporte para a marcação e categorização de imagens.|
|**[Descrever uma imagem](concept-describing-images.md)**|Gere uma descrição de uma imagem inteira em linguagem compreendida por humanos, usando frases completas. Os algoritmos da Pesquisa Visual Computacional geram várias descrições baseadas nos objetos identificados na imagem. Cada uma das descrições é avaliada e uma pontuação de confiança é gerada. Uma lista é então retornada, ordenada pela pontuação de confiança mais alta à mais baixa.|
|**[Detectar rostos](concept-detecting-faces.md)** |Detecte rostos em uma imagem e forneça informações sobre cada rosto detectado. A Pesquisa Visual Computacional retorna as coordenadas, o retângulo, o sexo e a idade de cada rosto detectado.<br/>A Pesquisa Visual Computacional fornece um subconjunto da funcionalidade do serviço [Detecção Facial](/azure/cognitive-services/face/). Você pode usar o serviço de Detecção Facial para uma análise mais detalhada, como identificação facial e detecção de pose.|
|**[Detectar tipos de imagem](concept-detecting-image-types.md)**|Detecte características sobre uma imagem, por exemplo, se uma imagem é um desenho de linha se é, possivelmente, um clip-art.|
|**[Detectar conteúdo específico de um domínio](concept-detecting-domain-content.md)**|Use modelos de domínio para detectar e identificar conteúdo específico de um assunto em uma imagem, como celebridades e pontos turísticos. Por exemplo, se uma imagem contiver pessoas, a Pesquisa Visual Computacional poderá usar um modelo de domínio para celebridades a fim de determinar se as pessoas detectadas na imagem são celebridades conhecidas.|
|**[Detectar o esquema de cores](concept-detecting-color-schemes.md)**|Analise o uso de cores em uma imagem. A Pesquisa Visual Computacional pode determinar se uma imagem está em preto e branco ou em cores e, para imagens com cores, identificar as cores dominantes e de detalhes.|
|**[Gerar uma miniatura](concept-generating-thumbnails.md)**|Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. A Pesquisa Visual Computacional primeiro gera uma miniatura de alta qualidade e, em seguida, analisa os objetos na imagem para determinar a *área de interesse*. A Pesquisa Visual Computacional corta a imagem para atender aos requisitos da área de interesse. A miniatura gerada pode ser apresentada usando uma taxa de proporção diferente da taxa de proporção da imagem original, dependendo de suas necessidades.|
|**[Obter a área de interesse](concept-generating-thumbnails.md#area-of-interest)**|Analise o conteúdo de uma imagem para retornar as coordenadas da *área de interesse*. Em vez de cortar a imagem e gerar uma miniatura, a Pesquisa Visual Computacional retorna as coordenadas da caixa delimitadora da região, de modo que o aplicativo de chamada possa modificar a imagem original conforme desejado.|

## <a name="extract-text-from-images"></a>Extrair texto de imagens

Use a [API de Leitura](concept-recognizing-text.md#read-api) da Pesquisa Visual Computacional para extrair um texto impresso e manuscrito de imagens para um fluxo de caracteres legível por computador. A API de Leitura usa nossos modelos mais recentes e funciona com texto em uma variedade de superfícies e telas de fundo, como recibos, cartazes, cartões de visita, cartas e quadros de comunicações. Atualmente, inglês é o único idioma com suporte.

Use também a API do [OCR (reconhecimento óptico de caracteres)](concept-recognizing-text.md#ocr-optical-character-recognition-api) para extrair um texto impresso em vários idiomas. Se necessário, o OCR corrige a rotação do texto reconhecido e fornece as coordenadas de quadro de cada palavra. O OCR dá suporte a 25 idiomas e detecta automaticamente o idioma do texto reconhecido.



## <a name="moderate-content-in-images"></a>Moderar conteúdo em imagens

Use a Pesquisa Visual Computacional para [detectar conteúdo para adulto e erótico](concept-detecting-adult-content.md) em uma imagem e retornar uma pontuação de confiança para ambos. Defina o filtro para detecção de conteúdo erótico e para adulto em uma escala deslizante para atender às suas preferências.

## <a name="use-containers"></a>Usar contêineres

[Use contêineres da Pesquisa Visual Computacional](computer-vision-how-to-install-containers.md) para reconhecer um texto impresso e manuscrito localmente pela instalação de um contêiner do Docker padronizado mais próximo aos dados.

## <a name="image-requirements"></a>Requisitos de imagem

A Pesquisa Visual Computacional pode analisar imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do arquivo da imagem deve ser menor que 4 MB (megabytes)
- As dimensões da imagem devem ser maiores que 50 x 50 pixels
  - Para a API de Leitura, as dimensões da imagem de entrada precisam estar entre 50 x 50 e 10000 x 10000 pixels.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Pesquisa Visual Computacional devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Introdução à Pesquisa Visual Computacional seguindo o guia de início rápido:

- [Início Rápido: Analisar uma imagem](quickstarts-sdk/csharp-analyze-sdk.md)
- [Início Rápido: Extrair texto escrito à mão](quickstarts-sdk/csharp-hand-text-sdk.md)
- [Início Rápido: Gerar uma miniatura](quickstarts-sdk/csharp-thumb-sdk.md)
