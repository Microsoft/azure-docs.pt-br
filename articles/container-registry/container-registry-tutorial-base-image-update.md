---
title: Tutorial – Automatizar builds de imagem de contêiner na atualização da imagem base – Tarefas do Registro de Contêiner do Azure
description: Neste tutorial, você aprenderá a configurar uma Tarefa do Registro de Contêiner do Azure para disparar builds de imagem de contêiner automaticamente na nuvem quando uma imagem base for atualizada.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 06/12/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 496aa065b3b10eac546dbe41f5a2650acc112d29
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68310515"
---
# <a name="tutorial-automate-container-image-builds-when-a-base-image-is-updated-in-an-azure-container-registry"></a>Tutorial: automatizar builds de imagem de contêiner na atualização da imagem base em um Registro de Contêiner do Azure 

As Tarefas do ACR são compatíveis com a execução de build automatizada quando uma imagem base do contêiner é atualizada, como quando um patch é aplicado ao sistema operacional ou estrutura do aplicativo em uma das suas imagens base. Neste tutorial, você aprenderá como compilar uma tarefa nas Tarefas do ACR que dispara um build na nuvem quando a imagem base do contêiner foi enviada por push para o registro.

Neste tutorial, o último da série:

> [!div class="checklist"]
> * Compilar a imagem base
> * Criar uma tarefa de compilação de imagem do aplicativo
> * Atualizar a imagem base para disparar uma tarefa de imagem de aplicativo
> * Exibir a tarefa disparada
> * Verificar a imagem do aplicativo atualizada

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você quiser usar a CLI do Azure localmente, precisará ter a versão da CLI do Azure **2.0.46** ou posterior instalada. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar a CLI, confira como [instalar a CLI do Azure][azure-cli].

## <a name="prerequisites"></a>Pré-requisitos

### <a name="complete-the-previous-tutorials"></a>Conclua os tutoriais anteriores

Este tutorial pressupõe que você já concluiu as etapas nos dois primeiros tutoriais na série, em que você:

* Crie um registro de contêiner do Azure
* Repositório de exemplo de fork
* Clona o repositório de exemplo
* Cria um token de acesso pessoal do GitHub

Se você ainda não fez isso, conclua os dois primeiros tutoriais antes de continuar:

[Compilar imagens de contêineres na nuvem com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-quick-task.md)

[Automatizar builds de imagem de contêiner com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md)

### <a name="configure-the-environment"></a>Configure o ambiente

Preencha essas variáveis de ambiente de shell com valores apropriados para seu ambiente. Esta etapa não é estritamente necessária, mas torna a execução dos comandos da CLI do Azure com várias linhas neste tutorial um pouco mais fácil. Se não popular essas variáveis de ambiente, você precisará substituir manualmente cada valor sempre que aparecerem nos comandos de exemplo.

```azurecli-interactive
ACR_NAME=<registry-name>        # The name of your Azure container registry
GIT_USER=<github-username>      # Your GitHub user account name
GIT_PAT=<personal-access-token> # The PAT you generated in the second tutorial
```

## <a name="base-images"></a>Imagens base

Definindo a maioria das imagens de contêiner de Dockerfiles especifica uma imagem pai da qual ele se baseia, conhecida como sua *imagem base*. Imagens de base normalmente contêm o sistema operacional, por exemplo [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], em que o resto do camadas do contêiner são aplicadas. Elas também podem incluir estruturas de aplicativo, como [Node.js][base-node] ou [.NET Core][base-dotnet].

### <a name="base-image-updates"></a>Atualizações de imagem base

Uma imagem de base geralmente é atualizada pelo mantenedor de imagem para incluir novos recursos ou aprimoramentos para o sistema operacional ou da estrutura na imagem. Patches de segurança são outra causa comum para uma atualização da imagem base.

Quando uma imagem de base é atualizada, você verá a necessidade de recriar as imagens de contêiner no registro com base nele para incluir os novos recursos e correções. As Tarefas do ACR incluem a capacidade de compilar imagens automaticamente para você quando a imagem base do contêiner for atualizada.

### <a name="tasks-triggered-by-a-base-image-update"></a>Tarefas disparadas por uma atualização de imagem base

* No momento, para builds de imagem de um Dockerfile, uma tarefa do ACR detecta as dependências nas imagens base no mesmo registro de contêiner do Azure, um repositório público do Docker Hub ou um repositório público no registro de contêiner da Microsoft. Se a imagem base especificada na instrução `FROM` residir em um desses locais, a tarefa do ACR adicionará um gancho para garantir que a imagem seja recompilada sempre que sua base for atualizada.

* Quando você cria uma tarefa do ACR com o comando[az acr task create][az-acr-task-create], por padrão, a tarefa é *habilitada* para ser disparada pela atualização da imagem base. Isto é, a propriedade `base-image-trigger-enabled` é definida como True. Se desejar desabilitar esse comportamento da tarefa, atualize a propriedade para False. Por exemplo, execute o seguinte comando [az acr task update][az-acr-task-update]:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* Para habilitar uma tarefa do ACR a fim de determinar e acompanhar as dependências de uma imagem do contêiner, que inclui a própria imagem base, você deve primeiro disparar a tarefa **pelo menos uma vez**. Por exemplo, disparar a tarefa manualmente usando o comando [az acr task run][az-acr-task-run].

* Para disparar uma tarefa na atualização da imagem base, esta precisa ter uma marca *estável*, como `node:9-alpine`. Essa marcação é típica de uma imagem base que é atualizada com o sistema operacional e patches de estrutura para uma versão estável mais recente. Se a imagem base é atualizada com uma nova marca de versão, ela não dispara uma tarefa. Para obter mais informações sobre a marcação de imagens, confira as [diretrizes de práticas recomendadas](https://stevelasker.blog/2018/03/01/docker-tagging-best-practices-for-tagging-and-versioning-docker-images/). 

### <a name="base-image-update-scenario"></a>Cenário de atualização de imagem base

Este tutorial orienta você através de um cenário de atualização da imagem base. O [exemplo de código][code-sample] inclui dois Dockerfiles: uma imagem do aplicativo e uma imagem que ele especifica como base. Nas seções a seguir, você criará uma tarefa do ACR que dispara automaticamente um build da imagem do aplicativo quando uma nova versão da imagem base é enviada por push para o mesmo registro de contêiner.

[Dockerfile-app][dockerfile-app]: um pequeno aplicativo Web Node.js que renderiza uma página da Web estática exibindo a versão do Node.js no qual é baseado. A cadeia de caracteres de versão é simulada: ela exibe o conteúdo de uma variável de ambiente, `NODE_VERSION`, que é definida na imagem base.

[Dockerfile-base][dockerfile-base]: a imagem que `Dockerfile-app` especifica como base. É baseado em uma imagem [Node][base-node] e inclui a variável de ambiente `NODE_VERSION`.

Nas seções a seguir, você criará uma tarefa, atualizará o valor `NODE_VERSION` no Dockerfile da imagem base e usará as Tarefas do ACR para compilar a imagem base. Quando a tarefa do ACR efetua push na nova imagem base para o registro, ela automaticamente dispara um build da imagem do aplicativo. Opcionalmente, você pode executar a imagem de contêiner do aplicativo localmente para ver as cadeias de caracteres de versões diferentes nas imagens compiladas.

Neste tutorial, a tarefa do ACR compila e envia por push uma única imagem do contêiner especificada em um Dockerfile. As Tarefas do ACR também podem executar [tarefas de várias etapas](container-registry-tasks-multi-step.md), usando um arquivo YAML para definir as etapas para criar, efetuar push e, opcionalmente, testar vários contêineres.

## <a name="build-the-base-image"></a>Compilar a imagem base

Comece compilando a imagem base com uma *tarefa rápida* das Tarefas do ACR. Conforme discutido no [primeiro tutorial](container-registry-tutorial-quick-task.md) da série, esse processo não só compila a imagem, mas efetuar push para o registro de contêiner, se o build for bem-sucedido.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

## <a name="create-a-task"></a>Cria uma tarefa

Em seguida, crie uma tarefa com [az acr task create][az-acr-task-create]:

```azurecli-interactive
az acr task create \
    --registry $ACR_NAME \
    --name taskhelloworld \
    --image helloworld:{{.Run.ID}} \
    --arg REGISTRY_NAME=$ACR_NAME.azurecr.io \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node.git \
    --file Dockerfile-app \
    --branch master \
    --git-access-token $GIT_PAT
```

> [!IMPORTANT]
> Se você já tiver criado tarefas durante a versão prévia com o comando `az acr build-task`, essas tarefas precisarão ser recriadas usando o comando [az acr task][az-acr-task].

Esta tarefa é semelhante à tarefa rápida criada no [tutorial anterior](container-registry-tutorial-build-task.md). Ela instrui as Tarefas do ACR para disparar um build de imagem quando as confirmações são enviadas por push para o repositório especificado por `--context`. Embora o Dockerfile usado para criar a imagem no tutorial anterior especifique uma imagem base pública (`FROM node:9-alpine`), o Dockerfile nessa tarefa, [Dockerfile-app][dockerfile-app], especifica uma imagem base no mesmo registro:

```Dockerfile
FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
```

Essa configuração facilita simular um patch do framework na imagem base, posteriormente no tutorial.

## <a name="build-the-application-container"></a>Compilar contêiner de aplicativo

Use o [az acr task run][az-acr-task-run] para disparar a tarefa manualmente e compilar a imagem do aplicativo. Esta etapa garante que a tarefa acompanhe a dependência da imagem do aplicativo na imagem base.

```azurecli-interactive
az acr task run --registry $ACR_NAME --name taskhelloworld
```

Depois que a tarefa for concluída, anote a **ID de execução** (por exemplo, “da6”) se você desejar concluir a etapa opcional a seguir.

### <a name="optional-run-application-container-locally"></a>Opcional: executar contêiner do aplicativo localmente

Se você estiver trabalhando localmente (não no Cloud Shell), e você tiver o Docker instalado, execute o contêiner para ver o aplicativo renderizado em um navegador da web antes de recriar a imagem base. Se você estiver usando o Cloud Shell, ignore esta seção (Cloud Shell não oferece suporte a `az acr login` ou `docker run`).

Primeiro, faça a autenticação em seu registro de contêiner com [az acr login][az-acr-login]:

```azurecli
az acr login --name $ACR_NAME
```

Agora, execute o contêiner localmente com `docker run`. Substitua **\<run-id\>** pela ID de execução encontrada na saída da etapa anterior (por exemplo, “da6”). Este exemplo nomeia o contêiner `myapp` e inclui o parâmetro `--rm` para remover o contêiner quando você o interrompe.

```bash
docker run -d -p 8080:80 --name myapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue até `http://localhost:8080` no seu navegador, e você deverá ver o número de versão do Node.js renderizado na página da web, semelhante à seguinte. Em uma etapa posterior, você deve aumentar a versão adicionando uma "a" para a cadeia de caracteres de versão.

![Captura de tela do aplicativo de exemplo renderizado no navegador][base-update-01]

Para interromper e remover o contêiner, execute o comando a seguir:

```bash
docker stop myapp
```

## <a name="list-the-builds"></a>Listar os builds

Em seguida, liste as execuções de tarefa que as tarefas do ACR concluíram em seu registro usando o comando [az acr task list-runs][az-acr-task-list-runs]:

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

Se você concluiu o tutorial anterior (e não excluiu o registro), você verá uma saída semelhante à seguinte. Anote o número de execuções de tarefa e a ID de EXECUÇÃO mais recente para que seja possível comparar a saída depois de atualizar a imagem base na próxima seção.

```console
$ az acr task list-runs --registry $ACR_NAME --output table

RUN ID    TASK            PLATFORM    STATUS     TRIGGER     STARTED               DURATION
--------  --------------  ----------  ---------  ----------  --------------------  ----------
da6       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual      2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit  2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual      2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual      2018-09-17T22:29:59Z  00:00:57
```

## <a name="update-the-base-image"></a>Atualizar a imagem base

Aqui você pode simular um patch de estrutura na imagem base. Edite **Dockerfile-base**e adicione um "a" depois do número de versão definido em `NODE_VERSION`:

```Dockerfile
ENV NODE_VERSION 9.11.2a
```

Execute uma tarefa rápida para compilar a imagem base modificada. Anote a **ID de execução** na saída.

```azurecli-interactive
az acr build --registry $ACR_NAME --image baseimages/node:9-alpine --file Dockerfile-base .
```

Quando o build tiver sido concluído e a tarefa do ACR tiver efetuado push a nova imagem base para o registro, ele disparará um build da imagem do aplicativo. Pode levar alguns instantes para que a tarefa criada anteriormente disparar o build da imagem do aplicativo, porque ela precisa detectar a imagem base recém-compilada e enviada por push.

## <a name="list-updated-build"></a>Listar o build atualizado

Agora que você atualizou a imagem base, liste suas execuções de tarefa novamente para compará-la com a lista anterior. Se a saída não diferir nem primeiro momento, execute periodicamente o comando para ver a nova execução de tarefa aparecer na lista.

```azurecli-interactive
az acr task list-runs --registry $ACR_NAME --output table
```

A saída deverá ser semelhante à seguinte. O GATILHO para o build executado por último será “Atualização da Imagem”, indicando que a tarefa foi iniciada pela sua tarefa rápida da imagem base.

```console
$ az acr task list-builds --registry $ACR_NAME --output table

Run ID    TASK            PLATFORM    STATUS     TRIGGER       STARTED               DURATION
--------  --------------  ----------  ---------  ------------  --------------------  ----------
da8       taskhelloworld  Linux       Succeeded  Image Update  2018-09-17T23:11:50Z  00:00:33
da7                       Linux       Succeeded  Manual        2018-09-17T23:11:27Z  00:00:35
da6       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T23:07:22Z  00:00:38
da5                       Linux       Succeeded  Manual        2018-09-17T23:06:33Z  00:00:31
da4       taskhelloworld  Linux       Succeeded  Git Commit    2018-09-17T23:03:45Z  00:00:44
da3       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:55:35Z  00:00:35
da2       taskhelloworld  Linux       Succeeded  Manual        2018-09-17T22:50:59Z  00:00:32
da1                       Linux       Succeeded  Manual        2018-09-17T22:29:59Z  00:00:57
```

Se você desejar realizar a seguinte etapa opcional de executar o contêiner recém-criado para ver o número de versão atualizado, anote o valor da **ID de EXECUÇÃO** para o build disparado pela Atualização da Imagem (na saída anterior, é “da8”).

### <a name="optional-run-newly-built-image"></a>Opcional: executar imagem criada recentemente

Se você estiver trabalhando localmente (não no Cloud Shell), e tiver o Docker instalado, execute a nova imagem de aplicativo após a conclusão da sua compilação. Substitua `<run-id>` pela ID de EXECUÇÃO obtida na etapa anterior. Se você estiver usando o Cloud Shell, ignore esta seção (Cloud Shell não oferece suporte a `docker run`).

```bash
docker run -d -p 8081:80 --name updatedapp --rm $ACR_NAME.azurecr.io/helloworld:<run-id>
```

Navegue até http://localhost:8081 no seu navegador, e você deverá ver o número de versão do Node.js atualizado (com o “a”) na página da web:

![Captura de tela do aplicativo de exemplo renderizado no navegador][base-update-02]

O que é importante a observar é que você atualizou sua imagem **base** com um novo número de versão, mas a última imagem de **aplicativo** compilada exibe a nova versão. As Tarefas do ACR obtiveram sua alteração da imagem base e recriaram sua imagem de aplicativo automaticamente.

Para interromper e remover o contêiner, execute o comando a seguir:

```bash
docker stop updatedapp
```

## <a name="clean-up-resources"></a>Limpar recursos

Para remover todos os recursos que você criou nesta série de tutoriais, incluindo o registro de contêiner, a instância do contêiner, o cofre de chaves e a entidade de serviço, emita os seguintes comandos:

```azurecli-interactive
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como usar uma tarefa para disparar builds de imagem de contêiner automaticamente quando a imagem base da imagem foi atualizada. Agora, vá para saber mais sobre a autenticação para o registro de contêiner.

> [!div class="nextstepaction"]
> [Autenticação no Registro de Contêiner do Azure](container-registry-authentication.md)

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
[az-acr-build]: /cli/azure/acr#az-acr-build-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-task-list-runs]: /cli/azure/acr
[az-acr-task]: /cli/azure/acr

<!-- IMAGES -->
[base-update-01]: ./media/container-registry-tutorial-base-image-update/base-update-01.png
[base-update-02]: ./media/container-registry-tutorial-base-image-update/base-update-02.png
