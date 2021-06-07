---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in Azure Active Directory SSO para dispositivos iOS, iPadOS e macOS.
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 05bfcc86c72d9eb393da919035ce198948b943f2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559121"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in do SSO corporativo da Microsoft para dispositivos Apple (versão prévia)

>[!IMPORTANT]
> Este recurso [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece logon único (SSO) para contas do Azure Active Directory (Azure AD) no MacOS, Ios e iPadOS em todos os aplicativos que dão suporte ao recurso de [logon único corporativo](https://developer.apple.com/documentation/authenticationservices) da Apple. O plug-in fornece o SSO para aplicativos mesmo antigos dos quais sua empresa pode depender, mas que ainda não dão suporte às bibliotecas de identidades ou aos protocolos mais recentes. A Microsoft trabalhou junto com a Apple para desenvolver esse plug-in a fim de aumentar a usabilidade do seu aplicativo, fornecendo a melhor proteção disponível.

O plug-in de SSO corporativo é atualmente um recurso interno dos seguintes aplicativos:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md): Ios, iPadOS
* [Portal da empresa](/mem/intune/apps/apps-company-portal-macos)de Microsoft Intune: MacOS

## <a name="features"></a>Recursos

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Ele fornece SSO para contas do Azure AD em todos os aplicativos que dão suporte ao recurso de SSO corporativo da Apple.
- Ele pode ser habilitado por qualquer solução de MDM (gerenciamento de dispositivo móvel).
- Ele estende o SSO para aplicativos que ainda não usam bibliotecas da plataforma de identidade da Microsoft.
- Ele estende o SSO para aplicativos que usam OAuth 2, OpenID Connect e SAML.

## <a name="requirements"></a>Requisitos

Para usar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- O dispositivo deve *dar suporte* e ter um aplicativo instalado que tenha o plug-in Microsoft Enterprise SSO para dispositivos Apple:
  - iOS 13,0 e posterior: [Microsoft Authenticator aplicativo](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13,0 e posterior: [Microsoft Authenticator aplicativo](../user-help/user-help-auth-app-overview.md)
  - macOS 10,15 e posterior: [portal da empresa do Intune aplicativo](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- O dispositivo deve ser *registrado no MDM*, por exemplo, por meio de Microsoft Intune.
- A configuração deve ser *enviada por push para o dispositivo* para habilitar o plug-in de SSO corporativo. A Apple requer essa restrição de segurança.

### <a name="ios-requirements"></a>requisitos do iOS:
- o iOS 13,0 ou superior deve estar instalado no dispositivo.
- Um aplicativo da Microsoft que fornece o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalado no dispositivo. Para a visualização pública, esses aplicativos são o [Microsoft Authenticator aplicativo](/azure/active-directory/user-help/user-help-auth-app-overview).


### <a name="macos-requirements"></a>requisitos do macOS:
- o macOS 10,15 ou superior deve estar instalado no dispositivo. 
- Um aplicativo da Microsoft que fornece o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalado no dispositivo. Para a visualização pública, esses aplicativos incluem o [aplicativo portal da empresa do Intune](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp).

## <a name="enable-the-sso-plug-in"></a>Habilitar o plug-in SSO

Use as informações a seguir para habilitar o plug-in SSO usando o MDM.
### <a name="microsoft-intune-configuration"></a>Configuração de Microsoft Intune

Se você usar Microsoft Intune como seu serviço MDM, poderá usar as configurações internas do perfil de configuração para habilitar o plug-in Microsoft Enterprise SSO:

1. Defina as configurações de [extensão do aplicativo SSO](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) de um perfil de configuração. 
1. Se o perfil ainda não tiver sido atribuído, [atribua o perfil a um usuário ou grupo de dispositivos](/mem/intune/configuration/device-profile-assign).

As configurações de perfil que habilitam o plug-in SSO são aplicadas automaticamente aos dispositivos do grupo na próxima vez que cada dispositivo fizer check-in com o Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configuração manual para outros serviços de MDM

Se você não usar o Intune para MDM, use os parâmetros a seguir para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple.

Configurações do iOS:

- **ID da extensão**: `com.microsoft.azureauthenticator.ssoextension`
- **ID da equipe**: esse campo não é necessário para o Ios.

Configurações do macOS:

- **ID da extensão**: `com.microsoft.CompanyPortalMac.ssoextension`
- **ID da equipe**: `UBF8T346G9`

Configurações comuns:

- **Tipo**: redirecionar
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="more-configuration-options"></a>Mais opções de configuração
Você pode adicionar mais opções de configuração para estender a funcionalidade de SSO para outros aplicativos.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Habilitar o SSO para aplicativos que não usam uma biblioteca da plataforma Microsoft Identity

O plug-in de SSO permite que qualquer aplicativo participe do SSO, mesmo que ele não tenha sido desenvolvido usando um SDK da Microsoft como MSAL (biblioteca de autenticação da Microsoft).

O plug-in de SSO é instalado automaticamente por dispositivos que têm:
* Baixado o aplicativo autenticador no iOS ou iPadOS, ou baixado o aplicativo Portal da Empresa do Intune no macOS.
* Registrou seu dispositivo na sua organização. 

Sua organização provavelmente usa o aplicativo autenticador para cenários como autenticação multifator (MFA), autenticação com senha e acesso condicional. Usando um provedor de MDM, você pode ativar o plug-in de SSO para seus aplicativos. A Microsoft facilitou a configuração do plug-in no Microsoft Endpoint Manager no Intune. Uma permissão é usada para configurar esses aplicativos para usar o plug-in de SSO.

>[!IMPORTANT]
> O plug-in Microsoft Enterprise SSO só dá suporte a aplicativos que usam tecnologias de rede da Apple ou exibições da empresa. Ele não dá suporte a aplicativos que enviam sua própria implementação de camada de rede.  

Use os parâmetros a seguir para configurar o plug-in Microsoft Enterprise SSO para aplicativos que não usam uma biblioteca da plataforma Microsoft Identity.

Para fornecer uma lista de aplicativos específicos, use estes parâmetros:

- **Chave**: `AppAllowList`
- **Tipo**: `String`
- **Valor**: lista delimitada por vírgulas de IDs de pacote de aplicativo para os aplicativos que têm permissão para participar do SSO.
- **Exemplo**: `com.contoso.workapp, com.contoso.travelapp`

Para fornecer uma lista de prefixos, use estes parâmetros:
- **Chave**: `AppPrefixAllowList`
- **Tipo**: `String`
- **Valor**: lista delimitada por vírgulas de prefixos de ID de pacote de aplicativo para os aplicativos que têm permissão para participar do SSO. Esse parâmetro permite que todos os aplicativos que começam com um prefixo específico participem do SSO.
- **Exemplo**: `com.contoso., com.fabrikam.`

Os [aplicativos consentidos](./application-consent-experience.md) que o administrador de MDM permite participar no SSO podem obter um token para o usuário final silenciosamente. Então, adicione somente aplicativos confiáveis àlist de permissão. 

>[!NOTE]
> Você não precisa adicionar aplicativos que usam MSAL ou ASWebAuthenticationSession à lista de aplicativos que podem participar do SSO. Esses aplicativos são habilitados por padrão. 

##### <a name="find-app-bundle-identifiers-on-ios-devices"></a>Localizar identificadores de pacote de aplicativo em dispositivos iOS

A Apple não fornece uma maneira fácil de obter IDs de pacote da loja de aplicativos. A maneira mais fácil de obter as IDs de pacote dos aplicativos que você deseja usar para o SSO é pedir ao seu fornecedor ou desenvolvedor de aplicativos. Se essa opção não estiver disponível, você poderá usar a configuração do MDM para localizar as IDs do pacote: 

1. Habilite temporariamente o seguinte sinalizador em sua configuração de MDM:

    - **Chave**: `admin_debug_mode_enabled`
    - **Tipo**: `Integer`
    - **Valor**: 1 ou 0
1. Quando esse sinalizador estiver ativado, entre em aplicativos iOS no dispositivo para o qual você deseja saber a ID do pacote. 
1. No aplicativo autenticador, selecione **ajuda**  >  **enviar logs**  >  **Exibir logs**. 
1. No arquivo de log, procure a seguinte linha: `[ADMIN MODE] SSO extension has captured following app bundle identifiers` . Essa linha deve capturar todas as IDs de pacote de aplicativo que são visíveis para a extensão de SSO. 

Use as IDs de pacote para configurar o SSO para os aplicativos. 

#### <a name="allow-users-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Permitir que os usuários se conectem de aplicativos desconhecidos e do navegador Safari

Por padrão, o plug-in Microsoft Enterprise SSO fornece SSO para aplicativos autorizados somente quando um usuário tiver entrado por meio de um aplicativo que usa uma biblioteca da plataforma Microsoft Identity como MSAL ou ADAL (biblioteca de autenticação Azure Active Directory). O plug-in Microsoft Enterprise SSO também pode adquirir uma credencial compartilhada quando é chamado por outro aplicativo que usa uma biblioteca da plataforma Microsoft Identity durante uma nova aquisição de token.

Quando você habilita o `browser_sso_interaction_enabled` sinalizador, os aplicativos que não usam uma biblioteca da plataforma de identidade da Microsoft podem fazer a inicialização inicial e obter uma credencial compartilhada. O navegador Safari também pode fazer a inicialização inicial e obter uma credencial compartilhada. 

Se o plug-in Microsoft Enterprise SSO ainda não tiver uma credencial compartilhada, ele tentará obter uma sempre que uma entrada for solicitada de uma URL do Azure AD dentro do navegador Safari, ASWebAuthenticationSession, SafariViewController ou outro aplicativo nativo permitido. 

Use estes parâmetros para habilitar o sinalizador: 

- **Chave**: `browser_sso_interaction_enabled`
- **Tipo**: `Integer`
- **Valor**: 1 ou 0

o macOS requer essa configuração para que possa fornecer uma experiência consistente em todos os aplicativos. o iOS e o iPadOS não exigem essa configuração porque a maioria dos aplicativos usa o aplicativo autenticador para entrar. Mas recomendamos que você habilite essa configuração porque, se alguns de seus aplicativos não usarem o aplicativo autenticador no iOS ou iPadOS, esse sinalizador melhorará a experiência. A configuração é desabilitada por padrão.

#### <a name="disable-asking-for-mfa-during-initial-bootstrapping"></a>Desabilitar a solicitação de MFA durante a inicialização inicial

Por padrão, o plug-in do SSO corporativo da Microsoft sempre solicita ao usuário a MFA durante a inicialização inicial e, ao mesmo tempo, obtém uma credencial compartilhada. O usuário será solicitado para a MFA, mesmo que não seja necessário para o aplicativo que o usuário abriu. Esse comportamento permite que a credencial compartilhada seja facilmente usada em todos os outros aplicativos sem a necessidade de solicitar o usuário se a MFA for necessária posteriormente. Como o usuário obtém menos prompts em geral, essa configuração é geralmente uma boa decisão.

Habilitar `browser_sso_disable_mfa` desativa o MFA durante a inicialização inicial e, ao mesmo tempo, obtém a credencial compartilhada. Nesse caso, o usuário é solicitado somente quando a MFA é exigida por um aplicativo ou recurso. 

Para habilitar o sinalizador, use estes parâmetros:

- **Chave**: `browser_sso_disable_mfa`
- **Tipo**: `Integer`
- **Valor**: 1 ou 0

É recomendável manter esse sinalizador desabilitado porque ele reduz o número de vezes que o usuário é solicitado a entrar. Se sua organização raramente usa a MFA, talvez você queira habilitar o sinalizador. Mas é recomendável usar a MFA com mais frequência. Por esse motivo, o sinalizador é desabilitado por padrão.

#### <a name="disable-oauth-2-application-prompts"></a>Desabilitar prompts do aplicativo OAuth 2

O plug-in Microsoft Enterprise SSO fornece o SSO acrescentando credenciais compartilhadas a solicitações de rede provenientes de aplicativos permitidos. No entanto, alguns aplicativos OAuth 2 podem impor incorretamente prompts do usuário final na camada de protocolo. Se você vir esse problema, você também verá que as credenciais compartilhadas são ignoradas para esses aplicativos. Seu usuário é solicitado a entrar mesmo que o plug-in Microsoft Enterprise SSO funcione para outros aplicativos.  

Habilitar o `disable_explicit_app_prompt` sinalizador restringe a capacidade de aplicativos nativos e aplicativos Web de forçar um prompt do usuário final na camada de protocolo e ignorar o SSO. Para habilitar o sinalizador, use estes parâmetros:

- **Chave**: `disable_explicit_app_prompt`
- **Tipo**: `Integer`
- **Valor**: 1 ou 0

É recomendável habilitar esse sinalizador para obter uma experiência consistente em todos os aplicativos. Isso está desabilitado por padrão. 

#### <a name="enable-sso-through-cookies-for-a-specific-application"></a>Habilitar o SSO por meio de cookies para um aplicativo específico

Alguns aplicativos podem ser incompatíveis com a extensão SSO. Especificamente, os aplicativos que têm configurações de rede avançadas podem apresentar problemas inesperados quando estão habilitados para SSO. Por exemplo, você pode ver um erro indicando que a solicitação de rede foi cancelada ou interrompida. 

Se você tiver problemas para entrar usando o método descrito na seção [aplicativos que não usam MSAL](#applications-that-dont-use-msal) , tente uma configuração alternativa. Use estes parâmetros para configurar o plug-in:

- **Chave**: `AppCookieSSOAllowList`
- **Tipo**: `String`
- **Valor**: lista delimitada por vírgulas de prefixos de ID de pacote de aplicativo para os aplicativos que têm permissão para participar do SSO. Todos os aplicativos que começam com os prefixos listados terão permissão para participar do SSO.
- **Exemplo**: `com.contoso.myapp1, com.fabrikam.myapp2`

Os aplicativos habilitados para o SSO usando essa configuração precisam ser adicionados ao `AppCookieSSOAllowList` e ao `AppPrefixAllowList` .

Tente essa configuração somente para aplicativos que tenham falhas de conexão inesperadas. 

#### <a name="use-intune-for-simplified-configuration"></a>Usar o Intune para configuração simplificada

Você pode usar o Intune como seu serviço de MDM para facilitar a configuração do plug-in do SSO corporativo da Microsoft. Por exemplo, você pode usar o Intune para habilitar o plug-in e adicionar aplicativos antigos a uma permissão para que eles obtenham SSO. 

Para obter mais informações, consulte a [documentação de configuração do Intune](/intune/configuration/ios-device-features-settings).

## <a name="use-the-sso-plug-in-in-your-application"></a>Usar o plug-in de SSO em seu aplicativo

O [MSAL para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versões 1.1.0 e posteriores dá suporte ao plug-in Microsoft Enterprise SSO para dispositivos Apple. É a maneira recomendada para adicionar suporte para o plug-in Microsoft Enterprise SSO. Ele garante que você obtenha os recursos completos da plataforma Microsoft Identity.

Se você estiver criando um aplicativo para cenários frente-Worker, consulte [modo de dispositivo compartilhado para dispositivos IOS](msal-ios-shared-devices.md) para obter informações de configuração.

## <a name="understand-how-the-sso-plug-in-works"></a>Entender como o plug-in de SSO funciona

O plug-in do SSO corporativo da Microsoft depende da [estrutura de SSO do Apple Enterprise](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Provedores de identidade que ingressam na estrutura podem interceptar o tráfego de rede para seus domínios e aprimorar ou alterar a forma como essas solicitações são tratadas. Por exemplo, o plug-in de SSO pode mostrar mais UIs para coletar credenciais do usuário final com segurança, exigir MFA ou fornecer tokens para o aplicativo de forma silenciosa.

Os aplicativos nativos também podem implementar operações personalizadas e se comunicar diretamente com o plug-in de SSO. Para obter mais informações, consulte este [vídeo de 2019 de conferências de desenvolvedores mundiais da Apple](https://developer.apple.com/videos/play/tech-talks/301/).

### <a name="applications-that-use-msal"></a>Aplicativos que usam MSAL

O [MSAL para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versões 1.1.0 e posteriores dá suporte ao plug-in Microsoft Enterprise SSO para dispositivos Apple nativamente para contas corporativas e de estudante. 

Você não precisará de nenhuma configuração especial se tiver seguido [todas as etapas recomendadas](./quickstart-v2-ios.md) e usado o [formato de URI de redirecionamento](./redirect-uris-ios.md)padrão. Em dispositivos que têm o plug-in SSO, o MSAL automaticamente o invoca para todas as solicitações de tokens interativas e silenciosas. Ele também o invoca para operações de enumeração de conta e de remoção de conta. Como o MSAL implementa um protocolo de plug-in SSO nativo que depende de operações personalizadas, essa configuração fornece a experiência nativa mais suave para o usuário final. 

Se o plug-in de SSO não estiver habilitado pelo MDM, mas o aplicativo Microsoft Authenticator estiver presente no dispositivo, MSAL usará o aplicativo autenticador para solicitações de token interativas. O plug-in de SSO compartilha o SSO com o aplicativo autenticador.

### <a name="applications-that-dont-use-msal"></a>Aplicativos que não usam MSAL

Os aplicativos que não usam uma biblioteca de plataforma de identidade da Microsoft, como MSAL, ainda poderão obter o SSO se um administrador adicionar esses aplicativos àlist de permissão. 

Você não precisa alterar o código nesses aplicativos, contanto que as seguintes condições sejam satisfeitas:

- O aplicativo usa o Apple frameworks para executar solicitações de rede. Essas estruturas incluem [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview) e [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession), por exemplo. 
- O aplicativo usa protocolos padrão para se comunicar com o Azure AD. Esses protocolos incluem, por exemplo, OAuth 2, SAML e WS-Federation.
- O aplicativo não coleta nomes de usuário e senhas de texto não criptografado na interface nativa.

Nesse caso, o SSO é fornecido quando o aplicativo cria uma solicitação de rede e abre um navegador da Web para conectar o usuário. Quando um usuário é redirecionado para uma URL de entrada do Azure AD, o plug-in de SSO valida a URL e verifica se há uma credencial SSO para essa URL. Se encontrar a credencial, o plug-in de SSO a transmitirá ao Azure AD, o que autorizará o aplicativo a concluir a solicitação de rede sem solicitar que o usuário insira as credenciais. Além disso, se o dispositivo for conhecido pelo Azure AD, o plug-in de SSO passará o certificado do dispositivo para atender à verificação de acesso condicional com base no dispositivo. 

Para dar suporte a SSO para aplicativos não MSAL, o plug-in de SSO implementa um protocolo semelhante ao plug-in de navegador do Windows descrito em [o que é um token de atualização primário?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Em comparação com aplicativos baseados em MSAL, o plug-in de SSO atua de forma mais transparente para aplicativos não MSAL. Ele se integra à experiência de entrada existente do navegador que os aplicativos fornecem. 

O usuário final vê a experiência familiar e não precisa entrar novamente em cada aplicativo. Por exemplo, em vez de exibir o seletor de conta nativa, o plug-in de SSO adiciona sessões de SSO à experiência do seletor de conta baseada na Web. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [modo de dispositivo compartilhado para dispositivos IOS](msal-ios-shared-devices.md).
