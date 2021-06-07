---
title: Configurar a inscrição e a entrada com uma conta do LinkedIn
titleSuffix: Azure AD B2C
description: Forneça a inscrição e entrada aos consumidores com contas do LinkedIn em seus aplicativos usando o Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: dfddc024255d90e8a89f49454e42eb5e94df083a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579970"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Configurar a inscrição e entrada com a conta do LinkedIn usando o Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-linkedin-application"></a>Criar um aplicativo do LinkedIn

Para habilitar a entrada para usuários com uma conta do LinkedIn no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo no [site de desenvolvedores do LinkedIn](https://www.developer.linkedin.com/). Para obter mais informações, consulte [fluxo de código de autorização](/linkedin/shared/authentication/authorization-code-flow). Se você ainda não tiver uma conta do LinkedIn, poderá se inscrever em [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Entre no [site de Desenvolvedores do LinkedIn](https://www.developer.linkedin.com/) com suas credencias de conta do LinkedIn.
1. Selecione **meus aplicativos** e, em seguida, clique em **criar aplicativo**.
1. Insira o **nome do aplicativo**, a página do LinkedIn, a URL **da** política de **privacidade** e o logotipo do **aplicativo**.
1. Concorde com os **termos de uso da API do** LinkedIn e clique em **criar aplicativo**.
1. Selecione a guia **autenticação** . Em **chaves de autenticação**, copie os valores para **ID do cliente** e segredo do **cliente**. Você precisará de ambos para configurar o LinkedIn como um provedor de identidade em seu locatário. O **segredo do cliente** é uma credencial de segurança importante.
1. Selecione o lápis de edição ao lado de **URLs de redirecionamento autorizado para seu aplicativo** e, em seguida, selecione **Adicionar URL de redirecionamento**. Digite `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Se você usar um [domínio personalizado](custom-domain.md), digite `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Substitua `your-tenant-name` pelo nome do seu locatário e `your-domain-name` pelo seu domínio personalizado. Todas as letras que você usar ao inserir o nome do locatário precisarão ser minúsculas, mesmo se o locatário estiver definido com letras maiúsculas no Azure AD B2C. Selecione **Atualizar**.
1. Por padrão, seu aplicativo do LinkedIn não é aprovado para escopos relacionados à entrada. Para solicitar uma revisão, selecione a guia **produtos** e, em seguida, selecione **entrar com o LinkedIn**. Quando a revisão for concluída, os escopos necessários serão adicionados ao seu aplicativo.
   > [!NOTE]
   > Você pode exibir os escopos que atualmente são permitidos para seu aplicativo na guia **autenticação** na seção **escopos do OAuth 2,0** .

::: zone pivot="b2c-user-flow"

## <a name="configure-linkedin-as-an-identity-provider"></a>Configurar o LinkedIn como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador global do locatário Azure AD B2C.
1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C selecionando o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure, procure e selecione **Azure AD B2C**.
1. Selecione **provedores de identidade** e, em seguida, selecione **LinkedIn**.
1. Insira um **Nome**. Por exemplo, *LinkedIn*.
1. Para a **ID do cliente**, insira a ID do cliente do aplicativo do LinkedIn que você criou anteriormente.
1. Para o **segredo do cliente**, insira o segredo do cliente que você registrou.
1. Selecione **Salvar**.

## <a name="add-linkedin-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade do LinkedIn a um fluxo de usuário 

Neste ponto, o provedor de identidade do LinkedIn foi configurado, mas ainda não está disponível em nenhuma das páginas de entrada. Para adicionar o provedor de identidade do LinkedIn a um fluxo de usuário:

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário que você deseja adicionar ao provedor de identidade do LinkedIn.
1. Em **provedores de identidade social**, selecione **LinkedIn**.
1. Selecione **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar fluxo de usuário** .
1. Na página inscrever-se ou entrar, selecione **LinkedIn** para entrar com a conta do LinkedIn.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que registrou anteriormente no seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Escolha o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
5. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
6. Para **Opções**, escolha `Manual`.
7. Insira um **Nome** para a chave de política. Por exemplo, `LinkedInSecret`. O prefixo *B2C_1A_* é adicionado automaticamente ao nome da sua chave.
8. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
9. Para **Uso de chave**, selecione `Signature`.
10. Clique em **Criar**.

## <a name="configure-linkedin-as-an-identity-provider"></a>Configurar o LinkedIn como um provedor de identidade

Para permitir que os usuários entrem usando uma conta do LinkedIn, você precisa definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Defina uma conta do LinkedIn como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra o arquivo *SocialAndLocalAccounts/* * TrustFrameworkExtensions.xml** * em seu editor. Esse arquivo está no [pacote de início de política personalizada][starter-pack] que você baixou como parte de um dos pré-requisitos.
1. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
1. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>linkedin.com</Domain>
      <DisplayName>LinkedIn</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LinkedIn-OAuth2">
          <DisplayName>LinkedIn</DisplayName>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="ProviderName">linkedin</Item>
            <Item Key="authorization_endpoint">https://www.linkedin.com/oauth/v2/authorization</Item>
            <Item Key="AccessTokenEndpoint">https://www.linkedin.com/oauth/v2/accessToken</Item>
            <Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
            <Item Key="scope">r_emailaddress r_liteprofile</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="external_user_identity_claim_id">id</Item>
            <Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
            <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="client_id">Your LinkedIn application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_LinkedInSecret" />
          </CryptographicKeys>
          <InputClaims />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="linkedin.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Substitua o valor de **client_id** pela ID do cliente do aplicativo do LinkedIn que você registrou anteriormente.
1. Salve o arquivo.

### <a name="add-the-claims-transformations"></a>Adicionar as transformações de declarações

O perfil técnico do LinkedIn exige que as transformações de declarações **ExtractGivenNameFromLinkedInResponse** e **ExtractSurNameFromLinkedInResponse** sejam adicionadas à lista de ClaimsTransformations. Se você não tiver um elemento **ClaimsTransformations** definido em seu arquivo, adicione os elementos XML pai, conforme mostrado abaixo. As transformações de declarações também precisam de um novo tipo de declaração definido chamado **nullStringClaim**.

Adicione o elemento **BuildingBlocks** próximo à parte superior do arquivo de *TrustFrameworkExtensions.xml* . Consulte *TrustFrameworkBase.xml* para obter um exemplo.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store output values from ClaimsTransformations that aren't useful. This claim should not be used in TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAuth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione **LinkedIn** para entrar com a conta do LinkedIn.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

## <a name="migration-from-v10-to-v20"></a>Migração de v 1.0 para v 2.0

O LinkedIn [atualizou recentemente suas APIs de v 1.0 para v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Para migrar sua configuração existente para a nova configuração, use as informações nas seções a seguir para atualizar os elementos no perfil técnico.

### <a name="replace-items-in-the-metadata"></a>Substituir itens nos metadados

No elemento de **metadados** existente do **TechnicalProfile**, atualize os seguintes elementos de **Item** de:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v1/people/~:(id,first-name,last-name,email-address,headline)</Item>
<Item Key="scope">r_emailaddress r_basicprofile</Item>
```

Para:

```xml
<Item Key="ClaimsEndpoint">https://api.linkedin.com/v2/me</Item>
<Item Key="scope">r_emailaddress r_liteprofile</Item>
```

### <a name="add-items-to-the-metadata"></a>Adicionar itens aos metadados

Nos **metadados** do **TechnicalProfile**, adicione os seguintes elementos de **Item** :

```xml
<Item Key="external_user_identity_claim_id">id</Item>
<Item Key="BearerTokenTransmissionMethod">AuthorizationHeader</Item>
<Item Key="ResolveJsonPathsInJsonTokens">true</Item>
```

### <a name="update-the-outputclaims"></a>Atualizar o OutputClaims

No **OutputClaims** existente do **TechnicalProfile**, atualize os seguintes elementos do **OutputClaim** de:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
```

Para:

```xml
<OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName.localized" />
<OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName.localized" />
```

### <a name="add-new-outputclaimstransformation-elements"></a>Adicionar novos elementos OutputClaimsTransformation

No **OutputClaimsTransformations** do **TechnicalProfile**, adicione os seguintes elementos de **OutputClaimsTransformation** :

```xml
<OutputClaimsTransformation ReferenceId="ExtractGivenNameFromLinkedInResponse" />
<OutputClaimsTransformation ReferenceId="ExtractSurNameFromLinkedInResponse" />
```

### <a name="define-the-new-claims-transformations-and-claim-type"></a>Definir as novas transformações de declarações e o tipo de declaração

Na última etapa, você adicionou novas transformações de declarações que precisam ser definidas. Para definir as transformações de declarações, adicione-as à lista de **ClaimsTransformations**. Se você não tiver um elemento **ClaimsTransformations** definido em seu arquivo, adicione os elementos XML pai, conforme mostrado abaixo. As transformações de declarações também precisam de um novo tipo de declaração definido chamado **nullStringClaim**.

O elemento **BuildingBlocks** deve ser adicionado próximo à parte superior do arquivo. Consulte o *TrustframeworkBase.xml* como exemplo.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <!-- Claim type needed for LinkedIn claims transformations -->
    <ClaimType Id="nullStringClaim">
      <DisplayName>nullClaim</DisplayName>
      <DataType>string</DataType>
      <AdminHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</AdminHelpText>
      <UserHelpText>A policy claim to store unuseful output values from ClaimsTransformations. This claim should not be used in a TechnicalProfiles.</UserHelpText>
    </ClaimType>
  </ClaimsSchema>

  <ClaimsTransformations>
    <!-- Claim transformations needed for LinkedIn technical profile -->
    <ClaimsTransformation Id="ExtractGivenNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="givenName" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="ExtractSurNameFromLinkedInResponse" TransformationMethod="GetSingleItemFromJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="surname" TransformationClaimType="inputJson" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="nullStringClaim" TransformationClaimType="key" />
        <OutputClaim ClaimTypeReferenceId="surname" TransformationClaimType="value" />
      </OutputClaims>
    </ClaimsTransformation>
  </ClaimsTransformations>
</BuildingBlocks>
```

### <a name="obtain-an-email-address"></a>Obter um endereço de email

Como parte da migração do LinkedIn de v 1.0 para v 2.0, uma chamada adicional para outra API é necessária para obter o endereço de email. Se você precisar obter o endereço de email durante a inscrição, faça o seguinte:

1. Conclua as etapas acima para permitir que Azure AD B2C federar com o LinkedIn para permitir que o usuário entre. Como parte da Federação, Azure AD B2C recebe o token de acesso para o LinkedIn.
1. Salve o token de acesso do LinkedIn em uma declaração. [Consulte as instruções aqui](idp-pass-through-user-flow.md).
1. Adicione o provedor de declarações a seguir que faz a solicitação para a API do LinkedIn `/emailAddress` . Para autorizar essa solicitação, você precisa do token de acesso do LinkedIn.

    ```xml
    <ClaimsProvider>
      <DisplayName>REST APIs</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="API-LinkedInEmail">
          <DisplayName>Get LinkedIn email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
              <Item Key="ServiceUrl">https://api.linkedin.com/v2/emailAddress?q=members&amp;projection=(elements*(handle~))</Item>
              <Item Key="AuthenticationType">Bearer</Item>
              <Item Key="UseClaimAsBearerToken">identityProviderAccessToken</Item>
              <Item Key="SendClaimsIn">Url</Item>
              <Item Key="ResolveJsonPathsInJsonTokens">true</Item>
          </Metadata>
          <InputClaims>
              <InputClaim ClaimTypeReferenceId="identityProviderAccessToken" />
          </InputClaims>
          <OutputClaims>
              <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="elements[0].handle~.emailAddress" />
          </OutputClaims>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. Adicione a seguinte etapa de orquestração em seu percurso do usuário, para que o provedor de declarações da API seja disparado quando um usuário entrar usando o LinkedIn. Certifique-se de atualizar o `Order` número adequadamente. Adicione essa etapa imediatamente após a etapa de orquestração que dispara o perfil técnico do LinkedIn.

    ```xml
    <!-- Extra step for LinkedIn to get the email -->
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="false">
          <Value>identityProvider</Value>
          <Value>linkedin.com</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="GetEmail" TechnicalProfileReferenceId="API-LinkedInEmail" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

A obtenção do endereço de email do LinkedIn durante a inscrição é opcional. Se você optar por não obter o email do LinkedIn, mas exigir um durante a inscrição, o usuário será solicitado a inserir manualmente o endereço de email e validá-lo.

Para obter uma amostra completa de uma política que usa o provedor de identidade do LinkedIn, consulte o [pacote de início de política personalizada](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/linkedin-identity-provider).

<!-- Links - EXTERNAL -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="migration-from-v10-to-v20"></a>Migração de v 1.0 para v 2.0

O LinkedIn [atualizou recentemente suas APIs de v 1.0 para v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Como parte da migração, Azure AD B2C só é capaz de obter o nome completo do usuário do LinkedIn durante a inscrição. Se um endereço de email for um dos atributos coletados durante a inscrição, o usuário deverá inserir manualmente o endereço de email e validá-lo.

::: zone-end