---
title: "Azure AD Connect: Autenticação de Passagem – Perguntas frequentes | Microsoft Docs"
description: "Obtenha respostas para perguntas frequentes sobre a Autenticação de Passagem do Azure Active Directory."
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
ms.openlocfilehash: e0c93cefb135d3ea1c5f0a3797602124aed85c5c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Autenticação de passagem do Azure Active Directory: perguntas frequentes

Neste artigo, respondemos perguntas frequentes sobre a Autenticação de Passagem do Azure AD (Azure Active Directory). Continue verificando para ver novo conteúdo.

## <a name="which-of-the-azure-ad-sign-in-methods---pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs---should-i-choose"></a>Qual dos métodos de entrada no Azure AD – Autenticação de Passagem, Sincronização de Hash de Senha e Serviços de Federação do Active Directory (AD FS) – devo escolher?

Depende de seu ambiente local e dos requisitos organizacionais. Revise este artigo para ver uma [comparação dos vários métodos de entrada do Azure AD](active-directory-aadconnect-user-signin.md).

## <a name="is-pass-through-authentication-a-free-feature"></a>A Autenticação de Passagem é um recurso gratuito?

A Autenticação de Passagem é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo. Ele permanecerá gratuito quando o recurso chegar à disponibilidade geral.

## <a name="is-pass-through-authentication-available-in-microsoft-cloud-germanyhttpwwwmicrosoftdecloud-deutschland-and-microsoft-azure-government-cloudhttpsazuremicrosoftcomfeaturesgov"></a>A Autenticação de Passagem está disponível na [Microsoft Cloud Alemanha](http://www.microsoft.de/cloud-deutschland) e na [Nuvem do Microsoft Azure Governamental](https://azure.microsoft.com/features/gov/)?

Não, a Autenticação de Passagem está disponível apenas na instância mundial do Azure AD.

## <a name="does-conditional-accessactive-directory-conditional-accessmd-work-with-pass-through-authentication"></a>O [Acesso Condicional](../active-directory-conditional-access.md) funciona com a Autenticação de Passagem?

Sim, todos os recursos de Acesso Condicional, incluindo a Autenticação Multifator do Azure, funcionam com a Autenticação de Passagem.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>A Autenticação de Passagem dá suporte à "ID alternativa" como nome de usuário, em vez de "userPrincipalName"?

Sim. A Autenticação de Passagem dá suporte a `Alternate ID` como nome de usuário quando configurada no Azure AD Connect, conforme mostrado [aqui](active-directory-aadconnect-get-started-custom.md). Nem todos os aplicativos do Office 365 dão suporte ao `Alternate ID`. Consulte a documentação específica do aplicativo para obter o demonstrativo de suporte.

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>A Sincronização de Hash de Senha funciona como um fallback da Autenticação de Passagem?

Não, a Sincronização de Hash de Senha não é um fallback genérico da Autenticação de Passagem. Ela funciona como fallback apenas para [cenários a que a Autenticação de Passagem não dá suporte atualmente](active-directory-aadconnect-pass-through-authentication-current-limitations.md#unsupported-scenarios). Para evitar falhas de entrada do usuário, você deve configurar a Autenticação de Passagem para [alta disponibilidade](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="can-i-install-an-azure-ad-application-proxyactive-directory-application-proxy-get-startedmd-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Eu posso instalar um conector de [Proxy de Aplicativo Azure AD](../active-directory-application-proxy-get-started.md) no mesmo servidor que um Agente de Autenticação de Passagem?

Sim, há suporte para essa configuração com as versões remodeladas do Agente de Autenticação de passagem (versões 1.5.193.0 ou posteriores).

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>De quais versões do Azure AD Connect e do Agente de Autenticação de Passagem você precisa?

Você precisa da versão 1.1.486.0 ou posterior para o Azure AD Connect e 1.5.58.0 ou posterior para o Agente de Autenticação de Passagem para que esse recurso funcione. Todo o software deve ser instalado em servidores com Windows Server 2012 R2 ou posterior.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-using-pass-through-authentication"></a>O que acontece se minha senha de usuário tiver expirado e eu tentar entrar usando a Autenticação de Passagem?

Caso você tenha configurado [write-back de senha](../active-directory-passwords-update-your-own-password.md) para um usuário específico e o usuário entrar usando a Autenticação de Passagem, ele poderá alterar ou redefinir sua senha. As senhas serão gravadas de volta no Active Directory local conforme o esperado.

No entanto, se o write-back de senha não estiver configurado para um usuário específico ou se o usuário não tiver uma licença válida do Azure AD atribuída a ele, o usuário não poderá atualizar sua senha na nuvem. Ele não poderá atualizar a senha mesmo que ela tenha expirado. Em vez disso, o usuário verá essa mensagem: “Sua organização não permite que você atualize sua senha neste site. Atualize-a de acordo com o método recomendado pela sua organização ou peça ajuda ao seu administrador se necessário". O usuário ou o administrador precisa redefinir a senha em seu Active Directory local.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Como a Autenticação de Passagem protege você contra ataques de senha de força bruta?

Leia [este artigo](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) para obter mais informações.

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>O que os Agentes de Autenticação de Passagem comunicam pelas portas 80 e 443?

- Os Agentes de Autenticação fazem solicitações HTTPS pela porta 443 para todas as operações de recurso.
- Os Agentes de Autenticação fazem solicitações HTTP pela porta 80 para baixar listas de revogação de certificado SSL.

     >[!NOTE]
     >Em atualizações recentes, reduzimos o número de portas exigidas pelo recurso. Se você tiver versões mais antigas do Azure Connect AD ou do Agente de Autenticação, mantenha estas portas abertas também: 5671, 8080, 9090, 9091, 9350, 9352 e 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Os Agentes de Autenticação de Passagem podem se comunicar através de um servidor proxy da Web de saída?

Sim. Se o WPAD (Descoberta Automática de Proxy da Web) estiver habilitado em seu ambiente local, os Agentes de Autenticação tentarão automaticamente localizar e usar um servidor proxy da Web na rede.

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Eu posso instalar dois ou mais Agentes de Autenticação de Passagem no mesmo servidor?

Não, você só pode instalar um Agente de Autenticação de Passagem em um único servidor. Se quiser configurar a Autenticação de Passagem para alta disponibilidade, siga as instruções neste [artigo](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="i-already-use-active-directory-federation-services-ad-fs-for-azure-ad-sign-in-how-do-i-switch-it-to-pass-through-authentication"></a>Eu já uso os Serviços de Federação do Active Directory (AD FS) para entrar no Azure AD. Como fazer para passar dele para a Autenticação de Passagem?

Se tiver configurado o AD FS como seu método de entrada usando o assistente do Azure AD Connect, altere o método de entrada do usuário para Autenticação de Passagem. Essa alteração habilita a Autenticação de Passagem no locatário e converte _todos_ os seus domínios federados em domínios gerenciados. Todas as solicitações de entrada posteriores em seu locatário serão tratadas pela Autenticação de Passagem. Atualmente, não há uma maneira com suporte no Azure AD Connect de usar uma combinação do AD FS e da Autenticação de Passagem em domínios diferentes.

Se o AD FS tiver sido configurado como o método de entrada _fora_ do assistente do Azure AD Connect, altere o método de entrada do usuário para Autenticação de Passagem (da opção "Não configurar"). Essa alteração habilita a Autenticação de Passagem no locatário. No entanto, todos os seus domínios federados continuarão usando o AD FS para entrar. Use o PowerShell para converter manualmente alguns ou todos esses domínios federados em domínios gerenciados. Depois disso, todas as solicitações de entrada em domínios gerenciados serão tradadas (_somente_) pela autenticação de passagem.

>[!IMPORTANT]
>A Autenticação de Passagem não trata da entrada para usuários do Azure AD somente na nuvem.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-ad-environment"></a>Eu posso usar a Autenticação de Passagem em um ambiente de várias floresta do AD?

Sim. Ambientes de várias florestas têm suporte se houver relações de confiança entre suas florestas do AD e se o encaminhamento de sufixo de nome estiver configurado corretamente.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Quantos Agentes de Autenticação de Passagem preciso instalar?

A instalação de vários Agentes de Autenticação de Passagem garante a [alta disponibilidade](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability). No entanto, ela não fornece balanceamento de carga. Um ou dois Agentes Autenticação poderão acabar tratando a maior parte das solicitações de entrada.

Considere o horário de pico e a carga média de solicitações de entrada que você espera ver no seu locatário. Como um parâmetro de comparação, um único agente de autenticação pode manipular de 300 mil a 400 mil autenticações por segundo em um servidor padrão com CPU de 4 núcleos e 16 GB de RAM. Para a maioria dos clientes, um total de dois ou três agentes de autenticação é o suficiente para alta disponibilidade e capacidade.

Nós recomendamos que você instale os Agentes de Autenticação perto de seus Controladores de Domínio para melhorar a latência de entrada.

## <a name="can-i-install-the-first-pass-through-authentication-agent-on-a-server-other-than-the-one-that-runs-azure-ad-connect"></a>Eu posso instalar o primeiro Agente de Autenticação de Passagem em um servidor diferente daquele que executa o Azure AD Connect?

Não, esse cenário _não_ tem suporte.

## <a name="how-many-pass-through-authentication-agents-should-i-install"></a>Quantos Agentes de Autenticação de Passagem devo instalar?

Recomendamos que:

- Você instale dois ou três Agentes de Autenticação no total. Essa configuração é suficiente para a maioria dos clientes.
- Você instale os Agentes de Autenticação em seus Controladores de Domínio (ou o mais próximo deles possível) para melhorar a latência de entrada.
- Você garanta que os servidores (em que os Agentes de Autenticação estão instalados) sejam adicionados à mesma floresta do AD que os usuários cujas senhas precisam ser validadas.

>[!NOTE]
>Há um limite do sistema de 12 Agentes de Autenticação por locatário.

## <a name="how-can-i-disable-pass-through-authentication"></a>Como posso desabilitar a Autenticação de Passagem?

Execute novamente o assistente Azure AD Connect e altere o método de entrada do usuário de Autenticação de Passagem para outro método. Essa alteração desabilita a Autenticação de Passagem no locatário e desinstala o Agente de Autenticação do servidor. Você precisa desinstalar manualmente os Agentes de Autenticação de outros servidores.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>O que acontece quando eu desinstalo um Agente de Autenticação de Passagem?

Desinstalar um Agente de Autenticação de Passagem de um servidor faz com que ele pare de aceitar solicitações de entrada. Verifique se você tem outro Agente de Autenticação em execução antes de realizar essa operação, a fim de evitar a interrupção de entrada de usuários em seu locatário.

## <a name="next-steps"></a>Próximas etapas
- [**Limitações atuais**](active-directory-aadconnect-pass-through-authentication-current-limitations.md) - Saiba quais cenários têm suporte e quais não têm.
- [**Início rápido**](active-directory-aadconnect-pass-through-authentication-quick-start.md) – instale e execute a autenticação de passagem do Azure AD.
- [**Bloqueio Inteligente**](active-directory-aadconnect-pass-through-authentication-smart-lockout.md) – configura a capacidade de Bloqueio Inteligente no seu locatário para proteger as contas de usuário.
- [**Aprofundamento técnico**](active-directory-aadconnect-pass-through-authentication-how-it-works.md) – entenda como esse recurso funciona.
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
- [**Aprofundamento em Segurança**](active-directory-aadconnect-pass-through-authentication-security-deep-dive.md) – informações técnicas aprofundadas adicionais sobre o recurso.
- [**SSO contínuo do Azure AD**](active-directory-aadconnect-sso.md) – Saiba mais sobre esse recurso complementar.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
