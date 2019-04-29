---
title: Filtrando e pré-processando no SDK do Azure Application Insights | Microsoft Docs
description: Escreva Processadores de Telemetria e Inicializadores de Telemetria para o SDK filtrar ou adicionar propriedades aos dados antes da telemetria ser enviada ao portal do Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 1b55a2b053b86d3260fdca201357445d2556c444
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60793967"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtrando e pré-processando a telemetria no SDK do Application Insights


Você pode escrever e configurar plug-ins para o SDK do Application Insights, a fim de personalizar o modo como a telemetria é capturada e processada antes de ser enviada ao serviço Application Insights.

* [Amostragem](../../azure-monitor/app/sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Ela mantém juntos os pontos de dados relacionadas para que você possa navegar entre eles para diagnosticar um problema. No portal, as contagens totais são multiplicadas para compensar a amostragem.
* A Filtragem com Processadores de Telemetria [para ASP.NET](#filtering) ou [Java](../../azure-monitor/app/java-filter-telemetry.md) permite selecionar ou modificar a telemetria no SDK antes que ela seja enviada ao servidor. Por exemplo, você pode reduzir o volume de telemetria excluindo as solicitações de robôs. Mas a filtragem é uma abordagem mais básica para reduzir o tráfego comparado à amostragem. Ela permite um maior controle sobre o que é transmitido, mas você precisa estar ciente de que ela afetará as estatísticas - por exemplo, se você filtrar todas as solicitações bem-sucedidas.
* [Inicializadores de Telemetria adicionam propriedades](#add-properties) a qualquer telemetria enviada do seu aplicativo, incluindo a telemetria dos módulos padrão. Por exemplo, você pode adicionar valores calculados ou números de versão pelos qual os dados serão filtrados no portal.
* [A API do SDK](../../azure-monitor/app/api-custom-events-metrics.md) é usada para enviar eventos personalizados e métricas.

Antes de começar:

* Instale o [SDK para ASP.NET](../../azure-monitor/app/asp-net.md) ou [SDK para Java](../../azure-monitor/app/java-get-started.md) do Application Insights em seu aplicativo.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Filtragem: ITelemetryProcessor
Essa técnica fornece um controle mais direto sobre o que é incluído ou excluído da transmissão de telemetria. Você pode usá-la em conjunto com a Amostragem, ou separadamente.

Para filtrar a telemetria, escreva um processador de telemetria e registre-o no SDK. Toda a telemetria passa pelo seu processador, e você pode optar por removê-la da transmissão ou por adicionar propriedades. Isso inclui a telemetria dos módulos padrão como o coletor de solicitação HTTP e o coletor de dependência, além da telemetria escrita por você. Por exemplo, você pode filtrar a telemetria sobre solicitações de robôs ou sobre chamadas de dependência bem-sucedidas.

> [!WARNING]
> Filtrar a telemetria enviada do SDK usando processadores pode distorcer as estatísticas que você vê no portal e dificultar o acompanhamento de itens relacionados.
>
> Em vez disso, considere usar a [amostragem](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Criar um processador de telemetria (C#)
1. Verifique se a versão do SDK do Application Insights em seu projeto é a 2.0.0 ou posterior. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções do Visual Studio e escolha Gerenciar Pacotes NuGet. No gerenciador de pacotes NuGet, marque Microsoft.ApplicationInsights.Web.
2. Para criar um filtro, implemente ITelemetryProcessor. Este é outro ponto de extensibilidade como módulo de telemetria, inicializador de telemetria e canal de telemetria.

    Observe que os Processadores de Telemetria criam uma cadeia de processamento. Ao criar uma instância de um processador de telemetria, você transmite um link para o próximo processador na cadeia. Quando um ponto de dados de telemetria é transmitido para o método Process, ele faz seu trabalho e, em seguida, chama o próximo Processador de Telemetria da cadeia.

    ```csharp

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
3. Insira isto no ApplicationInsights.config:

```xml

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Essa é a mesma seção em que você inicializa um filtro de amostragem).

Você pode transmitir valores de cadeia de caracteres do arquivo .config fornecendo propriedades nomeadas públicas em sua classe.

> [!WARNING]
> Fique atento para que o nome do tipo e todos os nomes de propriedade no arquivo .config correspondam aos nomes de classe e de propriedade no código. Se o arquivo .config fizer referência a um tipo ou propriedade inexistente, o SDK poderá silenciosamente falhar ao enviar qualquer telemetria.
>
>

**Como alternativa,** é possível inicializar o filtro no código. Em uma classe de inicialização adequada - por exemplo AppStart em Global.asax.cs - insira seu processador na cadeia:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Os TelemetryClients criados depois desse ponto usarão seus processadores.

### <a name="example-filters"></a>Filtros de exemplo
#### <a name="synthetic-requests"></a>Solicitações sintéticas
Filtre os bots e os testes Web. Embora o Metrics Explorer ofereça a opção para filtrar fontes sintéticas, essa opção reduz o tráfego filtrando-as no SDK.

```csharp

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Autenticação com falha
Filtre as solicitações com uma resposta "401".

```csharp

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar chamadas de dependência rápidas remotas
Se desejar diagnosticar chamadas lentas, filtre as rápidas.

> [!NOTE]
> Isso distorcerá as estatísticas que você vê no portal. O gráfico de dependência parecerá como se as chamadas de dependência fossem todas falhas.
>
>

```csharp

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência
[Este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência ao enviar automaticamente pings regulares para as dependências.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Adicionar propriedades: ITelemetryInitializer
Use inicializadores de telemetria para definir propriedades globais que são enviadas com todas as telemetrias e para substituir o comportamento selecionado dos módulos de telemetria padrão.

Por exemplo, o Application Insights para o pacote da Web coleta a telemetria sobre solicitações HTTP. Por padrão, ele sinaliza como qualquer solicitação com um código de resposta de falha > = 400. Mas se desejar tratar 400 como êxito, você pode fornecer um inicializador de telemetria que define a propriedade Sucess.

Se você fornecer um inicializador de telemetria, ele é chamado sempre que qualquer um dos métodos Track*() for chamado. Isso inclui métodos chamados pelos módulos de telemetria padrão. Por convenção, esses módulos não definem qualquer propriedade que já foi definida por um inicializador.

**Definir seu inicializador**

*C#*

```csharp

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Carregue seu inicializador**

Em ApplicationInsights.config:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

*Como alternativa,* você pode instanciar o inicializador no código, por exemplo em Global.aspx.cs:

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Ver mais deste exemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="java-telemetry-initializers"></a>Inicializadores de telemetria do Java

[Documentação do SDK do Java](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Em seguida, registre o inicializador personalizado em seu arquivo applicationinsights. XML.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
<Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria JavaScript
*JavaScript*

Insira um inicializador de telemetria logo após o código de inicialização que você obteve do portal:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item�s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para obter um resumo das propriedades não personalizadas disponíveis em telemetryItem, veja o [Modelo de dados de exportação do Application Insights](../../azure-monitor/app/export-data-model.md).

Você pode adicionar quantos inicializadores desejar.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer
Qual é a diferença entre os processadores de telemetria e inicializadores de telemetria?

* Há algumas sobreposições no que você pode fazer com eles: ambos podem ser usados para adicionar propriedades à telemetria.
* Sempre execute TelemetryInitializers antes de TelemetryProcessors.
* TelemetryProcessors permitem que você substitua ou descarte completamente um item de telemetria.
* TelemetryProcessors não processam telemetria do contador de desempenho.

## <a name="troubleshooting-applicationinsightsconfig"></a>Resolução de problemas ApplicationInsights.config
* Confirme se o nome do tipo totalmente qualificado e o nome do assembly estão corretos.
* Confirme se o arquivo applicationinsights. config está no seu diretório de saída e contém todas as alterações recentes.

## <a name="reference-docs"></a>Documentos de Referência
* [Visão geral da API](../../azure-monitor/app/api-custom-events-metrics.md)
* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>Código do SDK
* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [SDK do ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Próximas etapas
* [Pesquisar eventos e logs](../../azure-monitor/app/diagnostic-search.md)
* [Amostragem](../../azure-monitor/app/sampling.md)
* [Solução de problemas](../../azure-monitor/app/troubleshoot-faq.md)
