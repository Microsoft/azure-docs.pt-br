---
title: 'Início Rápido: reconhecer API de Fala, Objective-C – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Objective-C no iOS usando o SDK de Fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 55b442b74847ccbc0dcc944eada7b33ccd368bed
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605053"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-ios-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Objective-C no iOS usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo iOS em Objective-C usando o SDK de Fala dos Serviços Cognitivos para transcrever uma fala em texto do microfone ou de um arquivo com o áudio gravado.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior
* O destino definido para o iOS versão 9.3 ou posterior

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Fala para iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.6.0`.

Atualmente, o SDK de Fala dos Serviços Cognitivos para iOS é distribuído como um Cocoa Framework.
Ele pode ser baixado [aqui](https://aka.ms/csspeech/iosbinary). Baixe o arquivo para seu diretório inicial.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e iniciar um novo projeto clicando em **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo de seleção de modelo, escolha o modelo "Aplicativo de Exibição Única do iOS ".

Nas caixas de diálogo que seguem, faça as seguintes seleções:

1. Caixa de diálogo Opções de Projeto
    1. Insira um nome para o aplicativo de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e o identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, você pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Veja o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Verifique se Objective-C é escolhido como o idioma para o projeto.
    1. Desabilite todas as caixas de seleção para testes e dados principais.
    ![Configurações do projeto](media/sdk/qs-objectivec-project-settings.png)
1. Selecionar diretório do projeto
    1. Escolha seu diretório base no qual colocar o projeto. Isso cria um diretório `helloworld` no diretório base que contém todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.
    1. Ajuste os caminhos para o SDK em *Configurações do projeto*.
        1. Na guia **Geral** no cabeçalho **Binários Inseridos**, adicione a biblioteca do SDK como uma estrutura: **Adicionar binários inseridos** > **Adicionar outro…** > Navegue até seu diretório inicial e escolha o arquivo `MicrosoftCognitiveServicesSpeech.framework`. Isso adiciona a biblioteca do SDK ao cabeçalho **Estrutura e Bibliotecas Vinculadas** automaticamente.
        ![Estrutura Adicionada](media/sdk/qs-objectivec-framework.png)
        1. Vá para a guia **Configurações de Build** e ative **Todas** as configurações.
        1. Adicione o diretório `$(SRCROOT)/..` a *Caminhos de Pesquisa do Framework* no cabeçalho **Caminhos de Pesquisa**.
        ![Configuração de Caminho de Pesquisa de Estrutura](media/sdk/qs-objectivec-framework-search-paths.png)

## <a name="set-up-the-ui"></a>Configurar a interface do usuário

O aplicativo de exemplo terá uma interface do usuário muito simples: Dois botões para iniciar o reconhecimento de fala da entrada do microfone ou do arquivo e um rótulo de texto para exibir o resultado.
A interface do usuário é configurada no `Main.storyboard` como parte do projeto.
Abra a exibição XML do storyboard clicando com o botão direito do mouse na entrada `Main.storyboard` na árvore do projeto e selecionando **Abrir como…**  > **Código-Fonte**.
Substitua o XML gerado automaticamente por este código:

[!code-xml[](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/Base.lproj/Main.storyboard)]

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Baixe o [arquivo wav de amostra](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-speech-sdk/f9807b1079f3a85f07cbb6d762c6b5449d536027/samples/cpp/windows/console/samples/whatstheweatherlike.wav) clicando com o botão direito do mouse e escolhendo **Salvar destino como…** . Adicione o arquivo wav ao projeto como um recurso arrastando-o de uma janela do Finder para o nível raiz da exibição do Projeto.
   Clique em **Concluir** na seguinte caixa de diálogo sem alterar as configurações.
1. Substitua o conteúdo do arquivo `ViewController.m` gerado automaticamente por:

   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-ios/helloworld/helloworld/ViewController.m#code)]
1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).
1. Adicione a solicitação de acesso do microfone. Clique com o botão direito do mouse na entrada `Info.plist` da árvore do projeto e selecione **Abrir como...**  > **Código-fonte**. Adicione as seguintes linhas à seção `<dict>` e, em seguida, salve o arquivo.
    ```xml
    <key>NSMicrophoneUsageDescription</key>
    <string>Need microphone access for speech recognition from microphone.</string>
    ```

## <a name="building-and-running-the-sample"></a>Criação e execução da amostra

1. Torne a saída de depuração visível (**Exibição** > **Área de Depuração** > **Ativar Console**).
1. Escolha o simulador de iOS ou um dispositivo iOS conectado ao computador de desenvolvimento como o destino para o aplicativo da lista no menu **Produto** > **Destino**.
1. Compile e execute o exemplo de código no simulador do iOS selecionando **Produto** > **Executar** no menu ou clicando no botão **Reproduzir**.
1. Depois de clicar no botão "Reconhecer (arquivo)" no aplicativo, você deve ver o conteúdo do arquivo de áudio de "Como está o clima?” na parte inferior da tela.

   ![Aplicativo do iOS simulado](media/sdk/qs-objectivec-simulated-app.png)

1. Depois que você clicar no botão "Reconhecer (microfone)" no aplicativo e diga algumas palavras, você deve ver o texto falado por você na parte inferior da tela.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Objective-C no GitHub](https://aka.ms/csspeech/samples)
