---
title: Atualizar ou excluir um balanceador de carga existente usado por conjuntos de dimensionamento de máquinas virtuais
titleSuffix: Update or delete an existing load balancer used by virtual machine scale sets
description: Com este artigo de instruções, introdução aos conjuntos de dimensionamento de máquinas virtuais e Standard Load Balancer do Azure.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: irenehua
ms.openlocfilehash: 52f2a2ed301bf734ad605a2ee68a0ab672a97014
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102218716"
---
# <a name="update-or-delete-a-load-balancer-used-by-virtual-machine-scale-sets"></a>Atualizar ou excluir um balanceador de carga usado por conjuntos de dimensionamento de máquinas virtuais

Ao trabalhar com conjuntos de dimensionamento de máquinas virtuais e uma instância do Azure Load Balancer, você pode:

- Adicionar, atualizar e excluir regras.
- Adicionar configurações.
- Exclua o balanceador de carga.

## <a name="set-up-a-load-balancer-for-scaling-out-virtual-machine-scale-sets"></a>Configurar um balanceador de carga para dimensionar conjuntos de dimensionamento de máquinas virtuais

Certifique-se de que a instância do Azure Load Balancer tenha um [pool de NAT de entrada](/cli/azure/network/lb/inbound-nat-pool) configurado e que o conjunto de dimensionamento de máquinas virtuais seja colocado no pool de back-end do balanceador de carga. Load Balancer criará automaticamente novas regras NAT de entrada no pool de NAT de entrada quando novas instâncias de máquina virtual forem adicionadas ao conjunto de dimensionamento de máquinas virtuais.

Para verificar se o pool de NAT de entrada está configurado corretamente:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No menu do lado esquerdo, selecione **Todos os recursos**. Em seguida, selecione **MyLoadBalancer** na lista de recursos.
1. Em **configurações**, selecione **regras de NAT de entrada**. No painel direito, se você vir uma lista de regras criadas para cada instância individual no conjunto de dimensionamento de máquinas virtuais, você estará pronto para escalar verticalmente a qualquer momento.

## <a name="add-inbound-nat-rules"></a>Adicionar regras de NAT de entrada

Não é possível adicionar regras de NAT de entrada individuais. Mas você pode adicionar um conjunto de regras de NAT de entrada com um intervalo de portas de front-end definido e porta de back-end para todas as instâncias no conjunto de dimensionamento de máquinas virtuais.

Para adicionar um conjunto completo de regras de NAT de entrada para os conjuntos de dimensionamento de máquinas virtuais, primeiro crie um pool de NAT de entrada no balanceador de carga. Em seguida, faça referência ao pool de NAT de entrada do perfil de rede do conjunto de dimensionamento de máquinas virtuais. É mostrado um exemplo completo usando a CLI.

O novo pool de NAT de entrada não deve ter um intervalo de portas de front-end sobrepostos com pools NAT de entrada existentes. Para exibir os pools NAT de entrada existentes que estão configurados, use este [comando da CLI](/cli/azure/network/lb/inbound-nat-pool#az_network_lb_inbound_nat_pool_list):
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIp
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
```
## <a name="update-inbound-nat-rules"></a>Atualizar regras de NAT de entrada

As regras de NAT de entrada individuais não podem ser atualizadas. Mas você pode atualizar um conjunto de regras de NAT de entrada com um intervalo de portas de front-end definido e uma porta de back-end para todas as instâncias no conjunto de dimensionamento de máquinas virtuais.

Para atualizar um conjunto completo de regras NAT de entrada para conjuntos de dimensionamento de máquinas virtuais, atualize o pool NAT de entrada no balanceador de carga.
    
```azurecli-interactive
az network lb inbound-nat-pool update 
        -g MyResourceGroup 
        --lb-name MyLb 
        -n MyNatPool
        --protocol Tcp 
        --backend-port 8080
```

## <a name="delete-inbound-nat-rules"></a>Excluir regras de NAT de entrada

As regras de NAT de entrada individuais não podem ser excluídas, mas você pode excluir o conjunto inteiro de regras de NAT de entrada excluindo o pool de NAT de entrada.

Para excluir o pool de NAT, primeiro remova-o do conjunto de dimensionamento. Um exemplo completo usando a CLI é mostrado aqui:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools
     az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
    az network lb inbound-nat-pool delete
       --resource-group MyResourceGroup
       -–lb-name MyLoadBalancer
       --name MyNatPool
```

## <a name="add-multiple-ip-configurations"></a>Adicionar várias configurações de IP

Para adicionar várias configurações de IP:

1. No menu do lado esquerdo, selecione **Todos os recursos**. Em seguida, selecione **MyLoadBalancer** na lista de recursos.
1. Em **Configurações**, selecione **configuração IP de Front-end**. Em seguida, selecione **Adicionar**.
1. Na página **Adicionar endereço IP de front-end** , insira os valores e selecione **OK**.
1. Siga as [etapas 5](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) e [6](./load-balancer-multiple-ip.md#step-5-configure-the-health-probe) neste tutorial se forem necessárias novas regras de balanceamento de carga.
1. Crie um novo conjunto de regras NAT de entrada usando as configurações de IP de front-end recém-criadas, se necessário. Um exemplo é encontrado na seção anterior.

## <a name="multiple-virtual-machine-scale-sets-behind-a-single-load-balancer"></a>Vários conjuntos de dimensionamento de máquinas virtuais por trás de um único Load Balancer

Crie um pool NAT de entrada no Load Balancer, faça referência ao pool NAT de entrada no perfil de rede de um conjunto de dimensionamento de máquinas virtuais e, finalmente, atualize as instâncias para que as alterações entrem em vigor. Repita as etapas para todos os conjuntos de dimensionamento de máquinas virtuais.

Certifique-se de criar pools NAT de entrada separados com intervalos de porta de front-end não sobrepostos.
  
```azurecli-interactive
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool 
          --protocol Tcp 
          --frontend-port-range-start 80 
          --frontend-port-range-end 89 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS
          
  az network lb inbound-nat-pool create 
          -g MyResourceGroup 
          --lb-name MyLb
          -n MyNatPool2
          --protocol Tcp 
          --frontend-port-range-start 100 
          --frontend-port-range-end 109 
          --backend-port 80 
          --frontend-ip-name MyFrontendIpConfig2
  az vmss update 
          -g MyResourceGroup 
          -n myVMSS2 
          --add virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerInboundNatPools "{'id':'/subscriptions/mySubscriptionId/resourceGroups/MyResourceGroup/providers/Microsoft.Network/loadBalancers/MyLb/inboundNatPools/MyNatPool2'}"
            
  az vmss update-instances
          -–instance-ids *
          --resource-group MyResourceGroup
          --name MyVMSS2
```

## <a name="delete-the-front-end-ip-configuration-used-by-the-virtual-machine-scale-set"></a>Excluir a configuração de IP de front-end usada pelo conjunto de dimensionamento de máquinas virtuais

Para excluir a configuração de IP de front-end em uso pelo conjunto de dimensionamento:

 1. Primeiro, exclua o pool NAT de entrada (o conjunto de regras NAT de entrada) que faz referência à configuração de IP de front-end. As instruções sobre como excluir as regras de entrada são encontradas na seção anterior.
 1. Exclua a regra de balanceamento de carga que faz referência à configuração de IP de front-end.
 1. Exclua a configuração de IP de front-end.

## <a name="delete-a-load-balancer-used-by-a-virtual-machine-scale-set"></a>Excluir um balanceador de carga usado por um conjunto de dimensionamento de máquinas virtuais

Para excluir a configuração de IP de front-end em uso pelo conjunto de dimensionamento:

 1. Primeiro, exclua o pool NAT de entrada (o conjunto de regras NAT de entrada) que faz referência à configuração de IP de front-end. As instruções sobre como excluir as regras de entrada são encontradas na seção anterior.
 1. Exclua a regra de balanceamento de carga que faz referência ao pool de back-end que contém o conjunto de dimensionamento de máquinas virtuais.
 1. Remova a `loadBalancerBackendAddressPool` referência do perfil de rede do conjunto de dimensionamento de máquinas virtuais.
 
 Um exemplo completo usando a CLI é mostrado aqui:

```azurecli-interactive
    az vmss update
       --resource-group MyResourceGroup
       --name MyVMSS
       --remove virtualMachineProfile.networkProfile.networkInterfaceConfigurations[0].ipConfigurations[0].loadBalancerBackendAddressPools
    az vmss update-instances 
       --instance-ids "*" 
       --resource-group MyResourceGroup
       --name MyVMSS
```
Por fim, exclua o recurso de balanceador de carga.
 
## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre Azure Load Balancer e conjuntos de dimensionamento de máquinas virtuais, leia mais sobre os conceitos.

> [Azure Load Balancer com conjuntos de dimensionamento de máquinas virtuais](load-balancer-standard-virtual-machine-scale-sets.md)
