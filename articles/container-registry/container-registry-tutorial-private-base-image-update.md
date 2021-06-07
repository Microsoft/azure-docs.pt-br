---
title: Tutorial – Disparar o build da imagem na atualização da imagem base particular
description: Neste tutorial, você vai configurar uma Tarefa do Registro de Contêiner do Azure para disparar automaticamente builds de imagem de contêiner na nuvem quando uma imagem base em outro Registro de Contêiner do Azure particular for atualizada.
ms.topic: tutorial
ms.date: 11/20/2020
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 27ab7c3fc0f04023c32cfac181d8f8650de23560
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772353"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-another-private-container-registry"></a>Tutorial: Automatizar builds de imagem de contêiner quando uma imagem base é atualizada em outro registro de contêiner particular 

As [Tarefas do ACR](container-registry-tasks-overview.md) dão suporte aos builds de imagem automatizados quando a [imagem base](container-registry-tasks-base-images.md) de um contêiner é atualizada, como quando um patch é aplicado ao sistema operacional ou à estrutura do aplicativo em uma das imagens base. 

Neste tutorial, você aprenderá a criar uma tarefa do ACR que dispara um build na nuvem quando a imagem base de um contêiner é enviada por push para outro Registro de Contêiner do Azure. Experimente também um tutorial para criar uma tarefa do ACR que dispara um build de imagem quando uma imagem base é enviada por push para o [mesmo Registro de Contêiner do Azure](container-registry-tutorial-base-image-update.md).

Neste tutorial:

> [!div class="checklist"]
> * Compilar a imagem base em um Registro base
> * Criar uma tarefa de build do aplicativo em outro Registro para acompanhar a imagem base 
> * Atualizar a imagem base para disparar uma tarefa de imagem de aplicativo
> * Exibir a tarefa disparada
> * Verificar a imagem do aplicativo atualizada

## <a name="prerequisites"></a>Pré-requisitos

### <a name="complete-the-previous-tutorials"></a>Conclua os tutoriais anteriores

Este tutorial pressupõe que você já tenha configurado seu ambiente e concluído as etapas nos dois primeiros tutoriais da série, em que você:

* Crie um registro de contêiner do Azure
* Repositório de exemplo de fork
* Clona o repositório de exemplo
* Cria um token de acesso pessoal do GitHub

Caso ainda não tenha feito isso, conclua os seguintes tutoriais antes de continuar:

[Compilar imagens de contêineres na nuvem com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-quick-task.md)

[Automatizar builds de imagem de contêiner com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md)

Além do registro de contêiner criado para os tutoriais anteriores, você precisará criar um registro para armazenar as imagens base. Se desejar, crie o segundo Registro em uma localização diferente do Registro original.

### <a name="configure-the-environment"></a>Configure o ambiente

Preencha essas variáveis de ambiente de shell com valores apropriados para seu ambiente. Esta etapa não é estritamente necessária, mas torna a execução dos comandos da CLI do Azure com várias linhas neste tutorial um pouco mais fácil. Se não preencher essas variáveis de ambiente, você precisará substituir manualmente cada valor sempre que aparecerem nos comandos de exemplo.

```azurecli
BASE_ACR=<base-registry-name>   # The name of your Azure container registry for base images
ACR_NAME=<registry-name>        # The name of your Azure container registry for application images
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

### <a name="base-image-update-scenario"></a>Cenário de atualização de imagem base

Este tutorial orienta você através de um cenário de atualização da imagem base. Esse cenário reflete um fluxo de trabalho de desenvolvimento para gerenciar imagens base em um registro de contêiner particular comum ao criar imagens de aplicativo em outros registros. As imagens base podem especificar sistemas operacionais e estruturas comuns usados por uma equipe ou, até mesmo, por componentes de serviço comuns.

Por exemplo, os desenvolvedores que produzem imagens de aplicativos nos próprios registros podem acessar um conjunto de imagens base mantidas no Registro base comum. O Registro base pode estar em outra região ou, até mesmo, ter replicação geográfica.

O [exemplo de código][code-sample] inclui dois Dockerfiles: uma imagem do aplicativo e uma imagem que ele especifica como base. Nas seções a seguir, você criará uma tarefa do ACR que dispara automaticamente um build da imagem do aplicativo quando uma nova versão da imagem base é enviada por push para outro Registro de Contêiner do Azure.

* [Dockerfile-app][dockerfile-app]: um pequeno aplicativo Web Node.js que renderiza uma página da Web estática exibindo a versão do Node.js no qual é baseado. A cadeia de caracteres de versão é simulada: ela exibe o conteúdo de uma variável de ambiente, `NODE_VERSION`, que é definida na imagem base.

* [Dockerfile-base][dockerfile-base]: a imagem que `Dockerfile-app` especifica como base. É baseado em uma imagem [Node][base-node] e inclui a variável de ambiente `NODE_VERSION`.

Nas seções a seguir, você criará uma tarefa, atualizará o valor `NODE_VERSION` no Dockerfile da imagem base e usará as Tarefas do ACR para compilar a imagem base. Quando a tarefa do ACR efetua push na nova imagem base para o registro, ela automaticamente dispara um build da imagem do aplicativo. Opcionalmente, você pode executar a imagem de contêiner do aplicativo localmente para ver as cadeias de caracteres de versões diferentes nas imagens compiladas.

Neste tutorial, a tarefa do ACR compila e envia por push uma única imagem do contêiner especificada em um Dockerfile. As Tarefas do ACR também podem executar [tarefas de várias etapas](container-registry-tasks-multi-step.md), usando um arquivo YAML para definir as etapas para criar, efetuar push e, opcionalmente, testar vários contêineres.

## <a name="build-the-base-image"></a>Compilar a imagem base

Comece compilando a imagem base com uma *tarefa rápida* das Tarefas do ACR usando [az acr build][az-acr-build]. Conforme discutido no [primeiro tutorial](container-registry-tutorial-quick-task.md) da série, esse processo não só compila a imagem, mas efetuar push para o registro de contêiner, se o build for bem-sucedido. Neste exemplo, a imagem é enviada por push para o Registro da imagem base.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

## <a name="create-a-task-to-track-the-private-base-image"></a>Criar uma tarefa para acompanhar a imagem base particular

Em seguida, crie uma tarefa no Registro de imagem do aplicativo com [az acr task create][az-acr-task-create], habilitando uma [identidade gerenciada](container-registry-tasks-authentication-managed-identity.md). A identidade gerenciada será usada em etapas posteriores, de modo que a tarefa seja autenticada no registro da imagem base. 

Este exemplo usa uma identidade atribuída ao sistema, mas você pode criar e habilitar uma identidade gerenciada atribuída ao usuário para determinados cenários. Para obter detalhes, confira [Autenticação entre Registros em uma tarefa do ACR usando uma identidade gerenciada do Azure](container-registry-tasks-cross-registry-authentication.md).

```azurecli
az acr task create \
    --registry $ACR_NAME \
    --name baseexample2 \
    --image helloworld:{{.Run.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git#main \
    --file Dockerfile-app \
    --git-access-token $GIT_PAT \
    --arg REGISTRY_NAME=$BASE_ACR.azurecr.io \
    --assign-identity
```

Esta tarefa é semelhante à tarefa criada no [tutorial anterior](container-registry-tutorial-build-task.md). Ela instrui as Tarefas do ACR para disparar um build de imagem quando as confirmações são enviadas por push para o repositório especificado por `--context`. Embora o Dockerfile usado para compilar a imagem no tutorial anterior especifique uma imagem base pública (`FROM node:15-alpine`), o Dockerfile nesta tarefa, [Dockerfile-app][dockerfile-app], especifica uma imagem base no Registro da imagem base:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
```

Essa configuração facilita simular um patch do framework na imagem base, posteriormente no tutorial.

## <a name="give-identity-pull-permissions-to-base-registry"></a>Conceder permissões de pull da identidade ao Registro base

Para conceder as permissões de identidade gerenciada da tarefa para efetuar pull de imagens do Registro da imagem base, primeiro, execute [az acr task show][az-acr-task-show] para obter a ID da entidade de serviço da identidade. Em seguida, execute [az acr show][az-acr-show] para obter a ID do recurso do Registro base:

```azurecli
# Get service principal ID of the task
principalID=$(az acr task show --name baseexample2 --registry $ACR_NAME --query identity.principalId --output tsv) 

# Get resource ID of the base registry
baseregID=$(az acr show --name $BASE_ACR --query id --output tsv) 
```
 
Atribua as permissões de pull da identidade gerenciada ao Registro executando [az role assignment create][az-role-assignment-create]:

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID --role acrpull 
```

## <a name="add-target-registry-credentials-to-the-task"></a>Adicionar credenciais do Registro de destino à tarefa

Execute [az acr task credential add][az-acr-task-credential-add] para adicionar credenciais à tarefa. Transmita o parâmetro `--use-identity [system]` para indicar que a identidade gerenciada atribuída ao sistema da tarefa pode acessar as credenciais.

```azurecli
az acr task credential add \
  --name baseexample2 \
  --registry $ACR_NAME \
  --login-server $BASE_ACR.azurecr.io \
  --use-identity [system] 
```

## <a name="manually-run-the-task"></a>Executar a tarefa manualmente

Use o [az acr task run][az-acr-task-run] para disparar a tarefa manualmente e compilar a imagem do aplicativo. Esta etapa é necessária para que a tarefa acompanhe a dependência da imagem do aplicativo na imagem base.

```azurecli
az acr task run --registry $ACR_NAME --name baseexample2
```

Depois que a tarefa for concluída, anote a **ID de execução** (por exemplo, “da6”) se você desejar concluir a etapa opcional a seguir.

### <a name="optional-run-application-container-locally"></a>Opcional: executar contêiner do aplicativo localmente

Se você estiver trabalhando localmente (não no Cloud Shell), e você tiver o Docker instalado, execute o contêiner para ver o aplicativo renderizado em um navegador da web antes de recriar a imagem base. Se você estiver usando o Cloud Shell, ignore esta seção (Cloud Shell não oferece suporte a `az acr login` ou `docker run`).

Primeiro, faça a autenticação em seu registro de contêiner com [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Agora, execute o contêiner localmente com `docker run`. Substitua **\<run-id\>** pela ID de Execução encontrada na saída da etapa anterior (por exemplo, "da6"). Este exemplo nomeia o contêiner `myapp` e inclui o parâmetro `--rm` para remover o contêiner quando você o interrompe.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue até `http://localhost:8080` no seu navegador, e você deverá ver o número de versão do Node.js renderizado na página da web, semelhante à seguinte. Em uma etapa posterior, você deve aumentar a versão adicionando uma "a" para a cadeia de caracteres de versão.

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-01.png" alt-text="Captura de tela do aplicativo de exemplo no navegador":::

Para interromper e remover o contêiner, execute o comando a seguir:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Listar os builds

Em seguida, liste as execuções de tarefa que as tarefas do ACR concluíram em seu registro usando o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

Se você concluiu o tutorial anterior (e não excluiu o registro), você verá uma saída semelhante à seguinte. Anote o número de execuções de tarefa e a ID de EXECUÇÃO mais recente para que seja possível comparar a saída depois de atualizar a imagem base na próxima seção.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

UN ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

## <a name="update-the-base-image"></a>Atualizar a imagem base

Aqui você pode simular um patch de estrutura na imagem base. Edite **Dockerfile-base** e adicione um "a" depois do número de versão definido em `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 15.2.1a
```

Execute uma tarefa rápida para compilar a imagem base modificada. Anote a **ID de execução** na saída.

```azurecli
az acr build --registry $BASE_ACR --image baseimages/node:15-alpine --file Dockerfile-base .
```

Quando o build tiver sido concluído e a tarefa do ACR tiver efetuado push a nova imagem base para o registro, ele disparará um build da imagem do aplicativo. Pode levar alguns instantes para que a tarefa criada anteriormente disparar o build da imagem do aplicativo, porque ela precisa detectar a imagem base recém-compilada e enviada por push.

## <a name="list-updated-build"></a>Listar o build atualizado

Agora que você atualizou a imagem base, liste suas execuções de tarefa novamente para compará-la com a lista anterior. Se a saída não diferir nem primeiro momento, execute periodicamente o comando para ver a nova execução de tarefa aparecer na lista.

```azurecli
az acr task list-runs --registry $ACR_NAME --output table
```

A saída deverá ser semelhante à seguinte. O GATILHO para o build executado por último será “Atualização da Imagem”, indicando que a tarefa foi iniciada pela sua tarefa rápida da imagem base.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

         PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
ca13      baseexample2    linux       Succeeded  Image Update  2020-11-21T00:06:00Z  00:00:43
ca12      baseexample2    linux       Succeeded  Manual        2020-11-21T00:00:56Z  00:00:36
ca11      baseexample1    linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:34
ca10      taskhelloworld  linux       Succeeded  Image Update  2020-11-20T23:38:24Z  00:00:24
cay                       linux       Succeeded  Manual        2020-11-20T23:38:08Z  00:00:22
cax       baseexample1    linux       Succeeded  Manual        2020-11-20T23:33:12Z  00:00:30
caw       taskhelloworld  linux       Succeeded  Commit        2020-11-20T23:16:07Z  00:00:29
```

Caso deseje realizar a etapa opcional a seguir de execução do contêiner recém-criado para ver o número de versão atualizado, anote o valor da **ID DE EXECUÇÃO** do build disparado pela Atualização da Imagem (na saída anterior, ela é "ca13").

### <a name="optional-run-newly-built-image"></a>Opcional: executar imagem criada recentemente

Se você estiver trabalhando localmente (não no Cloud Shell), e tiver o Docker instalado, execute a nova imagem de aplicativo após a conclusão da sua compilação. Substitua `<run-id>` pela ID de EXECUÇÃO obtida na etapa anterior. Se você estiver usando o Cloud Shell, ignore esta seção (Cloud Shell não oferece suporte a `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue até http://localhost:8081 no seu navegador, e você deverá ver o número de versão do Node.js atualizado (com o “a”) na página da web:

:::image type="content" source="media/container-registry-tutorial-base-image-update/base-update-02.png" alt-text="Captura de tela do aplicativo de exemplo atualizado no navegador":::

O que é importante a observar é que você atualizou sua imagem **base** com um novo número de versão, mas a última imagem de **aplicativo** compilada exibe a nova versão. As Tarefas do ACR obtiveram sua alteração da imagem base e recriaram sua imagem de aplicativo automaticamente.

Para interromper e remover o contêiner, execute o comando a seguir:

```bash
docker stop updatedapp
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como usar uma tarefa para disparar builds de imagem de contêiner automaticamente quando a imagem base da imagem foi atualizada. Agora, vá para o próximo tutorial para saber como disparar tarefas conforme um agendamento definido.

> [!div class="nextstepaction"]
> [Executar uma tarefa em um agendamento](container-registry-tasks-scheduled.md)

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[code-sample]: https://github.com/Azure-Samples/acr-build-helloworld-node
[dockerfile-app]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-app
[dockerfile-base]: https://github.com/Azure-Samples/acr-build-helloworld-node/blob/master/Dockerfile-base

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-update]: /cli/azure/acr/task#az_acr_task_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task]: /cli/azure/acr#az_acr_task
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
