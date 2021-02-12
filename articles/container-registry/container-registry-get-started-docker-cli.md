---
title: Enviar por push & imagem do contêiner de pull
description: Enviar por push e extrair imagens do Docker para o registro de contêiner privado no Azure usando a CLI do Docker
ms.topic: article
ms.date: 01/23/2019
ms.custom: seodec18, H1Hack27Feb2017
ms.openlocfilehash: 83ef385313b035f5e5d7d993e7948725906c75a7
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987772"
---
# <a name="push-your-first-image-to-your-azure-container-registry-using-the-docker-cli"></a>Envie por push sua primeira imagem para o registro de contêiner do Azure usando a CLI do Docker

Um registro de contêiner do Azure armazena e gerencia imagens de contêineres particulares e outros artefatos, de forma semelhante ao modo como o [Docker Hub](https://hub.docker.com/) armazena imagens de contêiner do Docker públicas. Você pode usar a [interface de linha de comando do Docker](https://docs.docker.com/engine/reference/commandline/cli/) (CLI do Docker) para operações de [logon](https://docs.docker.com/engine/reference/commandline/login/), [Push](https://docs.docker.com/engine/reference/commandline/push/), [pull](https://docs.docker.com/engine/reference/commandline/pull/)e outras imagens de contêiner no registro de contêiner.

Nas etapas a seguir, você baixa uma [imagem Nginx](https://store.docker.com/images/nginx)pública, marca-a para seu registro de contêiner do Azure privado, envia-a por push para o registro e, em seguida, a extrai do registro.

## <a name="prerequisites"></a>Pré-requisitos

* **Registro de contêiner do Azure** -crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md).
* **CLI do Docker** – você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [macOS][docker-mac], [Windows][docker-windows] ou [Linux][docker-linux].

## <a name="log-in-to-a-registry"></a>Fazer logon em um registro

Há [várias maneiras de autenticar](container-registry-authentication.md) no registro de contêiner privado. É o método recomendado ao trabalhar em uma linha de comando com o comando [logon de acr az](/cli/azure/acr#az-acr-login) da CLI do Azure. Por exemplo, para fazer logon em um registro chamado *myregistry*, faça logon no CLI do Azure e, em seguida, autentique-se no registro:

```azurecli
az login
az acr login --name myregistry
```

Você também pode fazer logon com o [logon do docker](https://docs.docker.com/engine/reference/commandline/login/). Por exemplo, você pode ter [atribuído uma entidade de serviço](container-registry-authentication.md#service-principal) ao registro para um cenário de automação. Quando você executa o comando a seguir, forneça interativamente o appID (nome de usuário) e a senha da entidade de serviço quando solicitado. Para obter práticas recomendadas gerenciar credenciais de logon, confira a referência do comando [docker login](https://docs.docker.com/engine/reference/commandline/login/):

```
docker login myregistry.azurecr.io
```

Os dois comandos retornam `Login Succeeded` após a conclusão.
> [!NOTE]
>* Talvez você queira usar Visual Studio Code com extensão do Docker para um logon mais rápido e conveniente.

> [!TIP]
> Sempre especifique o nome totalmente qualificado do registro (todas as letras minúsculas) quando você usar `docker login` e quando marcar imagens para envio por push para o registro. Nos exemplos deste artigo, o nome totalmente qualificado é *myregistry.azurecr.io*.

## <a name="pull-a-public-nginx-image"></a>Efetuar pull de uma imagem Nginx pública

Primeiro, receba uma imagem Nginx pública em seu computador local. Este exemplo efetua pull de uma imagem do registro de contêiner da Microsoft.

```
docker pull mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

## <a name="run-the-container-locally"></a>Executar o contêiner localmente

Execute o seguinte comando de [execução do Docker](https://docs.docker.com/engine/reference/run/) para iniciar uma instância local do contêiner Nginx interativamente ( `-it` ) na porta 8080. O argumento `--rm` especifica que o contêiner deverá ser removido quando você o parar.

```
docker run -it --rm -p 8080:80 mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Navegue até `http://localhost:8080` para exibir a página da Web padrão servida pelo Nginx no contêiner em execução. Você verá uma página semelhante à seguinte:

![Nginx no computador local](./media/container-registry-get-started-docker-cli/nginx.png)

Como você iniciou o contêiner interativamente com `-it`, poderá ver a saída do servidor Nginx na linha de comando depois de ir até ele em seu navegador.

Para parar e remover o contêiner, pressione `Control`+`C`.

## <a name="create-an-alias-of-the-image"></a>Criar um alias da imagem

Use [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para criar um alias da imagem com o caminho totalmente qualificado para o registro. Este exemplo especifica o namespace `samples` para evitar confusão na raiz do registro.

```
docker tag mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine myregistry.azurecr.io/samples/nginx
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
