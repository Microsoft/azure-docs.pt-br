---
title: Considerações sobre o Xamarin iOS (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Saiba mais sobre as considerações para usar o Xamarin iOS com a biblioteca de autenticação da Microsoft para .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 62eb4ab9eb6e4b0e7be0f7aadae1173950d21615
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98064480"
---
# <a name="considerations-for-using-xamarin-ios-with-msalnet"></a>Considerações sobre o uso do Xamarin iOS com MSAL.NET

Ao usar a MSAL.NET (biblioteca de autenticação da Microsoft para .NET) no Xamarin iOS, você deve:

- Substitua e implemente a `OpenUrl` função no `AppDelegate` .
- Habilite grupos de conjunto de chaves.
- Habilitar o compartilhamento de cache de token.
- Habilite o acesso ao conjunto de chaves.
- Entenda os problemas conhecidos com o iOS 12 e o iOS 13 e a autenticação.

## <a name="implement-openurl"></a>Implementar OpenUrl

Substitua o `OpenUrl` método da `FormsApplicationDelegate` classe derivada e chame `AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs` . Veja um exemplo:

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
{
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url);
    return true;
}
```

Além disso, execute as seguintes tarefas:

* Defina um esquema de URI de redirecionamento.
* Exija permissões para que seu aplicativo chame outro aplicativo.
* Ter um formulário específico para o URI de redirecionamento.
* [Registre um URI de redirecionamento](quickstart-register-app.md#add-a-redirect-uri) no portal do Azure.

### <a name="enable-keychain-access"></a>Habilitar o acesso ao conjunto de chaves

Para habilitar o acesso ao conjunto de chaves, certifique-se de que seu aplicativo tenha um grupo de acesso da cadeia de chaves. Você pode definir o grupo de acesso do conjunto de chaves ao criar seu aplicativo usando a `WithIosKeychainSecurityGroup()` API.

Para se beneficiar do cache e do SSO (logon único), defina o grupo de acesso do conjunto de chaves com o mesmo valor em todos os seus aplicativos.

Este exemplo da instalação usa o MSAL 4. x:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Habilite também o acesso ao conjunto de chaves no `Entitlements.plist` arquivo. Use o grupo de acesso a seguir ou seu próprio grupo de acesso.

```xml
<dict>
  <key>keychain-access-groups</key>
  <array>
    <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
  </array>
</dict>
```

Quando você usa a `WithIosKeychainSecurityGroup()` API, o MSAL anexa automaticamente o grupo de segurança ao final da *ID de equipe* do aplicativo ( `AppIdentifierPrefix` ). O MSAL adiciona seu grupo de segurança porque, ao compilar seu aplicativo no Xcode, ele fará o mesmo. É por isso que os direitos no `Entitlements.plist` arquivo precisam ser incluídos `$(AppIdentifierPrefix)` antes do grupo de acesso do conjunto de chaves.

Para obter mais informações, consulte a [documentação de direitos do IOS](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps).

### <a name="enable-token-cache-sharing-across-ios-applications"></a>Habilitar o compartilhamento de cache de token em aplicativos iOS

A partir do MSAL 2. x, você pode especificar um grupo de acesso do conjunto de chaves para manter o cache do token entre vários aplicativos. Essa configuração permite que você compartilhe o cache do token entre vários aplicativos que têm o mesmo grupo de acesso do conjunto de chaves. Você pode compartilhar o cache de token entre aplicativos [Adal.net](https://aka.ms/adal-net) , aplicativos MSAL.net Xamarin. Ios e aplicativos Ios nativos que foram desenvolvidos em [Adal. ObjC](https://github.com/AzureAD/azure-activedirectory-library-for-objc) ou [MSAL. ObjC](https://github.com/AzureAD/microsoft-authentication-library-for-objc).

Ao compartilhar o cache de token, você permite o SSO (logon único) entre todos os aplicativos que usam o mesmo grupo de acesso do conjunto de chaves.

Para habilitar esse compartilhamento de cache, use o `WithIosKeychainSecurityGroup()` método para definir o grupo de acesso do conjunto de chaves com o mesmo valor em todos os aplicativos que compartilham o mesmo cache. O primeiro exemplo de código neste artigo mostra como usar o método.

Anteriormente neste artigo, você aprendeu que o MSAL adiciona `$(AppIdentifierPrefix)` sempre que você usa a `WithIosKeychainSecurityGroup()` API. O MSAL adiciona esse elemento porque a ID da equipe `AppIdentifierPrefix` garante que apenas os aplicativos que são feitos pelo mesmo editor possam compartilhar o acesso ao conjunto de chaves.

> [!NOTE]
> A `KeychainSecurityGroup` propriedade foi preterida. Use a propriedade `iOSKeychainSecurityGroup`. O `TeamId` prefixo não é necessário quando você usa `iOSKeychainSecurityGroup` .

### <a name="use-microsoft-authenticator"></a>Usar Microsoft Authenticator

Seu aplicativo pode usar Microsoft Authenticator como um agente para habilitar:

- **SSO**: quando você HABILITA o SSO, os usuários não precisam entrar em cada aplicativo.
- **Identificação do dispositivo**: Use a identificação do dispositivo para autenticar acessando o certificado do dispositivo. Esse certificado é criado no dispositivo quando ele é ingressado no local de trabalho. Seu aplicativo estará pronto se os administradores de locatários habilitarem o acesso condicional relacionado aos dispositivos.
- **Verificação da identificação do aplicativo**: quando um aplicativo chama o agente, ele passa sua URL de redirecionamento. O agente verifica a URL de redirecionamento.

Para obter detalhes sobre como habilitar um agente, consulte [usar Microsoft Authenticator ou Microsoft Intune portal da empresa em aplicativos Xamarin Ios e Android](msal-net-use-brokers-with-xamarin-apps.md).

## <a name="known-issues-with-ios-12-and-authentication"></a>Problemas conhecidos com o iOS 12 e a autenticação

A Microsoft lançou um [comunicado de segurança](https://github.com/aspnet/AspNetCore/issues/4647) sobre uma incompatibilidade entre o IOS 12 e alguns tipos de autenticação. A incompatibilidade interrompe as entradas sociais, WSFed e OIDC. A consultoria de segurança ajuda a entender como remover restrições de segurança ASP.NET de seus aplicativos para torná-las compatíveis com o iOS 12.

Ao desenvolver aplicativos MSAL.NET no Xamarin iOS, você poderá ver um loop infinito ao tentar entrar em sites do iOS 12. Esse comportamento é semelhante a esse problema de ADAL no GitHub: [loop infinito ao tentar fazer logon no site do IOS 12 #1329](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/issues/1329).

Você também pode ver uma interrupção em ASP.NET Core autenticação OIDC com o iOS 12 Safari. Para obter mais informações, consulte este [problema de WebKit](https://bugs.webkit.org/show_bug.cgi?id=188165).

## <a name="known-issues-with-ios-13-and-authentication"></a>Problemas conhecidos com o iOS 13 e a autenticação

Se seu aplicativo exigir acesso condicional ou suporte à autenticação de certificado, habilite seu aplicativo para se comunicar com o aplicativo do agente de Microsoft Authenticator. O MSAL é então responsável por manipular solicitações e respostas entre seu aplicativo e Microsoft Authenticator.

No iOS 13, a Apple fez uma alteração de API de interrupção, removendo a capacidade do aplicativo de ler o aplicativo de origem ao receber uma resposta de um aplicativo externo por meio de esquemas de URL personalizados.

Documentação da Apple para Estados [UIApplicationOpenURLOptionsSourceApplicationKeys](https://developer.apple.com/documentation/uikit/uiapplicationopenurloptionssourceapplicationkey?language=objc) :

> *Se a solicitação tiver sido originada de outro aplicativo pertencente à sua equipe, UIKit definirá o valor dessa chave como a ID do aplicativo. Se o identificador de equipe do aplicativo de origem for diferente do identificador de equipe do aplicativo atual, o valor da chave será nil.*

Essa alteração está interrompendo o MSAL porque ele se baseava em `UIApplication.SharedApplication.OpenUrl` verificar a comunicação entre o MSAL e o aplicativo Microsoft Authenticator.

Além disso, no iOS 13, o desenvolvedor precisa fornecer um controlador de apresentação ao usar o `ASWebAuthenticationSession` .

Seu aplicativo será afetado se você estiver criando com o Xcode 11 e usar o agente do iOS ou o `ASWebAuthenticationSession` .

Nesses casos, use [MSAL.NET 4.4.0 +](https://www.nuget.org/packages/Microsoft.Identity.Client/) para habilitar a autenticação bem-sucedida.

### <a name="additional-requirements"></a>Requisitos adicionais

- Ao usar as bibliotecas MSAL mais recentes, verifique se o **Microsoft Authenticator versão 6.3.19 +** está instalado no dispositivo.
- Ao atualizar para MSAL.NET 4.4.0 +, atualize o seu `LSApplicationQueriesSchemes` no arquivo *info. plist* e adicione `msauthv3` :

    ```xml
    <key>LSApplicationQueriesSchemes</key>
    <array>
         <string>msauthv2</string>
         <string>msauthv3</string>
    </array>
    ```

    Adicionar `msauthv3` ao *info. plist* é necessário para detectar a presença do aplicativo Microsoft Authenticator mais recente no dispositivo que dá suporte ao Ios 13.

## <a name="report-an-issue"></a>Relatar um problema

Se você tiver dúvidas ou desejar relatar um problema encontrado no MSAL.NET, abra um problema no repositório [AzureAD/Microsoft-Authentication-library-o-dotnet](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues) no github.

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre as propriedades do Xamarin iOS, consulte o parágrafo [Considerações específicas do IOS](https://github.com/Azure-Samples/active-directory-xamarin-native-v2/tree/master/1-Basic#ios-specific-considerations) do arquivo readme.MD do exemplo a seguir:

Amostra | Plataforma | Descrição
------ | -------- | -----------
[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, Plataforma Universal do Windows (UWP) | Um aplicativo Xamarin Forms simples que mostra como usar o MSAL para autenticar contas pessoais da Microsoft e o Azure AD por meio do ponto de extremidade 2,0 do Azure AD. O aplicativo também mostra como usar o token resultante para acessar Microsoft Graph.

<!--- https://github.com/Azure-Samples/active-directory-xamarin-native-v2/blob/master/ReadmeFiles/Topology.png -->
