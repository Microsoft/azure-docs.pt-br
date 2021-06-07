---
title: Usar imagens de VM compartilhadas para criar um conjunto de dimensionamento no CLI do Azure
description: Saiba como usar a CLI do Azure para criar imagens de VM compartilhadas a usar para implantar Conjuntos de Dimensionamento de Máquinas Virtuais no Azure.
author: axayjo
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: 418258c498f768697aefebd5df0cc64b7255076f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676071"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Criar e usar imagens personalizadas para Conjuntos de Dimensionamento de Máquinas Virtuais com a CLI do Azure 2.0

Ao criar um conjunto de dimensionamento, você especifica uma imagem a ser usada quando as instâncias de VM forem implantadas. Uma [Galeria de Imagens Compartilhadas](../virtual-machines/shared-image-galleries.md) simplifica o compartilhamento da imagem personalizada em sua organização. Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. 

A Galeria de imagens compartilhadas permite que você compartilhe suas imagens com outras pessoas. Escolha quais imagens você deseja compartilhar, em quais regiões deseja torná-las disponíveis e com quem deseja compartilhá-las. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Próximas etapas

Crie uma versão de imagem de uma [VM](../virtual-machines/image-version-vm-cli.md)ou de uma [imagem gerenciada](../virtual-machines/image-version-managed-image-cli.md).

Para obter mais informações sobre galerias de imagens compartilhadas, confira a [Visão geral](../virtual-machines/shared-image-galleries.md). Se você enfrentar problemas, confira [Solução de problemas de galerias de imagens compartilhadas](../virtual-machines/troubleshooting-shared-images.md).