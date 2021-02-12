---
title: Perguntas frequentes sobre a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados ao API de Análise de Texto para serviços cognitivas do Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/05/2021
ms.author: aahi
ms.openlocfilehash: 9a4e179767cc38169cd794f4cd629604bdcdaab0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955035"
---
# <a name="frequently-asked-questions-faq-about-the-text-analytics-api"></a>Perguntas frequentes sobre a API de Análise de Texto

 Encontre respostas para perguntas frequentes sobre conceitos, código e cenários relacionados ao API de Análise de Texto nos serviços cognitivas do Azure.

## <a name="can-text-analytics-identify-sarcasm"></a>Análise de Texto consegue identificar sarcasmo?

A análise é para sentimento positivo-negativo, e não para detecção de humor.

Sempre há um certo grau de imprecisão na análise de sentimento, mas o modelo é mais útil quando não há nenhum significado oculto nem subtexto no conteúdo. Ironia, sarcasmo, piadas e conteúdo com nuances similares dependem do contexto e das normas culturais para transmitir a intenção. Esse tipo de conteúdo está entre os mais desafiadores de analisar. Normalmente, a maior discrepância entre uma dada pontuação produzida por um analisador e uma avaliação subjetiva feita uma pessoa está em conteúdo com nuances de significado.

## <a name="can-i-add-my-own-training-data-or-models"></a>Adicionar meus próprios modelos ou dados de treinamento?

Não, os modelos são pré-treinados. As únicas operações disponíveis nos dados de upload são pontuação, extração de frases-chave e detecção de idioma. Não hospedamos modelos personalizados. Se você quiser criar e hospedar modelos de aprendizado de máquina personalizados, considere as [funcionalidades de aprendizado de máquina no Microsoft R Server](/r-server/r/concept-what-is-the-microsoftml-package).

## <a name="can-i-request-additional-languages"></a>Posso solicitar idiomas adicionais?

Análise de sentimento e extração de frases-chave estão disponíveis para um [número selecionado de idiomas](./language-support.md). Processamento de idioma natural é complexo e exige teste substancial antes de nova funcionalidade pode ser lançada. Por esse motivo, evitamos anunciar suporte para que ninguém conte com uma funcionalidade que precisa de mais tempo para amadurecer. 

Para nos ajudar a priorizar em quais idiomas trabalhar a seguir, vote em idiomas específicos na [Voz do Usuário](https://cognitive.uservoice.com/forums/555922-text-analytics). 

## <a name="why-does-key-phrase-extraction-return-some-words-but-not-others"></a>Por que a extração de frases-chave retorna algumas palavras, mas não outras?

A extração de frases-chave elimina palavras não essenciais e adjetivos independentes. Combinações de adjetivo-substantivo, como "vistas espetaculares" ou "clima enevoado" são retornadas em conjunto.

Em geral, saída consiste nos substantivos e nos objetos da frase. A saída é listada em ordem de importância, sendo a primeira frase a mais importante. A importância é medida pelo número de vezes que um determinado conceito é mencionado, ou pela relação do elemento com outros elementos do texto.

## <a name="why-does-output-vary-given-identical-inputs"></a>Por que saída varia com entradas idênticas?

Melhorias aos modelos e algoritmos serão anunciadas se a alteração for importante ou silenciosamente integradas ao serviço se a atualização for secundária. Ao longo do tempo, você poderá descobrir que a mesma entrada de texto resulta em uma pontuação de sentimento ou saída de frases-chave diferente. Essa é uma consequência normal e intencional do uso de recursos de aprendizado de máquina gerenciados na nuvem.

## <a name="service-availability-and-redundancy"></a>Disponibilidade e redundância do serviço

### <a name="is-text-analytics-service-zone-resilient"></a>O Análise de Texto zona de serviço é resiliente?

Sim. Por padrão, o serviço de Análise de Texto é resiliente por zona.

### <a name="how-do-i-configure-the-text-analytics-service-to-be-zone-resilient"></a>Como fazer configurar o serviço de Análise de Texto para ser resistente a zona?

Nenhuma configuração de cliente é necessária para habilitar a resiliência de zona. A resiliência de zona para recursos de Análise de Texto está disponível por padrão e gerenciada pelo próprio serviço.

## <a name="next-steps"></a>Próximas etapas

A sua pergunta é sobre a falta de um recurso ou funcionalidade? Considere solicitar ou votar nisso em nosso [site UserVoice](https://cognitive.uservoice.com/forums/555922-text-analytics).

## <a name="see-also"></a>Veja também

 * [StackOverflow: API de Análise de Texto](https://stackoverflow.com/questions/tagged/text-analytics-api)   
 * [StackOverflow: Serviços Cognitivos](https://stackoverflow.com/questions/tagged/microsoft-cognitive)
