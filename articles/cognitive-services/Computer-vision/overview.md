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
ms.date: 03/29/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: computer vision, computer vision applications, computer vision service
ms.openlocfilehash: 875ef961148668a83e94c116622b5e461d2413fa
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286127"
---
# <a name="what-is-computer-vision"></a>O que é a Pesquisa Visual Computacional?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

O serviço de Pesquisa Visual Computacional do Azure fornece acesso a algoritmos avançados que processam imagens e retornam informações com base nos recursos visuais de seu interesse. 

| Serviço|Descrição|
|---|---|
|[OCR (reconhecimento óptico de caracteres)](overview-ocr.md)|O serviço de OCR (reconhecimento óptico de caracteres) extrai textos de imagens. Use a nova API de Leitura para extrair um texto impresso e manuscrito de imagens e documentos. Ela usa os modelos baseados em aprendizado profundo e trabalha com textos em uma variedade de superfícies e telas de fundo. Eles incluem documentos comerciais, faturas, recibos, cartazes, cartões de visita, cartas e quadros de comunicação. As APIs de OCR dão suporte à extração de textos impressos em [vários idiomas](./language-support.md). Siga o [guia de início rápido do OCR](quickstarts-sdk/client-library.md) para obter uma introdução.|
|[Análise de imagens](overview-image-analysis.md)| O serviço de Análise de Imagem extrai muitos recursos visuais de imagens, como objetos, rostos, conteúdo para adulto e descrições de texto geradas automaticamente. Siga o [guia de início rápido da Análise de Imagem](quickstarts-sdk/image-analysis-client-library.md) para obter uma introdução.|
| [Análise Espacial](intro-to-spatial-analysis-public-preview.md)| O serviço de Análise Espacial analisa a presença e o movimento de pessoas em um feed de vídeo e produz eventos aos quais outros sistemas podem responder. Instale o [contêiner de Análise Espacial](spatial-analysis-container.md) para obter uma introdução.|

## <a name="computer-vision-for-digital-asset-management"></a>Pesquisa Visual Computacional para gerenciamento de ativo digital

A Pesquisa Visual Computacional pode viabilizar muitos cenários de gerenciamento de ativos digitais (DAM). O DAM é o processo comercial de organizar, armazenar e recuperar ativos de mídia avançados e gerenciar permissões e direitos digitais. Por exemplo, uma empresa pode querer agrupar e identificar imagens com base em logotipos visíveis, rostos, objetos, cores e assim por diante. Ou é possível que você queira [gerar legendas para imagens](./Tutorials/storage-lab-tutorial.md) automaticamente e vincular palavras-chave que possam ser pesquisadas. Para ter uma solução completa de DAM usando os Serviços Cognitivos, a Pesquisa Cognitiva do Azure e os relatórios inteligentes, confira o [Guia do Acelerador de Solução de Mineração de Conhecimento](https://github.com/Azure-Samples/azure-search-knowledge-mining) no GitHub. Para ter outros exemplos de DAM, confira o repositório [Modelos de Solução de Pesquisa Visual Computacional](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates).

## <a name="image-requirements"></a>Requisitos de imagem

A Pesquisa Visual Computacional pode analisar imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP
- O tamanho do arquivo da imagem deve ser menor que 4 MB (megabytes)
- As dimensões da imagem devem ser maiores que 50 x 50 pixels
  - Para a API de Leitura, as dimensões da imagem de entrada precisam estar entre 50 x 50 e 10000 x 10000 pixels.

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Pesquisa Visual Computacional devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

Siga um guia de início rápido para implementar e executar um serviço na linguagem de desenvolvimento de sua preferência.

* [Guia de início rápido: OCR (reconhecimento óptico de caracteres)](quickstarts-sdk/client-library.md)
* [Guia de início rápido: Análise de Imagem](quickstarts-sdk/image-analysis-client-library.md)
* [Guia de início rápido: Contêiner de Análise Espacial](spatial-analysis-container.md)
