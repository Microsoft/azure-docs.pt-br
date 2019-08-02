---
title: 'Referência da API do Azure Status Monitor v2: Iniciar rastreamento | Microsoft Docs'
description: Referência da API do Status Monitor v2. Start-Trace. Coletar logs do ETW do Status Monitor e do SDK do Application Insights.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 3060a48e297082f5b70c8bd96030fe519f6e8b6f
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326300"
---
# <a name="status-monitor-v2-api-start-applicationinsightsmonitoringtrace"></a>API Status Monitor v2: Start-ApplicationInsightsMonitoringTrace

Este artigo descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>DESCRIÇÃO

Coleta [eventos ETW](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) do tempo de execução de anexação de código. Esse cmdlet é uma alternativa à execução de [Perfview](https://github.com/microsoft/perfview).

Os eventos coletados serão impressos no console em tempo real e salvos em um arquivo ETL. O arquivo ETL de saída pode ser aberto por [Perfview](https://github.com/microsoft/perfview) para investigação adicional.

Este cmdlet será executado até atingir a duração do tempo limite (padrão 5 minutos) ou for interrompido manualmente`Ctrl + C`().

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

## <a name="examples"></a>Exemplos

### <a name="how-to-collect-events"></a>Como coletar eventos

Normalmente, pedimos que você colete eventos para investigar por que seu aplicativo não está sendo instrumentado.

O tempo de execução de anexação com código emitirá eventos ETW quando o IIS for iniciado e quando seu aplicativo for iniciado.

Para coletar esses eventos:
1. Em um console cmd com privilégios de administrador, `iisreset /stop` execute para desligar o IIS e todos os aplicativos Web.
2. Executar este cmdlet
3. Em um console cmd com privilégios de administrador, `iisreset /start` execute para iniciar o IIS.
4. Tente navegar até seu aplicativo.
5. Depois de concluir o carregamento do aplicativo, você pode interrompê`Ctrl + C`-lo manualmente () ou aguardar o tempo limite.

### <a name="what-events-to-collect"></a>Quais eventos coletar

Você tem três opções ao coletar eventos:
1. Use a opção `-CollectSdkEvents` para coletar eventos emitidos pelo SDK do Application insights.
2. Use a opção `-CollectRedfieldEvents` para coletar eventos emitidos por status monitor e o tempo de execução Redfield. Esses logs são úteis ao diagnosticar o IIS e a inicialização do aplicativo.
3. Use ambos os switches para coletar ambos os tipos de evento.
4. Por padrão, se nenhuma opção for especificada, ambos os tipos de eventos serão coletados.


## <a name="parameters"></a>parâmetros

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcional** Use esse parâmetro para definir por quanto tempo esse script deve coletar eventos. O padrão é de 5 minutos.

### <a name="-logdirectory"></a>-LogDirectory
**Opcional** Use essa opção para definir o diretório de saída do arquivo ETL. Por padrão, esse arquivo será criado no diretório de módulos do PowerShell. O caminho completo será exibido durante a execução do script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcional** Use essa opção para coletar Application Insights eventos do SDK.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcional** Use essa opção para coletar eventos de Status Monitor e o tempo de execução Redfield.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para gerar logs detalhados.



## <a name="output"></a>Saída


### <a name="example-of-application-startup-logs"></a>Exemplo de logs de inicialização do aplicativo
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Próximas etapas

Solução de problemas adicional:

- Examine as etapas de solução de problemas adicionais aqui: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Examine a [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para saber mais sobre os parâmetros que você pode ter perdido.
- Se precisar de ajuda adicional, você poderá entrar em contato conosco no [GitHub](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Faça mais com Status Monitor v2:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
 - [Obtenha a configuração](status-monitor-v2-api-get-config.md) para confirmar que as configurações foram registradas corretamente.
 - [Obtenha o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
