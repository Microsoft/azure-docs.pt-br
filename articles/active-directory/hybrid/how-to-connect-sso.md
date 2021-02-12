---
title: 'Azure AD Connect: Logon Único Contínuo | Microsoft Docs'
description: Este tópico descreve o Logon Único Contínuo do Azure AD (Azure Active Directory) e como ele permite que você forneça o verdadeiro logon único para usuários de área de trabalho corporativos dentro da sua rede corporativa.
services: active-directory
keywords: o que é o Azure AD Connect, instalar o Active Directory, componentes necessários do Azure AD, SSO, Logon Único
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88eae702782e2f1af9c20797676214db458c2adc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937627"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Logon Único Contínuo do Azure Active Directory

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>O que é o Logon Único Contínuo do Azure Active Directory?

O SSO Contínuo do Azure AD (Logon Único Contínuo do Azure Active Directory) conecta usuários automaticamente quando estiverem nos respectivos dispositivos corporativos conectados à sua rede corporativa. Quando habilitado, os usuários não precisam digitar as senhas para entrar no Azure AD e, geralmente, nem precisam digitar os nomes de usuário. Esse recurso fornece aos usuários acesso fácil a seus aplicativos baseados em nuvem sem a necessidade de nenhum componente local adicional.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

O SSO Contínuo pode ser combinado com o método de entrada de [Sincronização de Hash de Senha](how-to-connect-password-hash-synchronization.md) ou de [Autenticação de Passagem](how-to-connect-pta.md). O SSO contínuo _não_ é aplicável aos Serviços de Federação do Active Directory (AD FS).

![Logon Único Contínuo](./media/how-to-connect-sso/sso1.png)

## <a name="sso-via-primary-refresh-token-vs-seamless-sso"></a>SSO por meio do token de atualização primário vs. SSO contínuo

Para o Windows 10, é recomendável usar o SSO por meio do token de atualização primário (PRT). Para o Windows 7 e 8,1, é recomendável usar o SSO contínuo.
O SSO contínuo precisa que o dispositivo do usuário seja ingressado no domínio, mas não é usado em [dispositivos ingressados](../devices/concept-azure-ad-join.md) no Azure AD do Windows 10 ou em [dispositivos ingressados no Azure ad híbrido](../devices/concept-azure-ad-join-hybrid.md). SSO no Azure AD ingressado, ingressado no Azure AD híbrido e dispositivos registrados no Azure AD funcionam com base no [token de atualização primário (PRT)](../devices/concept-primary-refresh-token.md)

O SSO via PRT funciona depois que os dispositivos são registrados com o Azure AD para ingressado no Azure AD híbrido, dispositivos de registro pessoal ou ingressados no Azure AD por meio de adicionar conta corporativa ou de estudante. Para obter mais informações sobre como o SSO funciona com o Windows 10 usando o PRT, consulte: [token de atualização principal (PRT) e Azure ad](../devices/concept-primary-refresh-token.md)


## <a name="key-benefits"></a>Principais benefícios

- *Ótima experiência do usuário*
  - Os usuários são conectados automaticamente aos aplicativos baseados em nuvem e locais.
  - Os usuários não precisam digitar suas senhas repetidamente.
- *Fácil de implantar e administrar*
  - Não há necessidade de nenhum componente adicional local para fazer com que ele funcione.
  - Funciona com qualquer método de autenticação de nuvem – [Sincronização de hash de senha](how-to-connect-password-hash-synchronization.md) ou [Autenticação de passagem](how-to-connect-pta.md).
  - Pode ser distribuído a alguns ou todos os seus usuários usando a Política de Grupo.
  - Registre dispositivos não Windows 10 no Azure AD sem a necessidade de nenhuma infraestrutura do AD FS. Esse recurso deve usar a versão 2.1 ou posterior do [cliente workplace-join](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>Destaques do recurso

- O nome de usuário de conexão pode ser o nome de usuário local padrão (`userPrincipalName`) ou outro atributo configurado no Azure AD Connect (`Alternate ID`). Ambos casos de uso funcionam porque o SSO Contínuo usa a declaração `securityIdentifier` no tíquete do Kerberos para pesquisar o objeto de usuário correspondente no Azure AD.
- O SSO Contínuo é um recurso oportunista. Se ele falhar por qualquer motivo, a experiência de entrada do usuário retornará ao comportamento normal, ou seja, o usuário precisará digitar sua senha na página de entrada.
- Se um aplicativo (por exemplo, `https://myapps.microsoft.com/contoso.com`) encaminhar um parâmetro `domain_hint` (OpenID Connect) ou `whr` (SAML) - identificando seu locatário, ou um parâmetro `login_hint` - identificando o usuário, na solicitação de entrada do Azure Active Directory, os usuários serão automaticamente conectados sem inserir nomes de usuário ou senhas.
- Os usuários também terão uma experiência de logon silenciosa se um aplicativo (por exemplo, `https://contoso.sharepoint.com`) enviar solicitações de entrada para pontos de extremidade do Azure Active Directory configurados como locatários (ou seja, `https://login.microsoftonline.com/contoso.com/<..>` ou `https://login.microsoftonline.com/<tenant_ID>/<..>`) em vez do ponto de extremidade comum do Microsoft Azure AD, ou seja, `https://login.microsoftonline.com/common/<...>`.
- Há suporte para saída. Isso permite que os usuários escolham outra conta do Azure AD para conectar, em vez de conectar automaticamente usando o SSO contínuo automaticamente.
- Microsoft 365 clientes Win32 (Outlook, Word, Excel e outros) com versões 16.0.8730. xxxx e superior têm suporte usando um fluxo não interativo. Para o OneDrive, você precisará ativar o [recurso de Configuração silenciosa do OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) para uma experiência de logon silenciosa.
- Isso pode ser habilitado por meio do Azure AD Connect.
- Essa é um recurso gratuito e você não precisa de nenhuma edição paga do Azure AD para usá-lo.
- Há suporte para ele em clientes baseados em navegador da Web e clientes do Office que dão suporte à [autenticação moderna](/office365/enterprise/modern-auth-for-office-2013-and-2016) em plataformas e navegadores que sejam compatíveis com a autenticação Kerberos:

| Sistema operacional\Navegador |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Sim\*|Sim|Sim|Sim\*\*\*|N/D
|Windows 8.1|Sim\*|Ok\*\*\*|Sim|Sim\*\*\*|N/D
|Windows 8|Sim\*|N/D|Sim|Sim\*\*\*|N/D
|Windows 7|Sim\*|N/D|Sim|Sim\*\*\*|N/D
|Windows Server 2012 R2 ou acima|Sim\*\*|N/D|Sim|Sim\*\*\*|N/D
|Mac OS X|N/D|N/D|Sim\*\*\*|Sim\*\*\*|Sim\*\*\*


\*Requer o Internet Explorer versão 10 ou posterior.

\*\*Requer o Internet Explorer versão 10 ou posterior. Desabilitar o modo protegido avançado.

\*\*\*Requer [configuração adicional](how-to-connect-sso-quick-start.md#browser-considerations).

\*\*\*\*Requer o Microsoft Edge versão 77 ou posterior.

## <a name="next-steps"></a>Próximas etapas

- [**Início Rápido**](how-to-connect-sso-quick-start.md) – colocar o SSO Contínuo do Azure AD em funcionamento.
- [**Plano de Implantação**](../manage-apps/plan-sso-deployment.md) - Plano de implantação passo a passo.
- [**Aprofundamento técnico**](how-to-connect-sso-how-it-works.md) – entenda como esse recurso funciona.
- [**Perguntas frequentes**](how-to-connect-sso-faq.md) – respostas para perguntas frequentes.
- [**Solução de problemas**](tshoot-connect-sso.md) – Saiba como resolver problemas comuns do recurso.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – para registrar solicitações de novos recursos.
