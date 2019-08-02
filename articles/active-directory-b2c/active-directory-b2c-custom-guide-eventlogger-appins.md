---
title: Rastrear o comportamento do usuário usando eventos no Application Insights do Azure Active Directory B2C | Microsoft Docs
description: Saiba como ativar os logs de eventos no Application Insights a partir de jornadas do usuário do Azure AD B2C usando políticas personalizadas (visualização).
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e18157c95dac0de90c50b4b7e8591e32c5b76aaf
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227240"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Rastrear o comportamento do usuário no Azure Active Directory B2C usando o Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Ao usar o B2C do Azure Active Directory (Azure AD) junto com o Azure Application Insights, você pode obter logs de eventos detalhados e personalizados para as jornadas do usuário. Neste artigo, você aprenderá a:

* Obter insights sobre o comportamento do usuário.
* Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Criar notificações do Application Insights.

## <a name="how-it-works"></a>Como ele funciona

A estrutura de experiência de identidade no Azure AD B2C inclui o provedor `Handler="Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0`. Ele envia dados de eventos diretamente para o Application Insights usando a chave de instrumentação fornecida para o Azure Active Directory B2C.

Um perfil técnico usa esse provedor para definir um evento do Azure AD B2C. O perfil especifica o nome do evento, as reivindicações registradas e a chave de instrumentação. Para postar um evento, o perfil técnico é então adicionado como `orchestration step` ou como `validation technical profile` em uma jornada de usuário personalizada.

O Application Insights pode unificar os eventos usando uma ID de correlação para registrar uma sessão do usuário. O Application Insights disponibiliza o evento e a sessão em segundos e apresenta muitas ferramentas analíticas, de exportação e visualização.

## <a name="prerequisites"></a>Pré-requisitos

Conclua as etapas em [Introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md). Este artigo pressupõe que você esteja usando o pacote de inicializador de política personalizada. Mas o pacote de inicializador não é necessário.

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando você estiver usando o Application Insights com o Azure AD B2C, tudo o que você precisa fazer é criar um recurso e obter a chave de instrumentação.

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém sua assinatura do Azure clicando no **Diretório e no filtro de inscrição** no menu superior e escolhendo o diretório que contém sua assinatura. Esse locatário não é o seu locatário do Azure Active Directory B2C.
3. Escolher **criar um recurso** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **Application Insights**.
4. Clique em **Criar**.
5. Insira um **nome** para o recurso.
6. Para **tipo de aplicativo**, selecione **aplicativo web ASP.NET**.
7. Para **grupo de recursos**, selecione um grupo existente ou digite um nome para um novo grupo.
8. Clique em **Criar**.
4. Depois de criar o recurso Application Insights, abra-o, expanda **Essentials** e copie a chave de instrumentação.

![Visão geral do Application Insights e Chave de Instrumentação](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-insights.png)

## <a name="add-new-claimtype-definitions"></a>Adicionar novas definições de ClaimType

Abra o arquivo *TrustFrameworkExtensions.xml* do pacote inicial e adicione os seguintes elementos ao elemento [ BuildingBlocks](buildingblocks.md):

```xml
<ClaimsSchema>
  <ClaimType Id="EventType">
    <DisplayName>EventType</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="PolicyId">
    <DisplayName>PolicyId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="Culture">
    <DisplayName>Culture</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="CorrelationId">
    <DisplayName>CorrelationId</DisplayName>
    <DataType>string</DataType>
    <AdminHelpText />
    <UserHelpText />
  </ClaimType>
  <!--Additional claims used for passing claims to Application Insights Provider -->
  <ClaimType Id="federatedUser">
    <DisplayName>federatedUser</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
  <ClaimType Id="parsedDomain">
    <DisplayName>Parsed Domain</DisplayName>
    <DataType>string</DataType>
    <UserHelpText>The domain portion of the email address.</UserHelpText>
  </ClaimType>
  <ClaimType Id="userInLocalDirectory">
    <DisplayName>userInLocalDirectory</DisplayName>
    <DataType>boolean</DataType>
    <UserHelpText />
  </ClaimType>
</ClaimsSchema>
```

## <a name="add-new-technical-profiles"></a>Adicionar novos perfis técnicos

Perfis técnicos podem ser considerados funções na Estrutura de Experiência de Identidade do Azure Active Directory B2C. Esta tabela define os perfis técnicos que são usados para abrir uma sessão e postar eventos.

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AzureInsights-Common | Cria um conjunto comum de parâmetros a serem incluídos em todos os perfis técnicos do AzureInsights. | 
| AzureInsights-SignInRequest | Cria um evento SignIn com um conjunto de declarações quando uma solicitação de entrada foi recebida. | 
| AzureInsights-UserSignup | Cria um evento SignIn com um conjunto de declarações quando uma solicitação de entrada foi recebida. | 
| AzureInsights-SignInComplete | Registra a conclusão bem-sucedida de uma autenticação quando um token foi enviado ao aplicativo da terceira parte confiável. | 

Adicione os perfis para o *trustframeworkextensions. XML* arquivo do starter pack. Adicione esses elementos para o **ClaimsProviders** elemento:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-UserSignup">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
    </TechnicalProfile>
    <TechnicalProfile Id="AzureInsights-Common">
      <DisplayName>Alternate Email</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <!-- A Boolean that indicates whether developer mode is enabled. This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights. -->
        <Item Key="DeveloperMode">false</Item>
        <!-- A Boolean that indicates whether telemetry should be enabled or not. -->
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Altere a chave de instrumentação no perfil técnico `AzureInsights-Common` para o GUID que seu recurso do Application Insights fornece.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adicionar os perfis técnicos como etapas de orquestração

Chame `Azure-Insights-SignInRequest` como etapa de orquestração 2 para rastrear se uma solicitação de inscrição/entrada foi recebida:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes* da etapa de orquestração `SendClaims`, adicione uma nova etapa que chama `Azure-Insights-UserSignup`. É disparado quando o usuário seleciona o botão de inscrição em um percurso de inscrição/entrada.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente após a etapa de orquestração `SendClaims` chame `Azure-Insights-SignInComplete`. Esta etapa mostra um percurso concluído com êxito.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Após adicionar as novas etapas de orquestração, renumere as etapas sequencialmente sem ignorar números inteiros de 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Carregar seu arquivo, executar a política e exibir eventos

Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário. Em seguida, chame a política da terceira parte confiável de seu aplicativo ou use **Executar agora** no portal do Azure. Em segundos, os eventos estarão disponíveis no Application Insights.

1. Abra o recurso do **Application Insights** no locatário do Azure Active Directory.
2. Selecionar **Eventos** > **de Uso**.
3. Defina **Durante** para **Última hora** e **Por** para **3 minutos**.  Talvez você precise selecionar **Atualizar** para exibir os resultados.

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)

## <a name="next-steps"></a>Próximas etapas

Adicione tipos de declarações e eventos ao percurso do usuário para ajustar às suas necessidades. Você pode usar [resolvedores de declaração](claim-resolver-overview.md) ou qualquer tipo de declaração de sequência, adicionar as declarações adicionando um elemento **Entrada de declaração** ao evento Application Insights ou ao perfil técnico Comum do AzureInsights. 

- **ClaimTypeReferenceId** é a referência a um tipo de declaração.
- **PartnerClaimType** é o nome da propriedade que é exibido no Azure Insights. Use a sintaxe da `{property:NAME}`, onde `NAME` é a propriedade que está sendo adicionada ao evento. 
- **DefaultValue** usa qualquer valor de cadeia ou o resolvedor de reclamações. 

```XML
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

