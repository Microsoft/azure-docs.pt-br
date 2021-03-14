---
title: Referência da API do agente .net do Aplicativo Azure insights
description: Referência de API do agente Application Insights. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2278b9d70e888fa546dc64da4743b2bf5b6c45e8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587532"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Referência da API do agente do Application Insights Azure Monitor

Este artigo descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!NOTE] 
> - Para começar, você precisa de uma chave de instrumentação. Para obter mais informações, consulte [criar um recurso](create-new-resource.md#copy-the-instrumentation-key).
> - Esse cmdlet requer que você examine e aceite nossa licença e nossa política de privacidade.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador e uma política de execução elevada. Para obter mais informações, consulte [executar o PowerShell como administrador com uma política de execução elevada](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).
> - Esse cmdlet requer que você examine e aceite nossa licença e nossa política de privacidade.
> - O mecanismo de instrumentação adiciona sobrecarga adicional e está desativado por padrão.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Habilita o mecanismo de instrumentação definindo algumas chaves do registro.
Reinicie o IIS para que as alterações entrem em vigor.

O mecanismo de instrumentação pode complementar os dados coletados pelos SDKs do .NET.
Ele coleta eventos e mensagens que descrevem a execução de um processo gerenciado. Esses eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e [texto de comando SQL](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Habilite o mecanismo de instrumentação se:
- Você já habilitou o monitoramento com o cmdlet Enable, mas não habilitou o mecanismo de instrumentação.
- Você instrumentou manualmente seu aplicativo com os SDKs do .NET e deseja coletar telemetria adicional.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parâmetros

#### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Use essa opção para aceitar a licença e a política de privacidade em instalações sem periféricos.

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para gerar logs detalhados.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exemplo de saída de habilitação do mecanismo de instrumentação com êxito

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Habilita o monitoramento de anexação de código de aplicativos IIS em um computador de destino.

Esse cmdlet modificará o applicationHost.config do IIS e definirá algumas chaves do registro.
Ele também criará um arquivo de applicationinsights.ikey.config, que define a chave de instrumentação usada por cada aplicativo.
O IIS carregará o RedfieldModule na inicialização, que injetará o SDK Application Insights em aplicativos à medida que os aplicativos forem iniciados.
Reinicie o IIS para que suas alterações entrem em vigor.

Depois de habilitar o monitoramento, recomendamos que você use [métricas em tempo real](live-stream.md) para verificar rapidamente se seu aplicativo está enviando telemetria dos EUA.

### <a name="examples"></a>Exemplos

#### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual recebem uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa de chave de instrumentação
Neste exemplo:
- `MachineFilter` corresponde ao computador atual usando o `'.*'` caractere curinga.
- `AppFilter='WebAppExclude'` fornece uma `null` chave de instrumentação. O aplicativo especificado não será instrumentado.
- `AppFilter='WebAppOne'` atribui ao aplicativo especificado uma chave de instrumentação exclusiva.
- `AppFilter='WebAppTwo'` atribui ao aplicativo especificado uma chave de instrumentação exclusiva.
- Por fim, `AppFilter` o também usa o `'.*'` curinga para corresponder a todos os aplicativos Web que não são compatíveis com as regras anteriores e atribuir uma chave de instrumentação padrão.
- Os espaços são adicionados para facilitar a leitura.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>Parâmetros

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Necessário.** Use esse parâmetro para fornecer uma chave de instrumentação única para uso por todos os aplicativos no computador de destino.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Necessário.** Use esse parâmetro para fornecer várias chaves de instrumentação e um mapeamento das chaves de instrumentação usadas por cada aplicativo.
Você pode criar um único script de instalação para vários computadores definindo `MachineFilter` .

> [!IMPORTANT]
> Os aplicativos serão compatíveis com as regras na ordem em que as regras são fornecidas. Portanto, você deve especificar as regras mais específicas primeiro e as regras mais genéricas por último.

##### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** é um Regex C# necessário do computador ou nome da VM.
    - '. * ' corresponderá a todos
    - ' ComputerName ' corresponderá apenas a computadores com o nome exato especificado.
- **AppFilter** é um Regex do C# necessário do nome do site do IIS. Você pode obter uma lista de sites em seu servidor executando o comando [Get-iissite](/powershell/module/iisadministration/get-iissite).
    - '. * ' corresponderá a todos
    - ' SiteName ' corresponderá apenas ao site do IIS com o nome exato especificado.
- **InstrumentationKey** é necessário para habilitar o monitoramento de aplicativos que correspondem aos dois filtros anteriores.
    - Deixe esse valor NULL se você quiser definir regras para excluir o monitoramento.


#### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcional.** Use essa opção para habilitar o mecanismo de instrumentação para coletar eventos e mensagens sobre o que está acontecendo durante a execução de um processo gerenciado. Esses eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e texto de comando SQL.

O mecanismo de instrumentação adiciona sobrecarga e está desativado por padrão.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional.** Use essa opção para aceitar a licença e a política de privacidade em instalações sem periféricos.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando você tem um cluster de servidores Web, você pode estar usando uma [configuração compartilhada](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nessa configuração compartilhada.
Esse script falhará com a mensagem informando que as etapas de instalação extras são necessárias.
Use essa opção para ignorar essa verificação e continuar instalando os pré-requisitos. Para obter mais informações, consulte [conflito conhecido-com a configuração compartilhada do IIS](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para exibir logs detalhados.

#### <a name="-whatif"></a>-WhatIf 
**Parâmetro comum.** Use essa opção para testar e validar os parâmetros de entrada sem realmente habilitar o monitoramento.

### <a name="output"></a>Saída

#### <a name="example-output-from-a-successful-enablement"></a>Exemplo de saída de uma habilitação bem-sucedida

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Disable-InstrumentationEngine

Desabilita o mecanismo de instrumentação removendo algumas chaves do registro.
Reinicie o IIS para que as alterações entrem em vigor.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parâmetros 

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para gerar logs detalhados.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Exemplo de saída de desabilitar o mecanismo de instrumentação com êxito

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring

Desabilita o monitoramento no computador de destino.
Esse cmdlet removerá as edições do applicationHost.config do IIS e removerá as chaves do registro.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parâmetros 

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para exibir logs detalhados.

### <a name="output"></a>Saída


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Exemplo de saída de desabilitação do monitoramento com êxito

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Obtém o arquivo de configuração e imprime os valores no console.

### <a name="examples"></a>Exemplos

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parâmetros

Nenhum parâmetro é necessário.

### <a name="output"></a>Saída


##### <a name="example-output-from-reading-the-config-file"></a>Exemplo de saída da leitura do arquivo de configuração

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Get-ApplicationInsightsMonitoringStatus

Este cmdlet fornece informações de solução de problemas sobre Status Monitor.
Use este cmdlet para investigar o status de monitoramento, a versão do módulo do PowerShell e para inspecionar o processo em execução.
Esse cmdlet relatará informações de versão e informações sobre os arquivos de chave necessários para o monitoramento.

### <a name="examples"></a>Exemplos

#### <a name="example-application-status"></a>Exemplo: status do aplicativo

Execute o comando `Get-ApplicationInsightsMonitoringStatus` para exibir o status de monitoramento dos sites.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

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


#### <a name="example-powershell-module-information"></a>Exemplo: informações de módulo do PowerShell

Execute o comando `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` para exibir informações sobre o módulo atual:

```powershell

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

#### <a name="example-runtime-status"></a>Exemplo: status de tempo de execução

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

### <a name="parameters"></a>Parâmetros

#### <a name="no-parameters"></a>(Sem parâmetros)

Por padrão, esse cmdlet relatará o status de monitoramento dos aplicativos Web.
Use esta opção para examinar se o aplicativo foi instrumentado com êxito.
Você também pode examinar qual chave de instrumentação foi correspondida ao seu site.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Opcional**. Use essa opção para relatar os números de versão e os caminhos de DLLs necessários para o monitoramento.
Use esta opção se você precisar identificar a versão de qualquer DLL, incluindo o SDK do Application Insights.

#### <a name="-inspectprocess"></a>-InspectProcess

**Opcional**. Use essa opção para relatar se o IIS está em execução.
Ele também fará o download das ferramentas externas para determinar se as DLLs necessárias são carregadas no tempo de execução do IIS.


Se esse processo falhar por algum motivo, você poderá executar estes comandos manualmente:
- iisreset.exe/status
- [handle64.exe](/sysinternals/downloads/handle) -p w3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights
- [listdlls64.exe](/sysinternals/downloads/listdlls) w3wp | findstr/I "InstrumentationEngine ia ApplicationInsights"


#### <a name="-force"></a>-Force

**Opcional**. Usado somente com InspectProcess. Use essa opção para ignorar o prompt do usuário que aparece antes que as ferramentas adicionais sejam baixadas.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Define o arquivo de configuração sem fazer uma reinstalação completa.
Reinicie o IIS para que suas alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.


### <a name="examples"></a>Exemplos

#### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual serão atribuídos a uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa de chave de instrumentação
Neste exemplo:
- `MachineFilter` corresponde ao computador atual usando o `'.*'` caractere curinga.
- `AppFilter='WebAppExclude'` fornece uma `null` chave de instrumentação. O aplicativo especificado não será instrumentado.
- `AppFilter='WebAppOne'` atribui ao aplicativo especificado uma chave de instrumentação exclusiva.
- `AppFilter='WebAppTwo'` atribui ao aplicativo especificado uma chave de instrumentação exclusiva.
- Por fim, `AppFilter` o também usa o `'.*'` curinga para corresponder a todos os aplicativos Web que não são compatíveis com as regras anteriores e atribuir uma chave de instrumentação padrão.
- Os espaços são adicionados para facilitar a leitura.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

### <a name="parameters"></a>Parâmetros

#### <a name="-instrumentationkey"></a>-InstrumentationKey
**Necessário.** Use esse parâmetro para fornecer uma chave de instrumentação única para uso por todos os aplicativos no computador de destino.

#### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Necessário.** Use esse parâmetro para fornecer várias chaves de instrumentação e um mapeamento das chaves de instrumentação usadas por cada aplicativo.
Você pode criar um único script de instalação para vários computadores definindo `MachineFilter` .

> [!IMPORTANT]
> Os aplicativos serão compatíveis com as regras na ordem em que as regras são fornecidas. Portanto, você deve especificar as regras mais específicas primeiro e as regras mais genéricas por último.

##### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** é um Regex C# necessário do computador ou nome da VM.
    - '. * ' corresponderá a todos
    - ' ComputerName ' corresponderá apenas a computadores com o nome especificado.
- **AppFilter** é um Regex C# necessário do computador ou nome da VM.
    - '. * ' corresponderá a todos
    - ' ApplicationName ' corresponderá apenas aos aplicativos IIS com o nome especificado.
- **InstrumentationKey** é necessário para habilitar o monitoramento dos aplicativos que correspondem aos dois filtros anteriores.
    - Deixe esse valor NULL se você quiser definir regras para excluir o monitoramento.


#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para exibir logs detalhados.


### <a name="output"></a>Saída

Por padrão, nenhuma saída.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exemplo de saída detalhada da configuração do arquivo de configuração via-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exemplo de saída detalhada da configuração do arquivo de configuração via-InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Coleta [eventos ETW](/windows/desktop/etw/event-tracing-portal) do tempo de execução de anexação de código. Esse cmdlet é uma alternativa à execução de [Perfview](https://github.com/microsoft/perfview).

Os eventos coletados serão impressos no console em tempo real e salvos em um arquivo ETL. O arquivo ETL de saída pode ser aberto por [Perfview](https://github.com/microsoft/perfview) para investigação adicional.

Este cmdlet será executado até atingir a duração do tempo limite (padrão 5 minutos) ou for interrompido manualmente ( `Ctrl + C` ).

### <a name="examples"></a>Exemplos

#### <a name="how-to-collect-events"></a>Como coletar eventos

Normalmente, pedimos que você colete eventos para investigar por que seu aplicativo não está sendo instrumentado.

O tempo de execução de anexação com código emitirá eventos ETW quando o IIS for iniciado e quando seu aplicativo for iniciado.

Para coletar esses eventos:
1. Em um console cmd com privilégios de administrador, execute `iisreset /stop` para desligar o IIS e todos os aplicativos Web.
2. Executar este cmdlet
3. Em um console cmd com privilégios de administrador, execute `iisreset /start` para iniciar o IIS.
4. Tente navegar até seu aplicativo.
5. Depois de concluir o carregamento do aplicativo, você pode interrompê-lo manualmente ( `Ctrl + C` ) ou aguardar o tempo limite.

#### <a name="what-events-to-collect"></a>Quais eventos coletar

Você tem três opções ao coletar eventos:
1. Use a opção `-CollectSdkEvents` para coletar eventos emitidos pelo SDK do Application insights.
2. Use a opção `-CollectRedfieldEvents` para coletar eventos emitidos por status monitor e o tempo de execução Redfield. Esses logs são úteis ao diagnosticar o IIS e a inicialização do aplicativo.
3. Use ambos os switches para coletar ambos os tipos de evento.
4. Por padrão, se nenhuma opção for especificada, ambos os tipos de eventos serão coletados.


### <a name="parameters"></a>Parâmetros

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Opcional.** Use esse parâmetro para definir por quanto tempo esse script deve coletar eventos. O padrão é de 5 minutos.

#### <a name="-logdirectory"></a>-LogDirectory
**Opcional.** Use essa opção para definir o diretório de saída do arquivo ETL. Por padrão, esse arquivo será criado no diretório de módulos do PowerShell. O caminho completo será exibido durante a execução do script.


#### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Opcional.** Use essa opção para coletar Application Insights eventos do SDK.

#### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Opcional.** Use essa opção para coletar eventos de Status Monitor e o tempo de execução Redfield.

#### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para gerar logs detalhados.



### <a name="output"></a>Saída


#### <a name="example-of-application-startup-logs"></a>Exemplo de logs de inicialização do aplicativo
```powershell
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

  Exiba sua telemetria:
 - [Explore as métricas](../essentials/metrics-charts.md) para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](./diagnostic-search.md) para diagnosticar problemas.
- Use a [análise](../logs/log-query-overview.md) para consultas mais avançadas.
- [Crie painéis](./overview-dashboard.md).
 
 Adicione mais telemetria:
 - [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
- [Adicione telemetria de cliente Web](./javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](./asp-net.md) para que você possa inserir chamadas de rastreamento e log.
 
 Faça mais com Application Insights agente:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Application insights Agent.

