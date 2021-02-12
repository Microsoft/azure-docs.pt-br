---
title: IA (Inteligência Artificial)
description: O LUIS usa inteligência artificial (ia) para fornecer reconhecimento de linguagem aos seus dados, com base no esquema que você definiu.
ms.topic: conceptual
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 08/07/2020
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 39f73dd002091451ae832516d525499eae98564d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021364"
---
# <a name="artificial-intelligence-in-language-understanding-luis"></a>Inteligência artificial em Reconhecimento vocal (LUIS)

O LUIS usa a ia (inteligência artificial) para fornecer NLU (reconhecimento de linguagem natural) para seus dados, com base no esquema que você definiu.

## <a name="natural-language-processing-nlp"></a>NLP (processamento de idioma natural)

A Reconhecimento vocal natural (NLU) é um subtópico específico do NLP (processamento de idioma natural).

O processamento de linguagem natural é um conceito mais amplo que manipula qualquer forma de processamento de dados textuais, incluindo:

* Tokens
* Parte da marcação de fala (PDV)
* Segmentação
* Análise de morfológicas
* Similaridade semântica
* Discurso
* Tradução

## <a name="natural-language-processing-in-luis"></a>Processamento de idioma natural no LUIS

O processamento de idioma natural está disponível para seu aplicativo LUIS das seguintes maneiras:
* Luis ( [reconhecimento de linguagem natural](#natural-language-processing-nlp) )
* Aspectos NLP configuráveis no LUIS:
    * [Geração de tokens](luis-language-support.md#tokenization)
    * Morphology por meio de diacríticos, pontuação e configurações de [API](luis-reference-application-settings.md) de formulários do Word
* Pré ou pós-processamento do expressão de consulta fornecido por outros [Serviços cognitivas](../what-are-cognitive-services.md) , como:
    * [Tradução](../translator/translator-info-overview.md)

## <a name="natural-language-understanding-nlu"></a>NLU (Reconhecimento de idioma natural)

O NLU é a capacidade de _transformar_ uma instrução linguística em uma representação que lhe permita entender naturalmente seus usuários. A compreensão do idioma natural continua sendo um problema muito desafiador e é definida como um problema do _ia-Hard_ .

LUIS se destina a se concentrar na intenção e na extração, isso inclui a capacidade de identificar:
* O que o usuário deseja
* Sobre o que eles estão falando.

O LUIS tem pouco ou nenhum conhecimento dos aspectos mais amplos do _NLP_ , como similaridade semântica, sem identificação explícita em exemplos. Por exemplo, os seguintes tokens (palavras) são três coisas diferentes até serem usadas em contextos semelhantes nos exemplos fornecidos:

* comprar
* compras
* tenha

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [ciclo de vida de desenvolvimento](luis-concept-app-iteration.md) de um aplicativo Luis