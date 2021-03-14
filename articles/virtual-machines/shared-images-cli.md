---
title: Criar galerias de imagens compartilhadas com o CLI do Azure
description: Neste artigo, você aprenderá a usar a CLI do Azure para criar uma imagem compartilhada de uma VM no Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 991b5363b180651b775bd46a0a1353fd124d34e6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557721"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Criar uma galeria de imagens compartilhada com o CLI do Azure

Uma [Galeria de Imagens Compartilhadas](./shared-image-galleries.md) simplifica o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. 

A Galeria de Imagens Compartilhadas permite que você compartilhe suas imagens de VM personalizadas com outras pessoas. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Próximas etapas

Crie uma versão de imagem de uma [VM](image-version-vm-cli.md)ou de uma [imagem gerenciada](image-version-managed-image-cli.md) usando o CLI do Azure.

Para obter mais informações sobre galerias de imagens compartilhadas, confira a [Visão geral](./shared-image-galleries.md). Se você enfrentar problemas, confira [Solução de problemas de galerias de imagens compartilhadas](troubleshooting-shared-images.md).