---
title: Início Rápido – Criar e executar uma imagem de contêiner no Registro de Contêiner do Azure
description: Execute rapidamente tarefas com o Registro de Contêiner do Azure para criar e executar uma imagem de contêiner sob demanda, na nuvem.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: quickstart
ms.date: 04/02/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: e5e02d8194f9164a03bb27d932df45d91486c518
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310641"
---
# <a name="quickstart-build-and-run-a-container-image-using-azure-container-registry-tasks"></a>Início Rápido: Criar e executar uma imagem de contêiner usando as Tarefas do Registro de Contêiner do Azure

Neste início rápido, você usará comandos de Tarefas do Registro de Contêiner do Azure para criar, efetuar push e executar rapidamente uma imagem de contêiner do Docker nativamente dentro do Azure, mostrando como descarregar seu ciclo de desenvolvimento de “loop interno” para a nuvem. [Tarefas do ACR][container-registry-tasks-overview] é um conjunto de recursos dentro do Registro de Contêiner do Azure para ajudá-lo a gerenciar e a modificar imagens de contêiner em todo o ciclo de vida do contêiner. 

Após este início rápido, explore mais recursos avançados de Tarefas do ACR. As Tarefas do ACR podem automatizar builds de imagem com base em confirmações do código ou atualizações de imagem base ou testar vários contêineres, em paralelo, entre outros cenários. 

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][azure-account] antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Você pode usar o Azure Cloud Shell ou uma instalação local da CLI do Azure para concluir esse guia de início rápido. Se você quer usá-lo localmente, recomendamos usar a versão 2.0.58 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se você ainda não tiver um registro de contêiner, primeiro crie um grupo de recursos dentro do comando [az group create][az-group-create]. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Crie um registro de contêiner usando o comando [az acr create][az-acr-create]. O nome do registro deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. No exemplo a seguir, *myContainerRegistry008* é usado. Atualize-o para um valor exclusivo.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry008 --sku Basic
```

Este exemplo cria um Registro *Básico*, uma opção com otimização de custo para desenvolvedores que estão aprendendo a usar o Registro de Contêiner do Azure. Para obter detalhes sobre as camadas de serviço disponíveis, confira [SKUs de registro de contêiner][container-registry-skus].

## <a name="build-an-image-from-a-dockerfile"></a>Criar uma imagem com base em um Dockerfile

Agora, use o Registro de Contêiner do Azure para criar uma imagem. Primeiro, crie um diretório de trabalho e, em seguida, crie um Dockerfile chamado *Dockerfile* com o seguinte conteúdo. Este é um exemplo simples para criar uma imagem de contêiner do Linux, mas é possível criar seu próprio Dockerfile padrão e montar imagens para outras plataformas.

```bash
echo FROM hello-world > Dockerfile
```

Execute o comando [az acr build][az-acr-build] para criar a imagem. Quando montada com êxito, o push da imagem é efetuado para o Registro. O exemplo a seguir efetua push da imagem `sample/hello-world:v1`. O `.` no final do comando define a localização do Dockerfile; neste caso, o diretório atual.

```azurecli-interactive
az acr build --image sample/hello-world:v1 --registry myContainerRegistry008 --file Dockerfile . 
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
Step 1/1 : FROM hello-world
latest: Pulling from library/hello-world
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

Agora execute rapidamente a imagem montada e cujo push foi efetuado para seu Registro. Em seu fluxo de trabalho de desenvolvimento de contêiner, isso pode ser uma etapa de validação antes da implantação da imagem.

Crie um arquivo *quickrun.yaml* em um diretório de trabalho local com o seguinte conteúdo para uma única etapa. Substitua o nome do servidor de logon do Registro para *\<acrLoginServer\>* . O nome do servidor de logon está no formato *\<nomedoregistro\>.azurecr.io* (tudo em minúsculas), por exemplo, *mycontainerregistry008.azurecr.io*. Este exemplo supõe que você montou a imagem `sample/hello-world:v1` e efetuou push dela na seção anterior:

```yml
steps:
  - cmd: <acrLoginServer>/sample/hello-world:v1
```

A etapa `cmd` neste exemplo executa o contêiner em sua configuração padrão, mas o `cmd` dá suporte a parâmetros `docker run` adicionais ou até mesmo outros comandos `docker`.

Execute o contêiner com o seguinte comando:

```azurecli-interactive
az acr run --registry myContainerRegistry008 --file quickrun.yaml .
```

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

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o comando [az group delete][az-group-delete] para remover o grupo de recursos, o registro de contêiner e as imagens de contêiner armazenadas lá.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou recursos de Tarefas do ACR para criar, efetuar push e executar rapidamente uma imagem de contêiner do Docker nativamente dentro do Azure. Prossiga para os tutoriais do Registro de Contêiner do Azure para aprender como usar as Tarefas do ACR para automatizar builds e atualizações de imagem.

> [!div class="nextstepaction"]
> [Tutoriais de Registro de Contêiner do Azure][container-registry-tutorial-quick-task]

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
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-tasks-overview]: container-registry-tasks-overview.md
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
[container-registry-skus]: container-registry-skus.md
[azure-cli-install]: /cli/azure/install-azure-cli
