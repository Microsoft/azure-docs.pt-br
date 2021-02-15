---
title: 'IDs de contexto de usuário para monitorar atividade: Azure Application Insights'
description: Acompanhe como os usuários navegam pelo serviço, atribuindo a cada um deles uma cadeia de identificação exclusiva e persistente no Application Insights.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 01/03/2019
ms.reviewer: abgreg;mbullwin
ms.openlocfilehash: 46b7479df6d087915cfe81895a786a528da6b9bb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87327898"
---
# <a name="send-user-context-ids-to-enable-usage-experiences-in-azure-application-insights"></a>Enviar IDs de contexto de usuário para permitir experiências de uso no Azure Application Insights

## <a name="tracking-users"></a>Acompanhamento de usuários

O Application Insights permite monitorar e acompanhar seus usuários por meio de um conjunto de ferramentas de uso do produto:

- [Usuários, Sessões, Eventos](./usage-segmentation.md)
- [Funis](./usage-funnels.md)
- [Retenção](./usage-retention.md) Coortes
- [Pastas de trabalho](../platform/workbooks-overview.md)

Para acompanhar o que um usuário faz ao longo do tempo, o Application Insights precisa de uma ID para cada usuário ou sessão. Inclua estas IDs em cada exibição de página ou evento personalizado.

- Usuários, Funis, Retenção e Coortes: Inclua a ID de usuário.
- Sessões: Inclua a ID da sessão.

> [!NOTE]
> Este é um artigo avançado que descreve as etapas manuais para acompanhar a atividade do usuário com o Application Insights. Com muitos aplicativos Web, **talvez estas etapas não sejam necessárias**, pois, geralmente, os SDKs padrão do lado do servidor em conjunto com o [SDK do JavaScript do lado do cliente/navegador](./website-monitoring.md), são suficientes para controlar automaticamente a atividade de usuário. Se você não tiver configurado o [monitoramento do lado do cliente](./website-monitoring.md) além do SDK do lado do servidor, faça isso primeiro e teste se as ferramentas de análise de comportamento do usuário estão apresentando o desempenho esperado.

## <a name="choosing-user-ids"></a>Escolher as IDs de usuário

As IDs de usuário devem persistir entre as sessões de usuário para acompanhar o comportamento dos usuários ao longo do tempo. Há várias abordagens para persistir a ID.

- Uma definição de um usuário que você já tem em seu serviço.
- Se o serviço tem acesso a um navegador, ele pode passar um cookie contendo uma ID ao navegador. A ID persistirá enquanto o cookie permanecer no navegador do usuário.
- Se necessário, você pode usar uma nova ID a cada sessão, mas os resultados sobre os usuários serão limitados. Por exemplo, você não conseguirá ver as alterações no comportamento do usuário ao longo do tempo.

A ID deve ser um Guid ou outra cadeia de caracteres suficientemente complexa para identificar cada usuário exclusivamente. Por exemplo, poderia ser um número aleatório comprido.

Se a ID contém informações de identificação pessoal sobre o usuário, ela não é um valor apropriado para enviar ao Application Insights como uma ID de usuário. Você pode enviar tal ID como uma [ID de usuário autenticado](./api-custom-events-metrics.md#authenticated-users), mas ela não atende ao requisito de ID de usuário para cenários de uso.

## <a name="aspnet-apps-setting-the-user-context-in-an-itelemetryinitializer"></a>Aplicativos ASP.NET: Definir o contexto de usuário em um ITelemetryInitializer

Crie um inicializador de telemetria, conforme descrito em detalhes [aqui](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer). Passe a ID de sessão por meio da telemetria de solicitação e defina o Context.User.Id e o Context.Session.Id.

Este exemplo define a ID de usuário para um identificador que expira após a sessão. Se possível, use uma ID de usuário que persiste entre as sessões.

### <a name="telemetry-initializer"></a>Inicializador de telemetria

```csharp
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
        var ctx = HttpContext.Current;

        // If telemetry initializer is called as part of request execution and not from some async thread
        if (ctx != null)
        {
            var requestTelemetry = ctx.GetRequestTelemetry();
 
            // Set the user and session ids from requestTelemetry.Context.User.Id, which is populated in Application_PostAcquireRequestState in Global.asax.cs.
            if (requestTelemetry != null && !string.IsNullOrEmpty(requestTelemetry.Context.User.Id) &&
                (string.IsNullOrEmpty(telemetry.Context.User.Id) || string.IsNullOrEmpty(telemetry.Context.Session.Id)))
            {
                // Set the user id on the Application Insights telemetry item.
                telemetry.Context.User.Id = requestTelemetry.Context.User.Id;
 
                // Set the session id on the Application Insights telemetry item.
                telemetry.Context.Session.Id = requestTelemetry.Context.User.Id;
            }
        }
    }
  }
}
```

### <a name="globalasaxcs"></a>Global.asax.cs

```csharp
using System.Web;
using System.Web.Http;
using System.Web.Mvc;
using System.Web.Optimization;
using System.Web.Routing;

namespace MvcWebRole.Telemetry
{
    public class MvcApplication : HttpApplication
    {
        protected void Application_Start()
        {
            AreaRegistration.RegisterAllAreas();
            GlobalConfiguration.Configure(WebApiConfig.Register);
            FilterConfig.RegisterGlobalFilters(GlobalFilters.Filters);
            RouteConfig.RegisterRoutes(RouteTable.Routes);
            BundleConfig.RegisterBundles(BundleTable.Bundles);
        }
 
        protected void Application_PostAcquireRequestState()
        {
            var requestTelemetry = Context.GetRequestTelemetry();
 
            if (HttpContext.Current.Session != null && requestTelemetry != null && string.IsNullOrEmpty(requestTelemetry.Context.User.Id))
            {
                requestTelemetry.Context.User.Id = Session.SessionID;
            }
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

- Para habilitar as experiências de uso, comece enviando [eventos personalizados](./api-custom-events-metrics.md#trackevent) ou [exibições de página](./api-custom-events-metrics.md#page-views).
- Se você já envia eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários utilizam o seu serviço.
    - [Visão geral do uso](usage-overview.md)
    - [Usuários, Sessões e Eventos](usage-segmentation.md)
    - [Funis](usage-funnels.md)
    - [Retenção](usage-retention.md)
    - [Pastas de trabalho](../platform/workbooks-overview.md)

