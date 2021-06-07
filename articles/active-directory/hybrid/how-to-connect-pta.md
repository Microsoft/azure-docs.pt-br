---
title: 'Azure AD Connect: autenticação de passagem | Microsoft Docs'
description: Este artigo descreve a autenticação de passagem do Azure AD (Azure Active Directory) e como ela permite entradas do Azure AD por meio da validação de senhas de usuários no Active Directory local.
services: active-directory
keywords: o que é a autenticação de passagem do Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/21/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b8f613cb7c75d9dd6af1fcf62f9d484398072c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "89279458"
---
# <a name="user-sign-in-with-azure-active-directory-pass-through-authentication"></a>Entrada do usuário com autenticação de passagem do Azure Active Directory

## <a name="what-is-azure-active-directory-pass-through-authentication"></a>O que é a autenticação de passagem do Azure Active Directory?

A autenticação de passagem do Azure AD (Azure Active Directory) permite que os usuários entrem em aplicativos locais e baseados em nuvem usando as mesmas senhas. Esse recurso fornece aos usuários uma experiência melhor – uma senha a menos para se lembrar – e reduz os custos de suporte técnico de TI porque os usuários têm menor probabilidade de se esquecer como entrar. Quando os usuários entram usando o Azure AD, esse recurso valida as senhas dos usuários diretamente no seu Active Directory local.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Esse recurso é uma alternativa à [Sincronização de Hash de Senha do Azure AD](how-to-connect-password-hash-synchronization.md), que fornece o mesmo benefício da autenticação de nuvem para as organizações. No entanto, determinadas organizações que desejam impor suas políticas de segurança e senha do Active Directory local podem optar por usar a Autenticação de Passagem. Leia [este guia](./choose-ad-authn.md) para uma comparação entre os vários métodos de conexão do Azure AD e como escolher o método de entrada certo para a sua organização.

![O que é a Autenticação de Passagem do Azure AD](./media/how-to-connect-pta/pta1.png)

Você pode combinar a Autenticação de Passagem com o recurso de [SSO (logon único) Contínuo](how-to-connect-sso.md). Dessa forma, quando os usuários estiverem acessando aplicativos em seus computadores corporativos dentro de sua rede corporativa, eles não precisarão digitar senhas para entrar.

## <a name="key-benefits-of-using-azure-ad-pass-through-authentication"></a>Principais benefícios de usar a Autenticação de Passagem do Azure AD

- *Ótima experiência do usuário*
  - Os usuários usam as mesmas senhas para entrar em aplicativos locais e aplicativos baseados na nuvem.
  - Os usuários perdem menos tempo se comunicando com o suporte técnico de TI para resolver problemas de senha.
  - Os usuários podem concluir tarefas de [gerenciamento de senha por autoatendimento](../authentication/concept-sspr-howitworks.md) na nuvem.
- *Fácil de implantar e administrar*
  - Não são necessárias implantações locais ou configurações de rede complexas.
  - É necessário apenas que um agente leve seja instalado localmente.
  - Não há sobrecarga de gerenciamento. O agente recebe automaticamente melhorias e correções de bugs.
- *Proteger*
  - Senhas locais nunca são armazenadas na nuvem, em formato nenhum.
  - Proteja suas contas de usuário trabalhando diretamente com as [Políticas de acesso condicional do Azure AD](../conditional-access/overview.md), incluindo a MFA (Autenticação Multifator), [bloqueando autenticação herdada](../conditional-access/concept-conditional-access-conditions.md) e [filtrando ataques de senha de força bruta](../authentication/howto-password-smart-lockout.md).
  - O agente estabelece conexões de saída apenas de dentro de sua rede. Portanto, não há nenhum requisito de instalar o agente em uma rede de perímetro, também conhecida como DMZ.
  - A comunicação entre um agente e o Azure AD é protegida com a autenticação baseada em certificado. Esses certificados são renovados automaticamente a cada poucos meses pelo Azure AD.
- *Altamente disponível*
  - Agentes adicionais podem ser instalados em vários servidores locais para promover a alta disponibilidade de solicitações de entrada.

## <a name="feature-highlights"></a>Destaques do recurso

- Dá suporte à entrada do usuário em todos os aplicativos baseados em navegador da Web e em aplicativos de cliente do Microsoft Office que usam [autenticação moderna](https://aka.ms/modernauthga).
- Os nomes de usuário de entrada podem ser o nome de usuário local padrão (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (conhecido como `Alternate ID`).
- O recurso funciona diretamente com recursos de [acesso condicional](../conditional-access/overview.md) , como o MFA (autenticação multifator) para ajudar a proteger seus usuários.
- Integrado com [gerenciamento de senha de autoatendimento](../authentication/concept-sspr-howitworks.md) baseado em nuvem, incluindo write-back de senha para o Active Directory local e a proteção de senha por proibição de senhas usadas normalmente.
- Ambientes de várias florestas têm suporte se houver relações de confiança entre suas florestas do AD e se o encaminhamento de sufixo de nome estiver configurado corretamente.
- Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Ele pode ser habilitado por meio de [Azure ad Connect](whatis-hybrid-identity.md).
- Ele usa um agente local leve que escuta e responde a solicitações de validação de senha.
- Instalar vários agentes fornece alta disponibilidade de solicitações de entrada.
- Isso [protege](../authentication/howto-password-smart-lockout.md) suas contas locais contra ataques de senha de força bruta na nuvem.

## <a name="next-steps"></a>Próximas etapas

- [Início rápido](how-to-connect-pta-quick-start.md) -obtenha a autenticação de passagem do Azure AD.
- [Migrar do AD FS para Autenticação de Passagem](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – um guia detalhado para migrar do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem.
- [Bloqueio Inteligente](../authentication/howto-password-smart-lockout.md) – configura a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Limitações atuais](how-to-connect-pta-current-limitations.md) - Saiba quais cenários têm suporte e quais não têm.
- [Aprofundamento técnico](how-to-connect-pta-how-it-works.md) – entenda como esse recurso funciona.
- [Perguntas frequentes](how-to-connect-pta-faq.md) – respostas para perguntas frequentes.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [Aprofundamento em Segurança](how-to-connect-pta-security-deep-dive.md) – informações técnicas aprofundadas adicionais sobre o recurso.
- [SSO contínuo do Azure AD](how-to-connect-sso.md) – Saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.