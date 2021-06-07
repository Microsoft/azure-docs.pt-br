---
title: 'Azure AD Connect: Autenticação de Passagem – como ela funciona | Microsoft Docs'
description: Este artigo descreve como a Autenticação de Passagem do Azure Active Directory funciona
services: active-directory
keywords: Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/19/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe92f761ac0b16da7c3cc3c69c1fa4b00f4e7579
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996586"
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticação de Passagem do Azure Active Directory: aprofundamento técnico
Este artigo descreve como funciona a Autenticação de Passagem do Azure AD (Azure Active Directory). Para obter informações técnicas e de segurança profundas, consulte o artigo aprofundamento sobre [segurança](how-to-connect-pta-security-deep-dive.md) .

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Como a Autenticação de Passagem do Azure Active Directory funciona?

>[!NOTE]
>Como um pré-requisito para que a Autenticação de Passagem funcione, os usuários precisam ser provisionados no Azure AD do Active Directory local usando o Azure AD Connect. A Autenticação de Passagem não aplica-se a usuários somente de nuvem.

Quando um usuário tenta entrar em um aplicativo protegido pelo Azure AD, e se a Autenticação de Passagem está habilitada no locatário, ocorrem as seguintes etapas:

1. O usuário tenta acessar um aplicativo, por exemplo, [Outlook Web App](https://outlook.office365.com/owa/).
2. Se o usuário ainda não tiver entrado, ele será redirecionado para a página **Entrada de Usuário** do Azure AD.
3. O usuário insere seu nome de usuário na página de logon do Azure AD e, em seguida, seleciona o botão **Próximo**.
4. O usuário insere a senha na página de logon do Azure AD e seleciona o botão **Login**.
5. O Azure AD, ao receber a solicitação para entrar, coloca o nome de usuário e a senha (criptografados usando a chave pública dos Agentes de Autenticação) em uma fila.
6. Um Agente de Autenticação local recupera o nome de usuário e a senha criptografada da fila. Observe que o Agente não realiza com frequência uma sondagem por solicitações da fila, mas recupera as solicitações por uma conexão persistente pré-estabelecida.
7. O agente descriptografa a senha usando sua chave privada.
8. Em seguida, o agente valida o nome de usuário e a senha no Active Directory usando APIs padrão do Windows, que é um mecanismo semelhante ao usado pelos Serviços de Federação do Active Directory (AD FS). O nome de usuário pode ser o nome de usuário local padrão, normalmente `userPrincipalName`, ou outro atributo configurado no Azure AD Connect (também conhecido como `Alternate ID`).
9. Em seguida, o DC (Controlador de Domínio) do Active Directory local avalia a solicitação e retorna a resposta apropriada (êxito, falha, senha expirada ou usuário bloqueado) para o agente.
10. O Agente de Autenticação, por sua vez, retorna essa resposta ao Azure AD.
11. Azure AD avalia a resposta e responde ao usuário conforme apropriado. Por exemplo, o Azure AD assina o usuário imediatamente ou solicita a autenticação multifator do Azure AD.
12. Se a entrada do usuário for bem-sucedida, ele será capaz de acessar o aplicativo.

O diagrama a seguir ilustra a todos os componentes e as etapas envolvidas:

![Autenticação de Passagem](./media/how-to-connect-pta-how-it-works/pta2.png)

## <a name="next-steps"></a>Próximas etapas
- [Limitações atuais](how-to-connect-pta-current-limitations.md): saiba quais cenários têm suporte e quais não têm.
- [Início rápido](how-to-connect-pta-quick-start.md): colocar em funcionamento a autenticação de passagem do Azure AD.
- [Migrar do AD FS para Autenticação de Passagem](https://aka.ms/adfstoPTADP) – um guia detalhado para migrar do AD FS (ou outras tecnologias de federação) para Autenticação de Passagem.
- [Bloqueio Inteligente](../authentication/howto-password-smart-lockout.md): configure a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [Perguntas](how-to-connect-pta-faq.md)frequentes: Encontre respostas para perguntas frequentes.
- [Solução de problemas](tshoot-connect-pass-through-authentication.md): saiba como resolver problemas comuns com o recurso de Autenticação de Passagem.
- [Aprofundamento da segurança](how-to-connect-pta-security-deep-dive.md): Obtenha informações técnicas aprofundadas sobre o recurso de autenticação de passagem.
- [SSO contínuo do Azure AD](how-to-connect-sso.md): saiba mais sobre esse recurso complementar.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): use o Fórum do Azure Active Directory para arquivar novas solicitações.

