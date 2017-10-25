---
title: "Criar imagens de contêiner para o Azure Service Fabric | Microsoft Docs"
description: "Saiba como criar imagens de contêiner para um aplicativo do Service Fabric com vários contêineres."
services: service-fabric
documentationcenter: 
author: suhuruli
manager: timlt
editor: suhuruli
tags: servicefabric
keywords: "Docker, Contêineres, Microservices, Service Fabric, Azure"
ms.assetid: 
ms.service: service-fabric
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: 08b3cc4a52c09ee03818b563794ef9b009d12ef4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-container-images-for-service-fabric"></a>Criar imagens de contêiner para o Service Fabric

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

- Ambiente de desenvolvimento do Linux configurado para o Service Fabric. Siga as instruções [aqui](service-fabric-get-started-linux.md) para configurar seu ambiente do Linux. 
- Este tutorial exige que você esteja executando a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli). 
- Além disso, ele requer que você tenha uma assinatura do Azure disponível. Para obter mais informações sobre uma versão de avaliação gratuita, acesse [aqui](https://azure.microsoft.com/free/).

## <a name="get-application-code"></a>Obter o código de aplicativo

O aplicativo de exemplo usado neste tutorial é um aplicativo de votação. O aplicativo consiste de um componente Web de front-end e uma instância Redis de back-end. Os componentes são empacotados em imagens de contêiner. 

Use o git para baixar uma cópia do aplicativo em seu ambiente de desenvolvimento.

```bash
git clone https://github.com/Azure-Samples/service-fabric-dotnet-containers.git

cd service-fabric-dotnet-containers/Linux/container-tutorial/
```

O diretório 'container-tutorial' contém uma pasta chamada 'azure-vote'. Esta pasta 'azure-vote' contém o código-fonte de front-end e um Dockerfile para criar front-end. O diretório 'container-tutorial' também contém o diretório 'redis' que tem o Dockerfile para montar a imagem do redis. Esses diretórios contêm os ativos necessários para este conjunto de tutoriais. 

## <a name="create-container-images"></a>Criar imagens de contêiner

Dentro do diretório 'azure-vote', execute o seguinte comando para montar a imagem para o componente Web de front-end. Esse comando usa o Dockerfile nesse diretório para montar a imagem. 

```bash
docker build -t azure-vote-front .
```

Dentro do diretório 'redis', execute o seguinte comando para montar a imagem para o back-end do redis. Esse comando usa o Dockerfile no diretório para montar a imagem. 

```bash
docker build -t azure-vote-back .
```

Quando completado, use o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver as imagens criadas.

```bash
docker images
```

Observe que quatro imagens foram baixadas ou criadas. A imagem *azure-vote-front* contém o aplicativo. Ela foi derivada de uma imagem *python* do Hub do Docker. A imagem Redis foi baixada do Hub do Docker.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Primeiro, execute o comando [logon az](/cli/azure/login) para fazer logon em sua conta do Azure. 

Em seguida, use o comando [conta az](/cli/azure/account#set) para escolher sua assinatura para criar o Registro de Contêiner do Azure. 

```bash
az account set --subscription <subscription_id>
```

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *westus*. Escolha o grupo de recursos em uma região perto de você. 

```bash
az group create --name myResourceGroup --location westus
```

Crie um Registro de Contêiner do Azure com o comando [az acr create](/cli/azure/acr#create). O nome de um registro de contêiner **deve ser exclusivo**.

```bash
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic --admin-enabled true
```

Durante o restante deste tutorial, utilizamos "acrname" como um espaço reservado para o nome do registro de contêiner escolhido.

## <a name="log-in-to-your-container-registry"></a>Fazer logon no Registro de contêiner

Faça logon na instância do ACR antes de enviar imagens por push a ele. Use o comando [az acr login](/cli/azure/acr?view=azure-cli-latest#az_acr_login) para concluir a operação. Forneça o nome exclusivo fornecido para o Registro de contêiner quando ele foi criado.

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
azure-vote-back              latest              bf9a858a9269        3 seconds ago        107MB
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
redis                        latest              9813a7e8fcc0        2 days ago           107MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

Para obter o nome de loginServer, execute o comando a seguir:

```bash
az acr show --name <acrName> --query loginServer --output table
```

Agora, marque a imagem *azure-vote-front* com o loginServer do registro de contêiner. Além disso, adicione `:v1` ao final do nome da imagem. Esta marcação indica a versão da imagem.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Em seguida, marque a imagem *azure-vote-back* com o loginServer do Registro de contêiner. Além disso, adicione `:v1` ao final do nome da imagem. Esta marcação indica a versão da imagem.

```bash
docker tag azure-vote-back <acrLoginServer>/azure-vote-back:v1
```

Depois de marcada, execute 'docker images' para verificar a operação.


Saída:

```bash
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-back                        latest              bf9a858a9269        22 minutes ago      107MB
<acrName>.azurecr.io/azure-vote-back    v1                  bf9a858a9269        22 minutes ago      107MB
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf        23 minutes ago      708MB
redis                                  latest              9813a7e8fcc0        2 days ago          107MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Enviar a imagem *azure-vote-front* por push ao registro. 

Usando o exemplo a seguir, substitua o nome do loginServer do ACR pelo loginServer do seu ambiente.

```bash
docker push <acrLoginServer>/azure-vote-front:v1
```

Envie a imagem *azure-vote-back* por push ao Registro. 

Usando o exemplo a seguir, substitua o nome do loginServer do ACR pelo loginServer do seu ambiente.

```bash
docker push <acrLoginServer>/azure-vote-back:v1
```

Os comandos de push do Docker leva alguns minutos para ser concluído.

## <a name="list-images-in-registry"></a>Lista de imagens no registro

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list](/cli/azure/acr/repository#list). Atualize o comando com o nome da instância do ACR.

```bash
az acr repository list --name <acrName> --output table
```

Saída:

```bash
Result
----------------
azure-vote-back
azure-vote-front
```

Na conclusão do tutorial, a imagem de contêiner foi armazenada em uma instância privada de Registro de Contêiner do Azure. Essa imagem será implantada do ACR em um cluster do Service Fabric nos tutoriais subsequentes.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, foi efetuado pull de um aplicativo do Github e as imagens de contêiner foram criadas e enviadas por push para um Registro. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Clonar a origem do aplicativo do GitHub  
> * Criar uma imagem de contêiner da origem do aplicativo
> * Implantar uma instância de ACR (Registro de Contêiner do Azure)
> * Marcar uma imagem de contêiner para ACR
> * Carregar a imagem para ACR

Avança para o próximo tutorial para saber mais sobre empacotamento de contêineres em um aplicativo do Service Fabric usando o Yeoman. 

> [!div class="nextstepaction"]
> [Empacotar e implantar contêineres como um aplicativo do Service Fabric](service-fabric-tutorial-package-containers.md)
