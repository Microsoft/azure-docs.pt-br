---
title: Definir um perfil técnico RESTful em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico RESTful em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eb6d82019cccd1da327461cb0a0635aea4f3647f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102174964"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Defina um perfil técnico RESTful em uma política personalizada do Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure Active Directory B2C (Azure AD B2C) fornece suporte para integrar seu próprio serviço RESTful. O Azure AD B2C envia dados para o serviço RESTful na entrada de uma coleção de declarações e recebe dados de volta em uma coleção de declarações de saída. Para obter mais informações, consulte [integrar as trocas de declarações da API REST em sua política personalizada do Azure ad B2C](custom-policy-rest-api-intro.md).  

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `Proprietary`. O atributo **manipulador** deve conter o nome totalmente qualificado do assembly do manipulador de protocolo usado pelo Azure AD B2C: `Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`.

O exemplo a seguir mostra um perfil técnico RESTful:

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>Declarações de entrada

O elemento **InputClaims** contém uma lista de declarações a serem enviadas para a API REST. Você também pode mapear o nome de sua declaração para o nome definido na API REST. O exemplo a seguir mostra o mapeamento entre sua política e a API REST. A declaração **givenName** é enviada para a API REST como **firstName**, enquanto **surname** é enviado como **lastName**. O A declaração de **email** é definida como está.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

O elemento **InputClaimsTransformations** elemento pode conter uma coleção de elementos **InputClaimsTransformation** elementos usados para modificar as declarações de entrada ou gerar novas antes do envio à API REST.

## <a name="send-a-json-payload"></a>Enviar uma carga JSON

O perfil técnico da API REST permite enviar uma carga JSON complexa para um ponto de extremidade.

Para enviar uma carga JSON complexa:

1. Crie sua carga JSON com a transformação declarações [GenerateJson](json-transformations.md) .
1. No perfil técnico da API REST:
    1. Adicione uma transformação de declarações de entrada com uma referência à `GenerateJson` transformação declarações.
    1. Defina a `SendClaimsIn` opção de metadados como `body`
    1. Defina a `ClaimUsedForRequestPayload` opção de metadados como o nome da declaração que contém a carga JSON.
    1. Na declaração de entrada, adicione uma referência à declaração de entrada que contém a carga JSON.

O exemplo a seguir `TechnicalProfile` envia um email de verificação usando um serviço de email de terceiros (neste caso, SendGrid).

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações retornadas pela API REST. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido na API REST. Você também pode incluir declarações que não são retornadas pelo provedor de identidade da API REST, desde que defina o atributo `DefaultValue`.

O elemento **OutputClaimsTransformations** pode conter uma coleção de elementos **OutputClaimsTransformation** usados para modificar as declarações de saída ou gerar novas declarações.

O exemplo a seguir mostra a declaração retornada pela API REST:

- A declaração **MembershipId** mapeada para o nome da declaração **loyaltyNumber**.

O perfil técnico também retorna declarações que não são retornadas pelo provedor de identidade:

- A declaração **loyaltyNumberIsNew** que tem um valor padrão definido como `true`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>Metadados

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ServiceUrl | Sim | A URL do ponto de extremidade da API REST. |
| AuthenticationType | Sim | O tipo de autenticação que está sendo executada pelo provedor de declarações RESTful. Valores possíveis: `None` ,,, `Basic` `Bearer`  `ClientCertificate` ou `ApiKeyHeader` . <br /><ul><li>O `None` valor indica que a API REST é anônima. </li><li>O valor `Basic` indica que a API REST está protegida com a autenticação Básica HTTP. Somente usuários verificados, incluindo Azure AD B2C, podem acessar a API. </li><li>O `ClientCertificate` valor (recomendado) indica que a API REST restringe o acesso usando a autenticação de certificado do cliente. Somente os serviços que têm os certificados apropriados, por exemplo Azure AD B2C, podem acessar sua API. </li><li>O `Bearer` valor indica que a API REST restringe o acesso usando o token de portador OAuth2 do cliente. </li><li>O `ApiKeyHeader` valor indica que a API REST está protegida com o cabeçalho HTTP da chave de API, como *x-Functions-Key*. </li></ul> |
| AllowInsecureAuthInProduction| Não| Indica se o `AuthenticationType` pode ser definido como `none` no ambiente de produção ( `DeploymentMode` de [TrustFrameworkPolicy](trustframeworkpolicy.md) é definido como `Production` , ou não especificado). Valores possíveis: true ou false (padrão). |
| SendClaimsIn | Não | Especifica como as declarações de entrada são enviadas para o provedor de declarações RESTful. Valores possíveis: `Body` (padrão), `Form` , `Header` `Url` ou `QueryString` . O valor `Body` é a declaração de entrada enviada no corpo da solicitação no formato JSON. O valor `Form` é a declaração de entrada enviada no corpo da solicitação em um formato de valor de chave separado de e comercial '&'. O valor `Header` é a declaração de entrada enviada no cabeçalho da solicitação. O `Url` valor é a declaração de entrada que é enviada na URL, por exemplo, https://{claim1}. example. com/{claim2}/{claim3}? { claim4} = {claim5}. O valor `QueryString` é a declaração de entrada enviada na cadeia de caracteres de consulta da solicitação. Os verbos HTTP invocados por cada um são os seguintes:<br /><ul><li>`Body`: Postar</li><li>`Form`: Postar</li><li>`Header`: Obter</li><li>`Url`: Obter</li><li>`QueryString`: Obter</li></ul> |
| ClaimsFormat | Não | Não usado no momento, pode ser ignorado. |
| ClaimUsedForRequestPayload| Não | Nome de uma declaração de cadeia de caracteres que contém a carga a ser enviada para a API REST. |
| DebugMode | Não | Executa o perfil técnico no modo de depuração. Valores possíveis: `true` ou `false` (padrão). No modo de depuração, a API REST pode retornar mais informações. Consulte a seção [retornando mensagem de erro](#returning-validation-error-message) . |
| IncludeClaimResolvingInClaimsHandling  | Não | Para declarações de entrada e saída, especifica se a [resolução de declarações](claim-resolver-overview.md) está incluída no perfil técnico. Valores possíveis: `true` ou `false` (padrão). Se você quiser usar um resolvedor de declarações no perfil técnico, defina como `true` . |
| ResolveJsonPathsInJsonTokens  | Não | Indica se o perfil técnico resolve caminhos JSON. Valores possíveis: `true` ou `false` (padrão). Use esses metadados para ler dados de um elemento JSON aninhado. Em um [OutputClaim](technicalprofiles.md#output-claims), defina o `PartnerClaimType` como o elemento de caminho JSON que você deseja gerar. Por exemplo: `firstName.localized` , ou `data.0.to.0.email` .|
| UseClaimAsBearerToken| Não| O nome da declaração que contém o token de portador.|

## <a name="error-handling"></a>Tratamento de erros

Os metadados a seguir podem ser usados para configurar as mensagens de erro exibidas na falha da API REST. A mensagem de erro pode ser [localizada](localization-string-ids.md#restful-service-error-messages).

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | Não | Uma mensagem de erro personalizada padrão para todas as exceções da API REST.|
| UserMessageIfCircuitOpen | Não | Mensagem de erro quando a API REST não está acessível. Se não for especificado, o DefaultUserMessageIfRequestFailed será retornado. |
| UserMessageIfDnsResolutionFailed | Não | Mensagem de erro para a exceção de resolução de DNS. Se não for especificado, o DefaultUserMessageIfRequestFailed será retornado. | 
| UserMessageIfRequestTimeout | Não | Mensagem de erro quando a conexão atingir o tempo limite. Se não for especificado, o DefaultUserMessageIfRequestFailed será retornado. | 

## <a name="cryptographic-keys"></a>Chaves criptográficas

Se o tipo de autenticação for definido como `None`, o elemento **CryptographicKeys** não será usado.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

Se o tipo de autenticação for definido como `Basic`, o elemento **CryptographicKeys** conterá os seguintes atributos:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | Sim | O nome de usuário usado para autenticar. |
| BasicAuthenticationPassword | Sim | A senha usada para autenticar. |

O exemplo a seguir mostra um perfil técnico com a autenticação Básica:

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido como `ClientCertificate`, o elemento **CryptographicKeys** conterá o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| ClientCertificate | Sim | O certificado X509 (conjunto de chaves RSA) a ser usado para autenticar. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido como `Bearer`, o elemento **CryptographicKeys** conterá o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | Não | O token de portador OAuth 2,0. |

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

Se o tipo de autenticação for definido como `ApiKeyHeader`, o elemento **CryptographicKeys** conterá o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| O nome do cabeçalho HTTP, como `x-functions-key` , ou `x-api-key` . | Sim | A chave que é usada para autenticar. |

> [!NOTE]
> Neste momento, Azure AD B2C dá suporte a apenas um cabeçalho HTTP para autenticação. Se sua chamada RESTful exigir vários cabeçalhos, como uma ID do cliente e um segredo do cliente, você precisará fazer o proxy da solicitação de alguma maneira.

```xml
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ApiKeyHeader</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-validation-error-message"></a>Retornando mensagem de erro de validação

A API REST talvez precise retornar uma mensagem de erro, como "O usuário não foi encontrado no sistema CRM". Se ocorrer um erro, a API REST deverá retornar uma mensagem de erro HTTP 4xx, como o código de status de resposta 400 (solicitação inadequada) ou 409 (conflito). O corpo da resposta contém a mensagem de erro formatada em JSON:

```json
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| version | Sim | Sua versão da API REST. Por exemplo: 1.0.1 |
| status | Sim | Deve ser 409 |
| code | Não | Um código de erro do provedor de ponto de extremidade RESTful, que é exibido quando `DebugMode` está habilitado. |
| requestId | Não | Um identificador de solicitação do provedor de ponto de extremidade RESTful, que é exibido quando `DebugMode` está habilitado. |
| userMessage | Sim | Uma mensagem de erro que é mostrada ao usuário. |
| developerMessage | Não | A descrição detalhada do problema e como corrigi-lo, o que é exibido quando `DebugMode` está habilitado. |
| moreInfo | Não | Um URI que aponta para informações adicionais, que são exibidas quando `DebugMode` está habilitado. |


O exemplo a seguir mostra uma classe C# que retorna uma mensagem de erro:

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para obter exemplos de como usar um perfil técnico RESTful:

- [Integrar trocas de declarações da API REST em sua política personalizada no Azure AD B2C](custom-policy-rest-api-intro.md)
- [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação da entrada do usuário](custom-policy-rest-api-claims-validation.md)
- [Passo a passo: Adicionar trocas de declarações da API REST a políticas personalizadas no Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteger seus serviços de API REST](secure-rest-api.md)
