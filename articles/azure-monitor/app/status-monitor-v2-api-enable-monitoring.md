---
title: 'Referência da API do Azure Status Monitor v2: Habilitar monitoramento | Microsoft Docs'
description: Referência da API do Status Monitor v2. Enable-ApplicationInsightsMonitoring. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
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
ms.openlocfilehash: d3963889e3604fb67cb526b992e7ca27b1212b59
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326354"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring"></a>API Status Monitor v2: Habilitar-ApplicationInsightsMonitoring

Este artigo descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>DESCRIÇÃO

Habilita o monitoramento de anexação de código de aplicativos IIS em um computador de destino.

Esse cmdlet modificará o IIS applicationHost. config e definirá algumas chaves do registro.
Ele também criará um arquivo applicationinsights. iKey. config, que define a chave de instrumentação usada por cada aplicativo.
O IIS carregará o RedfieldModule na inicialização, que injetará o SDK Application Insights em aplicativos à medida que os aplicativos forem iniciados.
Reinicie o IIS para que suas alterações entrem em vigor.

Depois de habilitar o monitoramento, recomendamos que você use métricas em [tempo real](live-stream.md) para verificar rapidamente se seu aplicativo está enviando telemetria dos EUA.


> [!NOTE] 
> - Para começar, você precisa de uma chave de instrumentação. Para obter mais informações, consulte [criar um recurso](create-new-resource.md#copy-the-instrumentation-key).
> - Esse cmdlet requer que você examine e aceite nossa licença e nossa política de privacidade.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador e uma política de execução elevada. Para obter mais informações, consulte [executar o PowerShell como administrador com uma política de execução elevada](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual recebem uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Exemplo com um mapa de chave de instrumentação
Neste exemplo:
- `MachineFilter`corresponde ao computador atual usando o `'.*'` caractere curinga.
- `AppFilter='WebAppExclude'`fornece uma `null` chave de instrumentação. O aplicativo especificado não será instrumentado.
- `AppFilter='WebAppOne'`atribui ao aplicativo especificado uma chave de instrumentação exclusiva.
- `AppFilter='WebAppTwo'`atribui ao aplicativo especificado uma chave de instrumentação exclusiva.
- Por fim `AppFilter` , o também `'.*'` usa o curinga para corresponder a todos os aplicativos Web que não são compatíveis com as regras anteriores e atribuir uma chave de instrumentação padrão.
- Os espaços são adicionados para facilitar a leitura.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>parâmetros

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obrigatório.** Use esse parâmetro para fornecer uma chave de instrumentação única para uso por todos os aplicativos no computador de destino.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obrigatório.** Use esse parâmetro para fornecer várias chaves de instrumentação e um mapeamento das chaves de instrumentação usadas por cada aplicativo.
Você pode criar um único script de instalação para vários computadores definindo `MachineFilter`.

> [!IMPORTANT]
> Os aplicativos serão compatíveis com as regras na ordem em que as regras são fornecidas. Portanto, você deve especificar as regras mais específicas primeiro e as regras mais genéricas por último.

#### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** é um Regex C# necessário do computador ou nome da VM.
    - '. * ' corresponderá a todos
    - ' ComputerName ' corresponderá apenas a computadores com o nome exato especificado.
- **AppFilter** é um Regex C# necessário do nome do site do IIS. Você pode obter uma lista de sites em seu servidor executando o comando [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite).
    - '. * ' corresponderá a todos
    - ' SiteName ' corresponderá apenas ao site do IIS com o nome exato especificado.
- **InstrumentationKey** é necessário para habilitar o monitoramento de aplicativos que correspondem aos dois filtros anteriores.
    - Deixe esse valor NULL se você quiser definir regras para excluir o monitoramento.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcional** Use essa opção para habilitar o mecanismo de instrumentação para coletar eventos e mensagens sobre o que está acontecendo durante a execução de um processo gerenciado. Esses eventos e mensagens incluem códigos de resultado de dependência, verbos HTTP e texto de comando SQL.

O mecanismo de instrumentação adiciona sobrecarga e está desativado por padrão.

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional** Use essa opção para aceitar a licença e a política de privacidade em instalações sem periféricos.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Quando você tem um cluster de servidores Web, você pode estar usando uma [configuração compartilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
O HttpModule não pode ser injetado nessa configuração compartilhada.
Esse script falhará com a mensagem informando que as etapas de instalação extras são necessárias.
Use essa opção para ignorar essa verificação e continuar instalando os pré-requisitos. Para obter mais informações, consulte [conflito conhecido-com a configuração compartilhada do IIS](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para exibir logs detalhados.

### <a name="-whatif"></a>-WhatIf 
**Parâmetro comum.** Use essa opção para testar e validar os parâmetros de entrada sem realmente habilitar o monitoramento.

## <a name="output"></a>Saída


#### <a name="example-output-from-a-successful-enablement"></a>Exemplo de saída de uma habilitação bem-sucedida

```
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

## <a name="next-steps"></a>Próximas etapas

  Exiba sua telemetria:
 - [Explore](../../azure-monitor/app/metrics-explorer.md) as métricas para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use a análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Crie painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicione mais telemetria:
 - [Crie testes da Web](monitor-web-app-availability.md) para garantir que seu site permaneça ativo.
- [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log.
 
 Faça mais com Status Monitor v2:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
 - [Obtenha a configuração](status-monitor-v2-api-get-config.md) para confirmar que as configurações foram registradas corretamente.
 - [Obtenha o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
