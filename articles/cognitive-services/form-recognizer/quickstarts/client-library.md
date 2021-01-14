---
title: 'Início Rápido: Biblioteca de clientes do Reconhecimento de Formulários ou API REST'
titleSuffix: Azure Cognitive Services
description: Use a biblioteca de clientes do Reconhecimento de Formulários ou a API REST para criar um aplicativo de processamento de formulários que extraia pares chave/valor e dados de tabela dos documentos personalizados.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: forms processing, automated data processing
ms.openlocfilehash: c915e7753487eba284d89dbb480f9848e90c1ef1
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132249"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>Início Rápido: Usar a biblioteca de clientes do Reconhecimento de Formulários ou a API REST

Introdução ao Reconhecimento de Formulários usando uma linguagem de sua escolha. O Reconhecimento de Formulários do Azure é um serviço cognitivo que permite criar software de processamento de dados automatizado usando a tecnologia de machine learning. Identifique e extraia texto, pares chave/valor, marcas de seleção, dados de tabela e mais dos documentos de formulário&mdash;o serviço gera dados estruturados que incluem as relações no arquivo original. Você pode usar o Reconhecimento de Formulários por meio da API REST ou do SDK. Siga essas etapas para instalar o pacote do SDK e testar o código de exemplo para tarefas básicas. 

Use o Reconhecimento de Formulários para:

* [Analisar Layout](#analyze-layout)
* [Analisar recibos](#analyze-receipts)
* [Analisar cartões de visita](#analyze-business-cards)
* [Analisar faturas](#analyze-invoices)
* [Treinar um modelo personalizado](#train-a-custom-model)
* [Analisar formulários com um modelo personalizado](#analyze-forms-with-a-custom-model)
* [Gerenciar seus modelos personalizados](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end
