---
title: incluir arquivo
description: incluir arquivo
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781103"
---
### <a name="create-a-user-assigned-identity"></a>Criar uma identidade atribuída pelo usuário

Crie uma identidade chamada *myACRTasksId* na sua assinatura usando o comando [az identity create][az-identity-create]. Você pode usar o mesmo grupo de recursos que você usou anteriormente para criar um registro de contêiner, ou pode usar um diferente.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Para configurar a identidade atribuída pelo usuário nas etapas a seguir, use o comando [az identity show][az-identity-show] para armazenar a ID do recurso de identidade, a ID da entidade de segurança e a ID do cliente em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
