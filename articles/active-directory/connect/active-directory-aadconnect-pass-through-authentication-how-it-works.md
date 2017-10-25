---
title: "Azure AD Connect: Autenticação de Passagem – como ela funciona? | Microsoft Docs"
description: "Este artigo descreve como a Autenticação de Passagem do Azure Active Directory funciona."
services: active-directory
keywords: "Autenticação de Passagem do Azure AD Connect, instalar o Active Directory, componentes necessários para o Azure AD, SSO, Logon único"
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
ms.openlocfilehash: 9ded5e0199f5ca48e2a00d2afee0e4c13b3a3460
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-technical-deep-dive"></a>Autenticação de Passagem do Azure Active Directory: aprofundamento técnico
O artigo a seguir é uma visão geral de como funciona a autenticação de passagem do Azure AD.  Para informações de segurança e técnicas aprofundadas, consulte o artigo [**Aprofundamento sobre segurança**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md).

## <a name="how-does-azure-active-directory-pass-through-authentication-work"></a>Como a Autenticação de Passagem do Azure Active Directory funciona?

Quando um usuário tenta entrar em um aplicativo protegido pelo Azure AD (Azure Active Directory) e se a Autenticação de Passagem está habilitada no locatário, ocorrem as seguintes etapas:

1. O usuário tenta acessar um aplicativo (por exemplo, o Outlook Web App – https://outlook.office365.com/owa/).
2. Se o usuário ainda não tiver entrado, ele será redirecionado para a página de entrada do Azure AD.
3. O usuário insere o nome de usuário e a senha na página de entrada do Azure AD e clica no botão "Entrar".
4. O Azure AD, ao receber a solicitação de entrada, coloca o nome de usuário e a senha (criptografada com o uso de uma chave pública) em uma fila.
5. Um agente de Autenticação de Passagem local faz uma chamada de saída à fila e recupera o nome de usuário e a senha criptografada.
6. O agente descriptografa a senha usando sua chave privada.
7. Em seguida, o agente valida o nome de usuário e a senha no Active Directory usando APIs padrão do Windows (um mecanismo semelhante ao que é usado pelos Serviços de Federação do Active Directory (AD FS)). O nome de usuário pode ser o nome de usuário local padrão (normalmente `userPrincipalName`) ou outro atributo configurado no Azure AD Connect (também conhecido como `Alternate ID`).
8. Em seguida, o DC (Controlador de Domínio) do Active Directory local avalia a solicitação e retorna a resposta apropriada (êxito, falha, senha expirada ou usuário bloqueado) para o agente.
9. O agente, por sua vez, envia essa resposta de volta ao Azure AD.
10. O Azure AD avalia a resposta e responde ao usuário conforme apropriado – por exemplo, ele conecta o usuário imediatamente ou solicita a MFA (Autenticação Multifator).
11. Se a entrada do usuário for bem-sucedida, ele será capaz de acessar o aplicativo.

O diagrama a seguir ilustra a todos os componentes e as etapas envolvidas.

![Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="next-steps"></a>Próximas etapas
- [**Limitações atuais**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - Saiba quais cenários têm suporte e quais não têm.
- [**Início rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md) – instale e execute a autenticação de passagem do Azure AD.
- [**Bloqueio Inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) – configura a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [**Perguntas frequentes**](active-directory-aadconnect-pass-through-authentication-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [**Aprofundamento em Segurança**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) – informações técnicas aprofundadas adicionais sobre o recurso.
- [**SSO contínuo do Azure AD**](active-directory-aadconnect-sso.md) – Saiba mais sobre esse recurso complementar.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
