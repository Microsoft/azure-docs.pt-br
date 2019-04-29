---
title: Automatizar o build, teste e aplicação de patch de imagem do Registro de Contêiner do Azure com tarefas de várias etapas
description: Uma introdução às tarefas em várias etapas, um recurso das Tarefas do ACR no Registro de Contêiner do Azure, que fornece fluxos de trabalho baseados em tarefas para criar, testar e corrigir imagens de contêiner na nuvem.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/28/2019
ms.author: danlep
ms.openlocfilehash: ac0e4e9019a35d3fdb35c0b7af9cb1289f4bceeb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60829575"
---
# <a name="run-multi-step-build-test-and-patch-tasks-in-acr-tasks"></a>Executar tarefas de build, teste e aplicação de patch de várias etapas tarefas do ACR

Tarefas de várias etapas estendem a capacidade de build e envio por push de imagem única do ACR tarefas com fluxos de trabalho baseados em vários contêineres e várias etapas. Use tarefas com várias etapas para criar e enviar várias imagens, em série ou em paralelo. Em seguida, execute essas imagens como comandos em uma única execução de tarefa. Cada etapa define uma operação de envio por push ou build de imagem de contêiner, podendo também definir a execução de um contêiner. Cada etapa em uma tarefa de várias etapas usa um contêiner como seu ambiente de execução.

> [!IMPORTANT]
> Se você já tiver criado tarefas durante a versão prévia com o comando `az acr build-task`, essas tarefas precisarão ser recriadas usando o comando [az acr task][az-acr-task].

Por exemplo, você pode executar uma tarefa com etapas que automatizam a seguinte lógica:

1. Compilar uma imagem do aplicativo Web
1. Executar o contêiner de aplicativo Web
1. Compilar uma imagem de teste do aplicativo Web
1. Executar o contêiner de teste de aplicativo Web que executa testes em relação à execução do contêiner de aplicativo
1. Quando os testes são aprovados, compilar um pacote de arquivo morto de gráfico do Helm
1. Executar uma `helm upgrade` usando o novo pacote de arquivos de gráficos do Helm

Todas as etapas são executadas no Azure, descarregando o trabalho para os recursos de computação do Azure e liberando você das tarefas de gerenciamento da infraestrutura. Além do Registro de Contêiner do Azure, você paga apenas pelos recursos que usar. Para obter informações sobre preços, veja a seção **Build de Contêiner** em [Preços do Registro de Contêiner do Azure][pricing].


## <a name="common-task-scenarios"></a>Cenários comuns de tarefas

As tarefas em várias etapas permitem cenários como a seguinte lógica:

* Compilar, marcar e enviar por push uma ou mais imagens de contêiner, em série ou em paralelo.
* Executar e capturar resultados de cobertura de código e de teste de unidade.
* Executar e capturar testes funcionais. O ACR Tasks suporta a execução de mais de um contêiner, executando uma série de solicitações entre eles.
* Realizar execução baseada em tarefas, incluindo as etapas pré/pós de um build de imagem de contêiner.
* Implantar um ou mais contêineres com seu mecanismo de implantação favorito no seu ambiente de destino.

## <a name="multi-step-task-definition"></a>Definição de tarefa de várias etapas

Uma tarefa de várias etapas nas Tarefas do ACR é definida como uma série de etapas dentro de um arquivo YAML. Cada etapa pode especificar dependências após a conclusão bem-sucedida de uma ou mais etapas anteriores. Os seguintes tipos de etapa de tarefa estão disponíveis:

* [`build`](container-registry-tasks-reference-yaml.md#build): Criar um ou mais imagens de contêiner usando o familiar `docker build` sintaxe, em série ou em paralelo.
* [`push`](container-registry-tasks-reference-yaml.md#push): Push de imagens criadas para um registro de contêiner. Registros privados como o Registro de Contêiner do Azure são compatíveis, o mesmo se aplica ao Hub do Docker público.
* [`cmd`](container-registry-tasks-reference-yaml.md#cmd): Execute um contêiner, de modo que ele pode operar como uma função dentro do contexto da tarefa em execução. Você pode passar parâmetros para o `[ENTRYPOINT]` do contêiner e especificar propriedades como env, detach e outros parâmetros de `docker run` familiares. O tipo de etapa `cmd` permite testes de unidade e funcionais, com execução simultânea em contêineres.

Os trechos de código a seguir mostram como combinar esses tipos de etapa da tarefa. Tarefas de várias etapas podem ser tão simples quanto a criação de uma única imagem de um Dockerfile e enviar por push para seu registro, com um arquivo YAML semelhante a:

```yml
version: v1.0.0
steps:
  - build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
  - push: ["{{.Run.Registry}}/hello-world:{{.Run.ID}}"]
```

Ou, mais complexo, como essa definição fictícia de várias etapa que inclui as etapas para compilação, teste, o pacote do helm e o helm implantar (registro de contêiner e a configuração do repositório do Helm não mostrados):

```yml
version: v1.0.0
steps:
  - id: build-web
    build: -t {{.Run.Registry}}/hello-world:{{.Run.ID}} .
    when: ["-"]
  - id: build-tests
    build -t {{.Run.Registry}}/hello-world-tests ./funcTests
    when: ["-"]
  - id: push
    push: ["{{.Run.Registry}}/helloworld:{{.Run.ID}}"]
    when: ["build-web", "build-tests"]
  - id: hello-world-web
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
  - id: funcTests
    cmd: {{.Run.Registry}}/helloworld:{{.Run.ID}}
    env: ["host=helloworld:80"]
  - cmd: {{.Run.Registry}}/functions/helm package --app-version {{.Run.ID}} -d ./helm ./helm/helloworld/
  - cmd: {{.Run.Registry}}/functions/helm upgrade helloworld ./helm/helloworld/ --reuse-values --set helloworld.image={{.Run.Registry}}/helloworld:{{.Run.ID}}
```

Ver [exemplos de tarefas] [ task-examples] para concluir arquivos YAML de tarefa de várias etapas e Dockerfiles para vários cenários.

## <a name="run-a-sample-task"></a>Executar uma tarefa de exemplo

Tarefas dão suporte tanto à execução manual, chamada de "execução rápida", quanto à execução automatizada na confirmação de Git ou atualização de imagem base.

Para executar uma tarefa, você primeiro define as etapas da tarefa em um arquivo YAML, depois executa o comando da CLI do Azure [az acr run][az-acr-run].

Aqui está um exemplo de comando da CLI do Azure que executa uma tarefa usando um arquivo YAML de tarefa de exemplo. As etapas compilam uma imagem e, em seguida, enviam-na por push. Antes de executar o comando, atualize `\<acrName\>` com o nome do seu próprio Registro de Contêiner do Azure.

```azurecli
az acr run --registry <acrName> -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Quando você executar a tarefa, a saída deverá mostrar o progresso de cada etapa definida no arquivo YAML. Na saída a seguir, as etapas aparecem como `acb_step_0` e `acb_step_1`.

```console
$ az acr run --registry myregistry -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
Sending context to registry: myregistry...
Queued a run with ID: yd14
Waiting for an agent...
2018/09/12 20:08:44 Using acb_vol_0467fe58-f6ab-4dbd-a022-1bb487366941 as the home volume
2018/09/12 20:08:44 Creating Docker network: acb_default_network
2018/09/12 20:08:44 Successfully set up Docker network: acb_default_network
2018/09/12 20:08:44 Setting up Docker configuration...
2018/09/12 20:08:45 Successfully set up Docker configuration
2018/09/12 20:08:45 Logging in to registry: myregistry.azurecr-test.io
2018/09/12 20:08:46 Successfully logged in
2018/09/12 20:08:46 Executing step: acb_step_0
2018/09/12 20:08:46 Obtaining source code and scanning for dependencies...
2018/09/12 20:08:47 Successfully obtained source code and scanned for dependencies
Sending build context to Docker daemon  109.6kB
Step 1/1 : FROM hello-world
 ---> 4ab4c602aa5e
Successfully built 4ab4c602aa5e
Successfully tagged myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:48 Executing step: acb_step_1
2018/09/12 20:08:48 Pushing image: myregistry.azurecr-test.io/hello-world:yd14, attempt 1
The push refers to repository [myregistry.azurecr-test.io/hello-world]
428c97da766c: Preparing
428c97da766c: Layer already exists
yd14: digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812 size: 524
2018/09/12 20:08:55 Successfully pushed image: myregistry.azurecr-test.io/hello-world:yd14
2018/09/12 20:08:55 Step id: acb_step_0 marked as successful (elapsed time in seconds: 2.035049)
2018/09/12 20:08:55 Populating digests for step id: acb_step_0...
2018/09/12 20:08:57 Successfully populated digests for step id: acb_step_0
2018/09/12 20:08:57 Step id: acb_step_1 marked as successful (elapsed time in seconds: 6.832391)
The following dependencies were found:
- image:
    registry: myregistry.azurecr-test.io
    repository: hello-world
    tag: yd14
    digest: sha256:1a6fd470b9ce10849be79e99529a88371dff60c60aab424c077007f6979b4812
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0add3ace90ecb4adbf7777e9aacf18357296e799f81cabc9fde470971e499788
  git: {}


Run ID: yd14 was successful after 19s
```

Para obter mais informações sobre builds automatizados na confirmação do Git na atualização da imagem base, consulte os artigos de tutorial [Automatizar builds de imagem](container-registry-tutorial-build-task.md) e [Builds de atualização de imagem base](container-registry-tutorial-base-image-update.md).

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar referências e exemplos de tarefas de várias etapas aqui:

* [Referência de tarefa](container-registry-tasks-reference-yaml.md) – tipos de etapa de tarefa, suas propriedades e uso.
* [Exemplos de tarefas][task-examples] – arquivos `task.yaml` de exemplo para vários cenários, simples e complexos.
* [Repositório de cmd](https://github.com/AzureCR/cmd) -uma coleção de contêineres, como comandos para tarefas ACR.

<!-- IMAGES -->

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[task-examples]: https://github.com/Azure-Samples/acr-tasks
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task]: /cli/azure/acr/task