---
title: Referência de ApplicationInsights.config - Azure | Microsoft Docs
description: Habilitar ou desabilitar módulos de coleta de dados e adicionar contadores de desempenho e outros parâmetros.
ms.topic: conceptual
ms.date: 05/22/2019
ms.custom: devx-track-csharp
ms.reviewer: olegan
ms.openlocfilehash: d05503c2a22c476d9ab08e8aeb058ca1b9826778
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928683"
---
# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar o SDK do Application Insights com ApplicationInsights.config ou.xml
O SDK .NET do Application Insights consiste em vários pacotes NuGet. O [pacote principal](https://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para enviar telemetria ao Application Insights. Os [pacotes adicionais](https://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecem *módulos* e *inicializadores* de telemetria para rastreamento automático de telemetria do seu aplicativo e respectivo contexto. Ao ajustar o arquivo de configuração, você pode habilitar ou desabilitar módulos e inicializadores de telemetria e definir parâmetros para alguns deles.

O arquivo de configuração é chamado `ApplicationInsights.config` ou `ApplicationInsights.xml`, dependendo do tipo do seu aplicativo. Ele é adicionado automaticamente ao seu projeto quando você [instala a maioria das versões do SDK][start]. Por padrão, ao usar a experiência automatizada dos projetos de modelo do Visual Studio que dão suporte à **adição de > Application insights Telemetry**, o arquivo de ApplicationInsights.config é criado na pasta raiz do projeto e, quando compatível, é copiado para a pasta bin. Ele também é adicionado a um aplicativo Web por [status monitor em um servidor IIS][redfield]. O arquivo de configuração será ignorado se a [extensão para o site](azure-web-apps.md) ou [a extensão do Azure para a VM do Azure e o conjunto de dimensionamento de máquinas virtuais](azure-vm-vmss-apps.md) for usado.

Não há um arquivo equivalente para controlar o [SDK em uma página da Web][client].

Este documento descreve as seções que você vê no arquivo de configuração, como controlar os componentes do SDK, e quais pacotes NuGet carregar esses componentes.

> [!NOTE]
> As instruções de ApplicationInsights.config e .xml não são válidas para o SDK do .NET Core. Para configurar aplicativos .NET Core, siga [este](./asp-net-core.md) guia.

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetria (ASP.NET)
Cada módulo de telemetria coleta um tipo específico de dados e usa a API principal para enviar os dados. Os módulos são instalados por diferentes pacotes NuGet, que também adicionam as linhas necessárias para o arquivo. config.

Há um nó no arquivo de configuração para cada módulo. Para desabilitar um módulo, exclua o nó ou remova o comentário dele.

### <a name="dependency-tracking"></a>Acompanhamento de dependência
[Dependency tracking](./asp-net-dependencies.md) coleta a telemetria sobre chamadas para bancos de dados e serviços externos e torna o seu aplicativo. Para permitir que esse módulo funcione em um servidor IIS, é necessário [instalar o Status Monitor][redfield].

Você também pode escrever seu próprio código de rastreamento de dependência usando a [API TrackDependency](./api-custom-events-metrics.md#trackdependency).

* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) .

As dependências podem ser coletadas automaticamente sem modificar seu código usando a anexação baseada em agente (sem código). Para usá-lo em aplicativos Web do Azure, habilite a [extensão Application insights](azure-web-apps.md). Para usá-lo na VM do Azure ou no conjunto de dimensionamento de máquinas virtuais do Azure, habilite a [extensão de monitoramento de aplicativo para VM e conjunto de dimensionamento de máquinas](azure-vm-vmss-apps.md)

### <a name="performance-collector"></a>Coletor de desempenho
[Coleta contadores de desempenho do sistema](./performance-counters.md) como CPU, memória e carregamento de rede de instalações do IIS. Você pode especificar quais contadores coletar, incluindo contadores de desempenho que você configurou por si mesmo.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) .

### <a name="application-insights-diagnostics-telemetry"></a>Telemetria de diagnóstico do Application Insights
Os erros de `DiagnosticsTelemetryModule` relatórios na instrumentação do Application Insights se codificam sozinhos. Por exemplo, se o código não puder acessar contadores de desempenho ou se um `ITelemetryInitializer` lançar uma exceção. A telemetria de rastreamento rastreada por esse módulo aparece na [Pesquisa de Diagnóstico][diagnostic].

```
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet package. If you only install this package, the ApplicationInsights.config file is not automatically created.
```

### <a name="developer-mode"></a>Modo de Desenvolvedor
`DeveloperModeWithDebuggerAttachedTelemetryModule` força `TelemetryChannel` do Application Insights a enviar dados imediatamente, um item de telemetria por vez, quando um depurador é conectado ao processo de aplicativo. Isso reduz a quantidade de tempo entre o momento em que seu aplicativo rastreia a telemetria e quando ele aparece no portal do Application Insights. Faz com uma sobrecarga significativa na largura de banda de CPU e rede.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Application Insights Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) .

### <a name="web-request-tracking"></a>Acompanhamento de solicitação da Web
Relatórios do [código de tempo e o resultado da resposta](../../azure-monitor/app/asp-net.md) de solicitações HTTP.

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) .

### <a name="exception-tracking"></a>Acompanhamento de exceções
`ExceptionTrackingTelemetryModule` rastreia exceção sem tratamento no seu aplicativo Web. Consulte [Falhas e exceções][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) .
* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule` -rastreia exceções de tarefas não observadas
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule` -rastreia as exceções sem tratamento para funções de trabalho, serviços do Windows e aplicativos de console.
* [Application insights o Windows Server](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet.

### <a name="eventsource-tracking"></a>Monitoramento de EventSource
O `EventSourceTelemetryModule` permite configurar eventos EventSource a serem enviados ao Application Insights como rastreamentos. Para obter informações sobre o monitoramento de eventos EventSource, consulte [Usando eventos EventSource](./asp-net-trace-logs.md#use-eventsource-events).

* `Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule`
* [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener) 

### <a name="etw-event-tracking"></a>Monitoramento de eventos ETW
O `EtwCollectorTelemetryModule` permite configurar eventos de provedores ETW a serem enviados ao Application Insights como rastreamentos. Para obter informações sobre o monitoramento de eventos ETW, consulte [Usando eventos ETW](../../azure-monitor/app/asp-net-trace-logs.md#use-etw-events).

* `Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule`
* [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector) 

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights
O pacote Microsoft.ApplicationInsights fornece a [API principal](/dotnet/api/microsoft.applicationinsights) do SDK. Os outros módulos de telemetria usam isso, e você também pode [usá-lo para definir sua própria telemetria](./api-custom-events-metrics.md).

* Nenhuma entrada em ApplicationInsights.config.
* [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) . Se você acabou de instalar este NuGet, nenhum arquivo. config será gerado.

## <a name="telemetry-channel"></a>Canal de telemetria
O [canal de telemetria](telemetry-channels.md) gerencia o buffer e a transmissão de telemetria para o serviço de Application insights.

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel` é o canal padrão para aplicativos Web. Ele armazena em buffer os dados na memória e emprega mecanismos de repetição e armazenamento em disco local para entrega de telemetria mais confiável.
* `Microsoft.ApplicationInsights.InMemoryChannel` é um canal de telemetria leve, que será usado se nenhum outro canal estiver configurado. 

## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetria (ASP.NET)
Inicializadores de telemetria definem propriedades de contexto que são enviadas junto com cada item de telemetria.

Você pode [escrever seus próprios inicializadores](./api-filtering-sampling.md#add-properties) para definir as propriedades de contexto.

Os inicializadores padrão foram todos configurados pelos pacotes do WindowsServer NuGet ou Web:

* `AccountIdTelemetryInitializer` define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer` define a propriedade AuthenticatedUserId como definido pelo SDK do JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer` atualiza as propriedades `RoleName` e `RoleInstance` do contexto `Device` para todos os itens de telemetria com informações extraídas do ambiente de runtime do Azure.
* `BuildInfoConfigComponentVersionTelemetryInitializer` atualiza a `Version` propriedade do `Component` contexto para todos os itens de telemetria com o valor extraído do arquivo `BuildInfo.config` produzido pelo MS Build.
* `ClientIpHeaderTelemetryInitializer` atualiza a propriedade `Ip` do contexto `Location` de todos os itens de telemetria baseados no cabeçalho HTTP `X-Forwarded-For` da solicitação.
* `DeviceTelemetryInitializer` atualiza as propriedades a seguir do contexto `Device` para todos os itens de telemetria.
  * `Type` é definido como "PC"
  * `Id` é definido para o nome de domínio do computador onde o aplicativo Web está em execução.
  * `OemName` é definido para o valor extraído do campo `Win32_ComputerSystem.Manufacturer` usando WMI.
  * `Model` é definido para o valor extraído do campo `Win32_ComputerSystem.Model` usando WMI.
  * `NetworkType` é definido para o valor extraído de `NetworkInterface`.
  * `Language` é definido para o nome da `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer` atualiza a propriedade `RoleInstance` do contexto `Device` para todos os itens de telemetria com o nome de domínio do computador onde o aplicativo Web está em execução.
* `OperationNameTelemetryInitializer` atualiza a propriedade `Name` das propriedades `RequestTelemetry` e `Name` do contexto `Operation` de todos os itens de telemetria baseados no método HTTP, bem como nomes do controlador MVC do ASP.NET e ação invocada para processar a solicitação.
* `OperationIdTelemetryInitializer` ou `OperationCorrelationTelemetryInitializer` atualiza a propriedade de contexto `Operation.Id` de todos os itens de telemetria rastreados ao manipular uma solicitação com o `RequestTelemetry.Id` gerado automaticamente.
* `SessionTelemetryInitializer` atualiza a propriedade `Id` do contexto `Session` para todos os itens de telemetria com valor extraído do cookie `ai_session` gerado pelo código de instrumentação do JavaScript do ApplicationInsights em execução no navegador do usuário.
* `SyntheticTelemetryInitializer` ou `SyntheticUserAgentTelemetryInitializer` atualiza as propriedades de contexto `User`, `Session` e `Operation`de todos os itens de telemetria rastreados ao lidar com uma solicitação de uma fonte sintética, como um teste de disponibilidade ou um bot do mecanismo de pesquisa. Por padrão, o [Metrics Explorer](../platform/metrics-charts.md) não exibe telemetria sintética.

    Os `<Filters>` definem as propriedades de identificação das solicitações.
* `UserTelemetryInitializer` atualiza as propriedades `Id` e `AcquisitionDate` do contexto `User` para todos os itens de telemetria com valores extraídos do cookie `ai_user` gerado pelo código de instrumentação do JavaScript do Application Insights em execução no navegador do usuário.
* `WebTestTelemetryInitializer` define a ID de usuário, a ID de sessão e as propriedades de origem sintética para solicitações HTTP provenientes de [testes de disponibilidade](./monitor-web-app-availability.md).
  Os `<Filters>` definem as propriedades de identificação das solicitações.

Para aplicativos .NET em execução no Service Fabric, você pode incluir o pacote do NuGet `Microsoft.ApplicationInsights.ServiceFabric`. Este pacote inclui um `FabricTelemetryInitializer`, que adiciona propriedades do Service Fabric a itens de telemetria. Para obter mais informações, consulte a [página do GitHub](https://github.com/Microsoft/ApplicationInsights-ServiceFabric/blob/master/README.md) sobre as propriedades adicionadas por este pacote do NuGet.

## <a name="telemetry-processors-aspnet"></a>Processadores de Telemetria (ASP.NET)
Os processadores de telemetria podem filtrar e modificar cada item de telemetria logo antes de ele ser enviado do SDK para o Portal.

Você pode [escrever seus próprios processadores de telemetria](./api-filtering-sampling.md#filtering).

#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria de amostragem adaptável (de 2.0.0-Beta3)
Isso é habilitado por padrão. Se o seu aplicativo envia muita telemetria, esse processador remove parte dela.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece o destino que o algoritmo tenta obter. Cada instância do SDK funciona independentemente, portanto, se o servidor for um cluster de vários computadores, o volume real de telemetria será multiplicado adequadamente.

[Saiba mais sobre amostragem](./sampling.md).

#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processador de telemetria de amostragem de taxa fixa (de 2.0.0-beta1)
Também há um processador de [telemetria de amostragem](./api-filtering-sampling.md) padrão (da 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```

## <a name="instrumentationkey"></a>InstrumentationKey
Isso determina o recurso do Application Insights em que seus dados aparecem. Normalmente um recurso separado, com uma chave separada, é criado para cada um dos seus aplicativos.

Se você deseja definir a chave dinamicamente, por exemplo, se quiser enviar os resultados do seu aplicativo para outros recursos, é possível omitir a chave do arquivo de configuração e defini-lo no código.

Para definir a chave para todas as instâncias de TelemetryClient, incluindo módulos de telemetria padrão. Faça isso em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights;

    protected void Application_Start()
    {
        TelemetryConfiguration configuration = TelemetryConfiguration.CreateDefault();
        configuration.InstrumentationKey = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
        var telemetryClient = new TelemetryClient(configuration);
   
```

Se quiser enviar um conjunto específico de eventos para um recurso diferente, você pode definir a chave para um TelemetryClient específico:

```csharp

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

Para obter uma nova chave, [crie um novo recurso no portal do Application Insights][new].



## <a name="applicationid-provider"></a>Provedor ApplicationId

_Disponível a partir de v2.6.0_

A finalidade desse provedor é procurar uma ID de aplicativo com base em uma chave de instrumentação. A ID do aplicativo é incluída em RequestTelemetry e em DependencyTelemetry e é usada para determinar a correlação no Portal.

Isso está disponível pela configuração de `TelemetryConfiguration.ApplicationIdProvider` no código ou na configuração.

### <a name="interface-iapplicationidprovider"></a>Interface: IApplicationIdProvider

```csharp
public interface IApplicationIdProvider
{
    bool TryGetApplicationId(string instrumentationKey, out string applicationId);
}
```


Fornecemos duas implementações no [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights) sdk: `ApplicationInsightsApplicationIdProvider` e `DictionaryApplicationIdProvider`.

### <a name="applicationinsightsapplicationidprovider"></a>ApplicationInsightsApplicationIdProvider

Esse é um wrapper em torno da API do Perfil. Ele limitará as solicitações e os resultados do cache.

Este provedor é adicionado ao arquivo de configuração quando você instala [Microsoft.ApplicationInsights.DependencyCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) ou [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)

Essa classe tem uma propriedade opcional `ProfileQueryEndpoint`.
Por padrão, isso é definido como `https://dc.services.visualstudio.com/api/profiles/{0}/appId`.
Se for necessário configurar um proxy para essa configuração, é recomendável o proxy de endereço básico e incluindo "/api/profiles/{0}/appId". Observe que '{0}' é substituído em runtime por solicitação com a Chave de Instrumentação.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exemplo de configuração via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
        <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exemplo de Configuração via código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new ApplicationInsightsApplicationIdProvider();
```

### <a name="dictionaryapplicationidprovider"></a>DictionaryApplicationIdProvider

Esse é um provedor estático que depende dos pares ID do aplicativo/chave de instrumentação configurados.

Essa classe tem uma propriedade `Defined` que é um Dictionary<string,string> de pares de chave de instrumentação para ID do aplicativo.

Essa classe tem uma propriedade opcional `Next` que pode ser utilizada para configurar outro provedor a ser usado quando uma Chave de Instrumentação solicitada não existir na configuração.

#### <a name="example-configuration-via-applicationinsightsconfig"></a>Exemplo de configuração via ApplicationInsights.config:
```xml
<ApplicationInsights>
    ...
    <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.DictionaryApplicationIdProvider, Microsoft.ApplicationInsights">
        <Defined>
            <Type key="InstrumentationKey_1" value="ApplicationId_1"/>
            <Type key="InstrumentationKey_2" value="ApplicationId_2"/>
        </Defined>
        <Next Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights" />
    </ApplicationIdProvider>
    ...
</ApplicationInsights>
```

#### <a name="example-configuration-via-code"></a>Exemplo de Configuração via código:
```csharp
TelemetryConfiguration.Active.ApplicationIdProvider = new DictionaryApplicationIdProvider{
 Defined = new Dictionary<string, string>
    {
        {"InstrumentationKey_1", "ApplicationId_1"},
        {"InstrumentationKey_2", "ApplicationId_2"}
    }
};
```




## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre a API][api].

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[client]: ./javascript.md
[diagnostic]: ./diagnostic-search.md
[exceptions]: ./asp-net-exceptions.md
[netlogs]: ./asp-net-trace-logs.md
[new]: ./create-new-resource.md
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

