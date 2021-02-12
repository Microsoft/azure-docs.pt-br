---
title: Configurar o comportamento da sessão-Azure Active Directory B2C | Microsoft Docs
description: Saiba como configurar o comportamento da sessão no Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ad9bd8dec94660d94cf3a106d31dafdad06f47a8
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584503"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Configurar o comportamento da sessão no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

O SSO (logon único) adiciona segurança e conveniência quando os usuários entram em aplicativos em Azure Active Directory B2C (Azure AD B2C). Este artigo descreve os métodos de logon único usados no Azure AD B2C e ajuda a escolher o método de SSO mais apropriado ao configurar sua política.

Com o logon único, os usuários entram uma vez com uma única conta e obtêm acesso a vários aplicativos. O aplicativo pode ser um aplicativo Web, móvel ou de página única, independentemente da plataforma ou do nome de domínio.

Quando o usuário entra inicialmente em um aplicativo, Azure AD B2C persiste uma sessão baseada em cookie. Após as solicitações de autenticação subsequentes, o Azure AD B2C lê e valida a sessão baseada em cookies e emite um token de acesso sem solicitar que o usuário entre novamente. Se a sessão baseada em cookies expirar ou se tornar inválida, o usuário será solicitado a entrar novamente.  

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Visão geral da sessão de Azure AD B2C

A integração com o Azure AD B2C envolve três tipos de sessões de SSO:

- **Azure ad B2C** -sessão gerenciada por Azure ad B2C
- **Provedor de identidade federada** -sessão gerenciada pelo provedor de identidade, por exemplo, Facebook, Salesforce ou conta Microsoft
- **Aplicativo** -sessão gerenciada pelo aplicativo Web, móvel ou de página única

![Sessão de SSO](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Sessão de Azure AD B2C 

Quando um usuário é autenticado com êxito com uma conta local ou social, o Azure AD B2C armazena uma sessão baseada em cookie no navegador do usuário. O cookie é armazenado sob o nome de domínio do locatário Azure AD B2C, como `https://contoso.b2clogin.com` .

Se um usuário entrar inicialmente com uma conta federada e, durante a janela de tempo da sessão (vida útil ou TTL) entrar no mesmo aplicativo ou em um aplicativo diferente, Azure AD B2C tentará adquirir um novo token de acesso do provedor de identidade federada. Se a sessão do provedor de identidade federada tiver expirado ou for inválida, o provedor de identidade federada solicitará ao usuário suas credenciais. Se a sessão ainda estiver ativa (ou se o usuário tiver entrado com uma conta local em vez de uma conta federada), o Azure AD B2C autoriza o usuário e elimina solicitações adicionais.

Você pode configurar o comportamento da sessão, incluindo o TTL da sessão e como Azure AD B2C compartilha a sessão entre políticas e aplicativos.

### <a name="federated-identity-provider-session"></a>Sessão do provedor de identidade federada

Um provedor de identidade social ou empresarial gerencia sua própria sessão. O cookie é armazenado sob o nome de domínio do provedor de identidade, como `https://login.salesforce.com` . Azure AD B2C não controla a sessão do provedor de identidade federada. Em vez disso, o comportamento da sessão é determinado pelo provedor de identidade federada. 

Considere o seguinte cenário:

1. Um usuário entra no Facebook para verificar o feed.
2. Posteriormente, o usuário abre seu aplicativo e inicia o processo de entrada. O aplicativo redireciona o usuário para Azure AD B2C para concluir o processo de entrada.
3. Na página Azure AD B2C inscrição ou entrada, o usuário opta por entrar com sua conta do Facebook. O usuário é redirecionado para o Facebook. Se houver uma sessão ativa no Facebook, o usuário não receberá uma solicitação para fornecer suas credenciais e será redirecionado imediatamente para Azure AD B2C com um token do Facebook.

### <a name="application-session"></a>Sessão do aplicativo

Um aplicativo Web, móvel ou de página única pode ser protegido por acesso OAuth, tokens de ID ou tokens SAML. Quando um usuário tenta acessar um recurso protegido no aplicativo, o aplicativo verifica se há uma sessão ativa no lado do aplicativo. Se não houver nenhuma sessão de aplicativo ou a sessão tiver expirado, o aplicativo levará o usuário a Azure AD B2C para a página de entrada.

A sessão de aplicativo pode ser uma sessão baseada em cookie armazenada sob o nome de domínio do aplicativo, como `https://contoso.com` . Os aplicativos móveis podem armazenar a sessão de forma diferente, mas usando uma abordagem semelhante.

## <a name="configure-azure-ad-b2c-session-behavior"></a>Configurar o comportamento da sessão de Azure AD B2C

Você pode configurar o comportamento da sessão de Azure AD B2C, incluindo:

- Tempo de **vida da sessão do aplicativo Web (minutos)** – a quantidade de vezes que o cookie de sessão Azure ad B2C é armazenado no navegador do usuário após a autenticação bem-sucedida. Você pode definir o tempo de vida da sessão como um valor de 15 a 720 minutos.

- **Tempo limite da sessão do aplicativo Web** -indica como uma sessão é estendida pela configuração do tempo de vida da sessão ou pela configuração de manter-se conectado.
  - **Sem interrupção** -indica que a sessão é estendida toda vez que o usuário executa uma autenticação baseada em cookie (padrão).
  - **Absoluta** – indica que o usuário é forçado a autenticar novamente após o período de tempo especificado.

- **Configuração de logon único** – a sessão de Azure ad B2C pode ser configurada com os seguintes escopos:
  - **Locatário** -essa configuração é o padrão. O uso dessa configuração permite a vários aplicativos e fluxos de usuário em seu locatário B2C compartilhar a mesma sessão de usuário. Por exemplo, quando um usuário entra em um aplicativo, o usuário também pode entrar diretamente em outro acesso ao acessá-lo.
  - **Aplicação** - Esta configuração permite que você mantenha uma sessão de usuário exclusivamente para um aplicativo, independente de outros aplicativos. Por exemplo, você pode usar essa configuração se quiser que o usuário entre no contoso farmácia, independentemente de o usuário já estar conectado aos mercados da contoso.
  - **Política** - Essa configuração permite que você mantenha uma sessão de usuário exclusivamente para um fluxo de usuário, independentemente dos aplicativos que a usam. Por exemplo, se o usuário já tiver entrado e concluído uma etapa de autenticação multifator (MFA), o usuário poderá receber acesso a partes de segurança mais alta de vários aplicativos, desde que a sessão vinculada ao fluxo do usuário não expire.
  - **Desabilitado** – essa configuração força o usuário a executar todo o fluxo do usuário em cada execução da política.
::: zone pivot="b2c-custom-policy"
- **Mantenha-me conectado** -estende o tempo de vida da sessão por meio do uso de um cookie persistente. A sessão permanece ativa depois que o usuário fecha e reabre o navegador. A sessão é revogada somente quando um usuário se desconecta. O recurso conectado manter-me só se aplica à entrada com contas locais. O recurso conectado manter-me tem precedência sobre o tempo de vida da sessão. Se o recurso conectado manter-me estiver habilitado e o usuário o selecionar, esse recurso determinará quando a sessão irá expirar. 
::: zone-end

::: zone pivot="b2c-user-flow"

Para configurar o comportamento da sessão:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém o locatário Azure ad B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Escolha **Fluxos de usuário**.
1. Abra o fluxo de usuários criado anteriormente.
1. Selecione **Propriedades**.
1. Configure a **vida útil da sessão do aplicativo Web (minutos)**, o **tempo limite da sessão do aplicativo Web**, **a configuração de logon único** e **exija o token de ID em solicitações de logout** , conforme necessário.
1. Clique em **Save** (Salvar).

::: zone-end

::: zone pivot="b2c-custom-policy"

Para alterar o comportamento da sessão e as configurações de SSO, você adiciona um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty](relyingparty.md).  O elemento **UserJourneyBehaviors** deve vir logo após o **DefaultUserJourney**. O elemento **UserJourneyBehavors** deve ser semelhante a este exemplo:

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

## <a name="enable-keep-me-signed-in-kmsi"></a>Habilitar manter-me conectado (KMSI)

Você pode habilitar a funcionalidade manter-me conectado para usuários de seus aplicativos Web e nativos que têm contas locais em seu diretório Azure Active Directory B2C (Azure AD B2C). Esse recurso concede acesso a usuários que retornam ao seu aplicativo sem solicitar que eles reinsiram seu nome de usuário e senha. Esse acesso é revogado quando o usuário sai do serviço.

![Exemplo de página de entrada de inscrição mostrando uma caixa de seleção Mantenha-me conectado](./media/session-behavior/keep-me-signed-in.png)

Os usuários não devem habilitar essa opção em computadores públicos.

### <a name="configure-the-page-identifier"></a>Configurar o identificador de página

Para habilitar o KMSI, defina o elemento de definição de conteúdo `DataUri` para [identificador de página](contentdefinitions.md#datauri) `unifiedssp` e [página versão](page-layout.md) *1.1.0* ou superior.

1. Abra o arquivo de extensão da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>. Esse arquivo de extensão é um dos arquivos de política incluídos no pacote de início de política personalizada, que você deve ter obtido no pré-requisito, [introdução às políticas personalizadas](custom-policy-get-started.md).
1. Pesquise o elemento **BuildingBlocks**. Se o elemento não existir, adicione-o.
1. Adicione o elemento **ContentDefinitions** ao elemento **BuildingBlocks** da política.

    Sua política personalizada deve ser semelhante ao seguinte trecho de código:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>Adicionar os metadados ao perfil técnico autodeclarado

Para adicionar a caixa de seleção KMSI à página de inscrição e entrada, defina os `setting.enableRememberMe` metadados como true. Substitua os perfis técnicos SelfAsserted-LocalAccountSignin-email no arquivo de extensão.

1. Localize o elemento ClaimsProviders. Se o elemento não existir, adicione-o.
1. Adicione o seguinte provedor de declarações ao elemento ClaimsProviders:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. Salve o arquivo de extensões.

### <a name="configure-a-relying-party-file"></a>Configurar um arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Abra o arquivo de política personalizada. Por exemplo, *SignUpOrSignin.xml*.
1. Se ele ainda não existir, adicione um `<UserJourneyBehaviors>` nó filho ao `<RelyingParty>` nó. Ele deve estar localizado imediatamente após `<DefaultUserJourney ReferenceId="User journey Id" />` , por exemplo: `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` .
1. Adicione o seguinte nó como um filho do elemento `<UserJourneyBehaviors>`.

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

Recomendamos que você defina o valor de SessionExpiryInSeconds como um período curto (1200 segundos), enquanto o valor de KeepAliveInDays pode ser definido como um período relativamente longo (30 dias), conforme mostrado no exemplo a seguir:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>Sair

Quando você deseja desconectar o usuário do aplicativo, não é suficiente limpar os cookies do aplicativo ou encerrar a sessão com o usuário. Você deve redirecionar o usuário para Azure AD B2C para sair. Caso contrário, o usuário poderá se autenticar novamente em seus aplicativos sem inserir suas credenciais novamente.

Após uma solicitação de saída, Azure AD B2C:

1. Invalida o Azure AD B2C sessão baseada em cookie.
::: zone pivot="b2c-user-flow"
2. Tentativas de sair de provedores de identidade federada
::: zone-end
::: zone pivot="b2c-custom-policy"
3. Tentativas de sair de provedores de identidade federada:
   - OpenId Connect – se o ponto de extremidade de configuração bem conhecido do provedor de identidade especificar um `end_session_endpoint` local.
   - OAuth2 – se os [metadados do provedor de identidade](oauth2-technical-profile.md#metadata) contiverem o `end_session_endpoint` local.
   - SAML-se os [metadados do provedor de identidade](saml-identity-provider-technical-profile.md#metadata) contiverem o `SingleLogoutService` local.
4. Opcionalmente, desconecta-se de outros aplicativos. Para obter mais informações, consulte a seção de [saída única](#single-sign-out) .

> [!NOTE]
> Você pode desabilitar a saída de provedores de identidade federada, definindo os metadados do perfil técnico do provedor de identidade `SingleLogoutEnabled` como `false` .
::: zone-end

A saída limpa o estado de logon único do usuário com o Azure AD B2C, mas ele pode não desconectar o usuário da sessão do provedor de identidade social. Se o usuário selecionar o mesmo provedor de identidade durante uma entrada subsequente, ele poderá se autenticar sem inserir suas credenciais. Se um usuário quiser sair do aplicativo, isso não significa necessariamente que deseja sair de sua conta do Facebook. No entanto, se forem usadas contas locais, a sessão do usuário será encerrada corretamente.

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>Logout único 

Quando você redireciona o usuário para o ponto de extremidade de saída do Azure AD B2C (para os protocolos OAuth2 e SAML), Azure AD B2C limpa a sessão do usuário do navegador. No entanto, o usuário ainda pode estar conectado a outros aplicativos que usam Azure AD B2C para autenticação. Para permitir que esses aplicativos desconectem o usuário simultaneamente, Azure AD B2C envia uma solicitação HTTP GET para o registrado `LogoutUrl` de todos os aplicativos aos quais o usuário está conectado no momento.

Os aplicativos devem responder a essa solicitação, limpando as sessões que identificam o usuário e retornando uma resposta `200`. Se você quiser dar suporte ao logout único em seu aplicativo, deverá implementar um `LogoutUrl` no código do aplicativo. 

Para dar suporte ao logout único, os perfis técnicos do emissor do token para JWT e SAML devem especificar:

- O nome do protocolo, como `<Protocol Name="OpenIdConnect" />`
- A referência ao perfil técnico da sessão, como `UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` .

O exemplo a seguir ilustra os emissores JWT e token SAML com Logout único:

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>Proteger seu redirecionamento de logout

Após o logout, o usuário é redirecionado para o URI especificado no `post_logout_redirect_uri` parâmetro, independentemente das URLs de resposta que foram especificadas para o aplicativo. No entanto, se um valor válido `id_token_hint` for passado e o **token de ID necessário em solicitações de logout** estiver ativado, Azure ad B2C verificará se o valor de `post_logout_redirect_uri` corresponde a um dos URIs de redirecionamento configurados do aplicativo antes de executar o redirecionamento. Se nenhuma URL de resposta correspondente tiver sido configurada para o aplicativo, uma mensagem de erro será exibida e o usuário não será redirecionado. 

::: zone pivot="b2c-user-flow"

Para exigir um token de ID em solicitações de logout:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém o locatário Azure ad B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Escolha **Fluxos de usuário**.
1. Abra o fluxo de usuários criado anteriormente.
1. Selecione **Propriedades**.
1. Habilite o **token de ID de solicitação em solicitações de logout**.
1. Volte para  **Azure ad B2C**.
1. Selecione **registros de aplicativo** e, em seguida, selecione seu aplicativo.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL de logout** , digite o URI de redirecionamento de logout de postagem e, em seguida, selecione **salvar**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Para exigir um token de ID em solicitações de logout, adicione um elemento **UserJourneyBehaviors** dentro do elemento [RelyingParty](relyingparty.md) . Em seguida, defina **EnforceIdTokenHintOnLogout** do elemento **logon único** como `true` . O elemento **UserJourneyBehaviors** deve ser semelhante a este exemplo:

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

Para configurar a URL de logout do aplicativo:

1. Entre no [portal do Azure](https://portal.azure.com).
1. Verifique se você está usando o diretório que contém seu locatário de Azure AD B2C selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém o locatário Azure ad B2C.
1. Escolha **Todos os serviços** no canto superior esquerdo do Portal do Azure, pesquise **Azure AD B2C** e selecione-o.
1. Selecione **registros de aplicativo** e, em seguida, selecione seu aplicativo.
1. Selecione **Autenticação**.
1. Na caixa de texto **URL de logout** , digite o URI de redirecionamento de logout de postagem e, em seguida, selecione **salvar**.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [Configurar tokens no Azure ad B2C](configure-tokens.md).
