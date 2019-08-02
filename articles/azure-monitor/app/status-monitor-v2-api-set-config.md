---
title: 'Referência da API do Azure Status Monitor v2: Definir configuração | Microsoft Docs'
description: Referência da API do Status Monitor v2. Set-ApplicationInsightsMonitoringConfig. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
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
ms.openlocfilehash: f3a55caba13b3b96884d446e0750d9fb67a343df
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326287"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig"></a>API Status Monitor v2: Set-ApplicationInsightsMonitoringConfig

Este documento descreve um cmdlet que é membro do [módulo AZ. ApplicationMonitor do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>DESCRIÇÃO

Define o arquivo de configuração sem fazer uma reinstalação completa.
Reinicie o IIS para que suas alterações entrem em vigor.

> [!IMPORTANT] 
> Este cmdlet requer uma sessão do PowerShell com permissões de administrador.


## <a name="examples"></a>Exemplos

### <a name="example-with-a-single-instrumentation-key"></a>Exemplo com uma única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual serão atribuídos a uma única chave de instrumentação.

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
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** é um Regex C# necessário do computador ou nome da VM.
    - '. * ' corresponderá a todos
    - ' ComputerName ' corresponderá apenas a computadores com o nome especificado.
- **AppFilter** é um Regex C# necessário do computador ou nome da VM.
    - '. * ' corresponderá a todos
    - ' ApplicationName ' corresponderá apenas aos aplicativos IIS com o nome especificado.
- **InstrumentationKey** é necessário para habilitar o monitoramento dos aplicativos que correspondem aos dois filtros anteriores.
    - Deixe esse valor NULL se você quiser definir regras para excluir o monitoramento.


### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use essa opção para exibir logs detalhados.


## <a name="output"></a>Saída

Por padrão, nenhuma saída.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exemplo de saída detalhada da configuração do arquivo de configuração via-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exemplo de saída detalhada da configuração do arquivo de configuração via-InstrumentationKeyMap

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

## <a name="next-steps"></a>Próximas etapas

  Exiba sua telemetria:
 - [Explore](../../azure-monitor/app/metrics-explorer.md) as métricas para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use a análise](../../azure-monitor/app/analytics.md) para consultas mais avançadas.
- [Crie painéis](../../azure-monitor/app/overview-dashboard.md).
 
 Adicione mais telemetria:
 - [Crie testes da Web](monitor-web-app-availability.md) para garantir que seu site permaneça ativo.
- [Adicione telemetria de cliente Web](../../azure-monitor/app/javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir chamadas de rastreamento e log
 
 Faça mais com Status Monitor v2:
 - Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
 - [Obtenha a configuração](status-monitor-v2-api-get-config.md) para confirmar que as configurações foram registradas corretamente.
 - [Obtenha o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
