---
title: Reconhecer texto impresso/manuscrito, Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Conceitos relacionados ao reconhecimento de texto impresso e manuscrito em imagens usando a API da Pesquisa Visual Computacional.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: cfbbd0b353699c4b04ede07df0450e66bd59612f
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311959"
---
# <a name="recognize-printed-and-handwritten-text"></a>Reconhecer texto impresso e manuscrito

O Pesquisa Visual Computacional fornece vários serviços que detectam e extraem texto impresso ou manuscrito que aparece em imagens. Isso é útil em uma variedade de cenários, como a criação de observações, registros médicos, segurança e serviços bancários. As três seções a seguir detalham três APIs de reconhecimento de texto diferentes, cada uma otimizada para diferentes casos de uso.

## <a name="read-api"></a>API de leitura

A API de leitura detecta o conteúdo de texto em uma imagem usando nossos modelos de reconhecimento mais recentes e converte o texto identificado em um fluxo de caracteres legível por máquina. Ele é otimizado para imagens com texto pesado (como documentos que foram digitalizados digitalmente) e para imagens com muitos ruídos visuais. Ele determinará qual modelo de reconhecimento usar para cada linha de texto, dando suporte a imagens com texto impresso e manuscrito. A API de leitura é executada de forma assíncrona porque documentos maiores podem levar vários minutos para retornar um resultado.

A operação de leitura mantém os agrupamentos de linhas originais de palavras reconhecidas em sua saída. Cada linha vem com coordenadas de caixa delimitadora, e cada palavra dentro da linha também tem suas próprias coordenadas. Se uma palavra tiver sido reconhecida com baixa confiança, essas informações também serão transmitidas. Consulte os [documentos de referência da API de leitura](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/2afb498089f74080d7ef85eb) para saber mais.

> [!NOTE]
> Este recurso só está disponível para texto em inglês.

### <a name="image-requirements"></a>Requisitos de imagem

A API de leitura funciona com imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG, BMP, PDF ou TIFF.
- As dimensões da imagem devem estar entre 50 x 50 e 10000 x 10000 pixels. As páginas PDF devem ter 17 x 17 polegadas ou menos.
- O tamanho do arquivo da imagem deve ser inferior a 20 megabytes (MB).

### <a name="limitations"></a>Limitações

Se você estiver usando uma assinatura de camada gratuita, a API de leitura só processará as duas primeiras páginas de um documento PDF ou TIFF. Com uma assinatura paga, ele processará até 200 páginas. Observe também que a API detectará, no máximo, 300 linhas por página.

## <a name="ocr-optical-character-recognition-api"></a>API de OCR (reconhecimento óptico de caracteres)

A API de OCR (reconhecimento óptico de caracteres) do Pesquisa Visual Computacional é semelhante à API de leitura, mas é executada de forma síncrona e não é otimizada para documentos grandes. Ele usa um modelo de reconhecimento anterior, mas funciona com mais idiomas; consulte [suporte a idiomas](language-support.md#text-recognition) para obter uma lista completa dos idiomas com suporte.

Se necessário, o OCR corrige a rotação do texto reconhecido, retornando o deslocamento de rotação em graus do eixo horizontal da imagem. O OCR também fornece as coordenadas de quadro de cada palavra, como mostrado na ilustração a seguir.

![Uma imagem sendo girada e seu texto sendo lido e delimitado](./Images/vision-overview-ocr.png)

Consulte os [documentos de referência do OCR](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API de OCR funciona em imagens que atendem aos seguintes requisitos:

* A imagem deve ser apresentada no formato JPEG, PNG, GIF ou BMP.
* O tamanho da imagem de entrada deve estar entre 50 x 50 e 4200 x 4200 pixels.
* O texto na imagem pode ser girado por um ângulo múltiplo de 90 graus mais um ângulo múltiplo de 40 graus.

### <a name="limitations"></a>Limitações

Em fotografias em que o texto é dominante, falsos positivos podem vir de palavras reconhecidas parcialmente. Em algumas fotografias, especialmente fotos sem qualquer texto, a precisão pode variar dependendo do tipo de imagem.

## <a name="recognize-text-api"></a>API de Reconhecimento de Texto

> [!NOTE]
> A API de Reconhecimento de Texto está sendo preterida em favor da API de leitura. A API de leitura tem recursos semelhantes e é atualizada para manipular arquivos PDF, TIFF e de várias páginas.

A API de Reconhecimento de Texto é semelhante ao OCR, mas é executada de forma assíncrona e usa modelos de reconhecimento atualizados. Consulte os [documentos de referência da API reconhecimento de texto](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) para saber mais.

### <a name="image-requirements"></a>Requisitos de imagem

A API Reconhecimento de Texto funciona com imagens que atendem aos seguintes requisitos:

- A imagem deve ser apresentada no formato JPEG, PNG ou BMP.
- As dimensões da imagem devem estar entre 50 x 50 e 4200 x 4200 pixels.
- O tamanho do arquivo da imagem deve ser inferior a 4 megabytes (MB).

## <a name="limitations"></a>Limitações

A precisão das operações de reconhecimento de texto depende da qualidade das imagens. Os fatores a seguir podem causar uma leitura imprecisa:

* Imagens desfocadas.
* Texto manuscrito ou cursivo.
* Estilos de fonte artísticos.
* Tamanho de texto pequeno.
* Telas de fundo complexas, sombras, brilho sobre o texto ou distorção da perspectiva.
* Letras maiúsculas Superdimensionadas ou ausentes no início das palavras.
* Texto subscrito, sobrescrito ou tachado.

## <a name="next-steps"></a>Próximas etapas

Siga o guia de início rápido de [OCR (extrair texto impresso)](./quickstarts/csharp-print-text.md) para implementar o C# reconhecimento de texto em um aplicativo simples.
