---
title: "Enviar IDs de contexto de usuário para permitir experiências de uso no Azure Application Insights | Microsoft Docs"
description: "Acompanhe como os usuários navegam pelo serviço, atribuindo a cada um deles uma cadeia de identificação exclusiva e persistente no Application Insights."
services: application-insights
documentationcenter: 
author: abgreg
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: csharp
ms.topic: article
ms.date: 08/02/2017
ms.author: bwren
ms.openlocfilehash: e16866501fd34f0b998ba929771a423866759b55
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Enviar IDs de contexto de usuário para permitir experiências de uso no Azure Application Insights

## <a name="tracking-users"></a>Acompanhamento de usuários

O Application Insights permite monitorar e acompanhar seus usuários por meio de um conjunto de ferramentas de uso do produto: 
* [Usuários, Sessões, Eventos](https://docs.microsoft.com/azure/application-insights/app-insights-usage-segmentation)
* [Funis](https://docs.microsoft.com/azure/application-insights/usage-funnels)
* [Retenção](https://docs.microsoft.com/azure/application-insights/app-insights-usage-retention)
* Coortes
* [Pastas de trabalho](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)

Para acompanhar o que um usuário faz ao longo do tempo, o Application Insights precisa de uma ID para cada usuário ou sessão. Inclua estas IDs em cada exibição de página ou evento personalizado.
- Usuários, Funis, Retenção e Coortes: inclua a ID de usuário.
- Sessões: inclua a ID da sessão.

Se seu aplicativo estiver integrado com o [SDK do JavaScript](https://docs.microsoft.com/azure/application-insights/app-insights-javascript#set-up-application-insights-for-your-web-page), a ID de usuário será acompanhada automaticamente.

## <a name="choosing-user-ids"></a>Escolher as IDs de usuário

As IDs de usuário devem persistir entre as sessões de usuário para acompanhar o comportamento dos usuários ao longo do tempo. Há várias abordagens para persistir a ID.
- Uma definição de um usuário que você já tem em seu serviço.
- Se o serviço tem acesso a um navegador, ele pode passar um cookie contendo uma ID ao navegador. A ID persistirá enquanto o cookie permanecer no navegador do usuário.
- Se necessário, você pode usar uma nova ID a cada sessão, mas os resultados sobre os usuários serão limitados. Por exemplo, você não conseguirá ver as alterações no comportamento do usuário ao longo do tempo.

A ID deve ser um Guid ou outra cadeia de caracteres suficientemente complexa para identificar cada usuário exclusivamente. Por exemplo, poderia ser um número aleatório comprido.

Se a ID contém informações de identificação pessoal sobre o usuário, ela não é um valor apropriado para enviar ao Application Insights como uma ID de usuário. Você pode enviar tal ID como uma [ID de usuário autenticado](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#authenticated-users), mas ela não atende ao requisito de ID de usuário para cenários de uso.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplicativos ASP .NET: definir o contexto de usuário em um ITelemetryInitializer

Crie um inicializador de telemetria, conforme descrito em detalhes [aqui](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) e defina o Context.User.Id e o Context.Session.Id.

Este exemplo define a ID de usuário para um identificador que expira após a sessão. Se possível, use uma ID de usuário que persiste entre as sessões.

```C#

    using System;
    using System.Web;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that sets the user ID.
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            // For a full experience, track each user across sessions. For an incomplete view of user 
            // behavior within a session, store user ID on the HttpContext Session.
            // Set the user ID if we haven't done so yet.
            if (HttpContext.Current.Session["UserId"] == null)
            {
                HttpContext.Current.Session["UserId"] = Guid.NewGuid();
            }

            // Set the user id on the Application Insights telemetry item.
            telemetry.Context.User.Id = (string)HttpContext.Current.Session["UserId"];

            // Set the session id on the Application Insights telemetry item.
            telemetry.Context.Session.Id = HttpContext.Current.Session.SessionID;
        }
      }
    }
```

## <a name="next-steps"></a>Próximas etapas
- Para habilitar as experiências de uso, comece enviando [eventos personalizados](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já envia eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários utilizam o seu serviço.
    * [Visão geral do uso](app-insights-usage-overview.md)
    * [Usuários, Sessões e Eventos](app-insights-usage-segmentation.md)
    * [Funis](usage-funnels.md)
    * [Retenção](app-insights-usage-retention.md)
    * [Pastas de trabalho](app-insights-usage-workbooks.md)
