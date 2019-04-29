---
title: Escrever código para acompanhar as solicitações com o Azure Application Insights | Microsoft Docs
description: Escrever código para acompanhar as solicitações com o Application Insights para que você possa obter perfis para suas solicitações.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 4782e560b580b7f565724dbb35ed9876bffdc256
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730847"
---
# <a name="write-code-to-track-requests-with-application-insights"></a>Escrever código para acompanhar as solicitações com o Application Insights

Para exibir os perfis para o seu aplicativo na página de desempenho, o Azure Application Insights precisa estar controlando as solicitações para o aplicativo. O Application Insights pode acompanhar automaticamente as solicitações para aplicativos criados em estruturas já instrumentadas. Dois exemplos disso são o ASP.NET e ASP.NET Core. 

Para outros aplicativos, tais como funções de trabalho dos Serviços de Nuvem do Azure e APIs sem estado do Service Fabric, você precisa escrever código para informar ao Application Insights onde suas solicitações começam e terminam. Depois que você tiver escrito esse código, as solicitações de telemetria serão enviadas ao Application Insights. Você pode ver a telemetria na página Desempenho e perfis são coletados para essas solicitações. 

Para acompanhar solicitações manualmente, faça o seguinte:

  1. No início do tempo de vida do aplicativo, adicione o código a seguir:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      Para obter mais informações essa configuração de chave de Instrumentação global, consulte [Use Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) (Usando o Service Fabric com o Application Insights).  

  1. Para qualquer parte do código que você deseje instrumentar, adicione-a entre uma instrução `StartOperation<RequestTelemetry>` **using**, como no exemplo a seguir:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Não há suporte para chamar `StartOperation<RequestTelemetry>` dentro de outro escopo `StartOperation<RequestTelemetry>`. Você pode usar `StartOperation<DependencyTelemetry>` no escopo aninhado, em vez disso. Por exemplo:   
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```
