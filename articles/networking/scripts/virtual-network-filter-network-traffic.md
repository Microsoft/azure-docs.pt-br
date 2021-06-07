---
title: Exemplo de script da CLI do Azure - Filtrar o tráfego de rede da VM | Microsoft Docs
description: Use um script de CLI do Azure para filtrar o tráfego de rede de VM (máquina virtual) de entrada e saída com sub-redes de front-end e back-end.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.openlocfilehash: 61f2441d68954a167b9887a4dfd4b99a53c14166
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "88037211"
---
# <a name="use-an-azure-cli-script-to-filter-inbound-and-outbound-vm-network-traffic"></a>Usar um script de CLI do Azure para filtrar o tráfego de rede da VM de entrada e saída

Este exemplo de script cria uma rede virtual com sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a HTTP, HTTPS e SSH, enquanto o tráfego de rede de saída para a Internet da sub-rede de back-end não é permitido. Depois de executar o script, você terá uma máquina virtual com dois NICs. Cada NIC pode estar conectado a uma sub-rede diferente.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma rede virtual do Azure e uma sub-rede front-end. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Cria uma sub-rede back-end. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Associa os NSGs às sub-redes. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria um endereço IP público para acessar a VM da Internet. |
| [az network nic create](/cli/azure/network/nic) | Cria as interfaces de rede virtual e as anexa às sub-redes de front-end e back-end da rede virtual. |
| [az network nsg create](/cli/azure/network/nsg) | Cria NSG (grupos de segurança de rede) associados às sub-redes de front-end e back-end. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Cria regras NSG que permitem ou bloqueiam portas específicas para sub-redes específicas. |
| [az vm create](/cli/azure/vm) | Cria máquinas virtuais e anexa um NIC a cada VM. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas. |
| [az group delete](/cli/azure/group) | Exclui um grupo de recursos e todos os seus recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos de script da CLI de rede adicional podem ser encontrados na [documentação de visão geral da rede do Azure](../cli-samples.md)
