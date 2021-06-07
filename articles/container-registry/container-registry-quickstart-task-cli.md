---
title: Início Rápido – Criar uma imagem de contêiner sob demanda no Azure
description: Use os comandos do Registro de Contêiner do Azure para criar, enviar por push e executar rapidamente uma imagem de contêiner do Docker sob demanda na nuvem do Azure.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: d3ef126812cb36874b74c8569d141787f71a29da
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781443"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Início Rápido: Criar e executar uma imagem de contêiner usando as Tarefas do Registro de Contêiner do Azure

Neste início rápido, você usará comandos das [Tarefas do Registro de Contêiner do Azure][container-registry-tasks-overview] para criar, efetuar push e executar rapidamente uma imagem de contêiner do Docker nativamente no Azure, sem uma instalação local do Docker. Tarefas do ACR é um conjunto de recursos dentro do Registro de Contêiner do Azure para ajudá-lo a gerenciar e a modificar imagens de contêiner em todo o ciclo de vida do contêiner. Este exemplo mostra como descarregar seu ciclo de desenvolvimento de imagem de contêiner de "loop interno" para a nuvem com builds sob demanda usando um Dockerfile local. 

Após este início rápido, explore recursos mais avançados de Tarefas do ACR usando os [tutoriais](container-registry-tutorial-quick-task.md). As Tarefas do ACR podem automatizar builds de imagem com base em confirmações do código ou atualizações de imagem base ou testar vários contêineres, em paralelo, entre outros cenários. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
    
- Este guia de início rápido requer a versão 2.0.58 ou posterior da CLI do Azure. Se você está usando o Azure Cloud Shell, a versão mais recente já está instalada.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se você ainda não tiver um registro de contêiner, primeiro crie um grupo de recursos dentro do comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Crie um registro de contêiner usando o comando [az acr create][az-acr-create]. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No exemplo a seguir, *myContainerRegistry008* é usado. Atualize-o para um valor exclusivo.

```azurecli-interactive
az acr create --resource-group myResourceGroup \
  --name myContainerRegistry008 --sku Basic
```

Este exemplo cria um registro *Básico*, uma opção com otimização de custo para os desenvolvedores que estão aprendendo a usar o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, confira [Camadas de serviço do registro de contêiner][container-registry-skus].

## <a name="build-and-push-image-from-a-dockerfile"></a>Criar uma imagem e efetuar push dela de um Dockerfile

Agora, use o Registro de Contêiner do Azure para criar uma imagem e efetuar push dela. Primeiro, crie um diretório de trabalho local e crie um Dockerfile chamado *Dockerfile* com a única linha: `FROM mcr.microsoft.com/hello-world`. Este é um exemplo simples para criar uma imagem de contêiner do Linux com base na imagem `hello-world` hospedada no Registro de Contêiner da Microsoft. Você pode criar seu próprio Dockerfile padrão e criar imagens para outras plataformas. Se estiver trabalhando em um shell bash, crie o Dockerfile com o seguinte comando:

```bash
echo FROM mcr.microsoft.com/hello-world > Dockerfile
```

Execute o comando [az acr build][az-acr-build], que cria a imagem e, após criá-la com êxito, efetua push dela para o registro. O exemplo a seguir cria a imagem `sample/hello-world:v1` e efetua push dela. O `.` no final do comando define a localização do Dockerfile; neste caso, o diretório atual.

```azurecli-interactive
az acr build --image sample/hello-world:v1 \
  --registry myContainerRegistry008 \
  --file Dockerfile . 
```

A saída de um build bem-sucedido e o push é semelhante ao seguinte:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/build_archive_b0bc1e5d361b44f0833xxxx41b78c24e.tar.gz'...
Sending context (1.856 KiB) to registry: mycontainerregistry008...
Queued a build with ID: ca8
Waiting for agent...
2019/03/18 21:56:57 Using acb_vol_4c7ffa31-c862-4be3-xxxx-ab8e615c55c4 as the home volume
2019/03/18 21:56:57 Setting up Docker configuration...
2019/03/18 21:56:58 Successfully set up Docker configuration
2019/03/18 21:56:58 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/18 21:56:59 Executing step ID: build. Working directory: '', Network: ''
2019/03/18 21:56:59 Obtaining source code and scanning for dependencies...
2019/03/18 21:57:00 Successfully obtained source code and scanned for dependencies
2019/03/18 21:57:00 Launching container with name: build
Sending build context to Docker daemon  13.82kB
Step 1/1 : FROM mcr.microsoft.com/hello-world
latest: Pulling from hello-world
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586fxxxx21577a99efb77324b0fe535
Successfully built fce289e99eb9
Successfully tagged mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:01 Successfully executed container: build
2019/03/18 21:57:01 Executing step ID: push. Working directory: '', Network: ''
2019/03/18 21:57:01 Pushing image: mycontainerregistry008.azurecr.io/sample/hello-world:v1, attempt 1
The push refers to repository [mycontainerregistry008.azurecr.io/sample/hello-world]
af0b15c8625b: Preparing
af0b15c8625b: Layer already exists
v1: digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a size: 524
2019/03/18 21:57:03 Successfully pushed image: mycontainerregistry008.azurecr.io/sample/hello-world:v1
2019/03/18 21:57:03 Step ID: build marked as successful (elapsed time in seconds: 2.543040)
2019/03/18 21:57:03 Populating digests for step ID: build...
2019/03/18 21:57:05 Successfully populated digests for step ID: build
2019/03/18 21:57:05 Step ID: push marked as successful (elapsed time in seconds: 1.473581)
2019/03/18 21:57:05 The following dependencies were found:
2019/03/18 21:57:05
- image:
    registry: mycontainerregistry008.azurecr.io
    repository: sample/hello-world
    tag: v1
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: v1
    digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
  git: {}

Run ID: ca8 was successful after 10s
```

## <a name="run-the-image"></a>Executar a imagem

Agora execute rapidamente a imagem montada e cujo push foi efetuado para seu Registro. Aqui, você usará [az acr run][az-acr-run] para executar o comando de contêiner. No fluxo de trabalho de desenvolvimento de contêiner, essa pode ser uma etapa de validação antes de implantar a imagem ou você pode incluir o comando em um [arquivo YAML de várias etapas][container-registry-tasks-multi-step]. 

O seguinte exemplo usa `$Registry` para especificar o Registro em que você executa o comando:

```azurecli-interactive
az acr run --registry myContainerRegistry008 \
  --cmd '$Registry/sample/hello-world:v1' /dev/null
```

O parâmetro `cmd` deste exemplo executa o contêiner na configuração padrão, mas o `cmd` dá suporte a parâmetros `docker run` adicionais ou, até mesmo, a outros comandos `docker`.

A saída deverá ser semelhante a esta:

```console
Packing source code into tar to upload...
Uploading archived source code from '/tmp/run_archive_ebf74da7fcb04683867b129e2ccad5e1.tar.gz'...
Sending context (1.855 KiB) to registry: mycontainerre...
Queued a run with ID: cab
Waiting for an agent...
2019/03/19 19:01:53 Using acb_vol_60e9a538-b466-475f-9565-80c5b93eaa15 as the home volume
2019/03/19 19:01:53 Creating Docker network: acb_default_network, driver: 'bridge'
2019/03/19 19:01:53 Successfully set up Docker network: acb_default_network
2019/03/19 19:01:53 Setting up Docker configuration...
2019/03/19 19:01:54 Successfully set up Docker configuration
2019/03/19 19:01:54 Logging in to registry: mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Successfully logged into mycontainerregistry008.azurecr.io
2019/03/19 19:01:55 Executing step ID: acb_step_0. Working directory: '', Network: 'acb_default_network'
2019/03/19 19:01:55 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

2019/03/19 19:01:56 Successfully executed container: acb_step_0
2019/03/19 19:01:56 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 0.843801)

Run ID: cab was successful after 6s
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não for mais necessário, você poderá usar o comando [az group delete][az-group-delete] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas lá.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou recursos das Tarefas do ACR para criar, efetuar push e executar rapidamente uma imagem de contêiner do Docker nativamente no Azure, sem uma instalação local do Docker. Prossiga para os tutoriais das Tarefas do Registro de Contêiner do Azure para saber como usar as Tarefas do ACR a fim de automatizar builds e atualizações de imagem.

> [!div class="nextstepaction"]
> [Tutoriais das Tarefas do Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[azure-account]: https://azure.microsoft.com/free/

<!-- LINKS - internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tasks-multi-step]: container-registry-tasks-multi-step.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
