---
title: Gerenciar a disponibilidade de VMs Linux no Azure | Microsoft Docs
description: "Aprenda como usar várias máquinas virtuais para garantir a alta disponibilidade do aplicativo do Linux no Azure"
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 891c852a-84c0-4940-a61e-ada6e185bf37
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f153a740e4814e2573e53b9c051d24c30ff9088f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>Gerenciar a disponibilidade de máquinas virtuais do Linux

Aprenda como configurar e gerenciar várias máquinas virtuais para garantir a alta disponibilidade do aplicativo Linux no Azure. Você também pode [gerenciar a disponibilidade das máquinas virtuais do Windows](../windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Para obter instruções sobre como criar um conjunto de disponibilidade usando a CLI no modelo de implantação do Resource Manager, consulte [azure availset: comandos para gerenciar seus conjuntos de disponibilidade](../azure-cli-arm-commands.md#azure-availset-commands-to-manage-your-availability-sets).

[!INCLUDE [virtual-machines-common-manage-availability](../../../includes/virtual-machines-common-manage-availability.md)]

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o balanceamento de carga das máquinas virtuais, veja [Balanceamento de carga de máquinas virtuais](../virtual-machines-linux-load-balance.md).

