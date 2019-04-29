---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/20/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7fd5b2051f81a5dc34270a608c1518e8a11678b5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60542451"
---
## <a name="using-rbac-to-share-images"></a>Usar o RBAC para compartilhar imagens

Você pode compartilhar imagens entre assinaturas usando o RBAC (Controle de Acesso Baseado em Função). Qualquer usuário que tenha permissões de leitura para uma versão de imagem, mesmo entre assinaturas, poderá implantar uma máquina virtual usando a versão da imagem.

Para obter mais informações de como compartilhar recursos usando o RBAC, confira [Gerenciar o acesso usando a CLI do Azure e RBAC](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).


## <a name="list-information"></a>Listar informações

Obtenha informações de local, status e outras sobre as galerias de imagens disponíveis usando [az sig list](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Liste as definições de imagem em uma galeria, incluindo informações sobre o tipo e o status do sistema operacional, usando [az sig image-definition list](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list -g myGalleryRG -r myGallery -o table
```

Liste as versões da imagem compartilhada em uma galeria usando [az sig image-version list](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list -g myGalleryRG -r myGallery -i myGalleryImage -o table
```

Obtenha a ID de uma versão de imagem usando [az sig image-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```
az sig image-version show \
-g myGalleryRG \     
-r myGallery \     
-i myGalleryImage \     
--gallery-image-version-name 1.0.0 \     
--query "id"
```