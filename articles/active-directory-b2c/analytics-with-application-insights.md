---
title: Acompanhar o comportamento do usuário com Application Insights
titleSuffix: Azure AD B2C
description: Saiba como habilitar logs de eventos em Application Insights de Azure AD B2C percursos do usuário.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218546"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Rastrear o comportamento do usuário no Azure Active Directory B2C usando o Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

O Azure Active Directory B2C (Azure AD B2C) dá suporte ao envio de dados de eventos diretamente para [Application insights](../azure-monitor/app/app-insights-overview.md) usando a chave de instrumentação fornecida para Azure ad B2C. Com um perfil técnico Application Insights, você pode obter logs de eventos detalhados e personalizados para que seus percursos do usuário:

* Obter insights sobre o comportamento do usuário.
* Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
* Medir o desempenho.
* Criar notificações do Application Insights.

## <a name="overview"></a>Visão geral

Para habilitar logs de eventos personalizados, você adiciona um perfil técnico de Application Insights. No perfil técnico, você define a chave de instrumentação Application Insights, o nome do evento e as declarações a serem registradas. Para postar um evento, o perfil técnico é adicionado como uma etapa de orquestração em uma [jornada do usuário](userjourneys.md).

Ao usar o Application Insights, considere o seguinte:

- Há um pequeno atraso, normalmente menos de cinco minutos, antes de novos logs disponíveis no Application Insights.
- Azure AD B2C permite que você escolha as declarações a serem registradas. Não inclua declarações com dados pessoais.
- Para registrar uma sessão de usuário, os eventos podem ser unificados usando uma ID de correlação. 
- Chame o perfil técnico de Application Insights diretamente de um percurso do [usuário](userjourneys.md) ou uma [sub-rotina](subjourneys.md). Não use Application Insights perfil técnico como um [perfil técnico de validação](validation-technical-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando você estiver usando o Application Insights com o Azure AD B2C, tudo o que você precisa fazer é criar um recurso e obter a chave de instrumentação. Para obter informações, consulte [criar um recurso de Application insights](../azure-monitor/app/create-new-resource.md)

1. Entre no [portal do Azure](https://portal.azure.com/).
2. Verifique se você está usando o diretório que contém sua assinatura do Azure selecionando o **diretório +** filtro de assinatura no menu superior e escolhendo o diretório que contém sua assinatura. Esse locatário não é o seu locatário do Azure Active Directory B2C.
3. Escolher **criar um recurso** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **Application Insights**.
4. Clique em **Criar**.
5. Insira um **nome** para o recurso.
6. Para **tipo de aplicativo**, selecione **aplicativo web ASP.NET**.
7. Para **grupo de recursos**, selecione um grupo existente ou digite um nome para um novo grupo.
8. Clique em **Criar**.
4. Depois de criar o recurso Application Insights, abra-o, expanda **Essentials** e copie a chave de instrumentação.

![Visão geral do Application Insights e Chave de Instrumentação](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definir declarações

Uma declaração fornece um armazenamento temporário de dados durante uma execução de política de Azure AD B2C. O [esquema de declarações](claimsschema.md) é o lugar em que você declara suas declarações.

1. Abra o arquivo de extensões da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>.
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema](claimsschema.md). Se o elemento não existir, adicione-o.
1. Adicione as seguintes declarações ao elemento **ClaimsSchema**. 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Adicionar novos perfis técnicos

Os perfis técnicos podem ser considerados funções na política personalizada. Esta tabela define os perfis técnicos que são usados para abrir uma sessão e postar eventos. A solução usa a abordagem de [inclusão de perfil técnico](technicalprofiles.md#include-technical-profile) . Em que um perfil técnico inclui outro perfil técnico para alterar as configurações ou adicionar uma nova funcionalidade.

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AppInsights-Common | O perfil técnico comum com o conjunto comum de configuração. Incluindo, a chave de instrumentação de Application Insights, a coleta de declarações a serem registradas e o modo de desenvolvedor. Os seguintes perfis técnicos incluem o perfil técnico comum e adicionam mais declarações, como o nome do evento. |
| AppInsights-SignInRequest | Registra um `SignInRequest` evento com um conjunto de declarações quando uma solicitação de entrada é recebida. |
| AppInsights-UserSignUp | Registra um `UserSignUp` evento quando o usuário aciona a opção de inscrição em uma jornada de inscrição/entrada. |
| AppInsights-SignInComplete | Registra um `SignInComplete` evento após a conclusão bem-sucedida de uma autenticação, quando um token foi enviado para o aplicativo de terceira parte confiável. |

Adicione os perfis para o *trustframeworkextensions. XML* arquivo do starter pack. Adicione esses elementos para o **ClaimsProviders** elemento:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Altere a chave de instrumentação no perfil técnico `AppInsights-Common` para o GUID que seu recurso do Application Insights fornece.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Adicionar os perfis técnicos como etapas de orquestração

Chame `AppInsights-SignInRequest` como etapa de orquestração 2 para rastrear se uma solicitação de inscrição/entrada foi recebida:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente *antes* da etapa de orquestração `SendClaims`, adicione uma nova etapa que chama `AppInsights-UserSignup`. É disparado quando o usuário seleciona o botão de inscrição em um percurso de inscrição/entrada.

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
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Imediatamente após a etapa de orquestração `SendClaims` chame `AppInsights-SignInComplete`. Esta etapa mostra um percurso concluído com êxito.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Após adicionar as novas etapas de orquestração, renumere as etapas sequencialmente sem ignorar números inteiros de 1 a N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Carregar seu arquivo, executar a política e exibir eventos

Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário. Em seguida, chame a política da terceira parte confiável de seu aplicativo ou use **Executar agora** no portal do Azure. Aguarde um minuto, e seus eventos estarão disponíveis em Application Insights.

1. Abra o recurso do **Application Insights** no locatário do Azure Active Directory.
2. Selecione **uso** e, em seguida, **eventos**.
3. Defina **Durante** para **Última hora** e **Por** para **3 minutos**.  Talvez você precise selecionar **Atualizar** para exibir os resultados.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Coletar mais dados

Para atender às suas necessidades de negócios, talvez você queira registrar mais declarações. Para adicionar uma declaração, primeiro [defina uma declaração](#define-claims)e, em seguida, adicione a declaração à coleção de declarações de entrada. As declarações que você adicionar ao perfil técnico *AppInsights-comum* serão exibidas em todos os eventos. As declarações que você adicionar a um perfil técnico específico serão exibidas somente nesse evento. O elemento de declaração de entrada contém os seguintes atributos:

- **ClaimTypeReferenceId** -é a referência a um tipo de declaração. 
- **PartnerClaimType** -é o nome da propriedade que aparece no Azure insights. Use a sintaxe da `{property:NAME}`, onde `NAME` é a propriedade que está sendo adicionada ao evento.
- **DefaultValue** -um valor predefinido a ser registrado, como o nome do evento. Uma declaração que é usada no percurso do usuário, como o nome do provedor de identidade. Se a declaração estiver vazia, o valor padrão será usado. Por exemplo, a `identityProvider` declaração é definida pelos perfis técnicos da Federação, como o Facebook. Se a declaração estiver vazia, ela indicará a entrada do usuário com uma conta local. Portanto, o valor padrão é definido como *local*. Você também pode registrar um [resolvedor de declaração](claim-resolver-overview.md) com um valor contextual, como a ID do aplicativo ou o endereço IP do usuário.

### <a name="manipulating-claims"></a>Manipulando declarações

Você pode usar [transformações de declarações de entrada](custom-policy-trust-frameworks.md#manipulating-your-claims) para modificar as declarações de entrada ou gerar novas, antes de enviar para Application insights. No exemplo a seguir, o perfil técnico inclui a transformação de declarações de entrada *CheckIsAdmin* . 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Adicionar eventos

Para adicionar um evento, crie um novo perfil técnico que inclua o perfil técnico *AppInsights-comum* . Em seguida, adicione o perfil técnico como etapa de orquestração ao percurso do [usuário](custom-policy-trust-frameworks.md#orchestration-steps). Use a [pré-condição](userjourneys.md#preconditions) para disparar o evento quando desejado. Por exemplo, relate o evento somente quando os usuários executam por meio da MFA.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Agora que você tem um perfil técnico, adicione o evento à jornada do usuário. Em seguida, renumere as etapas sequencialmente sem ignorar nenhum inteiro de 1 a N.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Habilitar o modo de desenvolvedor

Ao usar o Application Insights para definir eventos, você pode indicar se o modo de desenvolvedor está habilitado. O modo de desenvolvedor controla como os eventos são armazenados em buffer. Em um ambiente de desenvolvimento com volume mínimo de eventos, habilitar o modo de desenvolvedor resulta em eventos enviados imediatamente para Application Insights. O valor padrão é `false`. Não habilite o modo de desenvolvedor em ambientes de produção.

Para habilitar o modo de desenvolvedor, no perfil técnico *AppInsights-comum* , altere os `DeveloperMode` metadados para `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Desabilitar telemetria

Para desabilitar os logs do Application insights, no perfil técnico *AppInsights-Common* , altere os `DisableTelemetry` metadados para `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Próximas etapas

- Saiba como [criar painéis de KPI personalizados usando o aplicativo Azure insights](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end