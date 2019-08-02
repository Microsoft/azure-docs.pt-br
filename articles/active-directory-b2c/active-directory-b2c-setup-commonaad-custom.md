---
title: Configurar a entrada para um provedor de identidade multilocatário do Azure AD usando políticas personalizadas no Azure Active Directory B2C | Microsoft Docs
description: Adicionar um provedor de identidade multilocatário do Azure AD usando políticas personalizadas – Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1938164e957daa84b22fa83e9cb9fa8d51ffeb15
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654092"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar a entrada para o Azure Active Directory multilocatário usando políticas personalizadas no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como habilitar o logon de usuários usando o ponto de extremidade de multi locação do Microsoft Azure Active Directory (Azure AD) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no Azure AD B2C. Isso permite que os usuários de vários locatários do Azure AD entrem no Azure AD B2C sem configurar um provedor técnico para cada locatário. No entanto, os membros em qualquer desses locatários **não** será capaz de entrar. Para fazer isso, você terá que [configurar individualmente cada locatário](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
>`Contoso.com` é usado para o locatário organizacional do Azure AD e `fabrikamb2c.onmicrosoft.com` é usado como o locatário do Azure AD B2C nas instruções a seguir.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrar um aplicativo

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o inquilino organizacional do Azure AD (contoso.com) clicando no **Diretório e no filtro de inscrição** no menu superior e escolhendo o diretório que contém seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Novo registro de aplicativo**.
5. Insira um nome para seu aplicativo. Por exemplo, `Azure AD B2C App`.
6. Para o **Tipo de aplicativo**, selecione `Web app / API`.
7. Para a **URL de logon**, digite a seguinte URL em letras minúsculas, em que `your-tenant` é substituído pelo nome do seu locatário do Azure AD B2C (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Clique em **Criar**. Copie a **ID do aplicativo** a ser usada posteriormente.
9. Selecione o aplicativo e, em seguida, selecione **Configurações**.
10. Selecione **Chaves**, insira a descrição da chave, selecione uma duração e, em seguida, clique em **Salvar**. Copie o valor da chave exibido para ser usado mais tarde.
11. Em **Configurações**, selecione **Propriedades**, defina **Multilocatário** para `Yes` e, em seguida, clique em **Salvar**

## <a name="create-a-policy-key"></a>Criar uma chave de política

Você precisa armazenar a chave do aplicativo que criou em seu locatário do Azure AD B2C.

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
4. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
5. Para **Opções**, escolha `Manual`.
6. Insira um **Nome** para a chave de política. Por exemplo, `ContosoAppSecret`.  O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
7. Em**segredo**, insira a chave do aplicativo que você registrou anteriormente.
8. Para **Uso de chave**, selecione `Signature`.
9. Clique em **Criar**.

## <a name="add-a-claims-provider"></a>Adicionar um provedor de declarações

Se você quiser que os usuários entrem usando o Azure AD, precisará definir o Azure AD como um provedor de declarações com o qual o Azure AD B2C pode se comunicar por meio de um endpoint. O ponto de extremidade fornece um conjunto de declarações que são usadas pelo Azure AD B2C para verificar se um usuário específico foi autenticado.

Você pode definir o Azure AD como um provedor de declarações adicionando o Azure AD ao elemento **ClaimsProvider** no arquivo de extensão de sua política.

1. Abra *TrustFrameworkExtensions.xml*.
2. Localize o elemento **ClaimsProviders**. Se ele não existir, adicione-o sob o elemento raiz.
3. Adicione um novo **ClaimsProvider** da seguinte forma:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>

            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" />
          </OutputClaims>
          <OutputClaimsTransformations>
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

4. Sob o elemento **ClaimsProvider**, atualize o valor de **Domain** para um valor exclusivo que pode ser usado para distingui-lo de outros provedores de identidade.
5. Sob o elemento **TechnicalProfile**, atualize o valor de **DisplayName**. Esse valor é exibido no botão de entrada em sua tela de entrada.
6. Defina **client_id** como a ID do aplicativo no registro de aplicativo multilocatário do Azure AD.

### <a name="restrict-access"></a>Restringir acesso

> [!NOTE]
> Usar `https://sts.windows.net` como o valor para **ValidTokenIssuerPrefixes** permite que todos os usuários do Azure AD entrem no seu aplicativo.

Você precisa atualizar a lista de emissores de token válidos e restringir o acesso a uma lista específica de locatários do Azure AD que podem entrar. Para obter os valores, você precisa examinar os metadados para cada um dos locatários específicos do Azure AD dos quais você gostaria que os usuários entrassem. O formato dos dados é semelhante ao seguinte: `https://login.windows.net/your-tenant/.well-known/openid-configuration`, onde `your-tenant` é o nome do locatário do Azure AD (contoso.com ou outro locatário do Azure AD).

1. Abra seu navegador e vá para a URL **METADATA**, procure o objeto **issuer** e copie seu valor. Ele deverá ter esta aparência: `https://sts.windows.net/tenant-id/`.
2. Copie e cole o valor na chave **ValidTokenIssuerPrefixes**. Você pode adicionar várias, separando-as com uma vírgula. Um exemplo disso é comentado no exemplo do XML acima.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

A essa altura, você já terá configurado a política, de forma que o Azure AD B2C saiba como se comunicar com o diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/entrada. Para disponibilizá-lo, você criará uma duplicata de um modelo de percurso do usuário existente e, depois, o modificará para que ele também tenha o provedor de identidade do Azure AD.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição/entrada. Se você adicionar um elemento **ClaimsProviderSelection** para o Azure AD, um novo botão será exibido quando um usuário chegar na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você criou.
2. Em **ClaimsProviderSelects**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Azure AD para receber um token. Vincule o botão a uma ação, vinculando o perfil técnico ao provedor de declarações do Azure AD.

1. Localize o **OrchestrationStep**, que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** usando o mesmo valor de **ID** usado para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico você já criou. Por exemplo, `Common-AAD`.

3. Salve o arquivo *TrustFrameworkExtensions.xml* e carregue-o novamente para verificação.

## <a name="create-an-azure-ad-b2c-application"></a>Criar um aplicativo Azure AD B2C

A comunicação com o Azure AD B2C ocorre por meio de um aplicativo que você cria no seu locatário. Esta seção lista etapas opcionais que você pode concluir para criar um aplicativo de teste, caso ainda não tenha feito isso.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
3. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
4. Selecione **Aplicativos** e, em seguida, selecione **Adicionar**.
5. Insira um nome para o aplicativo, por exemplo *testapp1*.
6. Para **Aplicativo Web/API Web**, selecione `Yes` e, em seguida, insira `https://jwt.ms` para a **URL de resposta**.
7. Clique em **Criar**.

## <a name="update-and-test-the-relying-party-file"></a>Atualizar e testar o arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-o para *SignUpSignContoso.xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo, `SignUpSignInContoso`.
3. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo, `http://contoso.com/B2C_1A_signup_signin_contoso`
4. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder à ID do novo percurso do usuário que você criou (SignUpSignContoso).
5. Salve suas alterações, faça upload do arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.
