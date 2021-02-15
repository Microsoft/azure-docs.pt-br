---
title: Perfil de aplicativos de Service Fabric dinâmicos do Azure com Application Insights
description: Habilitar o Profiler para um aplicativo do Service Fabric
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 317c02fe03a56ea3cd3a98b00f8a0080b3917108
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930274"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Criar perfil de aplicativos ativos do Azure Service Fabric com o Application Insights

Você também pode implantar o Application Insights Profiler nesses serviços:
* [Serviço de Aplicativo do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O Application Insights Profiler está incluído com o Diagnóstico do Azure. Você pode instalar a extensão de Diagnóstico do Azure usando um modelo do Azure Resource Manager para seu cluster do Service Fabric. Obtenha um [Modelo que instala o Diagnóstico do Azure em um Cluster do Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Para configurar seu ambiente, faça o seguinte:

1. O criador de perfil dá suporte a .NET Framework e .Net Core. Se você estiver usando .NET Framework, verifique se está usando [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior. É suficiente confirmar que o sistema operacional implantado é `Windows Server 2012 R2` ou posterior. O criador de perfil dá suporte ao .NET Core 2,1 e a aplicativos mais recentes.

1. Pesquise a extensão [Diagnóstico do Azure](../platform/diagnostics-extension-overview.md) no arquivo de modelo de implantação.

1. Adicione a seguinte seção `SinksConfig` como um elemento filho de `WadCfg`. Substitua o valor da propriedade `ApplicationInsightsProfiler` pela sua própria chave de instrumentação do Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Para obter informações sobre como adicionar a extensão de Diagnóstico em seu modelo de implantação, consulte [Usar monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Usando o modelo do Azure Resource Manager, implante seu cluster do Service Fabric.  
  Se as configurações estiverem corretas, o Application Insights Profiler será instalado e habilitado quando a extensão do Diagnóstico do Azure for instalada. 

1. Adicione o Application Insights ao seu aplicativo do Service Fabric.  
  Para que o profiler colete perfis para suas solicitações, seu aplicativo deve estar controlando operações com Application Insights. Para APIs sem monitoração de estado, você pode consultar as instruções para [controlar as solicitações de criação de perfil](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Para obter mais informações sobre como controlar operações personalizadas em outros tipos de aplicativos, consulte [rastrear operações personalizadas com o SDK do .net Application insights](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Reimplante o aplicativo.


## <a name="next-steps"></a>Próximas etapas

* Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
* Consulte [Rastreamentos do criador de perfil](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
* Para obter ajuda para a solução de problemas do Profiler, confira [Solução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
