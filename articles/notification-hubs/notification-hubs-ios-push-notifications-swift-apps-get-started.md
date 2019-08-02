---
title: Notificações por push para aplicativos iOS Swift que usam Hubs de notificação do Azure | Microsoft Docs
description: Saiba como notificações por push para aplicativos iOS Swift que usam Hubs de notificação do Azure.
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: c35044918876b2c7710e26f6b868bc1096c2f538
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340398"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>Tutorial: Notificações por push para aplicativos iOS Swift que usam a API de REST de Hubs de notificação

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

Neste tutorial, você usa os Hubs de notificação do Azure para notificações por push para um aplicativo iOS baseado em Swift usando o [API REST](/rest/api/notificationhubs/). Você também criar um aplicativo iOS em branco que recebe notificações por push usando o [Apple Push Notification service (APNs)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Este tutorial o guiará durante as etapas a seguir:

> [!div class="checklist"]
> * Gere o arquivo de solicitação de assinatura de certificado.
> * Solicitação de seu aplicativo para notificações por push.
> * Crie um perfil de provisionamento para o aplicativo.
> * Criar um hub de notificação.
> * Configure o hub de notificação com informações de APNs.
> * Conecte seu aplicativo iOS a um hub de notificação.
> * Teste a solução.

## <a name="prerequisites"></a>Pré-requisitos

Para acompanhar, você precisa:

- Para percorrer [visão geral dos Hubs de notificação do Azure](notification-hubs-push-notification-overview.md) se você não estiver familiarizado com o serviço.
- Para saber mais sobre [registros e a instalação](notification-hubs-push-notification-registration-management.md).
- Um ativo [conta de desenvolvedor da Apple](https://developer.apple.com).
- Um Mac executando o Xcode, juntamente com um certificado de desenvolvedor válida instalado em seu conjunto de chaves.
- Um dispositivo físico iPhone, você pode executar e depurar com, pois não é possível testar notificações por push com o simulador.
- Seu dispositivo físico iPhone registrado na [Portal da Apple](https://developer.apple.com) e associado ao certificado.
- Uma [assinatura do Azure](https://portal.azure.com) onde você pode criar e gerenciar recursos.

Mesmo que você não tenha nenhuma experiência anterior com o desenvolvimento do iOS, você deve ser capaz de acompanhar as etapas para criar este exemplo primeiro princípios. No entanto, você se beneficiará de estar familiarizado com os seguintes conceitos:

- Criar aplicativos iOS com Xcode e Swift.
- Configurando uma [hub de notificação do Azure](notification-hubs-ios-apple-push-notification-apns-get-started.md) para iOS.
- O [Portal do desenvolvedor da Apple](https://developer.apple.com) e o [portal do Azure](https://portal.azure.com).

> [!NOTE]
> O hub de notificação será configurado para usar o **área restrita** somente o modo de autenticação. Você não deve usar esse modo de autenticação para cargas de trabalho de produção.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>Conectar seu aplicativo iOS a um hub de notificação

Nesta seção, você compilará o aplicativo do iOS que se conecta ao hub de notificação.  

### <a name="create-an-ios-project"></a>Criar um projeto do iOS

1. No Xcode, crie um novo projeto do iOS e selecione o modelo **Aplicativo de Modo de Exibição Único** .

1. Ao definir as opções para o novo projeto:

   1. Especifique o **nome do produto** (PushDemo) e **identificador de organização** (`com.<organization>`) que você usou quando definiu **identificador de pacote** no Portal do desenvolvedor da Apple.

   1. Escolha o **Team** que o **ID do aplicativo** foi configurada para.

   1. Defina as **linguagem** à **Swift**.

   1. Selecione **Avançar**.

1. Criar uma nova pasta denominada **SupportingFiles**.

1. Crie um novo arquivo de lista de p chamado **devsettings.plist** na **SupportingFiles** pasta. Certifique-se de adicionar essa pasta ao seu **gitignore** arquivo para que não é confirmada quando estiver trabalhando com um repositório git. Em um aplicativo de produção, você seria provavelmente condicionalmente definindo esses segredos como parte de um processo de compilação automatizado. Essas configurações não são abordadas neste passo a passo.

1. Atualização **devsettings.plist** para incluir as seguintes entradas de configuração, usando seus próprios valores do hub de notificação que você provisionou:

   | Chave                            | Type                     | Value                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | Cadeia de caracteres                   | \<hubKey>                  |
   | notificationHubKeyName         | String                   | \<hubKeyName>              |
   | notificationHubName            | Cadeia de caracteres                   | \<hubName>                 |
   | notificationHubNamespace       | Cadeia de caracteres                   | \<hubNamespace>            |

   Você pode encontrar os valores necessários, navegando até o recurso de hub de notificação no portal do Azure. Em particular, o **notificationHubName** e **notificationHubNamespace** os valores estão no canto superior direito dos **Essentials** resumo dentro de **Visão geral** página.

   ![Resumo de recursos básicos de Hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   Você também pode encontrar o **notificationHubKeyName** e **notificationHubKey** valores, navegando até **políticas de acesso** e selecionando os respectivos  **Política de acesso**, tais como `DefaultFullSharedAccessSignature`. Depois disso, copiem o **cadeia de Conexão primária** o valor é prefixado com `SharedAccessKeyName=` para `notificationHubKeyName` e o valor é prefixado com `SharedAccessKey=` para o `notificationHubKey`.

   A cadeia de caracteres de conexão deve estar no seguinte formato:

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   Para manter a simplicidade, especifique `DefaultFullSharedAccessSignature` para que você pode usar o token para enviar notificações. Na prática a `DefaultListenSharedAccessSignature` seria uma opção melhor para situações em que você apenas deseja receber notificações.

1. Sob **navegador de projeto**, selecione o **nome do projeto** e, em seguida, selecione o **geral** guia.

1. Encontre **identidade** e, em seguida, defina o **identificador de pacote** valor para que ele corresponda à `com.<organization>.PushDemo`, que é o valor usado para o **ID do aplicativo** em uma etapa anterior.

1. Encontre **Signing**e, em seguida, selecionar as devidas **equipe** para sua **conta de desenvolvedor da Apple**. O **equipe** valor deve corresponder àquela sob a qual você criou seus certificados e perfis.

1. Xcode deverá automaticamente efetuar pull apropriado **perfil de provisionamento** valor com base nas **identificador de pacote**. Se você não vir o novo **perfil de provisionamento** valor, tente atualizar os perfis para o **identidade de assinatura** selecionando **Xcode**  >  **Preferências** > **conta** > **exibir detalhes**. Selecione **identidade de assinatura**e, em seguida, selecione o **atualizar** botão no canto inferior direito para baixar os perfis.

1. Selecione o **capacidades** guia e certifique-se de que **notificações por push** estão habilitados.

1. Abra seu **Appdelegate** arquivo para implementar o **UNUserNotificationCenterDelegate** de protocolo e adicione o seguinte código à parte superior da classe:

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    Você usará esses membros mais tarde. Especificamente, você usará o **marcas** e **genericTemplate** membros como parte do registro. Para obter mais informações sobre marcas, consulte [marcas de registros](notification-hubs-tags-segment-push-message.md) e [registros de modelo](notification-hubs-templates-cross-platform-push-messages.md).

1. No mesmo arquivo, adicione o seguinte código para o **didFinishLaunchingWithOptions** função:

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    Esse código recupera os valores de configuração de **devsettings.plist**, define o **AppDelegate** da classe como o **UNUserNotificationCenter** delegar autorização de solicitações para notificações por push e, em seguida, chama **registerForRemoteNotifications**.

    Para simplificar, o código suporta *iOS 10 e posterior somente*. Você pode adicionar suporte para versões anteriores do sistema operacional usando condicionalmente os respectivos APIs e as abordagens como você faria normalmente.

1. No mesmo arquivo, adicione as seguintes funções:

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    O código usa o **installationId** e **pushChannel** valores para se registrar no hub de notificação. Nesse caso, você está usando **UIDevice.current.identifierForVendor** para fornecer um valor exclusivo para identificar o dispositivo e a formatação, em seguida, o **deviceToken** para fornecer os detalhes desejados  **pushChannel** valor. O **showAlert** função existe simplesmente para exibir um texto de mensagem para fins de demonstração.

1. Ainda na **Appdelegate** do arquivo, adicione o **willPresent** e **didReceive** funções para **UNUserNotificationCenterDelegate**. Essas funções exibem um alerta quando eles serão notificados de que um aplicativo está em execução em primeiro plano ou segundo plano, respectivamente.

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. Adicionar instruções de impressão na parte inferior do **didRegisterForRemoteNotificationsWithDeviceToken** função para verificar se **installationId** e **pushChannel** estão sendo valores atribuídos.

1. Criar o **modelos**, **Services**, e **utilitários** pastas para os componentes fundamentais você o adicionará ao projeto mais tarde.

1. Verifique se o projeto compilado e executado em um dispositivo físico. Notificações por Push não podem ser testadas usando o simulador.

### <a name="create-models"></a>Criar modelos

Nesta etapa, você criará um conjunto de modelos para representar o [API de REST de Hubs de notificação](/rest/api/notificationhubs/) cargas e dados de token de SAS (assinatura) para armazenar o acesso compartilhado necessário.

1. Adicione um novo arquivo Swift chamado **PushTemplate.swift** para o **modelos** pasta. Esse modelo fornece uma estrutura que representa o **corpo** de um modelo individual como parte do **DeviceInstallation** carga.

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. Adicione um novo arquivo Swift chamado **DeviceInstallation.swift** para o **modelos** pasta. Esse arquivo define uma estrutura que representa o conteúdo para criar ou atualizar uma **instalação de dispositivo**. Adicione o seguinte código ao arquivo:

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. Adicione um novo arquivo Swift chamado **TokenData.swift** para o **modelos** pasta. Esse modelo será usado para armazenar um token SAS, juntamente com sua expiração.

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>Gerar um token SAS

Os Hubs de notificação usam a mesma infraestrutura de segurança como o barramento de serviço do Azure. Para chamar a API REST, você precisará [programaticamente, gerar um token SAS](/rest/api/eventhub/generate-sas-token) que pode ser usado na **autorização** cabeçalho da solicitação.  

O token resultante será no seguinte formato:

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

O próprio processo envolve as mesmas seis etapas principais:  

1. Computação de expiração na [época do UNIX](https://en.wikipedia.org/wiki/Unix_time) formato, o que significa que o número de segundos decorridos desde a meia-noite de tempo Universal Coordenado, 1º de janeiro de 1970.
1. Formatação de **ResourceUrl** que representa o recurso que você está tentando acessar, portanto, é codificado por percentual e em minúsculas. O **ResourceUrl** tem a forma `'https://<namespace>.servicebus.windows.net/<hubName>'`.
1. Preparando-se a **StringToSign**, que é formatado como `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'`.
1. Computação e a codificação de Base64 a **assinatura** usando o hash HMAC-SHA256 a **StringToSign** valor. O valor de hash é usado com o **chave** fazem parte do **cadeia de caracteres de Conexão** para os respectivos **regra de autorização**.
1. A codificação Base64 de formatação **assinatura** para que ela seja codificada por cento.
1. Construir o token no formato esperado usando o **UrlEncodedSignature**, **ExpiryEpoch**, **KeyName**, e **UrlEncodedResourceUrl** valores.

Consulte a [documentação do barramento de serviço do Azure](../service-bus-messaging/service-bus-sas.md) para obter uma visão mais completa de assinatura de acesso compartilhado e usá-lo como o barramento de serviço do Azure e Hubs de notificação.

Para os fins deste exemplo Swift, você vai usar o software livre da Apple **CommonCrypto** biblioteca para ajudar com o hash da assinatura. Como é uma biblioteca C, não é acessível em Swift fora da caixa. Você pode disponibilizar a biblioteca por meio de um cabeçalho ponte.

Para adicionar e configurar o cabeçalho ponte:

1. No Xcode, selecione **arquivo** > **New** > **arquivo** > **arquivo de cabeçalho**. Nomeie o arquivo de cabeçalho **BridgingHeader.h**.

1. Editar o arquivo a ser importado **CommonHMAC.h**:

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. Atualizar o destino **configurações de Build** para referenciar o cabeçalho ponte:

   1. Abra o **configurações de construção** guia e role para baixo até a **compilador Swift** seção.

   1. Certifique-se de que o **instalar o cabeçalho de compatibilidade de Objective-C** opção for definida como **Sim**.

   1. Insira o caminho do arquivo `'<ProjectName>/BridgingHeader.h'` para o **cabeçalho de ponte do Objective-C** opção. Esse é o caminho de arquivo para nosso cabeçalho ponte.

   Se você não encontrar essas opções, certifique-se de que você tenha o **todos os** modo de exibição selecionado em vez de **básica** ou **personalizado**.

   Há muitas bibliotecas de terceiros wrapper de código-fonte aberto disponíveis que podem fazer usando **CommonCrypto** um pouco mais fácil. No entanto, a discussão dessas bibliotecas é além do escopo deste artigo.

1. Adicionar um novo arquivo Swift denominado **TokenUtility.swift** dentro de **utilitários** pasta e adicione o seguinte código:

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   Esse utilitário encapsula a lógica responsável por gerar o token SAS.

   Conforme descrito anteriormente, o **getSasToken** função orquestra as etapas de alto nível necessárias para preparar o token. A função será chamada pelo serviço de instalação posteriormente no tutorial.

   As duas outras funções são chamadas pelo **getSasToken** função: **sha256HMac** para a assinatura de computação e **urlEncodedString** para codificar a URL associada cadeia de caracteres. O **urlEncodedString** função é necessária, pois não é possível obter a saída necessária usando internos **addingPercentEncoding** função.

   O [SDK do iOS de armazenamento do Azure](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) é um excelente exemplo de como abordar essas operações em Objective-C. Mais informações sobre tokens de SAS do barramento de serviço do Azure podem ser encontradas na [documentação do barramento de serviço do Azure](../service-bus-messaging/service-bus-sas.md).

### <a name="verify-the-sas-token"></a>Verifique se o token SAS

Antes de implementar o serviço de instalação no cliente, verifique que nosso aplicativo está gerando corretamente o token SAS usando o utilitário HTTP de escolha. Para os fins deste tutorial, nossa ferramenta de escolha estará **Postman**.

Use um ponto de interrupção ou instrução de impressão posicionada adequadamente para observar a **installationId** e o **token** valores que está sendo gerados pelo aplicativo.

Siga estas etapas para chamar o **instalações** API:

1. Na **Postman**, abra uma nova guia.

1. Definir a solicitação **obter** e especifique o endereço a seguir:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. Configure os cabeçalhos de solicitação da seguinte maneira:

   | Chave           | Value            |
   | ------------- | ---------------- |
   | Content-Type  | aplicativo/json |
   | Authorization | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. Selecione o **código** botão que aparece no canto superior direito sob a **salvar** botão. A solicitação deve ser semelhante ao exemplo a seguir:

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. Selecione o **enviar** botão.

Nenhum registro existe para especificado **installationId** neste momento. A verificação deve resultar em uma resposta de "404 não encontrado", em vez de uma resposta, "401 não autorizado". Esse resultado deve confirmar que o token SAS foi aceita.

### <a name="implement-the-installation-service-class"></a>Implementar a classe de serviço de instalação

Em seguida, você implementará básica wrapper em torno de [API REST de instalações](/rest/api/notificationhubs/create-overwrite-installation).  

Adicione um novo arquivo Swift chamado **NotificationRegistrationService.swift** sob o **Services** pasta e, em seguida, adicione o seguinte código para este arquivo:

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

Os requisito detalhes são fornecidos como parte da inicialização. Marcas e modelos, opcionalmente, são passados para o **registre** função como parte da **instalação do dispositivo** carga JSON.  

O **registrar** função chamar outras funções privadas para preparar a solicitação. Depois que uma resposta for recebida, a conclusão é chamada e indica se o registro foi bem-sucedido.  

O ponto de extremidade de solicitação é construído de **getBaseAddress** função. A construção usa os parâmetros de hub de notificação *namespace* e *nome* que foram fornecidos durante a inicialização.  

O **getSasToken** função verifica se o token armazenado no momento é válido. Se o token não é válido, a função chamará **TokenUtility** para gerar um novo token e, em seguida, armazena-o antes de retornar um valor.

Por fim, **encodeToJson** converte os objetos do respectivo modelo em JSON para uso como parte do corpo da solicitação.

### <a name="invoke-the-notification-hubs-rest-api"></a>Invocar os API REST de Hubs de notificação

A última etapa é atualizar **AppDelegate** usar **NotificationRegistrationService** registrar com nosso **NotificationHub**.

1. Abra **Appdelegate** e adicione uma variável de nível de classe para armazenar uma referência para o **NotificationRegistrationService**:

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. No mesmo arquivo, atualize o **didRegisterForRemoteNotificationsWithDeviceToken** função para inicializar o **NotificationRegistrationService** com os parâmetros necessários e em seguida, chame o **registrar** função.

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    Para manter a simplicidade, você vai usar algumas instruções de impressão para atualizar a janela de saída com o resultado do **registrar** operação.

1. Agora, compile e execute o aplicativo em um dispositivo físico. Você deve ver "Registrado" na janela de saída.

## <a name="test-the-solution"></a>Testar a solução

Nosso aplicativo neste estágio é registrado com **NotificationHub** e podem receber notificações por push. No Xcode, pare o depurador e feche o aplicativo se ele estiver em execução. Em seguida, verifique se o **instalação de dispositivo** detalhes estão conforme o esperado e que nosso aplicativo agora pode receber notificações por push.  

### <a name="verify-the-device-installation"></a>Verifique se a instalação do dispositivo

Agora você pode fazer a mesma solicitação, como você fez anteriormente usando **Postman** para [verificando o token SAS](#verify-the-sas-token). Supondo que o token SAS não tiver expirado, a resposta agora deve incluir os detalhes da instalação fornecida por você, como as marcas e modelos.

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>Enviar uma notificação de teste (portal do Azure)

É a maneira mais rápida que agora você pode receber notificações de teste navegar até o hub de notificação no portal do Azure:

1. No portal do Azure, navegue até a **visão geral** guia em seu hub de notificação.

1. Selecione **envio de teste**, que está acima do **Essentials** resumo no canto superior esquerdo da janela do portal:

    ![Conceitos básicos de Hubs de notificação botão de envio de teste de resumo](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. Escolher **modelo personalizado** da **plataformas** lista.

1. Insira **12345** para o **enviar para a expressão de marca**. Você tinha especificado nessa marca na nossa instalação.

1. Opcionalmente, editar os **mensagem** na carga JSON:

    ![Envio de teste de Hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. Selecione **Enviar**. O portal deve indicar se a notificação foi enviada com êxito para o dispositivo:

    ![Resultados de envio de teste de Hubs de notificação](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    Supondo que o aplicativo não está em execução em primeiro plano, você também verá uma notificação na **Centro de notificações** em seu dispositivo. Tocar a notificação deve abrir o aplicativo e mostrar o alerta.

    ![Exemplo de notificação recebida](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>Enviar uma notificação de teste (Mail operadora)

Você pode enviar notificações por meio de [API REST](/rest/api/notificationhubs/) usando **Postman**, que pode ser uma maneira mais conveniente para testar.

1. Abra uma nova guia no **Postman**.

1. Definir a solicitação **POST**e insira o endereço a seguir:

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. Configure os cabeçalhos de solicitação da seguinte maneira:

   | Chave                            | Value                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | tags                           | “12345”                        |

1. Configure a solicitação **corpo** usar **RAW - JSON (application.json)** com a seguinte carga JSON:

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. Selecione o **código** botão, que está sob o **salvar** botão no canto superior direito da janela. A solicitação deve ser semelhante ao exemplo a seguir:

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. Selecione o **enviar** botão.

Você deve obter um código de status de êxito e receber a notificação no dispositivo cliente.

## <a name="next-steps"></a>Próximas etapas
Agora você tem um aplicativo Swift do iOS básico conectado a um hub de notificação por meio de [API REST](/rest/api/notificationhubs/) e pode enviar e receber notificações. Para obter mais informações, consulte os seguintes artigos:

- [Visão geral de Hubs de notificação do Azure](notification-hubs-push-notification-overview.md)
- [APIs REST dos Hubs de Notificação](/rest/api/notificationhubs/)
- [SDK de Hubs de notificação para operações de back-end](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [Hubs de notificação do SDK no GitHub](https://github.com/Azure/azure-notificationhubs)
- [Registrar com o back-end do aplicativo](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [Gerenciamento de registro](notification-hubs-push-notification-registration-management.md)
- [Trabalhando com marcas](notification-hubs-tags-segment-push-message.md) 
- [Trabalhando com modelos personalizados](notification-hubs-templates-cross-platform-push-messages.md)
- [Controle de acesso do barramento de serviço com assinaturas de acesso compartilhado](../service-bus-messaging/service-bus-sas.md)
- [Por meio de programação gerar tokens SAS](/rest/api/eventhub/generate-sas-token)
- [Segurança da Apple: criptografia comum](https://developer.apple.com/security/)
- [Hora da época do UNIX](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)
