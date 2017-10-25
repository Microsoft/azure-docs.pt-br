---
title: "Guia de início rápido – criar um registro particular do Docker no Azure com a CLI do Azure"
description: "Aprenda rapidamente a criar um registro de contêiner particular do Docker com a CLI do Azure."
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: tysonn
tags: 
keywords: 
ms.assetid: 29e20d75-bf39-4f7d-815f-a2e47209be7d
ms.service: container-registry
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: nepeters
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 06967315dfa43e791e662a689ceb993c4af1c1e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-container-registry-using-the-azure-cli"></a>Criar um registro de contêiner usando a CLI do Azure

O Registro de Contêiner do Azure é um serviço de registro de contêiner Docker gerenciado usado para armazenar imagens de contêiner de Docker particulares. Este guia detalha a criação de uma instância do Registro de Contêiner do Azure usando a CLI do Azure.

Este guia de início rápido exige que você esteja executando a CLI do Azure versão 2.0.12 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

Você também deve ter o Docker instalado localmente. O Docker fornece pacotes que configuram facilmente o Docker em qualquer sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) ou [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container-registry"></a>Criar um registro de contêiner

O Registro de Contêiner do Azure está disponível em vários SKUs: `Basic`, `Managed_Basic`, `Managed_Standard` e `Managed_Premium`. Embora os SKUs `Managed_*` forneçam recursos avançados como armazenamento gerenciado e Webhooks, eles estão atualmente em versão prévia e não estão disponíveis em algumas regiões do Azure. Selecionamos o SKU `Basic` neste guia de início rápido devido a sua disponibilidade em todas as regiões.

Crie uma instância ACR usando o comando [az acr create](/cli/azure/acr#create).

O nome do registro **deve ser exclusivo**. No exemplo a seguir o nome *myContainerRegistry007* é usado. Atualize-o para um valor exclusivo.

```azurecli
az acr create --name myContainerRegistry007 --resource-group myResourceGroup --admin-enabled --sku Basic
```

Quando o registro é criado, o resultado é semelhante ao seguinte:

```azurecli
{
  "adminUserEnabled": true,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr223140"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

No restante deste guia de início rápido, utilizamos `<acrname>` como um espaço reservado para o nome do registro de contêiner.

## <a name="log-in-to-acr"></a>Fazer logon no ACR

Antes de enviar por push e pull imagens de contêiner, você deverá fazer logon na instância ACR. Para fazer isso, use o comando [az acr login](/cli/azure/acr#login).

```azurecli-interactive
az acr login --name <acrname>
```

O comando retorna uma mensagem de 'Logon bem-sucedido' assim que ele for concluído.

## <a name="push-image-to-acr"></a>Enviar imagem por push para o ACR

Para enviar por push uma imagem para um Registro de Contêiner do Azure, primeiro você deve ter uma imagem. Se necessário, execute o seguinte comando para efetuar pull de uma imagem pré-criada do Hub do Docker.

```bash
docker pull microsoft/aci-helloworld
```

A imagem deve ser marcada com o nome do servidor de logon do ACR. Execute o seguinte comando para retornar o nome do servidor de logon da instância do ACR.

```bash
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Marque a imagem usando o comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Substitua o *<acrLoginServer>* pelo nome do servidor de logon da sua instância do ACR.

```
docker tag microsoft/aci-helloworld <acrLoginServer>/aci-helloworld:v1
```

Por fim, use [docker push](https://docs.docker.com/engine/reference/commandline/push/) para enviar a imagem por push para a instância do ACR. Substitua o *<acrLoginServer>* pelo nome do servidor de logon da sua instância do ACR.

```
docker push <acrLoginServer>/aci-helloworld:v1
```

## <a name="list-container-images"></a>Listar imagens de contêiner

O exemplo a seguir lista os repositórios em um registro:

```azurecli
az acr repository list -n <acrname> -o table
```

Saída:

```json
Result
----------------
aci-helloworld
```

O exemplo a seguir lista as marcas no repositório **aci-helloworld**.

```azurecli
az acr repository show-tags -n <acrname> --repository aci-helloworld -o table
```

Saída:

```Result
--------
v1
```

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você pode usar o comando [az group delete](/cli/azure/group#delete) para remover o grupo de recursos, a instância do ACR e todas as imagens de contêiner.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você ciou um Registro de Contêiner do Azure com a CLI do Azure. Se você quiser usar o Registro de Contêiner do Azure com Instâncias de Contêiner do Azure, prossiga para o tutorial de Instâncias de Contêiner do Azure.

> [!div class="nextstepaction"]
> [Tutorial sobre Instâncias de Contêiner do Azure](../container-instances/container-instances-tutorial-prepare-app.md)