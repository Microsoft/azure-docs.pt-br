---
title: Migrar para o Azure firewall Premium Preview
description: Saiba como migrar do Azure firewall Standard para o Azure firewall Premium Preview.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549547"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Migrar para o Azure firewall Premium Preview

Você pode migrar o Azure firewall Standard para o Azure firewall Premium Preview para aproveitar os novos recursos premium. Para obter mais informações sobre os recursos do Azure firewall Premium Preview, consulte [recursos do Azure firewall Premium Preview](premium-features.md).

Os dois exemplos a seguir mostram como:
- Migrar uma política padrão existente usando Azure PowerShell
- Migre um firewall padrão existente (com regras clássicas) para o firewall Premium do Azure com uma política Premium.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Migrar uma política existente usando Azure PowerShell

`Transform-Policy.ps1` é um script Azure PowerShell que cria uma nova política Premium de uma política padrão existente.

Dada uma ID de política de firewall padrão, o script a transforma em uma política de firewall do Azure Premium. O script se conecta pela primeira vez à sua conta do Azure, efetua pull da política, transforma/adiciona vários parâmetros e, em seguida, carrega uma nova política Premium. A nova política Premium é denominada `<previous_policy_name>_premium` .

Exemplo de uso:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> O script não migra as configurações de inteligência contra ameaças. Você precisará anotar essas configurações antes de continuar e migrá-las manualmente.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Migrar um firewall padrão existente usando o portal do Azure

Este exemplo mostra como usar o portal do Azure para migrar um firewall padrão (regras clássicas) para o Firewall do Azure Premium com uma política Premium.

1. No portal do Azure, selecione o firewall padrão. Na página **visão geral** , selecione **migrar para a política de firewall**.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrar para a política de firewall":::

1. Na página **migrar para a política de firewall** , selecione **revisar + criar**.
1. Selecione **Criar**.

   A implantação leva alguns minutos para ser concluída.
1. Use o `Transform-Policy.ps1` [script Azure PowerShell](#migrate-an-existing-policy-using-azure-powershell) para transformar essa nova política padrão em uma política Premium.
1. No portal, selecione o recurso de firewall padrão. 
1. Em **automação**, selecione **Exportar modelo**. Mantenha esta guia do navegador aberta. Você voltará a ele mais tarde.
   > [!TIP]
   > Para garantir que você não perca o modelo, baixe e salve-o caso a guia do navegador seja fechada ou atualizada.
1. Abra uma nova guia do navegador, navegue até a portal do Azure e abra o grupo de recursos que contém o firewall.
1. Exclua a instância de firewall padrão existente.

   Isso levará alguns minutos para ser concluído.

1. Volte para a guia navegador com o modelo exportado.
1. Selecione **implantar** e, em seguida, na página **implantação personalizada** , selecione **Editar modelo**.
1. Edite o texto do modelo:
   
   1. Sob o `Microsoft.Network/azureFirewalls` recurso, em `Properties` , `sku` , altere o `tier` de "Standard" para "Premium".
   1. No modelo `Parameters` , altere `defaultValue` para o `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` de:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      para:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Clique em **Salvar**.
1. Selecione **Examinar + criar**.
1. Selecione **Criar**.

Quando a implantação for concluída, agora você poderá configurar todos os novos recursos do Azure firewall Premium Preview.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os recursos do firewall do Azure Premium](premium-features.md)