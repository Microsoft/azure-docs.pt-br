---
title: Criar uma máquina virtual nos laboratórios de desenvolvimento/teste com o Azure PowerShell | Microsoft Docs
description: Saiba como usar o Azure DevTest Labs para criar e gerenciar máquinas virtuais com o Azure PowerShell.
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
ms.openlocfilehash: a9629cd14c71a163612c2c4ba3c7b109a52b91ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622432"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Criar uma máquina virtual com o DevTest Labs usando o Azure PowerShell
Este artigo mostra como criar uma máquina virtual no Azure DevTest Labs usando o PowerShell do Azure. Você pode usar scripts do PowerShell para automatizar a criação de máquinas virtuais em um laboratório no Azure DevTest Labs. 

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar:

- [Criar um laboratório](devtest-lab-create-lab.md) se você não quiser usar um laboratório existente para testar o script ou os comandos neste artigo. 
- [Instalar o Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) ou usar o Azure Cloud Shell que é integrado ao portal do Azure. 

## <a name="powershell-script"></a>Script do PowerShell
O script de exemplo nesta seção usa o [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0) cmdlet.  Esse cmdlet usa a ID do recurso do laboratório, nome da ação para executar (`createEnvironment`), e os parâmetros necessários executam essa ação. Os parâmetros estão em uma tabela de hash que contém todas as propriedades de descrição da máquina virtual. 

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

As propriedades para a máquina virtual no script acima nos permitem criar uma máquina virtual com o Windows Server 2016 DataCenter como o sistema operacional. Para cada tipo de máquina virtual, essas propriedades será um pouco diferentes. O [definir máquina virtual](#define-virtual-machine) seção mostra como determinar quais propriedades a serem usadas neste script.

O comando a seguir fornece um exemplo da execução do script salvo em um nome de arquivo: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Definir a máquina virtual
Esta seção mostra como obter as propriedades que são específicas para um tipo de máquina virtual que você deseja criar. 

### <a name="use-azure-portal"></a>Usar o portal do Azure
Você pode gerar um modelo do Azure Resource Manager ao criar uma VM no portal do Azure. Você não precisa concluir o processo de criação da VM. Você apenas siga as etapas até que você vê o modelo. Isso é a melhor maneira de obter a descrição de JSON necessário se você ainda não tiver um laboratório de que VM criada. 

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Selecione **todos os serviços** no menu de navegação à esquerda.
3. Pesquise e selecione **DevTest Labs** na lista de serviços. 
4. Sobre o **DevTest Labs** , selecione seu laboratório na lista de laboratórios.
5. Na home page para seu laboratório, selecione **+ adicionar** na barra de ferramentas. 
6. Selecione uma **imagem base** para a VM. 
7. Selecione **opções de automação** na parte inferior da página acima a **enviar** botão. 
8. Você vê o **modelo do Resource Manager** para criar a máquina virtual. 
9. O segmento JSON na **recursos** seção tem a definição para o tipo de imagem que você selecionou anteriormente. 

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

Neste exemplo, você verá como obter uma definição de uma imagem de mercado do Azure. Você pode obter uma definição de uma imagem personalizada, uma fórmula ou um ambiente da mesma maneira. Adicione quaisquer artefatos necessários para a máquina virtual e defina as configurações avançadas exigidas. Depois de fornecer valores para os campos necessários e quaisquer campos opcionais, antes selecionando o **opções de automação** botão.

### <a name="use-azure-rest-api"></a>Usar a API REST do Azure
O procedimento a seguir fornece as etapas para obter as propriedades de uma imagem usando a API REST: Estas etapas funcionam apenas para uma VM existente em um laboratório. 

1. Navegue até a [máquinas virtuais – listar](/rest/api/dtl/virtualmachines/list) página, selecione **Experimente** botão. 
2. Selecione sua **assinatura do Azure**.
3. Insira o **grupo de recursos para o laboratório**.
4. Insira o **nome do laboratório**. 
5. Selecione **Executar**.
6. Você vê o **propriedades da imagem de** com base no qual a VM foi criada. 



## <a name="next-steps"></a>Próximas etapas
Consulte o seguinte conteúdo: [Documentação do PowerShell do Azure para o Azure DevTest Labs](/powershell/module/az.devtestlabs/)
