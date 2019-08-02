---
title: Comparar o Ingresso no Azure AD e o Azure Active Directory Domain Services| Microsoft Docs
description: Decidir entre o Ingresso no Azure AD e o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 31a71d36-58c1-4839-b958-80da0c6a77eb
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: b6f2ca922153bca4afa22c2b07db03ff2973444d
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67474283"
---
# <a name="choose-between-azure-active-directory-join-and-azure-active-directory-domain-services"></a>Escolha entre o ingresso no Azure Active Directory e o Azure Active Directory Domain Services
Este artigo descreve as diferenças entre o ingresso no Azure Active Directory (AD) e o Azure AD Domain Services, além de ajudar você a escolher com base em seus casos de uso.

## <a name="azure-ad-registered-and-azure-ad-joined-devices"></a>Azure AD registrado e dispositivos adicionados ao Azure AD
O Azure AD permite que você gerencie a identidade dos dispositivos usados pela sua organização e controle o acesso aos recursos corporativos desses dispositivos. Os usuários podem registrar seus dispositivos pessoais (traga seu próprio dispositivo) com o Azure AD, que fornece uma identidade ao dispositivo. Posteriormente, o Azure AD poderá autenticar o dispositivo quando um usuário fizer logon no Azure AD e usar o dispositivo para acessar recursos protegidos. Além disso, você pode gerenciar o dispositivo usando o software de MDM (Gerenciamento de Dispositivo Móvel), como o Microsoft Intune. Este recurso permite que você restrinja o acesso a recursos confidenciais de dispositivos gerenciados e em conformidade com a política.

Você também pode adicionar dispositivos pertencentes à organização ao Azure AD. Este mecanismo oferece os mesmos benefícios de registro de um dispositivo pessoal com o Azure AD. Além disso, os usuários podem entrar no dispositivo usando suas credenciais corporativas. Os dispositivos adicionados ao Azure AD oferecem os seguintes benefícios:
* Logon único (SSO) para aplicativos protegidos pelo Azure AD
* Roaming em conformidade com a política corporativa das configurações de usuário entre dispositivos.
* Acesso à Windows Store para Empresas usando suas credenciais corporativas.
* Windows Hello for Business
* Acesso restrito aos aplicativos e recursos de dispositivos em conformidade com as políticas corporativas.

| **Tipo de dispositivo** | **Plataformas de dispositivo** | **Mecanismo** |
|:---| --- | --- |
| Dispositivos pessoais | Windows 10, iOS, Android e Mac OS | Registrado no Azure AD |
| Dispositivo pertencente à organização não adicionado ao AD local | Windows 10 | Adicionado ao Azure AD |
| Dispositivo pertencente à organização adicionado a um AD local | Windows 10 | Adicionado ao Azure AD híbrido |

Em um Azure AD adicionado ou em um dispositivo registrado, a autenticação de usuário ocorre por meio do uso de protocolos modernos baseados em OAuth/OpenID Connect. Esses protocolos são projetados para funcionarem pela Internet e são ótimos para cenários móveis, nos quais os usuários acessam recursos corporativos de qualquer lugar.


## <a name="domain-join-to-azure-ad-domain-services-managed-domains"></a>Ingresso no domínio para domínios gerenciados do Azure AD Domain Services
O Azure AD Domain Services fornece um domínio de AD gerenciado em uma rede virtual do Azure. Você pode adicionar computadores a este domínio gerenciado usando mecanismos tradicionais de ingresso no domínio. Os computadores com Windows (Windows 7, Windows 10) e com Windows Server podem ser ingressados no domínio gerenciado. Além disso, você também pode adicionar computadores com Linux e Mac OS no domínio gerenciado. Quando você adicionar um computador a um domínio do AD, os usuários poderão entrar no computador usando suas credenciais corporativas. Esses computadores podem ser gerenciados usando a Política de Grupo, aplicando assim a conformidade com as políticas de segurança da sua organização.

Em um computador ingressado no domínio, a autenticação de usuário ocorre por meio do uso dos protocolos de autenticação NTLM ou Kerberos. O computador ingressado no domínio precisa da linha de visão para os controladores de domínio do domínio gerenciado para que a autenticação do usuário funcione. Logo, o computador ingressado no domínio precisa estar na mesma rede virtual que o domínio gerenciado. Como alternativa, o computador ingressado no domínio precisa estar conectado ao domínio gerenciado através de uma rede virtual emparelhada ou de uma conexão VPN/ExpressRoute de site a site. Portanto, este mecanismo não é ideal para dispositivos móveis ou para se conectar aos recursos de fora da rede corporativa.

> [!NOTE]
> Tecnicamente, é possível adicionar uma estação de trabalho local do cliente ao domínio gerenciado através de uma conexão VPN ou ExpressRoute de site a site. No entanto, para dispositivos de usuário final, recomendamos que você registre o dispositivo com o Azure AD (dispositivos pessoais) ou adicione o dispositivo ao Azure AD (dispositivos corporativos). Este mecanismo funciona melhor pela Internet e permite que os usuários finais trabalhem de qualquer lugar. O Azure AD Domain Services é excelente para máquinas virtuais do Windows ou do Linux Server implantadas em suas redes virtuais do Azure, nas quais os aplicativos são implantados.


## <a name="summary---key-differences"></a>Resumo – principais diferenças
| **Aspecto** | **Ingresso no Azure AD** | **Azure AD Domain Services** |
|:---| --- | --- |
| Dispositivo controlado por | AD do Azure | Domínio gerenciado do Azure AD Domain Services |
| Representação no diretório | Objetos de dispositivo no diretório do Azure AD. | Objetos de computador no domínio gerenciado do AAD-DS. |
| Authentication | Protocolos baseados em OAuth/OpenID Connect | Kerberos, protocolos NTLM |
| Gerenciamento | Software de MDM (Gerenciamento de Dispositivo Móvel) como o Intune | Política de Grupo |
| Rede | Funciona pela Internet | Exige que os computadores estejam na mesma rede virtual que o domínio gerenciado.|
| Excelente para... | Dispositivos da área de trabalho ou móveis de usuários finais | Máquinas virtuais de servidor implantadas no Azure |


## <a name="next-steps"></a>Próximas etapas
### <a name="learn-more-about-azure-ad-domain-services"></a>Saiba mais sobre o Azure AD Domain Services
* [Visão geral do Azure AD Domain Services](overview.md)
* [Recursos](active-directory-ds-features.md)
* [Cenários de implantação](scenarios.md)
* [Descubra se o Azure AD Domain Services é adequado aos seus casos de uso](comparison.md)
* [Entenda como o Azure AD Domain Services sincroniza-se ao diretório do Azure AD](synchronization.md)

### <a name="learn-more-about-azure-ad-join"></a>Saiba mais sobre o ingresso no Azure AD
* [Introdução ao gerenciamento de dispositivos no Azure Active Directory](../active-directory/device-management-introduction.md)

### <a name="get-started-with-azure-ad-domain-services"></a>Introdução aos Azure AD Domain Services
* [Habilitar o Azure AD Domain Services usando o portal do Azure](create-instance.md)
