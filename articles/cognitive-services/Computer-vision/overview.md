---
title: O que é a Pesquisa Visual Computacional?
titleSuffix: Azure Cognitive Services
description: O serviço de Pesquisa Visual Computacional fornece a você acesso a algoritmos avançados para processar imagens e retornar informações.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: computer vision, computer vision applications, computer vision service
ms.openlocfilehash: 62d82388d975f36c422f7eb1e9feff8ce2725d54
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033282"
---
# <a name="what-is-computer-vision"></a>O que é a Pesquisa Visual Computacional?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de Pesquisa Visual Computacional do Azure fornece acesso a algoritmos avançados que processam imagens e retornam informações com base nos recursos visuais de seu interesse. Por exemplo, a Pesquisa Visual Computacional pode determinar se uma imagem apresenta conteúdo para adulto, localizar marcas ou objetos específicos ou encontrar rostos humanos.

Você pode criar aplicativos da Pesquisa Visual Computacional por meio de um [SDK da biblioteca de clientes](./quickstarts-sdk/client-library.md) ou chamando a [API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) diretamente. Esta página cobre amplamente o que você pode fazer com a Pesquisa Visual Computacional.

## <a name="optical-character-recognition-ocr"></a>OCR (reconhecimento óptico de caracteres)

A Pesquisa Visual Computacional inclui funcionalidades de [OCR (reconhecimento óptico de caracteres)](concept-recognizing-text.md). Você pode usar a nova API de leitura para extrair texto impresso e manuscrito de imagens e documentos. Ela usa os modelos mais recentes e trabalha com texto em uma variedade de superfícies e telas de fundo. Eles incluem recibos, cartazes, cartões de visita, cartas e quadros de comunicação. As duas APIs de OCR dão suporte à extração de textos impressos em [vários idiomas](./language-support.md). Siga nosso [início rápido](./quickstarts-sdk/client-library.md) para começar.

## <a name="computer-vision-for-digital-asset-management"></a>Pesquisa Visual Computacional para gerenciamento de ativo digital

A Pesquisa Visual Computacional pode viabilizar muitos cenários de gerenciamento de ativos digitais (DAM). O DAM é o processo comercial de organizar, armazenar e recuperar ativos de mídia avançados e gerenciar permissões e direitos digitais. Por exemplo, uma empresa pode querer agrupar e identificar imagens com base em logotipos visíveis, rostos, objetos, cores e assim por diante. Ou é possível que você queira [gerar legendas para imagens](./Tutorials/storage-lab-tutorial.md) automaticamente e vincular palavras-chave que possam ser pesquisadas. Para ter uma solução completa de DAM usando os Serviços Cognitivos, a Pesquisa Cognitiva do Azure e os relatórios inteligentes, confira o [Guia do Acelerador de Solução de Mineração de Conhecimento](https://github.com/Azure-Samples/azure-search-knowledge-mining) no GitHub. Para ter outros exemplos de DAM, confira o repositório [Modelos de Solução de Pesquisa Visual Computacional](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates).

## <a name="analyze-images-for-insight"></a>Analisar imagens quanto a insights

Analise imagens para fornecer insights sobre os recursos visuais e as características. Todos os recursos na tabela a seguir são fornecidos pela API [Analisar Imagem](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b). Siga nosso [início rápido](./quickstarts-sdk/client-library.md) para começar.


### <a name="tag-visual-features"></a>Marcar recursos visuais

Identificar e marcar recursos visuais em uma imagem, de um conjunto de milhares de objetos reconhecíveis, seres vivos, cenários e ações. Quando as marcas forem ambíguas ou não pertencerem a um conhecimento comum, a resposta da API fornecerá dicas para esclarecer o contexto da tag. A marcação não está limitada ao assunto principal, como uma pessoa em primeiro plano, mas também inclui cenário (interno ou externo), móveis, ferramentas, plantas, animais, acessórios, gadgets e outros. [Marcar recursos visuais](concept-tagging-images.md)

### <a name="detect-objects"></a>Detectar objetos

A detecção de objetos é semelhante à marcação, mas a API retorna as coordenadas da caixa delimitadora para cada tag aplicada. Por exemplo, se uma imagem contiver um cachorro, um gato e uma pessoa, a operação Detect listará esses objetos junto com as coordenadas na imagem. Você pode usar essa funcionalidade para processar ainda mais as relações entre os objetos em uma imagem. Também permite que você saiba quando há várias instâncias da mesma tag em uma imagem. [Detectar objetos](concept-object-detection.md)

### <a name="detect-brands"></a>Detectar marcas

Identifique as marcas comerciais em imagens ou vídeos de um banco de dados de milhares de logotipos globais. Você pode usar esse recurso, por exemplo, para descobrir quais marcas são mais populares em mídia social ou mais predominantes no posicionamento de produto de mídia. [Detectar marcas](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Categorizar uma imagem

Identifique e categorize uma imagem inteira usando uma [taxonomia de categoria](Category-Taxonomy.md) com hierarquias hereditárias de pai/filho. As categorias podem ser usadas autonomamente ou com nossos novos modelos de marcação.<br/>Atualmente, o inglês é o único idioma com suporte para a marcação e categorização de imagens. [Categorizar uma imagem](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Descrever uma imagem

Gere uma descrição de uma imagem inteira em linguagem compreendida por humanos, usando frases completas. Os algoritmos da Pesquisa Visual Computacional geram várias descrições baseadas nos objetos identificados na imagem. Cada uma das descrições é avaliada e uma pontuação de confiança é gerada. Uma lista é então retornada, ordenada pela pontuação de confiança mais alta à mais baixa. [Descrever uma imagem](concept-describing-images.md)

### <a name="detect-faces"></a>Detectar faces

Detecte rostos em uma imagem e forneça informações sobre cada rosto detectado. A Pesquisa Visual Computacional retorna as coordenadas, o retângulo, o sexo e a idade de cada rosto detectado.<br/>A Pesquisa Visual Computacional fornece um subconjunto da funcionalidade do serviço [Detecção Facial](../face/index.yml). Você pode usar o serviço de Detecção Facial para uma análise mais detalhada, como identificação facial e detecção de pose. [Detectar faces](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Detectar tipos de imagem

Detecte características sobre uma imagem, por exemplo, se uma imagem é um desenho de linha se é, possivelmente, um clip-art. [Detectar tipos de imagem](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Detectar conteúdo específico de um domínio

Use modelos de domínio para detectar e identificar conteúdo específico de um assunto em uma imagem, como celebridades e pontos turísticos. Por exemplo, se uma imagem contiver pessoas, a Pesquisa Visual Computacional poderá usar um modelo de domínio para celebridades a fim de determinar se as pessoas detectadas na imagem são celebridades conhecidas. [Detectar conteúdo específico de um domínio](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Detectar o esquema de cores

Analise o uso de cores em uma imagem. A Pesquisa Visual Computacional pode determinar se uma imagem está em preto e branco ou em cores e, para imagens com cores, identificar as cores dominantes e de detalhes. [Detectar o esquema de cores](concept-detecting-color-schemes.md)

### <a name="generate-a-thumbnail"></a>Gerar uma miniatura

Analise o conteúdo de uma imagem para gerar uma miniatura adequada dessa imagem. A Pesquisa Visual Computacional primeiro gera uma miniatura de alta qualidade e, em seguida, analisa os objetos na imagem para determinar a *área de interesse*. A Pesquisa Visual Computacional corta a imagem para atender aos requisitos da área de interesse. A miniatura gerada pode ser apresentada usando uma taxa de proporção diferente da taxa de proporção da imagem original, dependendo de suas necessidades. [Gerar uma miniatura](concept-generating-thumbnails.md)

### <a name="get-the-area-of-interest"></a>Obter a área de interesse

Analise o conteúdo de uma imagem para retornar as coordenadas da *área de interesse*. Em vez de cortar a imagem e gerar uma miniatura, a Pesquisa Visual Computacional retorna as coordenadas da caixa delimitadora da região, de modo que o aplicativo de chamada possa modificar a imagem original conforme desejado. [Obter a área de interesse](concept-generating-thumbnails.md#area-of-interest)

## <a name="moderate-content-in-images"></a>Moderar conteúdo em imagens

Use a Pesquisa Visual Computacional para [detectar conteúdo para adulto](concept-detecting-adult-content.md) em uma imagem e retornar pontuações de confiança para diferentes classificações. O limite para a sinalização de conteúdo pode ser definido em uma escala deslizante para acomodar suas preferências.

## <a name="deploy-on-premises-using-docker-containers"></a>Implantação local usando contêineres do Docker

Use os contêineres da Pesquisa Visual Computacional para implantar recursos de API no local. Esses contêineres do Docker permitem que você aproxime o serviço dos seus dados para fins de conformidade, segurança ou outras razões operacionais. A Pesquisa Visual Computacional oferece os seguintes contêineres:

* O [contêiner de OCR de leitura da Pesquisa Visual Computacional (versão prévia)](computer-vision-how-to-install-containers.md) permite que você reconheça textos impressos e manuscritos em imagens.
* O [contêiner de análise espacial da Pesquisa Visual Computacional (versão prévia)](spatial-analysis-container.md) permite que você analise vídeos de streaming em tempo real para entender as relações espaciais existentes entre as pessoas e a movimentação delas nos ambientes físicos.

## <a name="image-requirements"></a>Requisitos de imagem

A Pesquisa Visual Computacional pode analisar imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do arquivo da imagem deve ser menor que 4 MB (megabytes)
- As dimensões da imagem devem ser maiores que 50 x 50 pixels
  - Para a API de Leitura, as dimensões da imagem de entrada precisam estar entre 50 x 50 e 10000 x 10000 pixels.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Pesquisa Visual Computacional devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Comece a usar a Pesquisa Visual Computacional seguindo o guia de início rápido na linguagem de desenvolvimento de sua preferência:

- [Início Rápido: Bibliotecas de clientes ou API REST da Pesquisa Visual Computacional](./quickstarts-sdk/client-library.md)
