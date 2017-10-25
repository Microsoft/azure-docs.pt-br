---
title: "Configurar pontos de extremidade de serviço de rede virtual do Azure | Microsoft Docs"
description: "Saiba como habilitar e desabilitar pontos de extremidade de serviço de sua rede virtual"
services: virtual-network
documentationcenter: na
author: anithaa
manager: narayan
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: anithaa
ms.custom: 
ms.openlocfilehash: 9c953e697d5d1c6e53df8eaf72c4e4fbd83ca286
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="configure-virtual-network-service-endpoints"></a>Configurar pontos de extremidade de serviço de rede virtual

Os pontos de serviço de VNet (rede virtual) permitem que você garanta recursos de serviço do Azure para sua rede virtual do Azure, removendo completamente o acesso à Internet para esses recursos. Os pontos de extremidade de serviço fornecem conexão direta de sua rede virtual para um serviço do Azure, permitindo que você use o espaço de endereço privado da rede virtual para acessar os serviços do Azure. O tráfego destinado aos serviços do Azure por meio de pontos de extremidade de serviço sempre ficam na rede de backbone do Microsoft Azure. Saiba mais sobre [“pontos de extremidade de serviço de rede virtual”](virtual-network-service-endpoints-overview.md)

Este artigo oferece etapas para habilitar e desabilitar pontos de extremidade de serviço. Depois que os pontos de extremidade são habilitados em uma sub-rede para um serviço do Azure, você pode garantir determinados recursos de serviço para uma rede virtual.

Os pontos de extremidade de serviço podem ser configurados usando o [portal do Azure](#azure-portal), o [Azure PowerShell](#azure-powershell), a [interface de linha de comando do Azure](#azure-cli) ou um [ modelo](#resource-manager-template) do Azure Resource Manager.

>[!NOTE]
Durante a versão prévia, o recurso de pontos de extremidade de serviço de rede virtual dá suporte a regiões específicas. Para obter a lista de regiões com suporte, consulte a página [Atualizações de rede virtual do Azure](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="service-endpoint-configuration-overview"></a>Visão geral de configuração de ponto de extremidade de serviço

- Os pontos de extremidade de serviço só podem ser configurados em VNets implantadas por meio do modelo de implantação do Azure Resource Manager.

- Os pontos de extremidade de serviço são definidos em cada sub-rede de uma rede virtual.

- Em cada sub-rede, você só pode configurar um ponto de extremidade de serviço para um serviço. Você pode configurar vários pontos de extremidade de serviço para serviços diferentes (por exemplo, Armazenamento do Azure, SQL Azure).

- Você pode habilitar os pontos de extremidade em uma sub-rede nova ou existente.

- O local é configurado automaticamente para um ponto de extremidade. Por padrão, os pontos de extremidade de serviço são configurados para a região da rede virtual. No caso do Armazenamento do Azure, para dar suporte a cenários de failover regionais, os pontos de extremidade são automaticamente configurados para [Regiões emparelhadas do Azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)

  >[!NOTE]
  Dependendo do tamanho da sub-rede/rede virtual, a habilitação do ponto de extremidade de serviço pode levar algum tempo para concluir. Verifique se nenhuma tarefa crítica está em andamento na hora de habilitar pontos de extremidade de serviço. Os pontos de extremidade de serviço mudam de rota em cada NIC na sua sub-rede e podem encerrar todas as conexões TCP abertas. 

- A chamada de ponto de extremidade de serviço retorna "com êxito" depois que o fluxo de tráfego para o serviço em todos os NICs da sub-rede muda para endereços IP privados de rede virtual.

- Rotas eficientes para validar a configuração de ponto de extremidade:

   Para validar se o ponto de extremidade de serviço está configurado corretamente, as "rotas eficientes" em uma NIC na sub-rede mostram uma nova rota "padrão" com nextHopType: VirtualNetworkServiceEndpoint, por serviço, por região. Saiba mais sobre [solução de problemas com rotas efetivas](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#using-effective-routes-to-troubleshoot-vm-traffic-flow)

   >[!NOTE]
   As Rotas eficientes só podem ser exibidas se você tem um ou mais NICs (adaptadores de rede) configurado e associado a uma máquina virtual na sub-rede.

## <a name="azure-portal"></a>Portal do Azure

### <a name="setting-up-service-endpoint-on-a-subnet-during-vnet-create"></a>Configurando ponto de extremidade de serviço em uma sub-rede durante a criação da rede virtual

1. Abra o [portal do Azure](https://portal.azure.com/).
Faça logon no Azure usando sua conta do Azure. Se não tiver uma conta do Azure, você poderá assinar uma versão de avaliação gratuita. A conta deve ter as [permissões](#provisioning) necessárias para criar uma rede virtual e um ponto de extremidade de serviço.
2. Clique em + Novo > Rede > Rede Virtual > +Adicionar.
3. Em “Criar rede virtual”, insira os valores a seguir e clique em Criar:

Configuração | Valor
------- | -----
Nome    | myVnet
Espaço de endereço | 10.0.0.0/16
Nome da sub-rede|mySubnet
Intervalo de endereços da sub-rede|10.0.0.0/24
Grupo de recursos|Deixe Criar novo selecionado e digite um nome.
Local|Qualquer região com suporte, digamos, Leste da Austrália
Assinatura|Selecione sua assinatura.
__ServiceEndpoints__|habilitado
__Serviços__ | Selecione um ou todos os serviços disponíveis. Durante a versão prévia, serviços com suporte: __"Microsoft.Storage", "Microsoft.Sql"__.

Selecione os serviços para os pontos de extremidade: ![Selecionar Serviços de Ponto de Extremidade de Serviço](media/virtual-network-service-endpoints-portal/vnet-create-flow-services.png)

4. Valide que todas as configurações estão corretas e clique em "Criar".

![Definir ponto de extremidade de serviço](media/virtual-network-service-endpoints-portal/create-vnet-flow.png)

5. Para concluir a garantia dos recursos de serviço do Azure para sua rede virtual, clique na documentação do serviço em [Próximas etapas](#Next%20Steps).


### <a name="validating-service-endpoint-configuration"></a>Validando a configuração do ponto de extremidade de serviço

Confirme se os pontos de extremidade de serviço estão configurados usando as seguintes etapas:

- Em Recursos, clique em "Redes Virtuais". Pesquise a rede virtual.
- Clique no nome da rede virtual e navegue até "Pontos de Extremidade de Serviço"
- Pontos de extremidade configurados mostram "Êxito". Os locais configurados automaticamente também podem ser vistos

![Confirmar a configuração do ponto de extremidade de serviço](media/virtual-network-service-endpoints-portal/vnet-validate-settings.png
)

### <a name="effective-routes-to-validate-endpoint-configuration"></a>Rotas eficientes para validar a configuração de ponto de extremidade

Para exibir a rota efetiva em um NiC (adaptador de rede) na sub-rede, clique em qualquer NIC nessa sub-rede. Em "Suporte + Solução de Problemas", clique em "Rotas eficientes". Se o ponto de extremidade está configurado, você vê uma nova rota "padrão" com prefixos de endereço do serviço como destino e nextHopType como "VirtualNetworkServiceEndpoint".

![Rotas eficientes para pontos de extremidade de serviço](media/virtual-network-service-endpoints-portal/effective-routes.png)

### <a name="setting-up-service-endpoints-for-existing-subnets-in-a-vnet"></a>Configurando pontos de extremidade de serviço para sub-redes existentes em uma rede virtual

1. Em recursos, clique em "Redes virtuais" e procure uma rede virtual existente
2. Clique no nome da rede virtual e navegue até "Pontos de extremidade de serviço"
3. Clique em “Adicionar”. Selecione "Serviço". Você pode criar um ponto de extremidade apenas para um serviço de cada vez. 
4. Selecione todas as sub-redes a que você deseja aplicar o ponto de extremidade. Clique em “Adicionar”

![Configuração do ponto de extremidade de serviço da sub-rede](media/virtual-network-service-endpoints-portal/existing-subnet.png)

### <a name="deleting-service-endpoints"></a>Excluindo pontos de extremidade de serviço
1. Em Recursos, clique em "Redes Virtuais". Procure uma rede virtual existente filtrando pelo nome da rede virtual.
2. Clique no nome da rede virtual e navegue até "Pontos de Extremidade de Serviço"
3. Clique no nome do serviço e clique com o botão direito do mouse na entrada do ponto de extremidade de serviço
4. Selecione “Excluir”

![Exclusão de ponto de extremidade de serviço](media/virtual-network-service-endpoints-portal/delete-endpoint.png)

## <a name="azure-powershell"></a>Azure PowerShell

Pré-requisitos de instalação:

- Instale a última versão do módulo [AzureRm](https://www.powershellgallery.com/packages/AzureRM/) do PowerShell. Se você for novo no Azure PowerShell, consulte [Visão geral do Azure PowerShell](/powershell/azure/overview?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Para iniciar uma sessão do PowerShell, acesse **Iniciar**, insira **powershell** e, em seguida, clique em **PowerShell**.
- No PowerShell, faça logon no Azure inserindo o comando `login-azurermaccount`. A conta deve ter as [permissões](#provisioning) necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

### <a name="get-available-service-endpoints-for-azure-region"></a>Obter pontos de extremidade de serviço disponíveis para a região do Azure

Use o comando a seguir para obter a lista de serviços com suporte para pontos de extremidade em uma região do Azure.
 ```powershell
Get-AzureRmVirtualNetworkAvailableEndpointService -location eastus
```

Saída: 
Nome | ID | Tipo
-----|----|-------
Microsoft.Storage|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage|Microsoft.Network/virtualNetworkEndpointServices
Microsoft.Sql|/subscriptions/xxxx-xxx-xxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql|Microsoft.Network/virtualNetworkEndpointServices

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Adicionar ponto de extremidade de serviço do Armazenamento do Azure a uma sub-rede *mySubnet* ao criar a rede virtual *myVNet*

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix "10.0.1.0/24" -ServiceEndpoint “Microsoft.Storage”

New-AzureRmVirtualNetwork -Name "myVNet" -AddressPrefix "10.0.0.0/16" -Subnet $subnet -ResourceGroupName "myRG" -Location "WestUS"
```
Você pode habilitar vários serviços usando a lista de nomes de serviço separada por vírgulas.
Exemplo: "Microsoft.Storage", "Microsoft.Sql"

Saída esperada:
```
Subnets : [
            {
            "Name": "mySubnet",
             ...
            "ServiceEndpoints": [
              {
                   "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "westus",
                        "eastus"
                                 ]
               }
                                ],
            "ProvisioningState": "Succeeded"
            }
          ]
```

Para concluir a garantia dos recursos de serviço do Azure para sua rede virtual, clique na documentação do serviço em [Próximas etapas](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Adicionar vários pontos de extremidade de serviço a uma sub-rede existente

```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint "Microsoft.Storage", "Microsoft.Sql" | Set-AzureRmVirtualNetwork
```

Saída esperada: 
```
Subnets : [
            {
                "Name": "mySubnet",
                 ...
                "ServiceEndpoints": [
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Storage",
                    "Locations": [
                        "eastus",
                        "westus"
                                 ]
                },
                {
                    "ProvisioningState": "Succeeded",
                    "Service": "Microsoft.Sql",
                    "Locations": [
                        "eastus"
                                 ]
                }
                ],
               "ProvisioningState": "Succeeded"
            }
         ]
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Exibir pontos de extremidade de serviço configurados em uma sub-rede

```powershell
$subnet=Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Get-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"
$subnet.ServiceEndpoints
```
Saída:
```
ProvisioningState Service           Locations
----------------- -------           ---------
Succeeded         Microsoft.Storage {eastus, westus}
Succeeded         Microsoft.Sql     {eastus}
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Excluir pontos de extremidade de serviço em uma sub-rede
```powershell
Get-AzureRmVirtualNetwork -ResourceGroupName "myRG" -Name "myVNet" | Set-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet"  -AddressPrefix "10.0.1.0/24" -ServiceEndpoint $null | Set-AzureRmVirtualNetwork
```

## <a name="azure-cli"></a>CLI do Azure

Pré-requisitos de instalação:
- Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela. Para obter mais informações sobre registro em log, consulte [Introdução à CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest).
 - A conta deve ter as [permissões](#provisioning) necessárias para criar uma rede virtual e um ponto de extremidade de serviço.

 Para obter uma lista completa de comandos para redes virtuais, confira [Comandos de rede virtual da CLI do Azure](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest)

### <a name="get-available-service-endpoints-for-azure-region"></a>Obter pontos de extremidade de serviço disponíveis para a região do Azure

Use o comando a seguir para obter a lista de serviços com suporte para pontos de extremidade em uma região do Azure, por exemplo, “EastUS”.
```azure-cli
az network vnet list-endpoint-services -l eastus
```
Saída:
```
    {
    "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Storage",
    "name": "Microsoft.Storage",
    "type": "Microsoft.Network/virtualNetworkEndpointServices"
     },
     {
     "id": "/subscriptions/xxxx-xxxx-xxxx/providers/Microsoft.Network/virtualNetworkEndpointServices/Microsoft.Sql",
     "name": "Microsoft.Sql",
     "type":   "Microsoft.Network/virtualNetworkEndpointServices"
     }
```

### <a name="add-azure-storage-service-endpoint-to-a-subnet-mysubnet-while-creating-the-virtual-network-myvnet"></a>Adicionar ponto de extremidade de serviço do Armazenamento do Azure a uma sub-rede *mySubnet* ao criar a rede virtual *myVNet*

```azure-cli
az network vnet create -g myRG -n myVNet --address-prefixes 10.0.0.0/16 -l eastUS

az network vnet subnet create -g myRG -n mySubnet --vnet-name myVNet --address-prefix 10.0.1.0/24 --service-endpoints Microsoft.Storage
```

Para adicionar vários pontos de extremidade: --service-endpoints Microsoft.Storage Microsoft.Sql

Saída:
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    }
  ]
}
```

Para concluir a garantia dos recursos de serviço do Azure para sua rede virtual, clique na documentação do serviço em [Próximas etapas](#Next%20Steps).

### <a name="add-multiple-service-endpoints-to-an-existing-subnet"></a>Adicionar vários pontos de extremidade de serviço a uma sub-rede existente

```azure-cli
az network vnet subnet update -g myRG -n mySubnet2 --vnet-name myVNet --service-endpoints Microsoft.Storage Microsoft.Sql
```

Saída esperada:
```
{
  "addressPrefix": "10.0.2.0/24",
  ...
  "name": "mySubnet2",
  ...
  "serviceEndpoints": [
    {
      "locations": [
        "eastus",
        "westus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Storage"
    },
    {
      "locations": [
        "eastus"
      ],
      "provisioningState": "Succeeded",
      "service": "Microsoft.Sql"
    }
  ]
}
```

### <a name="view-service-endpoints-configured-on-a-subnet"></a>Exibir pontos de extremidade de serviço configurados em uma sub-rede

```azure-cli
az network vnet subnet show -g myRG -n mySubnet --vnet-name myVNet
```

### <a name="delete-service-endpoints-on-a-subnet"></a>Excluir pontos de extremidade de serviço em uma sub-rede
```azure-cli
az network vnet subnet update -g myRG -n mySubnet --vnet-name myVNet --service-endpoints ""
```

Saída: 
```
{
  "addressPrefix": "10.0.1.0/24",
  ...
  "name": "mySubnet",
  "networkSecurityGroup": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myRG",
  "resourceNavigationLinks": null,
  "routeTable": null,
  "serviceEndpoints": null
}
```

## <a name="resource-manager-template"></a>Modelo do Resource Manager

### <a name="securing-azure-service-resources-to-vnets"></a>Garantindo recursos do serviço do Azure para VNets

Você pode garantir recursos específicos do Azure à sua rede virtual por meio de pontos de extremidade de serviço.

Baixe o [modelo do Gerenciador de Recursos](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration) de exemplo para garantir uma conta de armazenamento para uma sub-rede em uma rede virtual.

O modelo cria uma rede virtual com 2 sub-redes, uma VM com um NIC em cada uma das sub-redes. Permite o ponto de extremidade em uma sub-rede e garante uma conta de armazenamento para essa sub-rede.

Você pode baixar o modelo e modificar partes dele para ficar de acordo com o seu cenário.

As instruções são fornecidas com o modelo para implantação do modelo usando o portal do Azure, o PowerShell ou a CLI do Azure. Verifique se você tem as [permissões](#provisioning) necessárias para configurar o ponto de extremidade e garantir a conta.

Para garantir recursos do Azure para uma sub-rede:

- um ponto de extremidade de serviço deverá ser configurado na sub-rede.
- o recurso deverá ser garantido para a rede virtual pela adição de uma regra de rede virtual no recurso.

### <a name="deleting-service-endpoints-with-resources-secured-to-the-subnet"></a>Excluindo pontos de extremidade de serviço com recursos garantidos para a sub-rede
Se os recursos do serviço do Azure estão garantidos para a sub-rede e o ponto de extremidade de serviço é excluído, não é mais possível acessar o recurso da sub-rede.
Habilitar novamente o ponto de extremidade não restaura o acesso a recursos garantidos para a sub-rede anteriormente.

Para garantir o recurso de serviço para essa sub-rede novamente, você precisa:

- habilitar o ponto de extremidade novamente
- remover a regra de rede virtual antiga do recurso
- adicionar uma nova regra garantindo os recursos para a sub-rede

## <a name="provisioning"></a>Provisionamento

Pontos de extremidade de serviço podem ser configurados em redes virtuais de forma independente por um usuário com acesso de gravação à rede virtual.

Para garantir recursos de serviço do Azure para uma rede virtual, o usuário deve ter permissão "Microsoft.Network/JoinServicetoaSubnet" para as sub-redes que estão sendo adicionadas. Essa permissão está incluída nas funções de administrador de serviço internas por padrão e pode ser modificada com a criação de funções personalizadas.

Saiba mais sobre [funções internas](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles) e como atribuir permissões específicas a [funções personalizadas](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-custom-roles).

As VNets e os recursos de serviço do Azure podem estar na mesma assinatura ou em assinaturas diferentes. Se eles estão em assinaturas diferentes, os recursos devem estar no mesmo locatário do AD (Active Directory) durante essa versão prévia.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como garantir os recursos de serviço para VNets, consulte os seguintes links:

[Garantindo contas do Armazenamento do Azure para redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security)

[Garantindo SQL Azure para redes virtuais](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview)
