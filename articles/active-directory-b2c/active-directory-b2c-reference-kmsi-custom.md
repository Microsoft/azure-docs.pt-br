---
title: Mantenha-Me Conectado no Azure Active Directory B2C | Microsoft Docs
description: Aprenda a configurar o KMSI (Mantenha-me conectado) no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e99dacbe7ae0f42919616e04e60bf4f21b9bd985
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835375"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Habilitar o KMSI (Mantenha-me conectado) no Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

É possível habilitar a funcionalidade KMSI (Mantenha-me conectado) para seus aplicativos Web e nativos no Azure Active Directory (Azure AD) B2C. Esse recurso concede acesso ao aplicativo para usuários que estejam retornando, sem solicitar a reinserção de nome de usuário e senha. Esse acesso é revogado quando o usuário sai do serviço.

Os usuários não devem habilitar essa opção em computadores públicos.

![Inscreva-se entrar página de exemplo mostrando uma Mantenha-me conectado na caixa de seleção](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Pré-requisitos

Um locatário do Azure AD B2C que está configurado para permitir inscrição e entrada em conta local. Caso não tenha um locatário, você pode criar um usando as etapas em [Tutorial: Criar um locatário do Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Adicionar um elemento de definição de conteúdo

No elemento **BuildingBlocks** do seu arquivo de extensão, adicione em elemento **ContentDefinitions**.

1. No elemento **ContentDefinitions**, adicione um elemento **ContentDefinition** com um identificador de `api.signuporsigninwithkmsi`.
2. No elemento **ContentDefinition**, adicione os elementos **LoadUri**, **RecoveryUri** e **DataUri**. O valor `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` do elemento **DataUri** é um identificador compreensível por computador que abre uma caixa de seleção do KMSI nas páginas de entrada. Esse valor não deve ser alterado.

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Adicionar um provedor de declarações de entrada para uma conta local

É possível definir a entrada de conta local como provedor de declarações usando o elemento **ClaimsProvider** no arquivo de extensão da sua política:

1. Abra o arquivo *TrustFrameworkExtensions.xml* no seu diretório de trabalho.
2. Localize o elemento **ClaimsProviders**. Caso ele não exista, adicione-o sob o elemento raiz. O [pacote de inicialização](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) inclui um provedor de declarações de entrada de conta local.
3. Adicione um elemento **ClaimsProvider** com o **DisplayName** e o **TechnicalProfile** conforme mostrado neste exemplo:

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Adicionar os identificadores do aplicativo à política personalizada

Adicione os identificadores do aplicativo ao arquivo *TrustFrameworkExtensions.xml*.

1. No arquivo *TrustFrameworkExtensions.xml*, localize o elemento **TechnicalProfile** com o identificador de `login-NonInteractive` e o elemento **TechnicalProfile** com um identificador de `login-NonInteractive-PasswordChange` e substitua todos os valores de `IdentityExperienceFrameworkAppId` pelo identificador de aplicativo do aplicativo Identity Experience Framework, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Substitua todos os valores de `ProxyIdentityExperienceFrameworkAppId` pelo identificador de aplicativo do aplicativo Proxy Identity Experience Framework, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).
3. Salve o arquivo de extensões.

## <a name="create-a-kmsi-enabled-user-journey"></a>Criar um percurso do usuário habilitado por KMSI

Adicione o provedor de declarações de entrada para uma conta local ao seu percurso do usuário.

1. Abra o arquivo base da sua política. Por exemplo, *TrustFrameworkBase.xml*.
2. Localize o elemento **UserJourneys** e copie o conteúdo inteiro do elemento **UserJourney** que usa o identificador de `SignUpOrSignIn`.
3. Abra o arquivo de extensão. Por exemplo, *TrustFrameworkExtensions.xml* e localize o elemento **UserJourneys**. Se o elemento não existir, adicione um.
4. Cole todo o elemento **UserJourney** que copiou como um filho do elemento **UserJourneys**.
5. Altere o valor do identificador para o novo percurso do usuário. Por exemplo, `SignUpOrSignInWithKmsi`.
6. Por fim, na primeira etapa de orquestração, altere o valor de **ContentDefinitionReferenceId** para `api.signuporsigninwithkmsi`. A configuração desse valor habilita a caixa de seleção no percurso do usuário.
7. Salve e faça o upload desse arquivo e verifique se todas as validações são bem-sucedidas.

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>Criar um arquivo de terceira parte confiável

Atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou.

1. Faça uma cópia do arquivo *SignUpOrSignIn.xml* no seu diretório de trabalho e, em seguida, renomeie-o. Por exemplo, *SignUpOrSignInWithKmsi.xml*.
2. Abra o novo arquivo e atualize o atributo **PolicyId** para a **TrustFrameworkPolicy** com um valor exclusivo. Esse é o nome da sua política. Por exemplo, `SignUpOrSignInWithKmsi`.
3. Altere o atributo **ReferenceId** para o elemento **DefaultUserJourney** para coincidir com o identificador do novo percurso do usuário que você criou. Por exemplo, `SignUpOrSignInWithKmsi`.

    O KMSI foi configurado usando o elemento **UserJourneyBehaviors** com **SingleSignOn**,  **SessionExpiryType** e **SessionExpiryInSeconds** como os primeiros elementos filhos. O atributo **KeepAliveInDays** controla por quanto tempo o usuário permanece conectado. No exemplo a seguir, a sessão do KMSI expira automaticamente depois de `7` dias, não importa a frequência com que o usuário realiza a autenticação sem confirmação. A configuração do valor **KeepAliveInDays** para `0` desativa a funcionalidade KMSI. Por padrão, esse valor é `0`. Se o valor de **SessionExpiryType** for `Rolling`, a sessão do KMSI será estendida por `7` dias sempre que o usuário executar a autenticação sem confirmação.  Caso `Rolling` seja selecionado, mantenha o número de dias no mínimo.

    O valor de **SessionExpiryInSeconds** representa o horário de expiração de uma sessão de SSO. Ele é usado internamente pelo Azure AD B2C para verificar se a sessão do KMSI expirou ou não. O valor de **KeepAliveInDays** determina o valor Expires/Max-Age do cookie de SSO no navegador da Web. Ao contrário de **SessionExpiryInSeconds**, **KeepAliveInDays** é usado para impedir o navegador de limpar o cookie quando for fechado. Um usuário só poderá entrar sem autenticação se o cookie da sessão SSO existir, o que é controlado por **KeepAliveInDays** e não tiver expirado, o que é controlado por **SessionExpiryInSeconds**.

    Se um usuário não permite **Mantenha-me conectado** na página de inscrição e entrada, uma sessão expira após o tempo indicado pelo **SessionExpiryInSeconds** tenha passado ou o navegador está fechado. Se um usuário habilita **Mantenha-me conectado**, o valor de **KeepAliveInDays** substitui o valor de **SessionExpiryInSeconds** e determina o tempo de expiração de sessão. Mesmo se os usuários fecharem o navegador e o abrirem novamente, eles poderão entrar silenciosamente enquanto estiverem dentro do tempo de **KeepAliveInDays**. É recomendável que você defina o valor de **SessionExpiryInSeconds** para um período curto (1.200 segundos), enquanto o valor de **KeepAliveInDays** pode ser definido como um período relativamente longo (7 dias), conforme mostrado no exemplo a seguir:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
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
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. Salve as alterações e, em seguida, faça o upload do arquivo.
5. Para testar a política personalizada que você carregou, no portal do Azure, vá até a página de política e, em seguida, clique em **Executar agora**.

Você pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








