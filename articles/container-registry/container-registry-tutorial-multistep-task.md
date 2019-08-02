---
title: Tutorial – tarefas de contêiner de várias etapas – Tarefas do Registro de Contêiner do Azure
description: Neste tutorial, você aprenderá a configurar uma Tarefa de Registro de Contêiner do Azure para disparar automaticamente um fluxo de trabalho de várias etapas para criar, executar e enviar e imagens de contêiner por push na nuvem ao fazer commit do código-fonte em um repositório Git.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: c78c2c8279972108aee12b9b386175d0f27b7fee
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310405"
---
# <a name="tutorial-run-a-multi-step-container-workflow-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Executar um fluxo de trabalho de contêiner de várias etapas na nuvem ao fazer commit do código-fonte

Além de uma [tarefa rápida](container-registry-tutorial-quick-task.md), as Tarefas do ACR são compatíveis com fluxos de trabalho de várias etapas baseados em vários contêineres que podem ser automaticamente disparados quando você faz commit do código-fonte em um repositório Git. 

Neste tutorial, você aprenderá como usar arquivos YAML de exemplo para definir tarefas de várias etapas que criam, executar e enviam por push uma ou mais imagens de contêiner para um registro quando você faz commit do código-fonte. Para criar uma tarefa que automatiza apenas uma única imagem de build ao fazer commit do código, confira o [Tutorial: Automatizar builds de imagem de contêiner na nuvem ao fazer commit do código-fonte](container-registry-tutorial-build-task.md). Para uma visão geral sobre as Tarefas do ACR, confira [Automatizar aplicação de patch do sistema operacional e de estrutura com Tarefas do ACR](container-registry-tasks-overview.md).

Neste tutorial:

> [!div class="checklist"]
> * Definir uma tarefa de várias etapas usando um arquivo YAML
> * Cria uma tarefa
> * Você também pode adicionar credenciais à tarefa para permitir o acesso a outro registro
> * Testar a tarefa
> * Exibir status da tarefa
> * Disparar a tarefa com uma confirmação de código

Este tutorial presume que você já tenha concluído as tarefas no [tutorial anterior](container-registry-tutorial-quick-task.md). Se você ainda não tiver feito isso, conclua as etapas na seção [Pré-requisitos](container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se quiser usar a CLI do Azure localmente, será preciso ter a CLI do Azure versão **2.0.62** ou posterior instalada e conectada com [az login][az-login]. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar a CLI, confira como [instalar a CLI do Azure][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-a-multi-step-task"></a>Criar uma tarefa de várias etapas

Agora que você concluiu as etapas necessárias para habilitar as Tarefas do ACR para ler o status de commit e criar webhooks em um repositório, crie uma tarefa de várias etapas que dispara build, execução e envio por push de uma imagem de contêiner.

### <a name="yaml-file"></a>Arquivo YAML

Você define as etapas para uma tarefa de várias etapas em um [arquivo YAML](container-registry-tasks-reference-yaml.md). A primeira tarefa de várias etapas de exemplo deste tutorial é definida no arquivo `taskmulti.yaml`, que está na raiz do repositório GitHub que você clonou:

```yml
version: v1.0.0
steps:
# Build target image
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push image
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
```

Essa tarefa de várias etapa faz o seguinte:

1. Executa uma etapa `build` para criar uma imagem com base no Dockerfile no diretório de trabalho. A imagem se destina ao `Run.Registry`, o registro em que a tarefa é executada, e é marcada com uma ID exclusiva de execução de Tarefas do ACR. 
1. Executa uma etapa `cmd` para executar a imagem em um contêiner temporário. Este exemplo inicia um contêiner de longa execução em segundo plano e retorna a ID do contêiner e, em seguida, para o contêiner. Em um cenário do mundo real, você pode incluir etapas para testar o contêiner em execução e garantir que ele esteja em execução corretamente.
1. Em um etapa `push`, envia por push a imagem que foi criada para o registro de execução.

### <a name="task-command"></a>Comando de tarefa

Primeiro, preencha essas variáveis de ambiente de shell com valores apropriados para seu ambiente. Esta etapa não é estritamente necessária, mas torna a execução dos comandos da CLI do Azure com várias linhas neste tutorial um pouco mais fácil. Se não preencher essas variáveis de ambiente, você precisará substituir manualmente cada valor sempre que aparecerem nos comandos de exemplo.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the previous section
```

Agora, crie a tarefa executando o seguinte comando [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example1 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti.yaml \
    --git-access-token $GIT_PAT
```

Esta tarefa especifica que qualquer código de tempo é confirmado para o branch *mestre* no repositório especificado por `--context`, as Tarefas do ACR executarão a tarefa de várias etapas por meio do código desse branch. O arquivo YAML especificado por `--file` da raiz do repositório define as etapas. 

A saída de um comando [az acr task create][az-acr-task-create] bem-sucedido é semelhante à seguinte:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2019-05-03T03:14:31.763887+00:00",
  "credentials": null,
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskmulti",
  "location": "westus",
  "name": "example1",
  "platform": {
    "architecture": "amd64",
    "os": "linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "status": "Enabled",
  "step": {
    "baseImageDependencies": null,
    "contextAccessToken": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node.git",
    "taskFilePath": "taskmulti.yaml",
    "type": "FileTask",
    "values": [],
    "valuesFilePath": null
  },
  "tags": null,
  "timeout": 3600,
  "trigger": {
    "baseImageTrigger": {
      "baseImageTriggerType": "Runtime",
      "name": "defaultBaseimageTriggerName",
      "status": "Enabled"
    },
    "sourceTriggers": [
      {
        "name": "defaultSourceTriggerName",
        "sourceRepository": {
          "branch": "master",
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node.git",
          "sourceControlAuthProperties": null,
          "sourceControlType": "Github"
        },
        "sourceTriggerEvents": [
          "commit"
        ],
        "status": "Enabled"
      }
    ]
  },
  "type": "Microsoft.ContainerRegistry/registries/tasks"
}
```

## <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho de várias etapas

Para testar a tarefa de várias etapas, dispare-a manualmente executando o comando [az acr task run][az-acr-task-run]:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example1
```

Por padrão, o comando `az acr task run` transmite a saída de log para o console quando você executa o comando. A saída mostra o andamento da execução de cada uma das etapas da tarefa. A saída abaixo é condensada para mostrar as principais etapas.

```console
Queued a run with ID: cf19
Waiting for an agent...
2019/05/03 03:03:31 Downloading source code...
2019/05/03 03:03:33 Finished downloading source code
2019/05/03 03:03:33 Using acb_vol_cfe6bd55-3076-4215-8091-6a81aec3d1b1 as the home volume
2019/05/03 03:03:33 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 03:03:34 Successfully set up Docker network: acb_default_network
2019/05/03 03:03:34 Setting up Docker configuration...
2019/05/03 03:03:34 Successfully set up Docker configuration
2019/05/03 03:03:34 Logging in to registry: myregistry.azurecr.io
2019/05/03 03:03:35 Successfully logged into myregistry.azurecr.io
2019/05/03 03:03:35 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:35 Scanning for dependencies...
2019/05/03 03:03:36 Successfully scanned dependencies
2019/05/03 03:03:36 Launching container with name: acb_step_0
Sending build context to Docker daemon  24.06kB

[...]

Successfully built f669bfd170af
Successfully tagged myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:43 Successfully executed container: acb_step_0
2019/05/03 03:03:43 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:43 Launching container with name: acb_step_1
279b1cb6e092b64c8517c5506fcb45494cd5a0bd10a6beca3ba97f25c5d940cd
2019/05/03 03:03:44 Successfully executed container: acb_step_1
2019/05/03 03:03:44 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 03:03:44 Pushing image: myregistry.azurecr.io/hello-world:cf19, attempt 1

[...]

2019/05/03 03:03:46 Successfully pushed image: myregistry.azurecr.io/hello-world:cf19
2019/05/03 03:03:46 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 7.425169)
2019/05/03 03:03:46 Populating digests for step ID: acb_step_0...
2019/05/03 03:03:47 Successfully populated digests for step ID: acb_step_0
2019/05/03 03:03:47 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 0.827129)
2019/05/03 03:03:47 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.112113)
2019/05/03 03:03:47 The following dependencies were found:
2019/05/03 03:03:47
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf19
    digest: sha256:6b981a8ca8596e840228c974c929db05c0727d8630465de536be74104693467a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 1a3065388a0238e52865db1c8f3e97492a43444c

Run ID: cf19 was successful after 18s
```

## <a name="trigger-a-build-with-a-commit"></a>Disparar um build com uma confirmação

Agora que você testou a tarefa executando-a manualmente, dispare-a automaticamente com uma alteração de código-fonte.

Primeiro, verifique se você está no diretório que contém o clone local do [repositório][sample-repo]:

```azurecli-interactive
cd acr-build-helloworld-node
```

Em seguida, execute os seguintes comandos para criar, confirmar e enviar um novo arquivo para o seu fork do repositório no GitHub:

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Tasks"
git push origin master
```

Você pode ser solicitado a fornecer suas credenciais do GitHub quando executar o comando `git push`. Forneça seu nome de usuário do GitHub e insira o token de acesso pessoal (PAT) que criou anteriormente para a senha.

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

Depois que você tiver efetuado push em um commit para o repositório, o webhook criado pelas Tarefas do ACR dispararão e iniciarão a tarefa no Registro de Contêiner do Azure. Exiba os logs de compilação para a tarefa em execução no momento verificar e monitorar o andamento do build:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

A saída é semelhante à seguinte, mostrando a tarefa em execução no momento (ou executada por último):

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: cf1d

[...]

Run ID: cf1d was successful after 37s
```

## <a name="list-builds"></a>Listar builds

Para ver uma lista das execuções de tarefa que as Tarefas do ACR concluíram para seu registro, execute o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A saída do comando deve ser semelhante ao seguinte. As execuções que as Tarefas do ACR efetuou são exibidas, e “Confirmação de Git” aparece na coluna GATILHO para a tarefa mais recente:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
cf1d      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:37
cf1c      example1   linux       Succeeded  Commit     2019-05-03T04:16:44Z  00:00:39
cf1b      example1   linux       Succeeded  Manual     2019-05-03T03:10:30Z  00:00:31
cf1a      example1   linux       Succeeded  Commit     2019-05-03T03:09:32Z  00:00:31
cf19      example1   linux       Succeeded  Manual     2019-05-03T03:03:30Z  00:00:21
```

## <a name="create-a-multi-registry-multi-step-task"></a>Criar uma tarefa de várias etapas de vários registros

Por padrão, as Tarefas do ACR têm permissões para efetuar push ou pull de imagens do registro no qual a tarefa é executada. Você pode executar uma tarefa de várias etapas que tem como destino um ou mais registros além do registro de execução. Por exemplo, talvez seja necessário criar imagens em um registro e armazenar imagens com marcas diferentes em um segundo registro que é acessado por um sistema de produção. Este exemplo mostra como criar esse tipo de tarefa e fornecer credenciais para outro registro.

Se você ainda não tem um segundo registro, crie um para este exemplo. Se você precisar de um registro, confira o [tutorial anterior](container-registry-tutorial-quick-task.md) ou o [Início Rápido: Criar um registro de contêiner usando a CLI do Azure](container-registry-get-started-azure-cli.md).

Para criar a tarefa, você precisa do nome do servidor de logon do registro, que está no formato *mycontainerregistrydate.azurecr.io* (todas as letras minúsculas). Neste exemplo, você usa o segundo registro para armazenar imagens marcadas por data de build.

### <a name="yaml-file"></a>Arquivo YAML

A segunda tarefa de várias etapas de exemplo deste tutorial é definida no arquivo `taskmulti-multiregistry.yaml`, que está na raiz do repositório GitHub que você clonou:

```yml
version: v1.0.0
steps:
# Build target images
- build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} -f Dockerfile .
- build: -t {{.Values.regDate}}/hello-world:{{.Run.Date}} -f Dockerfile .
# Run image 
- cmd: -t {{.Run.Registry}}/hello-world:{{.Run.ID}}
  id: test
  detach: true
  ports: ["8080:80"]
- cmd: docker stop test
# Push images
- push:
  - {{.Run.Registry}}/hello-world:{{.Run.ID}}
  - {{.Values.regDate}}/hello-world:{{.Run.Date}}
```

Essa tarefa de várias etapa faz o seguinte:

1. Executa duas etapas `build` para criar imagens do Dockerfile no diretório de trabalho:
    * A primeira se destina ao `Run.Registry`, o registro em que a tarefa é executada, e é marcada com a ID de execução de Tarefas do ACR. 
    * A segunda se destina ao registro identificado pelo valor de `regDate`, que é definido quando você cria a tarefa (ou fornecer por meio de um arquivo externo `values.yaml` passado para `az acr task create`). Essa imagem é marcada com a data de execução.
1. Executa uma etapa `cmd` para executar um dos contêineres criados. Este exemplo inicia um contêiner de longa execução em segundo plano e retorna a ID do contêiner e, em seguida, para o contêiner. Em um cenário do mundo real, você pode testar um contêiner em execução e garantir que ele esteja em execução corretamente.
1. Em uma etapa `push`, envia por push as imagens que foram criadas, a primeira para o registro de execução, a segunda para o registro identificado por `regDate`.

### <a name="task-command"></a>Comando de tarefa

Usando as variáveis de ambiente do shell definidas anteriormente, crie a tarefa por meio da execução do seguinte comando [az acr task create][az-acr-task-create]. Substitua o nome do registro por *mycontainerregistrydate*.

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name example2 \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file taskmulti-image.yaml \
    --git-access-token $GIT_PAT \
    --set regDate=mycontainerregistrydate.azurecr.io
```

### <a name="add-task-credential"></a>Adicionar credencial de tarefa

Para efetuar push das imagens ao registro identificado pelo valor de `regDate`, use o comando [az acr task credential add][az-acr-task-credential-add] para adicionar credenciais de logon para esse registro para a tarefa.

Neste exemplo, é recomendável que você crie uma [entidade de serviço](container-registry-auth-service-principal.md) com acesso ao registro no escopo da função *AcrPush*. Para criar a entidade de serviço, confira esse [script da CLI do Azure](https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh).

Passar a ID do aplicativo e a senha da entidade de serviço no seguinte comando `az acr task credential add`:

```azurecli-interactive
az acr task credential add --name example2 \
    --registry $ACR_NAME \
    --login-server mycontainerregistrydate.azurecr.io \
    --username <service-principal-application-id> \
    --password <service-principal-password>
```

A CLI retorna o nome do servidor de logon do registro que você adicionou.

### <a name="test-the-multi-step-workflow"></a>Testar o fluxo de trabalho de várias etapas

Como no exemplo anterior, para testar a tarefa de várias etapas, dispare-a manualmente executando o comando [az acr task run][az-acr-task-run]. Para disparar a tarefa com um commit para o repositório Git, confira a seção [Disparar um build com um commit](#trigger-a-build-with-a-commit).

```azurecli-interactive
az acr task run --registry $ACR_NAME --name example2
```

Por padrão, o comando `az acr task run` transmite a saída de log para o console quando você executa o comando. Como antes, a saída mostra o andamento da execução de cada uma das etapas da tarefa. A saída foi condensada para mostrar as principais etapas

Saída:

```console
Queued a run with ID: cf1g
Waiting for an agent...
2019/05/03 04:33:39 Downloading source code...
2019/05/03 04:33:41 Finished downloading source code
2019/05/03 04:33:42 Using acb_vol_4569b017-29fe-42bd-83b2-25c45a8ac807 as the home volume
2019/05/03 04:33:42 Creating Docker network: acb_default_network, driver: 'bridge'
2019/05/03 04:33:43 Successfully set up Docker network: acb_default_network
2019/05/03 04:33:43 Setting up Docker configuration...
2019/05/03 04:33:44 Successfully set up Docker configuration
2019/05/03 04:33:44 Logging in to registry: mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Successfully logged into mycontainerregistry.azurecr.io
2019/05/03 04:33:45 Logging in to registry: mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Successfully logged into mycontainerregistrydate.azurecr.io
2019/05/03 04:33:47 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:47 Scanning for dependencies...
2019/05/03 04:33:47 Successfully scanned dependencies
2019/05/03 04:33:47 Launching container with name: acb_step_0
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:33:55 Successfully executed container: acb_step_0
2019/05/03 04:33:55 Executing step ID: acb_step_1. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:55 Scanning for dependencies...
2019/05/03 04:33:56 Successfully scanned dependencies
2019/05/03 04:33:56 Launching container with name: acb_step_1
Sending build context to Docker daemon  25.09kB

[...]

Successfully tagged mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:33:57 Successfully executed container: acb_step_1
2019/05/03 04:33:57 Executing step ID: acb_step_2. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:57 Launching container with name: acb_step_2
721437ff674051b6be63cbcd2fa8eb085eacbf38d7d632f1a079320133182101
2019/05/03 04:33:58 Successfully executed container: acb_step_2
2019/05/03 04:33:58 Executing step ID: acb_step_3. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:33:58 Launching container with name: acb_step_3
test
2019/05/03 04:34:09 Successfully executed container: acb_step_3
2019/05/03 04:34:09 Executing step ID: acb_step_4. Working directory: '', Network: 'acb_default_network'
2019/05/03 04:34:09 Pushing image: mycontainerregistry.azurecr.io/hello-world:cf1g, attempt 1
The push refers to repository [mycontainerregistry.azurecr.io/hello-world]

[...]

2019/05/03 04:34:12 Successfully pushed image: mycontainerregistry.azurecr.io/hello-world:cf1g
2019/05/03 04:34:12 Pushing image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z, attempt 1
The push refers to repository [mycontainerregistrydate.azurecr.io/hello-world]

[...]

2019/05/03 04:34:19 Successfully pushed image: mycontainerregistrydate.azurecr.io/hello-world:20190503-043342z
2019/05/03 04:34:19 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 8.125744)
2019/05/03 04:34:19 Populating digests for step ID: acb_step_0...
2019/05/03 04:34:21 Successfully populated digests for step ID: acb_step_0
2019/05/03 04:34:21 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.009281)
2019/05/03 04:34:21 Populating digests for step ID: acb_step_1...
2019/05/03 04:34:23 Successfully populated digests for step ID: acb_step_1
2019/05/03 04:34:23 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 0.795440)
2019/05/03 04:34:23 Step ID: acb_step_3 marked as successful (elapsed time in seconds: 11.446775)
2019/05/03 04:34:23 Step ID: acb_step_4 marked as successful (elapsed time in seconds: 9.734973)
2019/05/03 04:34:23 The following dependencies were found:
2019/05/03 04:34:23
- image:
    registry: mycontainerregistry.azurecr.io
    repository: hello-world
    tag: cf1g
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc
- image:
    registry: mycontainerregistrydate.azurecr.io
    repository: hello-world
    tag: 20190503-043342z
    digest: sha256:75354e9edb995e8661438bad9913deed87a185fddd0193811f916d684b71a5d2
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 9d9023473c46a5e2c315681b11eb4552ef0faccc

Run ID: cf1g was successful after 46s
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar tarefas de várias etapas baseadas em vários contêineres que são automaticamente disparadas quando você faz commit do código-fonte em um repositório Git. Para recursos avançados de tarefas de várias etapas, incluindo a execução paralela e dependente de etapas, confira a [referência de YAML de Tarefas do ACR](container-registry-tasks-reference-yaml.md). Vá para o próximo tutorial para aprender a criar tarefas que disparam builds quando a imagem base de uma imagem de contêiner for atualizada.

> [!div class="nextstepaction"]
> [Automatizar builds na atualização da imagem de base](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add    
[az-login]: /cli/azure/reference-index#az-login

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
