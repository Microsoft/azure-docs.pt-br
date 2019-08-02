---
title: 'Início Rápido: reconhecer API de Fala, Objective-C – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como reconhecer fala em Objective-C no macOS usando o SDK de Fala
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 25f341d167cecd765fd89d9286708d0bd8df6dd2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603032"
---
# <a name="quickstart-recognize-speech-in-objective-c-on-macos-using-the-speech-sdk"></a>Início Rápido: Reconhecer fala em Objective-C no macOS usando o SDK de Fala

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Neste artigo, você aprenderá a criar um aplicativo macOS em Objective-C usando o SDK de Fala dos Serviços Cognitivos para transcrever uma fala de um microfone.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, aqui está uma lista de pré-requisitos:

* Uma [chave de assinatura](get-started.md) para o Serviço de Fala
* Um computador macOS com [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) ou posterior e macOS 10.13 ou posterior

## <a name="get-the-speech-sdk-for-macos"></a>Obter o SDK de Fala para macOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

A versão atual do SDK de Fala dos Serviços Cognitivos é `1.6.0`.

O SDK de Fala dos Serviços Cognitivos para Mac é distribuído como um quadro de estrutura.
Ele pode ser usado em projetos do Xcode como um [CocoaPod](https://cocoapods.org/) ou baixados de https://aka.ms/csspeech/macosbinary e vinculados manualmente. Este guia usa um CocoaPod.

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Inicie o Xcode e iniciar um novo projeto clicando em **Arquivo** > **Novo** > **Projeto**.
Na caixa de diálogo de seleção de modelo, escolha o modelo "Aplicativo Cocoa".

Nas caixas de diálogo que seguem, faça as seguintes seleções:

1. Caixa de diálogo Opções de Projeto
    1. Insira um nome para o aplicativo de início rápido, por exemplo `helloworld`.
    1. Insira um nome de organização apropriado e um identificador de organização, se você já tiver uma conta de desenvolvedor da Apple. Para fins de teste, você pode escolher qualquer nome, como `testorg`. Para assinar o aplicativo, você precisa de um perfil de provisionamento adequado. Veja o [site do desenvolvedor da Apple](https://developer.apple.com/) para obter detalhes.
    1. Verifique se Objective-C é escolhido como o idioma para o projeto.
    1. Desabilite as caixas de seleção para usar storyboards e criar um aplicativo baseado no documento. A interface de usuário simples para o aplicativo de exemplo será criada por meio de programação.
    1. Desabilite todas as caixas de seleção para testes e dados principais.
    ![Configurações do projeto](media/sdk/qs-objectivec-macos-project-settings.png)
1. Selecionar diretório do projeto
    1. Escolha um diretório no qual colocar o projeto. Isso cria um diretório `helloworld` no diretório base que contém todos os arquivos para o projeto do Xcode.
    1. Desabilite a criação de um repositório Git para este projeto de exemplo.
1. Defina os direitos de acesso de rede e de microfone. Clique no nome do aplicativo na primeira linha na visão geral à esquerda para acessar a configuração do aplicativo e, em seguida, escolha a guia "Recursos".
    1. Habilite a configuração de "Área restrita de aplicativo" para o aplicativo.
    1. Habilite as caixas de seleção para acesso de "Conexões de saída" e "Microfone".
    ![Configurações de área restrita](media/sdk/qs-objectivec-macos-sandbox.png)
1. O aplicativo também precisa declarar o uso do microfone no arquivo `Info.plist`. Clique no arquivo na visão geral e adicione a chave "Privacidade - Descrição de uso do microfone", com um valor, como "Microfone é necessário para o reconhecimento de fala".
    ![Configurações no Info.plist](media/sdk/qs-objectivec-macos-info-plist.png)
1. Fechar o projeto do Xcode. Você usará uma instância diferente dele depois de configurar o CocoaPods.

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalar o SDK como um CocoaPod

1. Instale o gerenciador de dependência do CocoaPod conforme descrito nas [instruções de instalação](https://guides.cocoapods.org/using/getting-started.html).
1. Navegue até o diretório de seu aplicativo de exemplo (`helloworld`). Colocar um arquivo de texto com o nome `Podfile` e o seguinte conteúdo nesse diretório: [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/Podfile)]
1. Navegue até o diretório `helloworld` em um terminal e execute o comando `pod install`. Isso gerará um espaço de trabalho do Xcode `helloworld.xcworkspace` que contém o aplicativo de exemplo e o SDK de fala como uma dependência. Este espaço de trabalho será usado nas seções a seguir.

## <a name="add-the-sample-code"></a>Adicione o código de amostra

1. Abra o espaço de trabalho `helloworld.xcworkspace` no Xcode.
1. Substitua o conteúdo do arquivo `AppDelegate.m` gerado automaticamente por:  
   [!code-objectivec[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/objectivec-macos/helloworld/helloworld/AppDelegate.m#code)]
1. Substitua a cadeia de caracteres `YourSubscriptionKey` pela chave de assinatura.
1. Substitua a cadeia de caracteres `YourServiceRegion` pela [região](regions.md) associada à assinatura (por exemplo, `westus` para a assinatura de avaliação gratuita).

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra

1. Torne a saída de depuração visível (**Exibição** > **Área de Depuração** > **Ativar Console**).
1. Compile e execute o exemplo de código selecionando **Produto** > **Executar** no menu ou clicando no botão **Reproduzir**.
1. Depois que você clicar no botão e dizer algumas palavras, você verá o texto falado por você na parte inferior da tela. Ao executar o aplicativo pela primeira vez, você será solicitado a dar ao aplicativo acesso ao microfone do seu computador.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar amostras de Objective-C no GitHub](https://aka.ms/csspeech/samples)
