---
title: 'Azure Active Directory B2C: KMSI| Microsoft Docs'
description: "Um tópico demonstrando como configurar “mantenha-me conectado”"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: ajalexander
ms.assetid: 926e9711-71c0-49e8-b658-146ffb7386c0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: d09e15c6f6765eac436f573f89c6703039cd8397
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: habilitar “Mantenha-me conectado no (KMSI)”  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

O Azure AD B2C agora permite que seus aplicativos Web e nativos habilitem a funcionalidade “Mantenha-me conectado no (KMSI)”. Esse recurso concede acesso a usuários que estejam retornando ao aplicativo sem solicitar a reinserção de nome de usuário e senha. Esse acesso é revogado quando o usuário faz logoff. 

Não é recomendável que os usuários marquem essa opção em computadores públicos. 

![img](images/kmsi.PNG)


## <a name="prerequisites"></a>Pré-requisitos

Um locatário do Azure AD B2C configurado para permitir inscrição/entrada em conta local, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Como habilitar KMSI

Faça as seguintes alterações à sua política de extensões de estrutura confiável.

## <a name="adding-a-content-definition-element"></a>Adicionando um elemento de definição de conteúdo 

O nó `BuildingBlocks` do seu arquivo de extensão deve incluir um elemento `ContentDefinitions`. 

1. Na seção `ContentDefinitions`, defina um novo `ContentDefinition` com a ID `api.signuporsigninwithkmsi`.
2. Seu novo `ContentDefinition` deve incluir `LoadUri`, `RecoveryUri` e `DataUri` da seguinte maneira.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` é um identificador compreensível por computador que abre uma caixa de seleção KMSI nas páginas de entrada. Não altere esse valor. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Adicionar um provedor de declarações de entrada de conta local 

Você pode definir A Entrada em Conta Local como um provedor de declarações para o nó `<ClaimsProvider>` no arquivo de extensão de sua política:

1. Abra o arquivo de extensão (TrustFrameworkExtensions.xml) no diretório de trabalho. 
2. Localize a seção `<ClaimsProviders>`. Caso ela não exista, adicione-a sob o nó raiz.
3. O pacote inicial de [Introdução](active-directory-b2c-get-started-custom.md) vem com um provedor de declarações de “Entradas de Conta Local”. 
4. Caso contrário, adicione um novo nó `<ClaimsProvider>` da seguinte maneira:

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>Adicionar as IDs do Aplicativo à política personalizada

Adicione as IDs de aplicativo ao arquivo de extensões (`TrustFrameworkExtensions.xml`):

1. No arquivo de extensões (TrustFrameworkExtensions.xml), localize os elementos `<TechnicalProfile Id="login-NonInteractive">` e `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Substitua todas as instâncias do `IdentityExperienceFrameworkAppId` pela ID do aplicativo Identity Experience Framework conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md). Aqui está um exemplo:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Substitua todas as instâncias do `ProxyIdentityExperienceFrameworkAppId` pela ID do aplicativo Proxy Identity Experience Framework conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).

4. Salve o arquivo de extensões.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Criar um KMSI no percurso de usuário habilitado

Agora você precisa adicionar o provedor de declarações de Entrada de Conta Local ao seu percurso de usuário. 

1. Abra o arquivo base da política (por exemplo, TrustFrameworkBase.xml).
2. Localize o elemento `<UserJourneys>` e copie todo o nó `<UserJourney>`, que inclui `Id="SignUpOrSignIn"`.
3. Abra o arquivo de extensão (por exemplo, TrustFrameworkExtensions.xml) e localize o elemento `<UserJourneys>`. Se o elemento não existir, adicione um.
4. Cole todo o nó `<UserJourney>` copiado como um filho do elemento `<UserJourneys>`.
5. Renomeie a ID do novo Percurso do Usuário (por exemplo, renomeie como `SignUpOrSignInWithKmsi`).
6. Por fim, em `OrchestrationStep 1`, altere `ContentDefinitionReferenceId` para `api.signuporsigninwithkmsi`, conforme definido nas etapas anteriores. Isso habilita a caixa de seleção no percurso do usuário. 
7. Agora você concluiu a modificação do arquivo de extensão. Salve e carregue esse arquivo. Certifique-se de que todas as validações tenham êxito.

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

## <a name="create-a-relying-party-rp-file"></a>Criar um arquivo de RP (terceira parte confiável)

Em seguida, atualize o arquivo de RP (terceira parte confiável) que iniciará o percurso do usuário que você criou:

1. Faça uma cópia de SignUpOrSignIn.xml em seu diretório de trabalho. Em seguida, renomeie-a (por exemplo, SignUpOrSignInWithKmsi.xml).

2. Abra o novo arquivo e atualize o atributo `PolicyId` de `<TrustFrameworkPolicy>` com um valor exclusivo. Esse é o nome da sua política (por exemplo, SignUpOrSignInWithKmsi).

3. Modifique o atributo `ReferenceId` em `<DefaultUserJourney>` para que ele corresponda a `Id` do novo percurso do usuário criado (por exemplo, SignUpOrSignInWithKmsi).

4. KMSI está configurado no `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** controla por quanto tempo o usuário permanece conectado. No exemplo a seguir, a sessão KMSI expira automaticamente depois de 14 dias, não importa a frequência com que o usuário realiza a autenticação sem confirmação.

   Configurar o valor `KeepAliveInDays` como 0 desativa a funcionalidade KMSI. Por padrão, esse valor é 0

6. Se **`SessionExpiryType`** for *Sem interrupção*, a sessão KMSI será estendida por 14 dias sempre que o usuário executar a autenticação sem confirmação.  Se *Sem interrupção* for selecionado, recomendamos que você mantenha o número de dias em um mínimo. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
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

7. Salve as alterações e, em seguida, carregue o arquivo.

8. Para testar a política personalizada que você carregou, no portal do Azure, vá até a folha de política e, em seguida, clique em **Executar agora**.


## <a name="link-to-sample-policy"></a>Link para a política de exemplo

Você pode encontrar a política de exemplo [aqui](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








