---
title: Adicionar atributos de usuário e personalizar a entrada do usuário
titleSuffix: Azure AD B2C
description: Saiba como personalizar a entrada do usuário e adicionar atributos de usuário à jornada de inscrição ou entrada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 4e709719d56aacacf61e247a5dbe215f766a891a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102607944"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Adicionar atributos de usuário e personalizar a entrada do usuário no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

Neste artigo, você coleta um novo atributo durante a sua jornada de inscrição no Azure Active Directory B2C (Azure AD B2C). Você obterá a cidade dos usuários, o configurará como uma lista suspensa e definirá se ele precisa ser fornecido.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>Adicionar atributos de usuário seu fluxo de usuário

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **atributos de usuário** e, em seguida, selecione o atributo de usuário (por exemplo, "cidade"). 
1. Clique em **Salvar**.

## <a name="provide-optional-claims-to-your-app"></a>Fornecer declarações opcionais para seu aplicativo

As declarações de aplicativo são valores que são retornados para o aplicativo. Atualize seu fluxo de usuário para conter as declarações desejadas.

1. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **Declarações do aplicativo**.
1. Selecione os atributos que você deseja enviar de volta para seu aplicativo (por exemplo, "cidade")..
1. Clique em **Salvar**.
 
## <a name="configure-user-attributes-input-type"></a>Configurar tipo de entrada de atributos de usuário

1. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione **layouts de página**.
1. Selecione **a página de inscrição da conta local**.
1. Em **atributos de usuário**, selecione **cidade**.
    1. Na lista suspensa **tipo de entrada do usuário** , selecione **DropdownSingleSelect**.
    1. Na lista suspensa **opcional** , selecione **não**.
1. Clique em **Salvar**. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>Forneça uma lista de valores usando coleções localizadas

Para fornecer uma lista definida de valores para o atributo City: 

1. [Habilitar a personalização de idioma no fluxo do usuário](language-customization.md#support-requested-languages-for-ui_locales)
1. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Na página **Idiomas** do fluxo de usuário, selecione o idioma que você quer personalizar.
1. Em **nível de página – arquivos de recursos**, selecione **página de inscrição de conta local**.
1. Selecione **Baixar Padrões** (ou **Baixar substituições**, se esse idioma já foi editado anteriormente).
1. Crie um `LocalizedCollections` atributo.

O `LocalizedCollections` é uma matriz de `Name` `Value` pares e. A ordem dos itens será a ordem em que elas são exibidas. 

* `ElementId` é o atributo do usuário ao qual esse atributo `LocalizedCollections` é uma resposta.
* `Name` é o valor mostrado para o usuário.
* `Value` é o que é retornado na declaração quando essa opção é selecionada.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": false,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>Carregamento das suas alterações

1. Após concluir as alterações no arquivo JSON, retorne para o locatário B2C.
1. Selecione **fluxos de usuário** e selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Selecione os **Idiomas**.
1. Selecione o idioma para o qual você deseja traduzir.
1. Selecione a **página de inscrição da conta local**.
1. Selecione o ícone da pasta e selecione o arquivo JSON para upload. As alterações são salvas no fluxo de usuário automaticamente.

## <a name="test-your-user-flow"></a>Testar seu fluxo de usuário

1. Selecione sua política (por exemplo, "B2C_1_SignupSignin") para abri-la.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**

::: zone-end

::: zone pivot="b2c-custom-policy"

> [!NOTE]
> Este exemplo usa a declaração interna ' City '. Em vez disso, você pode escolher um dos [atributos internos de Azure ad B2C](user-profile-attributes.md) com suporte ou um atributo personalizado. Para usar um atributo personalizado, [habilite atributos personalizados](user-flow-custom-attributes.md). Para usar um atributo interno ou personalizado diferente, substitua ' City ' pelo atributo de sua escolha, por exemplo, o atributo interno *JobTitle* ou um atributo personalizado como *extension_loyaltyId*.  

Você pode coletar dados iniciais de seus usuários usando a jornada do usuário de inscrição ou de entrada. Declarações adicionais podem ser coletadas posteriormente usando um percurso do usuário de edição de perfil. A qualquer momento Azure AD B2C coleta informações diretamente do usuário de forma interativa, a estrutura de experiência de identidade usa seu [perfil técnico autodeclarado](self-asserted-technical-profile.md). Neste exemplo, você:

1. Defina uma declaração de "cidade". 
1. Peça ao usuário sua cidade.
1. Mantenha a cidade para o perfil do usuário no diretório Azure AD B2C.
1. Leia a declaração de cidade do diretório Azure AD B2C em cada entrada.
1. Retorne a cidade para seu aplicativo de terceira parte confiável após a entrada ou inscrição.  

## <a name="define-a-claim"></a>Definir uma declaração

Uma declaração fornece um armazenamento temporário de dados durante uma execução de política de Azure AD B2C. O [esquema de declarações](claimsschema.md) é o lugar em que você declara suas declarações. Os elementos a seguir são usados para definir a declaração:

- **DisplayName** – Uma cadeia de caracteres que define o rótulo voltado para o usuário.
- [DataType](claimsschema.md#datatype) -o tipo da declaração.
- **UserHelpText** – Ajuda o usuário a entender o que é necessário.
- [Userinputtype](claimsschema.md#userinputtype) – o tipo de controle de entrada, como caixa de texto, seleção de rádio, lista suspensa ou várias seleções.

Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione a declaração de cidade ao elemento **ClaimsSchema** .  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
        <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
        <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

## <a name="add-a-claim-to-the-user-interface"></a>Adicionar uma declaração à interface do usuário

Os seguintes perfis técnicos são [autodeclarados](self-asserted-technical-profile.md), invocados quando um usuário espera fornecer entrada:

- **LocalAccountSignUpWithLogonEmail** -fluxo de inscrição de conta local.
- **SelfAsserted-social** -entrada do usuário da primeira vez da conta federada.
- **SelfAsserted-ProfileUpdate** -editar fluxo de perfil.

Para coletar a declaração de cidade durante a inscrição, ela deve ser adicionada como uma declaração de saída ao `LocalAccountSignUpWithLogonEmail` perfil técnico. Substitua esse perfil técnico no arquivo de extensão. Especifique a lista completa de declarações de saída para controlar a ordem em que as declarações são apresentadas na tela. Localize o elemento **ClaimsProviders**. Adicione um novo ClaimsProviders da seguinte maneira:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Para coletar a declaração de cidade após a entrada inicial com uma conta federada, ela deve ser adicionada como uma declaração de saída ao `SelfAsserted-Social` perfil técnico. Para que os usuários da conta local e federada possam editar seus dados de perfil posteriormente, adicione as declarações de entrada e saída ao `SelfAsserted-ProfileUpdate` perfil técnico. Substitua esses perfis técnicos no arquivo de extensão. Especifique a lista completa das declarações de saída para controlar a ordem em que as declarações são apresentadas na tela. Localize o elemento **ClaimsProviders**. Adicione um novo ClaimsProviders da seguinte maneira:

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Ler e gravar uma declaração

Os perfis técnicos a seguir são [Active Directory perfis técnicos](active-directory-technical-profile.md), que lêem e gravam dados em Azure Active Directory.  
Use `PersistedClaims` para gravar dados no perfil do usuário e `OutputClaims` para ler dados do perfil do usuário dentro dos respectivos perfis técnicos do Active Directory.

Substitua esses perfis técnicos no arquivo de extensão. Localize o elemento **ClaimsProviders**.  Adicione um novo ClaimsProviders da seguinte maneira:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Incluir uma declaração no token 

Para retornar a declaração de cidade de volta para o aplicativo de terceira parte confiável, adicione uma declaração de saída ao <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> arquivo. A declaração de saída será adicionada ao token após um percurso de usuário bem-sucedido e será enviada para o aplicativo. Modifique o elemento de perfil técnico na seção terceira parte confiável para adicionar a cidade como uma declaração de saída.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Estrutura de Experiência de Identidade**.
5. Selecione **Carregar política personalizada** e carregue os dois arquivos de política alterados.
2. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
3. Você deverá conseguir se inscrever usando um endereço de email.

::: zone-end

A tela de inscrição deve ser semelhante à seguinte captura:

![Captura de tela da opção de inscrição modificada](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

O token enviado de volta ao seu aplicativo inclui a declaração `city`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o elemento [ClaimsSchema](claimsschema.md) na referência do IEF.
- Saiba como [usar atributos personalizados no Azure ad B2C](user-flow-custom-attributes.md).

::: zone-end
