---
title: "Autenticação baseada em certificado do Azure Active Directory no Android | Microsoft Docs"
description: "Saiba mais sobre os cenários com suporte e os requisitos para configuração de autenticação baseada em certificado em soluções com dispositivos Android"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 239d2e1724f2759a52cccdac11fc781d57393125
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticação baseada em certificado do Azure Active Directory no Android


A CBA (autenticação baseada em certificado) permite que você seja autenticado pelo Azure Active Directory com um certificado de cliente em um dispositivo Windows, Android ou iOS ao conectar sua conta do Exchange Online a: 

* Aplicativos móveis do Office, como Microsoft Outlook e Microsoft Word   
* Clientes do EAS (Exchange ActiveSync) 

Configurar esse recurso elimina a necessidade de digitar uma combinação de nome de usuário e senha em determinados emails e aplicativos do Microsoft Office no seu dispositivo móvel. 

Este tópico fornece os requisitos e os cenários com suporte para configurar a CBA em um dispositivo iOS(Android) para usuários de locatários nos planos do Office 365 Enterprise, Business, Education, US Government, China e Germany.



Esse recurso está disponível na visualização em planos do governo federal e para defesa governamental dos EUA do Office 365.


## <a name="office-mobile-applications-support"></a>Suporte a aplicativos móveis do Office
| Aplicativos | Suporte |
| --- | --- |
| Aplicativo de Proteção de Informações do Azure |![Verificação][1] |
| Equipes da Microsoft |![Verificação][1] |
| OneNote |![Verificação][1] |
| OneDrive |![Verificação][1] |
| Outlook |![Verificação][1] |
| Power BI |![Verificação][1] |
| Skype for Business |![Verificação][1] |
| Word/Excel/PowerPoint |![Verificação][1] |
| Yammer |![Verificação][1] |


### <a name="implementation-requirements"></a>Requisitos de implementação

A versão do sistema operacional do dispositivo deve ser Android 5.0 (Lollipop) e superior. 

Um servidor de federação deve ser configurado.  

Para que o Azure Active Directory revogue um certificado do cliente, o token ADFS deve ter as seguintes declarações:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (O número de série do certificado do cliente) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (A cadeia de caracteres para o emissor do certificado do cliente) 

O Azure Active Directory adiciona essas declarações ao token de atualização se elas estiverem disponíveis no token ADFS (ou qualquer outro token SAML). Quando o token de atualização precisa ser validado, essas informações são usadas para verificar a revogação. 

Como prática recomendada, você deve atualizar as páginas de erro do ADFS com instruções sobre como obter um certificado de usuário.  
Para obter mais detalhes, confira [Personalizando as páginas de entrada do AD FS](https://technet.microsoft.com/library/dn280950.aspx).  

Alguns aplicativos do Office (com autenticação moderna habilitada) enviam '*prompt=login*' ao Azure AD na solicitação. Por padrão, o Azure AD converte isso na solicitação ao ADFS para '*wauth=usernamepassworduri*' (solicita que o ADFS faça a autenticação U/P) e '*wfresh=0*' (solicita que o ADFS ignore o estado do SSO e faça uma nova autenticação). Se você quiser habilitar a autenticação baseada em certificado para esses aplicativos, precisará modificar o comportamento padrão do Azure AD. Basta definir o '*PromptLoginBehavior*' em suas configurações de domínio federado como '*Disabled*'. Você pode usar o cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) para executar essa tarefa:

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`



## <a name="exchange-activesync-clients-support"></a>Suporte aos clientes do Exchange ActiveSync
Há suporte para determinados aplicativos do Exchange ActiveSync no Android 5.0 (Lollipop) ou superior. Para determinar se seu aplicativo de email dá suporte a esse recurso, contate o desenvolvedor do aplicativo. 


## <a name="next-steps"></a>Próximas etapas

Se você quiser configurar a autenticação baseada em certificado em seu ambiente, confira [Get started with certificate-based authentication on Android](active-directory-certificate-based-authentication-get-started.md) (Introdução à autenticação baseada em certificado no Android) para obter instruções.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
