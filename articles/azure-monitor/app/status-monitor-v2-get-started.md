---
title: Agente do insights Aplicativo Azure-introdução | Microsoft Docs
description: Um guia de início rápido para o Application Insights Agent. Monitore o desempenho do site sem reimplantar o site. Funciona com aplicativos Web ASP.NET hospedados localmente, em VMs ou no Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 01/22/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 21184e1623fd47e8367d4c5dfbc2c85debe93124
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587390"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Introdução ao agente de Application Insights de Azure Monitor para servidores locais

Este artigo contém os comandos de início rápido esperados para funcionar para a maioria dos ambientes.
As instruções dependem do Galeria do PowerShell para distribuir atualizações.
Esses comandos oferecem suporte ao `-Proxy` parâmetro do PowerShell.

Para obter uma explicação desses comandos, instruções de personalização e informações sobre solução de problemas, consulte as [instruções detalhadas](status-monitor-v2-detailed-instructions.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="download-and-install-via-powershell-gallery"></a>Baixar e instalar via Galeria do PowerShell

### <a name="install-prerequisites"></a>Instalar pré-requisitos

> [!NOTE]
> A partir de abril de 2020, o Galeria do PowerShell preteriu o TLS 1,1 e 1,0.
>
> Para os pré-requisitos do additionnal que podem ser necessários, consulte [suporte a galeria do PowerShell TLS](https://devblogs.microsoft.com/powershell/powershell-gallery-tls-support).
>

Execute o PowerShell como administrador.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Feche o PowerShell.

### <a name="install-application-insights-agent"></a>Instalar o agente de Application Insights
Execute o PowerShell como administrador.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Habilitar o monitoramento
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>Baixar e instalar manualmente (opção offline)
### <a name="download-the-module"></a>Baixar o módulo
Baixe manualmente a versão mais recente do módulo em [Galeria do PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).

### <a name="unzip-and-install-application-insights-agent"></a>Descompactar e instalar Application Insights agente
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Habilitar o monitoramento
```powershell
Enable-ApplicationInsightsMonitoring -ConnectionString xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>Próximas etapas

 Exiba sua telemetria:

- [Explore as métricas](../essentials/metrics-charts.md) para monitorar o desempenho e o uso.
- [Pesquise eventos e logs](./diagnostic-search.md) para diagnosticar problemas.
- [Use a análise](../logs/log-query-overview.md) para consultas mais avançadas.
- [Crie painéis](./overview-dashboard.md).

 Adicione mais telemetria:

- [Crie testes na Web](monitor-web-app-availability.md) para ter a certeza de que seu site continua ativo.
- [Adicione telemetria de cliente Web](./javascript.md) para ver exceções do código de página da Web e para habilitar chamadas de rastreamento.
- [Adicione o SDK do Application insights ao seu código](./asp-net.md) para que você possa inserir chamadas de rastreamento e log.

Faça mais com Application Insights agente:

- Examine as [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para obter uma explicação dos comandos encontrados aqui.
- Use nosso guia para [solucionar problemas](status-monitor-v2-troubleshoot.md) do Application insights Agent.

