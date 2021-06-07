---
title: 'Tutorial: Criar um aplicativo iOS que usa uma foto e a inicia na Leitura Avançada (Swift)'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, você criará um aplicativo iOS do zero e adicionará a funcionalidade Imagem à Leitura Avançada.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: metang
ms.openlocfilehash: 23e9fd586882d25aed646b984e17c5885145ccff
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284627"
---
# <a name="tutorial-create-an-ios-app-that-launches-the-immersive-reader-with-content-from-a-photo-swift"></a>Tutorial: Criar um aplicativo iOS que inicia a Leitura Avançada com o conteúdo de uma foto (Swift)

A [Leitura Avançada](https://www.onenote.com/learningtools) é uma ferramenta projetada de forma inclusiva que implementa técnicas comprovadas para melhorar a compreensão da leitura.

A [API de Leitura de Serviços Cognitivos de Pesquisa Visual Computacional](../computer-vision/overview-ocr.md) detecta o conteúdo de texto em uma imagem usando os modelos de reconhecimento mais recentes da Microsoft e converte o texto identificado em um fluxo de caracteres legível por computador.

Neste tutorial, você criará um aplicativo iOS do zero e integrará a API de Leitura e a Leitura Avançada usando o SDK de Leitura Avançada. Um exemplo de funcionamento completo deste tutorial está disponível [aqui](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/ios).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
* Um recurso de Leitura Avançada configurado para autenticação do Azure Active Directory. Siga [estas instruções](./how-to-create-immersive-reader.md) para a configuração. Você precisará de alguns dos valores criados aqui ao configurar as propriedades do projeto de exemplo. Salve a saída da sessão em um arquivo de texto para referência futura.
* O uso deste exemplo requer uma assinatura do Azure para o Serviço Cognitivo de Pesquisa Visual Computacional. [Crie um recurso do Serviço Cognitivo de Pesquisa Visual Computacional no portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision).

## <a name="create-an-xcode-project"></a>Criar um projeto do Xcode

Criar um projeto no Xcode.

![Novo Projeto](./media/ios/xcode-create-project.png)

Escolha **Aplicativo de Modo de Exibição Único**.

![Novo Aplicativo de Modo de Exibição Único](./media/ios/xcode-single-view-app.png)

## <a name="get-the-sdk-cocoapod"></a>Obter o SDK CocoaPod
A maneira mais fácil de usar o SDK de Leitura Avançada é por meio do CocoaPods. Para instalar por meio do Cocoapods:
1. [Instalar CocoaPods](http://guides.cocoapods.org/using/getting-started.html) – Siga o guia de introdução para instalar o Cocoapods.
2. Crie um Podfile executando `pod init` no diretório raiz do projeto Xcode.
3.  Adicione o CocoaPod ao seu Podfile adicionando `pod 'immersive-reader-sdk', :path => 'https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS/immersive-reader-sdk'`. Seu Podfile deve ser semelhante ao seguinte, com o nome do destino substituindo picture-to-immersive-reader-swift:
 ```ruby
  platform :ios, '9.0'

  target 'picture-to-immersive-reader-swift' do
  use_frameworks!
  # Pods for picture-to-immersive-reader-swift
  pod 'immersive-reader-sdk', :git => 'https://github.com/microsoft/immersive-reader-sdk.git'
  end
```
4. No terminal, no diretório do seu projeto Xcode, execute o comando `pod install` para instalar o pod do SDK de Leitura Avançada.
5. Adicione `import immersive_reader_sdk` a todos os arquivos que precisam referenciar o SDK.
6. Abra o projeto abrindo o arquivo `.xcworkspace`, e não o arquivo `.xcodeproj`.

## <a name="acquire-an-azure-ad-authentication-token"></a>Adquirir um token de autenticação do Azure AD

Você precisará de alguns valores da etapa de pré-requisito de configuração da autenticação do Azure AD acima para esta parte. Veja novamente o arquivo de texto que você salvou dessa sessão.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Na pasta do projeto principal, que contém o arquivo ViewController.swift, crie um arquivo de classe Swift chamado Constants.swift. Substitua a classe pelo código a seguir, adicionando em seus valores quando aplicável. Mantenha esse arquivo como um arquivo local que só existe em seu computador e não faça commit desse arquivo no controle do código-fonte, pois ele contém segredos que não devem ser tornados públicos. É recomendável que você não mantenha segredos em seu aplicativo. Em vez disso, recomendamos usar um serviço de back-end para obter o token, em que os segredos podem ser mantidos fora do aplicativo e do dispositivo. O ponto de extremidade da API de back-end deve ser protegido por alguma forma de autenticação (por exemplo, [OAuth](https://oauth.net/2/)) para impedir que usuários não autorizados obtenham tokens para usar em seu serviço de Leitura Avançada e cobrança; esse trabalho está além do escopo deste tutorial.

## <a name="set-up-the-app-to-run-without-a-storyboard"></a>Configurar o aplicativo para ser executado sem um storyboard

Abra AppDelegate.swift e substitua o arquivo pelo código a seguir.

## <a name="add-functionality-for-taking-and-uploading-photos"></a>Adicionar funcionalidade para tirar e carregar fotos

Renomeie ViewController.swift para PictureLaunchViewController.swift e substitua o arquivo pelo código a seguir.

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

Defina o esquema de arquivo no Xcode selecionando um simulador ou um destino do dispositivo.
![Esquema de arquivo](./media/ios/xcode-archive-scheme.png)<br/>
![Selecionar destino](./media/ios/xcode-select-target.png)

No Xcode, pressione Ctrl + R ou clique no botão reproduzir para executar o projeto e o aplicativo deverá ser iniciado no simulador ou dispositivo especificado.

Em seu aplicativo, você deverá ver:

![Aplicativo de exemplo](./media/ios/picture-to-immersive-reader-ipad-app.png)

Dentro do aplicativo, tire ou carregue uma foto de texto pressionando o botão 'Tirar Foto' ou 'Escolher Foto da Biblioteca' e a Leitura Avançada será iniciada exibindo o texto da foto.

![Leitura Avançada](./media/ios/picture-to-immersive-reader-ipad.png)

## <a name="next-steps"></a>Próximas etapas

* Explorar a [Referência do SDK da Leitura Avançada](./reference.md)