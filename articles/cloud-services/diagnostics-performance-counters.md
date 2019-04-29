---
title: Coletar em Contadores de Desempenho nos Serviços de Nuvem do Microsoft Azure | Microsoft Docs
description: Saiba como descobrir, usar e criar contadores de desempenho nos Serviços de Nuvem com o Application Insights e Diagnóstico do Microsoft Azure.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: jeconnoc
ms.openlocfilehash: 68101be211335d51eb4bf99361ea36b73fa19218
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653732"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Coletar contadores de desempenho para o Serviço de Nuvem do Azure

Os contadores de desempenho fornecem uma maneira de acompanhar o desempenho do aplicativo e do host. O Windows Server fornece muitos contadores de desempenho diferentes relacionados a hardware, aplicativos, sistema operacional e muito mais. Ao coletar e enviar contadores de desempenho para o Azure, é possível analisar essas informações para ajudar a tomar as melhores decisões. 

## <a name="discover-available-counters"></a>Descubra os contadores disponíveis

Um contador de desempenho é composto por duas partes, um nome definido (também conhecido como categoria) e um ou mais contadores. É possível usar o PowerShell para obter uma lista de contadores de desempenho disponíveis:

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

A propriedade `CounterSetName` representa um conjunto (ou categoria) e é um bom indicador dos contadores de desempenho relacionados. A propriedade `Paths` representa uma coleção de contadores para um conjunto. Também é possível obter a propriedade `Description` para mais informações sobre o conjunto de contadores.

Para obter todos os contadores para um conjunto, use o valor `CounterSetName` e expanda a coleção `Paths`. Cada item de caminho é um contador que pode ser consultado. Por exemplo, para obter os contadores disponíveis relacionados ao conjunto `Processor`, expanda a coleção `Paths`:

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

Esses caminhos de contador individuais podem ser adicionados à estrutura de diagnóstico que seu serviço de nuvem usa. Para obter mais informações sobre como um caminho de contador de desempenho é construído, consulte [Especificar um Caminho do Contador](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85)).

## <a name="collect-a-performance-counter"></a>Coletar um contador de desempenho

Um contador de desempenho pode ser adicionado ao serviço de nuvem para o Diagnóstico do Azure ou Application Insights.

### <a name="application-insights"></a>Application Insights

O Azure Application Insights para Serviços de Nuvem permite especificar quais contadores de desempenho deseja coletar. Após [Adicionar o Application Insights ao seu projeto ](../azure-monitor/app/cloudservices.md#sdk), um arquivo de configuração nomeado **ApplicationInsights.config** será adicionado ao seu projeto do Visual Studio. Este arquivo de configuração define o tipo de informação que Application Insights coleta e envia para o Azure.

Abra o arquivo **ApplicationInsights.config** e localize o elemento **ApplicationInsights** > **TelemetryModules**. Cada elemento filho `<Add>` define um tipo de telemetria para coletar, juntamente com sua configuração. O tipo de módulo de telemetria do contador de desempenho é `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`. Se esse elemento já estiver definido, não adicione-o novamente. Cada contador de desempenho para coletar é definido sob um nó nomeado `<Counters>`. Aqui está um exemplo que coleta os contadores de desempenho da unidade:

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

Cada contador de desempenho é representado como um elemento `<Add>` em `<Counters>`. O atributo `PerformanceCounter` define qual contador de desempenho coletar. O atributo `ReportAs` é o título a ser exibido no Portal do Azure para o contador de desempenho. Qualquer contador de desempenho que você colecionar será colocado em uma categoria nomeada **Personalizado** no portal. Diferentemente do Diagnóstico do Azure, não é possível definir o intervalo que esses contadores de desempenho serão coletados e enviados para o Azure. Com Application Insights, os contadores de desempenho são coletados e enviados a cada minuto. 

O Application Insights coleta automaticamente os seguintes contadores de desempenho:

* \Process(??APP_WIN32_PROC??)\% Tempo do Processador
* \Memória\Bytes Disponíveis
* \.Exceções NET CLR (?. APP_CLR_PROC?)\# de exceções lançadas / s
* \Processo(??APP_WIN32_PROC??)\Bytes Privados
* \Processo(??APP_WIN32_PROC??)\Bytes de dados de ES/s
* \Processador(_Total)\% Tempo do processador

Para obter mais informações, consulte [Contadores de desempenho do sistema em Application Insights](../azure-monitor/app/performance-counters.md) e [Application Insights para Serviços de Nuvem do Azure](../azure-monitor/app/cloudservices.md#performance-counters).

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

> [!IMPORTANT]
> Embora todos esses dados sejam agregados na conta de armazenamento, o portal **não** fornece um modo nativo para colocar os dados em gráfico. É altamente recomendável que você integre outro serviço de diagnóstico, como o Application Insights, no seu aplicativo.

A extensão do Diagnóstico do Azure para os Serviços de Nuvem permite que você especifique quais contadores de desempenho deseja coletar. Para configurar o Diagnóstico do Azure, consulte [Visão geral do monitoramento do serviço de nuvem](cloud-services-how-to-monitor.md#setup-diagnostics-extension).

Os contadores de desempenho que deseja coletar estão definidos no arquivo **diagnostics.wadcfgx**. Abra esse arquivo (definido por função) no Visual Studio e localize o elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Adicione um novo elemento **PerformanceCounterConfiguration** como um elemento filho. Esse elemento possui dois atributos: `counterSpecifier` e `sampleRate`. O atributo `counterSpecifier` define qual conjunto de contador de desempenho do sistema (descrito na seção anterior) coletar. O valor `sampleRate` indica a frequência de sondagem desse valor. Como um todo, todos os contadores de desempenho são transferidos para o Azure de acordo com o valor do atributo do `PerformanceCounters` elemento`scheduledTransferPeriod` pai.

Para obter mais informações sobre o elemento de esquema `PerformanceCounters`, consulte o [Esquema do Diagnóstico do Azure](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element).

O período definido pelo atributo `sampleRate` utiliza o tipo de dados de duração XML para indicar a frequência de sondagem do contador de desempenho. No exemplo abaixo, a taxa está definida para `PT3M`, que significa `[P]eriod[T]ime[3][M]inutes`: a cada três minutos.

Para obter mais informações sobre como `sampleRate` e `scheduledTransferPeriod` são definidos, consulte a seção **Tipo de dados de duração** no tutorial [Tipos de data-hora e data do XML do W3](https://www.w3schools.com/XML/schema_dtypes_date.asp).

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>Criar um novo contador de desempenho

Um novo contador de desempenho pode ser criado e usado pelo seu código. O código que cria um novo contador de desempenho deverá ser de privilégio elevado, caso contrário, ele falhará. O código de inicialização do serviço de nuvem `OnStart` pode criar o contador de desempenho, exigindo que você execute a função em um contexto elevado. Ou você pode criar uma tarefa de inicialização que seja elevada e cria o contador de desempenho. Para obter mais informações sobre tarefas de inicialização, consulte [Como configurar e executar tarefas de inicialização para um serviço de nuvem](cloud-services-startup-tasks.md).

Para configurar a função a ser executada, adicione um elemento `<Runtime>` para o arquivo [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef).

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

É possível criar e registrar um novo contador de desempenho com algumas linhas de código. Use sobrecarga do método `System.Diagnostics.PerformanceCounterCategory.Create` que cria a categoria e o contador. O código a seguir, primeiro verifica se a categoria existe e, se ausente, criará tanto a categoria como o contador.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

Quando você quiser usar o contador, chame o método `Increment` ou `IncrementBy`.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

Agora que o aplicativo utiliza o contador personalizado, será necessário configurar o Diagnóstico do Azure ou o Application Insights para acompanhar o contador.


### <a name="application-insights"></a>Application Insights

Conforme indicado anteriormente, os contadores de desempenho para o Application Insights são definidos no arquivo **ApplicationInsights.config**. Abra **ApplicationInsights.config** e localize o elemento **ApplicationInsights** > **TelemetryModules** > **Adicionar** > **Contadores**. Crie um elemento-filho `<Add>` e defina o atributo `PerformanceCounter` para a categoria e o nome do contador de desempenho que você criou em seu código. Defina o atributo `ReportAs` como um nome amigável que deseja ver no portal.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Diagnóstico do Azure

Como indicado anteriormente, os contadores de desempenho que você deseja coletar estão definidos no arquivo **diagnostics.wadcfgx**. Abra esse arquivo (definido por função) no Visual Studio e localize o elemento **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**. Adicione um novo elemento **PerformanceCounterConfiguration** como um elemento filho. Defina o atributo `counterSpecifier` para a categoria e o nome do contador de desempenho que você criou em seu código. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>Mais informações

- [Application Insights para Serviços de Nuvem do Azure](../azure-monitor/app/cloudservices.md#performance-counters)
- [Contadores de desempenho do sistema no Application Insights](../azure-monitor/app/performance-counters.md)
- [Especificar um Caminho do Contador](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Esquema do Diagnóstico do Azure - Contadores de Desempenho](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)
