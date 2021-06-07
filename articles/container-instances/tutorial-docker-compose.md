---
title: Tutorial – Usar o Docker Compose para implantar o grupo de vários contêineres
description: Use o Docker Compose para compilar e executar um aplicativo de vários contêineres e, em seguida, colocar o aplicativo em Instâncias de Contêiner do Azure
ms.topic: tutorial
ms.date: 10/28/2020
ms.custom: ''
ms.openlocfilehash: ef08b9f9e0f596f1d94c0e6edfd46f735fe78053
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786901"
---
# <a name="tutorial-deploy-a-multi-container-group-using-docker-compose"></a>Tutorial: Implantar um grupo de vários contêineres usando o Docker Compose 

Neste tutorial, você usa o [Docker Compose](https://docs.docker.com/compose/) para definir e executar um aplicativo de vários contêineres localmente e, em seguida, implantá-lo como um [grupo de contêineres](container-instances-container-groups.md) em Instâncias de Contêiner do Azure. 

Execute contêineres em Instâncias de Contêiner do Azure sob demanda quando você desenvolve aplicativos nativos de nuvem com o Docker e quer passar diretamente do desenvolvimento local para a implantação na nuvem. Essa funcionalidade é habilitada pela [integração entre o Docker e o Azure](https://docs.docker.com/engine/context/aci-integration/). Você pode usar comandos nativos do Docker para executar [uma única instância de contêiner](quickstart-docker-cli.md) ou um grupo de vários contêineres no Azure.

> [!IMPORTANT]
> Nem todos os recursos das Instâncias de Contêiner do Azure são compatíveis. Forneça comentários sobre a integração Docker-Azure criando um problema no repositório GitHub [Integração de ACI do Docker](https://github.com/docker/aci-integration-beta).

> [!TIP]
> Use a [extensão do Docker](https://aka.ms/VSCodeDocker) para Visual Studio Code para uma experiência integrada de desenvolvimento, execução e gerenciamento de contêineres, imagens e contextos.

Neste artigo você:

> [!div class="checklist"]
> * Criar um Registro de Contêiner do Azure
> * Clonar código-fonte do aplicativo do GitHub
> * Usar o Docker Compose para criar uma imagem e executar um aplicativo de vários contêineres localmente
> * Enviar por push a imagem do aplicativo para o Registro de contêiner
> * Criar um contexto do Azure para o Docker
> * Abrir o aplicativo em Instâncias de Contêiner do Azure

## <a name="prerequisites"></a>Pré-requisitos

* **CLI do Azure** – você deve ter a CLI do Azure instalada no computador local. A versão 2.10.1 ou posterior é recomendada. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

* **Docker Desktop** – você deve usar o Docker Desktop versão 2.3.0.5 ou posterior, disponível para [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) ou [macOS](https://desktop.docker.com/mac/edge/Docker.dmg). Ou instale a [CLI de Integração do Docker ACI para Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux).

[!INCLUDE [container-instances-create-registry](../../includes/container-instances-create-registry.md)]

## <a name="get-application-code"></a>Obter o código de aplicativo

O aplicativo de exemplo usado neste tutorial é um aplicativo de votação básico. O aplicativo consiste de um componente Web de front-end e uma instância Redis de back-end. O componente Web é empacotado em uma imagem de contêiner personalizada. A instância Redis utiliza uma imagem não modificada do Hub Docker.

Use o [git](https://git-scm.com/downloads) para clonar o aplicativo de exemplo para seu ambiente de desenvolvimento:

```console
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Altere para o diretório clonado.

```console
cd azure-voting-app-redis
```

Dentro do diretório está o código-fonte do aplicativo e um arquivo Docker Compose pré-criado, docker-compose.yaml.

## <a name="modify-docker-compose-file"></a>Modificar arquivo do Docker Compose

Abra docker-compose.yaml em um editor de texto. O arquivo configura os serviços `azure-vote-back` e `azure-vote-front`.

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "8080:80"
```

Na configuração do `azure-vote-front`, faça as duas seguintes alterações:

1. Atualize a propriedade `image` no serviço `azure-vote-front`. Prefixe o nome da imagem com o nome do servidor de logon do seu Registro de contêiner do Azure, \<acrName\>.azurecr.io. Por exemplo, se o Registro for nomeado *myregistry*, o nome do servidor de logon será *myregistry.azurecr.io* (todas as letras minúsculas) e a propriedade de imagem será `myregistry.azurecr.io/azure-vote-front`.
1. Altere o mapeamento de `ports` para `80:80`. Salve o arquivo.

O arquivo atualizado deverá ser parecido com o seguinte:

```yml
version: '3'
services:
  azure-vote-back:
    image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
    container_name: azure-vote-back
    environment:
      ALLOW_EMPTY_PASSWORD: "yes"
    ports:
        - "6379:6379"

  azure-vote-front:
    build: ./azure-vote
    image: myregistry.azurecr.io/azure-vote-front
    container_name: azure-vote-front
    environment:
      REDIS: azure-vote-back
    ports:
        - "80:80"
```

Ao fazer essas substituições, a imagem de `azure-vote-front` que você cria na próxima etapa é marcada para o Registro de contêiner do Azure e a imagem pode ser retirada para ser executada em Instâncias de Contêiner do Azure.

> [!TIP]
> Você não precisa usar um Registro de contêiner do Azure para esse cenário. Por exemplo, você pode escolher um repositório privado no Docker Hub para hospedar a imagem do aplicativo. Se você escolher um Registro diferente, atualize a propriedade de imagem de modo adequado.

## <a name="run-multi-container-application-locally"></a>Executar aplicativo de vários contêineres localmente

Execute [docker-compose up](https://docs.docker.com/compose/reference/up/), que usa o arquivo `docker-compose.yaml` de exemplo para criar a imagem de contêiner, baixar a imagem Redis e iniciar o aplicativo:

```console
docker-compose up --build -d
```

Quando completado, use o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver as imagens criadas. Três imagens foram baixadas ou criadas. A imagem `azure-vote-front` contém o aplicativo de front-end, que usa a imagem `uwsgi-nginx-flask` como base. A imagem `redis` é usada para iniciar uma instância do Redis.

```
$ docker images

REPOSITORY                                TAG        IMAGE ID            CREATED             SIZE
myregistry.azurecr.io/azure-vote-front    latest     9cc914e25834        40 seconds ago      944MB
mcr.microsoft.com/oss/bitnami/redis       6.0.8      3a54a920bb6c        4 weeks ago          103MB
tiangolo/uwsgi-nginx-flask                python3.6  788ca94b2313        9 months ago        9444MB
```

Execute o comando [docker ps](https://docs.docker.com/engine/reference/commandline/ps/) para ver os contêineres em execução:

```
$ docker ps

CONTAINER ID        IMAGE                                      COMMAND                  CREATED             STATUS              PORTS                           NAMES
82411933e8f9        myregistry.azurecr.io/azure-vote-front     "/entrypoint.sh /sta…"   57 seconds ago      Up 30 seconds       443/tcp, 0.0.0.0:80->80/tcp   azure-vote-front
b62b47a7d313        mcr.microsoft.com/oss/bitnami/redis:6.0.8  "/opt/bitnami/script…"   57 seconds ago      Up 30 seconds       0.0.0.0:6379->6379/tcp          azure-vote-back
```

Para ver o aplicativo em execução, insira `http://localhost:80` em um navegador da Web local. O aplicativo de exemplo é carregado, conforme mostra o exemplo a seguir:

:::image type="content" source="media/tutorial-docker-compose/azure-vote.png" alt-text="Imagem do aplicativo de votação":::

Depois de tentar o aplicativo local, execute [docker-compose down](https://docs.docker.com/compose/reference/down/) para interromper o aplicativo e remover os contêineres.

```console
docker-compose down
```

## <a name="push-image-to-container-registry"></a>Enviar imagem por push para o Registro de contêiner

Para implantar o aplicativo nas Instâncias de Contêiner do Azure, envie a imagem por push de `azure-vote-front` para o Registro de contêiner. Execute [docker-compose push](https://docs.docker.com/compose/reference/push) para enviar a imagem por push:

```console
docker-compose push
```

O envio por push para o Registro pode levar alguns minutos.

Para verificar se a imagem está armazenada no Registro, execute o comando [az acr repository show](/cli/azure/acr/repository#az_acr_repository_show):

```azurecli
az acr repository show --name <acrName> --repository azure-vote-front
```

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="deploy-application-to-azure-container-instances"></a>Implantar aplicativo em instâncias de Contêiner do Azure

Em seguida, altere para o contexto da ACI. Os próximos comandos do Docker serão executados nesse contexto.

```console
docker context use myacicontext
```

Execute `docker compose up` para iniciar o aplicativo em Instâncias de Contêiner do Azure. A imagem de `azure-vote-front` é extraída do Registro de contêiner e o grupo de contêineres é criado em Instâncias de Contêiner do Azure.

```console
docker compose up
```

> [!NOTE]
> Os comandos do Docker Compose disponíveis atualmente em um contexto de ACI são `docker compose up` e `docker compose down`. Não há hífen entre `docker` e `compose` nesses comandos.

Em um curto período, o grupo de contêineres é implantado. Saída de exemplo:

```
[+] Running 3/3
 ⠿ Group azurevotingappredis  Created                          3.6s
 ⠿ azure-vote-back            Done                             10.6s
 ⠿ azure-vote-front           Done                             10.6s
```

Execute `docker ps` para ver os contêineres em execução e o endereço IP atribuído ao grupo de contêineres.

```console
docker ps
```

Saída de exemplo:

```
CONTAINER ID                           IMAGE                                         COMMAND             STATUS              PORTS
azurevotingappredis_azure-vote-back    mcr.microsoft.com/oss/bitnami/redis:6.0.8                         Running             52.179.23.131:6379->6379/tcp
azurevotingappredis_azure-vote-front   myregistry.azurecr.io/azure-vote-front                            Running             52.179.23.131:80->80/tcp
```

Para ver o aplicativo em execução na nuvem, insira o endereço IP exibido em um navegador da Web local. Neste exemplo, insira `52.179.23.131`. O aplicativo de exemplo é carregado, conforme mostra o exemplo a seguir:

:::image type="content" source="media/tutorial-docker-compose/azure-vote-aci.png" alt-text="Imagem do aplicativo de votação no ACI":::

Para ver os logs do contêiner de front-end, execute o comando [docker logs](https://docs.docker.com/engine/reference/commandline/logs). Por exemplo:

```console
docker logs azurevotingappredis_azure-vote-front
```

Você também pode usar o portal do Azure ou outras ferramentas do Azure para ver as propriedades e o status do grupo de contêineres implantado.

Quando você terminar de experimentar o aplicativo, interrompa o aplicativo e os contêineres com `docker compose down`:

```console
docker compose down
```

Esse comando exclui o grupo de contêineres nas Instâncias de Contêiner do Azure.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o Docker Compose para alternar da execução de um aplicativo de vários contêineres localmente para execução em Instâncias de Contêiner do Azure. Você aprendeu a:

> [!div class="checklist"]
> * Criar um Registro de Contêiner do Azure
> * Clonar código-fonte do aplicativo do GitHub
> * Usar o Docker Compose para criar uma imagem e executar um aplicativo de vários contêineres localmente
> * Enviar por push a imagem do aplicativo para o Registro de contêiner
> * Criar um contexto do Azure para o Docker
> * Abrir o aplicativo em Instâncias de Contêiner do Azure

Use também a [extensão do Docker](https://aka.ms/VSCodeDocker) para Visual Studio Code para uma experiência integrada de desenvolvimento, execução e gerenciamento de contêineres, imagens e contextos.

Se você quiser aproveitar mais recursos em Instâncias de Contêiner do Azure, use as ferramentas do Azure para especificar um grupo de vários contêineres. Por exemplo, confira os tutoriais para implantar um grupo de contêineres usando a CLI do Azure com um arquivo [YAML](container-instances-multi-container-yaml.md) ou implantar usando um [modelo do Azure Resource Manager](container-instances-multi-container-group.md). 