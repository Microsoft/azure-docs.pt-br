---
title: 'Início Rápido: Reconhecer fala, Swift – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Swift no macOS usando o SDK de Fala
services: cognitive-services
author: cbasoglu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 06/28/2019
ms.author: cbasoglu
ms.openlocfilehash: d81384c4a3b4691f179dc2b11330b25fe7fb42bb
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561528"
---
# <a name="quickstart-recognize-speech-in-swift-on-macos-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Swift no macOS usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo macOS em Swift usando o SDK de Fala dos Serviços Cognitivos para transcrever uma fala de um microfone.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala.
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e [CocoaPods](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-ios"></a>Obter o SDK de Fala para iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.6.0`. Observe que este tutorial não funcionará sem alterações para qualquer versão anterior do SDK.

O SDK de Fala dos Serviços Cognitivos para macOS é distribuído como um quadro de estrutura.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixados de https://aka.ms/csspeech/macosbinary e vinculados manualmente. Este guia usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e iniciar um novo projeto clicando em **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo de seleção de modelo, escolha o modelo "Aplicativo de Exibição Única do iOS ".

Nas caixas de diálogo que seguem, faça as seguintes seleções:

1. Caixa de diálogo Opções de Projeto
    1. Insira um nome para o aplicativo de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e um identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, você pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Veja o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Verifique se Swift está escolhido como a linguagem do projeto.
    1. Desabilite as caixas de seleção para usar storyboards e criar um aplicativo baseado no documento. A interface de usuário simples para o aplicativo de exemplo será criada por meio de programação.
    1. Desabilite todas as caixas de seleção para testes e dados principais.
1. Selecionar diretório do projeto
    1. Escolha um diretório no qual colocar o projeto. Isso cria um diretório `helloworld` no diretório escolhido que contém todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.
1. O aplicativo também precisa declarar o uso do microfone no arquivo `Info.plist`. Clique no arquivo na visão geral e adicione a chave "Privacidade - Descrição de uso do microfone", com um valor, como "Microfone é necessário para o reconhecimento de fala".
    ![Configurações no Info.plist](media/sdk/qs-swift-ios-info-plist.png)
1. Fechar o projeto do Xcode. Você usará uma instância diferente dele depois de configurar o CocoaPods.

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Coloque um novo arquivo de cabeçalho com o nome `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` no diretório `helloworld` dentro do projeto helloworld e cole o seguinte código nele: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Adicione o caminho relativo `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` ao cabeçalho ponte das configurações de projeto do Swift para o destino helloworld no campo *Cabeçalho ponte de Objective-C* nas ![propriedades do cabeçalho](media/sdk/qs-swift-ios-bridging-header.png)
1. Substitua o conteúdo do arquivo `AppDelegate.swift` gerado automaticamente por: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Substitua o conteúdo do arquivo `ViewController.swift` gerado automaticamente por: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. Em `ViewController.swift`, substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o gerenciador de dependência do CocoaPod conforme descrito nas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório de seu aplicativo de exemplo (`helloworld`). Colocar um arquivo de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório: [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Navegue até o diretório `helloworld` em um terminal e execute o comando `pod install`. Isso gerará um espaço de trabalho do Xcode `helloworld.xcworkspace` que contém o aplicativo de exemplo e o SDK de fala como uma dependência. Este espaço de trabalho será usado nas seções a seguir.

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Abra o espaço de trabalho `helloworld.xcworkspace` no Xcode.
1. Torne a saída de depuração visível (**Exibição** > **Área de Depuração** > **Ativar Console**).
1. Escolha o simulador de iOS ou um dispositivo iOS conectado ao computador de desenvolvimento como o destino para o aplicativo da lista no menu **Produto** > **Destino**.
1. Compile e execute o exemplo de código no simulador do iOS selecionando **Produto** > **Executar** no menu ou clicando no botão **Reproduzir**.
1. Depois que você clicar no botão "Reconhecer" no aplicativo e diga algumas palavras, você deve ver o texto falado por você na parte inferior da tela.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar nossos exemplos no GitHub](https://aka.ms/csspeech/samples)

