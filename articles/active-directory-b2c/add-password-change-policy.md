---
title: Configurar a alteração de senha usando políticas personalizadas
titleSuffix: Azure AD B2C
description: Saiba como permitir que usuários alterem suas senhas usando políticas personalizadas no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/17/2020
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a42cb97d123d0943dab02bf1f70fcf306d6bcd96
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/17/2020
ms.locfileid: "97629112"
---
# <a name="configure-password-change-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a alteração da senha usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

No Azure Active Directory B2C (Azure AD B2C), você pode habilitar os usuários que entraram com uma conta local para alterar a senha sem precisar provar sua autenticidade por verificação de email. O fluxo de alteração de senha envolve as seguintes etapas:

1. Entre com uma conta local. Se a sessão ainda estiver ativa, o Azure AD B2C autoriza o usuário e pula para a próxima etapa.
1. Os usuários devem verificar a **senha antiga**, criar e confirmar a **nova senha**.

![Fluxo de alteração de senha](./media/add-password-change-policy/password-change-flow.png)

## <a name="prerequisites"></a>Pré-requisitos

* Conclua as etapas em [Introdução às políticas personalizadas no Active Directory B2C](custom-policy-get-started.md).
* Se você ainda não fez isso, [registre um aplicativo Web em Azure Active Directory B2C](tutorial-register-applications.md).

## <a name="add-the-elements"></a>Adicionar os elementos

1. Abre seu arquivo *TrustframeworkExtensions.xml* e adicione o seguinte elemento **ClaimType** com um identificador de `oldPassword` para o elemento [ClaimsSchema](claimsschema.md):

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="oldPassword">
          <DisplayName>Old Password</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Enter your old password</UserHelpText>
          <UserInputType>Password</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Um elemento [ClaimsProvider](claimsproviders.md) contém o perfil técnico que autentica o usuário. Adicione os seguintes provedores de declarações ao elemento **ClaimsProviders**:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive-PasswordChange">
            <DisplayName>Local Account SignIn</DisplayName>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="oldPassword" PartnerClaimType="password" Required="true" />
              </InputClaims>
            <IncludeTechnicalProfile ReferenceId="login-NonInteractive" />
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

3. O elemento [UserJourney](userjourneys.md) define o caminho usado pelo usuário ao interagir com seu aplicativo. Adicione o elemento **UserJourneys** se ele não existir com o **UserJourney** identificado como `PasswordChange`:

    ```xml
    <UserJourneys>
      <UserJourney Id="PasswordChange">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.signuporsignin">
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
          <OrchestrationStep Order="4" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="5" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

4. Salve o arquivo de política *TrustFrameworkExtensions.xml*.
5. Copie o arquivo *ProfileEdit.xml* que você baixou com o pacote de início e nomeie-o *ProfileEditPasswordChange.xml*.
6. Abra o novo arquivo e atualize o atributo **PolicyId** com um valor exclusivo. Esse valor é o nome da sua política. Por exemplo, *B2C_1A_profile_edit_password_change*.
7. Modifique o atributo **ReferenceId** em `<DefaultUserJourney>` para corresponder à ID do novo percurso do usuário criado. Por exemplo, *PasswordChange*.
8. Salve as alterações.

## <a name="upload-and-test-the-policy"></a>Carregar e testar a política

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Na página de políticas personalizadas, clique em **Carregar Política**.
6. Habilite **Substituir a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
7. Clique em **Carregar**.
8. Repita as etapas 5 a 7 para o arquivo de terceira parte confiável, como *ProfileEditPasswordChange.xml*.

### <a name="run-the-policy"></a>Executar a política

1. Abra a política que você alterou. Por exemplo, *B2C_1A_profile_edit_password_change*.
2. Para **Aplicativo**, selecione seu aplicativo que você registrou anteriormente. Para ver o token, a **URL de resposta** deve mostrar `https://jwt.ms`.
3. Clique em **Executar agora**. Entre com a conta que você criou anteriormente. Agora você deve conseguir alterar a senha.

## <a name="next-steps"></a>Próximas etapas

- Localize a política de exemplo no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/password-change).
- Saiba mais sobre como você pode [Configurar a complexidade de senha no Azure ad B2C](password-complexity.md).
- Configure um [fluxo de redefinição de senha](add-password-reset-policy.md).

::: zone-end
