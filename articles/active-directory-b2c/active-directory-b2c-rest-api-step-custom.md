---
title: Azure Active Directory B2C - as trocas de declarações da API REST
description: Adicione trocas de declarações da API REST para políticas personalizadas no B2C do Active Directory.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 0bdef508e12a3b11143149b330da73838b53f860
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439010"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>Adicionar trocas de declarações da API REST para políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Você pode adicionar a interação com uma API RESTful para seus [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure Active Directory (Azure AD) B2C. Este artigo mostra como criar um percurso do usuário do Azure AD B2C que interage com serviços RESTful.

A interação inclui uma troca de declarações de informações entre as declarações da API REST e Azure AD B2C. As trocas de declarações têm as seguintes características:

- Pode ser projetada como uma etapa de orquestração.
- Pode disparar uma ação externa. Por exemplo, ela pode registrar um evento em um banco de dados externo.
- Pode ser usada para buscar um valor e, em seguida, armazená-lo no banco de dados do usuário.
- Pode alterar o fluxo de execução.

O cenário que é representado neste artigo inclui as seguintes ações:

1. Procurar pelo usuário em um sistema externo.
2. Obter a cidade em que o usuário está registrado.
3. Retornar o atributo para o aplicativo como uma declaração.

## <a name="prerequisites"></a>Pré-requisitos

- Conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).
- Um ponto de extremidade de API REST com o qual se irá interagir. Este artigo utilizará uma simple Azure funcione como um exemplo. Para criar a função do Azure, consulte [criar sua primeira função no portal do Azure](../azure-functions/functions-create-first-azure-function.md).

## <a name="prepare-the-api"></a>Preparar a API

Nesta seção, você prepara a função do Azure para receber um valor para `email`e, em seguida, retornar o valor para `city` que pode ser usado pelo Azure AD B2C como uma declaração.

Altere o arquivo Run. CSx para a função do Azure que você criou para usar o código a seguir:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>Configurar a troca de declarações

Um perfil técnico fornece a configuração para a troca de declarações.

Abra o *trustframeworkextensions. XML* do arquivo e adicione o seguinte **ClaimsProvider** elemento XML dentro a **ClaimsProviders** elemento.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

O **InputClaims** elemento define as declarações que são enviadas para o serviço REST. Neste exemplo, o valor da declaração `givenName` é enviada para o serviço REST como a declaração `email`. O **OutputClaims** elemento define as declarações que são esperadas do serviço REST.

## <a name="add-the-claim-definition"></a>Adicione a definição de declaração

Adicione uma definição para `city` dentro de **BuildingBlocks** elemento. Você encontra esse elemento no início do arquivo TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>Adicionar uma etapa de orquestração

Há muitos casos de uso em que a chamada à API REST pode ser usada como uma etapa de orquestração. Como uma etapa de orquestração, ela pode ser usada como uma atualização para um sistema externo depois que um usuário tenha concluído uma tarefa com êxito, como o primeiro registro, ou como uma atualização de perfil para manter as informações sincronizadas. Nesse caso, ela é usada para aumentar as informações fornecidas para o aplicativo depois da edição do perfil.

Adicione uma etapa para o percurso de usuário de edição de perfil. Depois que o usuário é autenticado (etapas de orquestração 1 a 4 no XML a seguir) e o usuário tenha fornecido as informações de perfil atualizado (etapa 5). Copie o perfil de edição de código XML do percurso de usuário da *Trustframeworkbase* do arquivo para seu *trustframeworkextensions. XML* dentro do arquivo a **UserJourneys** elemento. Em seguida, fazer a modificação como a etapa 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

O XML final para o percurso do usuário deve parecer com este exemplo:

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>Adicione a declaração

Editar o *Profileedit* arquivo e adicione `<OutputClaim ClaimTypeReferenceId="city" />` para o **OutputClaims** elemento.

Depois de adicionar a nova declaração, o perfil técnico se parece com este exemplo:

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>carregar suas alterações e testar

1. (Opcional:) Salve a versão existente (Baixando) dos arquivos antes de continuar.
2. Carregar o *trustframeworkextensions. XML* e *Profileedit* e selecione para substituir o arquivo existente.
3. Selecione **B2C_1A_ProfileEdit**.
4. Para **selecione o aplicativo** na página de visão geral da política personalizada, selecione o aplicativo web chamado *webapp1* que você registrou anteriormente. Certifique-se de que o **URL de resposta** é `https://jwt.ms`.
4. Selecione **executar agora**. Entre com suas credenciais de conta e, em seguida, clique em **continuar**.

Se tudo está configurado corretamente, o token inclui a nova declaração `city`, com o valor `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Próximas etapas

- Você também pode projetar a interação como um perfil de validação. Para saber mais, confira [Passo a passo: integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação na entrada do usuário](active-directory-b2c-rest-api-validation-custom.md).
- [Modificar a edição de perfil para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
