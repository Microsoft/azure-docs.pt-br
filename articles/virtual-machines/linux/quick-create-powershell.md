---
title: Início Rápido – Criar uma VM Linux com o Azure PowerShell
description: Neste início rápido, você aprenderá a usar o Azure PowerShell para criar uma máquina virtual do Linux
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/31/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 2cef611fe79ca04303840076b09b4cf6344b7e7d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102616223"
---
# <a name="quickstart-create-a-linux-virtual-machine-in-azure-with-powershell"></a>Início Rápido: Criar uma máquina virtual do Linux no Azure com o PowerShell

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure da linha de comando do PowerShell ou em scripts. Este início rápido mostra como usar o módulo do Azure PowerShell para implantar uma VM Linux (máquina virtual) no Azure. Este início rápido usa a imagem do marketplace do Ubuntu 18.04 LTS da Canonical. Para ver a VM em ação, você também habilitará o SSH na VM e instalará o servidor Web do NGINX.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

## <a name="create-ssh-key-pair"></a>Criar o par de chaves SSH

Use [ssh-keygen](https://www.ssh.com/ssh/keygen/) para criar um par de chaves SSH. Se você já tiver um par de chaves SSH, você pode ignorar esta etapa.


```azurepowershell-interactive
ssh-keygen -t rsa -b 4096
```

Você será solicitado a fornecer um nome de arquivo para o par de chaves ou poderá pressionar **Enter** para usar o local padrão de `/home/<username>/.ssh/id_rsa`. Você também poderá criar uma senha para as chaves, se desejar.

Para obter mais informações sobre como criar pares de chave SSH, confira [Como usar chaves SSH com o Windows](ssh-from-windows.md).

Se você criar o par de chaves SSH usando o Cloud Shell, ele será armazenado em uma [conta de armazenamento criada automaticamente pelo Cloud Shell](../../cloud-shell/persisting-shell-storage.md). Não exclua essa conta de armazenamento ou compartilhamento de arquivo nela até depois de recuperar suas chaves ou você perderá o acesso à VM. 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-virtual-network-resources"></a>Criar recursos de rede virtual

Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a VM e conectá-la à Internet:

```azurepowershell-interactive
# Create a subnet configuration
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name "mySubnet" `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myVNET" `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzPublicIpAddress `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

Crie uma regra de tráfego e um Grupo de Segurança de Rede do Azure. O Grupo de Segurança de Rede protege a VM com regras de entrada e saída. No exemplo a seguir, uma regra de entrada é criada para a porta TCP 22 que permite conexões SSH. Para permitir o tráfego da Web de entrada, uma regra de entrada para a porta TCP 80 também é criada.

```azurepowershell-interactive
# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleSSH"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 22 `
  -Access "Allow"

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWWW"  `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access "Allow"

# Create a network security group
$nsg = New-AzNetworkSecurityGroup `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -Name "myNetworkSecurityGroup" `
  -SecurityRules $nsgRuleSSH,$nsgRuleWeb
```

Crie a NIC (placa de adaptador de rede virtual) com [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface). A NIC virtual conecta a VM a uma sub-rede, um Grupo de Segurança de Rede e um endereço IP público.

```azurepowershell-interactive
# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzNetworkInterface `
  -Name "myNic" `
  -ResourceGroupName "myResourceGroup" `
  -Location "EastUS" `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma VM no PowerShell, você deve criar uma configuração que tem configurações como a imagem para opções de autenticação, tamanho e uso. Em seguida, a configuração é usada para compilar a VM.

Defina as credenciais do SSH, as informações do sistema operacional e o tamanho de VM. Neste exemplo, a chave SSH é armazenada no `~/.ssh/id_rsa.pub`. 

```azurepowershell-interactive
# Define a credential object
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("azureuser", $securePassword)

# Create a virtual machine configuration
$vmConfig = New-AzVMConfig `
  -VMName "myVM" `
  -VMSize "Standard_D1" | `
Set-AzVMOperatingSystem `
  -Linux `
  -ComputerName "myVM" `
  -Credential $cred `
  -DisablePasswordAuthentication | `
Set-AzVMSourceImage `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "18.04-LTS" `
  -Version "latest" | `
Add-AzVMNetworkInterface `
  -Id $nic.Id

# Configure the SSH key
$sshPublicKey = cat ~/.ssh/id_rsa.pub
Add-AzVMSshPublicKey `
  -VM $vmconfig `
  -KeyData $sshPublicKey `
  -Path "/home/azureuser/.ssh/authorized_keys"
```

Agora, combine as definições de configuração anteriores para criar com [New-AzVM](/powershell/module/az.compute/new-azvm):

```azurepowershell-interactive
New-AzVM `
  -ResourceGroupName "myResourceGroup" `
  -Location eastus -VM $vmConfig
```

Levará alguns minutos para que sua VM seja implantada. Quando a implantação for concluída, vá para a próxima seção.

## <a name="connect-to-the-vm"></a>Conectar-se à VM

Crie uma conexão SSH com a VM usando o endereço IP público. Para ver o endereço IP público da VM, use o cmdlet [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress):

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName "myResourceGroup" | Select "IpAddress"
```

Usando o mesmo shell que você usou para criar o par de chaves SSH, cole o comando a seguir no shell para criar uma sessão SSH. Substitua *10.111.12.123* pelo endereço IP da VM.

```bash
ssh azureuser@10.111.12.123
```

Quando solicitado, o nome de usuário de logon é *azureuser*. Se uma frase secreta é usada com as chaves SSH, você precisa inseri-la, quando solicitado.


## <a name="install-nginx"></a>Instalar o NGINX

Para ver a VM em ação, instale o servidor Web do NGINX. Na sua sessão de SSH, atualize suas fontes de pacote e, em seguida, instale o pacote mais recente do NGINX.

```bash
sudo apt-get -y update
sudo apt-get -y install nginx
```

Quando terminar, digite `exit` para sair da sessão SSH.


## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Use um navegador da Web de sua escolha para exibir a página inicial padrão do NGINX. Insira o endereço IP público da VM como o endereço Web. O endereço IP público pode ser encontrado na página de visão geral de VM ou como parte da cadeia de conexão SSH usada anteriormente.

![Página de boas-vindas padrão do NGINX](./media/quick-create-cli/nginix-welcome-page.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, você poderá usar o cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma máquina virtual simples, criou um Grupo de Segurança de Rede e uma regra e instalou um servidor Web básico. Para saber mais sobre máquinas virtuais do Azure, continue o tutorial para VMs do Linux.

> [!div class="nextstepaction"]
> [Tutoriais de máquina virtual do Linux Azure](./tutorial-manage-vm.md)
