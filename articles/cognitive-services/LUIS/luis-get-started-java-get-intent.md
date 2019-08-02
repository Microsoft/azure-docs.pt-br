---
title: Obter intenção, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: Neste início rápido em Java, use um aplicativo LUIS público disponível para determinar a intenção do usuário do texto de conversa.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: 42240c7b45029684e51c25419eab7f4378785a4d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276148"
---
# <a name="quickstart-get-intent-using-java"></a>Início Rápido: Reconhecer a intenção usando Java

Neste início rápido, passe enunciados para um ponto de extremidade LUIS e obtenha intenção e entidades.

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

<a name="create-luis-subscription-key"></a>

## <a name="prerequisites"></a>Pré-requisitos

* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Visual Studio Code](https://code.visualstudio.com/) ou seu IDE favorito
* ID do aplicativo público: df67dcdb-c37d-46af-88e1-8b97951ca1c2

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-luis-repo-note.md)]

## <a name="get-luis-key"></a>Obter chave LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-get-key-para.md)]

## <a name="get-intent-with-browser"></a>Obter a intenção com o navegador

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-browser-para.md)]

## <a name="get-intent-programmatically"></a>Obter a intenção de forma programática

Você pode usar o Java para acessar os mesmos resultados que você viu na janela do navegador na etapa anterior. Lembre-se de adicionar as bibliotecas do Apache ao projeto.

1. Copie o código a seguir para criar uma classe em um arquivo chamado `LuisGetRequest.java`:

   [!code-java[Console app code that calls a LUIS endpoint](~/samples-luis/documentation-samples/quickstarts/analyze-text/java/call-endpoint.java)]

2. Substitua o valor da variável `YOUR-KEY` pela sua chave do LUIS.

3. Substitua isso pelo caminho do arquivo e compile o programa Java em uma linha de comando: `javac -cp .;<FILE_PATH>\* LuisGetRequest.java`.

4. Substitua isso pelo caminho do arquivo e execute o aplicativo em uma linha de comando: `java -cp .;<FILE_PATH>\* LuisGetRequest.java`. Ele exibe o mesmo JSON que você viu anteriormente na janela do navegador.

    ![A janela de console exibe resultados JSON do LUIS](./media/luis-get-started-java-get-intent/console-turn-on.png)
    
## <a name="luis-keys"></a>Chaves de LUIS

[!INCLUDE [Use authoring key for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-key-usage-para.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Exclua a pasta de arquivo/projeto Java.

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Adicionar declarações](luis-get-started-java-add-utterance.md)
