---
title: Mineração de texto e análise com a API de Análise de Texto – Serviços Cognitivos do Azure
titleSuffix: Azure Cognitive Services
description: Saiba mais sobre a mineração de texto com a API de Análise de Texto. Use-a para análise de sentimentos, detecção de idioma e outras formas de Processamento de Idioma Natural.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 03/29/2021
ms.author: aahi
keywords: mineração de texto, análise de sentimentos, análise de texto
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: b586478b6b3943fb0154ed6c50bade6fd8b08b76
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219494"
---
# <a name="what-is-the-text-analytics-api"></a>O que é a API de Análise de Texto?

A API de Análise de Texto é um serviço baseado em nuvem que fornece recursos de NLP (Processamento de Idioma Natural) para mineração e análise de texto, incluindo análise de sentimento, mineração de opiniões, extração de frases-chave, detecção de idioma e reconhecimento de entidade nomeada.

A API faz parte dos [Serviços Cognitivos do Azure](../index.yml), uma coleção de algoritmos de IA e aprendizado de máquina na nuvem para seus projetos de desenvolvimento. Você pode usar esses recursos com a API REST [versão 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-V3-0/), a [versão prévia 3.1](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/) ou a [biblioteca de clientes](quickstarts/client-libraries-rest-api.md).

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Whats-New-in-Text-Analytics-Opinion-Mining-and-Async-API/player]

Esta documentação contém os seguintes tipos de artigos:
* Os [inícios rápidos](./quickstarts/client-libraries-rest-api.md) são instruções passo a passo que permitem fazer chamadas para o serviço e obter resultados em um período curto. 
* Os [guias de instruções](./how-tos/text-analytics-how-to-call-api.md) contêm instruções de uso do serviço de maneiras mais específicas ou personalizadas.
* Os [conceitos](text-analytics-user-scenarios.md) fornecem explicações detalhadas sobre a funcionalidade e os recursos do serviço.
* Os [tutoriais](./tutorials/tutorial-power-bi-key-phrases.md) são guias mais longos que mostram como usar o serviço como um componente de soluções de negócios mais amplas.

## <a name="sentiment-analysis"></a>Análise de sentimento

Use a [análise de sentimento](how-tos/text-analytics-how-to-sentiment-analysis.md) para descobrir o que as pessoas pensam da sua marca ou do seu tópico minerando o texto em busca de pistas sobre sentimentos positivos ou negativos. 

O recurso fornece rótulos de sentimentos (como "negativo", "neutro" e "positivo") com base na pontuação de confiança mais alta encontrada pelo serviço em um nível de frase e documento. Esse recurso também retorna pontuações de confiança entre 0 e 1 para cada documento e frase dentro dele para um sentimento positivo, neutro e negativo. Execute também o serviço no local [usando um contêiner](how-tos/text-analytics-how-to-install-containers.md).

Começando na versão prévia v3.1, a mineração de opiniões é um recurso da Análise de Sentimento. Também conhecida como Análise de Sentimento baseada em aspecto no NLP (processamento de idioma natural), esse recurso fornece informações mais granulares sobre as opiniões relacionadas a palavras (como os atributos de produtos ou serviços) no texto.

## <a name="key-phrase-extraction"></a>Extração de frases-chave

Use a [extração de frases-chave](how-tos/text-analytics-how-to-keyword-extraction.md) para identificar rapidamente os principais conceitos no texto. Por exemplo, no texto "A comida estava deliciosa e a equipe era maravilhosa", a Extração de Frases-chave retornará os principais pontos de discussão: "comida" e "equipe maravilhosa".

## <a name="language-detection"></a>Detecção de idioma

A detecção de idioma pode [detectar o idioma em que o texto de entrada foi escrito](how-tos/text-analytics-how-to-language-detection.md) e relatar um código de idioma único para cada documento enviado na solicitação em uma ampla variedade de idiomas, variantes, dialetos e alguns idiomas regionais/culturais. O código de idioma é emparelhado com uma pontuação de confiança.

## <a name="named-entity-recognition"></a>Reconhecimento de entidade nomeada

O NER (Reconhecimento de Entidade Nomeada) pode [Identificar e categorizar entidades](how-tos/text-analytics-how-to-entity-linking.md) no seu texto como pessoas, lugares, organizações, quantidades. Entidades conhecidas também são reconhecidas e vinculadas a mais informações na Web.

## <a name="deploy-on-premises-using-docker-containers"></a>Implantação local usando contêineres do Docker

[Use os contêineres da Análise de Texto](how-tos/text-analytics-how-to-install-containers.md) para implantar recursos de API no local. Esses contêineres do Docker permitem que você aproxime o serviço dos seus dados para fins de conformidade, segurança ou outras razões operacionais. A Análise de Texto oferece os seguintes contêineres:

* análise de sentimento
* Extração de frases-chave (versão prévia)
* Detecção de idioma (versão prévia)
* Análise de Texto para integridade (versão prévia)

## <a name="asynchronous-operations"></a>Operações assíncronas

O ponto de extremidade `/analyze` permite que você use os recursos selecionados da API de Análise de Texto [de maneira assíncrona](how-tos/text-analytics-how-to-call-api.md), como o NER e a Extração de Frases-chave.

## <a name="typical-workflow"></a>Fluxo de trabalho típico

O fluxo de trabalho é simples: você envia dados para análise e manipula as saídas no seu código. Os analisadores são consumidos como estão, sem nenhuma configuração ou personalização adicional.

1. [Crie um recurso do Azure](how-tos/text-analytics-how-to-call-api.md) para Análise de Texto. Posteriormente, [obtenha a chave](how-tos/text-analytics-how-to-call-api.md) gerada para autenticar suas solicitações.

2. [Formular uma solicitação](how-tos/text-analytics-how-to-call-api.md#json-schema) contendo seus dados como texto bruto não estruturado, em JSON.

3. Poste a solicitação no ponto de extremidade estabelecido durante a inscrição, acrescentando o recurso desejado: análise de sentimento, extração de frases-chave, detecção de idioma ou reconhecimento de entidade nomeada.

4. Transmita por streaming ou armazene a resposta localmente. Dependendo da solicitação, os resultados são uma pontuação de sentimento, uma coleção de frases-chave extraídas ou um código de idioma.

A saída é retornada como um único documento JSON, com resultados para cada documento de texto que você enviou, com base na ID. Posteriormente, você pode analisar, visualizar ou categorizar os resultados em insights acionáveis.

Os dados não são armazenados na sua conta. Operações executadas pela API de Análise de Texto não têm estado, o que significa que o texto que você fornece é processado e os resultados são retornados imediatamente.

## <a name="text-analytics-for-multiple-programming-experience-levels"></a>Análise de Texto para vários níveis de experiência com programação

Você pode começar a usar a API de Análise de Texto em seus processos mesmo que não tenha muita experiência com programação. Use estes tutoriais para saber como você pode usar a API para analisar texto de maneiras diferentes de acordo com seu nível de experiência. 

* Programação mínima necessária:
    * [Extrair informações no Excel usando a Análise de Texto e o Power Automate](tutorials/extract-excel-information.md)
    * [Use a API de Análise de Texto e o MS Flow para identificar o sentimento dos comentários em um grupo do Yammer](/Yammer/integrate-yammer-with-other-apps/sentiment-analysis-flow-azure?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Integre o Power BI com a API de Análise de Texto para analisar comentários dos clientes](tutorials/tutorial-power-bi-key-phrases.md)
* Experiência de programação recomendada:
    * [Análise de sentimento em dados de streaming usando o Azure Databricks](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)
    * [Crie um aplicativo Flask para traduzir texto, analisar o sentimento e sintetizar fala](../translator/tutorial-build-flask-app-translation-synthesis.md?bc=%2f%2fazure%2fbread%2ftoc.json&toc=%2f%2fazure%2fcognitive-services%2ftext-analytics%2ftoc.json)


<a name="supported-languages"></a>

## <a name="supported-languages"></a>Idiomas com suporte

Esta seção foi movida para um artigo separado para melhor capacidade de descoberta. Consulte [Idiomas compatíveis na API de Análise de Texto](./language-support.md) para este conteúdo.

<a name="data-limits"></a>

## <a name="data-limits"></a>Limites de dados

Todos os pontos de extremidade da API de Análise de Texto aceitam dados de texto brutos. Confira o artigo [Limites de dados](concepts/data-limits.md) para obter mais informações.

## <a name="unicode-encoding"></a>Codificação Unicode

A API de Análise de Texto usa codificação Unicode para representação de texto e cálculos de contagem de caracteres. As solicitações podem ser enviadas em UTF-8 e UTF-16 sem diferenças mensuráveis na contagem de caracteres. Pontos de código Unicode são usados como heurística para comprimento de caracteres e são considerados equivalentes para fins de limites de dados de análise de texto. Se você usar [`StringInfo.LengthInTextElements`](/dotnet/api/system.globalization.stringinfo.lengthintextelements) para obter a contagem de caracteres, você estará usando o mesmo método que usamos para medir o tamanho dos dados.

## <a name="next-steps"></a>Próximas etapas

+ [Crie um recurso do Azure](../cognitive-services-apis-create-account.md) para Análise de Texto e obtenha uma chave e um ponto de extremidade para seus aplicativos.

+ Use o [início rápido](quickstarts/client-libraries-rest-api.md) para começar a enviar chamadas à API. Saiba como enviar texto, escolher uma análise e exibir os resultados com o mínimo de código.

+ Veja [o que há de novo na API de Análise de Texto](whats-new.md) para obter informações sobre novas versões e recursos.

+ Se aprofunde um pouco mais com este [tutorial de análise de sentimento](/azure/databricks/scenarios/databricks-sentiment-analysis-cognitive-services) usando o Azure Databricks.

+ Confira nossa lista de postagens em blog e outros vídeos demonstrando como usar a API de Análise de Texto com outras ferramentas e tecnologias em nossa [página Conteúdo da Comunidade e Externo](text-analytics-resource-external-community.md).
