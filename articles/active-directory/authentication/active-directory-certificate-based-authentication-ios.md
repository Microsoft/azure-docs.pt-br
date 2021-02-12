---
title: Autenticação baseada em certificado no iOS-Azure Active Directory
description: Saiba mais sobre os cenários com suporte e os requisitos para configurar a autenticação baseada em certificado para Azure Active Directory em soluções com dispositivos iOS
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f9b96fe9ee0781803bbbd86316e8783b60a6f1
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861316"
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticação baseada em certificado do Azure Active Directory no iOS

Para melhorar a segurança, os dispositivos iOS podem usar a CBA (autenticação baseada em certificado) para autenticar no Azure Active Directory (AD do Azure) usando um certificado de cliente em seu dispositivo ao se conectar aos seguintes aplicativos ou serviços:

* Aplicativos móveis do Office, como Microsoft Outlook e Microsoft Word
* Clientes do EAS (Exchange ActiveSync)

O uso de certificados elimina a necessidade de inserir uma combinação de nome de usuário e senha em determinados emails e Microsoft Office aplicativos em seu dispositivo móvel.

Este artigo detalha os requisitos e os cenários com suporte para configurar o CBA em um dispositivo iOS. O CBA para iOS está disponível nas nuvens públicas do Azure, na nuvem do Microsoft governamental, Microsoft Cloud Alemanha e Microsoft Azure na 21Vianet da China.

## <a name="microsoft-mobile-applications-support"></a>Suporte a aplicativos móveis da Microsoft

| Aplicativos | Suporte |
| --- | --- |
| Aplicativo de Proteção de Informações do Azure |![Marca de seleção que significa suporte para este aplicativo][1] |
| Intune Portal da empresa |![Marca de seleção que significa suporte para este aplicativo][1] |
| Microsoft Teams |![Marca de seleção que significa suporte para este aplicativo][1] |
| Office (móvel) |![Marca de seleção que significa suporte para este aplicativo][1] |
| OneNote |![Marca de seleção que significa suporte para este aplicativo][1] |
| OneDrive |![Marca de seleção que significa suporte para este aplicativo][1] |
| Outlook |![Marca de seleção que significa suporte para este aplicativo][1] |
| Power BI |![Marca de seleção que significa suporte para este aplicativo][1] |
| Skype for Business |![Marca de seleção que significa suporte para este aplicativo][1] |
| Word/Excel/PowerPoint |![Marca de seleção que significa suporte para este aplicativo][1] |
| Yammer |![Marca de seleção que significa suporte para este aplicativo][1] |

## <a name="requirements"></a>Requisitos

Para usar o CBA com o iOS, os seguintes requisitos e considerações se aplicam:

* A versão do sistema operacional do dispositivo deve ser iOS 9 ou superior.
* É necessário um Microsoft Authenticator para aplicativos do Office em iOS.
* Uma preferência de identidade deve ser criada no conjunto de chaves do macOS que inclua a URL de autenticação do servidor ADFS. Para obter mais informações, consulte [criar uma preferência de identidade no acesso ao conjunto de chaves no Mac](https://support.apple.com/guide/keychain-access/create-an-identity-preference-kyca6343b6c9/mac).

Os seguintes requisitos e considerações do ADFS (Serviços de Federação do Active Directory (AD FS)) se aplicam:

* O servidor ADFS deve estar habilitado para autenticação de certificado e usar a autenticação federada.
* O certificado precisa ter que usar EKU (uso avançado de chave) e conter o UPN do usuário no *nome alternativo da entidade (nome da entidade NT)*.

## <a name="configure-adfs"></a>Configurar o ADFS

Para que o Azure AD revogue um certificado de cliente, o token do ADFS deve ter as seguintes declarações. O Azure AD adiciona essas declarações ao token de atualização se elas estiverem disponíveis no token do ADFS (ou qualquer outro token SAML). Quando o token de atualização precisa ser validado, essas informações são usadas para verificar a revogação:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` -Adicionar o número de série do seu certificado de cliente
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` -Adicionar a cadeia de caracteres para o emissor do seu certificado de cliente

Como prática recomendada, você também deve atualizar as páginas de erro do ADFS de sua organização com as seguintes informações:

* O requisito para instalar o Microsoft Authenticator no iOS.
* Instruções sobre como obter um certificado de usuário.

Para obter mais informações, consulte [Personalizando a página de entrada AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

## <a name="use-modern-authentication-with-office-apps"></a>Usar autenticação moderna com aplicativos do Office

Alguns aplicativos do Office com autenticação moderna habilitada enviam `prompt=login` para o Azure AD em sua solicitação. Por padrão, o Azure AD se traduz `prompt=login` na solicitação para o ADFS como `wauth=usernamepassworduri` (solicita que o ADFS faça a autenticação U/P) e `wfresh=0` (solicita que o ADFS ignore o estado do SSO e faça uma nova autenticação). Se você quiser habilitar a autenticação baseada em certificado para esses aplicativos, modifique o comportamento padrão do Azure AD.

Para atualizar o comportamento padrão, defina '*PromptLoginBehavior*' em suas configurações de domínio federado como *desabilitado*. Você pode usar o cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings) para executar essa tarefa, conforme mostrado no exemplo a seguir:

```powershell
Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled
```

## <a name="support-for-exchange-activesync-clients"></a>Suporte para clientes do Exchange ActiveSync

No iOS 9 ou posterior, há suporte para o cliente de email do iOS nativo. Para determinar se esse recurso tem suporte para todos os outros aplicativos do Exchange ActiveSync, contate o desenvolvedor do aplicativo.

## <a name="next-steps"></a>Próximas etapas

Para configurar a autenticação baseada em certificado em seu ambiente, consulte Introdução [à autenticação baseada em certificado](active-directory-certificate-based-authentication-get-started.md) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
