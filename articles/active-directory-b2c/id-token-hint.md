---
title: Definir um perfil técnico de dicas de token de ID em uma política personalizada
titleSuffix: Azure AD B2C
description: Defina um perfil técnico de dica de ID de identificação em uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d77e145cabcef2931d5fe6e76599da7931e576e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97669152"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definir um perfil técnico de dicas de token de ID em uma política personalizada de Azure Active Directory B2C

Azure AD B2C permite que aplicativos de terceira parte confiável enviem um JWT de entrada como parte da solicitação de autorização OAuth2. O token JWT pode ser emitido por um aplicativo de terceira parte confiável ou por um provedor de identidade e pode passar uma dica sobre o usuário ou a solicitação de autorização. Azure AD B2C valida a assinatura, o nome do emissor e o público-alvo do token e extrai a declaração do token de entrada.

## <a name="use-cases"></a>Casos de uso

Você pode usar essa solução para enviar dados para Azure AD B2C encapsulados em um único token JWT. A [solução inscrição com convite por email](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md), na qual o administrador do sistema pode enviar um convite assinado aos usuários, é baseado em id_token_hint. Somente os usuários com acesso ao email de convite podem criar a conta no diretório.

## <a name="token-signing-approach"></a>Abordagem de assinatura de token

Com id_token_hint, o emissor do token (um aplicativo de terceira parte confiável ou um provedor de identidade) compõe o token e, em seguida, o assina usando uma chave de assinatura para provar que o token é proveniente de uma fonte confiável. A chave de assinatura pode ser simétrica ou assimétrica. A criptografia simétrica ou a criptografia de chave privada usa um segredo compartilhado para assinar e validar a assinatura. A criptografia assimétrica ou a criptografia de chave pública é um sistema criptográfico que usa uma chave privada e uma chave pública. A chave privada é conhecida apenas pelo emissor do token e é usada para assinar o token. A chave pública é compartilhada com a política de Azure AD B2C para validar a assinatura do token.

## <a name="token-format"></a>Formato do token

O id_token_hint deve ser um token JWT válido. A tabela a seguir lista as declarações que são obrigatórias. Declarações adicionais são opcionais.

| Nome | Declaração | Valor de exemplo | Descrição |
| ---- | ----- | ------------- | ----------- |
| Público | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | Identifica o destinatário pretendido do token. O público-alvo é uma cadeia de caracteres arbitrária definida pelo emissor do token. Azure AD B2C valida esse valor e rejeita o token se ele não corresponder.  |
| Emissor | `iss` |`https://localhost` | Identifica o serviço de token de segurança (emissor do token). O emissor é um URI arbitrário definido pelo emissor do token. Azure AD B2C valida esse valor e rejeita o token se ele não corresponder.  |
| Hora de expiração | `exp` | `1600087315` | A hora em que o token se torna inválido, representada na época. Azure AD B2C valida esse valor e rejeita o token se o token tiver expirado.|
| Não antes de | `nbf` | `1599482515` | O horário em que o token se torna inválido, representado no horário da época. Esse horário geralmente é o mesmo no qual o token foi emitido. Azure AD B2C valida esse valor e rejeita o token se o tempo de vida do token não for válido. |

 O token a seguir é um exemplo de um token de ID válido:

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>Protocolo

O atributo **Name** do elemento **Protocol** precisa ser definido como `None`. Por exemplo, o protocolo para o perfil técnico de **IdTokenHint_ExtractClaims** é `None` :

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

O perfil técnico é chamado de uma etapa de orquestração com o tipo de `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>Declarações de saída

O elemento **OutputClaims** contém uma lista de declarações a serem extraídas do token JWT. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido no token JWT. Você também pode incluir declarações que não são retornadas pelo token JWT, desde que você defina o `DefaultValue` atributo.

## <a name="metadata"></a>Metadados

Os metadados a seguir são relevantes ao usar a chave simétrica. 

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| emissor | Sim | Identifica o serviço de token de segurança (emissor do token). Esse valor deve ser idêntico à `iss` declaração dentro da declaração de token JWT. | 
| IdTokenAudience | Sim | Identifica o destinatário pretendido do token. Deve ser idêntico à `aud` declaração dentro da declaração de token JWT. | 

Os metadados a seguir são relevantes ao usar uma chave assimétrica. 

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| METADATA| Sim | Uma URL que aponta para um documento de configuração de emissor de token, que também é conhecido como um ponto de extremidade de configuração conhecido do OpenID.   |
| emissor | Não | Identifica o serviço de token de segurança (emissor do token). Esse valor pode ser usado para substituir o valor configurado nos metadados e deve ser idêntico à `iss` declaração dentro da declaração de token JWT. |  
| IdTokenAudience | Não | Identifica o destinatário pretendido do token. Deve ser idêntico à `aud` declaração dentro da declaração de token JWT. |  

## <a name="cryptographic-keys"></a>Chaves criptográficas

Ao usar uma chave simétrica, o elemento **CryptographicKeys** contém o seguinte atributo:

| Atributo | Obrigatório | Descrição |
| --------- | -------- | ----------- |
| client_secret | Sim | A chave de criptografia usada para validar a assinatura de token JWT.|


## <a name="how-to-guide"></a>Guia de instruções

### <a name="issue-a-token-with-symmetric-keys"></a>Emitir um token com chaves simétricas

#### <a name="step-1-create-a-shared-key"></a>Etapa 1. Criar uma chave compartilhada 

Crie uma chave que possa ser usada para assinar o token. Por exemplo, use o código do PowerShell a seguir para gerar uma chave.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

Esse código cria uma cadeia de caracteres secreta como `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>Etapa 2. Adicionar a chave de assinatura a Azure AD B2C

A mesma chave usada pelo emissor do token precisa ser criada em suas chaves de política de Azure AD B2C.  

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, procure e selecione **Azure AD B2C**.
1. Na página Visão Geral, em **Políticas**, selecione **Identity Experience Framework**.
1. Selecionar **chaves de política** 
1. Selecione **manual**.
1. Para o **Nome**, use `IdTokenHintKey`.  
   O prefixo `B2C_1A_` pode ser adicionado automaticamente.
1. Na caixa **segredo** , insira a chave de entrada que você gerou anteriormente.
1. Para o **Uso da chave**, use **Criptografia**.
1. Selecione **Criar**.
1. Confirme que você criou a chave `B2C_1A_IdTokenHintKey`.


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>Etapa 3. Adicionar o perfil técnico de dica de token de ID

O seguinte perfil técnico valida o token e extrai as declarações. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>Etapa 4. Preparar sua política

Conclua a etapa [configurar sua política](#configure-your-policy) .

#### <a name="step-5-prepare-the-code"></a>Etapa 5. Preparar o código  

O [exemplo do GitHub](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) é um aplicativo Web ASP.net e um aplicativo de console que gera um token de ID assinado usando uma chave simétrica. 


### <a name="issue-a-token-with-asymmetric-keys"></a>Emitir um token com chaves assimétricas

Com uma chave assimétrica, o token é assinado usando certificados RSA. Esse aplicativo hospeda um ponto de extremidade de metadados do Open ID Connect e um ponto de extremidade JWKs (chaves Web JSON) que é usado pelo Azure AD B2C para validar a assinatura do token de ID.

O emissor do token deve fornecer os seguintes pontos de extremidade:

* `/.well-known/openid-configuration` -Um ponto de extremidade de configuração bem conhecido com informações relevantes sobre o token, como o nome do emissor do token e o link para o ponto de extremidade JWK. 
* `/.well-known/keys` -o ponto de extremidade da chave da Web JSON (JWK) com a chave pública usada para assinar a chave (com a parte da chave privada do certificado).

Consulte o exemplo do controlador do .NET MVC do [TokenMetadataController. cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .

#### <a name="step-1-prepare-a-self-signed-certificate"></a>Etapa 1. Preparar um certificado autoassinado

Se você ainda não tiver um certificado, poderá usar um certificado autoassinado para este guia de instruções. No Windows, você pode usar o cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) do PowerShell para gerar um certificado.

Execute este comando do PowerShell para gerar um certificado autoassinado. Modifique o argumento `-Subject` conforme apropriado para o aplicativo e nome de locatário do Azure AD B2C. Você também pode ajustar a data `-NotAfter` para especificar uma expiração diferente para o certificado.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>Etapa 2. Adicionar o perfil técnico de dica de token de ID 

O seguinte perfil técnico valida o token e extrai as declarações. Altere o URI de metadados para o ponto de extremidade de configuração bem conhecido do emissor do token.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>Etapa 3. Preparar sua política

Conclua a etapa [configurar sua política](#configure-your-policy) .

#### <a name="step-4-prepare-the-code"></a>Etapa 4. Preparar o código 

Este aplicativo Web ASP.NET de [exemplo do GitHub](https://github.com/azure-ad-b2c/id-token-builder) gera tokens de ID e hospeda os pontos de extremidade de metadados necessários para usar o parâmetro "id_token_hint" no Azure ad B2C.


### <a name="configure-your-policy"></a>Configurar sua política

Para abordagens simétricas e assimétricas, o `id_token_hint` perfil técnico é chamado de uma etapa de orquestração com o tipo de `GetClaims` e precisa especificar as declarações de entrada da política de terceira parte confiável.

1. Adicione o perfil técnico de IdTokenHint_ExtractClaims à sua política de extensão.
1. Adicione a seguinte etapa de orquestração à sua jornada do usuário como o primeiro item.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. Em sua política de terceira parte confiável, repita as mesmas declarações de entrada configuradas no perfil técnico de IdTokenHint_ExtractClaims. Por exemplo:
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

Dependendo dos seus requisitos de negócios, talvez seja necessário adicionar validações de token, por exemplo, verificar o formato do endereço de email. Para fazer isso, adicione etapas de orquestração que invocam um [perfil técnico de transformação de declarações](claims-transformation-technical-profile.md). Além disso, adicione um [perfil técnico autodeclarado](self-asserted-technical-profile.md) para apresentar uma mensagem de erro. 

### <a name="create-and-sign-a-token"></a>Criar e assinar um token

Os exemplos do GitHub ilustram como criar um token desse tipo um JWT que é enviado posteriormente como um `id_token_hint` parâmetro de cadeia de caracteres de consulta. Veja a seguir um exemplo de uma solicitação de autorização com id_token_hint parâmetro
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>Próximas etapas

- Verifique a solução [inscrever-se com email de convite](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) no repositório GitHub da Comunidade Azure ad B2C.
