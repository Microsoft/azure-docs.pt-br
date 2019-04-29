---
title: Iniciar máquinas usando runbooks de automação no Azure DevTest Labs | Microsoft Docs
description: Saiba como iniciar as máquinas virtuais em um laboratório no Azure DevTest Labs usando runbooks de automação do Azure.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.openlocfilehash: 8d3885ba25e479316f97ecbb0681a1680650fc09
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61083611"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Iniciar máquinas virtuais em um laboratório na ordem por meio de runbooks de automação do Azure
O [autostart](devtest-lab-set-lab-policy.md#set-autostart) recurso do DevTest Labs permite que você configure as VMs para iniciar automaticamente em um horário especificado. No entanto, esse recurso não oferece suporte a máquinas para iniciar em uma ordem específica. Há vários cenários em que esse tipo de automação seria útil.  Um cenário é onde uma VM Jumpbox em um laboratório precisa ser iniciado pela primeira vez, antes de outras VMs, como o Jumpbox é usado como o ponto de acesso às outras VMs.  Este artigo mostra como configurar uma conta de automação do Azure com um runbook do PowerShell que execute um script. O script usa marcas nas VMs no laboratório para permitir que você controle a ordem de inicialização sem ter que alterar o script.

## <a name="setup"></a>Configuração
Neste exemplo, VMs do laboratório precisam ter a marca **StartupOrder** adicionada com o valor apropriado (0,1,2, etc.). Designe qualquer computador que não precisa ser iniciado como -1.

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure
Criar uma conta de automação do Azure seguindo as instruções em [deste artigo](../automation/automation-create-standalone-account.md). Escolha o **contas executar como** opção ao criar a conta. Depois que a conta de automação é criada, abra o **módulos** da página e selecione **atualizar módulos do Azure** na barra de menus. Os módulos padrão são que várias versões antigas e sem a atualização, o script podem não funcionar.

## <a name="add-a-runbook"></a>Adicionar um runbook
Agora, para adicionar um runbook para a conta de automação, selecione **Runbooks** no menu à esquerda. Selecione **adicionar um runbook** no menu e siga as instruções para [criar um runbook do PowerShell](../automation/automation-first-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script do PowerShell
O script a seguir usa o nome da assinatura, o nome do laboratório como parâmetros. O fluxo do script é obter todas as VMs no laboratório e, em seguida, analisar as informações de marca para criar uma lista de nomes de VM e sua ordem de inicialização. O script percorre as VMs na ordem e inicia as VMs. Se houver várias VMs em um número de ordem específica, eles são iniciados de forma assíncrona usando trabalhos do PowerShell. Para essas VMs que não têm uma marca, definir o valor de inicialização para ser o último (10), ele serão iniciados últimos, por padrão.  Se o laboratório não deseja que a máquina virtual seja iniciado automaticamente, defina o valor de marca para 11 e ele será ignorado.

```powershell
#Requires -Version 3.0
#Requires -Module AzureRM.Resources

param
(
    [Parameter(Mandatory=$false, HelpMessage="Name of the subscription that has the lab")]
    [string] $SubscriptionName,

    [Parameter(Mandatory=$false, HelpMessage="Lab name")]
    [string] $LabName
)

# Connect and add the appropriate subscription
$Conn = Get-AutomationConnection -Name AzureRunAsConnection

Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationId -Subscription $SubscriptionName -CertificateThumbprint $Conn.CertificateThumbprint

# Find the lab
$dtLab = Find-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceNameEquals $LabName

# Get the VMs
$dtlAllVms = New-Object System.Collections.ArrayList
$AllVMs = Get-AzResource -ResourceId "$($dtLab.ResourceId)/virtualmachines" -ApiVersion 2016-05-15

# Get the StartupOrder tag, if missing set to be run last (10)
ForEach ($vm in $AllVMs) {
    if ($vm.Tags) {
        if ($vm.Tags['StartupOrder']) {
            $startupValue = $vm.Tags['StartupOrder']
        } else {
            $startupValue = 10
        }
        } else {
            $startupValue = 10
        }
        $dtlAllVms.Add(@{$vm.Name = $startupValue}) > $null
}

# Setup for the async multiple vm start

# Save profile
$profilePath = Join-Path $env:Temp "profile.json"
If (Test-Path $profilePath){
    Remove-Item $profilePath
}
Save-AzContext -Path $profilePath

# Job to start VMs asynch
$startVMBlock = {
    Param($devTestLab,$vmToStart,$profilePath)
    Import-AzContext -Path ($profilePath)
    Invoke-AzResourceAction `
        -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vmToStart" `
        -Action Start `
        -Force
    Write-Output "Started: $vmToStart"
}

$current = 0
# Start in order from 0 to 10

While ($current -le 10) {
# Get the VMs in the current stage
    $tobeStarted = $null
    $tobeStarted = $dtlAllVms | Where-Object { $_.Values -eq $current}
    if ($tobeStarted.Count -eq 1) {
        # Run sync – jobs not necessary for a single VM
        $returnStatus = Invoke-AzResourceAction `
                -ResourceId "$($dtLab.ResourceId)/virtualmachines/$($tobeStarted.Keys)" `
                -Action Start `
                -Force
        Write-Output "$($tobeStarted.Keys) status: $($returnStatus.status)"
    } elseif ($tobeStarted.Count -gt 1) {
        # Start multiple VMs async
        $jobs = @()
        Write-Output "Start Jobs start: $(Get-Date)"
        
        # Jobs
        $jobs += Start-Job -ScriptBlock $startVMBlock -ArgumentList $dtLab, $($singlevm.Keys), $profilePath
        Write-Output "Start Jobs end: $(Get-Date)"
    }

    # Get results from all jobs
    if($jobs.Count -ne 0) {
        Write-Output "Receive Jobs start: $(Get-Date)"
        foreach ($job in $jobs){
            $jobResult = Receive-Job -Job $job -Wait | Write-Output
        }
        Remove-Job -Job $jobs -Force
    }
    else
    {
        Write-Output "Information: No jobs available"
    }
}
```

## <a name="create-a-schedule"></a>Criar uma agenda
Para ter esse script executar diariamente [criar uma agenda](../automation/shared-resources/schedules.md#creating-a-schedule) na conta de automação. Depois que a agenda é criada, [vinculá-la ao runbook](../automation/shared-resources/schedules.md#linking-a-schedule-to-a-runbook). 

Em uma situação em grande escala em que há várias assinaturas com vários laboratórios, armazenar as informações de parâmetro em um arquivo para laboratórios diferentes e passar o arquivo para o script em vez dos parâmetros individuais. O script precisa ser modificada, mas a execução de núcleo seria o mesmo. Embora este exemplo usa a automação do Azure para executar o script do PowerShell, há outras opções, como usar uma tarefa em um pipeline de Build/versão.

## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte artigo para saber mais sobre a automação do Azure: [Uma introdução à automação do Azure](../automation/automation-intro.md).
