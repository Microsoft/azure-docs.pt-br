---
title: Exemplo de script da CLI do Azure - Como criar uma VM do Windows Server 2016 com NLB | Microsoft Docs
description: Exemplo de script da CLI do Azure - Como criar uma VM do Windows Server 2016 com NLB
services: virtual-machines-windows
documentationcenter: virtual-machines
author: rickstercdn
manager: gwallace
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rclaus
ms.openlocfilehash: b1cbb48bc6dcfcaf20d4331b93ba61e57979692c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703245"
---
# <a name="load-balance-traffic-between-highly-available-virtual-machines"></a>Como balancear a carga de tráfego entre máquinas virtuais altamente disponíveis

Este exemplo de script cria todos os componentes necessários para executar várias máquinas virtuais do Ubuntu configuradas em uma alta disponibilidade e configuração de balanceamento de carga. Após a execução do script, você terá três máquinas virtuais, associadas a um Conjunto de Disponibilidade do Azure, e acessíveis por meio de um Azure Load Balancer.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-vm-nlb/create-windows-vm-nlb.sh "Quick Create VM")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos para criar um grupo de recursos, uma máquina virtual, um conjunto de disponibilidade, um balanceador de carga e todos os recursos relacionados. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](https://docs.microsoft.com/cli/azure/network/vnet) | Cria uma sub-rede e uma rede virtual do Azure. |
| [az network public-ip create](https://docs.microsoft.com/cli/azure/network/public-ip) | Cria um endereço IP público com um endereço IP estático e um nome DNS associado. |
| [az network lb create](https://docs.microsoft.com/cli/azure/network/lb) | Cria um balanceador de carga de rede (NLB) do Azure. |
| [az network lb probe create](https://docs.microsoft.com/cli/azure/network/lb/probe) | Cria uma investigação NLB. Uma investigação NLB é usada para monitorar cada VM no conjunto do NLB. Se qualquer VM ficar inacessível, o tráfego não é roteado para a máquina virtual. |
| [az network lb rule create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Cria uma regra NLB. Neste exemplo, uma regra é criada para a porta 80. Conforme o tráfego HTTP chega ao NLB, ele é roteado para a porta 80, uma das VMs no conjunto do NLB. |
| [az network lb inbound-nat-rule create](https://docs.microsoft.com/cli/azure/network/lb/inbound-nat-rule) | Cria uma regra de conversão de endereços de rede (NAT) do NLB.  Regras de NAT mapeiam uma porta do NLB para uma porta em uma máquina virtual. Neste exemplo, é criada uma regra de NAT para o tráfego SSH para cada VM no conjunto do NLB.  |
| [az network nsg create](https://docs.microsoft.com/cli/azure/network/nsg) | Cria um grupo de segurança de rede (NSG), que é um limite de segurança entre a Internet e a máquina virtual. |
| [az network nsg rule create](https://docs.microsoft.com/cli/azure/network/nsg/rule) | Cria uma regra NSG para permitir o tráfego de entrada. Neste exemplo, a porta 22 é aberta para o tráfego SSH. |
| [az network nic create](https://docs.microsoft.com/cli/azure/network/nic) | Cria uma placa de rede virtual e a anexa à rede virtual, à sub-rede e ao NSG. |
| [az vm availability-set create](https://docs.microsoft.com/cli/azure/network/lb/rule) | Cria um conjunto de disponibilidade. Conjuntos de disponibilidade garantem o funcionamento de aplicativos, distribuindo as máquinas virtuais em recursos físicos, de modo que, se ocorrer uma falha, todo o conjunto não é afetado. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set) | Cria a máquina virtual e a conecta a placa de rede, a rede virtual, a sub-rede e o NSG. Este comando também especifica a imagem da máquina virtual a ser usada e as credenciais administrativas.  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](https://docs.microsoft.com/cli/azure).

Exemplos de script CLI máquinas virtuais adicionais podem ser encontrados na [documentação da VM do Windows do Azure](../windows/cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
