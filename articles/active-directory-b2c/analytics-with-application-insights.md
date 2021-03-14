---
title: Acompanhar o comportamento do usuário usando Application Insights
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
ms.openlocfilehash: 92da0b12a3119b048866eef5b18f658916595294
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101645918"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Acompanhe o comportamento do usuário em Azure AD B2C usando Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

No Azure Active Directory B2C (Azure AD B2C), você pode enviar dados de eventos diretamente para [Application insights](../azure-monitor/app/app-insights-overview.md) usando a chave de instrumentação fornecida para Azure ad B2C. Com um perfil técnico Application Insights, você pode obter logs de eventos detalhados e personalizados para que seus percursos do usuário:

- Obter insights sobre o comportamento do usuário.
- Solucionar problemas de suas próprias políticas em desenvolvimento ou em produção.
- Medir o desempenho.
- Criar notificações do Application Insights.

## <a name="overview"></a>Visão geral

Para habilitar logs de eventos personalizados, adicione um perfil técnico de Application Insights. No perfil técnico, você define a chave de instrumentação de Application Insights, o nome do evento e as declarações a serem registradas. Para postar um evento, adicione o perfil técnico como uma etapa de orquestração em uma [jornada do usuário](userjourneys.md).

Ao usar Application Insights, considere o seguinte:

- Há um pequeno atraso, normalmente menos de cinco minutos, antes que novos logs estejam disponíveis em Application Insights.
- Azure AD B2C permite que você escolha quais declarações registrar. Não inclua declarações com dados pessoais.
- Para registrar uma sessão de usuário, você pode usar uma ID de correlação para unificar eventos.
- Chame o perfil técnico de Application Insights diretamente de uma [jornada do usuário](userjourneys.md) ou uma [sub-rotina](subjourneys.md). Não use um perfil técnico Application Insights como um [perfil técnico de validação](validation-technical-profile.md).

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Criar um recurso do Application Insights

Quando você usa Application Insights com Azure AD B2C, tudo o que você precisa fazer é criar um recurso e obter a chave de instrumentação. Para obter informações, consulte [criar um recurso de Application insights](../azure-monitor/app/create-new-resource.md).

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Verifique se você está usando o diretório que tem sua assinatura do Azure. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o diretório que contém sua assinatura do Azure. Esse locatário não é seu locatário Azure AD B2C.
1. Escolha **criar um recurso** no canto superior esquerdo do portal do Azure e, em seguida, procure e selecione **Application insights**.
1. Selecione **Criar**.
1. Para **nome**, insira um nome para o recurso.
1. Para **tipo de aplicativo**, selecione **aplicativo web ASP.NET**.
1. Para **grupo de recursos**, selecione um grupo existente ou digite um nome para um novo grupo.
1. Selecione **Criar**.
1. Abra o novo recurso Application Insights, expanda **Essentials** e copie a chave de instrumentação.

![Captura de tela que mostra a chave de instrumentação na guia Visão geral Application Insights.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definir declarações

Uma declaração fornece armazenamento temporário de dados durante uma execução de política do Azure AD B2C. Você declara suas declarações no [elemento ClaimsSchema](claimsschema.md).

1. Abra o arquivo de extensões da sua política. O arquivo pode ser semelhante a `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. Pesquise o elemento [BuildingBlocks](buildingblocks.md). Se você não vir o elemento, adicione-o.
1. Localize o elemento **ClaimsSchema** . Se você não vir o elemento, adicione-o.
1. Adicione as seguintes declarações ao elemento **ClaimsSchema** :

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

Os perfis técnicos podem ser considerados funções na política personalizada. Essas funções usam a abordagem de [inclusão de perfil técnico](technicalprofiles.md#include-technical-profile) , em que um perfil técnico inclui outro perfil técnico e altera as configurações ou adiciona uma nova funcionalidade. A tabela a seguir define os perfis técnicos que são usados para abrir uma sessão e postar eventos.

| Perfil técnico | Tarefa |
| ----------------- | -----|
| AppInsights-Common | O perfil técnico comum com configuração típica. Ele inclui a chave de instrumentação de Application Insights, uma coleção de declarações a serem registradas e o modo de desenvolvedor. Os outros perfis técnicos incluem o perfil técnico comum e adicionam mais declarações, como o nome do evento. |
| AppInsights-SignInRequest | Registra um evento **SignInRequest** com um conjunto de declarações quando uma solicitação de entrada é recebida. |
| AppInsights-UserSignUp | Registra um evento **usersignup** quando o usuário aciona a opção de inscrição em uma jornada de inscrição ou entrada. |
| AppInsights-SignInComplete | Registra um evento **SignInComplete** após a autenticação bem-sucedida, quando um token foi enviado para o aplicativo de terceira parte confiável. |

Abra o arquivo *TrustFrameworkExtensions.xml* do pacote inicial. Adicione os perfis técnicos ao elemento **Claims** :

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

Adicione novas etapas de orquestração que se referem aos perfis técnicos.

> [!IMPORTANT]
> Após adicionar as novas etapas de orquestração, renumere as etapas sequencialmente sem ignorar números inteiros de 1 a N.

1. Chame `AppInsights-SignInRequest` como a segunda etapa de orquestração. Esta etapa acompanha que uma solicitação de inscrição ou de entrada foi recebida.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Antes da `SendClaims` etapa de orquestração, adicione uma nova etapa que chama `AppInsights-UserSignup` . Ele é disparado quando o usuário seleciona o botão de inscrição em uma jornada de inscrição ou entrada.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
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

1. Após a `SendClaims` etapa de orquestração, chame `AppInsights-SignInComplete` . Esta etapa mostra um percurso concluído com êxito.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Carregar seu arquivo, executar a política e exibir eventos

Faça upload do arquivo *TrustFrameworkExtensions.xml* no seu locatário. Em seguida, chame a política de terceira parte confiável do seu aplicativo ou use **executar agora** no portal do Azure. Aguarde até que os eventos estejam disponíveis no Application Insights.

1. Abra o recurso do **Application Insights** no locatário do Azure Active Directory.
1. Selecione **uso** e, em seguida, selecione **eventos**.
1. Defina **Durante** para **Última hora** e **Por** para **3 minutos**. Talvez seja necessário atualizar a janela para ver os resultados.

![Captura de tela que mostra Application Insights estatísticas de evento.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Coletar mais dados

Para atender às suas necessidades de negócios, talvez você queira registrar mais declarações. Para adicionar uma declaração, primeiro [defina uma declaração](#define-claims)e, em seguida, adicione a declaração à coleção de declarações de entrada. As declarações adicionadas ao perfil técnico **AppInsights-comum** aparecem em todos os eventos. As declarações que você adiciona a um perfil técnico específico aparecem apenas nesse evento. O elemento de declaração de entrada contém os seguintes atributos:

- **ClaimTypeReferenceId** é a referência a um tipo de declaração.
- **PartnerClaimType** é o nome da propriedade que é exibido no Azure Insights. Use a sintaxe de `{property:NAME}` , em que `NAME` é uma propriedade que está sendo adicionada ao evento.
- **DefaultValue** é um valor predefinido a ser registrado, como um nome de evento. Se uma declaração usada no percurso do usuário estiver vazia, o valor padrão será usado. Por exemplo, a `identityProvider` declaração é definida pelos perfis técnicos da Federação, como o Facebook. Se a declaração estiver vazia, indicará que o usuário entrou com uma conta local. Portanto, o valor padrão é definido como **local**. Você também pode registrar um [resolvedor de declaração](claim-resolver-overview.md) com um valor contextual, como a ID do aplicativo ou o endereço IP do usuário.

### <a name="manipulate-claims"></a>Manipular declarações

Você pode usar as [transformações de declarações de entrada](custom-policy-trust-frameworks.md#manipulating-your-claims) para modificar as declarações de entrada ou gerar novas, antes de enviá-las para Application insights. No exemplo a seguir, o perfil técnico inclui a `CheckIsAdmin` transformação declarações de entrada.

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

Para adicionar um evento, crie um novo perfil técnico que inclua o `AppInsights-Common` perfil técnico. Em seguida, adicione o novo perfil técnico como uma etapa de orquestração ao percurso do [usuário](custom-policy-trust-frameworks.md#orchestration-steps). Use o elemento de [pré-condição](userjourneys.md#preconditions) para disparar o evento quando você estiver pronto. Por exemplo, relate o evento somente quando os usuários executam a autenticação multifator.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Ao adicionar um evento à jornada do usuário, lembre-se de renumerar as etapas de orquestração em sequência.

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

Ao usar Application Insights para definir eventos, você pode indicar se o modo de desenvolvedor está habilitado. O modo de desenvolvedor controla como os eventos são armazenados em buffer. Em um ambiente de desenvolvimento com volume mínimo de eventos, habilitar o modo de desenvolvedor resulta em eventos enviados imediatamente para Application Insights. O valor padrão é `false`. Não habilite o modo de desenvolvedor em ambientes de produção.

Para habilitar o modo de desenvolvedor, altere os `DeveloperMode` metadados para `true` no `AppInsights-Common` perfil técnico:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Desabilitar telemetria

Para desabilitar logs de Application Insights, altere os `DisableTelemetry` metadados para `true` no `AppInsights-Common` perfil técnico:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Próximas etapas

Saiba como [criar painéis de KPI personalizados usando o aplicativo Azure insights](../azure-monitor/app/tutorial-app-dashboards.md).

::: zone-end