---
title: "Azure Active Directory B2C: mudança da senha de autoatendimento | Microsoft Docs"
description: "Um tópico que demonstra como configurar a alteração de senha por autoatendimento para seus consumidores no Azure Active Directory B2C"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: ajalexander
ms.assetid: 712a7128-5788-4914-8a52-24e200aa4de1
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: b152c22c96da38f8724010504cc2711ab82af00a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-configure-password-change-in-custom-policies"></a>Azure Active Directory B2C: configurar a alteração da senha em políticas personalizadas  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Com o recurso de alteração de senha, os consumidores conectados (usando contas locais) podem alterar as próprias senhas sem precisarem provar sua autenticidade por verificação de email, conforme descrito no [fluxo de redefinição de senha por autoatendimento.](active-directory-b2c-reference-sspr.md) Se a sessão expirar quando o consumidor chegar ao fluxo de alteração de senha, o usuário será solicitado a entrar novamente. 

## <a name="prerequisites"></a>Pré-requisitos

Um locatário do Azure AD B2C configurado para concluir uma inscrição/entrada de conta local, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).

## <a name="how-to-configure-password-change-in-custom-policy"></a>Como configurar a alteração de senha na política personalizada

Para configurar a alteração de senha na política personalizada, faça as seguintes alterações em sua política de extensões de estrutura confiável: 

## <a name="define-a-claimtype-oldpassword"></a>Defina um ClaimType 'oldPassword'

A estrutura geral de sua política personalizada deve incluir um `ClaimsSchema`e definir uma nova `ClaimType` 'oldPassword' como abaixo. 

```XML
  <BuildingBlocks>
    <ClaimsSchema>
      <ClaimType Id="oldPassword">
        <DisplayName>Old Password</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>Enter password</UserHelpText>
        <UserInputType>Password</UserInputType>
      </ClaimType>
    </ClaimsSchema>
  </BuildingBlocks>
```

O propósito desses elementos é o seguinte:

- O `ClaimsSchema` define qual declaração está sendo validada.  Nesse caso, a 'senha antiga' será validada. 

## <a name="add-a-password-change-claims-provider-with-its-supporting-elements"></a>Adicionar um provedor de declarações de alteração de senha com seus elementos de suporte

O provedor de declarações de alteração de senha vai

1. Autenticar o usuário em relação à senha antiga
2. E se 'nova senha' corresponder a 'confirmar nova senha', esse valor será armazenado no repositório de dados B2C e, portanto, a senha será alterada com êxito. 

![img](images/passwordchange.jpg)

Adicione o provedor de declarações a seguir à sua política de extensões. 

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="login-NonInteractive">
          <Metadata>
           <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
           <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
        </TechnicalProfile>
        <TechnicalProfile Id="login-NonInteractive-PasswordChange">
          <DisplayName>Local Account SignIn</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
            <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
            <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
            <Item Key="ProviderName">https://sts.windows.net/</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
            <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
            <Item Key="response_types">id_token</Item>
            <Item Key="response_mode">query</Item>
            <Item Key="scope">email openid</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
            <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
            <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
            <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
            <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Local Account Password Change</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountWritePasswordChangeUsingObjectId">
          <DisplayName>Change password (username)</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
          </CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="oldPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="login-NonInteractive-PasswordChange" />
            <ValidationTechnicalProfile ReferenceId="AAD-UserWritePasswordUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
  </ClaimsProviders>
```



### <a name="add-the-application-ids-to-your-custom-policy"></a>Adicionar as IDs do Aplicativo à política personalizada

Adicione as IDs de aplicativo ao arquivo de extensões (`TrustFrameworkExtensions.xml`):

1. No arquivo de extensões (TrustFrameworkExtensions.xml), localize os elementos `<TechnicalProfile Id="login-NonInteractive">` e `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Substitua todas as instâncias do `IdentityExperienceFrameworkAppId` pela ID do aplicativo Identity Experience Framework conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md). Aqui está um exemplo:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Substitua todas as instâncias do `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo Proxy Identity Experience Framework conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).

4. Salve o arquivo de extensões.



## <a name="create-a-password-change-user-journey"></a>Criar um percurso do usuário de alteração de senha

```XML
 <UserJourneys>
    <UserJourney Id="PasswordChange">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordChangeUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

Agora você concluiu a modificação do arquivo de extensão. Salve e carregue esse arquivo. Certifique-se de que todas as validações tenham êxito.



## <a name="create-a-relying-party-rp-file"></a>Criar um arquivo de RP (terceira parte confiável)

Em seguida, atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou:

1. Faça uma cópia de ProfileEdit.xml em seu diretório de trabalho. Em seguida, renomeie-a (por exemplo, PasswordChange.xml).
2. Abra o novo arquivo e atualize o atributo `PolicyId` de `<TrustFrameworkPolicy>` com um valor exclusivo. Esse é o nome da sua política (por exemplo, PasswordChange).
3. Modifique o atributo `ReferenceId` em `<DefaultUserJourney>` para que corresponda a `Id` do novo percurso do usuário criado (por exemplo, PasswordChange).
4. Salve as alterações e, em seguida, carregue o arquivo.
5. Para testar a política personalizada que você carregou, no portal do Azure, vá até a folha de política e, em seguida, clique em **Executar agora**.




## <a name="link-to-password-change-sample-policy"></a>Vincular à política de exemplo de alteração de senha

Você pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change). 










