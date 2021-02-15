---
title: Monitorando uso e desempenho de aplicativos de área de trabalho do Windows
description: Analise o uso e o desempenho de seu aplicativo da área de trabalho do Windows com o Application Insights.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 1aa1e8a9e7ccbbc90a961ebf47224f59f8a9e9fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91827862"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorando uso e desempenho de aplicativos de área de trabalho clássica do Windows

Aplicativos hospedados localmente, no Azure e em outras nuvens podem tirar proveito do Application Insights. A única limitação é a necessidade de [permitir a comunicação](./ip-addresses.md) para o serviço do Application Insights. Para monitorar os aplicativos da Plataforma Universal do Windows (UWP), recomendamos o [Visual Studio App Center](../learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Para enviar telemetria ao Application Insights a partir de um aplicativo do Windows clássico
1. No [portal do Azure](https://portal.azure.com), [crie um recurso Application Insights](./create-new-resource.md). 
2. Faça uma cópia da chave de instrumentação.
3. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicativo e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft. ApplicationInsights se você quiser apenas a API base, sem os módulos de coleta de telemetria padrão.)
4. Defina a chave de instrumentação no seu código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "`*sua chave*`";`
   
    ou em ApplicationInsights.config (se tiver instalado um dos pacotes de telemetria padrão):
   
    `<InstrumentationKey>`*sua chave*`</InstrumentationKey>` 
   
    Se você usar ApplicationInsights.config, verifique se suas propriedades no Gerenciador de Soluções estão definidas como **Ação de Compilação = Conteúdo, Copiar para Diretório de Saída = Copiar**.
5. [Use a API](./api-custom-events-metrics.md) para enviar telemetria.
6. Execute seu aplicativo e veja a telemetria no recurso que você criou na portal do Azure.

## <a name="example-code"></a><a name="telemetry"></a>Código de exemplo

```csharp
using Microsoft.ApplicationInsights;

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnFormClosing(System.Windows.Forms.FormClosingEventArgs e)
        {
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnFormClosing(e);
        }
        
        ...
```

## <a name="override-storage-of-computer-name"></a>Substituir o armazenamento do nome do computador

Por padrão, esse SDK coletará e armazenará o nome do computador do sistema que emite telemetria.

O nome do computador é usado pelo Application Insights [tipo de preço corporativo herdado (por nó)](./pricing.md#legacy-enterprise-per-node-pricing-tier) para fins de cobrança interno. Por padrão, se você usar um inicializador de telemetria para substituir `telemetry.Context.Cloud.RoleInstance` , será enviada uma propriedade separada `ai.internal.nodeName` que ainda conterá o valor do nome do computador. Esse valor não será armazenado com a telemetria do Application Insights, mas é usado internamente na ingestão para permitir a compatibilidade com versões anteriores com o modelo de cobrança baseado em nó herdado.

Se você estiver no [tipo de preço corporativo herdado (por nó)](./pricing.md#legacy-enterprise-per-node-pricing-tier) e simplesmente precisar substituir o armazenamento do nome do computador, use um inicializador de telemetria:

**Escreva personalizada telemetryinitializer personalizado como abaixo.**

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace CustomInitializer.Telemetry
{
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if (string.IsNullOrEmpty(telemetry.Context.Cloud.RoleInstance))
            {
                // Set custom role name here. Providing an empty string will result
                // in the computer name still be sent via this property.
                  telemetry.Context.Cloud.RoleInstance = "Custom RoleInstance";
            }
        }
    }
}
```

Crie uma instância do inicializador no `Program.cs` `Main()` método abaixo definindo a chave de instrumentação:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;

   static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            TelemetryConfiguration.Active.TelemetryInitializers.Add(new MyTelemetryInitializer());
            //...
        }
```

## <a name="override-transmission-of-computer-name"></a>Substituir a transmissão do nome do computador

Se você não estiver no [tipo de preço corporativo herdado (por nó)](./pricing.md#legacy-enterprise-per-node-pricing-tier) e quiser impedir completamente que qualquer telemetria que contenha o nome do computador seja enviada, você precisará usar um processador de telemetria.

### <a name="telemetry-processor"></a>Processador de telemetria

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;


namespace WindowsFormsApp2
{
    public class CustomTelemetryProcessor : ITelemetryProcessor
    {
        private readonly ITelemetryProcessor _next;

        public CustomTelemetryProcessor(ITelemetryProcessor next)
        {
            _next = next;
        }

        public void Process(ITelemetry item)
        {
            if (item != null)
            {
                item.Context.Cloud.RoleInstance = string.Empty;
            }

            _next.Process(item);
        }
    }
}
```

Crie uma instância do processador de telemetria no `Program.cs` `Main()` método abaixo definindo a chave de instrumentação:

```csharp
using Microsoft.ApplicationInsights.Extensibility;

namespace WindowsFormsApp2
{
    static class Program
    {
        static void Main()
        {
            TelemetryConfiguration.Active.InstrumentationKey = "{Instrumentation-key-here}";
            var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
            builder.Use((next) => new CustomTelemetryProcessor(next));
            builder.Build();
            //...
        }
    }
}

```

> [!NOTE]
> Embora você possa usar tecnicamente um processador de telemetria, conforme descrito acima, mesmo se você estiver no [tipo de preço corporativo herdado (por nó)](./pricing.md#legacy-enterprise-per-node-pricing-tier), isso resultará em potencial para cobrança excessiva devido à incapacidade de distinguir corretamente os nós para os preços por nó.

## <a name="next-steps"></a>Próximas etapas
* [Criar um dashboard](./overview-dashboard.md)
* [Pesquisa de Diagnóstico](./diagnostic-search.md)
* [Explorar métricas](../platform/metrics-charts.md)
* [Escrever consultas do Analytics](../log-query/log-query-overview.md)

