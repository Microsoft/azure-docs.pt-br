---
title: Habilitar Depurador de Instantâneos para aplicativos .NET e .NET Core no Azure Functions | Microsoft Docs
description: Habilitar Depurador de Instantâneos para aplicativos .NET e .NET Core no Azure Functions
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: d86455eae0834f29099c7d5c96f8326408daf519
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98675522"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Habilitar Depurador de Instantâneos para aplicativos .NET e .NET Core no Azure Functions

Depurador de Instantâneos atualmente trabalha para aplicativos ASP.NET e ASP.NET Core que estão em execução no Azure Functions nos planos de serviço do Windows.

Recomendamos que você execute seu aplicativo na camada de serviço básica ou superior ao usar Depurador de Instantâneos.

Para a maioria dos aplicativos, as camadas de serviço gratuitas e compartilhadas não têm memória suficiente ou espaço em disco para salvar instantâneos.

## <a name="prerequisites"></a>Pré-requisitos

* [Habilite o monitoramento de Application Insights no seu Aplicativo de funções](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a>Habilitar Depurador de Instantâneos

Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para habilitar Depurador de Instantâneos em outras plataformas com suporte:
* [Serviço de Aplicativo do Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Service Fabric do Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais ou físicas locais](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Para habilitar Depurador de Instantâneos em seu aplicativo de funções, você precisa atualizar o `host.json` arquivo adicionando a propriedade `snapshotConfiguration` conforme definido abaixo e reimplanta sua função.

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

O Depurador de Instantâneos é pré-instalado como parte do tempo de execução do Azure Functions, que por padrão é desabilitado.

Como Depurador de Instantâneos está incluído no tempo de execução de Azure Functions, não é necessário adicionar pacotes NuGet extras nem configurações de aplicativo.

Da mesma forma que a referência, para um aplicativo de funções simples (.NET Core), abaixo está como ele irá procurar `.csproj` , `{Your}Function.cs` e `host.json` depois de habilitado depurador de instantâneos nele.

Csproj do projeto

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Classe de função

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

Arquivo de host

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="disable-snapshot-debugger"></a>Desabilitar Depurador de Instantâneos

Para desabilitar Depurador de Instantâneos em seu aplicativo de funções, você só precisa atualizar o `host.json` arquivo definindo para `false` a propriedade `snapshotConfiguration.isEnabled` .

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

Recomendamos que você tenha Depurador de Instantâneos habilitado em todos os seus aplicativos para facilitar o diagnóstico de exceções de aplicativo.

## <a name="next-steps"></a>Próximas etapas

- Gere o tráfego para o aplicativo que pode disparar uma exceção. Em seguida, aguarde de 10 a 15 minutos para que os instantâneos sejam enviados para a instância de Application Insights.
- [Exiba instantâneos](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal) no portal do Azure.
- Personalize a configuração de Depurador de Instantâneos com base no seu caso de uso em seu aplicativo de funções. Para obter mais informações, consulte [configuração de instantâneo no host.jsem](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration).
- Para obter ajuda com a solução de problemas de Depurador de Instantâneos, consulte [depurador de instantâneos solução de problemas](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).