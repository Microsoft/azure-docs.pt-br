---
title: Exemplo de Script da CLI do Azure – Pool do Windows em Lote
description: Esse script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um pool dos nós de computação do Windows no Lote do Azure.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 773699dde9342a4b230a08471a289a56fca7e308
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768189"
---
# <a name="cli-example-create-and-manage-a-windows-pool-in-azure-batch"></a>Exemplo da CLI: criar e gerenciar um pool do Windows em Lote do Azure

Esse script demonstra alguns dos comandos disponíveis na CLI do Azure para criar e gerenciar um pool dos nós de computação do Windows no Lote do Azure. Um pool do Windows pode ser configurado de duas maneiras, com uma configuração de Serviços de Nuvem ou com uma configuração de Máquina Virtual. Esse exemplo mostra como criar um pool do Windows com a configuração de Serviços de Nuvem.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.20 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/manage-pool/manage-pool-windows.sh "Manage Windows Cloud Services Pool")]

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
| [az batch account login](/cli/azure/batch/account#az_batch_account_login) | Autentica na conta do Lote especificada para interação adicional com a CLI. |
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Cria um pool de nós de computação.  |
| [az batch pool set](/cli/azure/batch/pool#az_batch_pool_set) | Atualiza as propriedades de um pool.  |
| [az batch pool autoscale enable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_enable) | Habilita o dimensionamento automático em um pool e aplica uma fórmula.  |
| [az batch pool show](/cli/azure/batch/pool#az_batch_pool_show) | Exibe as propriedades de um pool.  |
| [az batch pool autoscale disable](/cli/azure/batch/pool/autoscale#az_batch_pool_autoscale_disable) | Desabilita o dimensionamento automático em um pool. |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
