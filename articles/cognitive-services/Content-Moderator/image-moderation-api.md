---
title: Imagem de moderação – Content Moderator
titlesuffix: Azure Cognitive Services
description: Use a moderação de imagem assistida por computador do Content Moderator e a ferramenta de revisão de humanos no circuito para moderar imagens para conteúdo adulto.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9f1df23d1f0f24787bb9267064ffd647eda2cb74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60699266"
---
# <a name="learn-image-moderation-concepts"></a>Aprender os conceitos de moderação de imagem

Use a moderação de imagem assistida por computador do Content Moderator e a [ferramenta de revisão de humanos no circuito](Review-Tool-User-Guide/human-in-the-loop.md) para moderar imagens para conteúdo adulto e vigoroso. Examine as imagens quanto a conteúdo de texto, extraia esse texto e detecte faces. Você pode combinar imagens com listas personalizadas e executar mais ações.

## <a name="evaluating-for-adult-and-racy-content"></a>Avaliação de conteúdo adulto e estimulante

A operação **Avaliar** retorna uma pontuação de confiança entre 0 e 1. Ela também retorna dados boolianos iguais a true ou false. Esses valores preveem se a imagem apresenta conteúdo adulto ou estimulante em potencial. Quando você chama a API com a imagem (arquivo ou URL), a resposta retornada inclui as seguintes informações:

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` representa a presença potencial de imagens que podem ser consideradas sexualmente explícitas ou de conteúdo para adulto em determinadas situações.
> - `isImageRacyClassified` representa a presença potencial de imagens que podem ser consideradas sexualmente sugestivas ou de conteúdo para adulto em determinadas situações.
> - As pontuações estão entre 0 e 1. Quanto maior a pontuação, maior o modelo estará prevendo que a categoria pode ser aplicável. Essa visualização se baseia em um modelo estatístico, em vez dos resultados codificados manualmente. É recomendável testar com o seu próprio conteúdo para determinar como cada categoria se alinha aos seus requisitos.
> - Os valores boolianos são true ou false dependendo dos limites da pontuação interna. Os clientes devem avaliar se desejam usar esse valor ou escolher limites personalizados com base nas políticas de conteúdo.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>Detectando texto com OCR (Reconhecimento Óptico de Caracteres)

A operação **OCR (Reconhecimento Óptico de Caracteres)** prevê a presença de conteúdo de texto em uma imagem e o extrai de moderação de texto, entre outros usos. Você pode especificar o idioma. Se você não especificar um idioma, a detecção usará inglês como padrão.

A resposta de serviço inclui as informações a seguir:
- O texto original.
- Os elementos de texto detectados com suas pontuações de confiança.

Extração de exemplo:

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>Detendo faces

Detectando rostos ajuda a detectar dados pessoais, como faces, nas imagens. Você detecta faces em potencial e o número de faces em potencial em cada imagem.

Uma resposta inclui estas informações:

- Contagem de faces
- Lista de locais de faces detectadas

Extração de exemplo:


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>Criando e gerenciando listas personalizadas

Em muitas comunidades online, depois que os usuários fazem upload de imagens ou de outro tipo de conteúdo, itens ofensivos podem ser compartilhados várias vezes nos dias, semanas e meses seguintes. Examinar e filtrar repetidamente a mesma imagem ou até mesmo versões ligeiramente modificadas da imagem de vários locais pode ter um custo alto, além de ser um processo propenso a erros.

Em vez de moderar a mesma imagem várias vezes, você pode adicionar imagens ofensivas à sua lista personalizada de conteúdo bloqueado. Dessa forma, o sistema de moderação de conteúdo comparará as imagens de entrada com as listas personalizadas e interromperá qualquer processamento adicional.

> [!NOTE]
> Há um limite máximo de **cinco listas de imagens** e cada lista **não deve exceder dez mil imagens**.
>

O Content Moderator fornece uma [API de Gerenciamento de Lista de Imagens](try-image-list-api.md) completa com operações para gerenciar listas de imagens personalizadas. Comece com o [Console de API de Listas de Imagens](try-image-list-api.md) e use os exemplos de código da API REST. Confira também o [Início rápido do .NET de Listas de Imagens](image-lists-quickstart-dotnet.md) se você estiver familiarizado com Visual Studio e C#.

## <a name="matching-against-your-custom-lists"></a>Correspondência com relação às suas listas personalizadas

A operação Correspondência permite realizar a correspondência difusa de imagens de entrada em relação a qualquer uma das suas listas personalizadas, criadas e gerenciadas usando as operações de Lista.

Se uma correspondência for encontrada, a operação retornará o identificador e as marcas de moderação da imagem correspondente. A resposta inclui estas informações:

- Pontuação de correspondência (entre 0 e 1)
- Imagem correspondente
- Marcas de imagem (atribuídas durante a moderação anterior)
- Rótulos de imagem

Extração de exemplo:

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>Ferramenta de análise humana

Para casos com mais nuances, use a [ferramenta de análise](Review-Tool-User-Guide/human-in-the-loop.md) do Content Moderator e sua API para capturar os resultados de moderação e o conteúdo na revisão para seus moderadores humanos. Eles examinam as marcações atribuídas por máquina e confirmam suas decisões finais.

![Revisão de imagem para moderadores humanos](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>Próximas etapas

Faça test drive do [console da API de Moderação de Imagem](try-image-api.md) e use os exemplos de código da API REST. Confira também o [Início rápido do .NET de moderação de imagem](image-moderation-quickstart-dotnet.md) se estiver familiarizado com Visual Studio e C#.
