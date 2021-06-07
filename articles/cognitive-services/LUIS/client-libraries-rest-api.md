---
title: 'Início Rápido: Bibliotecas de clientes do SDK do LUIS (Reconhecimento vocal) e API REST'
description: Crie e consulte um aplicativo do LUIS com as bibliotecas de cliente do SDK do LUIS e a API REST.
ms.topic: quickstart
ms.date: 03/29/2021
ms.service: cognitive-services
ms.author: aahi
manager: nitinme
ms.subservice: language-understanding
author: aahill
keywords: Azure, artificial intelligence, ai, natural language processing, nlp, LUIS, azure luis, natural language understanding, ai chatbot, chatbot maker,  understanding natural language
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-luis
ms.openlocfilehash: ca45266ce4b8ca784c3d54aafb80a66efaf2a1da
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278908"
---
# <a name="quickstart-language-understanding-luis-client-libraries-and-rest-api"></a>Início Rápido: Bibliotecas de clientes do LUIS (Reconhecimento vocal) e API REST

Crie e consulte um aplicativo de IA (inteligência artificial) do com as bibliotecas de clientes do SDK do Azure LUIS com este guia de início rápido usando C#, Python ou JavaScript. Você também pode usar a cURL para enviar solicitações usando a API REST.

O LUIS (Reconhecimento Vocal) permite aplicar o NLP (processamento de idioma natural) a um texto de idioma natural de conversa do usuário para prever o significado geral e extrair informações detalhadas relevantes.

* A biblioteca de clientes de **criação** e a API REST permitem a você criar, editar, treinar e publicar o aplicativo do LUIS.
* A biblioteca de clientes do **runtime de previsão** e a API REST permitem que você consulte o aplicativo publicado.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

::: zone pivot="rest-api"
[!INCLUDE [LUIS development with REST API](./includes/rest-api.md)]
::: zone-end

## <a name="clean-up-resources"></a>Limpar os recursos

Você pode excluir o aplicativo do [portal do LUIS](https://www.luis.ai) e excluir os recursos do Azure no [portal do Azure](https://portal.azure.com/).

Se você estiver usando a API REST, exclua o arquivo `ExampleUtterances.JSON` do sistema de arquivos quando terminar o início rápido.

## <a name="troubleshooting"></a>Solução de problemas

* Autenticação na biblioteca de clientes – erros de autenticação geralmente indicam que foram usados a chave e o ponto de extremidade incorretos. Para sua conveniência, este guia de início rápido usa a chave e o ponto de extremidade de criação para o runtime de previsão, mas só funcionará se você ainda não tiver usado a cota mensal. Se você não puder usar a chave e o ponto de extremidade de criação, precisará usar a chave e o ponto de extremidade do runtime de previsão ao acessar a biblioteca de cliente do SDK do runtime de previsão.
* Criação de entidades – se você receber um erro durante a criação da entidade de machine learning aninhada usada neste tutorial, verifique se você copiou o código e se não o alterou para criar uma entidade diferente.
* Criação de enunciados de exemplo – se você receber um erro durante a criação do enunciado de exemplo rotulado usado neste tutorial, verifique se você copiou o código e não o alterou para criar um exemplo rotulado diferente.
* Treinamento – se você recebe um erro de treinamento, isso geralmente indica um aplicativo vazio (sem intenções com os enunciados de exemplo) ou um aplicativo com tentativas ou entidades malformadas.
* Erros diversos – como o código faz chamadas às bibliotecas do cliente com objetos JSON e de texto, verifique se você não alterou o código.

Outros erros – se você receber um erro não abordado na lista anterior, informe-nos fornecendo comentários na parte inferior desta página. Inclua a linguagem de programação e a versão das bibliotecas de clientes instaladas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Desenvolvimento de aplicativo iterativo para LUIS](./luis-concept-app-iteration.md)