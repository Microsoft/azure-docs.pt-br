---
title: Efetuar push da imagem do Docker para o Registro de Contêiner do Azure privado
description: Envie e obtenha imagens do Docker para um registro de contêiner privado no Azure usando a CLI do Docker
services: container-registry
author: dlepow
manager: jeconnoc
ms.service: container-registry
ms.topic: article
ms.date: 01/23/2019
ms.author: danlep
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 2cb401dfd68075ff0867ae3f89eee3474000b5de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60828743"
---
# <a name="push-your-first-image-to-a-private-docker-container-registry-using-the-docker-cli"></a>Envie sua primeira imagem para um registro de contêiner privado do Docker usando a CLI do Docker

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do [Docker](https://hub.docker.com), de forma semelhante a como o [Docker Hub](https://hub.docker.com/) armazena imagens públicas do Docker. Você pode usar a [Interface de linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (Docker CLI) para [logon](https://docs.docker.com/engine/reference/commandline/login/), [push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/) e outras operações no registro de contêiner.

Nas etapas a seguir, você baixará uma [imagem Nginx](https://store.docker.com/images/nginx) oficial do registro do Hub do Docker público, a rotulará para seu registro de contêiner do Azure privado, enviará por push para o registro e, em seguida, efetuará pull dela do registro.

## <a name="prerequisites"></a>Pré-requisitos

* **Registro de Contêiner do Azure** - crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md).
* **CLI do Docker** – você também deve ter o Docker instalado localmente. O Docker fornece pacotes que o configuram facilmente em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Fazer logon em um registro

Há [várias maneiras de autenticar](container-registry-authentication.md) no registro de contêiner privado. É o método recomendado ao trabalhar em uma linha de comando com o comando [logon de acr az](/cli/azure/acr?view=azure-cli-latest#az-acr-login) da CLI do Azure. Por exemplo, para fazer logon em um registro chamado *meuregistro*:

```azurecli
az acr login --name myregistry
```

Você também pode fazer logon com o [logon do docker](https://docs.docker.com/engine/reference/commandline/login/). Por exemplo, você pode [atribuir uma entidade de serviço](container-registry-authentication.md#service-principal) ao registro para um cenário de automação. Quando você executa o comando a seguir, forneça interativamente o appID (nome de usuário) e a senha da entidade de serviço quando solicitado. Para obter práticas recomendadas gerenciar credenciais de logon, confira a referência do comando [docker login](https://docs.docker.com/engine/reference/commandline/login/):

```
docker login myregistry.azurecr.io
```

Os dois comandos retornam `Login Succeeded` após a conclusão.

> [!TIP]
> Sempre especifique o nome totalmente qualificado do registro (todas as letras minúsculas) quando você usar `docker login` e quando marcar imagens para envio por push para o registro. Nos exemplos deste artigo, o nome totalmente qualificado é *myregistry.azurecr.io*.

## <a name="pull-the-official-nginx-image"></a>Fazer pull da imagem oficial do Nginx

Primeiro, extraia a imagem Nginx pública para seu computador local.

```
docker pull nginx
```

## <a name="run-the-container-locally"></a>Executar o contêiner localmente

Execute o seguinte comando [docker run](https://docs.docker.com/engine/reference/run/) para iniciar uma instância local do contêiner Nginx interativamente (`-it`) na porta 8080. O argumento `--rm` especifica que o contêiner deverá ser removido quando você o parar.

```
docker run -it --rm -p 8080:80 nginx
```

Navegue até `http://localhost:8080` para exibir a página da web padrão servida pelo Nginx no contêiner em execução. Você verá uma página semelhante à seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Como você iniciou o contêiner interativamente com `-it`, poderá ver a saída do servidor Nginx na linha de comando depois de ir até ele em seu navegador.

Para parar e remover o contêiner, pressione `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Criar um alias da imagem

Use [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para criar um alias da imagem com o caminho totalmente qualificado para o registro. Este exemplo especifica o namespace `samples` para evitar confusão na raiz do registro.

```
docker tag nginx myregistry.azurecr.io/samples/nginx
```

Para saber mais sobre a marcação com namespaces, veja a seção [Namespaces de repositório](container-registry-best-practices.md#repository-namespaces) de [Práticas recomendadas para o Registro de Contêiner do Azure](container-registry-best-practices.md).

## <a name="push-the-image-to-your-registry"></a>Enviar por push a imagem ao registro

Agora que você já marcou a imagem com o caminho totalmente qualificado para o registro privado, poderá enviá-la por push para o registro com [docker push](https://docs.docker.com/engine/reference/commandline/push/):

```
docker push myregistry.azurecr.io/samples/nginx
```

## <a name="pull-the-image-from-your-registry"></a>Fazer pull da imagem do registro

Use o comando [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) para fazer pull da imagem do registro:

```
docker pull myregistry.azurecr.io/samples/nginx
```

## <a name="start-the-nginx-container"></a>Iniciar o contêiner Nginx

Use o comando [docker run](https://docs.docker.com/engine/reference/run/) para executar a imagem extraída por pull do registro:

```
docker run -it --rm -p 8080:80 myregistry.azurecr.io/samples/nginx
```

Navegue até `http://localhost:8080` para exibir o contêiner em execução.

Para parar e remover o contêiner, pressione `Control`+`C`.

## <a name="remove-the-image-optional"></a>Remover a imagem (opcional)

Se você não precisar mais da imagem Nginx, poderá excluí-la localmente com o comando [docker rmi](https://docs.docker.com/engine/reference/commandline/rmi/).

```
docker rmi myregistry.azurecr.io/samples/nginx
```

Para remover as imagens de seu registro de contêiner do Azure, você pode usar o comando [az acr repository delete](/cli/azure/acr/repository#az-acr-repository-delete) da CLI do Azure. Por exemplo, o comando a seguir exclui o manifesto referenciado pela marca `samples/nginx:latest`, quaisquer dados da camada exclusiva e todas as outras marcas que façam referência ao manifesto.

```azurecli
az acr repository delete --name myregistry --image samples/nginx:latest
```

## <a name="next-steps"></a>Próximas etapas

Agora que conhece os fundamentos, você está pronto para começar a usar o registro! Por exemplo, implante as imagens de contêiner do Registro para:

* [AKS (Serviço de Kubernetes do Azure)](../aks/tutorial-kubernetes-prepare-app.md)
* [Instâncias de Contêiner do Azure](../container-instances/container-instances-tutorial-prepare-app.md)
* [Service Fabric](../service-fabric/service-fabric-tutorial-create-container-images.md)

Opcionalmente, instale a [Extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetue pull e push de imagens para um registro de contêiner do Azure ou execute Tarefas do ACR, tudo isso no Visual Studio Code.


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
