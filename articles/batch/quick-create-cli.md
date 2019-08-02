---
title: Início rápido do Azure - Executar trabalho do Lote - CLI
description: Aprenda rapidamente a executar um trabalho do Lote com a CLI do Azure.
services: batch
author: laurenhughes
manager: gwallace
ms.service: batch
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 07/03/2018
ms.author: lahugh
ms.custom: mvc
ms.openlocfilehash: 72c3244fbd9c8d547e35c31f0cd6e659e367e21a
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68322502"
---
# <a name="quickstart-run-your-first-batch-job-with-the-azure-cli"></a>Início Rápido: Executar o primeiro trabalho em Lote com a CLI do Azure

A CLI do Azure é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia de início rápido mostra como usar a CLI do Azure para criar uma conta do Lote, um *pool* de nós de computação (máquinas virtuais) e um *trabalho* que executa *tarefas* no pool. Cada tarefa do exemplo executa um comando básico em um dos nós do pool. Depois de concluir este guia de início rápido, você entenderá os conceitos principais do serviço Lote e estará pronto para experimentar o Lote com cargas de trabalho mais realistas em maior escala.

[!INCLUDE [quickstarts-free-trial-note.md](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.20 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az-group-create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus2*.

```azurecli-interactive 
az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Você pode vincular uma conta de Armazenamento do Azure com sua conta do Lote. Embora não seja necessário para este guia de início rápido, a conta de armazenamento é útil para implantar aplicativos e armazenar dados de entrada e saída para a maioria das cargas de trabalho da vida real. Crie uma conta de armazenamento em seu grupo de recursos com o comando [az storage account create](/cli/azure/storage/account#az-storage-account-create).

```azurecli-interactive
az storage account create \
    --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus2 \
    --sku Standard_LRS
```

## <a name="create-a-batch-account"></a>Criar uma conta do Batch

Criar uma conta do Lote com o comando [az batch account create](/cli/azure/batch/account#az-batch-account-create). Você precisa de uma conta para criar recursos de computação (pools de nós de computação) e trabalhos do Lote.

O exemplo a seguir cria uma conta do Lote denominada *mybatchaccount* em *myResourceGroup* e vincula a conta de armazenamento que você criou.  

```azurecli-interactive 
az batch account create \
    --name mybatchaccount \
    --storage-account mystorageaccount \
    --resource-group myResourceGroup \
    --location eastus2
```

Para criar e gerenciar trabalhos e pools de computação, você precisa autenticar com o Lote. Faça logon na conta com o comando [az batch account login](/cli/azure/batch/account#az-batch-account-login). Depois que você fizer logon, seus comandos `az batch` usarão esse contexto de conta.

```azurecli-interactive 
az batch account login \
    --name mybatchaccount \
    --resource-group myResourceGroup \
    --shared-key-auth
```

## <a name="create-a-pool-of-compute-nodes"></a>Criar um pool de nós de computação

Agora que você tem uma conta do Lote, crie um pool de exemplo de nós de computação Linux usando o comando [az batch pool create](/cli/azure/batch/pool#az-batch-pool-create). O exemplo a seguir cria um pool chamado *mypool* com dois nós tamanho *Standard_A1_v2* executando o Ubuntu 16.04 LTS. O tamanho de nó sugerido oferece um bom equilíbrio entre desempenho e custo para este exemplo rápido.
 
```azurecli-interactive
az batch pool create \
    --id mypool --vm-size Standard_A1_v2 \
    --target-dedicated-nodes 2 \
    --image canonical:ubuntuserver:16.04-LTS \
    --node-agent-sku-id "batch.node.ubuntu 16.04" 
```

O Lote cria o pool imediatamente, mas leva alguns minutos para alocar e iniciar os nós de computação. Durante esse tempo, o pool está no estado `resizing`. Para ver o status do pool, execute o comando [az batch pool show](/cli/azure/batch/pool#az-batch-pool-show). Este comando mostra todas as propriedades do pool, e você pode consultar propriedades específicas. O seguinte comando obtém o estado de alocação do pool:

```azurecli-interactive
az batch pool show --pool-id mypool \
    --query "allocationState"
```

Continue as etapas a seguir para criar um trabalho e tarefas enquanto o estado do pool está sendo alterado. O pool está pronto para executar tarefas quando o estado de alocação é `steady` e todos os nós estão sendo executados. 

## <a name="create-a-job"></a>Criar um trabalho

Agora que você tem um pool, crie um trabalho para executar nele.  Um trabalho do Lote é um grupo lógico para uma ou mais tarefas. Um trabalho inclui configurações comuns às tarefas, como prioridade e o pool onde elas devem ser executadas. Crie um trabalho do Lote usando o comando [az batch job create](/cli/azure/batch/job#az-batch-job-create). O exemplo a seguir cria um trabalho *myjob* no pool *mypool*. Inicialmente, o trabalho não tem nenhuma tarefa.

```azurecli-interactive 
az batch job create \
    --id myjob \
    --pool-id mypool
```

## <a name="create-tasks"></a>Criar tarefas

Agora use o comando [az batch task create](/cli/azure/batch/task#az-batch-task-create) para criar algumas tarefas e executar o trabalho. Neste exemplo, você criará quatro tarefas idênticas. Cada tarefa executa uma `command-line` para exibir as variáveis de ambiente do Lote em um nó de computação e, em seguida, aguardar 90 segundos. Ao usar o Lote, essa linha de comando ficará onde você especifica seu aplicativo ou script. O Lote fornece várias maneiras para implantar aplicativos e scripts em nós de computação.

O script Bash a seguir cria quatro tarefas paralelas (*mytask1* a *mytask4*).

```azurecli-interactive 
for i in {1..4}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'printenv | grep AZ_BATCH; sleep 90s'"
done
```

A saída do comando mostra as configurações para cada uma das tarefas. O Lote distribui as tarefas para os nós de computação.

## <a name="view-task-status"></a>Exibir status da tarefa

Depois de criar uma tarefa, o Lote a enfileira para ser executada no pool. Quando um nó fica disponível para executá-lo, a tarefa é executada.

Use o comando [az batch task show](/cli/azure/batch/task#az-batch-task-show) para exibir o status das tarefas do Lote. O exemplo a seguir mostra detalhes sobre *mytask1* em execução em um dos nós do pool.

```azurecli-interactive 
az batch task show \
    --job-id myjob \
    --task-id mytask1
```

A saída do comando inclui muitos detalhes, mas anote o `exitCode` da linha de comando da tarefa e o `nodeId`. Um `exitCode` de 0 indica que a linha de comando da tarefa foi concluída com êxito. O `nodeId` indica a ID do nó de pool em que a tarefa foi executada.

## <a name="view-task-output"></a>Exibir saída da tarefa

Para listar os arquivos criados por uma tarefa em um nó de computação, use o comando [az batch task file list](/cli/azure/batch/task). O comando abaixo lista os arquivos criados por *mytask1*: 

```azurecli-interactive 
az batch task file list \
    --job-id myjob \
    --task-id mytask1 \
    --output table
```

A saída deverá ser semelhante a esta:

```
Name        URL                                                                                         Is Directory      Content Length
----------  ------------------------------------------------------------------------------------------  --------------  ----------------
stdout.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stdout.txt  False                  695
certs       https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/certs       True
wd          https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/wd          True
stderr.txt  https://mybatchaccount.eastus2.batch.azure.com/jobs/myjob/tasks/mytask1/files/stderr.txt  False                     0

```

Para baixar um dos arquivos de saída em um diretório local, use o comando [az batch task file download](/cli/azure/batch/task). Neste exemplo, a saída da tarefa está em `stdout.txt`. 

```azurecli-interactive
az batch task file download \
    --job-id myjob \
    --task-id mytask1 \
    --file-path stdout.txt \
    --destination ./stdout.txt
```

Você pode exibir o conteúdo de `stdout.txt` em um editor de texto. O conteúdo mostra as variáveis de ambiente do Lote do Azure que são definidas no nó. Ao criar seus próprios trabalhos do Lote, você pode consultar essas variáveis de ambiente nas linhas de comando da tarefa e nos aplicativos e scripts executados pelas linhas de comando. Por exemplo:

```
AZ_BATCH_TASK_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1
AZ_BATCH_NODE_STARTUP_DIR=/mnt/batch/tasks/startup
AZ_BATCH_CERTIFICATES_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/certs
AZ_BATCH_ACCOUNT_URL=https://mybatchaccount.eastus2.batch.azure.com/
AZ_BATCH_TASK_WORKING_DIR=/mnt/batch/tasks/workitems/myjob/job-1/mytask1/wd
AZ_BATCH_NODE_SHARED_DIR=/mnt/batch/tasks/shared
AZ_BATCH_TASK_USER=_azbatch
AZ_BATCH_NODE_ROOT_DIR=/mnt/batch/tasks
AZ_BATCH_JOB_ID=myjobl
AZ_BATCH_NODE_IS_DEDICATED=true
AZ_BATCH_NODE_ID=tvm-257509324_2-20180703t215033z
AZ_BATCH_POOL_ID=mypool
AZ_BATCH_TASK_ID=mytask1
AZ_BATCH_ACCOUNT_NAME=mybatchaccount
AZ_BATCH_TASK_USER_IDENTITY=PoolNonAdmin
```
## <a name="clean-up-resources"></a>Limpar recursos
Se você quiser continuar com exemplos e tutoriais do Lote, use a conta do Lote e a conta de armazenamento vinculada criada neste guia de início rápido. Não há nenhum encargo pela conta do Lote.

Você é cobrado pelos pools enquanto os nós estão em execução, mesmo se não há trabalhos agendados. Quando você não precisar mais um pool, exclua-o com o comando [az batch pool delete](/cli/azure/batch/pool#az-batch-pool-delete). Quando você excluir o pool, todas as saídas de tarefa nos nós são excluídas. 

```azurecli-interactive
az batch pool delete --pool-id mypool
```

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#az-group-delete) para remover o grupo de recursos, conta do Lote, pools e os recursos relacionados. Exclua os recursos da seguinte maneira:

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou uma conta do Lote, um pool do Lote e um trabalho do Lote. O trabalho executou tarefas de exemplo e você exibiu a saída criada em um dos nós. Agora que você conhece os conceitos principais do serviço Lote, está pronto para experimentar o Lote com cargas de trabalho mais realistas em maior escala. Para saber mais sobre o Lote do Azure, prossiga para os tutoriais do Lote do Azure. 


> [!div class="nextstepaction"]
> [Tutoriais do Lote do Azure](./tutorial-parallel-dotnet.md)
