---
title: Proteger um serviço Restful no Azure AD B2C
titleSuffix: Azure AD B2C
description: Proteja suas trocas de declarações da API REST personalizadas no Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f6907db7f6e53247a8f2fc0042e8c8e6b081dbd3
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516366"
---
# <a name="secure-your-restful-services"></a>Proteger os serviços RESTful 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Ao integrar uma API REST em um percurso do usuário do Azure AD B2C, você deve proteger o ponto de extremidade da API REST com autenticação. Isso garante que somente os serviços que têm as credenciais adequadas, como Azure AD B2C, possam fazer chamadas para o ponto de extremidade da API REST.

Saiba como integrar uma API REST no percurso do usuário do Azure AD B2C nos artigos [Validar entrada do usuário](custom-policy-rest-api-claims-validation.md) e [Adicionar trocas de declarações da API REST às políticas personalizadas](custom-policy-rest-api-claims-exchange.md).

Este artigo irá explorar como proteger a API REST com HTTP básico, certificado de cliente ou autenticação OAuth2. 

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em uma das seguintes 'Instruções':

- [Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C para validar a entrada do usuário](custom-policy-rest-api-claims-validation.md).
- [Adicionar trocas de declarações da API REST às políticas personalizadas](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>Autenticação básica HTTP

A autenticação básica HTTP é definida em [RFC 2617](https://tools.ietf.org/html/rfc2617). A autenticação básica funciona conforme segue: O Azure AD B2C envia uma solicitação HTTP com as credenciais do cliente no cabeçalho de Autorização. As credenciais são formatadas como a cadeia de caracteres codificada em base64 "name:password".  

### <a name="add-rest-api-username-and-password-policy-keys"></a>Adicionar chaves de política de nome de usuário e senha da API REST

Para configurar um perfil técnico da API REST com autenticação básica HTTP, crie as seguintes chaves de criptografia para armazenar o nome de usuário e a senha:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Em **Opções** selecione **Manual**.
1. Para **Nome**, digite **RestApiUsername**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Segredo**, digite o nome de usuário da API REST.
1. Para o **Uso da chave**, selecione **Criptografia**.
1. Selecione **Criar**.
1. Selecione **Chaves de Política** novamente.
1. Selecione **Adicionar**.
1. Em **Opções** selecione **Manual**.
1. Para **Nome**, digite **RestApiPassword**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **Segredo**, digite a senha da API REST.
1. Para o **Uso da chave**, selecione **Criptografia**.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>Configurar o perfil técnico da API REST para usar a autenticação básica HTTP

Depois de criar as chaves necessárias, configure os metadados de perfil técnico da API REST para fazer referência às credenciais.

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).
1. Pesquise o perfil técnico da API REST. Por exemplo, `REST-ValidateProfile` ou `REST-GetProfile`.
1. Localize o elemento `<Metadata>`.
1. Altere o *AuthenticationType* para `Basic`.
1. Altere o *AllowInsecureAuthInProduction* para `false`.
1. Adicione o seguinte snippet de código XML logo após fechar o elemento `</Metadata>`:
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

Veja a seguir um exemplo de um perfil técnico RESTful configurado com a autenticação básica HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>Autenticação de certificado do cliente HTTPS

A autenticação de certificado do cliente é uma autenticação mútua baseada em certificado, em que o cliente, Azure AD B2C, fornece o certificado do cliente para o servidor para comprovar sua identidade. Isso acontece como parte do handshake SSL. Somente os serviços que têm os certificados adequados, como o Azure AD B2C, podem acessar o serviço de API REST. O certificado do cliente é um certificado digital X.509. Nos ambientes de produção, deve ser assinado por uma autoridade de certificação.

### <a name="prepare-a-self-signed-certificate-optional"></a>Preparar um certificado autoassinado (opcional)

Para ambientes de não produção, se você ainda não tiver um certificado, é possível usar um certificado autoassinado. No Windows, você pode usar o cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) do PowerShell para gerar um certificado.

1. Execute esse comando do PowerShell para gerar um certificado autoassinado. Modifique o argumento `-Subject` conforme apropriado para o aplicativo e nome de locatário do Azure AD B2C. Você também pode ajustar a data `-NotAfter` para especificar uma expiração diferente para o certificado.
    ```powershell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```    
1. Abra **Gerenciar certificados de usuário** > **Usuário atual** > **Pessoal** > **Certificados** > *yourappname.yourtenant.onmicrosoft.com*.
1. Selecione o certificado > **Ação** > **Todas as tarefas** > **Exportar**.
1. Selecione **Sim** > **Próximo** > **Sim, exportar a chave privada** > **Próximo** .
1. Aceite os padrões para **Exportar formato de arquivo**.
1. Forneça a senha do certificado.

### <a name="add-a-client-certificate-policy-key"></a>Adicionar uma chave de política do certificado do cliente

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Na caixa **Opções**, selecione **Carregar**.
1. Na caixa **Nome**, digite **RestApiClientCertificate**.
    O prefixo *B2C_1A_* é adicionado automaticamente.
1. Na caixa **Carregar arquivo**, selecione o arquivo de certificado .pfx com uma chave privada.
1. Na caixa **Senha**, digite a senha do certificado.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>Configurar o perfil técnico da API REST para usar a autenticação do certificado do cliente

Depois de criar as chaves necessárias, configure os metadados de perfil técnico da API REST para fazer referência ao certificado do cliente.

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).
1. Pesquise o perfil técnico da API REST. Por exemplo, `REST-ValidateProfile` ou `REST-GetProfile`.
1. Localize o elemento `<Metadata>`.
1. Altere o *AuthenticationType* para `ClientCertificate`.
1. Altere o *AllowInsecureAuthInProduction* para `false`.
1. Adicione o seguinte snippet de código XML logo após fechar o elemento `</Metadata>`:
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

Veja a seguir um exemplo de um perfil técnico RESTful configurado com um certificado do cliente HTTP:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>Autenticação de portador OAuth2 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

A autenticação de token de portador é definida em [Estrutura de autorização OAuth 2.0: Uso do Token de Portador (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). Na autenticação de token de portador, o Azure AD B2C envia uma solicitação HTTP com um token no cabeçalho de autorização.

```http
Authorization: Bearer <token>
```

Um token de portador é uma cadeia de caracteres opaca. Pode ser um token de acesso JWT ou qualquer cadeia de caracteres que a API REST espera que o Azure AD B2C envie no cabeçalho de autorização. O Azure AD B2C dá suporte aos seguintes tipos:

- **Token de portador**. Para enviar o token de portador no perfil técnico RESTful, a política precisa primeiro adquirir o token de portador e depois usá-lo no perfil técnico RESTful.  
- **Token de portador estático**. Use esta abordagem quando a API REST emitir um token de acesso de longo prazo. Para usar um token de portador estático, crie uma chave de política e faça uma referência do perfil técnico RESTful para a chave de política. 


## <a name="using-oauth2-bearer"></a>Uso de portador OAuth2  

As etapas a seguir demonstram como usar as credenciais do cliente para obter um token de portador e passá-lo para o cabeçalho de autorização das chamadas à API REST.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>Definir uma declaração para armazenar o token de portador

Uma declaração fornece armazenamento temporário de dados durante uma execução de política do Azure AD B2C. O [esquema de declarações](claimsschema.md) é o lugar em que você declara suas declarações. O token de acesso deve ser armazenado em uma declaração para ser usado posteriormente. 

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localizar o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione as seguintes declarações ao elemento **ClaimsSchema**.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>Aquisição de um token de acesso 

Você pode obter um token de acesso de várias maneiras: obtendo [de um provedor de identidade federado](idp-pass-through-user-flow.md), chamando uma API REST que retorna um token de acesso, usando um [fluxo de ROPC](../active-directory/develop/v2-oauth-ropc.md) ou usando o [fluxo de credenciais do cliente](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md).  

O exemplo a seguir usa um perfil técnico da API REST para fazer uma solicitação para o ponto de extremidade do token do Azure AD, usando as credenciais do cliente passadas como autenticação básica HTTP. Para configurar isso no Azure AD, confira a [plataforma de identidade da Microsoft e o fluxo de credenciais do cliente OAuth 2.0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Talvez seja necessário modificar isso para a interface com o Provedor de identidade. 

Para o ServiceUrl, substitua your-tenant-name pelo nome do locatário do Azure Active Directory. Confira a referência de [perfil técnico RESTful](restful-technical-profile.md) para todas as opções disponíveis.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>Alterar o perfil técnico de REST para usar a autenticação de token de portador

Para dar suporte à autenticação de token de portador na política personalizada, modifique o perfil técnico da API REST com o seguinte:

1. No diretório de trabalho, abra o arquivo de política de extensão *TrustFrameworkExtensions.xml*.
1. Pesquise o nó `<TechnicalProfile>` que inclui `Id="REST-API-SignUp"`.
1. Localize o elemento `<Metadata>`.
1. Altere o *AuthenticationType* para *Portador* da seguinte maneira:
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. Altere ou adicione o *UseClaimAsBearerToken* para *bearerToken*, conforme segue. O *bearerToken* é o nome da declaração em que o token de portador será recuperado (a declaração de saída de `SecureREST-AccessToken`).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. Adicione a declaração usada acima como uma declaração de entrada:

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

Depois de adicionar os snippets acima, seu perfil técnico será semelhante ao seguinte código XML:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>Uso de um portador estático OAuth2 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>Adicione a chave de política de token do portador OAuth2

Para configurar um perfil técnico da API REST com um token de portador OAuth2, obtenha um token de acesso do proprietário da API REST. Em seguida, crie a seguinte chave de criptografia para armazenar o token de portador.

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `RestApiBearerToken`. O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
1. Em **Segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **Uso de chave**, selecione `Encryption`.
1. Selecione **Criar**.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>Configurar o perfil técnico da API REST para usar a chave de política do token de portador

Depois de criar as chaves necessárias, configure os metadados de perfil técnico da API REST para fazer referência ao token de portador.

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).
1. Pesquise o perfil técnico da API REST. Por exemplo, `REST-ValidateProfile` ou `REST-GetProfile`.
1. Localize o elemento `<Metadata>`.
1. Altere o *AuthenticationType* para `Bearer`.
1. Altere o *AllowInsecureAuthInProduction* para `false`.
1. Adicione o seguinte snippet de código XML logo após fechar o elemento `</Metadata>`:
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

Veja a seguir um exemplo de um perfil técnico RESTful configurado com a autenticação de token de portador:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>Autenticação de chave de API

A chave de API é um identificador exclusivo usado para autenticar um usuário para acessar um ponto de extremidade de API REST. A chave é enviada em um cabeçalho HTTP personalizado. Por exemplo, o [gatilho http Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys) usa o `x-functions-key` cabeçalho HTTP para identificar o solicitante.  

### <a name="add-api-key-policy-keys"></a>Adicionar chaves de política de chave de API

Para configurar um perfil técnico da API REST com autenticação de chave de API, crie a seguinte chave de criptografia para armazenar a chave de API:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Em **Opções** selecione **Manual**.
1. Para **nome**, digite **RestApiKey**.
    O prefixo *B2C_1A_* pode ser adicionado automaticamente.
1. Na caixa **segredo** , insira a chave da API REST.
1. Para o **Uso da chave**, selecione **Criptografia**.
1. Selecione **Criar**.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>Configurar seu perfil técnico da API REST para usar a autenticação de chave de API

Depois de criar a chave necessária, configure seus metadados de perfil técnico da API REST para fazer referência às credenciais.

1. No seu diretório de trabalho, abra o arquivo de política de extensão (TrustFrameworkExtensions.xml).
1. Pesquise o perfil técnico da API REST. Por exemplo, `REST-ValidateProfile` ou `REST-GetProfile`.
1. Localize o elemento `<Metadata>`.
1. Altere o *AuthenticationType* para `ApiKeyHeader`.
1. Altere o *AllowInsecureAuthInProduction* para `false`.
1. Adicione o seguinte snippet de código XML logo após fechar o elemento `</Metadata>`:
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

A **ID** da chave de criptografia define o cabeçalho http. Neste exemplo, a chave de API é enviada como **x-Functions-Key**.

Veja a seguir um exemplo de um perfil técnico RESTful configurado para chamar uma função do Azure com autenticação de chave de API:

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o elemento de [perfil técnico Restful](restful-technical-profile.md) na referência de IEF.