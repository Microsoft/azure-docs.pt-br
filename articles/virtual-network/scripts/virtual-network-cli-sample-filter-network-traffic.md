---
title: Filtrar o tráfego da rede de VMs – exemplo de script da CLI do Azure
description: Filtrar o tráfego de rede de VMs (máquinas virtuais) de entrada e de saída usando uma amostra de script da CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 86d33ba411c4a0294454a8e1421c35b694feb65d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87492966"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic-using-an-azure-cli-script-sample"></a>Filtrar o tráfego de rede de VMs de entrada e de saída usando uma amostra de script da CLI do Azure

Este exemplo de script cria uma rede virtual com sub-redes de front-end e back-end. O tráfego de rede de entrada para a sub-rede de front-end é limitado a HTTP, HTTPS e SSH, enquanto o tráfego de rede de saída para a Internet da sub-rede de back-end não é permitido. Depois de executar o script, você terá uma máquina virtual com dois NICs. Cada NIC pode estar conectado a uma sub-rede diferente.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/bash) ou de uma instalação local da CLI do Azure. Se você usar a CLI localmente, este script requer que você esteja executando a versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma rede virtual e grupos de segurança de rede. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

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

Amostras de script CLI de rede virtual adicionais podem ser encontradas em [Amostras de CLI de rede virtual](../cli-samples.md).
