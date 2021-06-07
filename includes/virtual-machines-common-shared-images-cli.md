---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 9556b20ba0ceac2d4c1ad92897e6f9d46293387f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96027658"
---
## <a name="create-an-image-gallery"></a>Criar uma galeria de imagens 

Uma galeria de imagens é o principal recurso usado para habilitar o compartilhamento de imagens. 

Caracteres permitidos para o nome da galeria são letras maiúsculas ou minúsculas, dígitos, pontos e pontos finais. O nome da galeria não pode conter traços.   Os nomes das galerias devem ser exclusivos dentro de sua assinatura. 

Criar uma galeria de imagens usando [az sig create](/cli/azure/sig#az-sig-create). O exemplo a seguir cria um grupo de recursos da galeria chamado *myGalleryRG* no *Leste dos EUA*, bem como uma galeria chamada *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Compartilhar a galeria

Você pode compartilhar imagens entre assinaturas usando o RBAC (Controle de Acesso Baseado em Função). Você pode compartilhar imagens na Galeria, na definição da imagem ou no nível da versão da imagem. Qualquer usuário que tenha permissões de leitura para uma versão de imagem, mesmo entre assinaturas, poderá implantar uma VM usando a versão da imagem.

Recomendamos que você compartilhe com outros usuários no nível da galeria. Para obter a ID do objeto da galeria, use [az sig show](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use a ID do objeto como um escopo, juntamente com um endereço de email e [az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) para conceder a um usuário acesso à galeria de imagens compartilhadas. Substitua `<email-address>` e `<gallery iD>` pelas suas informações.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para obter mais informações de como compartilhar recursos usando o RBAC, confira [Gerenciar o acesso usando a CLI do Azure e RBAC](../articles/role-based-access-control/role-assignments-cli.md).