---
title: Layouts-reconhecedor de formulário
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de layout com a API do reconhecedor de formulário-uso e limites.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: pafarley
ms.openlocfilehash: d529e74614a4fd5ee49d7e6161c65f68e8e76fce
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585051"
---
# <a name="form-recognizer-layout-service"></a>Serviço de layout do reconhecedor de formulário

O reconhecedor de formulários do Azure pode extrair texto, tabelas, marcas de seleção e informações de estrutura de documentos usando seu serviço de layout. A API de layout permite que os clientes consigam documentos em uma variedade de formatos e retornem dados estruturados e representação do documento. Ele combina nossos poderosos recursos de [OCR (reconhecimento óptico de caracteres)](../computer-vision/concept-recognizing-text.md) com documentos que compreendem modelos de aprendizado profundo para extrair texto, tabelas, marcas de seleção e estrutura de documentos. 

## <a name="what-does-the-layout-service-do"></a>O que o serviço de layout faz?

A API de layout extrai texto, tabelas, marcas de seleção e informações de estrutura de documentos com precisão excepcional e os retorna em uma resposta de JSON estruturada organizada. Os documentos podem ser de uma variedade de formatos e qualidade, incluindo imagens capturadas por telefone, documentos digitalizados e PDFs digitais. A API de layout extrairá a saída estruturada de todos esses documentos.

![Exemplo de layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço de layout do reconhecedor de formulário, vá para a ferramenta de interface do usuário de exemplo online:

> [!div class="nextstepaction"]
> [Interface de usuário de exemplo](https://fott-preview.azurewebsites.net/)

Você precisará de uma assinatura do Azure ([crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)) e um ponto de extremidade de [recurso do reconhecedor de formulário](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) e a chave para testar a API de layout do reconhecedor de formulário. 

![Captura de tela de exemplo; o texto, as tabelas e as marcas de seleção de um documento são analisados](./media/analyze-layout.png)

### <a name="input-requirements"></a>Requisitos de entrada 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>A operação de análise de layout

A operação [analisar layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync) usa um documento (imagem, TIFF ou arquivo PDF) como a entrada e extrai o texto, as tabelas, as marcas de seleção e a estrutura do documento. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location` . O `Operation-Location` valor é uma URL que contém a ID de resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.2/prebuilt/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

## <a name="the-get-analyze-layout-result-operation"></a>A operação obter resultado do layout de análise

A segunda etapa é chamar a operação [Get Analyze layout Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/GetAnalyzeLayoutResult) . Essa operação usa como entrada a ID de resultado que foi criada pela operação de análise de layout. Ele retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. 

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | `notStarted`: A operação de análise não foi iniciada.<br /><br />`running`: A operação de análise está em andamento.<br /><br />`failed`: Falha na operação de análise.<br /><br />`succeeded`: A operação de análise foi bem-sucedida.|

Você chama essa operação iterativamente até que ela retorne com o `succeeded` valor. Use um intervalo de 3 a 5 segundos para evitar exceder a taxa de solicitações por segundo (RPS).

Quando o campo **status** tiver o `succeeded` valor, a resposta JSON incluirá os resultados de extração de layout, o texto, as tabelas e as marcas de seleção extraídas. Os dados extraídos contêm as linhas e palavras de texto extraídas, a caixa delimitadora, a aparência de texto, a indicação manuscrita, as tabelas e as marcas de seleção com uma indicação de selecionado/não selecionado. 

### <a name="sample-json-output"></a>Saída JSON de exemplo

A resposta para a operação obter resultado do layout de análise será a representação estruturada do documento com todas as informações extraídas. Consulte aqui para obter um [arquivo de documento de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) e sua saída de layout de [exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json)de saída estruturada.

A saída JSON tem duas partes: 
* `"readResults"` o nó contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* `"pageResults"` o nó contém as tabelas e células extraídas com suas caixas delimitadoras, confiança e uma referência às linhas e palavras em "readResults".

## <a name="example-output"></a>Saída de exemplo

### <a name="text"></a>Texto

O layout extrai texto de documentos (PDF, TIFF) e imagens (jpg, png, BMP) com diferentes ângulos de texto, cores, ângulos, fotos de documentos, faxes, impressos, manuscritos (somente em inglês) e modos mistos. O texto é extraído com informações sobre linhas, palavras, caixas delimitadas, pontuações de confiança e estilo (manuscrito ou outro). Todas as informações de texto são incluídas na `"readResults"` seção da saída JSON. 

### <a name="tables"></a>Tabelas

O layout extrai tabelas de documentos (PDF, TIFF) e imagens (jpg, png, BMP). Os documentos podem ser digitalizados, fotografados ou digitalizados. As tabelas podem ser tabelas complexas com células ou colunas mescladas, com ou sem bordas, e com ângulos ímpares. As tabelas extraídas incluem o número de colunas e linhas, intervalo de linhas e span de coluna. Cada célula é extraída com sua caixa delimitadora e referência ao texto extraído na `"readResults"` seção. As informações de tabela estão localizadas na `"pageResults"` seção da saída JSON. 

![Exemplo de tabelas](./media/tables-example.jpg)

### <a name="selection-marks"></a>Marcas de seleção

O layout também extrai marcas de seleção de documentos. As marcas de seleção extraídas incluem a caixa delimitadora, a confiança e o estado (selecionado/não selecionado). As informações de marca de seleção são extraídas na `"readResults"` seção da saída JSON. 

## <a name="next-steps"></a>Próximas etapas

- Experimente sua própria extração de layout usando a [interface do usuário de exemplo do reconhecedor de formulário](https://fott-preview.azurewebsites.net/)
- Conclua um [início rápido do reconhecedor de formulário](quickstarts/client-library.md) para começar a extrair layouts na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
* [Documentos de referência da API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeLayoutAsync)