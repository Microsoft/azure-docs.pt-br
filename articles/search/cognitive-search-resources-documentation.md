---
title: Links de documentação para enriquecimento de IA
titleSuffix: Azure Cognitive Search
description: Uma lista com anotações de artigos, tutoriais, exemplos e postagens de blog relacionados às cargas de trabalho de enriquecimento de IA na Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88935340"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Recursos de documentação para enriquecimento de IA na Pesquisa Cognitiva do Azure

O enriquecimento de ia é um complemento à indexação baseada em indexador que localiza informações latentes em fontes sem texto e texto não diferenciado, transformando-o em conteúdo pesquisável de texto completo no Azure Pesquisa Cognitiva. 

Para processamento interno, os modelos de ia pré-treinados em serviços cognitivas são chamados internamente para executar as análises. Você também pode integrar modelos personalizados usando Azure Machine Learning, Azure Functions ou outras abordagens.

A seguir está uma lista consolidada da documentação para o enriquecimento do ia.

## <a name="concepts"></a>Conceitos

+ [Aprimoramentos de ia](cognitive-search-concept-intro.md)
+ [Conjuntos de habilidades](cognitive-search-working-with-skillsets.md)
+ [Sessões de depuração](cognitive-search-debug-session.md)
+ [Repositórios de conhecimento](knowledge-store-concept-intro.md)
+ [Projeções](knowledge-store-projection-overview.md)
+ [Enriquecimento incremental (reutilização de um documento aprimorado em cache)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>Passo a passos práticos

+ [Início rápido: criar um conconhecimento de cognitiva no portal do Azure](cognitive-search-quickstart-blob.md)
+ [Tutorial: indexação aprimorada com o ia](cognitive-search-tutorial-blob.md)
+ [Tutorial: diagnosticar, reparar e confirmar as alterações em seu confirmer com sessões de depuração](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>Repositórios de conhecimento

+ [Início rápido: criar uma loja de conhecimento no portal do Azure](knowledge-store-create-portal.md)
+ [Criar um repositório de conhecimento usando a REST e o Postman](knowledge-store-create-rest.md)
+ [Exibir um repositório de conhecimento com o Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)
+ [Conectar um repositório de conhecimento com o Power BI](knowledge-store-connect-power-bi.md)
+ [Exemplos de projeção (como formatar e exportar aprimoramentos)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>Habilidades personalizadas (avançadas)

+ [Como definir uma interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Exemplo: criar uma habilidade personalizada usando Azure Functions (e APIs de Pesquisa de Entidade do Bing)](cognitive-search-create-custom-skill-example.md)
+ [Exemplo: criar uma habilidade personalizada usando Python](cognitive-search-custom-skill-python.md)
+ [Exemplo: criar uma habilidade personalizada usando o reconhecedor de formulário](cognitive-search-custom-skill-form.md) 
+ [Exemplo: criar uma habilidade personalizada usando Azure Machine Learning](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>Diretrizes

+ [Anexar um recurso dos Serviços Cognitivos](cognitive-search-attach-cognitive-services.md)
+ [Definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [anotações de referência em um habilidades](cognitive-search-concept-annotations-syntax.md)
+ [Mapear campos para um índice](cognitive-search-output-field-mapping.md)
+ [Processar e extrair informações de imagens](cognitive-search-concept-image-scenarios.md)
+ [Configurar o Caching para enriquecimento incremental](search-howto-incremental-index.md)
+ [Como recompilar um índice da Pesquisa Cognitiva do Azure](search-howto-reindex.md)
+ [Dicas de design](cognitive-search-concept-troubleshooting.md)
+ [Erros e avisos comuns](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>Referência de habilidades

+ [Habilidades internas](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ Habilidades personalizadas
  + [Microsoft. Skills. Custom. AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [Habilidades preteridas](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>APIs

+ [REST API](/rest/api/searchservice/)
  + [Criar Qualificable (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Criar indexador (versão da API = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>Confira também

+ [API REST do Azure Cognitive Search](/rest/api/searchservice/)
+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [O que é o Azure Cognitive Search?](search-what-is-azure-search.md)