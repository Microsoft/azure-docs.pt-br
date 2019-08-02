---
title: 'Início Rápido: Reconhecer fala, C++ (macOS) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em C++ no macOS usando o SDK de Fala
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: de170955ced45040541565800fc9853edea322f5
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603076"
---
# <a name="quickstart-recognize-speech-in-c-on-macos-by-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em C++ no macOS usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você criará um aplicativo de console C++ para o macOS 10.13 ou posterior. Você usará o [SDK de Fala](speech-sdk.md) dos Serviços Cognitivos para transcrever a conversão de fala em texto em tempo real do microfone do seu Mac. O aplicativo é compilado com o [SDK de Fala para macOS](https://aka.ms/csspeech/macosbinary) e o compilador C++ padrão do Mac (por exemplo, `g++`).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de uma chave de assinatura dos Serviços de Fala para concluir este Início Rápido. Obtenha uma gratuitamente. Confira [Experimentar os Serviços de Fala gratuitamente](get-started.md) para obter mais detalhes.

## <a name="install-speech-sdk"></a>Instalar SDK de Fala

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.6.0`.

O SDK de Fala para o macOS pode ser baixado como um pacote compactado da estrutura de https://aka.ms/csspeech/macosbinary.

Baixe e instale o SDK conforme a seguir:

1. Escolha um diretório para o qual os arquivos do SDK de Fala serão extraídos e defina a variável de ambiente `SPEECHSDK_ROOT` para apontar para esse diretório. Essa variável facilita fazer referência a esse diretório em futuros comandos. Por exemplo, se você quiser usar o diretório `speechsdk` no seu diretório base, use um comando semelhante ao seguinte:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Crie o diretório se ainda não existir.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Baixe e extraia os arquivos `.zip` que contêm a estrutura do SDK de Fala:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Valide o conteúdo do diretório de nível superior do pacote extraído:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   A listagem de diretório deve conter o aviso de terceiros e os arquivos de licença, bem como um diretório `MicrosoftCognitiveServicesSpeech.framework`.

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Crie um arquivo de origem C++ chamado `helloworld.cpp` e cole o código a seguir nele.

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-macos/helloworld.cpp#code)]

1. Nesse novo arquivo, substitua a cadeia de caracteres `YourSubscriptionKey` pela sua chave de assinatura dos Serviços de Fala.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="build-the-app"></a>Compilar o aplicativo

> [!NOTE]
> Insira os comandos abaixo como uma _única linha de comando_. A maneira mais fácil de fazer isso é copiar o comando usando o **Copiar** ao lado de cada comando e, em seguida, colá-lo no prompt de shell.

* Execute o comando a seguir para compilar o aplicativo.

  ```sh
  g++ helloworld.cpp -o helloworld --std=c++14 -F${SPEECHSDK_ROOT} -framework MicrosoftCognitiveServicesSpeech
  ```

## <a name="run-the-app"></a>Execute o aplicativo

1. Defina o caminho da biblioteca do carregador para apontar para a biblioteca do SDK de Fala.

    ```sh
    export DYLD_FRAMEWORK_PATH="$DYLD_FRAMEWORK_PATH:$SPEECHSDK_ROOT"
    ```

1. Execute o aplicativo.

   ```sh
   ./helloworld
   ```

1. Será exibido um prompt na janela do console solicitando que você diga algo. Fale uma frase ou expressão em inglês. Sua fala será transmitida para os Serviços de Fala e transcrita para texto, que aparecerá na mesma janela.

   ```text
   Say something...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de C++ no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
