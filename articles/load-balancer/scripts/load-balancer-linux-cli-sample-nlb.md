---
title: Balancear o tráfego de VMs para HA - CLI do Azure - Azure Load Balancer
description: Este exemplo de script da CLI do Azure mostra como balancear a carga do tráfego para VMs para alta disponibilidade
services: load-balancer
documentationcenter: load-balancer
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: azurecli
ms.topic: sample
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: allensu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3aaa7246806a1a7f1352481d26df00091e52a440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789997"
---
# <a name="azure-cli-script-example-load-balance-traffic-to-vms-for-high-availability"></a>Exemplo de script da CLI do Azure: Balancear a carga do tráfego para VMs para alta disponibilidade

Este exemplo de script da CLI do Azure cria todos os componentes necessários para executar várias máquinas virtuais Ubuntu definidas em uma configuração de alta disponibilidade e balanceamento de carga. Após a execução do script, você terá três máquinas virtuais, associadas a um Conjunto de Disponibilidade do Azure, e acessíveis por meio de um Azure Load Balancer. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create) | Cria uma sub-rede e uma rede virtual do Azure. |
| [az network public-ip create](/cli/azure/network/public-ip#az_network_public_ip_create) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network lb create](/cli/azure/network/lb#az_network_lb_create) | Cria um Azure Load Balancer. |
| [az network lb probe create](/cli/azure/network/lb/probe#az_network_lb_probe_create) | Cria uma investigação do balanceador de carga. Uma investigação do balanceador de carga é usada para monitorar cada VM no conjunto de balanceadores de carga. Se qualquer VM ficar inacessível, o tráfego não é roteado para a máquina virtual. |
| [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria uma regra de balanceador de carga. Neste exemplo, uma regra é criada para a porta 80. Conforme o tráfego HTTP chega ao balanceador de carga, ele é roteado para a porta 80 em uma das VMs no conjunto de LB. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az_network_lb_inbound_nat_rule_create) | Cria uma regra de NAT (conversão de endereços de rede) do balanceador de carga.  Regras de NAT mapeiam uma porta do balanceador de carga para uma porta em uma VM. Neste exemplo, é criada uma regra de NAT para o tráfego SSH para cada VM no conjunto de balanceadores de carga.  |
| [az network nsg create](/cli/azure/network/nsg#az_network_nsg_create) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az_network_nsg_rule_create) | Cria uma regra NSG para permitir o tráfego de entrada. Neste exemplo, a porta 22 é aberta para o tráfego SSH. |
| [az network nic create](/cli/azure/network/nic#az_network_nic_create) | Cria uma placa de rede virtual e a anexa à rede virtual, à sub-rede e ao NSG. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az_network_lb_rule_create) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantem o funcionamento de aplicativos, distribuindo as máquinas virtuais em recursos físicos, de modo que, se ocorrer uma falha, todo o conjunto não é afetado. |
| [az vm create](/cli/azure/vm#az_vm_create) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem da máquina virtual a ser usada e as credenciais administrativas.  |
| [az group delete](/cli/azure/vm/extension#az_vm_extension_set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos adicionais de script de CLI de rede do Azure podem ser encontrados na [Documentação de rede do Azure](../cli-samples.md).