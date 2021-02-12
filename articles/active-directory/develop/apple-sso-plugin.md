---
title: Plug-in de SSO do Microsoft Enterprise para dispositivos Apple
titleSuffix: Microsoft identity platform | Azure
description: Saiba mais sobre o plug-in de SSO Azure Active Directory da Microsoft para dispositivos iOS e macOS.
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
ms.openlocfilehash: b7ec6ab8b52d9d43d898f481a2f36310e5c0897d
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94561073"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>Plug-in do SSO corporativo da Microsoft para dispositivos Apple (versão prévia)

>[!IMPORTANT]
> Este recurso [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

O *plug-in Microsoft Enterprise SSO para dispositivos Apple* fornece logon único (SSO) para contas do Azure Active Directory (AD do Azure) em todos os aplicativos que dão suporte ao recurso de [logon único corporativo](https://developer.apple.com/documentation/authenticationservices) da Apple. A Microsoft trabalhou junto com a Apple para desenvolver esse plug-in a fim de aumentar a usabilidade do seu aplicativo e, ao mesmo tempo, fornecer a melhor proteção que a Apple e a Microsoft podem fornecer.

Nesta versão de visualização pública, o plug-in de SSO corporativo está disponível apenas para dispositivos iOS e é distribuído em determinados aplicativos da Microsoft.

## <a name="features"></a>Recursos

O plug-in Microsoft Enterprise SSO para dispositivos Apple oferece os seguintes benefícios:

- Fornece SSO para contas do Azure AD em todos os aplicativos que dão suporte ao recurso Enterprise Single Sign-On da Apple.
- Entregue automaticamente no Microsoft Authenticator e pode ser habilitado por qualquer solução de MDM (gerenciamento de dispositivo móvel).

## <a name="requirements"></a>Requisitos

Para usar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- o iOS 13,0 ou superior deve estar instalado no dispositivo.
- Um aplicativo da Microsoft que fornece o plug-in Microsoft Enterprise SSO para dispositivos Apple deve ser instalado no dispositivo. Para a visualização pública, esses aplicativos incluem o [aplicativo Microsoft Authenticator](../user-help/user-help-auth-app-overview.md).
- O dispositivo deve ser registrado no MDM (por exemplo, com Microsoft Intune).
- A configuração deve ser enviada por push para o dispositivo para habilitar o plug-in Microsoft Enterprise SSO para dispositivos Apple no dispositivo. Essa restrição de segurança é exigida pela Apple.

## <a name="enable-the-sso-plug-in-with-mobile-device-management-mdm"></a>Habilitar o plug-in SSO com o MDM (gerenciamento de dispositivo móvel)

Para habilitar o plug-in Microsoft Enterprise SSO para dispositivos Apple, seus dispositivos precisam receber um sinal por meio de um serviço MDM. Como a Microsoft inclui o plug-in de SSO corporativo no [aplicativo Microsoft Authenticator](..//user-help/user-help-auth-app-overview.md), use o MDM para configurar o aplicativo para habilitar o plug-in Microsoft Enterprise SSO.

Use os seguintes parâmetros para configurar o plug-in Microsoft Enterprise SSO para dispositivos Apple:

- **Tipo** : redirecionar
- **ID da extensão** : `com.microsoft.azureauthenticator.ssoextension`
- **ID da equipe** : (este campo não é necessário para o IOS)
- **URLs** :
  - `https://login.microsoftonline.com`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`
  
### <a name="additional-configuration-options"></a>Opções de configuração adicionais
Opções de configuração adicionais podem ser adicionadas para estender a funcionalidade de SSO para aplicativos adicionais.

#### <a name="enable-sso-for-apps-that-dont-use-msal"></a>Habilitar o SSO para aplicativos que não usam MSAL

O plug-in de SSO permite que qualquer aplicativo participe de logon único mesmo que não tenha sido desenvolvido usando um SDK da Microsoft como a MSAL (biblioteca de autenticação da Microsoft).

O plug-in de SSO é instalado automaticamente por dispositivos que baixaram o aplicativo Microsoft Authenticator e registrou seu dispositivo na sua organização. Sua organização provavelmente usa o aplicativo autenticador hoje para cenários como autenticação multifator, autenticação sem senha e acesso condicional. Ele pode ser ativado para seus aplicativos usando qualquer provedor de MDM, embora a Microsoft tenha tornado fácil de configurar dentro do Microsoft Endpoint Manager do Intune. Uma lista de permissões é usada para configurar esses aplicativos para usar o plug-in de SSO instalado pelo aplicativo autenticador.

Somente os aplicativos que usam tecnologias de rede da Apple nativas ou webviews têm suporte. Se um aplicativo envia sua própria implementação de camada de rede, não há suporte para o plug-in Microsoft Enterprise SSO.  

Use os parâmetros a seguir para configurar o plug-in Microsoft Enterprise SSO para aplicativos que não usam o MSAL:

- **Chave** : `AppAllowList`
- **Tipo** : `String`
- **Valor** : lista delimitada por vírgulas de IDs de pacote de aplicativo para os aplicativos que têm permissão para participar do SSO
- **Exemplo** : `com.contoso.workapp, com.contoso.travelapp`

Os [aplicativos consentidos](./application-consent-experience.md) que são permitidos pelo administrador de MDM para participar do SSO podem obter silenciosamente um token para o usuário final. Portanto, é importante adicionar apenas aplicativos confiáveis à lista de permissões. 

Você não precisa adicionar aplicativos que usam MSAL ou ASWebAuthenticationSession à lista. Esses aplicativos são habilitados por padrão. 

#### <a name="allow-creating-sso-session-from-any-application"></a>Permitir a criação de uma sessão de SSO de qualquer aplicativo

Por padrão, o plug-in do SSO corporativo da Microsoft fornece SSO para aplicativos autorizados somente quando o plug-in de SSO já tiver uma credencial compartilhada. O plug-in Microsoft Enterprise SSO pode adquirir uma credencial compartilhada quando ela é chamada por outro aplicativo ADAL ou baseado em MSAL durante a aquisição de token. A maioria dos aplicativos da Microsoft usa o plug-in Microsoft Authenticator ou SSO. Isso significa que, por padrão, o SSO fora dos fluxos de aplicativos nativos é o melhor esforço.  

Habilitar `browser_sso_interaction_enabled` sinalizador permite que aplicativos não MSAL e o navegador Safari façam a inicialização inicial e obtenham uma credencial compartilhada. Se o plug-in Microsoft Enterprise SSO ainda não tiver uma credencial compartilhada, ele tentará obter uma sempre que uma entrada for solicitada de uma URL do Azure AD dentro do Safari Browser, ASWebAuthenticationSession, SafariViewController ou outro aplicativo nativo permitido.  

- **Chave** : `browser_sso_interaction_enabled`
- **Tipo** : `Integer`
- **Valor** : 1 ou 0

É recomendável habilitar esse sinalizador para obter uma experiência mais consistente em todos os aplicativos. Isso está desabilitado por padrão. 

#### <a name="disable-oauth2-application-prompts"></a>Desabilitar prompts do aplicativo OAuth2

O plug-in Microsoft Enterprise SSO fornece o SSO acrescentando credenciais compartilhadas a solicitações de rede provenientes de aplicativos permitidos. Alguns aplicativos OAuth2 podem estar impondo o prompt do usuário final na camada de protocolo. As credenciais compartilhadas seriam ignoradas para esses aplicativos.  

Habilitar `disable_explicit_app_prompt` o sinalizador restringe a capacidade de aplicativos nativos e Web de forçar um prompt do usuário final na camada de protocolo e ignorar o SSO.

- **Chave** : `disable_explicit_app_prompt`
- **Tipo** : `Integer`
- **Valor** : 1 ou 0

É recomendável habilitar esse sinalizador para obter uma experiência mais consistente em todos os aplicativos. Isso está desabilitado por padrão. 

#### <a name="use-intune-for-simplified-configuration"></a>Usar o Intune para configuração simplificada

Você pode usar Microsoft Intune como seu serviço de MDM para facilitar a configuração do plug-in do SSO corporativo da Microsoft. Para obter mais informações, consulte a [documentação de configuração do Intune](/intune/configuration/ios-device-features-settings).

## <a name="using-the-sso-plug-in-in-your-application"></a>Usando o plug-in de SSO em seu aplicativo

A [MSAL (biblioteca de autenticação da Microsoft) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e superior dá suporte ao plug-in Microsoft Enterprise SSO para dispositivos Apple.

Se você estiver criando um aplicativo para cenários de trabalho do frente, consulte [modo de dispositivo compartilhado para dispositivos IOS](msal-ios-shared-devices.md) para a configuração adicional do recurso.

## <a name="how-the-sso-plug-in-works"></a>Como funciona o plug-in de SSO

O plug-in Microsoft Enterprise SSO conta com a [estrutura de Sign-On empresarial da Apple](https://developer.apple.com/documentation/authenticationservices/asauthorizationsinglesignonprovider?language=objc). Provedores de identidade que integram a estrutura podem interceptar o tráfego de rede para seus domínios e aprimorar ou alterar a forma como essas solicitações são tratadas. Por exemplo, o plug-in de SSO pode mostrar a IU adicional para coletar credenciais do usuário final com segurança, exigir MFA ou fornecer tokens silenciosamente para o aplicativo.

Os aplicativos nativos também podem implementar operações personalizadas e conversar diretamente com o plug-in de SSO.
Você pode aprender sobre a estrutura de logon único neste [vídeo de 2019 WWDC da Apple](https://developer.apple.com/videos/play/tech-talks/301/)

### <a name="applications-that-use-msal"></a>Aplicativos que usam MSAL

A [MSAL (biblioteca de autenticação da Microsoft) para dispositivos Apple](https://github.com/AzureAD/microsoft-authentication-library-for-objc) versão 1.1.0 e superior dá suporte ao plug-in Microsoft Enterprise SSO para dispositivos Apple nativamente para contas corporativas e de estudante. 

Não há nenhuma configuração especial necessária se você seguiu [todas as etapas recomendadas](./quickstart-v2-ios.md) e usou o [formato de URI de redirecionamento](./redirect-uris-ios.md)padrão. Quando executado em um dispositivo que tem o plug-in de SSO presente, o MSAL o invocará automaticamente para todas as solicitações de tokens interativas e silenciosas, bem como as operações de enumeração de conta e de remoção de conta. Como o MSAL implementa o protocolo de plug-in SSO nativo que depende de operações personalizadas, essa configuração fornece a experiência nativa mais suave para o usuário final. 

Se o plug-in de SSO não estiver habilitado pelo MDM, mas o aplicativo Microsoft Authenticator estiver presente no dispositivo, o MSAL usará o aplicativo Microsoft Authenticator para quaisquer solicitações de token interativas. O plug-in de SSO compartilha o SSO com o aplicativo Microsoft Authenticator.

### <a name="applications-that-dont-use-msal"></a>Aplicativos que não usam MSAL

Os aplicativos que não usam MSAL ainda poderão obter o SSO se um administrador adicioná-los à lista de permissões explicitamente. 

Não há nenhuma alteração de código necessária nesses aplicativos, contanto que as seguintes condições sejam satisfeitas:

- O aplicativo está usando as estruturas da Apple para executar solicitações de rede (por exemplo, [WKWebView](https://developer.apple.com/documentation/webkit/wkwebview), [NSURLSession](https://developer.apple.com/documentation/foundation/nsurlsession)) 
- O aplicativo está usando protocolos padrão para se comunicar com o Azure AD (por exemplo, OAuth2, SAML, WS-Federation)
- O aplicativo não coleta nome de usuário e senha de texto não criptografado na interface do usuário nativa

Nesse caso, o SSO é fornecido quando o aplicativo cria uma solicitação de rede e abre um navegador da Web para conectar o usuário. Quando um usuário é redirecionado para uma URL de logon do Azure AD, o plug-in de SSO valida a URL e verifica se há uma credencial SSO disponível para essa URL. Se houver um, o plug-in SSO passará a credencial SSO para o Azure AD, o que autorizará o aplicativo a concluir a solicitação de rede sem solicitar que o usuário insira suas credenciais. Além disso, se o dispositivo for conhecido pelo Azure AD, o plug-in de SSO também passará o certificado do dispositivo para atender à verificação de acesso condicional com base no dispositivo. 

Para dar suporte a SSO para aplicativos não MSAL, o plug-in de SSO implementa um protocolo semelhante ao plug-in de navegador do Windows descrito em [o que é um token de atualização primário?](../devices/concept-primary-refresh-token.md#browser-sso-using-prt). 

Em comparação com aplicativos baseados em MSAL, o plug-in de SSO atua de forma mais transparente para aplicativos não MSAL, integrando-se com a experiência de logon existente do navegador que os aplicativos fornecem. O usuário final veria sua experiência familiar, com o benefício de não ter que executar entradas adicionais em cada um dos aplicativos. Por exemplo, em vez de exibir o seletor de conta nativa, o plug-in de SSO adiciona sessões de SSO à experiência do seletor de conta baseada na Web. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o modo de dispositivo compartilhado no iOS, consulte [modo de dispositivo compartilhado para dispositivos IOS](msal-ios-shared-devices.md).
