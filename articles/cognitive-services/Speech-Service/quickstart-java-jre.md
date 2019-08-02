---
title: 'Início Rápido: Reconhecimento de fala, Java (Windows, Linux) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você aprenderá a criar um aplicativo Java simples que captura e transcreve a fala do usuário por meio do microfone do computador.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: dce0a1b2adf20b2301402f37307e7ee1284c9aee
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605162"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-java"></a>Início Rápido: Reconhecimento de fala com o SDK de Fala para Java

Guias de início rápido também estão disponíveis para a [tradução de fala em fala](quickstart-translate-speech-java-jre.md) e o [assistente virtual que tem como prioridade o uso de voz](quickstart-virtual-assistant-java-jre.md).

Se desejar, escolha uma linguagem de programação e/ou ambiente diferente:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você criará um aplicativo de console Java usando o [SDK de Fala](speech-sdk.md). Você transcreve a fala em texto em tempo real por meio do microfone do computador. O aplicativo é criado com o pacote Maven do SDK de Fala e o Java IDE do Eclipse (v4.8) no Windows de 64 bits, no Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9) ou no macOS 10.13 ou posterior. Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operacional: Windows de 64 bits, Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9) ou macOS 10.13 ou posterior
* [Java IDE do Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).

Se você estiver executando o Linux, verifique se essas dependências estão instaladas antes de iniciar o Eclipse.

* No Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* No Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

Se você estiver executando o Windows (64 bits), verifique se instalou os Pacotes Redistribuíveis do Microsoft Visual C++ para a sua plataforma.
* [Baixar Pacotes Redistribuíveis do Microsoft Visual C++ para Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)

## <a name="create-and-configure-project"></a>Criar e configurar o projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Para adicionar uma nova classe vazia ao projeto Java, selecione **Arquivo** > **Novo** > **Classe**.

1. Na janela **Nova Classe Java**, insira **speechsdk.quickstart** no campo **Pacote** e **Principal** no campo **Nome**.

   ![Captura de tela da janela Nova Classe Java](media/sdk/qs-java-jre-06-create-main-java.png)

1. Substitua todo o código em `Main.java` pelo snippet de código a seguir:

   [!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java-jre/src/speechsdk/quickstart/Main.java#code)]

1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.

1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

1. Salve as alterações no projeto.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Pressione F11 ou selecione **Executar** > **Depurar**.
Os próximos 15 segundos de entrada de fala do microfone serão reconhecidos e registrados na janela do console.

![Captura de tela da saída do console após o reconhecimento com êxito](media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Próximas etapas

Amostras adicionais, por exemplo, como ler a fala de um arquivo de áudio, estão disponíveis no GitHub.

> [!div class="nextstepaction"]
> [Explorar amostras de Java no GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Consulte também

- [Início Rápido: Tradução de fala, Java (Windows, Linux)](quickstart-translate-speech-java-jre.md)
- [Personalizar modelos acústicos](how-to-customize-acoustic-models.md)
- [Personalizar modelos de linguagem](how-to-customize-language-model.md)
