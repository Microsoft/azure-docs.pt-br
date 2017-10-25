---
title: "Segurança de senha em camadas do Azure AD | Microsoft Docs"
description: "Explica como o Azure AD impõe senhas fortes e protege as senhas dos usuários contra os cibercriminosos."
services: active-directory
documentationcenter: 
author: barlanmsft
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: barlan
ms.openlocfilehash: 50b24466bcbd399de19934f7ec5ed096e308eaf3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="a-multi-tiered-approach-to-azure-ad-password-security"></a>Uma abordagem de várias camadas à segurança de senha do Azure AD

Este artigo discute algumas práticas recomendadas que você pode seguir como um usuário ou como um administrador para proteger sua Conta da Microsoft ou do Azure AD (Azure Active Directory).

 > [!NOTE]
 > **Você está aqui por que está enfrentando problemas para iniciar sessão?** Se sim, [veja aqui como alterar e redefinir sua senha](active-directory-passwords-update-your-own-password.md).
 >
 > Os administradores do Azure AD podem redefinir senhas de usuário usando as diretrizes neste artigo [Redefinir a senha de um usuário no Azure Active Directory](active-directory-users-reset-password-azure-portal.md).
 >

## <a name="password-requirements"></a>Requisitos de senha

O Azure AD inclui as seguintes abordagens comuns para proteger senhas:

* Requisitos de comprimento de senha
* Requisitos de complexidade de senha
* Expiração de senha regular e periódica

Para obter informações sobre a redefinição de senha no Azure Active Directory, consulte o tópico [Azure AD self-service password reset for the IT professional](active-directory-passwords.md) (Redefinição de senha de autoatendimento do Azure AD para o profissional de TI).

## <a name="azure-ad-password-protections"></a>Proteções de senha do Azure AD

O Azure AD e o Sistema de Contas da Microsoft usam abordagens comprovadas do setor para assegurar a proteção segura de senhas de usuário e administrador, incluindo:

* Senhas banidas dinamicamente
* Bloqueio de Senha Inteligente

Para obter informações sobre o gerenciamento de senha com base na pesquisa atual, confira o white paper [Password Guidance](http://aka.ms/passwordguidance) (Diretrizes de senha).

### <a name="dynamically-banned-passwords"></a>Senhas banidas dinamicamente

As Contas da Microsoft e do Azure AD garantem a proteção por senha banindo dinamicamente as senhas comumente usadas. A equipe do Azure AD Identity Protection analisa rotineiramente as listas de senhas proibidas, impedindo que os usuários selecionem senhas usadas comumente. Este serviço está disponível para o Azure AD e os clientes do serviço de Conta da Microsoft.

Durante a criação de senhas, é importante que os administradores incentivem os usuários a escolher frases de senha que incluam uma combinação exclusiva de letras, números, caracteres ou palavras. Essa abordagem ajuda a tornar as senhas de usuário praticamente impossíveis de serem comprometidas, mas mais fáceis de serem lembradas pelos usuários.

#### <a name="password-breaches"></a>Violações de senha

A Microsoft está sempre trabalhando para se manter um passo à frente dos cibercriminosos.

A equipe do Azure AD Identity Protection analisa continuamente as senhas que são usadas comumente. Os cibercriminosos também usam estratégias semelhantes para preparar seus ataques, como criar uma [tabela rainbow](https://en.wikipedia.org/wiki/Rainbow_table) de violação de hashes de senha.

A Microsoft analisa continuamente as [violações de dados](https://www.privacyrights.org/data-breaches) para manter uma lista de senhas banidas atualizada dinamicamente, que garante que as senhas vulneráveis sejam banidas antes de se tornarem realmente uma ameaça para os clientes do Azure AD. Para obter mais informações sobre nossas iniciativas de segurança atuais, confira o [Relatório de inteligência de segurança da Microsoft](https://www.microsoft.com/security/sir/default.aspx).

### <a name="smart-password-lockout"></a>Bloqueio de Senha Inteligente

Quando o Azure AD detecta que um cibercriminoso potencial está tentando invadir uma senha de usuário, bloqueamos a conta de usuário com o Bloqueio de Senha Inteligente. O Azure AD foi projetado para determinar o risco associado a sessões de logon específico. Usando os dados de segurança mais recentes, aplicamos semântica de bloqueio para parar ameaças cibernéticas.

Se um usuário estiver bloqueado do Azure AD, a tela será semelhante à abaixo:

  ![Bloqueado no Azure AD](./media/active-directory-secure-passwords/locked-out-azuread.png)

Para outras contas da Microsoft, a tela é semelhante à abaixo:

  ![Bloqueado em uma conta da Microsoft](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

Para obter informações sobre a redefinição de senha no Azure Active Directory, consulte o tópico [Azure AD self-service password reset for the IT professional](active-directory-passwords.md) (Redefinição de senha de autoatendimento do Azure AD para o profissional de TI).

  >[!NOTE]
  >Se você é administrador do Azure AD, convém usar o [Windows Hello](https://www.microsoft.com/windows/windows-hello) para evitar que os usuários criem senhas tradicionais.
  >

## <a name="next-steps"></a>Próximas etapas

* [Como atualizar sua própria senha](active-directory-passwords-update-your-own-password.md)
* [Os fundamentos do gerenciamento de identidades do Azure](fundamentals-identity.md)
* [Relatório de atividade de registro de redefinição de senha](active-directory-passwords-reporting.md)
