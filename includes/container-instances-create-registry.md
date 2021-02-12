---
title: incluir arquivo
description: incluir arquivo
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 173c9156f253e43111299b53287e97ab7b2c0aa5
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92746925"
---
## <a name="create-azure-container-registry"></a>Crie um registro de contêiner do Azure

Antes de criar o registro de contêiner, é necessário um *grupo de recursos* para implantá-lo. O grupo de recursos é uma coleção lógica na qual todos os recursos do Azure são implantados e gerenciados.

Crie um grupo de recursos com o comando [az group create][az-group-create]. No exemplo a seguir, um grupo de recursos chamado *myResourceGroup* é criado na região *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Após criar o grupo de recursos, crie um registro de contêiner do Azure com o comando [az acr create][az-acr-create]. O nome do Registro de contêiner deve ser exclusivo no Azure e conter de 5 a 50 caracteres alfanuméricos. Substitua `<acrName>` por um nome exclusivo para o registro:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Aqui está uma saída parcial para um novo registro de contêiner do Azure denominado *mycontainerregistry082*:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

O restante deste tutorial se refere a `<acrName>` como um espaço reservado para o nome do registro de contêiner que você escolheu nesta etapa.

## <a name="log-in-to-container-registry"></a>Faça logon no registro de contêiner

Você deve fazer logon na instância do Registro de Contêiner do Azure antes de enviar imagens por push a ela. Use o comando [az acr login][az-acr-login] para concluir a operação. Você deve fornecer o nome exclusivo que você escolheu para o registro de contêiner quando ele foi criado.

```azurecli
az acr login --name <acrName>
```

Por exemplo:

```azurecli
az acr login --name mycontainerregistry082
```

O comando retorna `Login Succeeded` na conclusão:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-group-create]: /cli/azure/group#az-group-create
