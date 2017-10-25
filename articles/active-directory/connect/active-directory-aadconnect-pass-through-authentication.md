---
title: "Azure AD Connect: autenticação de passagem | Microsoft Docs"
description: "Este artigo descreve a autenticação de passagem do Azure AD (Azure Active Directory) e como ela permite entradas do Azure AD por meio da validação de senhas de usuários no Active Directory local."
services: active-directory
keywords: "o que é a autenticação de passagem do Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: billmath
ms.openlocfilehash: 96a33547329931903d264d5ec4ea8da76e36a0a0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Entrada do usuário com autenticação de passagem do Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>O que é a autenticação de passagem do Azure Active Directory?

A autenticação de passagem do Azure AD (Azure Active Directory) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. Esse recurso fornece aos usuários uma experiência melhor – uma senha a menos para se lembrar – e reduz os custos de suporte técnico de TI porque os usuários têm menor probabilidade de se esquecer como entrar. Quando os usuários entram usando o Azure AD, esse recurso valida as senhas dos usuários diretamente no seu Active Directory local.

<iframe width="560" height="315" src="https://www.youtube.com/embed/PyeAC85Gm7w" frameborder="0" allowfullscreen></iframe>

Esse recurso é uma alternativa à [Sincronização de Hash de Senha do Azure AD](active-directory-aadconnectsync-implement-password-synchronization.md), que fornece o mesmo benefício da autenticação de nuvem para as organizações. No entanto, as políticas de conformidade e segurança de certas organizações não permitirem que essas organizações enviem as senhas dos usuários, mesmo em um formato com hash, para fora de seus limites internos. A Autenticação de Passagem é a solução ideal para essas organizações.

![O que é a Autenticação de Passagem do Azure AD](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

Você pode combinar a Autenticação de Passagem com o recurso de [SSO (logon único) Contínuo](active-directory-aadconnect-sso.md). Dessa forma, quando os usuários estiverem acessando aplicativos em seus computadores corporativos dentro de sua rede corporativa, eles não precisarão digitar senhas para entrar.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Principais benefícios de usar a Autenticação de Passagem do Azure AD

- *Ótima experiência do usuário*
  - Os usuários usam as mesmas senhas para entrar em aplicativos locais e aplicativos baseados na nuvem.
  - Os usuários perdem menos tempo se comunicando com o suporte técnico de TI para resolver problemas de senha.
  - Os usuários podem concluir tarefas de [gerenciamento de senha por autoatendimento](../active-directory-passwords-overview.md) na nuvem.
- *Fácil de implantar e administrar*
  - Não são necessárias implantações locais ou configurações de rede complexas.
  - É necessário apenas que um agente leve seja instalado localmente.
  - Não há sobrecarga de gerenciamento. O agente recebe automaticamente melhorias e correções de bugs.
- *Proteger*
  - Senhas locais nunca são armazenadas na nuvem, em formato nenhum.
  - O agente estabelece conexões de saída apenas de dentro de sua rede. Portanto, não há nenhum requisito de instalar o agente em uma rede de perímetro, também conhecida como DMZ.
  - Proteja suas contas de usuário trabalhando diretamente com as [Políticas de acesso condicional do Azure AD](../active-directory-conditional-access-azure-portal.md), incluindo a MFA (Autenticação Multifator), e [filtrando ataques de senha de força bruta](active-directory-aadconnect-pass-through-authentication-smart-lockout.md).
- *Altamente disponível*
  - Agentes adicionais podem ser instalados em vários servidores locais para promover a alta disponibilidade de solicitações de entrada.

## <a name="feature-highlights"></a>Destaques do recurso

- Dá suporte à entrada do usuário em todos os aplicativos baseados em navegador da Web e em aplicativos de cliente do Microsoft Office que usam [autenticação moderna](https://aka.ms/modernauthga).
- Os nomes de usuário de entrada podem ser o nome de usuário local padrão (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (conhecido como `Alternate ID`).
- O recurso funciona diretamente com recursos de [acesso condicional](../active-directory-conditional-access.md), como a MFA (Autenticação Multifator), para ajudar a proteger seus usuários.
- Integrado com [gerenciamento de senha de autoatendimento](../active-directory-passwords-overview.md) baseado em nuvem, incluindo write-back de senha para o Active Directory local e a proteção de senha por proibição de senhas usadas normalmente.
- Ambientes de várias florestas têm suporte se houver relações de confiança entre suas florestas do AD e se o encaminhamento de sufixo de nome estiver configurado corretamente.
- Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Ele pode ser habilitado por meio do [Azure AD Connect](active-directory-aadconnect.md).
- Ele usa um agente local leve que escuta e responde a solicitações de validação de senha.
- Instalar vários agentes fornece alta disponibilidade de solicitações de entrada.
- Isso [protege](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) suas contas locais contra ataques de senha de força bruta na nuvem.

## <a name="next-steps"></a>Próximas etapas

- [**Início rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md) – instale e execute a autenticação de passagem do Azure AD.
- [**Bloqueio Inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) – configura a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [**Limitações atuais**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - Saiba quais cenários têm suporte e quais não têm.
- [**Aprofundamento técnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](active-directory-aadconnect-pass-through-authentication-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [**Aprofundamento em Segurança**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) – informações técnicas aprofundadas adicionais sobre o recurso.
- [**SSO contínuo do Azure AD**](active-directory-aadconnect-sso.md) – Saiba mais sobre esse recurso complementar.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
