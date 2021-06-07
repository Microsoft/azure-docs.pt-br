---
title: Configurar um fluxo de redefinição de senha no Azure AD B2C
titleSuffix: Azure AD B2C
description: Saiba como configurar um fluxo de redefinição de senha no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/22/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f451d08dfbde643d91705f54296e9757a51c9d88
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798386"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Configurar um fluxo de redefinição de senha no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Fluxo de redefinição de senha

A [jornada de inscrição e entrada](add-sign-up-and-sign-in-policy.md) permite que os usuários redefinam sua própria senha usando o link **esqueceu sua senha?** . O fluxo de redefinição de senha envolve as seguintes etapas:

1. Na página de inscrição e entrada, o usuário clica no link **esqueceu sua senha?** . Azure AD B2C inicia o fluxo de redefinição de senha. 
2. O usuário fornece e verifica seu endereço de email com uma senha de tempo única.
3. Em seguida, o usuário pode inserir uma nova senha.

![Fluxo de redefinição de senha](./media/add-password-reset-policy/password-reset-flow.png)

O fluxo de redefinição de senha se aplica a contas locais em Azure AD B2C que usam um [endereço de email](identity-provider-local.md#email-sign-in) ou [nome de usuário](identity-provider-local.md#username-sign-in) com uma senha para entrar.

> [!TIP]
> O fluxo de redefinição de senha de autoatendimento permite que os usuários alterem sua senha quando o usuário esquece sua senha e deseja redefini-la. Considere configurar um [fluxo de alteração de senha](add-password-change-policy.md) para dar suporte a casos em que um usuário sabe sua senha e deseja alterá-la.

Uma prática comum após a migração de usuários para Azure AD B2C com senhas aleatórias é fazer com que os usuários verifiquem seus endereços de email e redefinam suas senhas durante a primeira conexão. Também é comum forçar o usuário a redefinir sua senha depois que um administrador altera sua senha; consulte [forçar redefinição de senha](force-password-reset.md) para habilitar esse recurso.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Redefinição de senha de autoatendimento (recomendado)

A nova experiência de redefinição de senha agora faz parte da política de inscrição ou de entrada. Quando o usuário seleciona o link **esqueceu sua senha?** , ele é imediatamente enviado para a experiência de senha esquecida. Seu aplicativo não precisa mais manipular o [código de erro AADB2C90118](#password-reset-policy-legacy)e você não precisa de uma política separada para redefinição de senha.

::: zone pivot="b2c-user-flow"

A experiência de redefinição de senha de autoatendimento pode ser configurada para os fluxos de usuário de **entrada (recomendado)** ou **inscrever-se e entrar (recomendado)** . Se você não tiver esse fluxo de usuário, crie um [entrada e](add-sign-up-and-sign-in-policy.md) o fluxo de usuário de inscrição. 

Para habilitar a redefinição de senha de autoatendimento para o fluxo de usuário de inscrição ou entrada:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Escolha **Fluxos de usuário**.
1. Selecione um fluxo de usuário de inscrição ou entrada (do tipo **recomendado**) que você deseja personalizar.
1. Em **configurações** no menu à esquerda, selecione **Propriedades**.
1. Em **complexidade da senha**, selecione **redefinição de senha de autoatendimento**.
1. Selecione **Salvar**.
1. Em **Personalizar** no menu à esquerda, selecione **layouts de página**.
1. Na **versão de layout da página**, escolha **2.1.2-Current** ou superior.
1. Selecione **Salvar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

As seções a seguir descrevem como adicionar uma experiência de autoatendimento de senha a uma política personalizada. O exemplo é baseado nos arquivos de política incluídos no [pacote de início de política personalizada](./custom-policy-get-started.md). 

> [!TIP]
> Você pode encontrar um exemplo completo da política "inscrever-se ou entrar com a redefinição de senha" no [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Indicar que um usuário selecionou a senha esquecida? criar

Para indicar à política que o usuário selecionou o link **esqueceu sua senha?** , defina uma declaração booliana. Essa declaração será usada para direcionar a jornada do usuário para o perfil técnico de senha esquecida. Essa declaração também pode ser emitida para o token para que o aplicativo esteja ciente de que o usuário se conectou por meio do fluxo de senha esquecida.

Você declara suas declarações no [esquema de declarações](claimsschema.md). Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione a declaração a seguir ao elemento **ClaimsSchema** . 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Atualizar a versão de layout da página

Versão de layout de [página](contentdefinitions.md#migrating-to-page-layout) `2.1.2` é necessário para habilitar o fluxo de redefinição de senha de autoatendimento dentro da jornada de inscrição ou entrada.

1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ContentDefinitions](contentdefinitions.md) . Se o elemento não existir, adicione-o.
1. Modifique o elemento **DataURI** dentro do elemento **ContentDefinition** com ID **API. signuporsignin** , conforme mostrado abaixo.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

Para iniciar a `isForgotPassword` declaração, um perfil técnico de transformação de declarações é usado. Este perfil técnico será referenciado mais tarde. Quando invocado, ele definirá o valor da `isForgotPassword` declaração como `true` . Localize o elemento `ClaimsProviders`. Se o elemento não existir, adicione-o. Em seguida, adicione o seguinte provedor de declarações:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

Os `SelfAsserted-LocalAccountSignin-Email` `setting.forgotPasswordLinkOverride` desfinadores de perfil técnico que a troca de declarações de redefinição de senha deve ser executada no percurso do usuário. 

### <a name="add-the-password-reset-sub-journey"></a>Adicionar a sub-rotina de redefinição de senha

Agora, sua jornada incluirá a capacidade para o usuário entrar, inscrever-se e executar a redefinição de senha. Para organizar melhor a jornada do usuário, uma [sub-rotina](subjourneys.md) pode ser usada para lidar com o fluxo de redefinição de senha.

A sub-rotina será chamada a partir do percurso do usuário e executará as etapas específicas para fornecer a experiência de redefinição de senha ao usuário. Use a `Call` jornada do tipo sub para que, depois que a sub-rotina for concluída, o controle seja retornado para a etapa de orquestração que iniciou a sub-rotina.

Localize o elemento `SubJourneys`. Se o elemento não existir, adicione-o após o `User Journeys` elemento. Em seguida, adicione a seguinte subjornada:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Preparar a jornada do usuário

Você precisará conectar o link **esqueceu sua senha?** para a jornada de sub-senha esquecida. Para fazer isso, referencie a ID da jornada de senha esquecida no elemento **ClaimsProviderSelection** da etapa **CombinedSignInAndSignUp** .

Se você não tiver seu próprio percurso de usuário personalizado com uma etapa **CombinedSignInAndSignUp** , use o procedimento a seguir para duplicar um percurso de usuário de inscrição ou de entrada existente. Caso contrário, prossiga para a próxima seção.

1. Abra o arquivo *TrustFrameworkBase.xml* do starter pack.
2. Localize e copie todo o conteúdo do elemento **UserJourney** que inclui `Id="SignUpOrSignIn"`.
3. Abra o *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Crie um elemento filho do elemento **Userjornadas** colando todo o conteúdo do elemento **userjornada** copiado na etapa 2.
5. Renomeie a ID da jornada do usuário. Por exemplo, `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Conectar o link de senha esquecida à subjornada de senha esquecida 

No percurso do usuário, você pode representar a subjornada de senha esquecida como um **ClaimsProviderSelection**. A adição desse elemento conecta o link **esqueceu sua senha?** para a subjornada de senha esquecida.

1. No percurso do usuário, localize o elemento etapa de orquestração que inclui `Type="CombinedSignInAndSignUp"` ou `Type="ClaimsProviderSelection"` . Geralmente é a primeira etapa de orquestração. O elemento **ClaimsProviderSelections** contém uma lista de provedores de identidade que um usuário pode usar para entrar. Adicione a seguinte linha:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. Na próxima etapa de orquestração, adicione um elemento **ClaimsExchange** . Adicione a seguinte linha:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Adicione a seguinte etapa de orquestração entre a etapa atual e a próxima etapa. A nova etapa de orquestração adicionada verifica se a `isForgotPassword` declaração existe. Se a declaração existir, ela invocará a [sub-rotina de redefinição de senha](#add-the-password-reset-sub-journey). 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Depois de adicionar a nova etapa de orquestração, renumere as etapas sequencialmente sem ignorar nenhum inteiro de 1 a N.

### <a name="set-the-user-journey-to-be-executed"></a>Definir a jornada do usuário a ser executada

Agora que você modificou ou criou um percurso do usuário, na seção **terceira parte confiável** , especifique a jornada que Azure ad B2C será executada para essa política personalizada. Dentro do elemento [RelyingParty](relyingparty.md) , localize o elemento **DefaultUserJourney** . Atualize o  **DefaultUserJourney referenceid** para corresponder à ID do percurso do usuário no qual você adicionou o **ClaimsProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Indicar o fluxo de senha esquecida para seu aplicativo

Seu aplicativo pode precisar detectar se o usuário se conectou por meio do fluxo de usuário de senha esquecida. A Declaração **isForgotPassword** contém um valor booliano que indica isso, que pode ser emitido no token enviado ao seu aplicativo. Se necessário, adicione `isForgotPassword` às declarações de saída na seção **terceira parte confiável** . Seu aplicativo pode verificar a `isForgotPassword` declaração para determinar se o usuário redefine sua senha.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Carregar a política personalizada

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o ícone **Diretório + Assinatura** na barra de ferramentas do portal e selecione o diretório que contém o locatário do Azure AD B2C.
1. No portal do Azure, pesquise e selecione **Azure AD B2C**.
1. Em **Políticas**, selecione **Identity Experience Framework**.
1. Selecione **carregar política personalizada** e, em seguida, carregue os dois arquivos de política que você alterou na seguinte ordem:
   1. A política de extensão, por exemplo `TrustFrameworkExtensions.xml` .
   2. A política de terceira parte confiável, por exemplo `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>Testar o fluxo de redefinição de senha

1. Selecione um fluxo de usuário de inscrição ou entrada (do tipo recomendado) que você deseja testar.
1. Selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Selecione **Executar fluxo de usuário**.
1. Na página inscrever-se ou entrar, selecione **esqueceu sua senha?**.
1. Verifique o endereço de email da conta que você criou anteriormente e, em seguida, selecione **continuar**.
1. Agora, você pode alterar a senha para o usuário. Altere a senha e selecione **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.
1. Verifique o valor de declaração do token de retorno `isForgotPassword` . Se existir e for definido como true, isso indicará que o usuário redefiniu a senha.

## <a name="password-reset-policy-legacy"></a>Política de redefinição de senha (herdada)

Se a experiência de [redefinição de senha de autoatendimento](#self-service-password-reset-recommended) não estiver habilitada, clicar nesse link não disparará automaticamente um fluxo de usuário de redefinição de senha. Em vez disso, o código de erro `AADB2C90118` é retornado para seu aplicativo. Seu aplicativo precisa manipular esse código de erro reinicializando a biblioteca de autenticação para autenticar um Azure AD B2C fluxo de usuário de redefinição de senha.

No diagrama a seguir:

1. No aplicativo, o usuário clica em entrar. O aplicativo inicia uma solicitação de autorização e leva o usuário a Azure AD B2C para concluir a entrada. A solicitação de autorização especifica o nome da política de inscrição ou de entrada, como **B2C_1_signup_signin**.
1. O usuário seleciona o link **esqueceu sua senha?** . Azure AD B2C retorna o código de erro AADB2C90118 para o aplicativo.
1. O aplicativo manipula o código de erro e inicia uma nova solicitação de autorização. A solicitação de autorização especifica o nome da política de redefinição de senha, como **B2C_1_pwd_reset**.

![Fluxo de usuário herdado de redefinição de senha](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Para ver um exemplo, dê uma olhada em um exemplo [simples de ASP.net](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI), que demonstra a vinculação de fluxos de usuário.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Criar um fluxo de usuário de redefinição de senha

Para permitir que os usuários do seu aplicativo redefinam sua senha, você cria um fluxo de usuário de redefinição de senha.

1. No menu de visão geral do locatário do Azure AD B2C, selecione **Fluxos dos usuários** e, em seguida, selecione **Novo fluxo de usuário**.
1. Na página **Criar um fluxo de usuário**, selecione o fluxo do usuário **Redefinição de senha**. 
1. Em **Selecionar uma versão**, selecione **Recomendado** e, em seguida, selecione **Criar**.
1. Insira um **Nome** para o fluxo de usuário. Por exemplo, *passwordreset1*.
1. Em **Provedores de identidade**, habilite **Redefinir senha usando endereço de email**.
1. Em **declarações do aplicativo**, selecione **Mostrar mais** e escolha as declarações que você deseja que sejam retornadas nos tokens de autorização enviados de volta para seu aplicativo. Por exemplo, selecione **ID de Objeto do Usuário**.
1. Selecione **OK**.
1. Selecione **Criar** para adicionar o fluxo de usuário. Um prefixo *B2C_1* é acrescentado automaticamente ao nome.

### <a name="test-the-user-flow"></a>Testar o fluxo de usuário

1. Selecione o fluxo de usuário que você criou para abrir a página de visão geral e, em seguida, selecione **Executar fluxo de usuário**.
1. Para **Aplicativo**, selecione o aplicativo Web denominado *webapp1* que você registrou anteriormente. A **URL de resposta** deve mostrar `https://jwt.ms`.
1. Clique em **executar fluxo de usuário**, verifique o endereço de email da conta que você criou anteriormente e, em seguida, selecione **continuar**.
1. Agora você pode alterar a senha do usuário. Altere a senha e selecione **Continuar**. O token é retornado para `https://jwt.ms` e deve ser exibido para você.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Criar uma política de redefinição de senha

As políticas personalizadas são um conjunto de arquivos XML que você carrega em seu locatário Azure AD B2C para definir percursos do usuário. Fornecemos pacotes de início com várias políticas predefinidas, incluindo: inscrição e entrada, redefinição de senha e política de edição de perfil. Para obter mais informações, consulte Introdução [às políticas personalizadas no Azure ad B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Próximas etapas

Configure uma [redefinição de senha forçada](force-password-reset.md).
