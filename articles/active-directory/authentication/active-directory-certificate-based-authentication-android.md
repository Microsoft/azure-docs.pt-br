---
title: Autenticação baseada em certificado do Android-Azure Active Directory
description: Saiba mais sobre os cenários com suporte e os requisitos para configuração de autenticação baseada em certificado em soluções com dispositivos Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1001d5524fe99783cda4d5b77bdaceacc6791848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96861361"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticação baseada em certificado do Azure Active Directory no Android

Os dispositivos Android podem usar CBA (Autenticação Baseada em Certificado) para autenticarem-se no Azure Active Directory usando um certificado do cliente nos dispositivos durante a conexão a:

* Aplicativos móveis do Office, como Microsoft Outlook e Microsoft Word
* Clientes do EAS (Exchange ActiveSync)

Configurar esse recurso elimina a necessidade de digitar uma combinação de nome de usuário e senha em determinados emails e aplicativos do Microsoft Office no seu dispositivo móvel.

Este tópico fornece os requisitos e os cenários com suporte para configurar o CBA em um dispositivo Android para usuários de locatários nos planos do Office 365 Enterprise, Business, Education, governo dos EUA, China e Alemanha.

Esse recurso está disponível na visualização em planos do governo federal e para defesa governamental dos EUA do Office 365.

## <a name="microsoft-mobile-applications-support"></a>Suporte a aplicativos móveis da Microsoft

| Aplicativos | Suporte |
| --- | --- |
| Aplicativo de Proteção de Informações do Azure |![Marca de seleção que significa suporte para este aplicativo][1] |
| Intune Portal da empresa |![Marca de seleção que significa suporte para este aplicativo][1] |
| Microsoft Teams |![Marca de seleção que significa suporte para este aplicativo][1] |
| OneNote |![Marca de seleção que significa suporte para este aplicativo][1] |
| OneDrive |![Marca de seleção que significa suporte para este aplicativo][1] |
| Outlook |![Marca de seleção que significa suporte para este aplicativo][1] |
| Power BI |![Marca de seleção que significa suporte para este aplicativo][1] |
| Skype for Business |![Marca de seleção que significa suporte para este aplicativo][1] |
| Word/Excel/PowerPoint |![Marca de seleção que significa suporte para este aplicativo][1] |
| Yammer |![Marca de seleção que significa suporte para este aplicativo][1] |

### <a name="implementation-requirements"></a>Requisitos de implementação

A versão do sistema operacional do dispositivo deve ser Android 5.0 (Lollipop) e superior.

Um servidor de federação deve ser configurado.

Para que o Azure Active Directory revogue um certificado do cliente, o token ADFS deve ter as seguintes declarações:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (O número de série do certificado do cliente)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (A cadeia de caracteres para o emissor do certificado do cliente)

O Azure Active Directory adiciona essas declarações ao token de atualização se elas estiverem disponíveis no token ADFS (ou qualquer outro token SAML). Quando o token de atualização precisa ser validado, essas informações são usadas para verificar a revogação.

Como melhor prática, é necessário atualizar as páginas de erro de ADFS da organização com as informações a seguir:

* O requisito para instalar o Microsoft Authenticator no Android.
* Instruções sobre como obter um certificado de usuário.

Para obter mais informações, consulte [Personalizando as páginas de entrada do AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Alguns aplicativos do Office (com autenticação moderna habilitada) enviam '*prompt = logon*' ao Azure AD em sua solicitação. Por padrão, o Azure AD traduz '*prompt = logon*' na solicitação para ADFS como '*wauth = usernamepassworduri*' (solicita que o ADFS faça a autenticação U/P) e '*wfresh = 0*' (solicita que o ADFS ignore o estado do SSO e faça uma nova autenticação). Se você quiser habilitar a autenticação baseada em certificado para esses aplicativos, precisará modificar o comportamento padrão do Azure AD. Defina '*PromptLoginBehavior*' nas configurações de domínio federado como '*Disabled*'.
Você pode usar o cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) para executar essa tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Suporte aos clientes do Exchange ActiveSync

Há suporte para determinados aplicativos do Exchange ActiveSync no Android 5.0 (Lollipop) ou superior. Para determinar se o aplicativo de email dá suporte a esse recurso, contate o desenvolvedor do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Se você quiser configurar a autenticação baseada em certificado em seu ambiente, confira [Get started with certificate-based authentication on Android](active-directory-certificate-based-authentication-get-started.md) (Introdução à autenticação baseada em certificado no Android) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
