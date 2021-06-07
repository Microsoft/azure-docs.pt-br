---
title: Layouts – Reconhecimento de Formulários
titleSuffix: Azure Cognitive Services
description: Aprenda conceitos relacionados à análise de layout com a API do Reconhecimento de Formulários – usos e limites.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: cc19d5652f416657cbcd339de61379265587b4e7
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505375"
---
# <a name="form-recognizer-layout-service"></a>Serviço de Layout do Reconhecimento de Formulários

O Reconhecimento de Formulários do Azure pode extrair texto, tabelas, marcas de seleção e informações de estrutura de documentos usando o serviço de Layout. A API de Layout permite que os clientes obtenham documentos em uma variedade de formatos e retornem representações de dados estruturados desses documentos. Ela combina nossos poderosos recursos de [OCR (reconhecimento óptico de caracteres)](../computer-vision/overview-ocr.md) com modelos de aprendizado profundo para extrair texto, tabelas, marcas de seleção e estrutura do documento. 

## <a name="what-does-the-layout-service-do"></a>O que o serviço de Layout faz?

A API de Layout extrai texto, tabelas, marcas de seleção e informações de estrutura de documentos com precisão excepcional e retorna uma resposta JSON organizada e estruturada. Os documentos podem ser de uma variedade de formatos e qualidade, incluindo imagens capturadas por telefone, documentos digitalizados e PDFs digitais. A API de Layout extrairá com precisão a saída estruturada de todos esses documentos.

![Exemplo de layout](./media/layout-tool-example.JPG)

## <a name="try-it-out"></a>Experimente

Para experimentar o serviço de Layout do Reconhecimento de Formulários, acesse a ferramenta de interface do usuário de exemplo online:

> [!div class="nextstepaction"]
> [Experimentar o Reconhecimento de Formulários](https://fott-preview.azurewebsites.net)

Você precisará de uma assinatura do Azure ([crie uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)), bem como um ponto de extremidade e uma chave de [recurso do Reconhecimento de Formulários](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) para experimentar a API de Layout do Reconhecimento de Formulários. 

![Captura de tela da interface do usuário de exemplo; o texto, as tabelas e as marcas de seleção de um documento são analisados](./media/analyze-layout.png)

### <a name="input-requirements"></a>Requisitos de entrada 

[!INCLUDE [input requirements](./includes/input-requirements-receipts.md)]

## <a name="the-analyze-layout-operation"></a>A operação Analisar Layout

Primeiro, chame a operação [Analisar Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync). A operação Analisar Layout usa um documento (imagem, TIFF ou arquivo PDF) como a entrada e extrai o texto, as tabelas, as marcas de seleção e a estrutura do documento. A chamada retorna um campo de cabeçalho de resposta chamado `Operation-Location`. O valor `Operation-Location` é uma URL que contém a ID de Resultado a ser usada na próxima etapa.

|Cabeçalho de resposta| URL do resultado |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.1-preview.3/layout/analyzeResults/44a436324-fc4b-4387-aa06-090cfbf0064f` |

### <a name="natural-reading-order-output-latin-only"></a>Saída em ordem de leitura natural (somente idiomas latinos)

Você pode especificar a ordem em que as linhas de texto são geradas com o parâmetro de consulta `readingOrder`. Use `natural` para uma saída de ordem de leitura mais amigável, conforme mostrado no exemplo a seguir. Esse recurso é compatível apenas com idiomas latinos.

:::image type="content" source="../Computer-vision/Images/ocr-reading-order-example.png" alt-text="Exemplo de ordem de Leitura de Layout" lightbox="../Computer-vision/Images/ocr-reading-order-example.png":::

### <a name="select-page-numbers-or-ranges-for-text-extraction"></a>Selecionar números de página ou intervalos para extração de texto

Para grandes documentos com várias páginas, use o parâmetro de consulta `pages` para indicar números de página ou intervalos de página específicos para a extração de texto. O exemplo a seguir mostra um documento com 10 páginas, com texto extraído para ambos os casos – todas as páginas (1 a 10) e páginas selecionadas (3 a 6).

:::image type="content" source="../Computer-vision/Images/ocr-select-pages.png" alt-text="Saída de páginas selecionadas de layout":::

## <a name="the-get-analyze-layout-result-operation"></a>A operação Obter Resultado de Analisar Layout

A segunda etapa é chamar a operação [Obter Resultado de Analisar Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult). Essa operação usa como entrada a ID de Resultado que foi criada pela operação Analisar Layout. Ela retorna uma resposta JSON que contém um campo de **status** com os seguintes valores possíveis. 

|Campo| Tipo | Valores possíveis |
|:-----|:----:|:----|
|status | string | `notStarted`: a operação de análise não foi iniciada.<br /><br />`running`: a operação de análise está em andamento.<br /><br />`failed`: a operação de análise falhou.<br /><br />`succeeded`: a operação de análise foi bem-sucedida.|

Chame essa operação iterativamente até que ela retorne o valor `succeeded`. Use um intervalo de três a cinco segundos para evitar exceder a taxa de RPS (solicitações por segundo).

Quando o campo **status** tiver o valor `succeeded`, a resposta JSON incluirá o layout extraído, o texto, as tabelas e as marcas de seleção. Os dados extraídos incluem linhas e palavras de texto extraídas, caixas delimitadas, aparência de texto com indicação manuscrita, tabelas e marcas de seleção com a indicação selecionada/não selecionada. 

### <a name="handwritten-classification-for-text-lines-latin-only"></a>Classificação manuscrita para linhas de texto (somente idiomas latinos)

A resposta inclui classificar se cada linha de texto tem um estilo manuscrito ou não, junto com uma pontuação de confiança. Esse recurso é compatível apenas com idiomas latinos. O exemplo a seguir mostra a classificação manuscrita para o texto na imagem.

:::image type="content" source="../Computer-vision/Images/ocr-handwriting-classification.png" alt-text="exemplo de classificação de manuscrito":::

### <a name="sample-json-output"></a>Saída JSON de exemplo

A resposta para a operação *Obter Resultado de Analisar Layout* é uma representação estruturada do documento com todas as informações extraídas. Confira aqui um [arquivo de documento de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout.pdf) e a respectiva [saída de layout de exemplo](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-layout-output.json) de saída estruturada.

A saída JSON tem duas partes:

* O nó `readResults` contém todas as marcas de texto e seleção reconhecidas. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. 
* O nó `pageResults` contém as tabelas e células extraídas com as caixas delimitadoras, a confiança e uma referência às linhas e palavras em "readResults".

## <a name="example-output"></a>Saída de exemplo

### <a name="text"></a>Texto

A API de Layout extrai texto de documentos (PDF, TIFF) e imagens (JPG, PNG, BMP) com várias cores e ângulos de texto. Ela aceita fotos de documentos, faxes, texto impresso e/ou manuscrito (somente em inglês) e modos mistos. O texto é extraído com informações fornecidas em linhas, palavras, caixas delimitadoras, pontuações de confiança e estilo (manuscrito ou outro). Todas as informações de texto são incluídas na seção `readResults` da saída JSON. 

### <a name="tables"></a>Tabelas

A API de Layout extrai tabelas de documentos (PDF, TIFF) e imagens (JPG, PNG, BMP). Os documentos podem ser fotografados ou digitalizados. As tabelas podem ser complexas com células ou colunas mescladas, com ou sem bordas e com ângulos peculiares. As informações extraídas da tabela incluem o número de colunas e linhas e a extensão de linhas e de colunas. Cada célula é extraída com sua caixa delimitadora e a referência ao texto extraído na seção `readResults`. As informações da tabela estão localizadas na seção `pageResults` da saída JSON. 

![Exemplo de tabelas](./media/tables-example.jpg)

### <a name="selection-marks"></a>Marcas de seleção

A API de Layout também extrai marcas de seleção de documentos. As marcas de seleção extraídas incluem a caixa delimitadora, a confiança e o estado (selecionado/não selecionado). As informações da marca de seleção são extraídas na seção `readResults` da saída JSON. 

## <a name="next-steps"></a>Próximas etapas

* Experimente sua própria extração de layout usando a [Ferramenta de interface do usuário de exemplo do Reconhecimento de Formulários](https://fott-preview.azurewebsites.net/)
* Conclua um [guia de início rápido do Reconhecimento de Formulários](quickstarts/client-library.md) para começar a extrair layouts na linguagem de desenvolvimento de sua escolha.

## <a name="see-also"></a>Confira também

* [O que é o Reconhecimento de Formulários?](./overview.md)
* [Documentos de referência da API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)
