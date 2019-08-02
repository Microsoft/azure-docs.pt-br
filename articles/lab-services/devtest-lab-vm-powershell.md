---
title: Criar uma máquina virtual no DevTest Labs com o Azure PowerShell | Microsoft Docs
description: Saiba como usar Azure DevTest Labs para criar e gerenciar máquinas virtuais com Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 1a6938bd541e316dbe9f333c670c382faab6ad21
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854257"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Criar uma máquina virtual com o DevTest Labs usando o Azure PowerShell
Este artigo mostra como criar uma máquina virtual no Azure DevTest Labs usando Azure PowerShell. Você pode usar scripts do PowerShell para automatizar a criação de máquinas virtuais em um laboratório no Azure DevTest Labs. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

- [Crie um laboratório](devtest-lab-create-lab.md) se você não quiser usar um laboratório existente para testar o script ou os comandos neste artigo. 
- [Instale o Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) ou use o Azure cloud Shell integrado ao portal do Azure. 

## <a name="powershell-script"></a>Script do PowerShell
O script de exemplo nesta seção usa o cmdlet [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) .  Esse cmdlet usa a ID de recurso do laboratório, o nome da ação a ser`createEnvironment`executada () e os parâmetros necessários para executar essa ação. Os parâmetros estão em uma tabela de hash que contém todas as propriedades de descrição da máquina virtual. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

As propriedades da máquina virtual no script acima nos permitem criar uma máquina virtual com o Windows Server 2016 datacenter como o sistema operacional. Para cada tipo de máquina virtual, essas propriedades serão um pouco diferentes. A seção [definir máquina virtual](#define-virtual-machine) mostra como determinar quais propriedades usar nesse script.

O comando a seguir fornece um exemplo de execução do script salvo em um nome de arquivo: Create-LabVirtualMachine. ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definir máquina virtual
Esta seção mostra como obter as propriedades que são específicas para um tipo de máquina virtual que você deseja criar. 

### <a name="use-azure-portal"></a>Usar o portal do Azure
Você pode gerar um modelo de Azure Resource Manager ao criar uma VM no portal do Azure. Você não precisa concluir o processo de criação da VM. Você só seguirá as etapas até ver o modelo. Essa é a melhor maneira de obter a descrição de JSON necessária se você ainda não tiver uma VM de laboratório criada. 

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu de navegação à esquerda.
3. Pesquise e selecione **DevTest Labs** na lista de serviços. 
4. Na página do **DevTest Labs** , selecione seu laboratório na lista de laboratórios.
5. Na home page para seu laboratório, selecione **+ Adicionar** na barra de ferramentas. 
6. Selecione uma **imagem base** para a VM. 
7. Selecione **Opções de automação** na parte inferior da página acima do botão **Enviar** . 
8. Você verá o **modelo de Azure Resource Manager** para criar a máquina virtual. 
9. O segmento JSON na seção de **recursos** tem a definição para o tipo de imagem que você selecionou anteriormente. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

Neste exemplo, você verá como obter uma definição de uma imagem do Azure Market Place. Você pode obter uma definição de uma imagem personalizada, uma fórmula ou um ambiente da mesma maneira. Adicione todos os artefatos necessários para a máquina virtual e defina as configurações avançadas necessárias. Depois de fornecer valores para os campos obrigatórios e quaisquer campos opcionais, antes de selecionar o botão **Opções de automação** .

### <a name="use-azure-rest-api"></a>Usar a API REST do Azure
O procedimento a seguir fornece as etapas para obter as propriedades de uma imagem usando a API REST: Essas etapas funcionam apenas para uma VM existente em um laboratório. 

1. Navegue até a página [máquinas virtuais – lista](/rest/api/dtl/virtualmachines/list) , selecione o botão **experimentar** . 
2. Selecione sua **assinatura do Azure**.
3. Insira o **grupo de recursos para o laboratório**.
4. Insira o **nome do laboratório**. 
5. Selecione **Executar**.
6. Você vê as **Propriedades da imagem** com base na qual a VM foi criada. 

## <a name="set-expiration-date"></a>Definir a data de validade
Em cenários como treinamento, demonstrações e avaliações, talvez você queira criar máquinas virtuais e excluí-las automaticamente após uma duração fixa para que não incorra em custos desnecessários. Você pode definir uma data de expiração para uma VM ao criá-la usando o PowerShell, conforme mostrado na seção exemplo de [script do PowerShell](#powershell-script) .

Aqui está um exemplo de script do PowerShell que define a data de expiração de todas as VMs existentes em um laboratório:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte conteúdo: [Azure PowerShell documentação para Azure DevTest Labs](/powershell/module/az.devtestlabs/)
