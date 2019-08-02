---
title: Configurar logon com uma conta do Active Directory do Azure no Azure Active Directory B2C usando políticas personalizadas | Microsoft Docs
description: Configure o logon com uma conta do Azure Active Directory no Azure Active Directory B2C usando políticas personalizadas.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 4ae821dc9ae0d739526b91bad76bca544cad2fb0
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203704"
---
# <a name="set-up-sign-in-with-an-azure-active-directory-account-using-custom-policies-in-azure-active-directory-b2c"></a>Configurar login com uma conta do Azure Active Directory usando políticas personalizadas no Azure Active Directory B2C 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Este artigo mostra como ativar a entrada de usuários de uma organização do Azure AD (Azure Active Directory) usando [políticas personalizadas](active-directory-b2c-overview-custom.md) no B2C do Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas no Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registrar um aplicativo

Para habilitar a entrada para usuários de uma organização específica do Azure AD, você precisa registrar um aplicativo no locatário organizacional do Azure AD.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Verifique se você está usando o diretório que contém o inquilino organizacional do Azure AD (contoso.com) clicando no **Diretório e no filtro de inscrição** no menu superior e escolhendo o diretório que contém seu inquilino.
3. Escolha **Todos os serviços** no canto superior esquerdo do portal do Azure e pesquise e selecione **Registros de aplicativo**.
4. Selecione **Novo registro de aplicativo**.
5. Insira um nome para seu aplicativo. Por exemplo: `Azure AD B2C App`.
6. Para o **Tipo de aplicativo**, selecione `Web app / API`.
7. Para a **URL de Logon**, digite a seguinte URL em letras minúsculas, em que `your-B2C-tenant-name` é substituído pelo nome do seu locatário do Microsoft Azure Active Directory B2C:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Por exemplo: `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

8. Clique em **Criar**. Copie a **ID do aplicativo** a ser usada posteriormente.
9. Selecione o aplicativo e, em seguida, selecione **Configurações**.
10. Selecione **Chaves**, insira a descrição da chave, selecione uma duração e, em seguida, clique em **Salvar**. Copie o valor da chave que é exibida para ser usada posteriormente.

## <a name="create-a-policy-key"></a>Crie uma chave de política

Você precisa armazenar a chave do aplicativo que criou em seu locatário do Azure AD B2C.

1. Verifique se você está usando o diretório que contém o locatário do Azure AD B2C clicando no **filtro Diretório e assinatura** no menu superior e escolhendo o diretório que contém seu locatário.
2. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
3. Na página de Visão Geral, selecione **Estrutura de Experiência de Identidade**.
4. Selecione **Chaves de Política** e, em seguida, escolha **Adicionar**.
5. Para **Opções**, escolha `Manual`.
6. Insira um **Nome** para a chave de política. Por exemplo: `ContosoAppSecret`.  O prefixo `B2C_1A_` será adicionado automaticamente ao nome da chave.
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
      <Domain>Contoso</Domain>
      <DisplayName>Login using Contoso</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="ContosoProfile">
          <DisplayName>Contoso Employee</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <OutputTokenFormat>JWT</OutputTokenFormat>
          <Metadata>
            <Item Key="METADATA">https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration</Item>
            <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
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
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" AlwaysUseDefaultValue="true" />
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

1. Atualize a ID do **TechnicalProfile** elemento. Essa ID é usada para se referir a esse perfil técnico em outras partes da política.
2. Atualize o valor de **DisplayName**. Esse valor será exibido no botão de entrada em sua tela de entrada.
3. Atualize o valor para **Descrição**.
4. O Azure AD usa o protocolo OpenID Connect, portanto, verifique se o valor para **Protocolo** é `OpenIdConnect`.
5. Defina o valor de **METADATA** para `https://login.windows.net/your-AD-tenant-name.onmicrosoft.com/.well-known/openid-configuration`, em que `your-AD-tenant-name` é o nome do seu locatário do Azure AD. Por exemplo, `https://login.windows.net/fabrikam.onmicrosoft.com/.well-known/openid-configuration`
6. Abra seu navegador e acesse a URL **METADATA** que acabou de atualizar, procure o objeto **emissor**, copie e cole o valor no valor de **ProviderName** no Arquivo XML.
8. Defina **client_id** e **IdTokenAudience** como o ID do aplicativo do registro do aplicativo.
9. Sob **CryptographicKeys**, atualize o valor de **StorageReferenceId** para a chave de política que você definiu. Por exemplo: `ContosoAppSecret`.

### <a name="upload-the-extension-file-for-verification"></a>Carregar o arquivo de extensão para verificação

A essa altura, você já terá configurado a política, de forma que o Azure AD B2C saiba como se comunicar com o diretório do Azure AD. Tente carregar o arquivo de extensão da política apenas para confirmar se ele não apresenta problemas até o momento.

1. Na página **Políticas Personalizadas** em seu locatário do Azure AD B2C, selecione **Carregar Política**.
2. Habilite **Substitua a política se ela existir** e, em seguida, navegue até o arquivo *TrustFrameworkExtensions.xml* e selecione-o.
3. Clique em **Carregar**.

## <a name="register-the-claims-provider"></a>Registrar o provedor de declarações

Neste ponto, o provedor de identidade foi definido, mas não está disponível em nenhuma das telas de inscrição/entrada. Para torná-lo disponível, crie uma duplicata de uma jornada de usuário de modelo existente e modifique-a para que ela também tenha o provedor de identidade do Azure AD:

1. Abra o arquivo *TrustFrameworkBase.xml* do pacote inicial.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o conteúdo do elemento **UserJourney** que você copiou como filho do elemento **UserJourneys**.
5. Renomeie a ID do percurso do usuário. Por exemplo: `SignUpSignInContoso`.

### <a name="display-the-button"></a>Exibir o botão

O elemento **ClaimsProviderSelection** é análogo a um botão do provedor de identidade em uma tela de inscrição/entrada. Se você adicionar um elemento **ClaimsProviderSelection** para o Azure AD, um novo botão será exibido quando um usuário chegar na página.

1. Encontre o elemento **OrchestrationStep** que inclui `Order="1"` na jornada do usuário que você criou.
2. Em **ClaimsProviderSelections**, adicione o elemento a seguir. Defina o valor de **TargetClaimsExchangeId** para um valor apropriado, por exemplo `ContosoExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Vincular o botão a uma ação

Agora que implementou um botão, você precisará vinculá-lo a uma ação. Nesse caso, a ação destina-se a que o Azure AD B2C se comunique com o Azure AD para receber um token. Vincule o botão a uma ação vinculando o perfil técnico ao provedor de declarações do Azure AD:

1. Localize o **OrchestrationStep** que inclui `Order="2"` no percurso do usuário.
2. Adicione o seguinte elemento **ClaimsExchange** usando o mesmo valor de **ID** usado para **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```
    
    Atualize o valor de **TechnicalProfileReferenceId** para a **ID** do perfil técnico você já criou. Por exemplo: `ContosoProfile`.

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

1. Faça uma cópia do *SignUpOrSignIn.xml* no diretório de trabalho e renomeie-a. Por exemplo, renomeie-o para *SignUpSignInContoso.xml*.
2. Abra o novo arquivo e atualize o valor do atributo **PolicyId** para **TrustFrameworkPolicy** com um valor exclusivo. Por exemplo: `SignUpSignInContoso`.
3. Atualize o valor de **PublicPolicyUri** com o URI da política. Por exemplo,`http://contoso.com/B2C_1A_signup_signin_contoso`
4. Atualize o valor do atributo **ReferenceId** em **DefaultUserJourney** para corresponder ao ID da nova jornada de usuário que você criou (SignUpSignInContoso).
5. Salve suas alterações, faça o upload do arquivo e, em seguida, selecione a nova política na lista.
6. Verifique se o aplicativo Azure AD B2C que você criou está selecionado no campo **Selecionar aplicativo** e teste-o clicando em **Executar agora**.

