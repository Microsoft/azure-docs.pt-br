---
title: Criar uma maquina virtual e uma conta de armazenamento para um aplicativo escalonável no Azure | Microsoft Docs
description: Saiba como implantar uma máquina virtual para executar um aplicativo escalonável usando o armazenamento de blobs do Azure
author: roygara
ms.service: storage
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.subservice: blobs
ms.openlocfilehash: 49689585b3f52bbee300ff68d2c2653f5e7ff8d0
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699075"
---
# <a name="create-a-virtual-machine-and-storage-account-for-a-scalable-application"></a>Crie uma máquina virtual e uma conta de armazenamento para um aplicativo escalonável

Este tutorial é a primeira parte de uma série. Este tutorial mostra como implantar um aplicativo que carrega e baixa grandes quantidades de dados aleatórios com uma conta de armazenamento do Azure. Quando você terminar, terá um aplicativo de console em execução em uma máquina virtual em que você carrega e baixa grandes quantidades de dados para uma conta de armazenamento.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar uma máquina virtual
> * Configurar uma extensão de script personalizado

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se você decidir instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão Az 0.7 ou posterior. Execute `Get-Module -ListAvailable Az` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-Az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
 
O exemplo carrega 50 arquivos grandes em um contêiner de blob em uma conta de Armazenamento do Azure. Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure. Crie uma conta de armazenamento no grupo de recursos que você criou usando o comando [New-AzStorageAccount](/powershell/module/az.Storage/New-azStorageAccount).

No comando a seguir, substitua seu próprio nome global exclusivo da conta de armazenamento de blobs quando o espaço reservado `<blob_storage_account>` for exibido.

```powershell-interactive
$storageAccount = New-AzStorageAccount -ResourceGroupName myResourceGroup `
  -Name "<blob_storage_account>" `
  -Location EastUS `
  -SkuName Standard_LRS `
  -Kind Storage `
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma configuração de máquina virtual. Essa configuração inclui as configurações que são usadas ao implantar a máquina virtual como uma imagem, tamanho e configuração de autenticação de máquina virtual. Ao executar esta etapa, credenciais serão solicitadas de você. Os valores que você insere são configurados como o nome de usuário e senha para a máquina virtual.

Crie a máquina virtual com [New-AzVM](/powershell/module/az.compute/new-azvm).

```azurepowershell-interactive
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "eastus"
$vmName = "myVM"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create a virtual network card and associate with public IP address
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig -VMName myVM -VMSize Standard_DS14_v2 | `
    Set-AzVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
    Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig

Write-host "Your public IP address is $($pip.IpAddress)"
```

## <a name="deploy-configuration"></a>Implantar configuração

Para este tutorial, há pré-requisitos que devem ser instalados na máquina virtual. A extensão do script personalizado é usado para executar um script do PowerShell que conclui as seguintes tarefas:

> [!div class="checklist"]
> * Instalar o .NET Core 2.0
> * Instalar chocolatey
> * Instalar GIT
> * Clonar o repositório de exemplo
> * Restaurar pacotes NuGet
> * Criar 50 arquivos de 1 GB cada com dados aleatórios

Execute o seguinte cmdlet para finalizar a configuração da máquina virtual. Esta etapa leva de 5 a 15 minutos para ser concluída.

```azurepowershell-interactive
# Start a CustomScript extension to use a simple PowerShell script to install .NET core, dependencies, and pre-create the files to upload.
Set-AzVMCustomScriptExtension -ResourceGroupName myResourceGroup `
    -VMName myVM `
    -Location EastUS `
    -FileUri https://raw.githubusercontent.com/azure-samples/storage-dotnet-perf-scale-app/master/setup_env.ps1 `
    -Run 'setup_env.ps1' `
    -Name DemoScriptExtension
```

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu como criar uma conta de armazenamento, implantar uma máquina virtual e configurar a máquina virtual com os pré-requisitos necessários, como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Criar uma máquina virtual
> * Configurar uma extensão de script personalizado

Vá para a parte dois da série para carregar grandes quantidades de dados para uma conta de armazenamento usando repetição exponencial e paralelismo.

> [!div class="nextstepaction"]
> [Carregar grandes quantidades de arquivos grandes em paralelo para uma conta de armazenamento](storage-blob-scalable-app-upload-files.md)
