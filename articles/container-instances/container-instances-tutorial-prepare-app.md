---
title: Tutorial – Preparar a imagem de contêiner para Instâncias de Contêiner do Azure
description: Tutorial das Instâncias de Contêiner do Azure, parte 1 de 3 – Preparar um aplicativo em uma imagem de contêiner para implantação nas Instâncias de Contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 719237f63d387cf56ab7947f8f168e0aa4351376
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68325577"
---
# <a name="tutorial-create-a-container-image-for-deployment-to-azure-container-instances"></a>Tutorial: Criar uma imagem de contêiner para implantação nas Instâncias de Contêiner do Azure

As Instâncias de Contêiner do Azure permitem a implantação de contêineres do Docker na infraestrutura do Azure sem o provisionamento de quaisquer máquinas virtuais ou a adoção de serviço de nível superior. Neste tutorial, você empacotará um aplicativo Web pequeno no Node.js em uma imagem de contêiner que pode ser executada usando as Instâncias de Contêiner do Azure.

Neste artigo, a primeira parte da série, você:

> [!div class="checklist"]
> * Clonar código-fonte do aplicativo do GitHub
> * Cria uma imagem de contêiner da origem do aplicativo
> * Testa a imagem em um ambiente de Docker local

Nas partes dois e três do tutorial, você carregará sua imagem no Registro de Contêiner do Azure e depois a implantará nas Instâncias de Contêiner do Azure.

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="get-application-code"></a>Obter o código de aplicativo

O aplicativo de exemplo neste tutorial é um aplicativo Web simples criado no [Node.js][nodejs]. O aplicativo serve a uma página HTML estática e é semelhante à captura de tela a seguir:

![Aplicativo de tutorial mostrado no navegador][aci-tutorial-app]

Use o Git para clonar o repositório do aplicativo de exemplo:

```bash
git clone https://github.com/Azure-Samples/aci-helloworld.git
```

Também é possível [baixar o arquivo morto ZIP][aci-helloworld-zip] diretamente do GitHub.

## <a name="build-the-container-image"></a>Criar a imagem de contêiner

O Dockerfile no aplicativo de exemplo mostra como o contêiner é criado. Ele é iniciado por meio de uma [imagem oficial do Node.js][docker-hub-nodeimage] com base no [Alpine Linux][alpine-linux], uma distribuição pequena que é adequada para uso com contêineres. Em seguida, ele copia os arquivos do aplicativo para o contêiner, instala dependências usando o Gerenciador de Pacotes do Node e inicia o aplicativo.

```Dockerfile
FROM node:8.9.3-alpine
RUN mkdir -p /usr/src/app
COPY ./app/ /usr/src/app/
WORKDIR /usr/src/app
RUN npm install
CMD node /usr/src/app/index.js
```

Use o comando [docker build][docker-build] para criar a imagem de contêiner e marque-o como *aci-tutorial-app*:

```bash
docker build ./aci-helloworld -t aci-tutorial-app
```

A saída do comando [docker build][docker-build] é semelhante ao seguinte (truncado para legibilidade):

```console
$ docker build ./aci-helloworld -t aci-tutorial-app
Sending build context to Docker daemon  119.3kB
Step 1/6 : FROM node:8.9.3-alpine
8.9.3-alpine: Pulling from library/node
88286f41530e: Pull complete
84f3a4bf8410: Pull complete
d0d9b2214720: Pull complete
Digest: sha256:c73277ccc763752b42bb2400d1aaecb4e3d32e3a9dbedd0e49885c71bea07354
Status: Downloaded newer image for node:8.9.3-alpine
 ---> 90f5ee24bee2
...
Step 6/6 : CMD node /usr/src/app/index.js
 ---> Running in f4a1ea099eec
 ---> 6edad76d09e9
Removing intermediate container f4a1ea099eec
Successfully built 6edad76d09e9
Successfully tagged aci-tutorial-app:latest
```

Use o comando [docker images][docker-images] para ver a imagem criada:

```bash
docker images
```

Sua imagem recém-criada deve aparecer na lista:

```console
$ docker images
REPOSITORY          TAG       IMAGE ID        CREATED           SIZE
aci-tutorial-app    latest    5c745774dfa9    39 seconds ago    68.1 MB
```

## <a name="run-the-container-locally"></a>Executar o contêiner localmente

Antes de implantar o contêiner nas Instâncias de Contêiner do Azure, use [docker run][docker-run] para executá-lo localmente e confirmar se ele funciona. A opção `-d` permite que o contêiner seja executado em segundo plano, enquanto `-p` permite mapear uma porta aleatória em seu computador para a porta 80 no contêiner.

```bash
docker run -d -p 8080:80 aci-tutorial-app
```

A saída do comando `docker run` exibe a identificação do contêiner em execução se o comando teve êxito:

```console
$ docker run -d -p 8080:80 aci-tutorial-app
a2e3e4435db58ab0c664ce521854c2e1a1bda88c9cf2fcff46aedf48df86cccf
```

Agora, navegue para `http://localhost:8080` no navegador para confirmar que o contêiner está sendo executado. Você verá uma página da Web semelhante à seguinte:

![Executando o aplicativo localmente no navegador][aci-tutorial-app-local]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de contêiner que pode ser implantada nas Instâncias de Contêiner do Azure e verificou se ela é executada localmente. Até agora, foi feito o seguinte:

> [!div class="checklist"]
> * A fonte do aplicativo foi clonada do GitHub
> * Uma imagem de contêiner da origem do aplicativo foi criada
> * O contêiner foi testado localmente

Siga para o próximo tutorial da série para saber mais sobre como armazenar imagens de contêiner em um Registro de Contêiner do Azure:

> [!div class="nextstepaction"]
> [Enviar imagem por push ao Registro de Contêiner do Azure](container-instances-tutorial-prepare-acr.md)

<!--- IMAGES --->
[aci-tutorial-app]:./media/container-instances-quickstart/aci-app-browser.png
[aci-tutorial-app-local]: ./media/container-instances-tutorial-prepare-app/aci-app-browser-local.png

<!-- LINKS - External -->
[aci-helloworld-zip]: https://github.com/Azure-Samples/aci-helloworld/archive/master.zip
[alpine-linux]: https://alpinelinux.org/
[docker-build]: https://docs.docker.com/engine/reference/commandline/build/
[docker-get-started]: https://docs.docker.com/get-started/
[docker-hub-nodeimage]: https://store.docker.com/images/node
[docker-images]: https://docs.docker.com/engine/reference/commandline/images/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/
[nodejs]: https://nodejs.org

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
