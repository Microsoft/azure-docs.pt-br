---
title: Início Rápido – Adicionar o ingresso em uma reunião do Teams um aplicativo iOS usando os Serviços de Comunicação do Azure
description: Neste início rápido, você aprenderá a usar a biblioteca de Inserção do Teams dos Serviços de Comunicação do Azure para iOS.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803858"
---
Neste início rápido, você aprenderá a ingressar em uma reunião no Teams usando a biblioteca de Inserção do Teams dos Serviços de Comunicação do Azure para iOS.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Um Mac executando [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), juntamente com um certificado de desenvolvedor válido instalado em seu conjunto de chaves.
- Um recurso dos Serviços de Comunicação implantado. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um [Token de Acesso do Usuário](../../access-tokens.md) para o seu Serviço de Comunicação do Azure.
- [CocoaPods](https://cocoapods.org/) instalado para o ambiente de build.

## <a name="setting-up"></a>Configurando

### <a name="creating-the-xcode-project"></a>Como criar o projeto do Xcode

No Xcode, crie um projeto do iOS e selecione o modelo **Aplicativo**. Usaremos storyboards UIKit. Você não criará testes durante este início rápido. Fique à vontade para desmarcar **Incluir Testes**.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Captura de tela mostrando a seleção do modelo Novo Projeto no Xcode.":::

Dê ao projeto o nome de `TeamsEmbedGettingStarted`.

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Captura de tela mostrando os detalhes de Novo Projeto no Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instale o pacote e as dependências com o CocoaPods

1. Crie um Podfile para o aplicativo:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Execute `pod install`.
3. Abra o `.xcworkspace` gerado com o Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Solicitar acesso ao microfone, à câmera etc.

Para acessar o hardware do dispositivo, atualize a Lista de Propriedades de Informações do aplicativo. Defina o valor associado como um `string` que será incluído na caixa de diálogo que o sistema usa para solicitar acesso do usuário.

Clique com o botão direito do mouse na entrada `Info.plist` da árvore do projeto e selecione **Abrir Como** > **Código-Fonte**. Adicione as linhas a seguir no nível superior da seção `<dict>` e, em seguida, salve o arquivo.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Adicionar a estrutura de Inserção do Teams

1. Baixe a estrutura.
2. Crie uma pasta `Frameworks` na raiz do projeto. Ex.: `\TeamsEmbedGettingStarted\Frameworks\`
3. Copie as estruturas `TeamsAppSDK.framework` e `MeetingUIClient.framework` baixadas para essa pasta.
4. Adicione o `TeamsAppSDK.framework` e o `MeetingUIClient.framework` ao destino do projeto na guia geral. Use o `Add Other` -> `Add Files...` para navegar até os arquivos de estrutura e adicioná-los.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Captura de tela mostrando as estruturas adicionadas no Xcode.":::

5. Se ainda não estiver, adicione `$(PROJECT_DIR)/Frameworks` a `Framework Search Paths` na guia de configurações do build de destino do projeto. Para localizar a configuração, altere o filtro de `basic` para `all` ou use a barra de pesquisa à direita.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Captura de tela mostrando o caminho de pesquisa da estrutura no Xcode.":::

### <a name="turn-off-bitcode"></a>Desligar o BitCode

Defina a opção `Enable Bitcode` como `No` nas configurações de build do projeto. Para localizar a configuração, altere o filtro de `basic` para `all` ou use a barra de pesquisa à direita.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Captura de tela mostrando a opção do BitCode no Xcode.":::

### <a name="add-framework-signing-script"></a>Adicionar script de assinatura da estrutura

Selecione o destino do aplicativo e escolha a guia `Build Phases`. Depois, clique em `+` e em `New Run Script Phase`. Verifique se essa nova fase ocorre depois das fases `Embed Frameworks`.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Captura de tela mostrando a adição do script de build no Xcode.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Ligar o modo VoIP em segundo plano.

Selecione o destino do aplicativo e clique na guia Recursos.

Ative `Background Modes` clicando no `+ Capabilities` na parte superior e selecione `Background Modes`.

Marque a caixa de seleção correspondente a `Voice over IP`.

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Captura de tela mostrando o VoIP habilitado no Xcode.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Adicionar uma referência de janela a AppDelegate

Abra o arquivo **AppDelegate.swift** de seu projeto e adicione uma referência a 'window'.

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Adicionar um botão ao ViewController

Crie um botão no retorno de chamada `viewDidLoad` em **ViewController.swift**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Crie uma saída para o botão em **ViewController.swift**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Abra o arquivo **ViewController.swift** do seu projeto e adicione uma declaração `import` à parte superior do arquivo para importar o `AzureCommunication library` e o `MeetingUIClient`. 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Substitua a implementação da classe `ViewController` por um botão simples para permitir que o usuário ingresse em uma reunião. Anexaremos a lógica de negócios ao botão neste guia de início rápido.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Modelo de objeto

As seguintes classes e as interfaces administram alguns dos principais recursos da biblioteca de Inserção do Teams dos Serviços de Comunicação do Azure:

| Nome                                  | Descrição                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | O MeetingUIClient é o principal ponto de entrada na biblioteca de Inserção do Teams. |
| MeetingUIClientDelegate | O MeetingUIClientDelegate é usado para receber eventos, como alterações no estado da chamada. |
| MeetingJoinOptions | MeetingJoinOptions são usadas para opções configuráveis, como nome de exibição. | 
| CallState | O CallState é usado para relatar alterações no estado da chamada. As opções são as seguintes: connecting, waitingInLobby, connected e ended. |

## <a name="create-and-authenticate-the-client"></a>Criar e autenticar o cliente

Inicialize uma instância de `MeetingUIClient` com um Token de Acesso do Usuário que nos permitirá ingressar em reuniões. Adicione o seguinte código ao retorno de chamada `viewDidLoad` no **ViewController.swift**:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Substitua `<USER_ACCESS_TOKEN>` por um token de acesso de usuário válido para o seu recurso. Confira a documentação do [token de acesso do usuário](../../access-tokens.md) se você ainda não tiver um token disponível.

### <a name="setup-token-refreshing"></a>Configurar a atualização do token

Crie um método `fetchTokenAsync`. Em seguida, adicione sua lógica `fetchToken` para obter o token do usuário.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Substitua `<USER_ACCESS_TOKEN>` por um token de acesso de usuário válido para o seu recurso.

## <a name="join-a-meeting"></a>Ingressar em uma reunião

O método `joinMeeting` é definido como a ação que será executada ao tocar no botão *Ingressar na Reunião*. Atualize a implementação para ingressar em uma reunião com o `MeetingUIClient`:

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Substitua `<MEETING URL>` por um link de reunião do Teams.

### <a name="get-a-teams-meeting-link"></a>Obter o link de reunião do Teams

Um link de reunião do Teams pode ser recuperado usando APIs do Graph. Isso é detalhado na [documentação do Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
O SDK de Chamada de Serviços de Comunicação aceita um link de reunião completo do Teams. Esse link é retornado como parte do recurso `onlineMeeting`, acessível na propriedade [`joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) Você também pode obter as informações da reunião necessárias da URL **Ingressar na Reunião** no próprio convite da reunião do Teams.

## <a name="run-the-code"></a>Executar o código

Você pode criar e executar o seu aplicativo no simulador de iOS selecionando **Produto** > **Executar** ou usando o atalho de teclado (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Aparência final do aplicativo de início rápido":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Aparência final após ingressar na reunião":::

> [!NOTE]
> Na primeira vez que você ingressar em uma reunião, o sistema solicitará o acesso ao microfone. Em um aplicativo de produção, você deve usar a API `AVAudioSession` para [verificar o status de permissão](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e atualizar normalmente o comportamento do aplicativo quando a permissão não for concedida.

## <a name="add-localization-support-based-on-your-app"></a>Adicionar suporte à localização com base em seu aplicativo

O SDK do Microsoft Teams dá suporte a mais de 100 cadeias de caracteres e recursos. O pacote de estrutura contém idiomas de base e inglês. Os demais estão incluídos no arquivo `Localizations.zip` incluído com o pacote.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Adicionar localizações ao SDK com base no suporte de seu aplicativo

1. Determine os tipos de localização que têm suporte de seu aplicativo na lista Projeto do Xcode > Informações > Localizações do aplicativo
2. Descompacte o Localizations.zip incluído com o pacote
3. Copie as pastas de localização da pasta descompactada com base no suporte de seu aplicativo para a raiz de TeamsAppSDK.framework

## <a name="preparation-for-app-store-upload"></a>Preparação para upload da App Store

Remova as arquiteturas i386 e x86_64 das estruturas em caso de arquivamento.

Adicione as arquiteturas `i386` e `x86_64` removendo o script para as Fases de Build antes da fase de codesign da estrutura se você quiser arquivar o aplicativo.

No Navegador de Projetos, selecione seu projeto. No painel Rditor, vá para Fases de Build → clique no sinal de + → Criar Fase do Script de Execução.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Exemplo de código

É possível baixar o aplicativo de exemplo do [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started)
