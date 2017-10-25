---
title: "Criar uma Máquina virtual do SQL Server no Azure PowerShell (Resource Manager) | Microsoft Docs"
description: "Fornece etapas e scripts do PowerShell para criar uma VM do Azure com imagens da galeria de máquinas virtuais do SQL Server."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/29/2017
ms.author: jroth
ms.openlocfilehash: 4b8cc80f2d1ed6f09ec917118dc9495d20394b94
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-resource-manager"></a>Provisionar uma máquina virtual SQL Server usando o Azure PowerShell (Gerenciador de Recursos)
> [!div class="op_single_selector"]
> * [Portal](virtual-machines-windows-portal-sql-server-provision.md)
> * [PowerShell](virtual-machines-windows-ps-sql-create.md)
>
>

## <a name="overview"></a>Visão geral
Este tutorial mostra como criar uma única máquina virtual do Azure usando o modelo de implantação do **Azure Resource Manager** usando os cmdlets do Azure PowerShell. Neste tutorial, criaremos uma única máquina virtual usando uma única unidade de disco de uma imagem na Galeria de SQL. Vamos criar novos provedores de armazenamento, rede e recursos de computação que serão usados pela máquina virtual. Se você tiver provedores existentes para qualquer um desses recursos, poderá usar tais provedores.

Se você precisar da versão clássica deste tópico, confira [Provisionar uma máquina virtual do SQL Server usando o Azure PowerShell Clássico](../classic/ps-sql-create.md).

## <a name="prerequisites"></a>Pré-requisitos
Para este tutorial, será necessário:

* Uma conta do Azure e uma assinatura antes de começar. Se não tiver uma, inscreva-se em uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [Azure PowerShell](/powershell/azure/overview), versão mínima do 1.4.0 ou posterior (este tutorial foi escrito usando a versão 1.5.0).
  * Para recuperar a versão, digite **Get-Module Azure -ListAvailable**.

## <a name="configure-your-subscription"></a>Configurar sua assinatura
Abra o Windows PowerShell e estabeleça o acesso à sua conta do Azure ao executar o cmdlet a seguir. Você encontrará uma tela de para se conectar e inserir suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

```PowerShell
Add-AzureRmAccount
```

Depois de se conectar com êxito, você verá algumas informações na tela, incluindo o Nome e ID da Assinatura padrão. Esta é a assinatura em que os recursos para este tutorial serão criados, a menos que você altere para uma assinatura diferente. Se você tiver várias assinaturas, execute o seguinte cmdlet para retornar uma lista de todas as suas assinaturas:

```PowerShell
Get-AzureRmSubscription
```
Para alterar para outra assinatura, execute o seguinte comando com seu SubscriptionName desejado.

```PowerShell
Select-AzureRmSubscription -SubscriptionName YourTargetSubscriptionName
```

## <a name="define-image-variables"></a>Definir variáveis de imagem
Para simplificar a usabilidade e a compreensão do script concluído deste tutorial, vamos começar definindo um número de variáveis. Altere os valores de parâmetro como achar melhor, mas lembre-se de restrições de nomenclatura relacionadas a tamanhos de nome e caracteres especiais ao modificar os valores fornecidos.

### <a name="location-and-resource-group"></a>Local e Grupo de Recursos
Use duas variáveis para definir a região de dados e o grupo de recursos no qual você criará outros recursos para a máquina virtual.

Modifique conforme desejado e, em seguida, execute os seguintes cmdlets para inicializar essas variáveis.

```PowerShell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"
```

### <a name="storage-properties"></a>Propriedades de armazenamento
Use as seguintes variáveis para definir a conta de armazenamento e o tipo de armazenamento a ser usado pela máquina virtual.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis. Observe que, neste exemplo estamos usando o [Armazenamento Premium](../../../storage/common/storage-premium-storage.md), que é recomendado para cargas de trabalho de produção. Para obter detalhes sobre essas diretrizes e outras recomendações, confira [Melhores práticas para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-performance.md).

```PowerShell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Propriedades da rede
Use as seguintes variáveis para definir a interface de rede, o método de alocação de TCP/IP, o nome da rede virtual, o nome da sub-rede virtual, o intervalo de endereços IP para a rede virtual, o intervalo de endereços IP para a sub-rede e o rótulo de nome de domínio público a ser usado pela rede na máquina virtual.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

```PowerShell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = "sqlvm1"
```

### <a name="virtual-machine-properties"></a>Propriedades de máquina virtual
Use as seguintes variáveis para definir o nome da máquina virtual, o nome do computador, o tamanho da máquina virtual e o nome do disco do sistema operacional da máquina virtual.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

```PowerShell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="image-properties"></a>Propriedades da imagem
Use as seguintes variáveis para definir a imagem a ser usada para a máquina virtual. Neste exemplo, a imagem de edição do SQL Server 2016 Developer é usada. A edição Developer é licenciada livremente para teste e desenvolvimento, e você só paga o custo da execução da VM.

Modifique conforme desejado e, em seguida, execute o seguinte cmdlet para inicializar essas variáveis.

```PowerShell
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "SQLDEV"
$Version = "latest"
```

Observe que você pode obter uma lista completa das ofertas de imagem do SQL Server com o comando Get-AzureRmVMImageOffer:

```PowerShell
Get-AzureRmVMImageOffer -Location 'East US' -Publisher 'MicrosoftSQLServer'
```

E você pode ver as SKUs disponíveis para uma oferta com o comando Get-AzureRmVMImageSku. O comando a seguir mostra todas as SKUs disponíveis para a oferta **SQL2016SP1-WS2016** .

```PowerShell
Get-AzureRmVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer 'SQL2016SP1-WS2016' | Select Skus
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Com o modelo de implantação do Gerenciador de Recursos, o primeiro objeto criado por você é o grupo de recursos. Usaremos o cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) para criar um grupo de recursos do Azure e seus recursos com o nome do grupo de recursos e localização definidos pelas variáveis que você inicializou anteriormente.

Execute o seguinte cmdlet para criar novo grupo de recursos.

```PowerShell
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento
A máquina virtual exige recursos de armazenamento para o disco do sistema operacional para os arquivos de log de dados do SQL Server. Para simplificar, criaremos um único disco para ambos. Você pode anexar discos adicionais posteriormente usando o cmdlet [Adicionar Disco do Azure](/powershell/module/azure/add-azuredisk) para colocar seus dados do SQL Server e o log de arquivos em discos dedicados. Usaremos o cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) para criar uma conta de armazenamento standard em seu novo grupo de recursos e com o nome da conta de armazenamento, um nome de SKU de armazenamento e uma localização definida usando as variáveis inicializadas anteriormente.

Execute o cmdlet a seguir para criar sua nova conta de armazenamento.

```PowerShell
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location
```

## <a name="create-network-resources"></a>Criar recursos da rede
A máquina virtual requer um número de recursos de rede para conectividade de rede.

* Cada máquina virtual exige um rede virtual.
* Uma rede virtual deve ter pelo menos uma sub-rede definida.
* Uma interface de rede deve ser definida com um público ou um endereço IP privado.

### <a name="create-a-virtual-network-subnet-configuration"></a>Criar uma configuração de sub-rede da rede virtual
Vamos começar criando uma configuração de sub-rede para nossa rede virtual. Para nosso tutorial, criaremos uma sub-rede padrão usando o cmdlet [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) . Criaremos nossa configuração de sub-rede da rede virtual com o prefixo de nome e endereço de sub-rede definido usando as variáveis inicializadas anteriormente.

> [!NOTE]
> Você pode definir propriedades adicionais da configuração de sub-rede da rede virtual usando esse cmdlet, mas isso está além do escopo deste tutorial.

Execute o seguinte cmdlet para criar a configuração da sub-rede virtual.

```PowerShell
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Em seguida, criaremos nossa rede virtual usando o cmdlet [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) . Criaremos nossa rede virtual no novo grupo de recursos, com o nome, local e endereço de prefixo definido usando as variáveis inicializadas anteriormente e com a configuração de sub-rede que você definiu na etapa anterior.

Execute o seguinte cmdlet para criar a configuração da rede virtual.

```PowerShell
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Criar um endereço IP público
Agora que temos nossa rede virtual definida, precisamos configurar um endereço IP para conectividade com a máquina virtual. Para este tutorial, criaremos um endereço IP público usando endereçamento IP dinâmico para dar suporte à conectividade com a Internet. Usaremos o cmdlet [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) para criar o endereço IP público no grupo de recursos criado anteriormente e com o nome, a localização, o método de alocação e o rótulo de nome de domínio DNS definido usando as variáveis inicializadas anteriormente.

> [!NOTE]
> Você pode definir propriedades adicionais do endereço IP público usando esse cmdlet, mas isso está além do escopo deste tutorial. Você também pode criar um endereço particular ou um endereço com um endereço estático, mas isso também está além do escopo deste tutorial.

Execute o seguinte cmdlet para criar o endereço IP público.

```PowerShell
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-interface"></a>Criar a interface de rede
Agora estamos prontos para criar a interface de rede que nossa máquina virtual usará. Usaremos o cmdlet [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) para criar nossa interface de rede no grupo de recursos criado anteriormente e com o nome, a localização, a sub-rede e o endereço IP público definidos anteriormente.

Execute o seguinte cmdlet para criar sua interface de rede.

```PowerShell
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id
```

## <a name="configure-a-vm-object"></a>Configurar um objeto da VM
Agora que temos recursos de armazenamento e rede definidos, estamos prontos para definir recursos de computação para a máquina virtual. Para nosso tutorial, vamos especificar varias propriedades do sistema operacional e tamanhos de máquina virtual, especificar a interface de rede criada anteriormente, definir o armazenamento de blobs e, em seguida, especificar o disco do sistema operacional.

### <a name="create-the-vm-object"></a>Criar o objeto da VM
Vamos começar especificando o tamanho da máquina virtual. Para este tutorial, estamos especificando um DS13. Usaremos o cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) para criar um objeto de máquina virtual configurável com o nome e o tamanho definidos usando as variáveis inicializadas anteriormente.

Execute o seguinte cmdlet para criar o objeto da máquina virtual.

```PowerShell
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Criar um objeto de credencial para armazenar o nome e a senha para as credenciais de administrador local
Antes que possamos definir as propriedades de sistema operacional da máquina virtual, precisamos fornecer as credenciais da conta de administrador local como uma cadeia de caracteres segura. Para fazer isso, usaremos o cmdlet [Get-Credential](https://technet.microsoft.com/library/hh849815.aspx) .

Execute o cmdlet a seguir e, na janela de solicitação de credencial do Windows PowerShell, digite o nome e a senha a ser usada para a conta de administrador local na máquina virtual do Windows.

```PowerShell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>Definir as propriedades de sistema operacional da máquina virtual
Agora estamos prontos para definir as propriedades de sistema operacional da máquina virtual. Usaremos o cmdlet [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem) para definir o tipo de sistema operacional como Windows, para exigir que o [agente da máquina virtual](../classic/agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) seja instalado, para especificar que o cmdlet habilite a atualização automática e para definir o nome da máquina virtual, o nome do computador e a credencial usando as variáveis que você inicializou anteriormente.

Execute o seguinte cmdlet para definir as propriedades de sistema operacional de sua máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>Adicionar a interface de rede à máquina virtual
Em seguida, adicionaremos a interface de rede que criamos anteriormente à máquina virtual. Usaremos o cmdlet [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface) para adicionar a interface de rede usando a variável de interface de rede que você definiu anteriormente.

Execute o seguinte cmdlet para definir a interface de rede para sua máquina virtual.

```PowerShell
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>Definir o local de armazenamento de blobs para o disco a ser usado pela máquina virtual
Em seguida, definiremos o local de armazenamento de blobs para o disco a ser usado pela máquina virtual usando as variáveis que você definiu anteriormente.

Execute o seguinte cmdlet para definir o local de armazenamento de blobs.

```PowerShell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>Definir as propriedades de disco do sistema operacional para a máquina virtual
Em seguida, definiremos as propriedades de disco do sistema operacional para a máquina virtual. Usaremos o cmdlet [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk) para especificar que o sistema operacional da máquina virtual será proveniente de uma imagem, para definir o caching para somente leitura (pois o SQL Server é instalado no mesmo disco) e para definir o nome da máquina virtual e o disco do sistema operacional definido usando as variáveis que determinamos anteriormente.

Execute o seguinte cmdlet para definir as propriedades de disco do sistema operacional de sua máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>Especificar a imagem da plataforma para a máquina virtual
Nossa última etapa da configuração é especificar a imagem de plataforma para nossa máquina virtual. Para nosso tutorial, estamos usando a imagem mais recente do SQL Server 2016 CTP. Usaremos o cmdlet [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) para usar essa imagem, conforme definido pelas variáveis que você definiu anteriormente.

Execute o seguinte cmdlet para especificar a imagem da plataforma para sua máquina virtual.

```PowerShell
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>Criar a VM de SQL
Agora que você concluiu as etapas de configuração, você está pronto para criar a máquina virtual. Usaremos o cmdlet [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) para criar a máquina virtual usando as variáveis que definimos.

Execute o seguinte cmdlet para criar sua máquina virtual.

```PowerShell
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

A máquina virtual é criada. Observe que a conta de armazenamento padrão é criada para o diagnóstico de inicialização porque a conta de armazenamento especificada para o disco da máquina virtual é uma conta de armazenamento premium.

Agora você pode ver este computador no Portal do Azure para ver [seu endereço IP público e o nome de domínio totalmente qualificado](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="example-script"></a>Script de exemplo
O script a seguir contém o script completo do PowerShell para este tutorial. Ele pressupõe que você já configurou a assinatura do Azure para usar com os comandos **Add-AzureRmAccount** e **Select-AzureRmSubscription**.

```PowerShell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm1"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = "sqlvm1"

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2016-WS2016"
$Sku = "Enterprise"
$Version = "latest"

# Resource Group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzureRmPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$Interface = New-AzureRmNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id

# Compute
$VirtualMachine = New-AzureRmVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

## Create the VM in Azure
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

## <a name="next-steps"></a>Próximas etapas
Depois que a máquina virtual for criada, você estará pronto para se conectar à máquina virtual usando a conectividade de instalação e RDP. Para obter mais informações, confira [Conectar-se a uma Máquina Virtual do SQL Server (Gerenciador de Recursos)](virtual-machines-windows-sql-connect.md).
