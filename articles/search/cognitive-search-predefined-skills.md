---
title: Extração de dados internos, idioma natural, processamento de imagem - Azure Search
description: Extração de dados, idioma natural, habilidades cognitivas de processamento de imagem adicionar semântica e a estrutura para conteúdo bruto em um pipeline do Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: eddbb41f346661e86c64b1dd703321f434c5bbb6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61342597"
---
# <a name="predefined-skills-for-content-enrichment-azure-search"></a>Habilidades predefinidas para enriquecimento de conteúdo (Azure Search)

Neste artigo, você aprenderá sobre as habilidades cognitivas fornecidas com o Azure Search. Uma *habilidade cognitiva* é uma operação que transforma o conteúdo de alguma forma. Geralmente, é um componente que extrai ou infere a estrutura e, portanto, aumenta o seu entendimento sobre os dados de entrada. Quase sempre, a saída é baseada em texto. Um *conjunto de qualificações* é o conjunto de habilidades que define o enriquecimento do pipeline. 

> [!NOTE]
> A partir de 21 de dezembro de 2018, será possível associar um recurso de Serviços Cognitivos a um conjunto de habilidades do Azure Search. Isso nos permitirá começar a cobrar pela execução do conjunto de habilidades. Nessa data, também passaremos a cobrar pela extração de imagem como parte do estágio de decodificação de documentos. A extração de texto de documentos continuará sendo oferecida sem custo adicional.
>
> A execução do conjunto de qualificações será cobrada com o [Preço pago conforme o uso dos Serviços Cognitivos](https://azure.microsoft.com/pricing/details/cognitive-services/) existente. O preço da extração de imagem será cobrado com o preço da versão prévia, o que está descrito na [página de preço do Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400). Saiba [mais](cognitive-search-attach-cognitive-services.md).

## <a name="predefined-skills"></a>Habilidades predefinidas

Diversas habilidades são flexíveis na forma que consomem ou produzem. Em geral, a maioria das habilidades é baseada em modelos previamente treinados, o que significa que você não pode treinar o modelo usando seus próprios dados de treinamento. A tabela a seguir enumera e descreve as habilidades fornecidas pela Microsoft. 

| Habilidade | DESCRIÇÃO |
|-------|-------------|
| [Microsoft.Skills.Text.KeyPhraseSkill](cognitive-search-skill-keyphrases.md) | Essa habilidade usa um modelo pré-treinado para detectar frases importantes com base no posicionamento de termos, regras linguísticas, proximidade com outros termos e o quanto o termo é incomum nos dados de origem. |
| [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)  | Essa habilidade usa um modelo pré-treinado para detectar o idioma usado (uma ID de idioma por documento). Quando vários idiomas são usados dentro do mesmo segmentos de texto, a saída é o LCID do idioma predominantemente usado.|
| [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md) | Consolida o texto de uma coleção de campos em um único campo.  |
| [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md) | Essa habilidade usa um modelo pré-treinado para estabelecer entidades para um conjunto fixo de categorias: pessoas, local, organização, emails, URLs, campos datetime. |
| [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)  | Essa habilidade usa um modelo pré-treinado para classificar um sentimento positivo ou negativo em um registro por uma base de registro. O valor está entre  0 e 1. Pontuações neutras ocorrem para caso nulo quando o sentimento não puder ser detectado, e para o texto que é considerado neutro.  |
| [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md) | Divide o texto em páginas de forma que você possa enriquecer ou aumentar o conteúdo incrementalmente. |
| [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md) | Essa habilidade usa um algoritmo de detecção de imagem para identificar o conteúdo de uma imagem e gerar uma descrição de texto. |
| [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md) | Reconhecimento de caractere óptico. |
| [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md) | Saída de mapas para um tipo complexo (um tipo de dados de multi-parte que deve ser usado para um nome completo, um endereço de várias linhas ou uma combinação do sobrenome e um identificador pessoal) |
| [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md) | Permite a extensibilidade do pipeline de pesquisa cognitiva ao fazer uma chamada HTTP em uma API personalizada da Web |


Para obter diretrizes sobre como criar uma [habilidade personalizada](cognitive-search-custom-skill-web-api.md), consulte [Como definir uma interface personalizada](cognitive-search-custom-skill-interface.md) e [Exemplo: criar uma habilidade personalizada](cognitive-search-create-custom-skill-example.md).

## <a name="see-also"></a>Consulte também

+ [Como definir um conjunto de qualificações](cognitive-search-defining-skillset.md)
+ [Definição da interface de habilidades personalizadas](cognitive-search-custom-skill-interface.md)
+ [Tutorial: Indexação aprimorada com pesquisa cognitiva](cognitive-search-tutorial-blob.md)
