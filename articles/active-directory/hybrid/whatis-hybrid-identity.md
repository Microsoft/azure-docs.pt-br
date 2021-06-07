---
title: O que é a identidade híbrida com o Azure Active Directory?
description: Identidade híbrida é ter uma identidade de usuário comum para autenticação e autorização no local e na nuvem.
keywords: introdução ao Azure AD Connect, visão geral do Azure AD Connect, o que é o Azure AD Connect, instalar o active directory
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895355b5acebffe6ad24b15b0c709d21e8f20be4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "90016641"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>O que é a identidade híbrida com o Azure Active Directory?

Hoje, as empresas e corporações estão se tornando cada vez mais uma mistura de aplicativos locais e na nuvem.  Os usuários precisam ter acesso local e na nuvem a esses aplicativos. Gerenciar usuários locais e na nuvem apresenta cenários desafiadores. 

As soluções de identidade da Microsoft abrangem funcionalidades locais e baseadas em nuvem.  Essas soluções criam uma identidade de usuário comum para autenticação e autorização para todos os recursos, independentemente da localização. Chamamos isso de **identidade híbrida**.

Com a identidade híbrida para o Azure AD e o gerenciamento de identidade híbrida, esses cenários se tornam possíveis.

Para obter a identidade híbrida com o Azure AD, pode ser usado um dos três métodos de autenticação, dependendo dos cenários.   Os três métodos são: 

- **[PHS (sincronização de hash de senha)](whatis-phs.md)**  
- **[PTA (autenticação de passagem)](how-to-connect-pta.md)**  
- **[Federação (AD FS)](whatis-fed.md)** 

Esses métodos de autenticação também fornecem funcionalidades de [logon único](how-to-connect-sso.md).  O logon único faz logon automaticamente dos seus usuários quando eles estão em seus dispositivos corporativos, conectados à sua rede corporativa.

Para saber mais, confira [Escolha o método de autenticação certo para sua solução de identidade híbrida do Azure Active Directory](./choose-ad-authn.md). 

## <a name="common-scenarios-and-recommendations"></a>Cenários e recomendações comuns 

Aqui estão alguns dos cenários comuns de gerenciamento de acesso e identidade híbrida com recomendações sobre qual opção de identidade híbrida pode ser apropriada para cada um. 

|Eu preciso de:|PHS e SSO<sup>1</sup>| PTA e SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|Sincronizar automaticamente na nuvem as contas de novo usuário, de contato e de grupo criadas no meu Active Directory local.|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Configurar meu locatário para cenários híbridos do Microsoft 365.|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilitar os usuários a entrar e acessar serviços de nuvem usando suas senhas locais.|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Implementar o logon único usando credenciais corporativas.|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| ![Recomendadas](./media/whatis-hybrid-identity/ic195031.png) |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|  
|Assegurar que nenhum hash de senha seja armazenado na nuvem.| |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilite soluções de autenticação multifator baseadas em nuvem.|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)|![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Habilitar soluções de autenticação multifator locais.| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Dar suporte à autenticação de cartão inteligente para meus usuários.<sup>4</sup>| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 
|Exibir notificações de expiração de senha no Portal do Office e na área de trabalho do Windows 10.| | |![Recomendadas](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Sincronização de hash de senha com logon único. 
> 
> <sup>2</sup> Autenticação de passagem e Logon único.  
> 
> <sup>3</sup> Logon único federado com o AD FS.  
>  
> <sup>4</sup> O AD FS pode ser integrado com a Enterprise PKI para permitir a entrada usando certificados. Esses certificados podem ser certificados suaves implantados por meio dos canais de provisionamento confiáveis como certificados de cartão inteligente (incluindo cartões PIV/CAC), MDM, GPO ou Hello for Business (certificado confiável). Para obter mais informações sobre o suporte à autenticação de cartão inteligente, consulte [este blog](/archive/blogs/samueld/adfs-certauth-aad-o365). 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>Requisitos de licença para usar o Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Próximas etapas 

- [O que é o Azure AD Connect e o Connect Health?](whatis-azure-ad-connect.md) 
- [O que é PHS (sincronização de hash de senha)?](whatis-phs.md) 
- [O que é PTA (autenticação de passagem)?](how-to-connect-pta.md) 
- [O que é federação?](whatis-fed.md) 
- [O que é logon único?](how-to-connect-sso.md)