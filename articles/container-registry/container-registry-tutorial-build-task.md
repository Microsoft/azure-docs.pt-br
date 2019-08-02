---
title: Tutorial – automatizar builds de imagem de contêiner – Tarefas do Registro de Contêiner do Azure
description: Neste tutorial, você aprenderá a configurar uma Tarefa de Registro de Contêiner do Azure para disparar builds de imagem de contêiner automaticamente na nuvem quando você confirmar o código-fonte em um repositório Git.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/04/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: ea3f4f295da747b3a53956c0888797a5f8607d6e
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310488"
---
# <a name="tutorial-automate-container-image-builds-in-the-cloud-when-you-commit-source-code"></a>Tutorial: Automatizar builds de imagem de contêiner na nuvem ao confirmar o código-fonte

Além de uma [tarefa rápida](container-registry-tutorial-quick-task.md), as Tarefas do ACR são compatíveis com builds de imagem de contêiner automatizados do Docker na nuvem quando você faz commit do código-fonte em um repositório Git.

Neste tutorial, a tarefa ACR cria e envia por push uma única imagem de contêiner especificada em um Dockerfile quando você faz commit do código-fonte em um repositório Git. Para criar uma [tarefa de várias etapas](container-registry-tasks-multi-step.md) que usa um arquivo YAML para definir etapas para criar, enviar por push e, opcionalmente, testar vários contêineres ao fazer commit do código, confira o [Tutorial: Executar um fluxo de trabalho de contêiner de várias etapas na nuvem ao fazer commit do código-fonte](container-registry-tutorial-multistep-task.md). Para uma visão geral sobre as Tarefas do ACR, confira [Automatizar aplicação de patch do sistema operacional e de estrutura com Tarefas do ACR](container-registry-tasks-overview.md)

Neste tutorial:

> [!div class="checklist"]
> * Cria uma tarefa
> * Testar a tarefa
> * Exibir status da tarefa
> * Disparar a tarefa com uma confirmação de código

Este tutorial presume que você já tenha concluído as tarefas no [tutorial anterior](container-registry-tutorial-quick-task.md). Se você ainda não tiver feito isso, conclua as etapas na seção [Pré-requisitos](container-registry-tutorial-quick-task.md#prerequisites) do tutorial anterior antes de continuar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se desejar usar a CLI do Azure localmente, será preciso ter a CLI do Azure versão **2.0.46** ou posterior instalada e conectada com [az login][az-login]. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar a CLI, confira como [instalar a CLI do Azure][azure-cli].

[!INCLUDE [container-registry-task-tutorial-prereq.md](../../includes/container-registry-task-tutorial-prereq.md)]

## <a name="create-the-build-task"></a>Criar a tarefa de build

Agora que você concluiu as etapas necessárias para habilitar as Tarefas do ACR para ler o status de confirmação e criar webhooks em um repositório, você pode criar uma tarefa que dispara um build de imagem de contêiner em confirmações para o repositório.

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
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --branch master \
    --file Dockerfile \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Se você já tiver criado tarefas durante a versão prévia com o comando `az acr build-task`, essas tarefas precisarão ser recriadas usando o comando [az acr task][az-acr-task].

Esta tarefa especifica que qualquer código de tempo é confirmado para o branch *mestre* no repositório especificado por `--context`, as Tarefas do ACR compilarão a imagem de contêiner do código presente nesse branch. O Dockerfile especificado pelo `--file` da raiz do repositório é usado para criar a imagem. O argumento `--image` especifica um valor com parâmetros de `{{.Run.ID}}` para a parte da versão de marca da imagem, garantindo que a imagem criada esteja correlacionada a um build específico e seja marcada exclusivamente.

A saída de um comando [az acr task create][az-acr-task-create] bem-sucedido é semelhante à seguinte:

```console
{
  "agentConfiguration": {
    "cpu": 2
  },
  "creationDate": "2018-09-14T22:42:32.972298+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myregistry/providers/Microsoft.ContainerRegistry/registries/myregistry/tasks/taskhelloworld",
  "location": "westcentralus",
  "name": "taskhelloworld",
  "platform": {
    "architecture": "amd64",
    "os": "Linux",
    "variant": null
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "myregistry",
  "status": "Enabled",
  "step": {
    "arguments": [],
    "baseImageDependencies": null,
    "contextPath": "https://github.com/gituser/acr-build-helloworld-node",
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Run.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "type": "Docker"
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
          "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
          "sourceControlAuthProperties": null,
          "sourceControlType": "GitHub"
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

## <a name="test-the-build-task"></a>Testar a tarefa de build

Agora, você tem uma tarefa que define seu build. Para testar o pipeline de build, dispare um build manualmente executando o comando [az acr task run][az-acr-task-run]:

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Por padrão, o comando `az acr task run` transmite a saída de log para o console quando você executa o comando.

```console
$ az acr task run --registry $ACR_NAME --name taskhelloworld

2018/09/17 22:51:00 Using acb_vol_9ee1f28c-4fd4-43c8-a651-f0ed027bbf0e as the home volume
2018/09/17 22:51:00 Setting up Docker configuration...
2018/09/17 22:51:02 Successfully set up Docker configuration
2018/09/17 22:51:02 Logging in to registry: myregistry.azurecr.io
2018/09/17 22:51:03 Successfully logged in
2018/09/17 22:51:03 Executing step: build
2018/09/17 22:51:03 Obtaining source code and scanning for dependencies...
2018/09/17 22:51:05 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  23.04kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
Status: Image is up to date for node:9-alpine
 ---> a56170f59699
Step 2/5 : COPY . /src
 ---> 5f574fcf5816
Step 3/5 : RUN cd /src && npm install
 ---> Running in b1bca3b5f4fc
npm notice created a lockfile as package-lock.json. You should commit this file.
npm WARN helloworld@1.0.0 No repository field.

up to date in 0.078s
Removing intermediate container b1bca3b5f4fc
 ---> 44457db20dac
Step 4/5 : EXPOSE 80
 ---> Running in 9e6f63ec612f
Removing intermediate container 9e6f63ec612f
 ---> 74c3e8ea0d98
Step 5/5 : CMD ["node", "/src/server.js"]
 ---> Running in 7382eea2a56a
Removing intermediate container 7382eea2a56a
 ---> e33cd684027b
Successfully built e33cd684027b
Successfully tagged myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:11 Executing step: push
2018/09/17 22:51:11 Pushing image: myregistry.azurecr.io/helloworld:da2, attempt 1
The push refers to repository [myregistry.azurecr.io/helloworld]
4a853682c993: Preparing
[...]
4a853682c993: Pushed
[...]
da2: digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419 size: 1366
2018/09/17 22:51:21 Successfully pushed image: myregistry.azurecr.io/helloworld:da2
2018/09/17 22:51:21 Step id: build marked as successful (elapsed time in seconds: 7.198937)
2018/09/17 22:51:21 Populating digests for step id: build...
2018/09/17 22:51:22 Successfully populated digests for step id: build
2018/09/17 22:51:22 Step id: push marked as successful (elapsed time in seconds: 10.180456)
The following dependencies were found:
- image:
    registry: myregistry.azurecr.io
    repository: helloworld
    tag: da2
    digest: sha256:c24e62fd848544a5a87f06ea60109dbef9624d03b1124bfe03e1d2c11fd62419
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:8dafc0968fb4d62834d9b826d85a8feecc69bd72cd51723c62c7db67c6dec6fa
  git:
    git-head-revision: 68cdf2a37cdae0873b8e2f1c4d80ca60541029bf


Run ID: da2 was successful after 27s
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

Depois que você tiver efetuado push em uma confirmação para o repositório, o webhook criado pelas Tarefas do ACR dispararão e iniciarão um build no Registro de Contêiner do Azure. Exiba os logs de compilação para a tarefa em execução no momento verificar e monitorar o andamento do build:

```azurecli-interactive
az acr task logs --registry $ACR_NAME
```

A saída é semelhante à seguinte, mostrando a tarefa em execução no momento (ou executada por último):

```console
$ az acr task logs --registry $ACR_NAME
Showing logs of the last created run.
Run ID: da4

[...]

Run ID: da4 was successful after 38s
```

## <a name="list-builds"></a>Listar builds

Para ver uma lista das execuções de tarefa que as Tarefas do ACR concluíram para seu registro, execute o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A saída do comando deve ser semelhante ao seguinte. As execuções que as Tarefas do ACR efetuou são exibidas, e “Confirmação de Git” aparece na coluna GATILHO para a tarefa mais recente:

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprenderá como usar uma tarefa para disparar builds de imagem de contêiner automaticamente no Azure quando você confirmar o código-fonte para um repositório Git. Vá para o próximo tutorial para aprender a criar tarefas que disparam builds quando a imagem base de uma imagem de contêiner for atualizada.

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
[az-login]: /cli/azure/reference-index#az-login



