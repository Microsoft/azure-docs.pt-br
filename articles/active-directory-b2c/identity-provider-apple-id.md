---
title: Configurar a inscrição e a entrada com uma ID da Apple
titleSuffix: Azure AD B2C
description: Forneça inscrição e entrada para clientes com a ID da Apple em seus aplicativos usando Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cc385c3a7ceb0245e3a4acbedb037b1b28bde7b3
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518102"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Configurar a inscrição e a entrada com uma ID da Apple usando Azure Active Directory B2C (versão prévia)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Criar um aplicativo de ID da Apple

Para habilitar a entrada para usuários com uma ID da Apple no Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo no [https://developer.apple.com](https://developer.apple.com) . Para obter mais informações, consulte [entrar com a Apple](https://developer.apple.com/sign-in-with-apple/get-started/). Se você ainda não tiver uma conta de desenvolvedor da Apple, poderá se inscrever no [Apple Developer Program](https://developer.apple.com/programs/enroll/).

1. Entre no portal do [desenvolvedor da Apple](https://developer.apple.com/account) com suas credenciais de conta.
1. No menu, selecione **certificados, IDs, & perfis** e, em seguida, selecione **(+)**.
1. Para **registrar um novo identificador**, selecione **IDs de aplicativo** e, em seguida, selecione **continuar**.
1. Para **selecionar um tipo**, selecione **aplicativo** e, em seguida, selecione **continuar**.
1. Para **registrar uma ID do aplicativo**:
    1. Insira uma **Descrição** 
    1. Insira a **ID do pacote**, como `com.contoso.azure-ad-b2c` . 
    1. Para **recursos**, selecione **entrar com a Apple** na lista de recursos. 
    1. Anote o prefixo da ID do aplicativo (ID da equipe) nesta etapa. Você precisará dela mais tarde.
    1. Selecione **Continuar** e, em seguida, **Registrar**.
1. No menu, selecione **certificados, IDs, & perfis** e, em seguida, selecione **(+)**.
1. Para **registrar um novo identificador**, selecione **IDs de serviços** e, em seguida, selecione **continuar**.
1. Para **registrar uma ID de serviços**:
    1. Insira uma **Descrição**. A descrição é mostrada para o usuário na tela de consentimento.
    1. Insira o **identificador**, como `com.consoto.azure-ad-b2c-service` . O identificador é a ID do cliente para o fluxo do OpenID Connect.
    1. Selecione **continuar** e, em seguida, selecione **registrar**.
1. Em **identificadores**, selecione o identificador que você criou.
1. Selecione **entrar com a Apple** e, em seguida, selecione **Configurar**.
    1. Selecione a **ID do aplicativo primário** com a qual você deseja configurar a entrada com a Apple.
    1. Em **domínios e subdomínios**, digite `your-tenant-name.b2clogin.com` . Substitua nome-do-seu-locatário pelo nome do seu locatário.
    1. Em **URLs de retorno**, insira `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Substitua nome-do-seu-locatário pelo nome do seu locatário.
    1. Selecione **Avançar** e, em seguida, selecione **concluído**.
    1. Quando a janela pop-up for fechada, selecione **continuar** e, em seguida, selecione **salvar**.

## <a name="creating-an-apple-client-secret"></a>Criando um segredo do cliente da Apple

1. No menu do portal do desenvolvedor da Apple, selecione **chaves** e, em seguida, selecione **(+)**.
1. Para **registrar uma nova chave**:
    1. Digite um **nome de chave**.
    1. Selecione **entrar com a Apple** e, em seguida, selecione **Configurar**.
    1. Para a **ID do aplicativo primário**, selecione o aplicativo que você criou anteriormente e selecione **salvar**.
    1. Selecione **Configurar** e, em seguida, selecione **registrar** para concluir o processo de registro de chave.
1. Para **baixar sua chave**, selecione **baixar** para baixar um arquivo. P8 que contém sua chave.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Configurar a Apple como um provedor de identidade

1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador global do seu locatário Azure ad B2C.
1. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
1. Em **Serviços do Azure**, selecione **Azure ad B2C**. Ou use a caixa de pesquisa para localizar e selecionar **Azure ad B2C**.
1. Selecione **provedores de identidade** e, em seguida, selecione **Apple (versão prévia)**.
1. Insira um **Nome**. Por exemplo, *Apple*.
1. Insira a **ID do desenvolvedor da Apple (ID da equipe)**.
1. Insira a **ID de serviço da Apple (ID do cliente)**.
1. Insira a **ID da chave da Apple**.
1. Selecione e carregue os **dados de certificado da Apple**.
1. Selecione **Salvar**.


> [!IMPORTANT] 
> - Entrar com a Apple exige que o administrador renove o segredo do cliente a cada seis meses. 
> - Durante a visualização pública desse recurso, você precisará renovar manualmente o segredo do cliente da Apple se ele expirar. Um aviso será exibido com antecedência nos provedores de identidade da Apple configurar a página de IDP social, mas recomendamos que você defina seu próprio lembrete. 
> - Se você precisar renovar o segredo, abra Azure ad B2C na portal do Azure, acesse **provedores de identidade**  >  **Apple** e selecione **renovar segredo**.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade da Apple a um fluxo de usuário

Para permitir que os usuários entrem usando uma ID da Apple, você precisa adicionar o provedor de identidade da Apple a um fluxo de usuário. Entre com a Apple pode ser configurada apenas para a versão **recomendada** de fluxos de usuário. Para adicionar o provedor de identidade da Apple a um fluxo de usuário:

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Selecione um fluxo de usuário para o qual você deseja adicionar o provedor de identidade da Apple. 
1. Em **provedores de identidade social**, selecione **Apple (versão prévia)**.
1. Selecione **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar fluxo de usuário** .
1. Na página inscrever-se ou entrar, selecione **Apple** para entrar com a ID da Apple.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Assinando o segredo do cliente

Use o arquivo. P8 baixado anteriormente para assinar o segredo do cliente em um token JWT. Há muitas bibliotecas que podem criar e assinar o JWT para você. Use a [função do Azure que cria um token](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) para você. 

1. Crie uma [função do Azure](../azure-functions/functions-create-function-app-portal.md).
1. Em **desenvolvedor**, selecione **código + teste**. 
1. Copie o conteúdo do arquivo [Run. CSX](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) e cole-o no editor.
1. Selecione **Salvar**.
1. Faça uma `POST` solicitação HTTP e forneça as seguintes informações:

    - **appleTeamId**: sua ID da equipe de desenvolvedores da Apple
    - **appleServiceId**: a ID de serviço da Apple (também a ID do cliente)
    - **p8key**: a chave de formato PEM. Você pode obtê-lo abrindo o arquivo. P8 em um editor de texto e copiando tudo entre `-----BEGIN PRIVATE KEY-----` e `-----END PRIVATE KEY-----` sem quebras de linha.
 
O JSON a seguir é um exemplo de uma chamada para a função do Azure:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

A função do Azure responde com um JWT de segredo do cliente corretamente formatado e assinado em uma resposta, por exemplo:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar o segredo do cliente que registrou anteriormente no seu locatário do Azure AD B2C.

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
2. Em **Serviços do Azure**, selecione **Azure ad B2C**. Ou use a caixa de pesquisa para localizar e selecionar **Azure ad B2C**.
1. Na página **visão geral** , selecione **Identity Experience Framework**.
1. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
1. Para **Opções**, escolha **manual**.
1. Insira um **Nome** para a chave de política. Por exemplo, "AppleSecret". O prefixo "B2C_1A_" é adicionado automaticamente ao nome da sua chave.
1. Em **segredo**, insira o valor de um token retornado pela função do Azure (um token JWT).
1. Para **Uso de chave**, selecione **Assinatura**.
1. Selecione **Criar**.

> [!IMPORTANT] 
> - Entrar com a Apple exige que o administrador renove o segredo do cliente a cada seis meses.
> - Você precisará renovar manualmente o segredo do cliente da Apple se ele expirar e armazenar o novo valor na chave de política.
> - Recomendamos que você defina seu próprio lembrete dentro de 6 meses para gerar um novo segredo do cliente. 

## <a name="configure-apple-as-an-identity-provider"></a>Configurar a Apple como um provedor de identidade

Para permitir que os usuários entrem usando uma ID da Apple, você precisa definir a conta como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico é autenticado.

Você pode definir uma ID da Apple como um provedor de declarações adicionando-a ao elemento **ClaimsProviders** no arquivo de extensão da política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.firstName user.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Defina **client_id** para o identificador de serviço. Por exemplo, `com.consoto.azure-ad-b2c-service`.
5. Salve o arquivo.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione **Apple** para entrar com a ID da Apple.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end
