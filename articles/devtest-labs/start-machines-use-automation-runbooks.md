---
title: Iniciar computadores usando runbooks de automação no Azure DevTest Labs
description: Saiba como iniciar máquinas virtuais em um laboratório no Azure DevTest Labs usando runbooks de automação do Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 231e79d594aab7c59fa21f9ee512abaa9ac67043
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87282255"
---
# <a name="start-virtual-machines-in-a-lab-in-order-by-using-azure-automation-runbooks"></a>Iniciar máquinas virtuais em um laboratório em ordem usando runbooks de automação do Azure
O recurso de [inicialização](devtest-lab-set-lab-policy.md#set-autostart) automática do DevTest Labs permite que você configure VMs para iniciar automaticamente em um horário especificado. No entanto, esse recurso não dá suporte a computadores para iniciar em uma ordem específica. Há vários cenários em que esse tipo de automação seria útil.  Um cenário é onde uma VM Jumpbox em um laboratório precisa ser iniciada primeiro, antes das outras VMs, pois o Jumpbox é usado como o ponto de acesso para as outras VMs.  Este artigo mostra como configurar uma conta de automação do Azure com um runbook do PowerShell que executa um script. O script usa marcas em VMs no laboratório para permitir que você controle a ordem de inicialização sem precisar alterar o script.

## <a name="setup"></a>Instalação
Neste exemplo, as VMs no laboratório precisam ter a marca **StartupOrder** adicionada com o valor apropriado (0, 1, 2, etc.). Designe qualquer computador que não precise ser iniciado como-1.

## <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure
Crie uma conta de automação do Azure seguindo as instruções neste [artigo](../automation/automation-create-standalone-account.md). Escolha a opção **contas Executar como** ao criar a conta. Depois que a conta de automação for criada, abra a página **módulos** e selecione **Atualizar módulos do Azure** na barra de menus. Os módulos padrão são várias versões antigas e sem a atualização o script pode não funcionar.

## <a name="add-a-runbook"></a>Adicionar um runbook
Agora, para adicionar um runbook à conta de automação, selecione **Runbooks** no menu à esquerda. Selecione **Adicionar um runbook** no menu e siga as instruções para [criar um runbook do PowerShell](../automation/learn/automation-tutorial-runbook-textual-powershell.md).

## <a name="powershell-script"></a>Script do PowerShell
O script a seguir usa o nome da assinatura, o nome do laboratório como parâmetros. O fluxo do script é obter todas as VMs no laboratório e, em seguida, analisar as informações de marca para criar uma lista de nomes de VM e sua ordem de inicialização. O script percorre as VMs na ordem e inicia as VMs. Se houver várias VMs em um número de ordem específico, elas serão iniciadas de forma assíncrona usando trabalhos do PowerShell. Por padrão, para as VMs que não têm uma marca, defina o valor de inicialização como a última (10). elas serão iniciadas por último.  Se o laboratório não quiser que a VM seja iniciada de forma automática, defina o valor da marca como 11 e ela será ignorada.

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

## <a name="create-a-schedule"></a>Criar um agendamento
Para que esse script seja executado diariamente, [crie uma agenda](../automation/shared-resources/schedules.md#create-a-schedule) na conta de automação. Depois que o agendamento for criado, [vincule-o ao runbook](../automation/shared-resources/schedules.md#link-a-schedule-to-a-runbook). 

Em uma situação em larga escala em que há várias assinaturas com vários laboratórios, armazene as informações de parâmetro em um arquivo para diferentes laboratórios e passe o arquivo para o script em vez dos parâmetros individuais. O script precisaria ser modificado, mas a execução principal seria a mesma. Embora este exemplo use a automação do Azure para executar o script do PowerShell, há outras opções, como usar uma tarefa em um pipeline de Build/versão.

## <a name="next-steps"></a>Próximas etapas
Consulte o artigo a seguir para saber mais sobre a automação do Azure: [uma introdução à automação do Azure](../automation/automation-intro.md).
