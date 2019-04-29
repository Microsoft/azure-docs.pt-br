---
title: Insights do Aplicativo do Azure - Funções do Azure Recursos Suportados | Microsoft Docs
description: Recursos suportados do Application Insights para funções do Azure
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: ''
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 10/05/2018
ms.reviewer: mbullwin
ms.author: tilee
ms.openlocfilehash: 101c985178b8269b4ff542b94b057330d0c2652a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60902240"
---
# <a name="application-insights-for-azure-functions-supported-features"></a>Recursos suportados do Application Insights para o Azure Functions

O Azure Functions oferece [integração interna](https://docs.microsoft.com/azure/azure-functions/functions-monitoring) com o Application Insights, que está disponível por meio da Interface do ILogger. Abaixo está a lista de recursos atualmente suportados. Examine o guia do Azure Functions para [Introdução ao](https://github.com/Azure/Azure-Functions/wiki/App-Insights).

## <a name="supported-features"></a>Recursos com suporte

| Funções do Azure                       | V1                | V2 (2018 do Ignite)  | 
|-----------------------------------    |---------------    |------------------ |
| **SDK .NET do Application Insights**   | **2.5.0**       | **2.9.1**         |
| | | | 
| **Coleta automática de**        |                 |                   |               
| &bull; Solicitações                     | Sim             | Sim               | 
| &bull; Exceções                   | Sim             | Sim               | 
| &bull; Contadores de desempenho         | Sim             | Sim               |
| &bull; Dependências                   |                   |                   |               
| &nbsp;&nbsp;&nbsp;&mdash; HTTP      |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; Barramento de serviço|                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; hub de eventos  |                 | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; SQL       |                 | Sim               | 
| | | | 
| **Recursos com suporte**                |                   |                   |               
| &bull; QuickPulse/LiveMetrics       | Sim             | Sim               | 
| &nbsp;&nbsp;&nbsp;&mdash; Canal de controle seguro|                 | Sim               | 
| &bull; Amostragem                     | Sim             | Sim               | 
| &bull; Pulsações                   |                 | Sim               | 
| | | | 
| **Correlação**                       |                   |                   |               
| &bull; Barramento de serviço                     |                   | Sim               | 
| &bull; hub de eventos                       |                   | Sim               | 
| | | | 
| **Configurável**                      |                   |                   |           
| &bull;Totalmente configurável.<br/>Ver [Azure Functions](https://github.com/Microsoft/ApplicationInsights-aspnetcore/issues/759#issuecomment-426687852) para obter instruções.<br/>Ver [Asp.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) para todas as opções.               |                   | Sim                   | 


## <a name="performance-counters"></a>Contadores de desempenho

A coleta automática de contadores de desempenho só funciona em máquinas Windows.


## <a name="live-metrics--secure-control-channel"></a>Live Metrics e canal de controle seguro

Os critérios de filtro personalizados especificados são enviados para o componente de Métricas em tempo real no SDK do Application Insights. Os filtros podem conter informações confidenciais, como customerIDs. Torne o canal seguro com uma chave de API secreta. Confira [Proteger o canal de controle](https://docs.microsoft.com/azure/azure-monitor/app/live-stream#secure-the-control-channel) para obter instruções.

## <a name="sampling"></a>amostragem

As funções do Azure ativam a amostragem por padrão em suas configurações. Para obter mais informações, consulte [configurar amostragem](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling).

Se seu projeto usa uma dependência no SDK do Application Insights para fazer o acompanhamento de telemetria manual, você pode enfrentar um comportamento estranho se sua configuração de amostragem é diferente de configuração de amostragem do Functions. 

É recomendável usar a mesma configuração como funções. Com o **Functions v2**, você pode obter a mesma configuração usando a injeção de dependência em seu construtor:

```csharp
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Extensibility;

public class Function1 
{

    private readonly TelemetryClient telemetryClient;

    public Function1(TelemetryConfiguration configuration)
    {
        this.telemetryClient = new TelemetryClient(configuration);
    }

    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req, ILogger logger)
    {
        this.telemetryClient.TrackTrace("C# HTTP trigger function processed a request.");
    }
}
```
