---
title: Conceitos de detecção e atributos de face
titleSuffix: Azure Cognitive Services
description: A detecção facial é a ação de localizar faces humanas em uma imagem e, opcionalmente, retornar tipos diferentes de dados relacionados à face.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: pafarley
ms.openlocfilehash: 68d1e9744d937cf80327c3f41cc69f4af97d3400
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600177"
---
# <a name="face-detection-and-attributes"></a>Detecção de face e atributos

Este artigo explica os conceitos de detecção de face e dados de atributos de face. A detecção facial é a ação de localizar faces humanas em uma imagem e, opcionalmente, retornar tipos diferentes de dados relacionados à face.

Você usa a operação de [detecção de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para detectar faces em uma imagem. No mínimo, cada face detectada corresponde a um campo faceRectangle na resposta. Esse conjunto de coordenadas de pixel para o lado esquerdo, superior, largura e altura marca a face localizada. Usando essas coordenadas, você pode obter o local da face e seu tamanho. Na resposta da API, os rostos são listados na ordem de tamanho do maior para o menor.

## <a name="face-id"></a>Face ID

A ID de face é uma cadeia de caracteres de identificador exclusivo para cada face detectada em uma imagem. Você pode solicitar uma ID facial em sua chamada à API de [detecção de face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) .

## <a name="face-landmarks"></a>Pontos de referência facial

As dicas de referência são um conjunto de pontos fáceis de encontrar em uma face, como o Pupils ou a gorjeta do nariz. Por padrão, há 27 pontos de referência facial predefinidos. A figura a seguir mostra todos os 27 pontos:

![Um diagrama de face com todos os 27 pontos de referência rotulados](../Images/landmarks.1.jpg)

As coordenadas dos pontos são retornadas em unidades de pixels.

## <a name="attributes"></a>Atributos

Os atributos são um conjunto de recursos que, opcionalmente, podem ser detectados pela API de [detecção facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) . Os seguintes atributos podem ser detectados:

* **Idade**. A idade estimada em anos de uma face específica.
* **Desfoque**. O desfoque da face na imagem. Esse atributo retorna um valor entre zero e um e uma classificação informal de baixo, médio ou alto.
* **Emoção**. Uma lista de emoções com a confiança de detecção para a face determinada. As pontuações de confiança são normalizadas e as pontuações em todas as emoções somam-se a uma. As emoções retornadas são felicidade, tristeza, neutral, raiva, contentative, aversão, surpresa e medo.
* **Exposição**. A exposição da face na imagem. Esse atributo retorna um valor entre zero e um e uma classificação informal de underExposure, goodExposure ou superexposição.
* **Cabelo facial**. A presença de cabelo facial estimada e o comprimento da face fornecida.
* **Sexo**. O gênero estimado da face fornecida. Os valores possíveis são masculino, feminino e sexo.
* **Óculos**. Se a face fornecida tem óculos. Os valores possíveis são novidros, ReadingGlasses, óculos e Goggles de nada.
* **Cabelo**. O tipo de cabelo da face. Esse atributo mostra se o cabelo está visível, se baldness é detectado e quais cores de cabelo são detectadas.
* **Pose de cabeçalho**. A orientação da face no espaço 3D. Esse atributo é descrito pelos ângulos de inclinação, roll e guinada em graus. Os intervalos de valores são de-90 graus a 90 graus,-90 graus a 90 graus e-90 graus para 90 graus, respectivamente. Consulte o diagrama a seguir para obter os mapeamentos de ângulo:

    ![Uma cabeça com os eixos de pitch, rolo e guinada rotulados](../Images/headpose.1.jpg)
* **Composição**. Se a face tem composição. Esse atributo retorna um valor booliano para eyeMakeup e lipMakeup.
* **Ruído**. O ruído visual detectado na imagem de face. Esse atributo retorna um valor entre zero e um e uma classificação informal de baixo, médio ou alto.
* **Oclusão**. Se há objetos bloqueando partes da face. Esse atributo retorna um valor booliano para eyeOccluded, foreheadOccluded e mouthOccluded.
* **Smiley**. A expressão de Smiley da face fornecida. Esse valor é entre zero e nenhum Smiley e um para um Smiley claro.

> [!IMPORTANT]
> Os atributos de face são previstos com o uso de algoritmos estatísticos. Eles talvez nem sempre sejam precisos. Tome cuidado ao tomar decisões com base em dados de atributos.

## <a name="input-data"></a>Dados de entrada

Use as seguintes dicas para garantir que suas imagens de entrada forneçam os resultados de detecção mais precisos:

* Os formatos de imagem de entrada com suporte são JPEG, PNG, GIF para o primeiro quadro e BMP.
* O tamanho do arquivo de imagem não deve ser maior que 6 MB.
* O tamanho mínimo de face detectável é 36 x 36 pixels em uma imagem que não é maior que 1920 x 1080 pixels. Imagens com mais de 1920 x 1080 pixels têm um tamanho de face mínimo proporcionalmente maior. Reduzir o tamanho da face pode fazer com que algumas faces não sejam detectadas, mesmo se forem maiores do que o tamanho de face mínimo detectável.
* O tamanho máximo de face detectável é de 4096 x 4096 pixels.
* As faces fora do intervalo de tamanho de 36 x 36 a 4096 x 4096 pixels não serão detectadas.
* Algumas faces podem não ser detectadas devido a desafios técnicos. Ângulos de face radicais (pose de cabeça) ou oclusão facial (objetos como óculos ou hands que bloqueiam parte da face) podem afetar a detecção. Os rostos frontais e Near-frontais fornecem os melhores resultados.

Se você estiver detectando rostos de um feed de vídeo, poderá melhorar o desempenho ajustando certas configurações em sua câmera de vídeo:

* **Suavização**: muitas câmeras de vídeo aplicam um efeito de suavização. Você deve desativar essa opção se puder, pois ela cria um desfoque entre os quadros e reduz a clareza.
* **Velocidade do obturador**: uma velocidade de obturador mais rápida reduz a quantidade de movimento entre os quadros e torna cada quadro mais claro. Recomendamos velocidades do obturador de 1/60 segundo ou mais rápido.
* **Ângulo do obturador**: algumas câmeras especificam ângulo do obturador em vez de velocidade do obturador. Você deve usar um ângulo do obturador inferior, se possível. Isso resultará em quadros de vídeo mais claros.

    >[!NOTE]
    > Uma câmera com um ângulo do obturador inferior receberá menos luz em cada quadro, de modo que a imagem será mais escura. Você precisará determinar o nível certo a ser usado.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com os conceitos de detecção facial, saiba como escrever um script que detecta rostos em uma determinada imagem.

* [Detectar faces em uma imagem](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
