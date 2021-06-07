---
title: Modo de dispositivo compartilhado para dispositivos iOS
titleSuffix: Microsoft identity platform | Azure
description: Saiba como habilitar o modo de dispositivo compartilhado para permitir que os trabalhadores do frente compartilhem um dispositivo iOS
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 3353d87291492563acbd3a85bbae266c711377f2
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611796"
---
# <a name="shared-device-mode-for-ios-devices"></a>Modo de dispositivo compartilhado para dispositivos iOS

>[!IMPORTANT]
> Este recurso [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Os operadores de frente como Retail Associates, membros da equipe de voo e funcionários de serviço de campo geralmente usam um dispositivo móvel compartilhado para realizar seu trabalho. Esses dispositivos compartilhados podem apresentar riscos de segurança se os usuários compartilharem suas senhas ou PINs, intencionalmente ou não, para acessar dados de clientes e de negócios no dispositivo compartilhado.

O modo de dispositivo compartilhado permite que você configure um dispositivo iOS 13 ou superior para ser compartilhado de forma mais fácil e segura pelos funcionários. Os funcionários podem entrar e acessar as informações do cliente rapidamente. Quando eles forem concluídos com sua mudança ou tarefa, eles poderão sair do dispositivo e ficarão imediatamente prontos para uso pelo próximo funcionário.

O modo de dispositivo compartilhado também fornece gerenciamento com suporte de identidade da Microsoft do dispositivo.

Esse recurso usa o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) para gerenciar os usuários no dispositivo e para distribuir o [plug-in do SSO corporativo da Microsoft para dispositivos Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Criar um aplicativo de modo de dispositivo compartilhado

Para criar um aplicativo de modo de dispositivo compartilhado, os desenvolvedores e os administradores de dispositivo de nuvem funcionam juntos:

1. **Os desenvolvedores de aplicativos** gravam um aplicativo de conta única (aplicativos de várias contas não têm suporte no modo de dispositivo compartilhado) e gravam o código para lidar com coisas como a saída do dispositivo compartilhado.

1. **Os administradores de dispositivo** preparam o dispositivo a ser compartilhado usando um provedor de MDM (gerenciamento de dispositivo móvel) como Microsoft Intune para gerenciar os dispositivos em sua organização. O MDM envia por push o aplicativo Microsoft Authenticator para os dispositivos e ativa o "modo compartilhado" para cada dispositivo por meio de uma atualização de perfil para o dispositivo. Essa configuração de modo compartilhado é o que altera o comportamento dos aplicativos com suporte no dispositivo. Essa configuração do provedor de MDM define o modo de dispositivo compartilhado para o dispositivo e habilita o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) que é necessário para o modo de dispositivo compartilhado.

1. [**Necessário somente durante a visualização pública**] Um usuário com a função de [administrador de dispositivo de nuvem](../roles/permissions-reference.md#cloud-device-administrator) deve iniciar o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) e ingressar seu dispositivo na organização.

    Para configurar a associação de suas funções organizacionais no portal do Azure: **Azure Active Directory**  >  **funções e administradores**  >  **administrador do dispositivo de nuvem**

As seções a seguir ajudam a atualizar seu aplicativo para dar suporte ao modo de dispositivo compartilhado.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Usar o Intune para habilitar o modo de dispositivo compartilhado & extensão SSO

> [!NOTE]
> A etapa a seguir é necessária somente durante a visualização pública.

Seu dispositivo precisa ser configurado para dar suporte ao modo de dispositivo compartilhado. Ele deve ter o iOS 13 + instalado e ser registrado em MDM. A configuração do MDM também precisa habilitar [o plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md). Para saber mais sobre extensões de SSO, consulte o [vídeo da Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. No portal de configuração do Intune, diga ao dispositivo para habilitar o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) com a seguinte configuração:

    - **Tipo**: redirecionar
    - **ID da extensão**: com. Microsoft. azureauthenticator. ssoextension
    - **ID da equipe**: (este campo não é necessário para o IOS)
    - **URLs**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **Dados adicionais a serem configurados**:
      - Chave: sharedDeviceMode
      - Tipo: booliano
      - Valor: true

    Para obter mais informações sobre como configurar o com o Intune, consulte a [documentação de configuração do Intune](/intune/configuration/ios-device-features-settings).

1. Em seguida, configure o MDM para enviar por push o aplicativo Microsoft Authenticator para seu dispositivo por meio de um perfil MDM.

    Defina as seguintes opções de configuração para ativar o modo de dispositivo compartilhado:

    - Configuração 1:
      - Chave: sharedDeviceMode
      - Tipo: booliano
      - Valor: true

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modificar seu aplicativo iOS para dar suporte ao modo de dispositivo compartilhado

Os usuários dependem de você para garantir que seus dados não sejam vazados para outro usuário. As seções a seguir fornecem sinais úteis para indicar ao aplicativo que uma alteração ocorreu e deve ser tratada.

Você é responsável por verificar o estado do usuário no dispositivo toda vez que seu aplicativo é usado e, em seguida, limpar os dados do usuário anterior. Isso inclui se ele é recarregado do plano de fundo em várias tarefas.

Em uma alteração de usuário, você deve garantir que os dados do usuário anterior sejam apagados e que todos os dados armazenados em cache que estão sendo exibidos em seu aplicativo sejam removidos. É altamente recomendável que você e sua empresa realizem um processo de revisão de segurança depois de atualizar seu aplicativo para dar suporte ao modo de dispositivo compartilhado.

### <a name="detect-shared-device-mode"></a>Detectar o modo de dispositivo compartilhado

Detectar o modo de dispositivo compartilhado é importante para seu aplicativo. Muitos aplicativos precisarão de uma alteração na experiência do usuário (UX) quando o aplicativo for usado em um dispositivo compartilhado. Por exemplo, seu aplicativo pode ter um recurso de "inscrição", que não é apropriado para um trabalho frente porque eles provavelmente já têm uma conta. Talvez você também queira adicionar segurança extra à manipulação de dados do seu aplicativo se ele estiver no modo de dispositivo compartilhado.

Use a `getDeviceInformationWithParameters:completionBlock:` API no `MSALPublicClientApplication` para determinar se um aplicativo está sendo executado em um dispositivo no modo de dispositivo compartilhado.

Os trechos de código a seguir mostram exemplos de como usar a `getDeviceInformationWithParameters:completionBlock:` API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Obter o usuário conectado e determinar se um usuário foi alterado no dispositivo

Outra parte importante do suporte ao modo de dispositivo compartilhado é determinar o estado do usuário no dispositivo e limpar os dados do aplicativo se um usuário tiver mudado ou se não houver nenhum usuário no dispositivo. Você é responsável por garantir que os dados não sejam vazados para outro usuário.

Você pode usar a `getCurrentAccountWithParameters:completionBlock:` API para consultar a conta conectada no momento no dispositivo.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Conectar um usuário globalmente

Quando um dispositivo é configurado como um dispositivo compartilhado, seu aplicativo pode chamar a `acquireTokenWithParameters:completionBlock:` API para entrar na conta. A conta estará disponível globalmente para todos os aplicativos qualificados no dispositivo depois que o primeiro aplicativo entrar na conta.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Desconectar um usuário globalmente

O código a seguir remove a conta conectada e limpa os tokens em cache não apenas do aplicativo, mas também do dispositivo que está no modo de dispositivo compartilhado. No entanto, ele não limpa os *dados* do seu aplicativo. Você deve limpar os dados do seu aplicativo, bem como limpar os dados armazenados em cache que seu aplicativo pode exibir para o usuário.

#### <a name="clear-browser-state"></a>Limpar estado do navegador

> [!NOTE]
> A etapa a seguir é necessária somente durante a visualização pública.

Nesta versão de visualização pública, o [plug-in Microsoft Enterprise SSO para dispositivos Apple](apple-sso-plugin.md) limpa o estado somente para aplicativos. Ele não limpa o estado no navegador Safari. Recomendamos que você limpe manualmente a sessão do navegador para garantir que nenhum rastreamento do estado do usuário seja deixado para trás. Você pode usar a `signoutFromBrowser` propriedade opcional mostrada abaixo para limpar todos os cookies. Isso fará com que o navegador seja inicializado brevemente no dispositivo.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Próximas etapas

Para ver o modo de dispositivo compartilhado em ação, o exemplo de código a seguir no GitHub inclui um exemplo de execução de um aplicativo de trabalho do frente em um dispositivo iOS no modo de dispositivo compartilhado:

[Exemplo de API de Microsoft Graph MSAL do iOS Swift](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
