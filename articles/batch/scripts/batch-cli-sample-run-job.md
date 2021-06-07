---
title: Exemplo de Script da CLI do Azure – Execute um trabalho em lotes
description: Esse script cria um trabalho no Lote e adiciona uma série de tarefas ao trabalho. Ele também demonstra como monitorar um trabalho e suas tarefas.
ms.topic: sample
ms.date: 12/12/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e51fa96370b46c9a5a5d5ed6fd7593a06e45430
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768153"
---
# <a name="cli-example-run-a-job-and-tasks-with-azure-batch"></a>Exemplo de CLI: executar um trabalho e tarefas com o Lote do Azure

Esse script cria um trabalho no Lote e adiciona uma série de tarefas ao trabalho. Ele também demonstra como monitorar um trabalho e suas tarefas. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este tutorial requer a versão 2.0.20 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada. 

## <a name="example-script"></a>Script de exemplo

[!code-azurecli-interactive[main](../../../cli_scripts/batch/run-job/run-job.sh "Run Job")]

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
| [az batch pool create](/cli/azure/batch/pool#az_batch_pool_create) | Cria um pool de nós de computação.  |
| [az batch job create](/cli/azure/batch/job#az_batch_job_create) | Cria um trabalho do Lote.  |
| [az batch task create](/cli/azure/batch/task#az_batch_task_create) | Adiciona uma tarefa ao trabalho do Lote especificado.  |
| [az batch job set](/cli/azure/batch/job#az_batch_job_set) | Atualiza as propriedades de um trabalho do Lote.  |
| [az batch job show](/cli/azure/batch/job#az_batch_job_show) | Recupera detalhes de um trabalho especificado do Lote.  |
| [az batch task show](/cli/azure/batch/task#az_batch_task_show) | Recupera os detalhes de uma tarefa do trabalho do Lote especificado.  |
| [az group delete](/cli/azure/group#az_group_delete) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a CLI do Azure, veja a [documentação da CLI do Azure](/cli/azure).
