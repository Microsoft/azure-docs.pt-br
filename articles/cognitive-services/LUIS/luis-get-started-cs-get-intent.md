---
title: Obter a intenção, C#
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste início rápido de C#, use um aplicativo LUIS público disponível para determinar a intenção do usuário do texto de conversa.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 73dba63c464ee31dd24fe0fdf97e948a5b6004fc
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276262"
---
# <a name="quickstart-get-intent-using-c"></a>Início Rápido: Obter a intenção usando C#

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* [Edição do Visual Studio Community 2017](https://visualstudio.microsoft.com/vs/community/)
* Linguagem de programação C# (incluída com o VS Community 2017)
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2


[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o navegador

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção de forma programática

Use C# para consultar a [API](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78) GET do ponto de extremidade de previsão para obter os mesmos resultados vistos na janela do navegador na seção anterior. 

1. Crie um novo aplicativo de console no Visual Studio. 

    ![Crie um novo aplicativo de console no Visual Studio](media/luis-get-started-cs-get-intent/visual-studio-console-app.png)

2. No projeto do Visual Studio, no Gerenciador de Soluções, selecione **Adicionar referência** e, em seguida, selecione **System.Web** na guia de Assemblies.

    ![Selecione Adicionar referência e, em seguida, selecione System.Web na guia Assemblies](media/luis-get-started-cs-get-intent/add-system-dot-web-to-project.png)

3. Substitua Program.cs pelo código a seguir:
    
   [!code-csharp[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/csharp/Program.cs)]

4. Substitua o valor de `YOUR_KEY` pela sua chave do LUIS.

5. Crie e execute o aplicativo de console. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

    ![A janela de console exibe resultados JSON do LUIS](./media/luis-get-started-cs-get-intent/console-turn-on.png)

## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Quando tiver terminado com este início rápido, feche o projeto do Visual Studio e remova o diretório do projeto do sistema de arquivos. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar enunciados e treinar com C#](luis-get-started-cs-add-utterance.md)
