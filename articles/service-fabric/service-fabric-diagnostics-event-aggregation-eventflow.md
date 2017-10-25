---
title: "Agregação de Eventos do Service Fabric do Azure com EventFlow | Microsoft Docs"
description: "Saiba mais sobre a agregação e a coleta de eventos utilizando EventFlow para monitoramento e diagnóstico de clusters do Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: dekapur
ms.openlocfilehash: f57c915dd566e9da9b751bb776a1170842d87297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Agregação e coleta de eventos usando EventFlow

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) pode encaminhar eventos de um nó a um ou mais destinos de monitoramentos. Como ele está incluído como um pacote NuGet em seu projeto de serviço, o código e a configuração do EventFlow viajam com o serviço, eliminando o problema de configuração por nó mencionado anteriormente sobre o Diagnóstico do Azure. O EventFlow é executado em seu processo de serviço e conecta-se diretamente às saídas configuradas. Devido a essa conexão direta, o EventFlow funciona para o Azure, o contêiner e as implantações de serviço locais. Tenha cuidado se você executar EventFlow em cenários de alta densidade, como em um contêiner, pois cada pipeline EventFlow faz uma conexão externa. Então, se você hospedar vários processos, obterá várias conexões de saída! Isso não é tanto uma preocupação para aplicativos de Service Fabric, porque todas as réplicas de um `ServiceType` são executadas no mesmo processo e isso limita o número de conexões de saída. O EventFlow também oferece a filtragem de eventos e, portanto, somente os eventos que correspondem ao filtro especificado são enviadas.

## <a name="setting-up-eventflow"></a>Configuração do EventFlow

EventFlow binários estão disponíveis como um conjunto de pacotes do NuGet. Para adicionar EventFlow a um projeto de serviço do Service Fabric, clique com botão direito do mouse no projeto no Gerenciador de Soluções e escolha "Gerenciar pacotes NuGet." Alternar para a guia "Procurar" e pesquise por "`Diagnostics.EventFlow`":

![Pacotes do EventFlow NuGet no Gerenciador de pacotes do NuGet do Visual Studio da interface do usuário](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Você verá uma lista de vários pacotes, rotulados como "Entradas" e "Saídas". O EventFlow dá suporte a vários provedores de log e analisadores diferentes. O serviço que hospeda EventFlow deve incluir pacotes apropriados dependendo da origem e destino para os logs de aplicativo. Além do pacote principal do Service Fabric, você também precisa de pelo menos uma Entrada e Saída configuradas. Por exemplo, você pode adicionar os seguintes pacotes para eventos EventSource enviados ao Application Insights:

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` para capturar dados de classe do EventSource do serviço e de EventSources padrão como *Microsoft-ServiceFabric-Services* e *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` (vamos enviar os logs para um recurso do Azure Application Insights)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(habilita a inicialização do pipeline EventFlow da configuração do serviço Service Fabric e relata quaisquer problemas com o envio de dados de diagnóstico como relatórios de integridade do Service Fabric)

>[!NOTE]
>O pacote `Microsoft.Diagnostics.EventFlow.Input.EventSource` requer que o projeto de serviço tenha como destino o .NET Framework 4.6 ou mais recente. Certifique-se de que definir a estrutura de destino apropriada nas propriedades do projeto antes de instalar este pacote.

Depois que todos os pacotes são instalados, a próxima etapa é configurar e habilitar EventFlow no serviço.

## <a name="configuring-and-enabling-log-collection"></a>Configurar e habilitar a coleta de logs
O pipeline de EventFlow, responsável pelo envio de logs, é criado de uma especificação armazenada em um arquivo de configuração. O `Microsoft.Diagnostics.EventFlow.ServiceFabric`pacote instala um arquivo de configuração inicial do EventFlow em `PackageRoot\Config` pasta da solução, chamada `eventFlowConfig.json`. Esse arquivo de configuração precisa ser modificado para capturar dados da classe `EventSource` do serviço padrão e quaisquer outras entradas que você deseja configurar, bem como enviar dados ao local apropriado.

Aqui está um *eventFlowConfig.json* de exemplo com base nos pacotes NuGet mencionados acima:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

O nome da ServiceEventSource do serviço é o valor da propriedade Nome do `EventSourceAttribute` aplicado à classe ServiceEventSource. Isso é especificado no arquivo `ServiceEventSource.cs`, que faz parte do código do serviço. Por exemplo, no trecho de código a seguir, o nome do ServiceEventSource é *MyCompany-Application1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Observe que o arquivo `eventFlowConfig.json` faz parte do pacote de configuração de serviço. As alterações feitas neste arquivo podem ser incluídas em atualizações completo - ou apenas da configuração do serviço, sujeito a verificações de integridade de atualização do Service Fabric e a reversão automática se houver falha na atualização. Para saber mais, confira [Atualização de aplicativos do Service Fabric](service-fabric-application-upgrade.md).

A seção *Filtros* da configuração permite que você personalize as informações que passarão pelo pipeline EventFlow para as saídas, permitindo que você descarte ou inclua determinadas informações ou altere ainda mais a estrutura dos dados do evento. Para saber mais sobre filtragem, confira [Filtros EventFlow](https://github.com/Azure/diagnostics-eventflow#filters).

A etapa final é criar uma instância de pipeline de EventFlow no código de inicialização do serviço, localizado no arquivo `Program.cs`:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

O nome passado como o parâmetro do método `CreatePipeline` do `ServiceFabricDiagnosticsPipelineFactory` é o nome da *entidade de integridade* que representa o pipeline de coleta de logs de EventFlow. Esse nome será usado se EventFlow encontrar um erro e o reportar por meio do subsistema de integridade do Service Fabric.

### <a name="using-service-fabric-settings-and-application-parameters-to-in-eventflowconfig"></a>Usando configurações do Service Fabric e parâmetros de aplicativo em eventFlowConfig

O EventFlow dá suporte ao uso de configurações do Service Fabric e parâmetros de aplicativo para definir configurações de EventFlow. Você pode se referir aos parâmetros de configuração do Service Fabric usando essa sintaxe especial para valores:

```json
servicefabric:/<section-name>/<setting-name>
``` 

`<section-name>` é o nome da seção de configuração do Service Fabric e `<setting-name>` é a configuração que fornece o valor que será usado para definir uma configuração de EventFlow. Para ler mais sobre como fazer isso, acesse o [Suporte para parâmetros de aplicativo e configurações do Service Fabric](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Verificação

Inicie o serviço e observe a depuração janela saída no Visual Studio. Depois que o serviço é iniciado, você deve começar a ver evidências de que ele está enviando registros para a saída que você configurou. Navegue até a plataforma de análise e visualização de eventos e confirme se os logs começaram a ser mostrados (isso pode levar alguns minutos).

## <a name="next-steps"></a>Próximas etapas

* [Visualização e Análise de Eventos com o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Visualização com o OMS e Análise de Eventos](service-fabric-diagnostics-event-analysis-oms.md)
* [Documentação de EventFlow](https://github.com/Azure/diagnostics-eventflow)
