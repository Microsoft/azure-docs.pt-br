---
title: "Application Insights para solucionar problemas de Políticas Personalizadas – Azure AD B2C | Microsoft Docs"
description: "como configurar o Application Insights para rastrear a execução de políticas personalizadas"
services: active-directory-b2c
documentationcenter: 
author: saeedakhter-msft
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: saeda
ms.openlocfilehash: 8c79df33cd5f04f490e2cc6372f7e8ac1c4d9bbe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Coleta de logs

Este artigo fornece etapas para coletar logs do Azure AD B2C para que você possa diagnosticar problemas com suas políticas personalizadas.

>[!NOTE]
>Atualmente, os logs de atividade detalhados descritos aqui são projetados **APENAS** para ajudar no desenvolvimento de políticas personalizadas. Não use o modo de desenvolvimento em produção.  Os logs coletam todas as declarações enviadas entre os provedores de identidade durante o desenvolvimento.  Se for usado em produção, o desenvolvedor assumirá a responsabilidade pela PII (Informações de identificação particular) coletadas no log do App Insights que ele possui.  Esses logs detalhados são coletados apenas quando a política é colocada em **MODO DE DESENVOLVIMENTO**.


## <a name="use-application-insights"></a>Usar o Application insights

O Azure AD B2C oferece suporte a um recurso para envio de dados ao Application Insights.  O Application Insights fornece uma maneira de diagnosticar exceções e visualizar os problemas de desempenho do aplicativo.

### <a name="setup-application-insights"></a>Configurar o Application Insights

1. Vá para o [Portal do Azure](https://portal.azure.com). Certifique-se de que você esteja no locatário com sua assinatura do Azure (não no locatário do Azure AD B2C).
1. Clique em **+ Novo** no menu de navegação esquerdo.
1. Pesquise e selecione **Application Insights** e clique em **Criar**.
1. Preencha o formulário e clique em **Criar**. Selecione **Geral** para o **Tipo de Aplicativo**.
1. Após a criação do recurso, abra o recurso Application Insights.
1. Localize **Propriedades** no menu à esquerda e clique nele.
1. Copie a **Chave de Instrumentação** e salve-a na próxima seção.

### <a name="set-up-the-custom-policy"></a>Configurar a política personalizada

1. Abra o arquivo RP (por exemplo, SignUpOrSignin.xml).
1. Adicione os atributos a seguir ao elemento `<TrustFrameworkPolicy>`:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Se ele ainda não existir, adicione um nó filho `<UserJourneyBehaviors>` ao nó `<RelyingParty>`. Ele deve estar localizado imediatamente após o `<DefaultUserJourney ReferenceId="YourPolicyName" />`
2. Adicione o seguinte nó como um filho do elemento `<UserJourneyBehaviors>`. Substitua `{Your Application Insights Key}` pela **Chave de Instrumentação** que você obteve do Application Insights na seção anterior.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` informa ao ApplicationInsights para agilizar a telemetria por meio do pipeline de processamento, bom para o desenvolvimento, mas com restrição em grandes volumes.
  * `ClientEnabled="true"` envia o script do lado do cliente do ApplicationInsights para rastrear erros de exibição de página e do lado do cliente (não é necessário).
  * `ServerEnabled="true"` envia o JSON UserJourneyRecorder existente como um evento personalizado para o Application Insights.
Exemplo:

  ```XML
  <TrustFrameworkPolicy
    ...
    TenantId="fabrikamb2c.onmicrosoft.com"
    PolicyId="SignUpOrSignInWithAAD"
    DeploymentMode="Development"
    UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="YourPolicyName" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
  </TrustFrameworkPolicy>
  ```

3. Carregue a política.

### <a name="see-the-logs-in-application-insights"></a>Veja os logs de no Application Insights

>[!NOTE]
> Há um breve atraso (menos de cinco minutos) antes que você possa ver novos logs no Application Insights.

1. Abra o recurso do Application Insights que você criou no [Portal do Azure](https://portal.azure.com).
1. No menu **Visão geral**, clique em **Análise**.
1. Abra uma nova guia no Application Insights.
1. Aqui está uma lista de consultas que você pode usar para ver os logs

| Consultar | Descrição |
|---------------------|--------------------|
traces | Veja todos os logs gerados pelo Azure AD B2C |
traces \| em que timestamp > ago(1d) | Veja todos os logs gerados pelo Azure AD B2C para o último dia

As entradas podem ser longas.  Exporte para CSV para uma análise mais detalhada.

Saiba mais sobre essa ferramentas de análise [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>A comunidade desenvolveu um visualizador userjourney para ajudar os desenvolvedores de identidade.  Não tem suporte da Microsoft e é disponibilizado estritamente como está.  Ele lê na sua instância do Application Insights e fornece uma exibição bem estruturada dos eventos userjourney.  Obtenha o código-fonte e o implante em sua própria solução.

>[!NOTE]
>Atualmente, os logs de atividade detalhados descritos aqui são projetados **APENAS** para ajudar no desenvolvimento de políticas personalizadas. Não use o modo de desenvolvimento em produção.  Os logs coletam todas as declarações enviadas entre os provedores de identidade durante o desenvolvimento.  Se for usado em produção, o desenvolvedor assumirá a responsabilidade pela PII (Informações de identificação particular) coletadas no log do App Insights que ele possui.  Esses logs detalhados são coletados apenas quando a política é colocada em **MODO DE DESENVOLVIMENTO**.

[Repositório Github para obter exemplos de política personalizada sem suporte e ferramentas relacionadas](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Próximas etapas

Explore os dados no Application Insights para ajudar a entender como o Identity Experience Framework por trás do B2C trabalha para fornecer suas próprias experiências de identidade.
