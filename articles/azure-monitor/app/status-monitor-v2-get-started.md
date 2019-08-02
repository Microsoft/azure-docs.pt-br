---
title: Azure Status Monitor v2-introdução | Microsoft Docs
description: Um guia de início rápido para o Status Monitor v2. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
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
ms.openlocfilehash: a0c836c8ef947e190a0090b3435eec1c53ded436
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326253"
---
# <a name="get-started-with-status-monitor-v2"></a>Introdução ao Status Monitor v2

Este artigo contém os comandos de início rápido esperados para funcionar para a maioria dos ambientes.
As instruções dependem do Galeria do PowerShell para distribuir atualizações.
Esses comandos oferecem suporte ao `-Proxy` parâmetro do PowerShell.

Para obter uma explicação desses comandos, instruções de personalização e informações sobre solução de problemas, consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="download-and-install-via-powershell-gallery"></a>Baixar e instalar via Galeria do PowerShell

### <a name="install-prerequisites"></a>Instalar pré-requisitos
Execute o PowerShell como administrador.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Feche o PowerShell.

### <a name="install-status-monitor-v2"></a>Instalar o Status Monitor v2
Execute o PowerShell como administrador.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Habilitar o monitoramento
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Baixar e instalar manualmente (opção offline)
### <a name="download-the-module"></a>Baixar o módulo
Baixe manualmente a versão mais recente do módulo em [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-status-monitor-v2"></a>Descompactar e instalar o Status Monitor v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Habilitar o monitoramento
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

- Examine as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para obter uma explicação dos comandos encontrados aqui.
- Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) status monitor v2.
