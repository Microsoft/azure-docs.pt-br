---
title: Guia de migração do ADAL para MSAL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Conheça as diferenças entre o MSAL para iOS/macOS e a biblioteca de autenticação do Azure AD para ObjectiveC (ADAL. ObjC) e como migrar para o MSAL para iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 7dc3241198fbc6eeddba059251f28c6dc35c8a29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98754935"
---
# <a name="migrate-applications-to-msal-for-ios-and-macos"></a>Migrar aplicativos para o MSAL para iOS e macOS

A biblioteca de autenticação Azure Active Directory ([Adal Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc)) foi criada para funcionar com contas de Azure Active Directory por meio do ponto de extremidade v 1.0.

A biblioteca de autenticação da Microsoft para iOS e macOS (MSAL) foi criada para funcionar com todas as identidades da Microsoft, como contas do Azure Active Directory (Azure AD), contas pessoais da Microsoft e contas de Azure AD B2C por meio da plataforma de identidade da Microsoft (formalmente o ponto de extremidade v 2.0 do Azure AD).

A plataforma de identidade da Microsoft tem algumas diferenças importantes com o Azure Active Directory v 1.0. Este artigo destaca essas diferenças e fornece orientação para migrar um aplicativo do ADAL para o MSAL.

## <a name="adal-and-msal-app-capability-differences"></a>Diferenças de capacidade de aplicativo ADAL e MSAL

### <a name="who-can-sign-in"></a>Quem pode entrar

* A ADAL só dá suporte a contas corporativas e de estudante – também conhecidas como contas do Azure AD.
* O MSAL dá suporte a contas pessoais da Microsoft (contas MSA), como Hotmail.com, Outlook.com e Live.com.
* O MSAL dá suporte a contas corporativas e de estudante e contas de Azure AD B2C.

### <a name="standards-compliance"></a>Conformidade com os padrões

* A plataforma de identidade da Microsoft segue os padrões OAuth 2,0 e OpenId Connect.

### <a name="incremental-and-dynamic-consent"></a>Consentimento incremental e dinâmico

* O ponto de extremidade do Azure Active Directory v 1.0 requer que todas as permissões sejam declaradas com antecedência durante o registro do aplicativo. Isso significa que essas permissões são estáticas.
* A plataforma Microsoft Identity permite que você solicite permissões dinamicamente. Os aplicativos podem solicitar permissões apenas conforme necessário e solicitar mais como o aplicativo precisa delas.

Para obter mais informações sobre as diferenças entre o Azure Active Directory v 1.0 e a plataforma de identidade da Microsoft, consulte [por que atualizar para a plataforma Microsoft Identity?](../azuread-dev/azure-ad-endpoint-comparison.md).

## <a name="adal-and-msal-library-differences"></a>Diferenças de biblioteca ADAL e MSAL

A API pública do MSAL reflete algumas diferenças importantes entre o Azure AD v 1.0 e a plataforma de identidade da Microsoft.

### <a name="msalpublicclientapplication-instead-of-adauthenticationcontext"></a>MSALPublicClientApplication em vez de ADAuthenticationContext

`ADAuthenticationContext` é o primeiro objeto criado por um aplicativo ADAL. Representa uma instanciação da ADAL. Os aplicativos criam uma nova instância do `ADAuthenticationContext` para cada combinação de Azure Active Directory nuvem e locatário (autoridade). O mesmo `ADAuthenticationContext` pode ser usado para obter tokens para vários aplicativos cliente públicos.

No MSAL, a principal interação é por meio de um `MSALPublicClientApplication` objeto, que é modelado após o [cliente público OAuth 2,0](https://tools.ietf.org/html/rfc6749#section-2.1). Uma instância do `MSALPublicClientApplication` pode ser usada para interagir com várias nuvens do AAD e locatários, sem a necessidade de criar uma nova instância para cada autoridade. Para a maioria dos aplicativos, uma `MSALPublicClientApplication` instância é suficiente.

### <a name="scopes-instead-of-resources"></a>Escopos em vez de recursos

Na ADAL, um aplicativo tinha que fornecer um identificador de *recurso* como `https://graph.microsoft.com` para adquirir tokens do ponto de extremidade Azure Active Directory v 1.0. Um recurso pode definir um número de escopos ou oAuth2Permissions no manifesto do aplicativo, que ele compreende. Isso permitia que os aplicativos cliente solicitassem tokens desse recurso para um determinado conjunto de escopos predefinidos durante o registro do aplicativo.

No MSAL, em vez de um único identificador de recurso, os aplicativos fornecem um conjunto de escopos por solicitação. Um escopo é um identificador de recurso seguido por um nome de permissão no formulário recurso/permissão. Por exemplo, `https://graph.microsoft.com/user.read`

Há duas maneiras de fornecer escopos no MSAL:

* Forneça uma lista de todas as permissões de que seus aplicativos precisam. Por exemplo: 

    `@[@"https://graph.microsoft.com/directory.read", @"https://graph.microsoft.com/directory.write"]`

    Nesse caso, o aplicativo solicita as `directory.read` permissões e `directory.write` . O usuário será solicitado a consentir essas permissões se elas ainda não tiverem consentidas antes para este aplicativo. O aplicativo também pode receber permissões adicionais para as quais o usuário já tenha consentido para o aplicativo. O usuário só será solicitado a dar consentimento para novas permissões ou permissões que não foram concedidas.

* O escopo de `/.default`.

Esse é o escopo interno para cada aplicativo. Ele se refere à lista estática de permissões configuradas quando o aplicativo foi registrado. Seu comportamento é semelhante ao do `resource` . Isso pode ser útil ao migrar para garantir que um conjunto semelhante de escopos e experiência do usuário seja mantido.

Para usar o `/.default` escopo, acrescente `/.default` ao identificador de recurso. Por exemplo: `https://graph.microsoft.com/.default`. Se o recurso terminar com uma barra ( `/` ), você ainda deverá acrescentar `/.default` , incluindo a barra de avanço à esquerda, resultando em um escopo que tem uma barra dupla de avanço ( `//` ).

Você pode ler mais informações sobre como usar o escopo "/.default" [aqui](./v2-permissions-and-consent.md#the-default-scope)

### <a name="supporting-different-webview-types--browsers"></a>Suporte a diferentes tipos de WebView & navegadores

A ADAL só dá suporte a UIWebView/WKWebView para iOS e WebView para macOS. O MSAL para iOS dá suporte a mais opções para exibir o conteúdo da Web ao solicitar um código de autorização e não dá mais suporte `UIWebView` a; o que pode melhorar a experiência e a segurança do usuário.

Por padrão, o MSAL no iOS usa o [ASWebAuthenticationSession](https://developer.apple.com/documentation/authenticationservices/aswebauthenticationsession?language=objc), que é o componente da Web que a Apple recomenda para autenticação em dispositivos IOS + +. Ele fornece benefícios únicos de Sign-On (SSO) por meio do compartilhamento de cookies entre aplicativos e o navegador Safari.

Você pode optar por usar um componente da Web diferente dependendo dos requisitos do aplicativo e da experiência do usuário final que desejar. Consulte [tipos de exibição da Web com suporte](customize-webviews.md) para obter mais opções.

Ao migrar do ADAL para o MSAL, `WKWebView` o fornece a experiência do usuário mais semelhante à Adal no Ios e no MacOS. Incentivamos você a migrar para o `ASWebAuthenticationSession` no Ios, se possível. Para o macOS, incentivamos você a usar o `WKWebView` .

### <a name="account-management-api-differences"></a>Diferenças de API de gerenciamento de conta

Ao chamar os métodos ADAL `acquireToken()` ou `acquireTokenSilent()` , você recebe um `ADUserInformation` objeto que contém uma lista de declarações do `id_token` que representa a conta que está sendo autenticada. Além disso, `ADUserInformation` retorna um `userId` com base na `upn` declaração. Após a aquisição de token interativo inicial, a ADAL espera que o desenvolvedor forneça `userId` em todas as chamadas silenciosas.

A ADAL não fornece uma API para recuperar identidades de usuário conhecidas. Ele depende do aplicativo para salvar e gerenciar essas contas.

O MSAL fornece um conjunto de APIs para listar todas as contas conhecidas para o MSAL sem a necessidade de adquirir um token.

Como a ADAL, MSAL retorna informações de conta que contêm uma lista de declarações do `id_token` . Faz parte do `MSALAccount` objeto dentro do `MSALResult` objeto.

O MSAL fornece um conjunto de APIs para remover contas, tornando as contas removidas inacessíveis para o aplicativo. Depois que a conta for removida, as chamadas de aquisição de token posteriores solicitarão que o usuário faça a aquisição de token interativa. A remoção de conta só se aplica ao aplicativo cliente que o iniciou e não remove a conta dos outros aplicativos em execução no dispositivo ou no navegador do sistema. Isso garante que o usuário Continue tendo uma experiência de SSO no dispositivo mesmo depois de sair de um aplicativo individual.

Além disso, o MSAL também retorna um identificador de conta que pode ser usado para solicitar um token silenciosamente mais tarde. No entanto, o identificador de conta (acessível por meio `identifier` da propriedade no `MSALAccount` objeto) não é exibível e você não pode assumir em que formato ele se encontra nem deve tentar interpretá-lo ou analisá-lo.

### <a name="migrating-the-account-cache"></a>Migrando o cache da conta

Ao migrar do ADAL, os aplicativos normalmente armazenam `userId` o Adal, que não tem o `identifier` exigido pelo MSAL. Como uma etapa de migração única, um aplicativo pode consultar uma conta do MSAL usando o userId da ADAL com a seguinte API:

`- (nullable MSALAccount *)accountForUsername:(nonnull NSString *)username error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Essa API lê o cache de MSAL e ADAL para localizar a conta por userId da ADAL (UPN).

Se a conta for encontrada, o desenvolvedor deverá usar a conta para realizar a aquisição de token silenciosa. A primeira aquisição de token silenciosa atualizará efetivamente a conta, e o desenvolvedor obterá um identificador de conta compatível com MSAL no resultado de MSAL ( `identifier` ). Depois disso, somente `identifier` deve ser usado para pesquisas de conta usando a seguinte API:

`- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier error:(NSError * _Nullable __autoreleasing * _Nullable)error;`

Embora seja possível continuar usando a ADAL `userId` para todas as operações no MSAL, como o `userId` é baseado no UPN, ele está sujeito a várias limitações que resultam em uma experiência de usuário inadequada. Por exemplo, se o UPN for alterado, o usuário precisará entrar novamente. Recomendamos que todos os aplicativos usem a conta não-reutilizável `identifier` para todas as operações.

Leia mais sobre a [migração de estado do cache](sso-between-adal-msal-apps-macos-ios.md).

### <a name="token-acquisition-changes"></a>Alterações de aquisição de token

MSAL introduz algumas alterações de chamada de aquisição de token:

* Como a ADAL, `acquireTokenSilent` o sempre resulta em uma solicitação silenciosa.
* Ao contrário da ADAL, o `acquireToken` sempre resulta em uma interface do usuário acionável por meio da exibição da Web ou do aplicativo Microsoft Authenticator. Dependendo do estado de SSO dentro do WebView/Microsoft Authenticator, o usuário pode ser solicitado a inserir suas credenciais.
* Na ADAL, `acquireToken` com a `AD_PROMPT_AUTO` primeira tentativa de aquisição de token silencioso e mostra apenas a interface do usuário se a solicitação silenciosa falhar. No MSAL, essa lógica pode ser obtida primeiro chamando `acquireTokenSilent` e apenas chamando `acquireToken` se a aquisição silenciosa falhar. Isso permite que os desenvolvedores personalizem a experiência do usuário antes de iniciar a aquisição de token interativa.

### <a name="error-handling-differences"></a>Diferenças de tratamento de erro

O MSAL fornece mais clareza entre os erros que podem ser tratados pelo seu aplicativo e aqueles que exigem a intervenção do usuário. Há um número limitado de erros que o desenvolvedor deve manipular:

* `MSALErrorInteractionRequired`: o usuário deve fazer uma solicitação interativa. Isso pode ser causado por vários motivos, como uma sessão de autenticação expirada, a política de acesso condicional foi alterada, um token de atualização expirou ou foi revogado, não há tokens válidos no cache e assim por diante.
* `MSALErrorServerDeclinedScopes`: A solicitação não foi totalmente concluída e alguns escopos não tinham acesso concedido. Isso pode ser causado por um usuário que está recusando o consentimento para um ou mais escopos.

A manipulação de todos os outros erros na [ `MSALError` lista](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128) é opcional. Você pode usar as informações nesses erros para melhorar a experiência do usuário.

Consulte [tratamento de exceções e erros usando o MSAL](msal-error-handling-ios.md) para obter mais informações sobre o tratamento de erros do MSAL.

### <a name="broker-support"></a>Suporte do Broker

MSAL, a partir da versão 0.3.0, fornece suporte para autenticação orientada usando o aplicativo Microsoft Authenticator. Microsoft Authenticator também habilita o suporte para cenários de acesso condicional. Exemplos de cenários de acesso condicional incluem políticas de conformidade do dispositivo que exigem que o usuário registre o dispositivo por meio do Intune ou registre-se no AAD para obter um token. E políticas de acesso condicional de MAM (gerenciamento de aplicativo móvel), que exigem prova de conformidade antes que seu aplicativo possa obter um token.

Para habilitar o Broker para seu aplicativo:

1. Registre um formato de URI de redirecionamento compatível com o agente para o aplicativo. O formato do URI de redirecionamento compatível com o agente é `msauth.<app.bundle.id>://auth` . Substitua `<app.bundle.id>` pela ID do pacote do seu aplicativo. Se você estiver migrando do ADAL e seu aplicativo já tiver capacidade de agente, não há nada de mais que você precise fazer. O URI de redirecionamento anterior é totalmente compatível com MSAL, portanto, você pode pular para a etapa 3.

2. Adicione o esquema de URI de redirecionamento do seu aplicativo ao seu arquivo info. plist. Para o URI de redirecionamento MSAL padrão, o formato é `msauth.<app.bundle.id>` . Por exemplo:

    ```xml
    <key>CFBundleURLSchemes</key>
    <array>
        <string>msauth.<app.bundle.id></string>
    </array>
    ```

3. Adicione os seguintes esquemas ao info. plist do seu aplicativo em LSApplicationQueriesSchemes:

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

4. Adicione o seguinte ao seu arquivo AppDelegate. m para lidar com retornos de chamada: Objective-C:
    
    ```objc
    - (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
    {
        return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
    }
    ```
    
    Swift:
    
    ```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }
    ```

### <a name="business-to-business-b2b"></a>Entre empresas (B2B)

Na ADAL, você cria instâncias separadas do `ADAuthenticationContext` para cada locatário para o qual o aplicativo solicita tokens. Isso não é mais um requisito no MSAL. No MSAL, você pode criar uma única instância do `MSALPublicClientApplication` e usá-la para qualquer nuvem e organização do AAD especificando uma autoridade diferente para chamadas acquireToken e acquireTokenSilent.

## <a name="sso-in-partnership-with-other-sdks"></a>SSO em parceria com outros SDKs

O MSAL para iOS pode obter o SSO por meio de um cache unificado com os seguintes SDKs:

- Objetivo de ADAL-C 2.7. x +
- MSAL.NET para Xamarin 2.4. x +
- ADAL.NET para Xamarin 4.4. x +

O SSO é obtido por meio do compartilhamento de conjunto de chaves do iOS e só está disponível entre aplicativos publicados da mesma conta de desenvolvedor da Apple.

O SSO por meio do compartilhamento de conjunto de chaves do iOS é o único tipo de SSO silencioso.

No macOS, o MSAL pode obter SSO com outros MSAL para aplicativos baseados em iOS e macOS e aplicativos baseados em Objective-C de ADAL.

O MSAL no iOS também dá suporte a dois outros tipos de SSO:

* SSO por meio do navegador da Web. O MSAL para iOS dá suporte `ASWebAuthenticationSession` , que fornece o SSO por meio de cookies compartilhados entre outros aplicativos no dispositivo e, especificamente, o navegador Safari.
* SSO por meio de um agente de autenticação. Em um dispositivo iOS, Microsoft Authenticator atua como o agente de autenticação. Ele pode seguir políticas de acesso condicional, como exigir um dispositivo compatível, e fornece SSO para dispositivos registrados. Os SDKs do MSAL, começando com a versão 0.3.0, oferecem suporte a um agente por padrão.

## <a name="intune-mam-sdk"></a>SDK do MAM do Intune

O [SDK do MAM do Intune](/intune/app-sdk-get-started) dá suporte a MSAL para Ios a partir da versão [11.1.2](https://github.com/msintuneappsdk/ms-intune-app-sdk-ios/releases/tag/11.1.2)

## <a name="msal-and-adal-in-the-same-app"></a>MSAL e ADAL no mesmo aplicativo

A ADAL versão 2.7.0, e acima, não pode coexistir com MSAL no mesmo aplicativo. O principal motivo é por causa do código comum do submódulo compartilhado. Como Objective-C não dá suporte a namespaces, se você adicionar estruturas ADAL e MSAL ao seu aplicativo, haverá duas instâncias da mesma classe. Não há nenhuma garantia para a qual uma é escolhida no tempo de execução. Se ambos os SDKs estiverem usando a mesma versão da classe conflitante, seu aplicativo ainda poderá funcionar. No entanto, se for uma versão diferente, seu aplicativo poderá experimentar falhas inesperadas que são difíceis de diagnosticar.

Não há suporte para a execução de ADAL e MSAL no mesmo aplicativo de produção. No entanto, se você estiver apenas testando e migrando seus usuários do ADAL Objective-C para o MSAL para iOS e macOS, poderá continuar usando [a Adal Objective-c 2.6.10](https://github.com/AzureAD/azure-activedirectory-library-for-objc/releases/tag/2.6.10). É a única versão que funciona com MSAL no mesmo aplicativo. Não haverá nenhuma nova atualização de recurso para esta versão da ADAL, portanto, ela deve ser usada somente para fins de migração e teste. Seu aplicativo não deve depender da ADAL e da coexistência de MSAL a longo prazo.

Não há suporte para a coexistência de ADAL e MSAL no mesmo aplicativo.
O ADAL e a coexistência de MSAL entre vários aplicativos tem suporte total.

## <a name="practical-migration-steps"></a>Etapas de migração práticas

### <a name="app-registration-migration"></a>Migração de registro do aplicativo

Você não precisa alterar seu aplicativo AAD existente para mudar para MSAL e habilitar as contas do AAD. No entanto, se seu aplicativo baseado em ADAL não der suporte à autenticação orientada, você precisará registrar um novo URI de redirecionamento para o aplicativo antes de alternar para MSAL.

O URI de redirecionamento deve estar neste formato: `msauth.<app.bundle.id>://auth` . Substitua `<app.bundle.id>` pela ID do pacote do seu aplicativo. Especifique o URI de redirecionamento no [portal do Azure](https://aka.ms/MobileAppReg).

Somente para iOS, para oferecer suporte à autenticação baseada em certificado, um URI de redirecionamento adicional precisa ser registrado em seu aplicativo e o portal do Azure no seguinte formato: `msauth://code/<broker-redirect-uri-in-url-encoded-form>` . Por exemplo, `msauth://code/msauth.com.microsoft.mybundleId%3A%2F%2Fauth`

Recomendamos que todos os aplicativos registrem ambos os URIs de redirecionamento.

Se você quiser adicionar suporte para consentimento incremental, selecione as APIs e as permissões às quais seu aplicativo está configurado para solicitar acesso em seu registro de aplicativo na guia **permissões de API** .

Se você estiver migrando do ADAL e quiser dar suporte a contas do AAD e do MSA, seu registro de aplicativo existente precisará ser atualizado para dar suporte a ambos. Não recomendamos que você atualize seu aplicativo de produção existente para dar suporte ao AAD e ao MSA imediatamente. Em vez disso, crie outra ID do cliente que dê suporte ao AAD e à MSA para teste e, depois de verificar que todos os cenários funcionam, atualize o aplicativo existente.

### <a name="add-msal-to-your-app"></a>Adicionar MSAL ao seu aplicativo

Você pode adicionar o SDK do MSAL ao seu aplicativo usando sua ferramenta de gerenciamento de pacotes preferida. Consulte [as instruções detalhadas aqui](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki/Installation).

### <a name="update-your-apps-infoplist-file"></a>Atualizar o arquivo info. plist do seu aplicativo

Somente para iOS, adicione o esquema de URI de redirecionamento do seu aplicativo ao seu arquivo info. plist. Para aplicativos compatíveis com o agente ADAL, ele já deve estar lá. O esquema de URI de redirecionamento MSAL padrão estará no formato: `msauth.<app.bundle.id>` .  

```xml
<key>CFBundleURLSchemes</key>
<array>
    <string>msauth.<app.bundle.id></string>
</array>
```

Adicione os seguintes esquemas ao info. plist do seu aplicativo em `LSApplicationQueriesSchemes` .

```xml
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```

### <a name="update-your-appdelegate-code"></a>Atualizar seu código AppDelegate

Somente para iOS, adicione o seguinte ao seu arquivo AppDelegate. m:

Objective-C:

```objc
- (BOOL)application:(UIApplication *)app openURL:(NSURL *)url options:(NSDictionary<NSString *,id> *)options`
{
    return [MSALPublicClientApplication handleMSALResponse:url sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
    return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
}
```

**Se você estiver usando o Xcode 11**, deverá posicionar o retorno de chamada MSAL no `SceneDelegate` arquivo em vez disso.
Se você der suporte a UISceneDelegate e UIApplicationDelegate para compatibilidade com o iOS mais antigo, o retorno de chamada da MSAL precisará ser colocado nos dois arquivos.

Objective-C:

```objc
 - (void)scene:(UIScene *)scene openURLContexts:(NSSet<UIOpenURLContext *> *)URLContexts
 {
     UIOpenURLContext *context = URLContexts.anyObject;
     NSURL *url = context.URL;
     NSString *sourceApplication = context.options.sourceApplication;
     
     [MSALPublicClientApplication handleMSALResponse:url sourceApplication:sourceApplication];
 }
```

Swift:

```swift
func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
```

Isso permite que o MSAL manipule respostas do agente e do componente da Web.
Isso não era necessário na ADAL, uma vez que o aplicativo "swizzled" Delega métodos de delegação automaticamente. Adicioná-lo manualmente é menos propenso a erros e dá ao aplicativo mais controle.

### <a name="enable-token-caching"></a>Ativar cache de token

Por padrão, a MSAL armazena em cache os tokens do seu aplicativo nos conjuntos de chaves do iOS ou macOS. 

Para habilitar o cache de token:
1. Verifique se seu aplicativo está assinado corretamente
2. Acesse as Configurações do Projeto do Xcode > **guia Funcionalidades** > **Habilitar o Compartilhamento do Conjunto de Chaves**
3. Clique em **+** e insira uma entrada de **Grupos de Conjunto de Chaves** a seguir: 3.a Para iOS, insira `com.microsoft.adalcache` 3.b Para o macOS, insira `com.microsoft.identity.universalstorage`

### <a name="create-msalpublicclientapplication-and-switch-to-its-acquiretoken-and-acquiretokesilent-calls"></a>Criar MSALPublicClientApplication e alternar para suas chamadas acquireToken e acquireTokeSilent

Você pode criar `MSALPublicClientApplication` usando o código a seguir:

Objective-C:

```objc
NSError *error = nil;
MSALPublicClientApplicationConfig *configuration = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
    
MSALPublicClientApplication *application =
[[MSALPublicClientApplication alloc] initWithConfiguration:configuration
                                                     error:&error];
```

Swift:

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application
            
} catch let error as NSError {
  // handle error here
}
```

Em seguida, chame a API de gerenciamento de conta para ver se há alguma conta no cache:

Objective-C:

```objc
NSString *accountIdentifier = nil /*previously saved MSAL account identifier */;
NSError *error = nil;
MSALAccount *account = [application accountForIdentifier:accountIdentifier error:&error];
```

Swift:

```swift
// definitions that need to be initialized
let application: MSALPublicClientApplication!
let accountIdentifier: String! /*previously saved MSAL account identifier */

do {
  let account = try application.account(forIdentifier: accountIdentifier)
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



ou Leia todas as contas:

Objective-C:

```objc
NSError *error = nil;
NSArray<MSALAccount *> *accounts = [application allAccounts:&error];
```

Swift:

```swift
let application: MSALPublicClientApplication!
do {
  let accounts = try application.allAccounts()
  // continue with account usage
} catch let error as NSError {
  // handle error here
}
```



Se uma conta for encontrada, chame a `acquireTokenSilent` API MSAL:

Objective-C:

```objc
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"<your-resource-here>/.default"] account:account];
    
[application acquireTokenSilentWithParameters:silentParameters
                              completionBlock:^(MSALResult *result, NSError *error)
{
    if (result)
    {
        NSString *accessToken = result.accessToken;
        // Use your token
    }
    else
    {
        // Check the error
        if ([error.domain isEqual:MSALErrorDomain] && error.code == MSALErrorInteractionRequired)
        {
            // Interactive auth will be required
        }
            
        // Other errors may require trying again later, or reporting authentication problems to the user
    }
}];
```

Swift:

```swift
let application: MSALPublicClientApplication!
let account: MSALAccount!
        
let silentParameters = MSALSilentTokenParameters(scopes: ["<your-resource-here>/.default"], 
                                                 account: account)
application.acquireTokenSilent(with: silentParameters) {
  (result: MSALResult?, error: Error?) in
  if let accessToken = result?.accessToken {
     // use accessToken
  }
  else {
    // Check the error
    guard let error = error else {
      assert(true, "callback should contain a valid result or error")
      return
    }
    
    let nsError = error as NSError
    if (nsError.domain == MSALErrorDomain
        && nsError.code == MSALError.interactionRequired.rawValue) {
      // Interactive auth will be required
    }
                
    // Other errors may require trying again later, or reporting authentication problems to the user
  }
}
```



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os [Fluxos de autenticação e cenários de aplicativos](authentication-flows-app-scenarios.md)