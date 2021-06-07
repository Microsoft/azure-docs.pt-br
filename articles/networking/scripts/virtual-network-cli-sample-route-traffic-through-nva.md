---
title: CLI do Azure amostra de script – rotear o tráfego por meio de uma solução de virtualização de rede
description: Exemplo de script da CLI do Azure - Rotear o tráfego por meio de uma solução de virtualização de rede de firewall.solução de virtualização.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: a349f8160e8ab5b6459b2085e21e7368570c57db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87503830"
---
# <a name="use-an-azure-cli-script-to-route-traffic-through-a-network-virtual-appliance"></a>Usar um script de CLI do Azure para rotear o tráfego por meio de uma solução de virtualização de rede

Este exemplo de script cria uma rede virtual com sub-redes de front-end e back-end. Ele também cria uma VM com o encaminhamento de IP habilitado para rotear o tráfego entre as duas sub-redes. Depois de executar o script, você pode implantar o software de rede, como um aplicativo de firewall, à VM.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exemplo de script


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/route-traffic-through-nva/route-traffic-through-nva.sh "Route traffic through a network virtual appliance")]

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
| [az network subnet create](/cli/azure/network/vnet/subnet) | Cria as sub-redes back-end e DMZ. |
| [az network public-ip create](/cli/azure/network/public-ip) | Cria um endereço IP público para acessar a VM da Internet. |
| [az network nic create](/cli/azure/network/nic) | Cria uma interface de rede virtual e habilita o encaminhamento IP nela. |
| [az network nsg create](/cli/azure/network/nsg) | Cria um grupo de segurança de rede (NSG). |
| [az network nsg rule create](/cli/azure/network/nsg/rule) | Cria regras de NSG que permitem as portas HTTP e HTTPS de entrada para a VM. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet)| Associa os NSGs e tabelas de rotas às sub-redes. |
| [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create)| Cria uma tabela de rotas com todas as rotas. |
| [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create)| Cria as rotas para rotear o tráfego entre sub-redes e a Internet por meio da VM. |
| [az vm create](/cli/azure/vm) | Cria uma máquina virtual e anexa o NIC a ela. Este comando também especifica a imagem de máquina virtual a ser usada e as credenciais administrativas. |
| [az group delete](/cli/azure/group) | Exclui um grupo de recursos e todos os seus recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Exemplos de script da CLI de rede adicional podem ser encontrados na [documentação de visão geral da rede do Azure](../cli-samples.md)
