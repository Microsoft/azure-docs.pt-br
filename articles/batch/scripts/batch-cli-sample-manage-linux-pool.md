---
title: Exemplo de Script da CLI do Azure – Pool do Linux em Lote
description: O script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um pool dos nós de computação do Linux no Lote do Azure.
ms.topic: sample
ms.date: 01/29/2018
ms.custom: devx-track-azurecli
ms.openlocfilehash: b5e1bdccefffa7803fbe744e27c1b36ca719560d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768279"
---
# <a name="cli-example-create-and-manage-a-linux-pool-in-azure-batch"></a>Exemplo da CLI: criar e gerenciar um pool do Linux em Lote do Azure

O script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um pool dos nós de computação do Linux no Lote do Azure.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.20 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-linux.sh "Manage Linux Virtual Machine Pool")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Execute o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os comandos a seguir. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az batch account create](/cli/azure/batch/account#az_batch_account_create) | Cria a conta do Lote. |
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentica na conta do Lote especificada para interação adicional com a CLI.  |
| [az batch pool node-agent-skus list](../batch-linux-nodes.md#list-of-virtual-machine-images) | Lista as informações de imagem e SKUs do agente de nó disponíveis.  |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Cria um pool de nós de computação.  |
| [az batch pool resize](/cli/azure/batch/pool#az_batch_pool_resize) | Redimensiona o número de VMs em execução no pool especificado.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Exibe as propriedades de um pool.  |
| [az batch node list](/cli/azure/batch/node#az_batch_node_list) | Lista todos os nós de computação no pool especificado.  |
| [az batch node reboot](/cli/azure/batch/node#az_batch_node_reboot) | Reinicia o nó de computação especificado.  |
| [az batch node delete](/cli/azure/batch/node#az_batch_node_delete) | Exclui os nós listados do pool especificado.  |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
