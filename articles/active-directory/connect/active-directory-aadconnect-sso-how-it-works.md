---
title: "Azure AD Connect: Logon Único Contínuo – como ele funciona | Microsoft Docs"
description: "Este artigo descreve como o recurso Logon Único Contínuo do Azure Active Directory funciona."
services: active-directory
keywords: "o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: 38b107513e72635fd034bb86d0d866bcb0fcb8e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Logon Único Contínuo do Azure Active Directory: aprofundamento técnico

Este artigo fornece detalhes técnicos sobre como o recurso SSO Contínuo (Logon único contínuo) do Azure Active Directory funciona.

## <a name="how-does-seamless-sso-work"></a>Como o SSO contínuo funciona?

Esta seção tem duas partes:
1. A instalação do recurso de SSO Contínuo.
2. Como uma transação única de entrada de usuário funciona com o SSO Contínuo.

### <a name="how-does-set-up-work"></a>Como funciona a configuração?

O SSO Contínuo é habilitado por meio do Azure AD Connect, conforme mostrado [aqui](active-directory-aadconnect-sso-quick-start.md). Ao habilitar o recurso, ocorrem as seguintes etapas:
- Uma conta de computador denominada `AZUREADSSOACC` (que representa o Azure AD) é criada em seu AD (Active Directory) local.
- A chave de descriptografia Kerberos da conta do computador é compartilhada com segurança com o Azure AD.
- Além disso, os dois SPNs (nomes de entidade de serviço) Kerberos são criados para representar duas URLs que são usadas durante a entrada no Azure AD.

>[!NOTE]
> A conta do computador e os SPNs Kerberos são criados em cada floresta do AD que você sincroniza com o Azure AD (usando o Azure AD Connect) e para cujos usuários você deseja o SSO Contínuo. Mova a conta do computador `AZUREADSSOACC` para uma UO (unidade organizacional) em que outras contas de computador são armazenadas para garantir que ela seja gerenciada da mesma maneira e não seja excluída.

>[!IMPORTANT]
>É altamente recomendável que você [sobreponha a chave de descriptografia do Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) da conta do computador `AZUREADSSOACC` pelo menos a cada 30 dias.

### <a name="how-does-sign-in-with-seamless-sso-work"></a>Como a entrada com o SSO Contínuo funciona?

Quando essa configuração estiver concluída, o SSO Contínuo funcionará da mesma maneira que qualquer outra entrada que use a IWA (Autenticação Integrada do Windows). O fluxo é da seguinte maneira:

1. O usuário tenta acessar um aplicativo (por exemplo, o Outlook Web App – https://outlook.office365.com/owa/) por meio de um dispositivo corporativo ingressado no domínio na sua rede corporativa.
2. Se o usuário ainda não tiver entrado, ele será redirecionado para a página de entrada do Azure AD.

  >[!NOTE]
  >Se a solicitação de entrada do Azure AD incluir um parâmetro `domain_hint` (identificando o seu locatário, por exemplo, contoso.onmicrosoft.com) ou `login_hint` (identificando o usuário, por exemplo, user@contoso.onmicrosoft.com ou user@contoso.com), a etapa 2 será ignorada.

3. O usuário digita o nome de usuário na página de entrada do Azure AD.
4. Usando JavaScript em segundo plano, o Azure AD desafia o navegador, por meio de uma resposta 401 Não autorizado, para fornecer um tíquete Kerberos.
5. O navegador, por sua vez, solicita um tíquete do Active Directory para a conta do computador `AZUREADSSOACC` (que representa o Azure AD).
6. O Active Directory localiza a conta do computador e retorna um tíquete Kerberos para o navegador criptografado com o segredo da conta do computador.
7. O navegador encaminha o tíquete Kerberos que adquiriu do Active Directory para o Azure AD (em uma das [URLs do Azure AD adicionadas anteriormente às configurações de zona de Intranet do navegador](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature)).
8. O Azure AD descriptografa o tíquete Kerberos, que inclui a identidade do usuário conectado ao dispositivo corporativo, usando a chave compartilhada anteriormente.
9. Após a avaliação, o Azure AD retorna um token ao aplicativo ou solicita que o usuário realize provas adicionais, como a Autenticação Multifator.
10. Se a entrada do usuário for bem-sucedida, ele será capaz de acessar o aplicativo.

O diagrama a seguir ilustra a todos os componentes e as etapas envolvidas.

![Logon Único Contínuo](./media/active-directory-aadconnect-sso/sso2.png)

O SSO Contínuo é oportunista, o que significa que, se ele falhar, a experiência de entrada retornará ao comportamento normal, ou seja, o usuário precisará inserir a senha para entrar.

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido** ](active-directory-aadconnect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Perguntas frequentes**](active-directory-aadconnect-sso-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
