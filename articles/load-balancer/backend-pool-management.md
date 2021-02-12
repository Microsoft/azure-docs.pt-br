---
title: Gerenciamento de pools de back-end
titleSuffix: Azure Load Balancer
description: Comece a aprender a configurar e gerenciar o pool de back-end de um Azure Load Balancer
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 07/07/2020
ms.author: allensu
ms.openlocfilehash: e5efbf695b85f474e5d7c84c86809acb2f5a1035
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429595"
---
# <a name="backend-pool-management"></a>Gerenciamento de pools de back-end
O pool de back-end é um componente crítico do balanceador de carga. O pool de back-end define o grupo de recursos que receberá o tráfego para determinada regra de balanceamento de carga.

Há duas maneiras de configurar um pool de back-end:
* NIC (placa de adaptador de rede)
* Combinação de endereço IP e identificação do recurso da VNET (Rede Virtual)

Configure o pool de back-end pela NIC ao usar máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais existentes. Esse método cria o link mais direto entre o recurso e o pool de back-end. 

Ao alocar previamente o seu pool de back-end com um intervalo de endereços IP com o qual você planeja criar posteriormente máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais, configure o pool de back-end por endereço IP e por combinação de ID de VNET.

Você pode configurar pools de back-end baseados em IP e em NIC para o mesmo balanceador de carga, no entanto, você não pode criar um único pool de back-end que combine endereços com suporte direcionados por endereços IP e NIC dentro do mesmo pool.

As seções de configuração deste artigo terão como foco:

* Azure PowerShell
* CLI do Azure
* API REST
* Modelos do Azure Resource Manager 

Essas seções fornecem insights sobre como os pools de back-end são estruturados para cada opção de configuração.

## <a name="configuring-backend-pool-by-nic"></a>Como configurar o pool de back-end pela NIC
O pool de back-end é criado como parte da operação do balanceador de carga. A propriedade de configuração de IP da NIC é usada para adicionar membros do pool de back-end.

Os exemplos a seguir têm como foco as operações de criação e preenchimento do pool de back-end a fim de realçar esse fluxo de trabalho e essa relação.

  >[!NOTE] 
  >É importante observar que os pools de back-end configurados por meio do adaptador de rede não podem ser atualizados como parte de uma operação no pool de back-end. Qualquer adição ou exclusão de recursos de back-end precisa ocorrer no adaptador de rede do recurso.

### <a name="powershell"></a>PowerShell
Criar um pool de back-end:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Criar um adaptador de rede e adicioná-lo ao pool de back-end:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

Recuperar as informações do pool de back-end para o balanceador de carga, a fim de confirmar se esse adaptador de rede foi adicionado ao pool de back-end:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Criar uma máquina virtual e anexar o adaptador de rede para colocá-lo no pool de back-end:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Criar o pool de back-end:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Criar um adaptador de rede e adicioná-lo ao pool de back-end:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

Recuperar o pool de back-end para confirmar se o endereço IP foi adicionado corretamente:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Criar uma máquina virtual e anexar o adaptador de rede para colocá-lo no pool de back-end:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="rest-api"></a>API REST
Criar o pool de back-end:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Criar um adaptador de rede e adicioná-lo ao pool de back-end criado por meio da propriedade de configurações de IP do adaptador de rede:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Corpo da solicitação JSON:
```json
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          },
          "loadBalancerBackendAddressPools": {
                                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Recuperar as informações do pool de back-end para o balanceador de carga, a fim de confirmar se esse adaptador de rede foi adicionado ao pool de back-end:

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Criar uma VM e anexar a NIC que referencia o pool de back-end:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Corpo da solicitação JSON:
```JSON
{
  "location": "easttus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="resource-manager-template"></a>Modelo do Resource Manager
Siga este [modelo do Resource Manager do guia de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) para implantar um balanceador de carga e máquinas virtuais e adicionar as máquinas virtuais ao pool de back-end por meio do adaptador de rede.

## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>Configurar o pool de back-end por endereço IP e rede virtual
Em cenários com pools de back-end previamente preenchidos, use o IP e a rede virtual.

Todo o gerenciamento de pools de back-end é feito diretamente no objeto de pool de back-end, conforme realçado nos exemplos abaixo.

  >[!IMPORTANT] 
  >Esse recurso está atualmente na visualização. Confira a [seção de limitações](#limitations) para saber os limites atuais desse recurso.

### <a name="powershell"></a>PowerShell
Criar um pool de back-end:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Atualizar o pool de back-end com um novo IP da rede virtual existente:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

Recuperar as informações do pool de back-end para o balanceador de carga, a fim de confirmar se os endereços de back-end foram adicionados ao pool de back-end:

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Crie um adaptador de rede e adicione-o ao pool de back-end. Defina o endereço IP com um dos endereços de back-end:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Criar uma VM e anexar a NIC com um endereço IP no pool de back-end:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>CLI
Usando a CLI, você pode preencher o pool de back-end por meio dos parâmetros de linha de comando ou de um arquivo de configuração JSON. 

Criar e preencher o pool de back-end por meio dos parâmetros de linha de comando:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

Criar e preencher o pool de back-end por meio do arquivo de configuração JSON:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

Arquivo de configuração JSON:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

Recuperar as informações do pool de back-end para o balanceador de carga, a fim de confirmar se os endereços de back-end foram adicionados ao pool de back-end:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Crie um adaptador de rede e adicione-o ao pool de back-end. Defina o endereço IP com um dos endereços de back-end:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Criar uma VM e anexar a NIC com um endereço IP no pool de back-end:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>API REST

Crie o pool de back-end e defina os endereços de back-end por meio de uma solicitação PUT do pool de back-end. Configure os endereços de back-end no corpo JSON da solicitação PUT por:

* Nome do endereço
* Endereço IP
* ID da rede virtual 

```
PUT https://management.azure.com/subscriptions/subid/resourceGroups/testrg/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/backend?api-version=2020-05-01
```

Corpo da solicitação JSON:
```JSON
{
  "properties": {
    "loadBalancerBackendAddresses": [
      {
        "name": "address1",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.4"
        }
      },
      {
        "name": "address2",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.5"
        }
      }
    ]
  }
}
```

Recuperar as informações do pool de back-end para o balanceador de carga, a fim de confirmar se os endereços de back-end foram adicionados ao pool de back-end:
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Crie um adaptador de rede e adicione-o ao pool de back-end. Defina o endereço IP com um dos endereços de back-end:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

Corpo da solicitação JSON:
```JSON
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAddress": "10.0.0.4",
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Criar uma VM e anexar a NIC com um endereço IP no pool de back-end:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

Corpo da solicitação JSON:
```JSON
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

## <a name="limitations"></a>Limitações
Um pool de back-end configurado pelo endereço IP tem as seguintes limitações:
  * Somente Standard Load Balancer
  * Limite de 100 endereços IP no pool de backend
  * Os recursos de back-end precisam estar na mesma rede virtual do balanceador de carga
  * Um Load Balancer com o pool de back-end baseado em IP não pode funcionar como um serviço de Link Privado
  * Atualmente, não há suporte para esse recurso no portal do Azure
  * Atualmente, não há suporte para contêineres ACI neste recurso
  * Balanceadores de carga ou serviços administrados por balanceadores de carga não podem ser colocados no pool de backend do balanceador de carga
  * As regras NAT de entrada não podem ser especificadas pelo endereço IP
  
## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu mais sobre o gerenciamento de pools de back-end do Azure Load Balancer e como configurar um pool de back-end por endereço IP e rede virtual.

Saiba mais sobre o [Azure Load Balancer](load-balancer-overview.md).
