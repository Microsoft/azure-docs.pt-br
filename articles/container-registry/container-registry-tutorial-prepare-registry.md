---
title: Tutorial – criar um registro do Docker com replicação geográfica no Azure
description: Crie um Registro de contêiner do Azure, configure a replicação geográfica, prepare uma imagem do Docker e implante-a no Registro. Parte um de uma série de três partes.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: tutorial
ms.date: 04/30/2017
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 87746bd39e624699612bf5221258ad757cd462b3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68309580"
---
# <a name="tutorial-prepare-a-geo-replicated-azure-container-registry"></a>Tutorial: Preparar um Registro de contêiner do Azure com replicação geográfica

Um Registro de contêiner do Azure é um Registro do Docker privado implantado no Azure que você pode manter perto da rede para suas implantações. Nesse conjunto de três artigos tutoriais, você aprenderá a usar a replicação geográfica para implantar um aplicativo Web ASP.NET Core em execução em um contêiner do Linux em duas instâncias de [Aplicativos Web para Contêineres](../app-service/containers/index.yml). Você verá como o Azure implanta automaticamente a imagem em cada instância de aplicativo Web do repositório com replicação geográfica mais próximo.

Neste tutorial, parte um de uma série de três partes:

> [!div class="checklist"]
> * Criar um Registro de contêiner do Azure com replicação geográfica
> * Clonar código-fonte do aplicativo do GitHub
> * Criar uma imagem de contêiner do Docker da origem de aplicativo
> * Efetuar push da imagem de contêiner para seu Registro

Nos tutoriais subsequentes, você implantará o contêiner do seu Registro particular em um aplicativo Web em execução em duas regiões do Azure. Em seguida, você atualizará o código no aplicativo e atualizará ambas as instâncias do aplicativo Web com um único `docker push` em seu Registro.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial requer uma instalação local da CLI do Azure (versão 2.0.31 ou posterior). Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

Você deverá estar familiarizado com os conceitos do Docker, como contêineres, imagens de contêiner e comandos básicos da CLI do Docker. Consulte [Introdução ao Docker]( https://docs.docker.com/get-started/) para conhecer os conceitos básicos de contêiner.

Para concluir este tutorial, você precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

Azure Cloud Shell não inclui os componentes de Docker necessários para concluir cada etapa neste tutorial. Portanto, é recomendável uma instalação local do ambiente de desenvolvimento da CLI do Azure e do Docker.

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

Entre no [Portal do Azure](https://portal.azure.com).

Selecione **Criar um recurso** > **Contêineres** > **Registro de Contêiner do Azure**.

![Criar um registro de contêiner no Portal do Azure][tut-portal-01]

Configure seu novo Registro com as seguintes configurações:

* **Nome do registro**: crie um nome para o registro exclusivo globalmente dentro do Azure e que contenha de 5 a 50 caracteres alfanuméricos
* **Grupo de recursos**: **Criar novo** > `myResourceGroup`
* **Local**: `West US`
* **Usuário administrador**: `Enable` (necessário para o aplicativo Web para os Contêineres efetuarem pull das imagens)
* **SKU**: `Premium` (necessário para replicação geográfica)

Selecione **Criar** para implantar a instância do ACR.

![Criar um registro de contêiner no Portal do Azure][tut-portal-02]

Durante o restante deste tutorial, usaremos `<acrName>` como um espaço reservado para o **nome do Registro** do contêiner escolhido.

> [!TIP]
> Como os Registros de contêiner do Azure normalmente são recursos de vida longa usados entre vários hosts de contêiner, é recomendável que você crie seu Registro no próprio grupo de recursos dele. À medida que você configura Registros e webhooks com replicação geográfica, esses recursos adicionais são colocados no mesmo grupo de recursos.
>

## <a name="configure-geo-replication"></a>Configurar a replicação geográfica

Agora que você tem um Registro Premium, é possível configurar a replicação geográfica. Seu aplicativo Web, que você configurará no próximo tutorial a ser executado em duas regiões, pode efetuar pull de suas imagens de contêiner do Registro mais próximo.

Navegue até seu novo Registro de contêiner no Portal do Azure e selecione **Replicações** em **SERVIÇOS**:

![Replicações na interface do usuário de Registro de contêiner do Portal do Azure][tut-portal-03]

Um mapa é exibido mostrando hexágonos verdes que representam as regiões do Azure disponíveis para replicação geográfica:

 ![Mapa de região no Portal do Azure][tut-map-01]

Replique seu Registro na região Leste dos EUA selecionando seu hexágono verde; em seguida, selecione **Criar** em **Criar replicação**:

 ![Criar a interface do usuário de replicação no Portal do Azure][tut-portal-04]

Quando a replicação for concluída, o portal refletirá *Pronto* para ambas as regiões. Use o botão **Atualização** para atualizar o status da replicação; isso pode levar cerca de um minuto para que as réplicas sejam criadas e sincronizadas.

![Interface do usuário do status de replicação no Portal do Azure][tut-portal-05]

## <a name="container-registry-login"></a>Logon no registro de contêiner

Agora que você configurou a replicação geográfica, crie uma imagem de contêiner e efetue push dela para seu Registro. Primeiro, é necessário fazer logon em sua instância do ACR antes de efetuar push das imagens para ela.

Use o comando [az acr login](https://docs.microsoft.com/cli/azure/acr#az-acr-login) para autenticar e armazenar em cache as credenciais em seu Registro. Substitua `<acrName>` pelo nome do registro criado anteriormente.

```azurecli
az acr login --name <acrName>
```

O comando retorna `Login Succeeded` na conclusão.

## <a name="get-application-code"></a>Obter o código de aplicativo

O exemplo neste tutorial inclui um pequeno aplicativo Web criado com o [ASP.NET Core][aspnet-core]. O aplicativo atende uma página HTML que exibe a região na qual a imagem foi implantada pelo Registro de Contêiner do Azure.

![Aplicativo de tutorial mostrado no navegador][tut-app-01]

Use o git para baixar o exemplo para um diretório local e `cd` para o diretório:

```bash
git clone https://github.com/Azure-Samples/acr-helloworld.git
cd acr-helloworld
```

Caso `git` não esteja instalado, você poderá [baixar o arquivo morto ZIP][acr-helloworld-zip] diretamente do GitHub.

## <a name="update-dockerfile"></a>Atualizar o Dockerfile

O Dockerfile incluído no exemplo mostra como o contêiner é criado. Ele inicia em uma imagem [aspnetcore][dockerhub-aspnetcore] oficial, copia os arquivos de aplicativo para o contêiner, instala dependências, compila a saída usando a imagem [aspnetcore-build][dockerhub-aspnetcore-build] oficial e, finalmente, cria uma imagem aspnetcore otimizada.

O [Dockerfile][dockerfile] está localizado em `./AcrHelloworld/Dockerfile` na fonte clonada.

```Dockerfile
FROM microsoft/aspnetcore:2.0 AS base
# Update <acrName> with the name of your registry
# Example: uniqueregistryname.azurecr.io
ENV DOCKER_REGISTRY <acrName>.azurecr.io
WORKDIR /app
EXPOSE 80

FROM microsoft/aspnetcore-build:2.0 AS build
WORKDIR /src
COPY *.sln ./
COPY AcrHelloworld/AcrHelloworld.csproj AcrHelloworld/
RUN dotnet restore
COPY . .
WORKDIR /src/AcrHelloworld
RUN dotnet build -c Release -o /app

FROM build AS publish
RUN dotnet publish -c Release -o /app

FROM base AS production
WORKDIR /app
COPY --from=publish /app .
ENTRYPOINT ["dotnet", "AcrHelloworld.dll"]
```

O aplicativo na imagem *acr-helloworld* tenta determinar a região da qual seu contêiner foi implantado consultando o DNS para obter informações sobre o servidor de logon do Registro. É necessário especificar o nome de domínio totalmente qualificado (FQDN) do servidor de logon de registro na variável de ambiente `DOCKER_REGISTRY` no Dockerfile.

Primeiro, obtenha o servidor de logon do registro com o comando `az acr show`. Substitua `<acrName>` pelo nome do Registro criado nas etapas anteriores.

```azurecli
az acr show --name <acrName> --query "{acrLoginServer:loginServer}" --output table
```

Saída:

```bash
AcrLoginServer
-----------------------------
uniqueregistryname.azurecr.io
```

Em seguida, atualize a linha `ENV DOCKER_REGISTRY` com o FQDN do servidor de logon do registro. Este exemplo reflete o nome do registro de exemplo, *uniqueregistryname*:

```Dockerfile
ENV DOCKER_REGISTRY uniqueregistryname.azurecr.io
```

## <a name="build-container-image"></a>Criar imagem de contêiner

Agora que você atualizou o Dockerfile com o FQDN do servidor de logon de registro, é possível usar `docker build` para criar a imagem de contêiner. Execute o seguinte comando para criar a imagem e marque-a com a URL do seu Registro privado, substituindo novamente `<acrName>` pelo nome do seu Registro:

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

Várias linhas de saída são exibidas à medida que a imagem do Docker é criada (mostradas aqui truncadas):

```bash
Sending build context to Docker daemon  523.8kB
Step 1/18 : FROM microsoft/aspnetcore:2.0 AS base
2.0: Pulling from microsoft/aspnetcore
3e17c6eae66c: Pulling fs layer

[...]

Step 18/18 : ENTRYPOINT dotnet AcrHelloworld.dll
 ---> Running in 6906d98c47a1
 ---> c9ca1763cfb1
Removing intermediate container 6906d98c47a1
Successfully built c9ca1763cfb1
Successfully tagged uniqueregistryname.azurecr.io/acr-helloworld:v1
```

Use `docker images` para ver a imagem construída e marcada:

```console
$ docker images
REPOSITORY                                      TAG    IMAGE ID        CREATED               SIZE
uniqueregistryname.azurecr.io/acr-helloworld    v1     01ac48d5c8cf    About a minute ago    284MB
[...]
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagem por push ao Registro de Contêiner do Azure

Em seguida, use o comando `docker push` para efetuar push da imagem *acr helloworld* para seu registro. Substitua `<acrName>` pelo nome do seu Registro.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

Como você configurou seu Registro para replicação geográfica, sua imagem é replicada automaticamente nas regiões *Oeste dos EUA* e *Leste dos EUA* com este único comando `docker push`.

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
cd54739c444b: Pushed
d6803756744a: Pushed
b7b1f3a15779: Pushed
a89567dff12d: Pushed
59c7b561ff56: Pushed
9a2f9413d9e4: Pushed
a75caa09eb1f: Pushed
v1: digest: sha256:0799014f91384bda5b87591170b1242bcd719f07a03d1f9a1ddbae72b3543970 size: 1792
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um Registro de contêiner com replicação geográfica e privado, criou uma imagem de contêiner e efetuou push dessa imagem para seu Registro.

Passe para o próximo tutorial para implantar seu contêiner em várias instâncias de Aplicativos Web para Contêineres, usando a replicação geográfica para atender às imagens localmente.

> [!div class="nextstepaction"]
> [Implantar aplicativo Web do Registro de Contêiner do Azure](container-registry-tutorial-deploy-app.md)

<!-- IMAGES -->
[tut-portal-01]: ./media/container-registry-tutorial-prepare-registry/tut-portal-01.png
[tut-portal-02]: ./media/container-registry-tutorial-prepare-registry/tut-portal-02.png
[tut-portal-03]: ./media/container-registry-tutorial-prepare-registry/tut-portal-03.png
[tut-portal-04]: ./media/container-registry-tutorial-prepare-registry/tut-portal-04.png
[tut-portal-05]: ./media/container-registry-tutorial-prepare-registry/tut-portal-05.png
[tut-app-01]: ./media/container-registry-tutorial-prepare-registry/tut-app-01.png
[tut-map-01]: ./media/container-registry-tutorial-prepare-registry/tut-map-01.png

<!-- LINKS - External -->
[acr-helloworld-zip]: https://github.com/Azure-Samples/acr-helloworld/archive/master.zip
[aspnet-core]: https://dot.net
[dockerhub-aspnetcore]: https://hub.docker.com/r/microsoft/aspnetcore/
[dockerhub-aspnetcore-build]: https://store.docker.com/community/images/microsoft/aspnetcore-build
[dockerfile]: https://github.com/Azure-Samples/acr-helloworld/blob/master/AcrHelloworld/Dockerfile