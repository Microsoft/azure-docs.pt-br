---
title: "Monitorando uso e desempenho de aplicativos de área de trabalho do Windows"
description: "Analise o uso e o desempenho do seu aplicativo da área de trabalho do Windows usando o HockeyApp e o Application Insights."
services: application-insights
documentationcenter: windows
author: CFreemanwa
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2016
ms.author: bwren
ms.openlocfilehash: 9d7e2a390adf10cbf5d88dd0084ce09136987309
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Monitorando uso e desempenho de aplicativos de área de trabalho do Windows


[Azure Application Insights](app-insights-overview.md) e [HockeyApp](https://hockeyapp.net) permitem que você monitore seu aplicativo implantado quanto ao uso e desempenho.

> [!IMPORTANT]
> Recomendamos o [HockeyApp](https://hockeyapp.net) para distribuir e monitorar aplicativos de área de trabalho e dispositivo. Com o HockeyApp, você pode gerenciar a distribuição, testes ao vivo e comentários do usuário, bem como monitorar relatórios de uso e falhas. Você também pode [exportar e consultar a telemetria com o Analytics](app-insights-hockeyapp-bridge-app.md).
> 
> Embora a telemetria possa ser enviada para o Application Insights de um aplicativo de área de trabalho, isso é principalmente útil para fins experimentais e de depuração.
> 
> 

## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Para enviar telemetria ao Application Insights de um aplicativo do Windows
1. No [portal do Azure](https://portal.azure.com), [crie um recurso Application Insights](app-insights-create-new-resource.md). Para o tipo de aplicativo, escolha o aplicativo ASP.NET.
2. Faça uma cópia da chave de instrumentação. Localize a chave no menu suspenso Essentials do novo recurso que você acabou de criar. 
3. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicativo e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se você quiser apenas a API básica, sem os módulos de coleta da telemetria padrão.)
4. Defina a chave de instrumentação no seu código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *sua chave* `";` 
   
    ou em ApplicationInsights.config (se tiver instalado um dos pacotes de telemetria padrão):
   
    `<InstrumentationKey>`*sua chave*`</InstrumentationKey>` 
   
    Se você usar ApplicationInsights.config, verifique se suas propriedades no Gerenciador de Soluções estão definidas como **Ação de Compilação = Conteúdo, Copiar para Diretório de Saída = Copiar**.
5. [Use a API](app-insights-api-custom-events-metrics.md) para enviar telemetria.
6. Execute o aplicativo e veja a telemetria no recurso criado no Portal do Azure.

## <a name="telemetry"></a>Código de exemplo
```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Próximas etapas
* [Criar um painel](app-insights-dashboards.md)
* [Pesquisa de Diagnóstico](app-insights-diagnostic-search.md)
* [Explorar métricas](app-insights-metrics-explorer.md)
* [Escrever consultas do Analytics](app-insights-analytics.md)

