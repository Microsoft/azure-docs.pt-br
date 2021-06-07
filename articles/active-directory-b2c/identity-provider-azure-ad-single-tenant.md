---
title: Configurar a entrada para uma organização do Azure AD
titleSuffix: Azure AD B2C
description: Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit, project-no-code
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 490880e4a37711a92b44a0ffe01315edfa6ddb26
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104580110"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Configurar assinatura para uma organização do Active Directory do Azure específica no Azure Active Directory B2C

Este artigo mostra como habilitar a entrada de usuários a partir de uma organização específica do Azure AD usando um fluxo de usuário no Azure AD B2C.

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="register-an-azure-ad-app"></a>Registrar um aplicativo do Azure AD

Para habilitar a entrada para usuários com uma conta do Azure AD de uma organização específica do Azure AD, em Azure Active Directory B2C (Azure AD B2C), você precisa criar um aplicativo no [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [registrar um aplicativo com a plataforma de identidade da Microsoft](../active-directory/develop/quickstart-register-app.md).

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário organizacional do Azure AD (por exemplo, contoso.com). Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário do Azure AD.
1. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
1. Selecione **Novo registro**.
1. Insira um **Nome** para seu aplicativo. Por exemplo, `Azure AD B2C App`.
1. Aceite a seleção padrão de **contas neste diretório organizacional somente** para este aplicativo.
1. Para o **URI de redirecionamento**, aceite o valor de **Web** e insira a URL a seguir em todas as letras minúsculas, onde `your-B2C-tenant-name` é substituído pelo nome do seu locatário de Azure ad B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

    Se você usar um [domínio personalizado](custom-domain.md), digite `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Substitua `your-domain-name` pelo seu domínio personalizado e `your-tenant-name` pelo nome do seu locatário.

1. Selecione **Registrar**. Registre a **ID do aplicativo (cliente)** para uso em uma etapa posterior.
1. Selecione **certificados & segredos** e, em seguida, selecione **novo segredo do cliente**.
1. Insira uma **Descrição** para o segredo, selecione uma expiração e, em seguida, selecione **Adicionar**. Registre o **valor** do segredo para uso em uma etapa posterior.

### <a name="configuring-optional-claims"></a>Como configurar as declarações opcionais

Se quiser obter as declarações `family_name` e `given_name` do Azure Active Directory, você poderá configurar declarações opcionais para seu aplicativo na interface do usuário do portal do Azure ou no manifesto do aplicativo. Para obter mais informações, veja [Como fornecer declarações opcionais ao aplicativo Azure Active Directory (visualização pública)](../active-directory/develop/active-directory-optional-claims.md).

1. Entre no [portal do Azure](https://portal.azure.com). Pesquise **Azure Active Directory** e selecione-o.
1. Na seção **Gerenciar**, escolha **Registros de aplicativo**.
1. Selecione o aplicativo para o qual você deseja configurar declarações opcionais na lista.
1. Na seção **Gerenciar**, selecione **Configuração do token**.
1. Escolha **Adicionar declaração opcional**.
1. Para o **tipo de token**, selecione **ID**.
1. Selecione as declarações opcionais a serem adicionadas `family_name` e `given_name` .
1. Clique em **Adicionar**.

::: zone pivot="b2c-user-flow"

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C. Selecione o filtro **Diretório + assinatura** no menu superior e escolha o diretório que contém o locatário do Azure AD B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **Provedores de identidade** e **Novo provedor do OpenID Connect**.
1. Insira um **Nome**. Por exemplo, insira *Contoso Azure AD*.
1. Para a **URL dos metadados**, insira a seguinte URL, substituindo `{tenant}` pelo nome de domínio do locatário do Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Por exemplo, `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Para **ID do cliente**, insira a ID do aplicativo que você registrou anteriormente.
1. Para **Segredo do cliente**, insira o segredo do cliente que você registrou anteriormente.
1. Para **escopo**, insira `openid profile` .
1. Deixe os valores padrão para **Tipo de resposta** e **Modo de resposta**.
1. (Opcional) Em **Dica de domínio**, insira `contoso.com`. Para obter mais informações, confira [Configurar entrada direta usando o Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. Em **Mapeamento de declarações do provedor de identidade**, selecione as seguintes declarações:

    - **ID de usuário**: *oid*
    - **Nome de exibição**: *name*
    - **Nome fornecido**: *given_name*
    - **Sobrenome**: *family_name*
    - **Email**: *preferred_username*

1. Selecione **Salvar**.

## <a name="add-azure-ad-identity-provider-to-a-user-flow"></a>Adicionar o provedor de identidade do Azure AD a um fluxo de usuário 

Neste ponto, o provedor de identidade do Azure AD foi configurado, mas ainda não está disponível em nenhuma das páginas de entrada. Para adicionar o provedor de identidade do Azure AD a um fluxo de usuário:

1. No locatário do Azure AD B2C, selecione **Fluxos dos usuários**.
1. Clique no fluxo de usuário que você deseja adicionar ao provedor de identidade do Azure AD.
1. Em **provedores de identidade social**, selecione **contoso Azure ad**.
1. Selecione **Salvar**.
1. Para testar sua política, selecione **executar fluxo de usuário**.
1. Para **aplicativo**, selecione o aplicativo Web chamado *testapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar fluxo de usuário** .
1. Na página inscrever-se ou entrar, selecione **contoso Azure ad** para entrar com a conta da Contoso do Azure AD.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave do aplicativo que criou em seu locatário do Azure AD B2C.

1. Verifique se você está usando o diretório que contém seu locatário do Azure AD B2C. Selecione o **diretório + filtro de assinatura** no menu superior e escolha o diretório que contém seu locatário de Azure ad B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **chaves de política** e, em seguida, selecione **Adicionar**.
1. Para **Opções**, escolha `Manual`.
1. Insira um **Nome** para a chave de política. Por exemplo, `ContosoAppSecret`.  O prefixo `B2C_1A_` é adicionado automaticamente ao nome da sua chave quando ele é criado, portanto, sua referência no XML na seção a seguir é *B2C_1A_ContosoAppSecret*.
1. Em **segredo**, insira o segredo do cliente que você registrou anteriormente.
1. Para **Uso de chave**, selecione `Signature`.
1. Selecione **Criar**.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Configurar o Azure AD como um provedor de identidade

Para permitir que os usuários entrem usando uma conta do Azure AD, você precisa definir o Azure AD como um provedor de declarações com o qual Azure AD B2C pode se comunicar por meio de um ponto de extremidade. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações adicionando o Azure AD ao elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra o arquivo *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte maneira:
    ```xml
    <ClaimsProvider>
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AADContoso-OpenIdConnect">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. No elemento **ClaimsProvider**, atualize o valor do **Domínio** para um valor exclusivo que possa ser usado para diferenciá-lo de outros provedores de identidade. Por exemplo, `Contoso`. Você não coloque um `.com` no final dessa configuração de domínio.
5. No elemento **ClaimsProvider**, atualize o valor de **DisplayName** para um nome amigável para o provedor de declarações. Esse valor não é usado atualmente.

### <a name="update-the-technical-profile"></a>Atualizar o perfil técnico

Para obter um token do ponto de extremidade do Azure AD, você precisa definir os protocolos que o Azure AD B2C deve usar para se comunicar com o Azure AD. Isso é feito dentro do elemento **TechnicalProfile** de **ClaimsProvider**.

1. Atualize a ID do **TechnicalProfile** elemento. Essa ID é usada para fazer referência a esse perfil técnico de outras partes da política, por exemplo `AADContoso-OpenIdConnect` .
1. Atualize o valor de **DisplayName**. Esse valor será exibido no botão de entrada em sua tela de entrada.
1. Atualize o valor para **Descrição**.
1. O Azure AD usa o protocolo OpenID Connect, portanto, verifique se o valor para **Protocolo** é `OpenIdConnect`.
1. Defina o valor de **METADATA** para `https://login.microsoftonline.com/tenant-name.onmicrosoft.com/v2.0/.well-known/openid-configuration`, em que `tenant-name` é o nome do seu locatário do Azure AD. Por exemplo, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`
1. Defina **client_id** para a ID do aplicativo de registro de aplicativo.
1. Em **CryptographicKeys**, atualize o valor de **StorageReferenceId** para o nome da chave de política que você criou anteriormente. Por exemplo, `B2C_1A_ContosoAppSecret`.


[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADContosoExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AzureADContosoExchange" TechnicalProfileReferenceId="AADContoso-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testar sua política personalizada

1. Selecione a política de terceira parte confiável, por exemplo `B2C_1A_signup_signin` .
1. Para **aplicativo**, selecione um aplicativo Web que você [registrou anteriormente](troubleshoot-custom-policies.md#troubleshoot-the-runtime). A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione o botão **executar agora** .
1. Na página inscrever-se ou entrar, selecione funcionário da **contoso** para entrar com a conta da Contoso do Azure AD.

Se o processo de entrada for bem-sucedido, seu navegador será redirecionado para `https://jwt.ms` , que exibe o conteúdo do token retornado por Azure ad B2C.

## <a name="next-steps"></a>Próximas etapas

Ao trabalhar com políticas personalizadas, às vezes você pode precisar de informações adicionais ao solucionar problemas de uma política durante seu desenvolvimento.

Para ajudar a diagnosticar problemas, você pode colocar temporariamente a política em "modo de desenvolvedor" e coletar logs com o Aplicativo Azure insights. Descubra como [Azure Active Directory B2C: coletando logs](troubleshoot-with-application-insights.md).

::: zone-end
