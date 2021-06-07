---
title: Autenticação entre registros da tarefa do ACR
description: Configurar uma Tarefa do ACR (Tarefa de Registro de Contêiner do Azure) para acessar outro registro privado de contêiner do Azure, usando uma identidade gerenciada para os recursos Azure
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 789d2c141f8b7c3f2eb8daa31d99090e3d028a43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915821"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticação entre registros em uma tarefa do ACR usando uma identidade gerenciada do Azure 

Em uma [tarefa do ACR](container-registry-tasks-overview.md), você pode [habilitar uma identidade gerenciada para os recursos do Azure](container-registry-tasks-authentication-managed-identity.md). A tarefa pode usar a identidade para acessar outros recursos Azure, sem a necessidade de fornecer ou gerenciar credenciais. 

Neste artigo, você aprende como habilitar uma identidade gerenciada em uma tarefa para efetuar pull de uma imagem de um registro diferente daquele usado para executar a tarefa.

Para criar os recursos do Azure, este artigo requer que você execute a versão 2.0.68 ou posterior da CLI do Azure. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

## <a name="scenario-overview"></a>Visão geral do cenário

A tarefa de exemplo efetua pull de uma imagem base de outro registro de contêiner do Azure para compilar e enviar uma imagem de aplicação por push. Para efetuar pull da imagem base, você configura a tarefa com uma identidade gerenciada e atribui as permissões apropriadas a ela. 

Este exemplo mostra as etapas usando uma identidade gerenciada atribuída pelo usuário ou pelo sistema. A sua escolha de identidade depende das necessidades da sua organização.

Em um cenário real, uma organização pode manter um conjunto de imagens base utilizadas por todas as equipes de desenvolvimento para compilar suas aplicações. Estas imagens base são armazenadas em um registro corporativo, sendo que cada equipe de desenvolvimento tem apenas direitos de efetuar pull. 

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa de dois registros de contêiner do Azure:

* Você usa o primeiro registro para criar e executar tarefas do ACR. Neste artigo, esse registro é nomeado *myregistry*. 
* O segundo registro hospeda uma imagem base usada para que a tarefa compile uma imagem. Neste artigo, o segundo registro é nomeado *mybaseregistry*. 

Substitua por seus próprios nomes de registro em etapas posteriores.

Se você ainda não tem os registros de contêiner do Azure necessários, consulte [Início Rápido: Criar um registro de contêiner privado usando a CLI do Azure](container-registry-get-started-azure-cli.md). Você ainda não precisa enviar imagens por push para o registro.

## <a name="prepare-base-registry"></a>Preparar registro base

Para fins de demonstração, como uma operação única, execute [AZ ACR Import] [AZ-ACR-Import] para importar uma imagem de Node.js pública do Hub do Docker para o registro base. Na prática, outra equipe ou processo na organização pode manter imagens no registro base.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Definir as etapas de tarefa no arquivo YAML

As etapas para o exemplo [tarefa de várias etapas](container-registry-tasks-multi-step.md) estão definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md). Crie um arquivo chamado `helloworldtask.yaml` neste diretório de trabalho e cole o conteúdo a seguir. Atualize o valor de `REGISTRY_NAME` na etapa de compilação com o nome do servidor do registro base.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

A etapa de compilação usa o arquivo `Dockerfile-app` no repositório [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) para criar uma imagem. O `--build-arg` faz referência ao registro base para efetuar pull da imagem base. Quando compilada com êxito, a imagem é enviada por push ao registro usado para executar a tarefa.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opção 1: Criar tarefa com identidade atribuída pelo usuário

As etapas nesta seção criam uma tarefa e habilitam uma identidade atribuída pelo usuário. Se você quiser habilitar uma identidade atribuída pelo sistema, consulte [Opção 2: Criar tarefa com identidade atribuída pelo usuário](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *helloworldtask* executando o comando [az acr task create][az-acr-task-create]. A tarefa é executada sem um contexto de código-fonte, e o comando faz referência ao arquivo `helloworldtask.yaml` no diretório de trabalho. O parâmetro `--assign-identity` passa a ID do recurso da identidade atribuída pelo usuário. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Dar permissões de pull de identidade para o registro base

Nesta seção, dê as permissões de identidade gerenciada para efetuar pull do registro base, *mybaseregistry*.

Use o comando [az acr show][az-acr-show] para obter a ID do recurso do registro base e armazená-la em uma variável:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Use o comando [az role assignment create][az-role-assignment-create] para atribuir a identidade da função `acrpull` ao registro base. Esta função tem permissões apenas para efetuar pull de imagens do registro.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Vá para [Adicionar credenciais de registro de destino à tarefa](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opção 2: Criar tarefa com identidade atribuída pelo sistema

As etapas nesta seção criam uma tarefa e habilitam uma identidade atribuída pelo sistema. Se você quiser habilitar uma identidade atribuída pelo usuário, consulte [Opção 1: Criar tarefa com identidade atribuída pelo usuário](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *helloworldtask* executando o comando [az acr task create][az-acr-task-create]. A tarefa é executada sem um contexto de código-fonte, e o comando faz referência ao arquivo `helloworldtask.yaml` no diretório de trabalho. O parâmetro `--assign-identity` sem valor habilita a identidade atribuída pelo sistema na tarefa. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Dar permissões de pull de identidade para o registro base

Nesta seção, dê as permissões de identidade gerenciada para efetuar pull do registro base, *mybaseregistry*.

Use o comando [az acr show][az-acr-show] para obter a ID do recurso do registro base e armazená-la em uma variável:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Use o comando [az role assignment create][az-role-assignment-create] para atribuir a identidade da função `acrpull` ao registro base. Esta função tem permissões apenas para efetuar pull de imagens do registro.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Adicionar credenciais de registro de destino à tarefa

Agora use o comando [az acr task credential add][az-acr-task-credential-add] para habilitar a tarefa a autenticar com o registro base usando as credenciais da identidade. Execute o comando correspondente ao tipo de identidade gerenciada que você habilitou na tarefa. Se você habilitou uma identidade atribuída pelo usuário, passe `--use-identity` com a ID do cliente da identidade. Se você habilitou uma identidade atribuída ao sistema, passe `--use-identity [system]`.

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Executar a tarefa manualmente

Para verificar se a tarefa na qual você habilitou uma identidade gerenciada está sendo executada com sucesso, acione manualmente a tarefa com o comando [az acr task run][az-acr-task-run]. 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Se a tarefa for executada com sucesso, a saída é semelhante a:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Execute o comando [az acr repository show-tags][az-acr-repository-show-tags] para verificar se a imagem foi compilada e se foi enviada por push com sucesso para *myregistry*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Saída de exemplo:

```console
cf10
```

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [habilitar uma identidade gerenciada em uma tarefa do ACR](container-registry-tasks-authentication-managed-identity.md).
* Consulte [a referência YAML das Tarefas do ACR](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
