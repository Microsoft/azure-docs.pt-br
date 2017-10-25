---
title: "Azure AD Connect: Autenticação de passagem – atualização de agentes de autenticação de versão prévia | Microsoft Docs"
description: "Este artigo descreve como atualizar sua configuração da Autenticação de passagem do Azure AD (Azure Active Directory)."
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
ms.date: 08/04/2017
ms.author: billmath
ms.openlocfilehash: 0a7293f2b3a366b25e780ee75601dfbb2b35ddaa
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-upgrade-preview-authentication-agents"></a>Autenticação de passagem do Azure Active Directory: atualização de agentes de autenticação de versão prévia

## <a name="overview"></a>Visão geral

Este artigo é para clientes que usam a Autenticação de passagem do Azure AD por meio da versão prévia. Nós recentemente atualizamos (e remodelamos) o software do Agente de autenticação. Você precisa atualizar _manualmente_ os Agentes de autenticação da versão prévia instalados nos seus servidores locais. Esta atualização manual é uma ação que deve ser realizada uma única vez. Todas as atualizações futuras dos Agentes de autenticação serão automáticas. Os motivos para atualizar são os seguintes:

- As versões prévias dos Agentes de autenticação não receberão mais correções de segurança ou de bugs.
-   As versões prévias dos Agentes de autenticação não podem ser instaladas em servidores adicionais para obter alta disponibilidade.

## <a name="check-versions-of-your-authentication-agents"></a>Verificar as versões dos seus Agentes de autenticação

### <a name="step-1-check-where-your-authentication-agents-are-installed"></a>Etapa 1: verificar o local em que os Agentes de autenticação estão instalados

Siga essas etapas para verificar o local em que os Agentes de autenticação estão instalados:

1. Entre no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais do Administrador Global do seu locatário.
2. Selecione **Azure Active Directory** na navegação à esquerda.
3. Selecione **Azure AD Connect**. 
4. Selecione **Autenticação de passagem**. Essa folha lista os servidores em que os Agentes de autenticação estão instalados.

![Centro de administração do Azure Active Directory - folha Autenticação de Passagem](./media/active-directory-aadconnect-pass-through-authentication/pta8.png)

### <a name="step-2-check-the-versions-of-your-authentication-agents"></a>Etapa 2: verificar as versões dos seus Agentes de autenticação

Para verificar as versões dos seus Agentes de autenticação em cada servidor identificado na etapa anterior, siga estas instruções:

1. Acesse **Painel de controle -> Programas -> Programas e recursos** no servidor local.
2. Se houver uma entrada para "**Agente de autenticação do Microsoft Azure AD Connect**", você não precisará realizar nenhuma ação nesse servidor.
3. Se houver uma entrada para "**Conector de Proxy de Aplicativo do Microsoft Azure AD**", versões 1.5.132.0 ou anteriores, você precisará atualizar manualmente nesse servidor.

![Versão prévia do Agente de autenticação](./media/active-directory-aadconnect-pass-through-authentication/pta6.png)

## <a name="best-practices-to-follow-before-starting-the-upgrade"></a>Melhores práticas a serem seguidas antes de iniciar a atualização

Antes de atualizar, verifique se você tem os seguintes itens no lugar:

1. **Criar conta Administrador Global somente de nuvem**: não atualize sem ter uma conta Administrador Global somente de nuvem para ser usada em situações de emergência em que seus Agentes de Autenticação de passagem não estejam funcionando adequadamente. Saiba mais sobre [adicionar uma conta de Administrador Global somente de nuvem](../active-directory-users-create-azure-portal.md). A realização dessa etapa é fundamental e garante que você não ficará bloqueado do seu locatário.
2.  **Garantir a alta disponibilidade**: se não concluído anteriormente, instale um segundo Agente de autenticação autônomo para fornecer alta disponibilidade para solicitações de conexão, usando essas [instruções](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability).

## <a name="upgrading-the-authentication-agent-on-your-azure-ad-connect-server"></a>Atualizando o Agente de autenticação no servidor do Azure AD Connect

Você precisa atualizar o Azure AD Connect antes de atualizar o Agente de autenticação no mesmo servidor. Siga estas etapas em seus servidores primários e de preparo do Azure AD Connect:

1. **Atualizar o Azure AD Connect**: siga esse [artigo](./active-directory-aadconnect-upgrade-previous-version.md) e atualize para a versão mais recente do Azure AD Connect.
2. **Desinstalar a versão prévia do Agente de autenticação**: baixe [este script do PowerShell](https://aka.ms/rmpreviewagent) e execute-o como um Administrador no servidor.
3. **Baixe a versão mais recente do Agente de Autenticação (versões 1.5.193.0 ou posteriores)**: entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de Administrador Global do seu locatário. Selecione **Azure Active Directory -> Azure AD Connect -> Autenticação de passagem -> Baixar agente**. Aceite os [termos de serviço](https://aka.ms/authagenteula) e baixe a versão mais recente do Agente de autenticação. Você também pode baixar o Agente de Autenticação [aqui](https://aka.ms/getauthagent).
4. **Instalar a versão mais recente do Agente de autenticação**: execute o executável baixado na Etapa 3. Forneça as suas credenciais de Administrador Global do locatário quando solicitado.
5. **Verificar se a versão mais recente foi instalada**: conforme mostrado antes, acesse **Painel de controle -> Programas -> Programas e recursos** e verifique se há uma entrada para "**Agente de autenticação do Microsoft Azure AD Connect**".

>[!NOTE]
>Se você verificar a folha Autenticação de Passagem no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) após a conclusão das etapas anteriores, verá duas entradas do Agente de Autenticação por servidor - uma entrada mostrando o Agente de Autenticação como **Ativo** e o outro como **Inativo**. Isso é _esperado_. A entrada **Inativo** é descartada automaticamente depois de alguns dias.

## <a name="upgrading-the-authentication-agent-on-other-servers"></a>Atualizar o Agente de autenticação em outros servidores

Siga estas etapas para atualizar os Agentes de autenticação em outros servidores (em que o Azure AD Connect não está instalado):

1. **Desinstalar a versão prévia do Agente de autenticação**: baixe [este script do PowerShell](https://aka.ms/rmpreviewagent) e execute-o como um Administrador no servidor.
2. **Baixe a versão mais recente do Agente de Autenticação (versões 1.5.193.0 ou posteriores)**: entre no [Centro de administração do Azure Active Directory](https://aad.portal.azure.com) com as credenciais de Administrador Global do seu locatário. Selecione **Azure Active Directory -> Azure AD Connect -> Autenticação de passagem -> Baixar agente**. Aceite os termos do serviço e baixe a versão mais recente.
3. **Instalar a versão mais recente do Agente de autenticação**: execute o executável baixado na Etapa 2. Forneça as suas credenciais de Administrador Global do locatário quando solicitado.
4. **Verificar se a versão mais recente foi instalada**: conforme mostrado antes, acesse **Painel de controle -> Programas -> Programas e recursos** e verifique se há uma entrada chamada "**Agente de autenticação do Microsoft Azure AD Connect**".

>[!NOTE]
>Se você verificar a folha Autenticação de Passagem no [centro de administração do Azure Active Directory](https://aad.portal.azure.com) após a conclusão das etapas anteriores, verá duas entradas do Agente de Autenticação por servidor - uma entrada mostrando o Agente de Autenticação como **Ativo** e o outro como **Inativo**. Isso é _esperado_. A entrada **Inativo** é descartada automaticamente depois de alguns dias.

## <a name="next-steps"></a>Próximas etapas
- [**Solução de problemas**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) – Saiba como resolver problemas comuns do recurso.
