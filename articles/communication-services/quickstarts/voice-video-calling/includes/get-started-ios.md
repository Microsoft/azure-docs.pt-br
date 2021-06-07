---
title: Guia de início rápido – Adicionar uma chamada a um aplicativo iOS usando os Serviços de Comunicação do Azure
description: Neste guia de início rápido, você aprenderá a usar o SDK de Chamada dos Serviços de Comunicação do Azure para iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 22c9d8f8bdf3e6195bf152fa0431ad5ce9bcdfeb
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "106073020"
---
Neste guia de início rápido, você aprenderá a iniciar uma chamada usando o SDK de Chamada dos Serviços de Comunicação do Azure para iOS.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Este documento usa a versão 1.0.0-beta.8 do SDK de Chamada.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um Mac executando [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), juntamente com um certificado de desenvolvedor válido instalado em seu conjunto de chaves.
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um [Token de Acesso do Usuário](../../access-tokens.md) para o seu Serviço de Comunicação do Azure.

## <a name="setting-up"></a>Configurando

### <a name="creating-the-xcode-project"></a>Como criar o projeto do Xcode

No Xcode, crie um projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único**. Este tutorial usa a [estrutura SwiftUI](https://developer.apple.com/xcode/swiftui/), portanto, você deve definir a **Linguagem** como **Swift** e a **Interface do Usuário** como **SwiftUI**. Você não criará testes durante este guia de início rápido. Fique à vontade para desmarcar **Incluir Testes**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Captura de tela mostrando a janela Novo Projeto no Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instale o pacote e as dependências com o CocoaPods

1. Para criar um Podfile para seu aplicativo, abra o terminal, navegue até a pasta do projeto e execute ```pod init```
3. Adicione o seguinte código ao Podfile e salve (verifique se "target" corresponde ao nome do seu projeto):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

3. Execute `pod install`.
3. Abra o `.xcworkspace` com o Xcode.

### <a name="request-access-to-the-microphone"></a>Solicitar acesso ao microfone

Para acessar o microfone do dispositivo, você precisa atualizar a Lista de Propriedades de Informações do aplicativo com um `NSMicrophoneUsageDescription`. Você define o valor associado como um `string` que será incluído na caixa de diálogo que o sistema usa para solicitar acesso do usuário.

Clique com o botão direito do mouse na entrada `Info.plist` da árvore do projeto e selecione **Abrir Como** > **Código-Fonte**. Adicione as linhas a seguir no nível superior da seção `<dict>` e, em seguida, salve o arquivo.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Abra o arquivo **ContentView.swift** do seu projeto e adicione uma declaração `import` à parte superior do arquivo para importar o `AzureCommunicationCalling library`. Além disso, importe `AVFoundation`. Precisaremos disso para a solicitação de permissão de áudio no código.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Substitua a implementação do struct `ContentView` por alguns controles de interface do usuário simples que permitem que um usuário inicie e termine uma chamada. Anexaremos a lógica de negócios a esses controles neste guia de início rápido.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e interfaces cuidam de alguns dos principais recursos do SDK de Chamada da Interface do Usuário dos Serviços de Comunicação do Azure:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | O CallClient é o ponto de entrada principal para o SDK de Chamada.|
| CallAgent | O CallAgent é usado para iniciar e gerenciar chamadas. |
| CommunicationTokenCredential | O CommunicationTokenCredential é usado como a credencial de token para criar uma instância do CallAgent.| 
| CommunicationUserIdentifier | O CommunicationUserIdentifier é usado para representar a identidade do usuário, que pode ser uma das seguintes: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Autenticar o cliente

Inicializar uma instância de `CallAgent` com um Token de Acesso de Usuário que nos permitirá fazer e receber chamadas. Adicione o seguinte código ao retorno de chamada `onAppear` no **ContentView.swift**:

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Você precisa substituir `<USER ACCESS TOKEN>` por um token de acesso de usuário válido para o seu recurso. Confira a documentação do [token de acesso do usuário](../../access-tokens.md) se você ainda não tiver um token disponível.

## <a name="start-a-call"></a>Iniciar uma chamada

O método `startCall` é definido como a ação que será executada ao tocar no botão *Iniciar Chamada*. Atualize a implementação para iniciar uma chamada com o `ASACallAgent`:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.call(participants: callees, options: StartCallOptions())
        }
    }
}
```

Você também pode usar as propriedades em `StartCallOptions` para definir as opções iniciais para a chamada (ou seja, é possível iniciar a chamada com o microfone no mudo).

## <a name="end-a-call"></a>Encerrar uma chamada

Implemente o método `endCall` para encerrar a chamada atual quando o botão *Encerrar Chamada* for tocado.

```swift
func endCall()
{    
    self.call!.hangup(HangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

Você pode criar e executar o seu aplicativo no simulador de iOS selecionando **Produto** > **Executar** ou usando o atalho de teclado (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Aparência final do aplicativo de início rápido":::

Você pode fazer uma chamada VOIP de saída fornecendo uma ID de usuário no campo de texto e tocando no botão **Iniciar Chamada**. Chamar `8:echo123` conecta você a um bot de eco. Isso é ótimo para começar e verificar se os dispositivos de áudio estão funcionando. 

> [!NOTE]
> Na primeira vez que você fizer uma chamada, o sistema solicitará o acesso ao microfone. Em um aplicativo de produção, você deve usar a API `AVAudioSession` para [verificar o status de permissão](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e atualizar normalmente o comportamento do aplicativo quando a permissão não for concedida.

## <a name="sample-code"></a>Exemplo de código

É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
