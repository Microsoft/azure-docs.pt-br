---
title: 'Referência da API do Azure Status Monitor v2: Obter status | Microsoft Docs'
description: Referência da API do Status Monitor v2. Get-ApplicationInsightsMonitoringStatus. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
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
ms.openlocfilehash: 50703ede205b42032e9618c1b16b0de2bd7f3a23
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424134"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus"></a>API Status Monitor v2: Get-ApplicationInsightsMonitoringStatus

Este artigo descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrição

Este cmdlet fornece informações de solução de problemas sobre Status Monitor.
Use este cmdlet para investigar o status de monitoramento, a versão do módulo do PowerShell e para inspecionar o processo em execução.
Esse cmdlet relatará informações de versão e informações sobre os arquivos de chave necessários para o monitoramento.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.

## <a name="examples"></a>Exemplos

### <a name="example-application-status"></a>Exemplo: Status do aplicativo

Execute o comando `Get-ApplicationInsightsMonitoringStatus` para exibir o status de monitoramento dos sites.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

Neste exemplo;
- O **identificador da máquina** é uma ID anônima usada para identificar exclusivamente o servidor. Se você criar uma solicitação de suporte, precisaremos dessa ID para localizar os logs do seu servidor.
- O **site padrão** é interrompido no IIS
- O **DemoWebApp111** foi iniciado no IIS, mas não recebeu nenhuma solicitação. Este relatório mostra que não há nenhum processo em execução (ProcessId: não encontrado).
- **DemoWebApp222** está sendo executado e está sendo monitorado (instrumentado: true). Com base na configuração do usuário, a chave de instrumentação xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123 foi correspondida para este site.
- O **DemoWebApp333** foi instrumentado manualmente usando o SDK do Application insights. Status Monitor detectou o SDK e não monitorará este site.


### <a name="example-powershell-module-information"></a>Exemplo: Informações do módulo do PowerShell

Execute o comando `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` para exibir informações sobre o módulo atual:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

### <a name="example-runtime-status"></a>Exemplo: Status do tempo de execução

Você pode inspecionar o processo no computador instrumentado para ver se todas as DLLs estão carregadas. Se o monitoramento estiver funcionando, pelo menos 12 DLLs deverão ser carregadas.

Execute o comando `Get-ApplicationInsightsMonitoringStatus -InspectProcess`:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>Parâmetros

### <a name="no-parameters"></a>(Sem parâmetros)

Por padrão, esse cmdlet relatará o status de monitoramento dos aplicativos Web.
Use esta opção para examinar se o aplicativo foi instrumentado com êxito.
Você também pode examinar qual chave de instrumentação foi correspondida ao seu site.


### <a name="-powershellmodule"></a>-PowerShellModule
**Opcional**. Use essa opção para relatar os números de versão e os caminhos de DLLs necessários para o monitoramento.
Use esta opção se você precisar identificar a versão de qualquer DLL, incluindo o SDK do Application Insights.

### <a name="-inspectprocess"></a>-InspectProcess

**Opcional**. Use essa opção para relatar se o IIS está em execução.
Ele também fará o download das ferramentas externas para determinar se as DLLs necessárias são carregadas no tempo de execução do IIS.


Se esse processo falhar por algum motivo, você poderá executar estes comandos manualmente:
- iisreset.exe /status
- [handle64. exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64. exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr/I "InstrumentationEngine ia ApplicationInsights"


### <a name="-force"></a>-Force

**Opcional**. Usado somente com InspectProcess. Use essa opção para ignorar o prompt do usuário que aparece antes que as ferramentas adicionais sejam baixadas.


## <a name="next-steps"></a>Próximas etapas

 Faça mais com Status Monitor v2:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
