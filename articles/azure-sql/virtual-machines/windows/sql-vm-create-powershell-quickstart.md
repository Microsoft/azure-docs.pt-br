---
title: Criar um SQL Server em uma máquina virtual do Windows no Azure PowerShell | Microsoft Docs
description: Este tutorial mostra como usar o Azure PowerShell para criar uma máquina virtual do Windows que executa o SQL Server 2017.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: deployment
ms.topic: quickstart
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 350df1aede076120fd14dc46da351835c894c5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97356813"
---
# <a name="quickstart-create-sql-server-on-a-windows-virtual-machine-with-azure-powershell"></a>Início Rápido: Criar um SQL Server em uma máquina virtual do Windows no Azure PowerShell

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este início rápido descreve a criação de uma VM (máquina virtual) do SQL Server com o Azure PowerShell.

> [!TIP]
> - Este início rápido fornece um caminho para provisionar rapidamente e conectar-se a uma VM do SQL. Para obter mais informações sobre outras opções do Azure PowerShell para criar VMs do SQL, confira o [Guia de provisionamento para VMs do SQL Server com o Azure PowerShell](create-sql-vm-powershell.md).
> - Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).

## <a name="get-an-azure-subscription"></a><a id="subscription"></a> Obter uma assinatura do Azure

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="get-azure-powershell"></a><a id="powershell"></a> Obter o Azure PowerShell

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-powershell"></a>Configurar o PowerShell

1. Abra o PowerShell e estabeleça o acesso à sua conta do Azure executando o comando **Connect-AzAccount**.

   ```powershell
   Connect-AzAccount
   ```

1. Quando a janela de logon for exibida, insira suas credenciais. Use o mesmo email e senha usados para entrar no Portal do Azure.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

1. Defina uma variável com um nome exclusivo de grupo de recurso. Para simplificar o restante do início rápido, o restante dos comandos usa esse nome como base para outros nomes de recurso.

   ```powershell
   $ResourceGroupName = "sqlvm1"
   ```

1. Defina um local de uma região de destino do Azure para todos os recursos de VM.

   ```powershell
   $Location = "East US"
   ```

1. Crie o grupo de recursos.

   ```powershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   ```

## <a name="configure-network-settings"></a>Definir as configurações de rede

1. Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a máquina virtual e conectá-la à Internet.

   ``` PowerShell
   $SubnetName = $ResourceGroupName + "subnet"
   $VnetName = $ResourceGroupName + "vnet"
   $PipName = $ResourceGroupName + $(Get-Random)

   # Create a subnet configuration
   $SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $Vnet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
      -Name $VnetName -AddressPrefix 192.168.0.0/16 -Subnet $SubnetConfig

   # Create a public IP address and specify a DNS name
   $Pip = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
      -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name $PipName
   ```

1. Crie um grupo de segurança de rede. Configure regras para permitir conexões da área de trabalho remota (RDP) e do SQL Server.

   ```powershell
   # Rule to allow remote desktop (RDP)
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   #Rule to allow SQL Server connections on port 1433
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow

   # Create the network security group
   $NsgName = $ResourceGroupName + "nsg"
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

1. Crie a interface de rede.

   ```powershell
   $InterfaceName = $ResourceGroupName + "int"
   $Interface = New-AzNetworkInterface -Name $InterfaceName `
      -ResourceGroupName $ResourceGroupName -Location $Location `
      -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $Pip.Id `
      -NetworkSecurityGroupId $Nsg.Id
   ```

## <a name="create-the-sql-vm"></a>Criar a VM de SQL

1. Defina suas credenciais para entrar na VM. O nome de usuário é "azureadmin". Altere a \<password> antes de executar o comando.

   ``` PowerShell
   # Define a credential object
   $SecurePassword = ConvertTo-SecureString '<password>' `
      -AsPlainText -Force
   $Cred = New-Object System.Management.Automation.PSCredential ("azureadmin", $securePassword)
   ```

1. Crie um objeto de configuração de máquina virtual e depois crie a VM. O comando a seguir cria uma VM do SQL Server 2017 Developer Edition no Windows Server 2016.

   ```powershell
   # Create a virtual machine configuration
   $VMName = $ResourceGroupName + "VM"
   $VMConfig = New-AzVMConfig -VMName $VMName -VMSize Standard_DS13_V2 |
      Set-AzVMOperatingSystem -Windows -ComputerName $VMName -Credential $Cred -ProvisionVMAgent -EnableAutoUpdate |
      Set-AzVMSourceImage -PublisherName "MicrosoftSQLServer" -Offer "SQL2017-WS2016" -Skus "SQLDEV" -Version "latest" |
      Add-AzVMNetworkInterface -Id $Interface.Id
   
   # Create the VM
   New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VMConfig
   ```

   > [!TIP]
   > São necessários vários minutos para criar a VM.

## <a name="register-with-sql-vm-rp"></a>Registrar-se com o RP da VM do SQL 

Para obter a integração do portal e os recursos da VM do SQL, registre-se na [extensão do Agente de IaaS do SQL](sql-agent-extension-manually-register-single-vm.md).

Para obter a funcionalidade completa, você precisará se registrar na extensão no modo completo. No entanto, isso reinicia o serviço SQL Server, portanto, a abordagem recomendada é registrar-se no modo leve e atualizar para completo em uma janela de manutenção. 

Primeiro, registre a VM do SQL Server no modo leve: 

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
  -LicenseType PAYG -SqlManagementType LightWeight
```

Em seguida, durante uma janela de manutenção, atualize para o modo completo: 

```powershell-interactive
# Get the existing Compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
      
# Register with SQL IaaS Agent extension in full mode
Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
```



## <a name="remote-desktop-into-the-vm"></a>Área de trabalho remota na VM

1. Use o comando a seguir para recuperar o endereço IP público da nova VM.

   ```powershell
   Get-AzPublicIpAddress -ResourceGroupName $ResourceGroupName | Select IpAddress
   ```

1. Passe o endereço IP retornado como um parâmetro de linha de comando para **mstsc** para iniciar uma sessão da Área de Trabalho Remota na nova VM.

   ```
   mstsc /v:<publicIpAddress>
   ```

1. Quando as credenciais forem solicitadas, opte por inserir as credenciais para uma conta diferente. Insira o nome de usuário precedido de uma barra invertida (por exemplo, `\azureadmin`) e a senha definida anteriormente neste início rápido.

## <a name="connect-to-sql-server"></a>Conecte-se ao SQL Server

1. Depois de entrar na sessão da Área de Trabalho Remota, inicie o **SQL Server Management Studio 2017** no menu Iniciar.

1. Na caixa de diálogo **Conectar-se ao Servidor**, mantenha os padrões. O nome do servidor é o mesmo da VM. A autenticação está definida como **Autenticação do Windows**. Selecione **Conectar**.

Agora você está conectado ao SQL Server localmente. Caso deseje se conectar remotamente, [configure a conectividade](ways-to-connect-to-sql.md) no portal do Azure ou manualmente.

## <a name="clean-up-resources"></a>Limpar os recursos

Caso não precise que a VM seja executada continuamente, é possível evitar encargos desnecessários interrompendo-a quando não estiver em uso. O comando a seguir interrompe a VM, mas a deixa disponível para uso futuro.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

Também é possível excluir permanentemente todos os recursos associados à máquina virtual com o comando **Remove-AzResourceGroup**. Essa ação também excluirá a máquina virtual permanentemente; portanto, use esse comando com cuidado.

## <a name="next-steps"></a>Próximas etapas

Nesse guia de início rápido, você criou uma máquina virtual do SQL Server 2017 no Azure PowerShell. Para saber mais sobre como migrar seus dados para o novo SQL Server, consulte o artigo a seguir.

> [!div class="nextstepaction"]
> [Migrar um banco de dados para uma VM do SQL](migrate-to-vm-from-sql-server.md)
