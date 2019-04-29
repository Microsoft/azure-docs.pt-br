---
title: Recursos de documentação da pesquisa cognitiva – Azure Search
description: Uma lista com anotações de artigos, tutoriais, exemplos e postagens de blog relacionados às cargas de trabalho de pesquisa no Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 609b5d990cffce10733f6fc82e6b1032ad0f06bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60951779"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Recursos de documentação para cargas de trabalho de pesquisa cognitivas

A pesquisa cognitiva, agora em visualização pública, é uma nova camada de enriquecimento na indexação de pesquisa do Azure que localiza informações latentes em texto não fontes e texto não-diferenciado, transformando-os em conteúdo de pesquisa de texto completo no Azure Search.

Os artigos a seguir são a documentação completa de pesquisa cognitiva.

## <a name="getting-started"></a>Introdução
+ [O que é pesquisa cognitiva?](cognitive-search-concept-intro.md)
+ [Início Rápido: Experimentar a pesquisa cognitiva no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de pesquisa cognitiva](cognitive-search-tutorial-blob.md)
+ [Exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md)

## <a name="how-to-guidance"></a>Diretrizes
+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Como referenciar anotações em um conjunto de qualificações](cognitive-search-concept-annotations-syntax.md)
+ [Como mapear campos a um índice](cognitive-search-output-field-mapping.md)
+ [Como processar e extrair informações de imagens](cognitive-search-concept-image-scenarios.md)
+ [Como recompilar um índice do Azure Search](search-howto-reindex.md)
+ [Como definir uma interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Dicas de solução de problemas](cognitive-search-concept-troubleshooting.md)

## <a name="reference"></a>Referência

+ [Habilidades predefinidas](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ [API REST da visualização](search-api-2017-11-11-preview.md)
  + [Criar conjunto de qualificações (api-version=2017-11-11-Versão prévia)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Criar Indexador (api-version=2017-11-11-Versão prévia)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Consulte também

+ [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [O que é o Azure Search?](search-what-is-azure-search.md)
