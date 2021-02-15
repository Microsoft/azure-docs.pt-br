---
title: Adicionar um artefato a uma VM no Azure DevTest Labs | Microsoft Docs
description: Saiba como adicionar um artefato a uma máquina virtual em um laboratório no Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483883"
---
# <a name="add-an-artifact-to-a-vm"></a>Adicionar um artefato a uma VM
Ao criar uma VM, você pode adicionar artefatos existentes a ela. Esses artefatos podem ser do [repositório do git do DevTest Labs público](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ou de seu próprio repositório git. Este artigo mostra como adicionar artefatos no portal do Azure e usando Azure PowerShell. 

Os *artefatos* do Azure DevTest Labs permitem que você especifique *ações* que são executadas quando a VM é provisionada, como executar scripts do Windows PowerShell, executando comandos Bash e instalar software. Os *parâmetros* de artefato permitem que você personalize o artefato para seu cenário específico.

Para saber mais sobre como criar artefatos personalizados, consulte o artigo: [criar artefatos personalizados](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Usar o portal do Azure 
1. Entre no [portal do Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecione **Todos os Serviços** e selecione **Laboratórios de Desenvolvimento/Teste** na lista.
1. Na lista de laboratórios, selecione o laboratório contendo a VM com a qual você quer trabalhar.  
1. Selecione **Minhas máquinas virtuais**.
1. Selecione a VM desejada.
1. Selecione **Gerenciar artefatos**. 
1. Selecione **Aplicar artefatos**.
1. No painel **Aplicar artefatos**, selecione o artefato que você deseja adicionar à VM.
1. No painel **Adicionar artefatos**, insira os valores de parâmetro necessários e quaisquer parâmetros opcionais dos quais você precise.  
1. Selecione **Adicionar** para adicionar o artefato e retornar ao painel **Aplicar artefatos**.
1. Continue adicionando artefatos à sua VM conforme o necessário.
1. Após adicionar os artefatos, você pode [alterar a ordem em que eles são executados](#change-the-order-in-which-artifacts-are-run). Você pode também voltar e [exibir ou modificar um artefato](#view-or-modify-an-artifact).
1. Quando você terminar de adicionar artefatos, selecione **Aplicar**

### <a name="change-the-order-in-which-artifacts-are-run"></a>Alterar a ordem de execução dos artefatos
Por padrão, as ações dos artefatos são executadas na ordem em que eles são adicionados à VM. As etapas a seguir ilustram como alterar a ordem de execução dos artefatos.

1. Na parte superior do painel **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.
   
    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel **Artefatos selecionados**, arraste e solte os artefatos na ordem desejada. Se você enfrentar problemas para arrastar o artefato, verifique se está arrastando do lado esquerdo do artefato. 
1. Selecione **OK** quando tiver concluído.  

### <a name="view-or-modify-an-artifact"></a>exibir ou modificar um artefato
As etapas a seguir ilustram como exibir ou modificar os parâmetros de um artefato:

1. Na parte superior do painel **Aplicar artefatos**, selecione o link que indica o número de artefatos adicionados à VM.
   
    ![Número de artefatos adicionados à VM](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. No painel **Artefatos selecionados**, selecione o artefato que você deseja exibir ou editar.  
1. No painel **Adicionar artefato**, faça as alterações necessárias e selecione **OK** para fechar o painel **Adicionar artefato**.
1. Selecione **OK** para fechar o painel **Artefatos selecionados**.

## <a name="use-powershell"></a>Usar o PowerShell
O script a seguir aplica o artefato especificado à VM especificada. O comando [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) é aquele que executa a operação.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Próximas etapas
Consulte os seguintes artigos sobre artefatos:

- [Especificar artefatos obrigatórios para seu laboratório](devtest-lab-mandatory-artifacts.md)
- [Criar artefatos personalizados](devtest-lab-artifact-author.md)
- [Adicionar um repositório de artefatos a um laboratório](devtest-lab-artifact-author.md)
- [Diagnosticar falhas do artefato](devtest-lab-troubleshoot-artifact-failure.md)
