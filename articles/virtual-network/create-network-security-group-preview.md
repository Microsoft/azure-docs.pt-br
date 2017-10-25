---
title: "Filtrar o tráfego de rede com os grupos de segurança de rede e de aplicativo do Azure (versão prévia) | Microsoft Docs"
description: "Saiba como criar grupos de segurança de rede e de aplicativo (versão prévia) para restringir o tipo do tráfego de rede de entrada e de saída nas máquinas virtuais."
services: virtual-network
documentationcenter: 
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2017
ms.author: jdial
ms.custom: 
ms.openlocfilehash: 035eb44432081ef52c758a5d311b4d2ba2c6108d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="filter-network-traffic-with-network-and-application-security-groups-preview"></a>Filtrar o tráfego de rede com os grupos de segurança de rede e de aplicativo do (versão prévia)

Neste tutorial, saiba como criar grupos de segurança de rede para filtrar o tráfego de rede para e de grupos de máquinas virtuais com grupos de segurança de aplicativo. Para saber mais sobre grupos de segurança de rede e grupos de segurança de aplicativo, leia a [Visão geral de segurança](security-overview.md). 

As seções a seguir incluem etapas que você pode executar para criar grupos de segurança de rede usando a interface de linha de comando do Azure ([CLI do Azure](#azure-cli)) e o [Azure PowerShell](#powershell). O resultado é o mesmo, não importando a ferramenta que você use para criar os grupos de segurança de rede. Clique no link da ferramenta para ir para essa seção do tutorial. 

Este artigo fornece etapas para criar grupos de segurança de rede usando o modelo de implantação do Resource Manager, que é o modelo de implantação recomendado ao criar grupos de segurança de rede. Se você precisa criar um grupo de segurança de rede (clássico), consulte [Criar um grupo de segurança de rede (clássico)](virtual-networks-create-nsg-classic-ps.md). Se você não estiver familiarizado com os modelos de implantação do Azure, confira [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!NOTE]
> Este tutorial usa recursos de grupo de segurança de rede que estão atualmente em versão prévia. Recursos de versão prévia não têm a mesma disponibilidade e confiabilidade que recursos de versão geral. Enquanto estiverem em versão prévia, os recursos estarão disponíveis somente nas seguintes regiões: WestCentralUS. Se você deseja implementar grupo de segurança de rede usando apenas recursos de versão geral, consulte [Criar um grupo de segurança de rede](virtual-networks-create-nsg-arm-pportal.md). 

## <a name="azure-cli"></a>CLI do Azure

Comandos da CLI do Azure são os mesmos, se você executar os comandos do Windows, Linux ou macOS. No entanto, há diferenças de script entre shells de sistema operacional. Os scripts nas etapas a seguir são executados em um shell Bash. 

1. [Instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Verifique se você está usando a versão 2.0.18 ou superior da CLI do Azure digitando o comando `az --version`. Se não estiver, instale a versão mais recente.
3. Faça logon no Azure com o comando `az login`.
4. Registre-se para a versão prévia inserindo os seguintes comandos:
    
    ```azurecli-interactive
    az feature register --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ``` 

5. Digite o seguinte comando para confirmar que você está registrado para a versão prévia:

    ```azurecli-interactive
    az feature show --name AllowApplicationSecurityGroups --namespace Microsoft.Network
    ```

    Não continue com as etapas restantes até que *Registrado* apareça como o valor de **estado** na saída retornada pelo comando anterior. Se você continuar antes de estar registrado, as etapas restantes falharão.
6. Execute o script bash a seguir para criar um grupo de recursos:

    ```azurecli-interactive
    #!/bin/bash
    
    az group create \
      --name myResourceGroup \
      --location westcentralus
    ```

7. Crie três grupos de segurança de aplicativo, um para cada tipo de servidor:

    ```azurecli-interactive
    az network asg create \
      --resource-group myResourceGroup \
      --name WebServers \
      --location westcentralus  

    az network asg create \
      --resource-group myResourceGroup \
      --name AppServers \
      --location westcentralus

    az network asg create \
      --resource-group myResourceGroup \
      --name DatabaseServers \
      --location westcentralus
    ```

8. Crie um grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg create \
      --resource-group myResourceGroup \
      --name myNsg \
      --location westcentralus
    ```

9. Crie regras de segurança dentro do NSG, configurando os grupos de segurança do aplicativo como o destino:
    
    ```azurecli-interactive    
    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name WebRule \
      --priority 200 \
      --access "Allow" \
      --direction "inbound" \
      --source-address-prefixes "Internet" \
      --destination-asgs "WebServers" \
      --destination-port-ranges 80 \
      --protocol "TCP"

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name AppRule \
      --priority 300 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "WebServers" \
      --destination-asgs "AppServers" \
      --destination-port-ranges 443 \
      --protocol "TCP"  

    az network nsg rule create \
      --resource-group myResourceGroup \
      --nsg-name myNsg \
      --name DatabaseRule \
      --priority 400 \
      --access "Allow" \
      --direction "inbound" \
      --source-asgs "AppServers" \
      --destination-asgs "DatabaseServers" \
      --destination-port-ranges 1336 \
      --protocol "TCP" 
    ``` 

10. Criar uma rede virtual: 
    
    ```azurecli-interactive
    az network vnet create \
      --name myVnet \
      --resource-group myResourceGroup \
      --subnet-name mySubnet \
      --address-prefix 10.0.0.0/16 \
      --location westcentralus
    ```

11. Associe o Grupo de Segurança de Rede para a sub-rede na rede virtual:

    ```azurecli-interactive
    az network vnet subnet update \
      --name mySubnet \
      --resource-group myResourceGroup \
      --vnet-name myVnet \
      --network-security-group myNsg
    ```
    
12. Crie três adaptadores de rede, um para cada tipo de servidor: 

    ```azurecli-interactive
    az network nic create \
      --resource-group myResourceGroup \
      --name myNic1 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "WebServers" "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic2 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "AppServers"

    az network nic create \
      --resource-group myResourceGroup \
      --name myNic3 \
      --vnet-name myVnet \
      --subnet mySubnet \
      --network-security-group myNsg \
      --location westcentralus \
      --application-security-groups "DatabaseServers"
    ```

    Somente a regra de segurança correspondente que você criou na etapa 9 é aplicada ao adaptador de rede, com base no grupo de segurança de aplicativo do qual o adaptador de rede é um membro. Por exemplo, somente o *WebRule* está em vigor para o *myWebNic*, pois o adaptador de rede é um membro do grupo de segurança do aplicativo *WebServers* e a regra especifica o grupo de segurança de aplicativo *WebServers* como seu destino. As regras *AppRule* e *DatabaseRule* não são aplicadas ao *myWebNic*, pois o adaptador de rede não é um membro dos grupos de segurança de aplicativo *AppServers* e *DatabaseServers*.

13. Crie uma máquina virtual para cada tipo de servidor, anexando o adaptador de rede correspondente a cada máquina virtual. Este exemplo cria máquinas virtuais do Windows, mas você pode alterar *win2016datacenter* para *UbuntuLTS* para criar máquinas virtuais Linux em vez disso.

    ```azurecli-interactive
    # Update for your admin password
    AdminPassword=ChangeYourAdminPassword1

    az vm create \
      --resource-group myResourceGroup \
      --name myWebVm \
      --location westcentralus \
      --nics myNic1 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myAppVm \
      --location westcentralus \
      --nics myNic2 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword

    az vm create \
      --resource-group myResourceGroup \
      --name myDatabaseVm \
      --location westcentralus \
      --nics myNic3 \
      --image win2016datacenter \
      --admin-username azureuser \
      --admin-password $AdminPassword    
    ```

14. **Opcional**: exclua os recursos criados neste tutorial concluindo as etapas em [Excluir recursos](#delete-cli).

## <a name="powershell"></a>PowerShell

1. Instale e configure o [PowerShell](/powershell/azure/install-azurerm-ps).
2. Verifique se você tem a versão 4.4.0 ou superior do módulo AzureRm instalada. Você pode verificar a versão atualmente instalada digitando o comando `Get-Module -ListAvailable AzureRM`. Se você precisar instalar ou atualizar, instale a versão mais recente do módulo AzureRM por meio da [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureRM).
3. Em uma sessão do PowerShell, faça logon no Azure com sua [conta do Azure](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#account) usando o comando `login-azurermaccount`.
4. Registre-se para a versão prévia inserindo os seguintes comandos:
    
    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ``` 

5. Digite o seguinte comando para confirmar que você está registrado para a versão prévia:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowApplicationSecurityGroups -ProviderNamespace Microsoft.Network
    ```

    Não continue com as etapas restantes até que *Registrado* apareça na coluna **RegistrationState** da saída retornada pelo comando anterior. Se você continuar antes de estar registrado, as etapas restantes falharão.
        
6. Crie um grupo de recursos:

    ```powershell
    New-AzureRmResourceGroup `
      -Name myResourceGroup `
      -Location westcentralus
    ```

7. Crie três grupos de segurança de aplicativo, um para cada tipo de servidor:

    ```powershell
    $webAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name WebServers `
      -Location westcentralus
  
    $appAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name AppServers `
      -Location westcentralus

    $databaseAsg = New-AzureRmApplicationSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Name DatabaseServers `
      -Location westcentralus
    ```

8. Crie regras de segurança para cada tipo de servidor.
    
    ```powershell
    $webRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "WebRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 200 `
      -SourceAddressPrefix Internet `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $webAsg.id `
      -DestinationPortRange 80  

    $appRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "AppRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 300 `
      -SourceApplicationSecurityGroupId $webAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $appAsg.id `
      -DestinationPortRange 443 

    $databaseRule = New-AzureRmNetworkSecurityRuleConfig `
      -Name "DatabaseRule" `
      -Access Allow `
      -Protocol Tcp `
      -Direction Inbound `
      -Priority 400 `
      -SourceApplicationSecurityGroupId $appAsg.id `
      -SourcePortRange * `
      -DestinationApplicationSecurityGroupId $databaseAsg.id `
      -DestinationPortRange 1336    
    ``` 

9. Crie um grupo de segurança de rede:

    ```powershell
    $nsg = New-AzureRmNetworkSecurityGroup `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Name myNsg `
      -SecurityRules $WebRule,$AppRule,$DatabaseRule
    ```

10. Crie uma configuração de sub-rede e associe o grupo de segurança de rede a ele:
    
    ```powershell
    $subnet = New-AzureRmVirtualNetworkSubnetConfig `
      -AddressPrefix 10.0.0.0/24 `
      -Name mySubnet `
      -NetworkSecurityGroup $nsg
    ```

11. Criar uma rede virtual: 
    
    ```powershell
    $vNet = New-AzureRmVirtualNetwork `
      -Name myVnet `
      -AddressPrefix '10.0.0.0/16' `
      -Subnet $subnet `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus
    ```

12. Crie três adaptadores de rede, um para cada tipo de servidor. 

    ```powershell
    $nic1 = New-AzureRmNetworkInterface `
      -Name myNic1 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $webAsg,$appAsg

    $nic2 = New-AzureRmNetworkInterface `
      -Name myNic2 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $appAsg

    $nic3 = New-AzureRmNetworkInterface `
      -Name myNic3 `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -Subnet $vNet.Subnets[0] `
      -NetworkSecurityGroup $nsg `
      -ApplicationSecurityGroup $databaseAsg
    ```

    Somente a regra de segurança correspondente que você criou na etapa 8 é aplicada ao adaptador de rede, com base no grupo de segurança de aplicativo do qual o adaptador de rede é um membro. Por exemplo, somente o *WebRule* está em vigor para o *myWebNic*, pois o adaptador de rede é um membro do grupo de segurança do aplicativo *WebServers* e a regra especifica o grupo de segurança de aplicativo *WebServers* como seu destino. As regras *AppRule* e *DatabaseRule* não são aplicadas ao *myWebNic*, pois o adaptador de rede não é um membro dos grupos de segurança de aplicativo *AppServers* e *DatabaseServers*.

13. Crie uma máquina virtual para cada tipo de servidor, anexando o adaptador de rede correspondente a cada máquina virtual. Este exemplo cria máquinas virtuais do Windows, mas antes de executar o script, você pode alterar *-Windows* para *-Linux*, *MicrosoftWindowsServer* para *Canonical*, *WindowsServer* para *UbuntuServer* e *2016-Datacenter* para *14.04.2-LTS* para criar máquinas virtuais Linux em vez disso.

    ```powershell
    # Create user object
    $cred = Get-Credential -Message "Enter a username and password for the virtual machine."
   
    # Create the web server virtual machine configuration and virtual machine.
    $webVmConfig = New-AzureRmVMConfig `
      -VMName myWebVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myWebVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic1.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $webVmConfig

    # Create the app server virtual machine configuration and virtual machine.
    $appVmConfig = New-AzureRmVMConfig `
      -VMName myAppVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName myAppVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic2.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $appVmConfig

    # Create the database server virtual machine configuration and virtual machine.
    $databaseVmConfig = New-AzureRmVMConfig `
      -VMName myDatabaseVm `
      -VMSize Standard_DS1_V2 | `
    Set-AzureRmVMOperatingSystem -Windows `
      -ComputerName mydatabaseVm `
      -Credential $cred | `
    Set-AzureRmVMSourceImage `
      -PublisherName MicrosoftWindowsServer `
      -Offer WindowsServer `
      -Skus 2016-Datacenter `
      -Version latest | `
    Add-AzureRmVMNetworkInterface `
      -Id $nic3.Id
    New-AzureRmVM `
      -ResourceGroupName myResourceGroup `
      -Location westcentralus `
      -VM $databaseVmConfig
    ```

14. **Opcional**: exclua os recursos criados neste tutorial concluindo as etapas em [Excluir recursos](#delete-cli).

## <a name="delete"></a>Excluir recursos

Após a conclusão este tutorial, convém excluir os recursos criados, para não incorrer em encargos de uso. A exclusão de um grupo de recursos também exclui todos os recursos que estão no grupo de recursos.

### <a name="delete-portal"></a>Portal do Azure

1. Na caixa de pesquisa do portal, insira **myResourceGroup**. Nos resultados da pesquisa, clique em **myResourceGroup**.
2. Na folha **myResourceGroup**, clique no ícone **Excluir**.
3. Para confirmar a exclusão, na caixa **DIGITAR O NOME DO GRUPO DE RECURSOS**, insira **myResourceGroup** e, depois, clique em **Excluir**.

### <a name="delete-cli"></a>Azure CLI

Em uma sessão da CLI, insira o seguinte comando:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

### <a name="delete-powershell"></a>PowerShell

Em uma sessão do PowerShell, insira o seguinte comando:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

