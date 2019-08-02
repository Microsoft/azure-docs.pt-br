---
title: Create imagens de contêiner no Service Fabric no Azure | Microsoft Docs
description: Neste tutorial, você aprende a criar imagens de contêiner para um aplicativo do Service Fabric com vários contêineres.
services: service-fabric
documentationcenter: ''
author: suhuruli
manager: chackdan
editor: suhuruli
tags: servicefabric
keywords: Docker, Contêineres, Microservices, Service Fabric, Azure
ms.assetid: ''
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: b5dba1f7e71149c37edc909d2bb43d708192bdbb
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385217"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>Tutorial: Criar imagens de contêiner em um cluster do Service Fabric do Linux

Este tutorial é a parte um de uma série de tutoriais que demonstra como usar contêineres em um cluster do Service Fabric do Linux. Neste tutorial, um aplicativo com vários contêineres é preparado para uso com o Service Fabric. Nos tutoriais subsequentes, essas imagens são usadas como parte de um aplicativo do Service Fabric. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Clonar a origem do aplicativo do GitHub
> * Criar uma imagem de contêiner da origem do aplicativo
> * Implantar uma instância de ACR (Registro de Contêiner do Azure)
> * Marcar uma imagem de contêiner para ACR
> * Carregar a imagem para ACR

Nesta série de tutoriais, você aprenderá a:

> [!div class="checklist"]
> * Criar imagens de contêiner para o Service Fabric
> * [Criar e executar um aplicativo do Service Fabric com contêineres](service-fabric-tutorial-package-containers.md)
> * [Como o failover e o dimensionamento são manipulados no Service Fabric](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>Pré-requisitos

* Ambiente de desenvolvimento do Linux configurado para o Service Fabric. Siga as instruções [aqui](service-fabric-get-started-linux.md) para configurar seu ambiente do Linux.
* Este tutorial exige que você esteja executando a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Além disso, ele requer que você tenha uma assinatura do Azure disponível. Para obter mais informações sobre uma versão de avaliação gratuita, acesse [aqui](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Obter o código de aplicativo

O aplicativo de exemplo usado neste tutorial é um aplicativo de votação. O aplicativo consiste de um componente Web de front-end e uma instância Redis de back-end. Os componentes são empacotados em imagens de contêiner.

Use o git para baixar uma cópia do aplicativo em seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

A solução contém duas pastas e um arquivo ' docker-compose.yml'. A pasta 'azure voto' contém o serviço de front-end do Python junto com o Dockerfile usado para criar a imagem. O diretório 'Votação' contém o pacote de aplicativos do Service Fabric que é implantado no cluster. Esses diretórios contêm os ativos necessários para este tutorial.

## <a name="create-container-images"></a>Criar imagens de contêiner

Dentro do diretório **azure-vote'** , execute o seguinte comando para criar a imagem para o componente Web de front-end. Esse comando usa o Dockerfile nesse diretório para montar a imagem.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> Se a permissão estiver sendo negada, siga [esta](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) documentação sobre como trabalhar com o docker sem sudo.

Este comando pode levar algum tempo, pois todas as dependências necessárias precisam ser extraídas do Hub do Docker. Quando completado, use o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver as imagens criadas.

```bash
docker images
```

Observe que duas imagens foram baixadas ou criadas. A imagem *azure-vote-front* contém o aplicativo. Ela foi derivada de uma imagem *python* do Hub do Docker.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Primeiro, execute o comando **az login** para entrar em sua conta do Azure.

```bash
az login
```

Em seguida, use o comando **conta az** para escolher sua assinatura para criar o Registro de Contêiner do Azure. Você tem que inserir a ID da sua assinatura do Azure no lugar de <subscription_id>.

```bash
az account set --subscription <subscription_id>
```

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando **az group create**. Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *westus*.

```bash
az group create --name <myResourceGroup> --location westus
```

Crie um Registro de Contêiner do Azure com o comando **az acr create**. Substitua \<acrName > pelo nome do registro de contêiner que você deseja criar em sua assinatura. Esse nome de exibição deve ser alfanumérico e exclusivo.

```bash
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

Durante o restante deste tutorial, utilizamos "acrName" como um espaço reservado para o nome do registro de contêiner escolhido. Anote este valor.

## <a name="sign-in-to-your-container-registry"></a>Entre em seu registro de contêiner

Entre na instância do ACR antes de enviar imagens por push a ela. Use o comando **az acr login** para concluir a operação. Forneça o nome exclusivo fornecido para o Registro de contêiner quando ele foi criado.

```bash
az acr login --name <acrName>
```

O comando retorna uma mensagem de 'Logon bem-sucedido' quando é concluído.

## <a name="tag-container-images"></a>Marcar imagens de contêiner

Cada imagem de contêiner precisa ser marcada com o nome do registro loginServer. Essa marca é usada para roteamento ao enviar imagens de contêiner por push a um registro da imagem.

Para consultar uma lista de imagens atuais, utilize o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/).

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Para obter o nome de loginServer, execute o comando a seguir:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Isso gera uma tabela com os resultados a seguir. Esse resultado será usado para marcar sua imagem **azure-vote-front** antes de enviá-la por push para o registro de contêiner na próxima etapa.

```bash
Result
------------------
<acrName>.azurecr.io
```

Agora, marque a imagem *azure-vote-front* com o loginServer do registro de contêiner. Além disso, adicione `:v1` ao final do nome da imagem. Esta marcação indica a versão da imagem.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

Depois de marcada, execute 'docker images' para verificar a operação.

Saída:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Enviar a imagem *azure-vote-front* por push ao registro. 

Usando o exemplo a seguir, substitua o nome do loginServer do ACR pelo loginServer do seu ambiente.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

Os comandos de push do Docker leva alguns minutos para ser concluído.

## <a name="list-images-in-registry"></a>Lista de imagens no registro

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list](/cli/azure/acr/repository). Atualize o comando com o nome da instância do ACR.

```bash
az acr repository list --name <acrName> --output table
```

Saída:

```bash
Result
----------------
azure-vote-front
```

Na conclusão do tutorial, a imagem de contêiner foi armazenada em uma instância privada de Registro de Contêiner do Azure. Essa imagem será implantada do ACR em um cluster do Service Fabric nos tutoriais subsequentes.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, foi efetuado pull de um aplicativo do GitHub e as imagens de contêiner foram criadas e enviadas por push para um Registro. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Clonar a origem do aplicativo do GitHub
> * Criar uma imagem de contêiner da origem do aplicativo
> * Implantar uma instância de ACR (Registro de Contêiner do Azure)
> * Marcar uma imagem de contêiner para ACR
> * Carregar a imagem para ACR

Avança para o próximo tutorial para saber mais sobre empacotamento de contêineres em um aplicativo do Service Fabric usando o Yeoman.

> [!div class="nextstepaction"]
> [Empacotar e implantar contêineres como um aplicativo do Service Fabric](service-fabric-tutorial-package-containers.md)
