---
title: Problemas conhecidos e solução de problemas do agente do Aplicativo Azure insights | Microsoft Docs
description: Os problemas conhecidos do agente de Application Insights e exemplos de solução de problemas. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 2641218fa9ddef65c45f2f1a9c9ce807cef35048
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642738"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Solucionando problemas do agente de Application Insights (anteriormente denominado Status Monitor v2)

Ao habilitar o monitoramento, você pode enfrentar problemas que impedem a coleta de dados.
Este artigo lista todos os problemas conhecidos e fornece exemplos de solução de problemas.

## <a name="known-issues"></a>Problemas conhecidos

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>DLLs conflitantes no diretório bin de um aplicativo

Se qualquer uma dessas DLLs estiver presente no diretório bin, o monitoramento poderá falhar:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Algumas dessas DLLs estão incluídas nos modelos de aplicativo padrão do Visual Studio, mesmo que seu aplicativo não as use.
Você pode usar ferramentas de solução de problemas para ver o comportamento do sintoma:

- Perfview
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset e carga do aplicativo (sem telemetria). Investigue com o Sysinternals (Handle.exe e ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="powershell-versions"></a>Versões do PowerShell
Este produto foi escrito e testado usando o PowerShell v 5.1.
Este módulo não é compatível com o PowerShell versões 6 ou 7.
É recomendável usar o PowerShell v 5.1 juntamente com as versões mais recentes. Para obter mais informações, consulte [usando o PowerShell 7 lado a lado com o powershell 5,1](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7#using-powershell-7-side-by-side-with-windows-powershell-51).

### <a name="conflict-with-iis-shared-configuration"></a>Conflito com a configuração compartilhada do IIS

Se você tiver um cluster de servidores Web, talvez esteja usando uma [configuração compartilhada](/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nessa configuração compartilhada.
Execute o comando Enable em cada servidor Web para instalar a DLL no GAC de cada servidor.

Depois de executar o comando Enable, conclua estas etapas:
1. Vá para o diretório de configuração compartilhada e localize o arquivo de applicationHost.config.
2. Adicione essa linha à seção módulos de sua configuração:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Aplicativos aninhados do IIS

Não instrumento aplicativos aninhados no IIS na versão 1,0.

### <a name="advanced-sdk-configuration-isnt-available"></a>A configuração avançada do SDK não está disponível.

A configuração do SDK não é exposta ao usuário final na versão 1,0.

    
    
## <a name="troubleshooting"></a>Solução de problemas
    
### <a name="troubleshooting-powershell"></a>Solucionando problemas do PowerShell

#### <a name="determine-which-modules-are-available"></a>Determinar quais módulos estão disponíveis
Você pode usar o `Get-Module -ListAvailable` comando para determinar quais módulos estão instalados.

#### <a name="import-a-module-into-the-current-session"></a>Importar um módulo para a sessão atual
Se um módulo não tiver sido carregado em uma sessão do PowerShell, você poderá carregá-lo manualmente usando o `Import-Module <path to psd1>` comando.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Solucionando problemas do módulo Application Insights Agent

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>Lista os comandos disponíveis no módulo do agente de Application Insights
Execute o comando `Get-Command -Module Az.ApplicationMonitor` para obter os comandos disponíveis:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>Determinar a versão atual do módulo do agente de Application Insights
Execute o `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` comando para exibir as seguintes informações sobre o módulo:
   - Versão do módulo do PowerShell
   - Versão do SDK do Application Insights
   - Caminhos de arquivo do módulo do PowerShell
    
Examine a [referência da API](status-monitor-v2-api-reference.md) para obter uma descrição detalhada de como usar esse cmdlet.


### <a name="troubleshooting-running-processes"></a>Solucionando problemas de processos em execução

Você pode inspecionar os processos no computador instrumentado para determinar se todas as DLLs estão carregadas.
Se o monitoramento estiver funcionando, pelo menos 12 DLLs deverão ser carregadas.

Use o `Get-ApplicationInsightsMonitoringStatus -InspectProcess` comando para verificar as DLLs.

Examine a [referência da API](status-monitor-v2-api-reference.md) para obter uma descrição detalhada de como usar esse cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>Coletar logs do ETW usando PerfView

#### <a name="setup"></a>Instalação

1. Baixe PerfView.exe e PerfView64.exe do [GitHub](https://github.com/Microsoft/perfview/releases).
2. Iniciar PerfView64.exe.
3. Expanda **Opções Avançadas**.
4. Desmarque estas caixas de seleção:
    - **Rápida**
    - **Mesclagem**
    - **Coleção de símbolos .NET**
5. Defina estes **provedores adicionais**: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Coletando logs

1. Em um console de comando com privilégios de administrador, execute o `iisreset /stop` comando para desligar o IIS e todos os aplicativos Web.
2. Em PerfView, selecione **Iniciar coleta**.
3. Em um console de comando com privilégios de administrador, execute o `iisreset /start` comando para iniciar o IIS.
4. Tente navegar até seu aplicativo.
5. Depois que o aplicativo for carregado, retorne para PerfView e selecione **parar coleta**.

### <a name="how-to-capture-full-sql-command-text"></a>Como capturar texto completo do comando SQL

Para capturar o texto do comando SQL completo, você precisa modificar o arquivo de applicationinsights.config com o seguinte:

```xml
<Add Type="Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.AI.DependencyCollector">,
<EnableSqlCommandTextInstrumentation>true</EnableSqlCommandTextInstrumentation>
</Add>
```

## <a name="next-steps"></a>Próximas etapas

- Examine a [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para saber mais sobre os parâmetros que você pode ter perdido.