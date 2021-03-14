---
title: Acesso condicional para usuários de colaboração B2B – Azure AD
description: Saiba como impor políticas de autenticação multifator para usuários Azure Active Directory B2B.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74bfa4987f584bbd3490bc5f4f187dee5bc1bd87
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646275"
---
# <a name="conditional-access-for-b2b-collaboration-users"></a>Acesso condicional para usuários de colaboração B2B

Este artigo descreve como as organizações podem fazer o escopo de políticas de acesso condicional (AC) para usuários convidados B2B acessarem seus recursos.
>[!NOTE]
>Esse fluxo de autenticação ou autorização é um pouco diferente para usuários convidados do que para os usuários existentes do IdP (provedor de identidade).

## <a name="authentication-flow-for-b2b-guest-users-from-an-external-directory"></a>Fluxo de autenticação para usuários convidados B2B de um diretório externo

O diagrama a seguir ilustra o Flow: ![ Image mostra o fluxo de autenticação para usuários convidados B2B de um diretório externo](./media/conditional-access-b2b/authentication-flow-b2b-guests.png)

| Etapa | Descrição |
|--------------|-----------------------|
| 1. | O usuário convidado B2B solicita acesso a um recurso. O recurso redireciona o usuário para seu locatário de recursos, um IdP confiável.|
| 2. | O locatário do recurso identifica o usuário como externo e redireciona o usuário para o IdP do usuário convidado B2B. O usuário executa a autenticação primária no IdP.
| 3. | O IdP do usuário convidado B2B emite um token para o usuário. O usuário é Redirecionado de volta para o locatário do recurso com o token. O locatário do recurso valida o token e, em seguida, avalia o usuário em relação às políticas da autoridade de certificação. Por exemplo, o locatário de recursos pode exigir que o usuário execute a autenticação multifator do Azure Active Directory (AD).
| 4. | Depois que todas as políticas de AC do locatário de recursos forem satisfeitas, o locatário de recursos emitirá seu próprio token e redirecionará o usuário para seu recurso.

## <a name="authentication-flow-for-b2b-guest-users-with-one-time-passcode"></a>Fluxo de autenticação para usuários convidados B2B com senha de uso único

O diagrama a seguir ilustra o Flow: ![ Image mostra o fluxo de autenticação para usuários convidados B2B com senha ocasional](./media/conditional-access-b2b/authentication-flow-b2b-guests-otp.png)

| Etapa | Descrição |
|--------------|-----------------------|
| 1. |O usuário solicita acesso a um recurso em outro locatário. O recurso redireciona o usuário para seu locatário de recursos, um IdP confiável.|
| 2. | O locatário do recurso identifica o usuário como um [usuário externo de senha de uso único (OTP) de email](./one-time-passcode.md) e envia um email com a OTP para o usuário.|
| 3. | O usuário recupera a OTP e envia o código. O locatário do recurso avalia o usuário em relação às políticas de autoridade de certificação.
| 4. | Depois que todas as políticas de autoridade de certificação forem satisfeitas, o locatário de recursos emitirá um token e redirecionará o usuário para seu recurso. |

>[!NOTE]
>Se o usuário for de um locatário de recurso externo, não será possível que as políticas de AC do IdP do usuário convidado B2B também sejam avaliadas. A partir de hoje, somente as políticas de autoridade de certificação do locatário de recursos se aplicam a seus convidados.

## <a name="azure-ad-multi-factor-authentication-for-b2b-users"></a>Autenticação multifator do Azure AD para usuários B2B

As organizações podem impor várias políticas de autenticação multifator do Azure AD para seus usuários convidados B2B. Essas políticas podem ser impostas no nível de locatário, aplicativo ou usuário individual da mesma maneira que estão habilitadas para funcionários e membros de tempo integral da organização.
O locatário de recursos é sempre responsável pela autenticação multifator do Azure AD para usuários, mesmo que a organização do usuário convidado tenha recursos de autenticação multifator. Aqui está um exemplo –

1. Um administrador ou um operador de informações em uma empresa denominada Fabrikam convida o usuário de outra empresa chamada contoso para usar seu aplicativo Woodgrove.

2. O aplicativo do Woodgrove na Fabrikam está configurado para exigir a autenticação multifator do Azure AD no Access.

3. Quando o usuário convidado B2B da Contoso tenta acessar o Woodgrove no locatário da Fabrikam, ele é solicitado a concluir o desafio da autenticação multifator do Azure AD.

4. O usuário convidado pode então configurar a autenticação multifator do Azure AD com a Fabrikam e selecionar as opções.

5. Esse cenário funciona para qualquer identidade – Azure AD ou MSA (conta pessoal da Microsoft). Por exemplo, se o usuário no contoso se autenticar usando a ID social.

6. A Fabrikam deve ter licenças Premium do Azure AD suficientes que dão suporte à autenticação multifator do Azure AD. O usuário da Contoso consome essa licença da Fabrikam. Consulte [modelo de cobrança para identidades externas do Azure ad](./external-identities-pricing.md) para obter informações sobre o licenciamento B2B.

>[!NOTE]
>A autenticação multifator do Azure AD é feita no locatário de recursos para garantir a previsibilidade.

### <a name="set-up-azure-ad-multi-factor-authentication-for-b2b-users"></a>Configurar a autenticação multifator do Azure AD para usuários B2B

Para configurar a autenticação multifator do Azure AD para usuários de colaboração B2B, Assista a este vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-conditional-access-setup/Player]

### <a name="b2b-users-azure-ad-multi-factor-authentication-for-offer-redemption"></a>Usuários B2B da autenticação multifator do Azure AD para resgate de oferta

Para saber mais sobre a experiência de resgate da autenticação multifator do Azure AD, Assista a este vídeo:

>[!VIDEO https://channel9.msdn.com/Blogs/Azure/MFA-redemption/Player]

### <a name="azure-ad-multi-factor-authentication-reset-for-b2b-users"></a>Redefinição da autenticação multifator do Azure AD para usuários B2B

Agora, os seguintes cmdlets do PowerShell estão disponíveis para verificar os usuários convidados B2B:

1. Conecte-se ao AD do Azure

   ```
   $cred = Get-Credential
   Connect-MsolService -Credential $cred
   ```
2. Obter todos os usuários com métodos de verificação

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```
   Veja um exemplo:

   ```
   Get-MsolUser | where { $_.StrongAuthenticationMethods} | select UserPrincipalName, @{n="Methods";e={($_.StrongAuthenticationMethods).MethodType}}
   ```

3. Redefina o método de autenticação multifator do Azure AD para um usuário específico exigir que o usuário de colaboração B2B defina os métodos de verificação novamente. 
   Veja um exemplo:

   ```
   Reset-MsolStrongAuthenticationMethodByUpn -UserPrincipalName gsamoogle_gmail.com#EXT#@ WoodGroveAzureAD.onmicrosoft.com
   ```

## <a name="conditional-access-for-b2b-users"></a>Acesso condicional para usuários B2B

Há vários fatores que influenciam as políticas de CA para usuários convidados B2B.

### <a name="device-based-conditional-access"></a>Acesso Condicional baseado no dispositivo

Na CA, há uma opção para exigir que o dispositivo do usuário [esteja em conformidade ou ingressado no Azure ad híbrido](../conditional-access/concept-conditional-access-conditions.md#device-state-preview). Os usuários convidados B2B só poderão satisfazer a conformidade se o locatário de recursos puder gerenciar seus dispositivos. Os dispositivos não podem ser gerenciados por mais de uma organização por vez. Os usuários convidados B2B não podem atender à junção híbrida do Azure AD porque não têm uma conta do AD local. Somente se o dispositivo do usuário convidado não for gerenciado, ele poderá registrar ou registrar seu dispositivo no locatário do recurso e, em seguida, tornar o dispositivo compatível. Em seguida, o usuário pode satisfazer o controle Grant.

>[!Note]
>Não é recomendável exigir um dispositivo gerenciado para usuários externos.

### <a name="mobile-application-management-policies"></a>Políticas de gerenciamento de aplicativos móveis

Os controles de concessão de autoridade de certificação, como **exigir aplicativos cliente aprovados** e **exigir políticas de proteção de aplicativo** , precisam que o dispositivo seja registrado no locatário. Esses controles só podem ser aplicados a [dispositivos IOS e Android](../conditional-access/concept-conditional-access-conditions.md#device-platforms). No entanto, nenhum desses controles pode ser aplicado aos usuários convidados B2B se o dispositivo do usuário já estiver sendo gerenciado por outra organização. Um dispositivo móvel não pode ser registrado em mais de um locatário por vez. Se o dispositivo móvel for gerenciado por outra organização, o usuário será bloqueado. Somente se o dispositivo do usuário convidado não for gerenciado, ele poderá registrar seu dispositivo no locatário do recurso. Em seguida, o usuário pode satisfazer o controle Grant.  

>[!NOTE]
>Não é recomendável exigir uma política de proteção de aplicativo para usuários externos.

### <a name="location-based-conditional-access"></a>Acesso condicional com base no local

A [política baseada em local](../conditional-access/concept-conditional-access-conditions.md#locations) com base em intervalos de IP pode ser imposta se a organização que está convidando puder criar um intervalo de endereços IP confiável que defina suas organizações parceiras.

As políticas também podem ser impostas com base em **locais geográficos**.

### <a name="risk-based-conditional-access"></a>Acesso Condicional com base em risco

A [política de risco de entrada](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk) é imposta se o usuário convidado B2B satisfizer o controle Grant. Por exemplo, uma organização pode exigir a autenticação multifator do Azure AD para risco de entrada média ou alta. No entanto, se um usuário não tiver sido registrado anteriormente para a autenticação multifator do Azure AD no locatário do recurso, o usuário será bloqueado. Isso é feito para impedir que usuários mal-intencionados registrem suas próprias credenciais de autenticação multifator do Azure AD no caso de comprometer a senha de um usuário legítimo.

No entanto [, a política de risco do usuário](../conditional-access/concept-conditional-access-conditions.md#user-risk) não pode ser resolvida no locatário do recurso. Por exemplo, se você precisar de uma alteração de senha para usuários convidados de alto risco, eles serão bloqueados devido à incapacidade de redefinir senhas no diretório de recursos.

### <a name="conditional-access-client-apps-condition"></a>Condição de aplicativos cliente de acesso condicional

As [condições de aplicativos cliente](../conditional-access/concept-conditional-access-conditions.md#client-apps) se comportam da mesma forma para os usuários convidados B2B como no caso de qualquer outro tipo de usuário. Por exemplo, você pode impedir que usuários convidados usem protocolos de autenticação herdados.

### <a name="conditional-access-session-controls"></a>Controles de sessão de acesso condicional

Os [controles de sessão](../conditional-access/concept-conditional-access-session.md) se comportam da mesma forma para os usuários convidados B2B como eles fazem para qualquer outro tipo de usuário.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos sobre a colaboração B2B do Azure AD:

- [O que é a colaboração B2B do AD do Azure?](./what-is-b2b.md)
- [Usuários do Identity Protection e B2B](../identity-protection/concept-identity-protection-b2b.md)
- [Preço de identidades externas](https://azure.microsoft.com/pricing/details/active-directory/)
- [Perguntas frequentes (FAQs)](./faq.md)