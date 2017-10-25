---
title: "Azure Active Directory B2C: Gerenciar a personalização de SSO e de tokens com políticas personalizadas | Microsoft Docs"
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/02/2017
ms.author: sama
ms.openlocfilehash: 8f5703d15766f221517cd89352d41685652d32d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: Gerenciar a personalização de SSO e de tokens com políticas personalizadas
Usar políticas personalizadas fornece algum controle sobre seu token, sessão e configurações de logon único (SSO) por meio de políticas internas.  Para saber o que faz cada configuração, veja a documentação [aqui](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Configuração de declarações e de tempos de vida de token
Para alterar as configurações em seus tempos de vida de token, você precisa adicionar um elemento `<ClaimsProviders>` ao arquivo de terceira parte confiável da política que você deseja afetar.  O elemento `<ClaimsProviders>` é filho do `<TrustFrameworkPolicy>`.  Lá, você precisará colocar as informações que afetam os tempos de vida de token.  O XML tem esta aparência:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

**Tempos de vida de token de acesso** O tempo de vida de token de acesso pode ser alterado modificando o valor dentro do `<Item>` com Key="token_lifetime_secs" em segundos.  O valor padrão interno é de 3600 segundos (60 minutos).

**Tempo de vida do token de ID** O tempo de vida do token de ID pode ser alterado por meio da modificação do valor de `<Item>` comKey="id_token_lifetime_secs" em segundos.  O valor padrão interno é de 3600 segundos (60 minutos).

**Tempo de vida do token de atualização** O tempo de vida do token de atualização pode ser alterado por meio da modificação do valor de `<Item>` com Key="refresh_token_lifetime_secs" em segundos.  O valor padrão interno é 1209600 segundos (14 dias).

**Atualizar o tempo de vida de janela deslizante token** Se você deseja definir um tempo de vida de janela deslizante para o token de atualização, modifique o valor de `<Item>` com Key="rolling_refresh_token_lifetime_secs" em segundos.  O valor padrão interno é 7776000 (90 dias).  Se você não quiser impor um tempo de vida de janela deslizante, substitua essa linha por:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Declaração do emissor (iss)** Se você quiser alterar a declaração do emissor (iss), modifique o valor no `<Item>` com Key="IssuanceClaimPattern".  Os valores aplicáveis são `AuthorityAndTenantGuid` e `AuthorityWithTfp`.

**Configuração de declaração que representa a ID de política** As opções para definir esse valor são TFP (política de confiança da estrutura) e ACR (referência do contexto de autenticação).  
É recomendável definir isso como TFP; para fazer isso, verifique se o `<Item>` com Key="AuthenticationContextReferenceClaimPattern" existe e se o valor é `None`.
No seu item do `<OutputClaims>`, adicione este elemento:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
Para o ACR, remova o `<Item>` com Key="AuthenticationContextReferenceClaimPattern".

**Declaração de assunto (sub)** Essa opção é padronizada para a ObjectID, se você quiser trocar para `Not Supported`, faça o seguinte:

Substituir esta linha 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
com esta linha:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Comportamento da sessão e SSO
Para alterar o comportamento da sessão e as configurações de SSO, será necessário adicionar um elemento `<UserJourneyBehaviors>` ao elemento `<RelyingParty>`.  O elemento `<UserJourneyBehaviors>` deve seguir imediatamente o `<DefaultUserJourney>`.  O interior do seu elemento `<UserJourneyBehavors>` deve ter esta aparência:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Configuração de logon único (SSO)** Para alterar a configuração de logon único, você precisa modificar o valor de `<SingleSignOn>`.  Os valores aplicáveis são `Tenant`, `Application`, `Policy` e `Disabled`. 

**Tempo de vida de sessão do aplicativo Web (minutos)** Para alterar o tempo de vida de sessão do aplicativo Web, você precisa modificar o valor do elemento `<SessionExpiryInSeconds>`.  O valor padrão em políticas internas é de 86400 segundos (1440 minutos).

**Tempo limite de sessão de aplicativo Web** Para alterar o tempo limite de sessão do aplicativo Web, será necessário modificar o valor de `<SessionExpiryType>`.  Os valores aplicáveis são `Absolute` e `Rolling`.
