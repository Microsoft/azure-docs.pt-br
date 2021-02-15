---
title: Listar, atualizar e excluir recursos de imagem usando o CLI do Azure
description: Liste, atualize e exclua recursos de imagem em sua galeria de imagens compartilhadas usando CLI do Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6099afc82e76ed28e8557ac0eee3e64cb292a715
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882001"
---
# <a name="list-update-and-delete-image-resources"></a>Listar, atualizar e excluir recursos de imagem 

Você pode gerenciar seus recursos da Galeria de imagens compartilhadas usando o CLI do Azure.

[!INCLUDE [virtual-machines-common-gallery-list-cli.md](../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Próximas etapas

O [Construtor de imagens do Azure (visualização)](./image-builder-overview.md) pode ajudar a automatizar a criação da versão da imagem, até mesmo usá-la para atualizar e [criar uma nova versão da imagem a partir de uma versão de imagem existente](./linux/image-builder-gallery-update-image-version.md).