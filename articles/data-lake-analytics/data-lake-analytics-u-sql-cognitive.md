---
title: Usando recursos cognitivos do U-SQL no Azure Data Lake Analytics | Microsoft Docs
description: "Saiba como usar a inteligência de recursos Cognitivos no U-SQL"
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: jhubbard
editor: cgronlun
ms.assetid: 019c1d53-4e61-4cad-9b2c-7a60307cbe19
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: saveenr
ms.openlocfilehash: a651fe045d7eb1265f698ebb89843fd4c2b1c436
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-get-started-with-the-cognitive-capabilities-of-u-sql"></a>Tutorial: introdução aos recursos Cognitivos do U-SQL

## <a name="overview"></a>Visão geral
Os recursos cognitivos para o U-SQL permitem aos desenvolvedores colocar inteligência em seus programas de Big Data. 

Os seguintes recursos cognitivos estão disponíveis:
* Geração de imagens: detectar faces
* Geração de imagens: detectar emoção
* Geração de imagens: detectar objetos (marcação)
* Geração de imagens: OCR (reconhecimento óptico de caracteres)
* Texto: Extração de Frases-chave
* Texto: Análise de Sentimento

## <a name="how-to-use-cognitive-in-your-u-sql-script"></a>Como usar os Cognitivos em seu script U-SQL

O processo geral é simples:

* Usar a instrução REFERENCE ASSEMBLY para habilitar os recursos cognitivos para o Script U-SQL
* Use a PROCESS em um conjunto de linhas de entrada usando um Cognitivo UDO, para gerar um conjunto de linhas de saída

### <a name="detecting-objects-in-images"></a>Detecção de objetos em imagens

O exemplo a seguir mostra como usar os recursos cognitivos para detectar objetos em imagens.

```
REFERENCE ASSEMBLY ImageCommon;
REFERENCE ASSEMBLY FaceSdk;
REFERENCE ASSEMBLY ImageEmotion;
REFERENCE ASSEMBLY ImageTagging;
REFERENCE ASSEMBLY ImageOcr;

// Get the image data

@imgs =
    EXTRACT 
        FileName string, 
        ImgData byte[]
    FROM @"/usqlext/samples/cognition/{FileName}.jpg"
    USING new Cognition.Vision.ImageExtractor();

//  Extract the number of objects on each image and tag them 

@tags =
    PROCESS @imgs 
    PRODUCE FileName,
            NumObjects int,
            Tags SQL.MAP<string, float?>
    READONLY FileName
    USING new Cognition.Vision.ImageTagger();

@tags_serialized =
    SELECT FileName,
           NumObjects,
           String.Join(";", Tags.Select(x => String.Format("{0}:{1}", x.Key, x.Value))) AS TagsString
    FROM @tags;

OUTPUT @tags_serialized
    TO "/tags.csv"
    USING Outputers.Csv();
```
Para obter mais exemplos, consulte **U-SQL/Exemplos de Cognitivos** na seção **Próximas etapas**.

## <a name="next-steps"></a>Próximas etapas
* [U-SQL/Exemplos de Cognitivos](https://github.com/Azure-Samples?utf8=✓&q=usql%20cognitive)
* [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Usar funções da janela do U-SQL para trabalhos de análise do Azure Data Lake](data-lake-analytics-use-window-functions.md)
