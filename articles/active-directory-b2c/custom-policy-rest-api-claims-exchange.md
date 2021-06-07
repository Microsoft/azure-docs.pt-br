---
title: Trocas de declarações da API REST – Azure Active Directory B2C
description: Adicionar trocas de declarações da API REST a políticas personalizadas no Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 84053df34ffda0d4686ad80a9e5f3af00ac53d72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "94949472"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Passo a passo: Adicionar trocas de declarações da API REST a políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C (Azure Active Directory B2C) permite que o desenvolvedor de identidade integre uma interação com uma API RESTful em um percurso do usuário. Ao final deste passo a passo, você estará apto a criar um percurso do usuário do Azure AD B2C que interage com [serviços RESTful](custom-policy-rest-api-intro.md).

Neste cenário, enriquecemos os dados de token do usuário integrando a um fluxo de trabalho corporativo de linha de negócios. Durante a inscrição ou a entrada com a conta local ou federada, o Azure AD B2C invoca uma API REST para obter os dados de perfil estendido do usuário em uma fonte de dados remota. Neste exemplo, o Azure AD B2C envia o identificador exclusivo do usuário, o objectId. A API REST retorna o saldo da conta do usuário (um número aleatório). Use este exemplo como um ponto de partida para integração com seu próprio sistema CRM, banco de dados de marketing ou qualquer fluxo de trabalho de linha de negócios.

Você também pode projetar a interação como um perfil técnico de validação. Isso é adequado quando a API REST for validar dados na tela e retornar declarações. Saiba mais no [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C para validar a entrada do usuário](custom-policy-rest-api-claims-validation.md).

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas](custom-policy-get-started.md). Você deve ter uma política personalizada funcional para inscrição e conexão com contas locais.
- Saiba como [Integrar trocas de declarações da API REST em sua política personalizada no Azure AD B2C](custom-policy-rest-api-intro.md).

## <a name="prepare-a-rest-api-endpoint"></a>Preparar um ponto de extremidade da API REST

Para esta explicação, você deve ter uma API REST que valide se o objectId do Azure AD B2C de um usuário está registrado no sistema back-end. Se estiver registrado, a API REST retornará o saldo da conta do usuário. Caso contrário, a API REST registrará a nova conta no diretório e retornará o saldo inicial `50.00`.

O código JSON a seguir ilustra os dados que o Azure AD B2C enviará para o ponto de extremidade da API REST. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

Depois que a API REST validar os dados, ela deverá retornar um HTTP 200 (Ok), com os seguintes dados JSON:

```json
{
    "balance": "760.50"
}
```

A configuração do ponto de extremidade da API REST está fora do escopo deste artigo. Criamos um exemplo do [Azure Functions](../azure-functions/functions-reference.md). Você pode acessar o código completo de função do Azure no [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function).

## <a name="define-claims"></a>Definir declarações

Uma declaração fornece armazenamento temporário de dados durante uma execução de política do Azure AD B2C. Você pode definir declarações na seção de [esquema de declarações](claimsschema.md). 

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione as seguintes declarações ao elemento **ClaimsSchema**.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>Adicionar o perfil técnico da API RESTful 

Um[perfil técnico RESTful](restful-technical-profile.md) fornece suporte para a interface com seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful em uma coleção`InputClaims` e recebe dados de volta em uma coleção`OutputClaims`. Localize o elemento **ClaimsProviders** no seu arquivo <em> **`TrustFrameworkExtensions.xml`**</em> e adicione um novo provedor de declarações da seguinte maneira:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

Neste exemplo, `userLanguage` será enviado para o serviço REST como `lang` no conteúdo do JSON. O valor da declaração `userLanguage` contém a ID de idioma do usuário atual. Para obter mais informações, confira [resolvedor de declarações](claim-resolver-overview.md).

### <a name="configure-the-restful-api-technical-profile"></a>Configurar o perfil técnico da API RESTful 

Depois de implantar sua API REST, defina os metadados do `REST-ValidateProfile` perfil técnico para refletir sua própria API REST, incluindo:

- **ServiceUrl**. Defina a URL do ponto de extremidade da API REST.
- **SendClaimsIn**. Especifique como as declarações de entrada são enviadas para o provedor de declarações RESTful.
- **AuthenticationType**. Defina o tipo de autenticação que está sendo executada pelo provedor de declarações RESTful. 
- **AllowInsecureAuthInProduction**. Em um ambiente de produção, certifique-se de definir esses metadados para `true`
    
Consulte os [metadados do perfil técnico RESTful](restful-technical-profile.md#metadata) para obter mais configurações.

Os comentários acima de `AuthenticationType` e `AllowInsecureAuthInProduction` especificam as alterações que você deve fazer ao mudar para um ambiente de produção. Para saber como proteger suas APIs RESTful para produção, confira [Proteger API RESTful](secure-rest-api.md).

## <a name="add-an-orchestration-step"></a>Adicionar uma etapa de orquestração

[Percursos do usuário](userjourneys.md) especificam caminhos explícitos por meio dos quais uma política permite que um aplicativo de terceira parte confiável obtenhas as declarações desejadas para um usuário. Um percurso do usuário é representado como uma sequência de orquestração que deve ser seguida para ter uma transação bem-sucedida. Você pode adicionar ou subtrair etapas de orquestração. Neste caso, você adicionará uma nova etapa de orquestração usada para aumentar as informações fornecidas ao aplicativo após a inscrição ou entrada do usuário por meio da chamada à API REST.

1. Abra o arquivo base da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>.
1. Pesquise o elemento `<UserJourneys>`. Copie o elemento inteiro e, em seguida, exclua-o.
1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Cole o `<UserJourneys>` no arquivo de extensões, após o fechamento do elemento `<ClaimsProviders>`.
1. Localize o `<UserJourney Id="SignUpOrSignIn">` e adicione a etapa de orquestração a seguir antes da última.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. Refatore a última etapa de orquestração alterando `Order` para `8`. Suas duas etapas finais de orquestração devem ser semelhantes a estas:

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. Repita as duas últimas etapas para os percursos do usuário **ProfileEdit** e **PasswordReset**.


## <a name="include-a-claim-in-the-token"></a>Incluir uma declaração no token 

Para retornar a declaração `balance` de volta para o aplicativo de terceira parte confiável, adicione uma declaração de saída ao arquivo <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em>. A adição de uma declaração de saída emitirá a declaração para o token, após um percurso do usuário bem-sucedido, e será enviada para o aplicativo. Modifique o elemento de perfil técnico na seção de terceira parte confiável para adicionar `balance` como uma declaração de saída.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Repita essa etapa para os percursos do usuário **ProfileEdit. xml** e **PasswordReset. xml**.

Salve os arquivos que você alterou: *TrustFrameworkBase.xml* e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* e *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>Teste a política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém o locatário do Azure Active Directory escolhendo o filtro **Diretório + assinatura** no menu superior e escolhendo o diretório que contém o locatário do Azure Active Directory.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Carregar política personalizada** e carregue os arquivos de política alterados: *TrustFrameworkBase.xml* e *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* e *PasswordReset.xml*. 
1. Selecione a política de inscrição ou de entrada carregada e clique no botão **Executar agora**.
1. Você conseguirá se inscrever usando um endereço de email ou uma conta do Facebook.
1. O token enviado de volta ao seu aplicativo inclui a declaração `balance`.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como proteger suas APIs, confira os seguintes arquivos:

- [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração](custom-policy-rest-api-claims-exchange.md)
- [Proteger sua API RESTful](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)