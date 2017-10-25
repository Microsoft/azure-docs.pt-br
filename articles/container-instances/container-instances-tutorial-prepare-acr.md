---
title: "Tutorial sobre Instâncias de Contêiner do Azure – preparar o Registro de Contêiner do Azure | Microsoft Docs"
description: "Tutorial sobre Instâncias de Contêiner do Azure – preparar o Registro de Contêiner do Azure"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, Contêineres, Microsserviços, Kubernetes, DC/SO, Azure"
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 7ac85bffb9593923808c77f2240e6f0e841e74cd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-use-azure-container-registry"></a>Implantar e usar o Registro de Contêiner do Azure

Esta é a parte dois de um tutorial de três partes. Na [etapa anterior](./container-instances-tutorial-prepare-app.md), uma imagem de contêiner foi criada para um aplicativo Web simples escrito em [Node.js](http://nodejs.org). Neste tutorial, essa imagem é enviada por push para um Registro de Contêiner do Azure. Se você não criou a imagem de contêiner, retorne ao [Tutorial 1 – Criar imagem de contêiner](./container-instances-tutorial-prepare-app.md). 

O Registro de Contêiner do Azure é um registro privado baseado no Azure para imagens de contêiner do Docker. Este tutorial demonstra a implantação de uma instância do Registro de Contêiner do Azure e o envio por push de uma imagem de contêiner a ele. As etapas concluídas incluem:

> [!div class="checklist"]
> * Implantando uma instância do Registro de Contêiner do Azure
> * Marcação de imagem de contêiner para o Registro de Contêiner do Azure
> * Upload da imagem para o Registro de Contêiner do Azure

Nos tutoriais subsequentes, você implanta o contêiner do seu registro particular nas Instâncias de Contêiner do Azure.

## <a name="before-you-begin"></a>Antes de começar

Este tutorial exige que você esteja executando a CLI do Azure versão 2.0.12 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure]( /cli/azure/install-azure-cli).

## <a name="deploy-azure-container-registry"></a>Implantar o Registro de Contêiner do Azure

Ao implantar um Registro de Contêiner do Azure, primeiro você precisa de um grupo de recursos. Um grupo de recursos do Azure é uma coleção lógica na qual os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Neste exemplo, um grupo de recursos denominado *myResourceGroup* é criado na região *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

Crie um Registro de Contêiner do Azure com o comando [az acr create](/cli/azure/acr#create). O nome de um registro de contêiner **deve ser exclusivo**. No exemplo a seguir, usamos o nome *mycontainerregistry082*.

```azurecli
az acr create --resource-group myResourceGroup --name mycontainerregistry082 --sku Basic --admin-enabled true
```

Durante o restante deste tutorial, utilizamos `<acrname>` como um espaço reservado para o nome do registro de contêiner escolhido.

## <a name="container-registry-login"></a>Logon no registro de contêiner

Você deverá entrar na instância do ACR antes de enviar imagens por push a ele. Use o comando [az acr login](https://docs.microsoft.com/en-us/cli/azure/acr#az_acr_login) para concluir a operação. Você precisa fornecer o nome exclusivo fornecido para o registro de contêiner quando ele foi criado.

```azurecli
az acr login --name <acrName>
```

O comando retorna uma mensagem de 'Logon bem-sucedido' quando é concluído.

## <a name="tag-container-image"></a>Marcar imagem de contêiner

Para implantar uma imagem de contêiner de um registro privado, é necessário que a imagem seja marcada com o nome de `loginServer` do registro.

Para ver uma lista de imagens atuais, use o comando `docker images`.

```bash
docker images
```

Saída:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
aci-tutorial-app             latest              5c745774dfa9        39 seconds ago       68.1 MB
```

Para obter o nome de loginServer, execute o comando a seguir.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

Marque a imagem *aci-tutorial-app* com o loginServer do registro de contêiner. Além disso, adicione `:v1` ao final do nome da imagem. Essa marca indica o número de versão da imagem.

```bash
docker tag aci-tutorial-app <acrLoginServer>/aci-tutorial-app:v1
```

Depois de marcar, execute `docker images` para verificar a operação.

```bash
docker images
```

Saída:

```bash
REPOSITORY                                                TAG                 IMAGE ID            CREATED             SIZE
aci-tutorial-app                                          latest              5c745774dfa9        39 seconds ago      68.1 MB
mycontainerregistry082.azurecr.io/aci-tutorial-app        v1                  a9dace4e1a17        7 minutes ago       68.1 MB
```

## <a name="push-image-to-azure-container-registry"></a>Enviar imagem por push ao Registro de Contêiner do Azure

Envie por push a imagem *aci-tutorial-app* ao registro.

Usando o exemplo a seguir, substitua o nome do loginServer do registro de contêiner pelo loginServer do seu ambiente.

```bash
docker push <acrLoginServer>/aci-tutorial-app:v1
```

## <a name="list-images-in-azure-container-registry"></a>Listar imagens no Registro de Contêiner do Azure

Para retornar uma lista de imagens que foram enviadas por push ao Registro de Contêiner do Azure, use o comando [az acr repository list](/cli/azure/acr/repository#list). Atualize o comando com o nome do registro de contêiner.

```azurecli
az acr repository list --name <acrName> --output table
```

Saída:

```azurecli
Result
----------------
aci-tutorial-app
```

E, em seguida, para ver as marcas de uma imagem específica, use o comando [az acr repository show-tags](/cli/azure/acr/repository#show-tags).

```azurecli
az acr repository show-tags --name <acrName> --repository aci-tutorial-app --output table
```

Saída:

```azurecli
Result
--------
v1
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, foi preparado um Registro de Contêiner do Azure para ser usado com Instâncias de Contêiner do Azure e a imagem de contêiner foi enviada por push. As etapas a seguir foram concluídas:

> [!div class="checklist"]
> * Implantando uma instância do Registro de Contêiner do Azure
> * Marcação de imagem de contêiner para o Registro de Contêiner do Azure
> * Upload da imagem para o Registro de Contêiner do Azure

Avance para o próximo tutorial para saber mais sobre a implantação do contêiner no Azure usando as Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Implantar contêineres nas Instâncias de Contêiner do Azure](./container-instances-tutorial-deploy-app.md)
