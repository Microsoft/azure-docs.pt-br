---
title: Emparelhar duas redes virtuais – script de exemplo da CLI do Azure
description: Cria e conecta duas redes virtuais na mesma região através da rede do Azure usando um script de exemplo da CLI do Azure.
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
ms.openlocfilehash: 902d6643420ff6bdafcf52278f3a1c0df28647ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98217521"
---
# <a name="peer-two-virtual-networks-with-an-azure-cli-script-sample"></a>Emparelhar duas redes virtuais com um script de exemplo da CLI do Azure

Esse script cria e conecta duas redes virtuais na mesma região através da rede do Azure. Depois de executar o script, você terá um emparelhamento entre duas redes virtuais.

Você pode executar o script do Azure [Cloud Shell](https://shell.azure.com/bash) ou de uma instalação local da CLI do Azure. Se você usar a CLI localmente, este script requer que você esteja executando a versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]


## <a name="sample-script"></a>Exemplo de script

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.sh "Peer two networks")]

## <a name="clean-up-deployment"></a>Limpar a implantação 

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir para criar um grupo de recursos, uma máquina virtual e todos os recursos relacionados. Cada comando na tabela a seguir redireciona para a documentação específica do comando:

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az network vnet create](/cli/azure/network/vnet) | Cria uma sub-rede e uma rede virtual do Azure. |
| [criar emparelhamento de VNET de rede virtual az](/cli/azure/network/vnet/peering) | Cria um emparelhamento entre duas redes virtuais.  |
| [az group delete](/cli/azure/vm/extension) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).

Amostras de script CLI de rede virtual adicionais podem ser encontradas em [Amostras de CLI de rede virtual](../cli-samples.md).