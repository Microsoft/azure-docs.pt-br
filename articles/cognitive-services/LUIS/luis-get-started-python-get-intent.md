---
title: Obter a intenção, Python
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste início rápido, passe enunciados para um ponto de extremidade LUIS e obtenha intenção e entidades.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 559b934194fa9aa160f4e7c5fe65610063181223
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276104"
---
# <a name="quickstart-get-intent-using-python"></a>Início Rápido: Obter a intenção usando Python
Neste início rápido, passe enunciados para um ponto de extremidade LUIS e obtenha intenção e entidades.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6](https://www.python.org/downloads/) ou posterior.
* [Visual Studio Code](https://code.visualstudio.com/)

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o navegador

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent--programmatically"></a>Obter a intenção de forma programática

Você pode usar o Python para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior.

1. Copie um dos seguintes snippets de código em um arquivo chamado `quickstart-call-endpoint.py`:

   [!code-python[Console app code that calls a LUIS endpoint for Python 2.7](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/2.x/quickstart-call-endpoint-2-7.py)]

   [!code-python[Console app code that calls a LUIS endpoint for Python 3.6](~/samples-luis/documentation-samples/quickstarts/analyze-text/python/3.x/quickstart-call-endpoint-3-6.py)]

2. Substitua o valor do campo `Ocp-Apim-Subscription-Key` pela sua chave de ponto de extremidade do LUIS.

3. Instale as dependências com `pip install requests`.

4. Execute o script com `python ./quickstart-call-endpoint.py`. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos
Exclua o arquivo do Python. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-python-add-utterance.md)
