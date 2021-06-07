---
title: Execução de tarefa rápida com modelo
description: Enfileirar uma tarefa ACR executada para criar uma imagem usando um modelo de Azure Resource Manager
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e8023c088ac328c2b6e95fccd0230c4d40325c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98916058"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Executar tarefas ACR usando modelos do Resource Manager

[Tarefas do ACR](container-registry-tasks-overview.md) é um conjunto de recursos dentro do Registro de Contêiner do Azure para ajudá-lo a gerenciar e a modificar imagens de contêiner em todo o ciclo de vida do contêiner. 

Este artigo mostra Azure Resource Manager exemplos de modelo para enfileirar uma execução de tarefa rápida, semelhante a uma que você pode criar manualmente usando o comando [AZ ACR Build][az-acr-build] .

Um modelo do Resource Manager para enfileirar uma execução de tarefa é útil em cenários de automação e estende a funcionalidade do `az acr build` . Por exemplo:

* Use um modelo para criar um registro de contêiner e, imediatamente, colocar uma tarefa em fila para criar e enviar por push uma imagem de contêiner
* Criar ou habilitar recursos adicionais que você pode usar em uma tarefa de execução rápida, como uma identidade gerenciada para recursos do Azure

## <a name="limitations"></a>Limitações

* Você deve especificar um contexto remoto, como um repositório GitHub como o [local de origem](container-registry-tasks-overview.md#context-locations) para a execução da tarefa. Você não pode usar um contexto de origem local.
* Para execução de tarefas usando uma identidade gerenciada, somente uma identidade gerenciada *atribuída pelo usuário* é permitida.

## <a name="prerequisites"></a>Pré-requisitos

* **Conta do GitHub** – crie uma conta no https://github.com se você ainda não tiver uma. 
* **Repositório de exemplo de bifurcação** – para os exemplos de tarefa mostrados aqui, use a interface do usuário do GitHub para bifurcar o seguinte repositório de exemplo em sua conta do github: https://github.com/Azure-Samples/acr-build-helloworld-node . Esse repositório contém Dockerfiles de exemplo e código-fonte para criar pequenas imagens de contêiner.

## <a name="example-create-registry-and-queue-task-run"></a>Exemplo: criar registro e executar tarefa de fila

Este exemplo usa um [modelo de exemplo](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) para criar um registro de contêiner e uma fila de execução de tarefa que cria e envia uma imagem. 

### <a name="template-parameters"></a>Parâmetros de modelo

Para este exemplo, forneça valores para os seguintes parâmetros de modelo:

|Parâmetro  |Valor  |
|---------|---------|
|registryName     |Nome exclusivo do registro que é criado         |
|repository     |Repositório de destino para tarefa de compilação        |
|taskRunName     |Nome da execução da tarefa, que especifica a marca de imagem |
|sourceLocation     |Contexto remoto para a tarefa de compilação, por exemplo, https://github.com/Azure-Samples/acr-build-helloworld-node . O Dockerfile na raiz do repositório cria uma imagem de contêiner para um pequeno aplicativo Web de Node.js. Se desejar, use a bifurcação do repositório como o contexto de compilação.         |

### <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo com o comando [AZ Deployment Group Create][az-deployment-group-create] . Este exemplo cria e envia por push a imagem *HelloWorld-node: TestRun* para um registro chamado *mycontainerregistry*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

O comando anterior passa os parâmetros na linha de comando. Se desejar, passe-os em um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificar implantação

Depois que a implantação for concluída com êxito, verifique se a imagem foi criada executando [AZ ACR Repository show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Saída:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Exibir log de execução

Para exibir detalhes sobre a execução da tarefa, exiba o log de execução.

Primeiro, obtenha a ID de execução com [AZ ACR Task List-execuções][az-acr-task-list-runs]
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

A saída é semelhante a:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Execute [os logs de tarefa AZ ACR][az-acr-task-logs] para exibir os logs de execução de tarefa para a ID de execução, neste caso, *CA1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

A saída mostra o log de execução da tarefa.

Você também pode exibir o log de execução de tarefa no portal do Azure. 

1. Navegue até o registro de contêiner
2. Em **Serviços**, selecione **tarefas**  >  **execuções**.
3. Selecione a ID de execução, nesse caso, *CA1*. 

O portal mostra o log de execução da tarefa.

## <a name="example-task-run-with-managed-identity"></a>Exemplo: execução de tarefa com identidade gerenciada

Use um [modelo de exemplo](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) para enfileirar uma tarefa executada que habilita uma identidade gerenciada atribuída pelo usuário. Durante a execução da tarefa, a identidade é autenticada para efetuar pull de uma imagem de outro registro de contêiner do Azure. 

Esse cenário é semelhante à [autenticação entre registros em uma tarefa ACR usando uma identidade gerenciada pelo Azure](container-registry-tasks-cross-registry-authentication.md). Por exemplo, uma organização pode manter um registro centralizado com imagens de base acessadas por várias equipes de desenvolvimento.

### <a name="prepare-base-registry"></a>Preparar registro base

Para fins de demonstração, crie um registro de contêiner separado como seu registro de base e envie por push um Node.js imagem base extraída do Hub do Docker.

1. Crie um segundo registro de contêiner, por exemplo, *mybaseregistry*, para armazenar imagens base.
1. Puxe a `node:9-alpine` imagem do Hub do Docker, marque-a para o registro base e envie-a por push para o registro base:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Criar novo Dockerfile

Crie um Dockerfile que efetua pull da imagem base do registro base. Execute as etapas a seguir em sua bifurcação local do repositório GitHub, por exemplo, `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. Na interface do usuário do GitHub, selecione **criar novo arquivo**.
1. Nomeie o arquivo *Dockerfile-teste* e cole o conteúdo a seguir. Substitua o nome do registro por *mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Selecione **confirmar novo arquivo**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Dar permissões de pull de identidade para o registro base

Forneça as permissões de identidade gerenciadas para efetuar pull do registro de base, *mybaseregistry*.

Use o comando [az acr show][az-acr-show] para obter a ID do recurso do registro base e armazená-la em uma variável:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Use o comando [AZ role Assignment Create][az-role-assignment-create] para atribuir a identidade a função Acrpull ao registro base. Esta função tem permissões apenas para efetuar pull de imagens do registro.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parâmetros de modelo

Para este exemplo, forneça valores para os seguintes parâmetros de modelo:

|Parâmetro  |Valor  |
|---------|---------|
|registryName     |Nome do registro em que a imagem é criada  |
|repository     |Repositório de destino para tarefa de compilação        |
|taskRunName     |Nome da execução da tarefa, que especifica a marca de imagem |
|userAssignedIdentity |ID de recurso da identidade atribuída pelo usuário habilitada na tarefa|
|customRegistryIdentity | ID do cliente da identidade atribuída pelo usuário habilitada na tarefa, usada para autenticar com o registro personalizado |
|customRegistry |Nome do servidor de logon do registro personalizado acessado na tarefa, por exemplo, *mybaseregistry.azurecr.Io*|
|sourceLocation     |Contexto remoto para a tarefa de compilação, por exemplo, *https://github.com/ \<your-GitHub-ID\> /ACR-Build-HelloWorld-node.* |
|dockerFilePath | Caminho para o Dockerfile no contexto remoto, usado para criar a imagem. |

### <a name="deploy-the-template"></a>Implantar o modelo

Implante o modelo com o comando [AZ Deployment Group Create][az-deployment-group-create] . Este exemplo cria e envia por push a imagem *HelloWorld-node: TestRun* para um registro chamado *mycontainerregistry*. A imagem base é extraída de *mybaseregistry.azurecr.Io*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

O comando anterior passa os parâmetros na linha de comando. Se desejar, passe-os em um [arquivo de parâmetros](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Verificar implantação

Depois que a implantação for concluída com êxito, verifique se a imagem foi criada executando [AZ ACR Repository show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Saída:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Exibir log de execução

Para exibir o log de execução, consulte as etapas na [seção anterior](#view-run-log).

## <a name="next-steps"></a>Próximas etapas

 * Veja mais exemplos de modelo no [repositório GitHub do ACR](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Para obter detalhes sobre as propriedades do modelo, consulte a referência de modelo para [tarefas](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)e [execuções de tarefas](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) .


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
