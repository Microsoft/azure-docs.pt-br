---
title: Exibir logs de execução de tarefa – tarefas
description: Como exibir e gerenciar logs de execução gerados por tarefas ACR.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: b2a10d4a3a2746acf38445673af994c6317c77de
ms.sourcegitcommit: daab0491bbc05c43035a3693a96a451845ff193b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2020
ms.locfileid: "93027169"
---
# <a name="view-and-manage-task-run-logs"></a>Exibir e gerenciar logs de execução de tarefa

Cada tarefa executada nas [tarefas do registro de contêiner do Azure](container-registry-tasks-overview.md) gera a saída de log que você pode inspecionar para determinar se as etapas da tarefa foram executadas com êxito. 

Este artigo explica como exibir e gerenciar logs de execução de tarefas.

## <a name="view-streamed-logs"></a>Exibir logs transmitidos

Quando você dispara uma tarefa manualmente, a saída do log é transmitida diretamente para o console. Por exemplo, ao disparar uma tarefa manualmente usando o comando [AZ ACR Build](/cli/azure/acr#az-acr-build), [AZ ACR execute](/cli/azure/acr#az-acr-run)ou [AZ ACR Task execute](/cli/azure/acr/task#az-acr-task-run) , você vê a saída de log transmitida para o console. 

O comando [AZ ACR Run](/cli/azure/acr#az-acr-run) de exemplo a seguir dispara manualmente uma tarefa que executa um contêiner extraído do mesmo registro:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Log em fluxo:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Exibir logs armazenados 

O registro de contêiner do Azure armazena logs de execução para todas as tarefas. Você pode exibir os logs de execução armazenados no portal do Azure. Ou use o comando [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) para exibir um log selecionado. Por padrão, os logs são retidos por 30 dias.

Se uma tarefa for disparada automaticamente, por exemplo, por uma atualização de código-fonte, o acesso aos logs armazenados será a *única* maneira de exibir os logs de execução. Os gatilhos de tarefa automática incluem confirmações de código-fonte ou solicitações de pull, atualizações de imagem de base e gatilhos de temporizador.

Para exibir os logs de execução no Portal:

1. Navegue até seu registro de contêiner.
1. Em **Serviços** , selecione **tarefas**  >  **execuções** .
1. Selecione uma **ID de execução** para exibir o status de execução e os logs de execução. O log contém as mesmas informações que um log transmitido, se um for gerado.

![Exibir o portal de logon da execução de tarefa](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Para exibir um log usando o CLI do Azure, execute [AZ ACR Task logs](/cli/azure/acr/task#az-acr-task-logs) e ESPECIFIQUE uma ID de execução, um nome de tarefa ou uma imagem específica criar por uma tarefa de compilação. Se um nome de tarefa for especificado, o comando mostrará o log para a última execução criada.

O exemplo a seguir gera o log para a execução com ID *cf4* :

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Armazenamento de log alternativo

Talvez você queira armazenar os logs de execução de tarefas em um sistema de arquivos local ou usar uma solução de arquivamento alternativa, como o armazenamento do Azure.

Por exemplo, crie um diretório *tasklogs* local e redirecione a saída dos [logs de tarefa AZ ACR](/cli/azure/acr/task#az-acr-task-logs) para um arquivo local:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Você também pode salvar arquivos de log locais no armazenamento do Azure. Por exemplo, use a [CLI do Azure](../storage/blobs/storage-quickstart-blobs-cli.md), a [portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md)ou outros métodos para carregar arquivos em uma conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [as tarefas do registro de contêiner do Azure](container-registry-tasks-overview.md)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
