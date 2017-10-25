---
title: "Azure Active Directory B2C: as trocas de declarações da API REST como uma etapa de orquestração | Microsoft Docs"
description: "Um tópico sobre as políticas personalizadas do Azure Active Directory B2C que se integram com uma API"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.openlocfilehash: dc319c97e64e55861b84cc3943667418077a05d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>Passo a passo: integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como uma etapa de orquestração

A IEF (Estrutura de Experiência de Identidade) subjacente ao Azure AD B2C (Azure Active Directory B2C) permite que o desenvolvedor de identidade integre uma interação com uma API RESTful em um percurso do usuário.  

Ao final deste passo a passo, você estará apto a criar um percurso do usuário do Azure AD B2C que interage com serviços RESTful.

A IEF envia dados em declarações e recebe dados de volta em declarações. A troca de declarações da API REST:

- Pode ser projetada como uma etapa de orquestração.
- Pode disparar uma ação externa. Por exemplo, ela pode registrar um evento em um banco de dados externo.
- Pode ser usada para buscar um valor e, em seguida, armazená-lo no banco de dados do usuário.

Você pode usar as declarações recebidas posteriormente para alterar o fluxo de execução.

Você também pode projetar a interação como um perfil de validação. Para obter mais informações, veja [Passo a passo: integrar as trocas de declarações da API REST no seu percurso do usuário do Azure AD B2C como validação sobre a entrada do usuário](active-directory-b2c-rest-api-validation-custom.md).

O cenário é aquele em que, quando um usuário realiza uma edição de perfil, desejamos:

1. Procurar pelo usuário em um sistema externo.
2. Obter a cidade em que o usuário está registrado.
3. Retornar o atributo para o aplicativo como uma declaração.

## <a name="prerequisites"></a>Pré-requisitos

- Um locatário do Azure AD B2C configurado para concluir uma inscrição/entrada de conta local, conforme descrito em [Introdução](active-directory-b2c-get-started-custom.md).
- Um ponto de extremidade de API REST com o qual se irá interagir. Este passo a passo usa um webhook de aplicativo de funções simples do Azure como um exemplo.
- *Recomendado*: conclua o [passo a passo da troca de declarações da API REST como uma etapa de validação](active-directory-b2c-rest-api-validation-custom.md).

## <a name="step-1-prepare-the-rest-api-function"></a>Etapa 1: preparar a função da API REST

> [!NOTE]
> A configuração das funções da API REST está fora do escopo deste artigo. O [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) fornece um kit de ferramentas excelente para criar serviços RESTful na nuvem.

Definimos uma função do Azure que recebe uma declaração chamada `email` e, em seguida, retorna a declaração `city` com o valor atribuído de `Redmond`. A função do Azure de exemplo está no [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples).

A declaração `userMessage` que a função do Azure retorna é opcional nesse contexto e a IEF vai ignorá-la. Você pode usá-la como uma mensagem passada para o aplicativo e apresentada ao usuário mais tarde.

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

Um aplicativo de funções do Azure facilita a obtenção da URL da função, a qual inclui o identificador da função específica. Nesse caso, a URL é: https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==. Você pode usá-la para teste.

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>Etapa 2: configurar a troca de declarações da API RESTful como um perfil técnico no arquivo TrustFrameworExtensions.xml

Um perfil técnico é a configuração completa da troca desejada com o serviço RESTful. Abra o arquivo TrustFrameworkExtensions.xml e adicione o seguinte trecho de código XML dentro do elemento `<ClaimsProvider>`.

> [!NOTE]
> No XML a seguir, o provedor RESTful `Version=1.0.0.0` é descrito como o protocolo. Considere-o como a função que interagirá com o serviço externo. <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
            </InputClaims>
            <OutputClaims>
                <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
            </OutputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

O elemento `<InputClaims>` define as declarações que serão enviadas pela IEF para o serviço REST. Neste exemplo, o conteúdo da declaração `givenName` será enviado para o serviço REST como a declaração `email`.  

O elemento `<OutputClaims>` define as declarações que a IEF espera do serviço REST. Independentemente do número de declarações recebidas, a IEF usará apenas aquelas identificadas aqui. Neste exemplo, uma declaração recebida como `city` será mapeada para uma declaração da IEF chamada `city`.

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>Etapa 3: adicionar a nova declaração `city` ao esquema do arquivo TrustFrameworkExtensions.xml

A declaração `city` não está definida em nenhum outro lugar no nosso esquema. Portanto, adicione uma definição dentro do elemento `<BuildingBlocks>`. Você encontra esse elemento no início do arquivo TrustFrameworkExtensions.xml.

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
    <ClaimsSchema>
        <ClaimType Id="city">
            <DisplayName>City</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your city</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>Etapa 4: incluir a troca de declarações do serviço REST como uma etapa de orquestração em seu percurso do usuário de edição de perfil no TrustFrameworkExtensions.xml

Adicione uma etapa ao percurso do usuário de edição de perfil após a autenticação do usuário (etapas de orquestração 1 a 4 no XML a seguir) e depois que ele tenha fornecido as informações de perfil atualizado (etapa 5).

> [!NOTE]
> Há muitos casos de uso em que a chamada à API REST pode ser usada como uma etapa de orquestração. Como uma etapa de orquestração, ela pode ser usada como uma atualização para um sistema externo depois que um usuário tenha concluído uma tarefa com êxito, como o primeiro registro, ou como uma atualização de perfil para manter as informações sincronizadas. Nesse caso, ela é usada para aumentar as informações fornecidas para o aplicativo depois da edição do perfil.

Copie o código XML do percurso do usuário de edição de perfil do arquivo TrustFrameworkBase.xml para o seu arquivo TrustFrameworkExtensions.xml dentro do elemento `<UserJourneys>`. Em seguida, faça a modificação conforme a etapa 6.

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Se a ordem não corresponde à sua versão, verifique se você inseriu o código como a etapa antes do tipo `ClaimsExchange` `SendClaims`.

O XML final da jornada do usuário deve ter esta aparência:

```XML
<UserJourney Id="ProfileEdit">
    <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
            <ClaimsProviderSelections>
                <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
                <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
                <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>localAccountAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="ClaimsExchange">
            <Preconditions>
                <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
                    <Value>authenticationSource</Value>
                    <Value>socialIdpAuthentication</Value>
                    <Action>SkipThisOrchestrationStep</Action>
                </Precondition>
            </Preconditions>
            <ClaimsExchanges>
                <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="5" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <!-- Add a step 6 to the user journey before the JWT token is created-->
        <OrchestrationStep Order="6" Type="ClaimsExchange">
            <ClaimsExchanges>
                <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
            </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    </OrchestrationSteps>
    <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>Etapa 5: adicionar a declaração `city` ao seu arquivo de política de terceira parte confiável para que a declaração seja enviada ao seu aplicativo

Edite o arquivo RP (terceira parte confiável), ProfileEdit.xml e modifique o elemento `<TechnicalProfile Id="PolicyProfile">` para adicionar o seguinte: `<OutputClaim ClaimTypeReferenceId="city" />`.

Depois de adicionar a nova declaração, o perfil técnico terá esta aparência:

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="step-6-upload-your-changes-and-test"></a>Etapa 6: carregar suas alterações e testar

Substitua as versões existentes da política.

1.  (Opcional:) Salve a versão existente (baixando-a) do seu arquivo de extensões antes de continuar. Para reduzir a complexidade inicial, é recomendável que você não carregue várias versões do arquivo de extensões.
2.  (Opcional:) Renomeie a nova versão da ID de política do arquivo de edição de política alterando `PolicyId="B2C_1A_TrustFrameworkProfileEdit"`.
3.  Carregue o arquivo de extensões.
4.  Carregue o arquivo RP de edição de política.
5.  Use **Executar Agora** para testar a política. Examine o token que a IEF retorna ao aplicativo.

Se tudo estiver configurado corretamente, o token incluirá a nova declaração `city`, com o valor `Redmond`.

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Próximas etapas

[Usar uma API REST como uma etapa de validação](active-directory-b2c-rest-api-validation-custom.md)

[Modificar a edição de perfil para coletar informações adicionais de seus usuários](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
