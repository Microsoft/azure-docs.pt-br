---
title: Exemplos de cenário do usuário para a API de Análise de Texto
titleSuffix: Azure Cognitive Services
description: Use este artigo para ver alguns cenários comuns para integrar a API de Análise de Texto a seus processos e serviços.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: a4c551a6e9b8d79fdeb7b5219f311c7c7c969757
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97505351"
---
# <a name="example-user-scenarios-for-the-text-analytics-api"></a>Exemplos de cenário do usuário para a API de Análise de Texto

A API de Análise de Texto é um serviço baseado em nuvem que fornece processamento de linguagem natural em torno de texto. Este artigo descreve alguns casos de uso de exemplo para integrar a API a seus processos e soluções de negócios. 

## <a name="analyze-survey-results"></a>Analisar resultados de pesquisa

Obtenha insights dos resultados de pesquisas de clientes e funcionários processando as respostas em texto bruto usando a Análise de Sentimento. Agregue as descobertas para análise, acompanhamento e promoção do engajamento.

![Uma imagem descrevendo como realizar a análise de sentimento em pesquisas de clientes e funcionários.](media/use-cases/survey-results.svg)

## <a name="analyze-recorded-inbound-customer-calls"></a>Analisar chamadas de clientes gravadas

Extraia informações de chamadas de serviços de clientes usando a conversão de fala em texto, Análise de Sentimento e Extração de Frases-chave. Exiba os resultados em um dashboard do Power BI ou um portal para compreender melhor os clientes, destacar tendências de atendimento ao cliente e aumentar o engajamento do cliente. Envie solicitações de API como um lote para emissão de relatórios ou em tempo real para intervenção. Consulte o código de exemplo [no GitHub](https://github.com/rlagh2/callcenteranalytics).

![Uma imagem descrevendo como automatizar a obtenção de insights de chamadas de serviço de atendimento ao cliente usando análise de sentimento](media/use-cases/azure-inbound.svg)

## <a name="process-and-categorize-support-incidents"></a>Processar e categorizar os incidentes de suporte

Use a Extração de Frases-chave e o Reconhecimento de Entidade para processar solicitações de suporte enviadas em formato de texto não estruturado. Use as frases e as entidades extraídas para categorizar as solicitações para planejamento de recursos e análise de tendências.

![Uma imagem descrevendo como usar o reconhecimento de entidade e a extração de frases-chave para categorizar relatórios de incidentes e tendências](media/use-cases/support-incidents.svg)

## <a name="monitor-your-products-social-media-feeds"></a>Monitorar os feeds de mídia social de seu produto

Monitore os comentários sobre o produto do usuário na página do Twitter ou do Facebook do seu produto. Use os dados para analisar o sentimento do cliente com relação a novos lançamentos de produtos, extraia frases-chave sobre recursos e solicitações de recursos ou responda a reclamações do cliente conforme elas ocorrerem. Confira o [modelo](https://flow.microsoft.com/galleries/public/templates/2680d2227d074c4d901e36c66e68f6f9/run-sentiment-analysis-on-tweets-and-push-results-to-a-power-bi-dataset/)exemplo de automação de energia da Microsoft.

![Uma imagem descrevendo como monitorar comentários sobre seu produto e sua empresa em redes sociais usando extração de frases-chave](media/use-cases/social-feed.svg)

## <a name="classify-and-redact-documents-that-have-sensitive-information"></a>Classificar e redigir documentos que têm informações confidenciais

Use o reconhecimento de entidade nomeada para identificar informações pessoais e confidenciais em documentos. Use os dados para classificar documentos ou redação-los para que eles possam ser compartilhados com segurança.

![Uma imagem que descreve como usar o NER para detectar informações pessoais e classificar e redigir documentos](media/use-cases/sensitive-docs.jpg)

## <a name="perform-opinion-mining"></a>Executar mineração de opinião

Agrupe opiniões relacionadas a aspectos específicos de um produto ou serviço em pesquisas, comentários de clientes ou onde quer que o texto tenha uma opinião sobre um aspecto. Use-o para ajudar a orientar os lançamentos e melhorias do produto, os esforços de marketing ou realçar o desempenho de seu produto ou serviço. 

> [!div class="mx-imgBorder"] 
> :::image type="content" source="media/use-cases/aspect-based-sentiment.png" alt-text="Opiniões de exemplo sobre um hotel.":::

## <a name="next-steps"></a>Próximas etapas

* [O que é a API de Análise de Texto?](overview.md)
* [Enviar uma solicitação à API de Análise de Texto usando a biblioteca de clientes](quickstarts/client-libraries-rest-api.md)
