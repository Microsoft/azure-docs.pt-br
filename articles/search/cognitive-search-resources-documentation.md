---
title: Recursos de documentação da pesquisa cognitiva – Azure Search
description: Uma lista com anotações de artigos, tutoriais, exemplos e postagens de blog relacionados às cargas de trabalho de pesquisa no Azure Search.
services: search
manager: cgronlun
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 3a23828d98b14c094aa751d3fb2e53575c5e6479
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347821"
---
# <a name="documentation-resources-for-cognitive-search-workloads"></a>Recursos de documentação para cargas de trabalho de pesquisa cognitivas

A pesquisa cognitiva, agora disponível para o público geral, é uma nova camada de enriquecimento na indexação de Azure Search que localiza informações latentes em fontes sem texto e texto não diferenciado, transformando-o em conteúdo pesquisável de texto completo no Azure Search.

Os artigos a seguir são a documentação completa de pesquisa cognitiva.

## <a name="getting-started"></a>Introdução
+ [O que é pesquisa cognitiva?](cognitive-search-concept-intro.md)
+ [Início Rápido: Experimentar a pesquisa cognitiva no portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Conheça as APIs de pesquisa cognitiva](cognitive-search-tutorial-blob.md)
+ [Exemplo: Como criar uma habilidade personalizada para a pesquisa cognitiva](cognitive-search-create-custom-skill-example.md)

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
  + [Microsoft. Skills. Text. KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft. Skills. Text. TranslationSkill (versão prévia)](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft. Skills. util. ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Habilidades personalizadas
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Habilidades preteridas](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

+ [API REST](https://docs.microsoft.com/rest/api/searchservice/)
  + [Criar Conjunto de Qualificações (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
  + [Criar Indexador (api-version=2019-05-06)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Consulte também

+ [API REST do Azure Search](https://docs.microsoft.com/rest/api/searchservice/)
+ [Indexadores no Azure Search](search-indexer-overview.md)
+ [O que é o Azure Search?](search-what-is-azure-search.md)
