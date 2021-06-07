---
title: O que é o OCR (reconhecimento óptico de caracteres)?
titleSuffix: Azure Cognitive Services
description: O serviço de OCR (reconhecimento óptico de caracteres) extrai o texto visível em uma imagem e o retorna como cadeias de caracteres estruturadas.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 03/29/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3fff9f4bd34fc1defdb50f2eefbc8ac1f39b46af
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287370"
---
# <a name="what-is-optical-character-recognition"></a>O que é o OCR (reconhecimento óptico de caracteres)?

O serviço de OCR (reconhecimento óptico de caracteres) permite que você extraia textos impressos ou manuscritos de imagens, como fotos de placas de licença ou contêineres com números de série, bem como documentos&mdash;faturas, relatórios financeiros, artigos entre outros. Ela usa os modelos baseados em aprendizado profundo e trabalha com texto em uma variedade de superfícies e telas de fundo.

As APIs de OCR dão suporte à extração de textos impressos em [vários idiomas](./language-support.md). Siga nosso [início rápido](./quickstarts-sdk/client-library.md) para começar.

![Demonstrações de OCR](./Images/ocr-demo.gif)

Esta documentação contém os seguintes tipos de artigos:
* Os [inícios rápidos](./quickstarts-sdk/client-library.md) são instruções passo a passo que permitem fazer chamadas para o serviço e obter resultados em um período curto. 
* Os [guias de instruções](./Vision-API-How-to-Topics/call-read-api.md) contêm instruções para usar o serviço de maneiras mais específicas ou personalizadas.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="supported-languages"></a>Idiomas com suporte
As APIs de OCR dão suporte a um total de 73 idiomas para impressão do texto do estilo. Veja a lista completa de [idiomas compatíveis com o OCR](./language-support.md#optical-character-recognition-ocr). O OCR de estilo manuscrito tem suporte exclusivo para inglês.

## <a name="input-requirements"></a>Requisitos de entrada

A chamada de **Leitura** usa imagens e documentos como a entrada. Eles têm os seguintes requisitos:

* Formatos de arquivo compatíveis: JPEG, PNG, BMP, PDF e TIFF
* Para arquivos PDF e TIFF, até 2 mil páginas (apenas as duas primeiras páginas da camada gratuita) são processadas.
* O tamanho do arquivo precisa ser menor que 50 MB (4 MB para a camada gratuita) e ter como dimensões, no mínimo, 50 x 50 pixels e, no máximo, 10.000 x 10.000 pixels. 
* As dimensões do PDF precisam ter, no máximo, 17 x 17 polegadas, correspondentes aos tamanhos de papel ofício ou A3 e menores.

## <a name="read-api"></a>API de leitura 

A [API de Leitura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005) da Pesquisa Visual Computacional é a tecnologia de OCR mais recente do Azure ([conheça as novidades](./whats-new.md)) que extrai texto impresso (em vários idiomas), texto manuscrito (somente em inglês), dígitos e símbolos de moeda de imagens e documentos PDF de várias páginas. Ela é otimizada para extrair texto de imagens com muito texto e documentos PDF de várias páginas com idiomas mistos. Dá suporte à detecção de texto impresso e manuscrito na mesma imagem ou no mesmo documento.

![Como o OCR converte imagens e documentos em uma saída estruturada com o texto extraído](./Images/how-ocr-works.svg)


## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Usar a API de nuvem ou implantá-la no local
As APIs de nuvem de Leitura 3.x são a opção preferencial para a maioria dos clientes devido à facilidade de integração e produtividade rápida pronta para uso. O Azure e o serviço de Pesquisa Visual Computacional cuidam das necessidades de escala, desempenho, segurança de dados e conformidade enquanto você se concentra em atender às necessidades dos seus clientes.

Para a implantação local, o [contêiner do Docker de Leitura (versão prévia)](./computer-vision-how-to-install-containers.md) permite que você implante as novas funcionalidades de OCR no seu ambiente local. Contêineres são excelentes para especificar requisitos de segurança e governança de dados.

## <a name="ocr-api"></a>API de OCR

A [API de OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f20d) herdada usa um modelo de reconhecimento mais antigo, só dá suporte a imagens e é executada de modo síncrono, retornando imediatamente com o texto detectado. Confira a coluna de OCR de [idiomas compatíveis](./language-support.md#optical-character-recognition-ocr) para obter uma lista dos idiomas compatíveis.

## <a name="recognizetext-api"></a>API de Reconhecimento de Texto

> [!WARNING]
> As operações de Reconhecimento de Texto da Pesquisa Visual Computacional 2.0 estão no processo de serem preteridas em favor da nova [API de Leitura](#read-api) abordada neste artigo. Os clientes existentes devem [fazer a transição para ela usando operações de Leitura](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Segurança e privacidade de dados

Assim como ocorre com todos os Serviços Cognitivos, os desenvolvedores que usam o serviço de Pesquisa Visual Computacional devem estar cientes das políticas da Microsoft em relação aos dados do cliente. Confira a [página de Serviços Cognitivos](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) na Central de Confiabilidade da Microsoft para saber mais.

## <a name="next-steps"></a>Próximas etapas

- Introdução aos [guias de início rápido da API REST ou da biblioteca do clientes de OCR](./quickstarts-sdk/client-library.md).
- Saiba mais sobre a [API REST de Leitura 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/5d986960601faab4bf452005).
- Saiba mais sobre a [API REST de Leitura 3.2 de versão prévia pública](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/5d986960601faab4bf452005) com suporte para um total de 73 idiomas.
