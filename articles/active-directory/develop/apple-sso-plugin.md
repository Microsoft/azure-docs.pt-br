---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in de SSO Azure Active Directory da Microsoft para dispositivos iOS, iPadOS e macOS.
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
ms.openlocfilehash: 96fbf23128896f23beee70a6b3d32b4b87a454ea
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102427089"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in do SSO corporativo da Microsoft para dispositivos Apple (versão prévia)

>[!IMPORTANT]
> Este recurso [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece logon único (SSO) para contas do Azure Active Directory (Azure AD) no MacOS, Ios e iPadOS em todos os aplicativos que dão suporte ao recurso de [logon único corporativo](https://developer.apple.com/documentation/authenticationservices) da Apple. Isso inclui aplicativos mais antigos dos quais sua empresa pode depender, mas que ainda não dão suporte às bibliotecas de identidades ou protocolos mais recentes. A Microsoft trabalhou junto com a Apple para desenvolver esse plug-in a fim de aumentar a usabilidade do seu aplicativo e, ao mesmo tempo, fornecer a melhor proteção que a Apple e a Microsoft podem fornecer.

O plug-in Enterprise SSO está disponível atualmente como um recurso interno dos seguintes aplicativos:

* [Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) -Ios, iPadOS
* Microsoft Intune [portal da empresa](/mem/intune/apps/apps-company-portal-macos) -MacOS

## <a name="features"></a>Recursos

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece SSO para contas do Azure AD em todos os aplicativos que dão suporte ao recurso Enterprise Single Sign-On da Apple.
- Pode ser habilitado por qualquer solução de MDM (gerenciamento de dispositivo móvel).
- Estende o SSO para aplicativos que ainda não usam as bibliotecas da plataforma de identidade da Microsoft.
- Estende o SSO para aplicativos que usam OAuth2, OpenID Connect e SAML.

## <a name="requirements"></a>Requisitos

Para usar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- O dispositivo deve **dar suporte** e ter um aplicativo que inclui o plug-in Microsoft Enterprise SSO para dispositivos Apple **instalados**:
  - iOS 13.0 +: [Microsoft Authenticator aplicativo](../user-help/user-help-auth-app-overview.md)
  - iPadOS 13.0 +: [Microsoft Authenticator aplicativo](../user-help/user-help-auth-app-overview.md)
  - macOS 10.15 +: [portal da empresa do Intune aplicativo](/mem/intune/user-help/enroll-your-device-in-intune-macos-cp)
- O dispositivo deve ser **registrado no MDM** (por exemplo, com Microsoft Intune).
- A configuração deve ser **enviada por push para o dispositivo** para habilitar o plug-in de SSO corporativo no dispositivo. Essa restrição de segurança é exigida pela Apple.

### <a name="ios-requirements"></a>requisitos do iOS:
- o iOS 13,0 ou superior deve estar instalado no dispositivo.
- Um aplicativo da Microsoft que fornece o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalado no dispositivo. Para a visualização pública, esses aplicativos são o [Microsoft Authenticator aplicativo](/intune/user-help/user-help-auth-app-overview.md).


### <a name="macos-requirements"></a>requisitos do macOS:
- o macOS 10,15 ou superior deve estar instalado no dispositivo. 
- Um aplicativo da Microsoft que fornece o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalado no dispositivo. Para a visualização pública, esses aplicativos incluem o [aplicativo portal da empresa do Intune](/intune/user-help/enroll-your-device-in-intune-macos-cp.md).

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Habilitar o plug-in SSO com o MDM (gerenciamento de dispositivo móvel)

### <a name="microsoft-intune-configuration"></a>Configuração de Microsoft Intune

Se você usar Microsoft Intune como seu serviço MDM, poderá usar as configurações internas do perfil de configuração para habilitar o plug-in Microsoft Enterprise SSO.

Primeiro, defina as configurações de [extensão do aplicativo de logon único](/mem/intune/configuration/device-features-configure#single-sign-on-app-extension) de um perfil de configuração e [atribua o perfil a um usuário ou grupo de dispositivos](/mem/intune/configuration/device-profile-assign) (se ainda não tiver sido atribuído).

As configurações de perfil que habilitam o plug-in SSO são aplicadas automaticamente aos dispositivos do grupo na próxima vez que cada dispositivo fizer check-in com o Intune.

### <a name="manual-configuration-for-other-mdm-services"></a>Configuração manual para outros serviços de MDM

Se você não estiver usando Microsoft Intune para o gerenciamento de dispositivo móvel, use os parâmetros a seguir para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple.

#### <a name="ios-settings"></a>configurações do iOS:

- **ID da extensão**: `com.microsoft.azureauthenticator.ssoextension`
- **ID da equipe**: (este campo não é necessário para o IOS)

#### <a name="macos-settings"></a>configurações do macOS:

- **ID da extensão**: `com.microsoft.CompanyPortalMac.ssoextension`
- **ID da equipe**: `UBF8T346G9`

#### <a name="common-settings"></a>Configurações comuns:

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
  
### <a name="additional-configuration-options"></a> Opções de configuração adicionais
Opções de configuração adicionais podem ser adicionadas para estender a funcionalidade de SSO para aplicativos adicionais.

#### <a name="enable-sso-for-apps-that-dont-use-a-microsoft-identity-platform-library"></a>Habilitar o SSO para aplicativos que não usam uma biblioteca da plataforma Microsoft Identity

O plug-in de SSO permite que qualquer aplicativo participe de logon único mesmo que não tenha sido desenvolvido usando um SDK da Microsoft como a MSAL (biblioteca de autenticação da Microsoft).

O plug-in de SSO é instalado automaticamente por dispositivos que baixaram o aplicativo Microsoft Authenticator no iOS e iPadOS ou Portal da Empresa do Intune aplicativo no macOS e registrou seu dispositivo na sua organização. Sua organização provavelmente usa o aplicativo autenticador hoje para cenários como autenticação multifator, autenticação sem senha e acesso condicional. Ele pode ser ativado para seus aplicativos usando qualquer provedor de MDM, embora a Microsoft tenha tornado fácil de configurar dentro do Microsoft Endpoint Manager do Intune. Uma lista de permissões é usada para configurar esses aplicativos para usar o plug-in de SSO.

>[!IMPORTANT]
> Somente os aplicativos que usam tecnologias de rede da Apple nativas ou webviews têm suporte. Se um aplicativo envia sua própria implementação de camada de rede, não há suporte para o plug-in Microsoft Enterprise SSO.  

Use os parâmetros a seguir para configurar o plug-in Microsoft Enterprise SSO para aplicativos que não usam uma biblioteca da plataforma Microsoft Identity:

Se você quiser fornecer uma lista de aplicativos específicos:

- **Chave**: `AppAllowList`
- **Tipo**: `String`
- **Valor**: lista delimitada por vírgulas de IDs de pacote de aplicativo para os aplicativos que têm permissão para participar do SSO
- **Exemplo**: `com.contoso.workapp, com.contoso.travelapp`

Ou, se você quiser fornecer uma lista de prefixos:
- **Chave**: `AppPrefixAllowList`
- **Tipo**: `String`
- **Valor**: lista delimitada por vírgulas de prefixos de ID de pacote de aplicativo para os aplicativos que têm permissão para participar do SSO. Observe que isso permitirá que todos os aplicativos começando com um prefixo específico participem do SSO
- **Exemplo**: `com.contoso., com.fabrikam.`

Os [aplicativos consentidos](./application-consent-experience.md) que são permitidos pelo administrador de MDM para participar do SSO podem obter silenciosamente um token para o usuário final. Portanto, é importante adicionar apenas aplicativos confiáveis à lista de permissões. 

>[!NOTE]
> Você não precisa adicionar aplicativos que usam MSAL ou ASWebAuthenticationSession à lista. Esses aplicativos são habilitados por padrão. 

##### <a name="how-to-discover-app-bundle-identifiers-on-ios-devices"></a>Como descobrir identificadores de pacote de aplicativo em dispositivos iOS

A Apple não fornece uma maneira fácil de descobrir IDs de pacote na App Store. A maneira mais fácil de descobrir as IDs de pacote dos aplicativos que desejam usar para o SSO é pedir ao seu fornecedor ou desenvolvedor de aplicativos. Se essa opção não estiver disponível, você poderá usar a configuração do MDM para descobrir as IDs do pacote. 

Habilite temporariamente o sinalizador a seguir em sua configuração de MDM:

- **Chave**: `admin_debug_mode_enabled`
- **Tipo**: `Integer`
- **Valor**: 1 ou 0

Quando esse sinalizador está na entrada para aplicativos iOS no dispositivo, você deseja saber a ID do pacote. Em seguida, abra Microsoft Authenticator app-> ajuda-> enviar logs-> exibir logs. 

No arquivo de log, procure a seguinte linha:

`[ADMIN MODE] SSO extension has captured following app bundle identifiers:`

Isso deve capturar todos os identificadores de pacote de aplicativo visíveis para a extensão de SSO. Você pode usar esses identificadores para configurar o SSO para esses aplicativos. 

#### <a name="allow-user-to-sign-in-from-unknown-applications-and-the-safari-browser"></a>Permitir que o usuário entre aplicativos desconhecidos e o navegador Safari.

Por padrão, o plug-in Microsoft Enterprise SSO fornece SSO para aplicativos autorizados somente quando um usuário tiver entrado por meio de um aplicativo que usa uma biblioteca da plataforma Microsoft Identity como ADAL ou MSAL. O plug-in Microsoft Enterprise SSO também pode adquirir uma credencial compartilhada quando ela é chamada por outro aplicativo que usa uma biblioteca da plataforma Microsoft Identity durante uma nova aquisição de token.

Habilitar `browser_sso_interaction_enabled` sinalizador habilita o aplicativo que não usa uma biblioteca da plataforma Microsoft Identity para fazer a inicialização inicial e obter uma credencial compartilhada. Ele também permite que o navegador Safari faça a inicialização inicial e obtenha uma credencial compartilhada. Se o plug-in Microsoft Enterprise SSO ainda não tiver uma credencial compartilhada, ele tentará obter uma sempre que uma entrada for solicitada de uma URL do Azure AD dentro do Safari Browser, ASWebAuthenticationSession, SafariViewController ou outro aplicativo nativo permitido.  

- **Chave**: `browser_sso_interaction_enabled`
- **Tipo**: `Integer`
- **Valor**: 1 ou 0

Para o macOS, essa configuração é necessária para obter uma experiência mais consistente em todos os aplicativos. Para iOS e iPadOS, essa configuração não é necessária, pois a maioria dos aplicativos usa o aplicativo Microsoft Authenticator para entrar. No entanto, se você tiver alguns aplicativos que não usam o Microsoft Authenticator no iOS ou iPadOS, esse sinalizador melhorará a experiência para que seja recomendável habilitar a configuração. Isso está desabilitado por padrão.

#### <a name="disable-asking-for-mfa-on-initial-bootstrapping"></a>Desabilitar a solicitação de MFA na inicialização inicial

Por padrão, o plug-in do SSO corporativo da Microsoft sempre solicita o usuário para a autenticação multifator (MFA) ao fazer a inicialização inicial e obter uma credencial compartilhada, mesmo que ela não seja necessária para o aplicativo atual que o usuário iniciou. Isso é para que as credenciais compartilhadas possam ser facilmente usadas em todos os aplicativos adicionais sem avisar o usuário se a MFA for necessária posteriormente. Isso reduz os tempos em que o usuário precisa ser solicitado no dispositivo e geralmente é uma boa decisão.

Habilitar `browser_sso_disable_mfa` desativa isso e solicitará ao usuário apenas quando a MFA for exigida por um aplicativo ou recurso. 

- **Chave**: `browser_sso_disable_mfa`
- **Tipo**: `Integer`
- **Valor**: 1 ou 0

É recomendável manter esse sinalizador desabilitado, pois ele reduz os horários em que o usuário precisa ser solicitado no dispositivo. Se sua organização raramente usa a MFA, talvez você queira habilitar o sinalizador, mas recomendamos usar a MFA com mais frequência. Por esse motivo, ele está desabilitado por padrão.

#### <a name="disable-oauth2-application-prompts"></a>Desabilitar prompts do aplicativo OAuth2

O plug-in Microsoft Enterprise SSO fornece o SSO acrescentando credenciais compartilhadas a solicitações de rede provenientes de aplicativos permitidos. No entanto, alguns aplicativos OAuth2s podem impor incorretamente prompts do usuário final na camada de protocolo. Se isso estiver acontecendo, você verá que as credenciais compartilhadas são ignoradas para esses aplicativos e seu usuário é solicitado a entrar mesmo que o plug-in Microsoft Enterprise SSO esteja funcionando para outros aplicativos.  

Habilitar `disable_explicit_app_prompt` o sinalizador restringe a capacidade de aplicativos nativos e Web de forçar um prompt do usuário final na camada de protocolo e ignorar o SSO.

- **Chave**: `disable_explicit_app_prompt`
- **Tipo**: `Integer`
- **Valor**: 1 ou 0

É recomendável habilitar esse sinalizador para obter uma experiência mais consistente em todos os aplicativos. Isso está desabilitado por padrão. 

#### <a name="enable-sso-through-cookies-for-specific-application"></a>Habilitar o SSO por meio de cookies para um aplicativo específico

Um pequeno número de aplicativos pode ser incompatível com a extensão SSO. Especificamente, os aplicativos que têm configurações de rede avançadas podem apresentar problemas inesperados quando estão habilitados para o SSO (por exemplo, você pode ver um erro de que a solicitação de rede foi cancelada ou interrompida). 

Se você estiver tendo problemas para entrar usando o método descrito na `Enable SSO for apps that don't use MSAL` seção, poderá tentar a configuração alternativa para esses aplicativos. 

Use os parâmetros a seguir para configurar o plug-in Microsoft Enterprise SSO para esses aplicativos específicos:

- **Chave**: `AppCookieSSOAllowList`
- **Tipo**: `String`
- **Valor**: lista delimitada por vírgulas de prefixos de ID de pacote de aplicativo para os aplicativos que têm permissão para participar do SSO. Observe que isso permitirá que todos os aplicativos começando com um prefixo específico participem do SSO
- **Exemplo**: `com.contoso.myapp1, com.fabrikam.myapp2`

Observe que os aplicativos habilitados para o SSO usando esse mecanismo precisam ser adicionados ao `AppCookieSSOAllowList` e ao `AppPrefixAllowList` .

Recomendamos que você tente esta opção somente para aplicativos que apresentam falhas de entrada inesperadas. 

#### <a name="use-intune-for-simplified-configuration"></a>Usar o Intune para configuração simplificada

Conforme mencionado anteriormente, você pode usar Microsoft Intune como seu serviço de MDM para facilitar a configuração do plug-in do SSO corporativo da Microsoft, incluindo a habilitação do plug-in e a adição de seus aplicativos mais antigos a uma lista de permissões para que eles obtenham SSO. Para obter mais informações, consulte a [documentação de configuração do Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Usando o plug-in de SSO em seu aplicativo

A [MSAL (biblioteca de autenticação da Microsoft) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e superior dá suporte ao plug-in Microsoft Enterprise SSO para dispositivos Apple. É a maneira recomendada de adicionar suporte para o plug-in Microsoft Enterprise SSO e garante que você obtenha os recursos completos da plataforma Microsoft Identity.

Se você estiver criando um aplicativo para cenários de trabalho do frente, consulte [modo de dispositivo compartilhado para dispositivos IOS](msal-ios-shared-devices.md) para a configuração adicional do recurso.

## <a name="how-the-sso-plug-in-works"></a>Como funciona o plug-in de SSO

O plug-in Microsoft Enterprise SSO conta com a [estrutura de Sign-On empresarial da Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Provedores de identidade que integram a estrutura podem interceptar o tráfego de rede para seus domínios e aprimorar ou alterar a forma como essas solicitações são tratadas. Por exemplo, o plug-in de SSO pode mostrar a IU adicional para coletar credenciais do usuário final com segurança, exigir MFA ou fornecer tokens silenciosamente para o aplicativo.

Os aplicativos nativos também podem implementar operações personalizadas e conversar diretamente com o plug-in de SSO.
Você pode aprender sobre a estrutura de logon único neste [vídeo de 2019 WWDC da Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-a-microsoft-identity-platform-library"></a>Aplicativos que usam uma biblioteca da plataforma Microsoft Identity

A [MSAL (biblioteca de autenticação da Microsoft) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e superior dá suporte ao plug-in Microsoft Enterprise SSO para dispositivos Apple nativamente para contas corporativas e de estudante. 

Não há nenhuma configuração especial necessária se você seguiu [todas as etapas recomendadas](./quickstart-v2-ios.md) e usou o [formato de URI de redirecionamento](./redirect-uris-ios.md)padrão. Quando executado em um dispositivo que tem o plug-in de SSO presente, o MSAL o invocará automaticamente para todas as solicitações de tokens interativas e silenciosas, bem como as operações de enumeração de conta e de remoção de conta. Como o MSAL implementa o protocolo de plug-in SSO nativo que depende de operações personalizadas, essa configuração fornece a experiência nativa mais suave para o usuário final. 

Se o plug-in de SSO não estiver habilitado pelo MDM, mas o aplicativo Microsoft Authenticator estiver presente no dispositivo, o MSAL usará o aplicativo Microsoft Authenticator para quaisquer solicitações de token interativas. O plug-in de SSO compartilha o SSO com o aplicativo Microsoft Authenticator.

### <a name="applications-that-dont-use-a-microsoft-identity-platform-library"></a>Aplicativos que não usam uma biblioteca da plataforma Microsoft Identity

Os aplicativos que não usam uma biblioteca da plataforma Microsoft Identity como MSAL ainda poderão obter o SSO se um administrador adicioná-los à lista de permissões explicitamente. 

Não há nenhuma alteração de código necessária nesses aplicativos, contanto que as seguintes condições sejam satisfeitas:

- O aplicativo está usando as estruturas da Apple para executar solicitações de rede (por exemplo, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- O aplicativo está usando protocolos padrão para se comunicar com o Azure AD (por exemplo, OAuth2, SAML, WS-Federation)
- O aplicativo não coleta nome de usuário e senha de texto não criptografado na interface do usuário nativa

Nesse caso, o SSO é fornecido quando o aplicativo cria uma solicitação de rede e abre um navegador da Web para conectar o usuário. Quando um usuário é redirecionado para uma URL de logon do Azure AD, o plug-in de SSO valida a URL e verifica se há uma credencial SSO disponível para essa URL. Se houver um, o plug-in SSO passará a credencial SSO para o Azure AD, o que autorizará o aplicativo a concluir a solicitação de rede sem solicitar que o usuário insira suas credenciais. Além disso, se o dispositivo for conhecido pelo Azure AD, o plug-in de SSO também passará o certificado do dispositivo para atender à verificação de acesso condicional com base no dispositivo. 

Para dar suporte a SSO para aplicativos não MSAL, o plug-in de SSO implementa um protocolo semelhante ao plug-in de navegador do Windows descrito em [o que é um token de atualização primário?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Em comparação com aplicativos baseados em MSAL, o plug-in de SSO atua de forma mais transparente para aplicativos não MSAL, integrando-se com a experiência de logon existente do navegador que os aplicativos fornecem. O usuário final veria sua experiência familiar, com o benefício de não ter que executar entradas adicionais em cada um dos aplicativos. Por exemplo, em vez de exibir o seletor de conta nativa, o plug-in de SSO adiciona sessões de SSO à experiência do seletor de conta baseada na Web. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o modo de dispositivo compartilhado no iOS, consulte [modo de dispositivo compartilhado para dispositivos IOS](msal-ios-shared-devices.md).
