---
title: Mover uma máquina virtual de autogerenciamento do Azure entre regiões
description: Saiba como mover uma máquina virtual autogerenciada entre regiões
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/05/2021
ms.author: alsin
ms.custom: subject-moving-resources
ms.openlocfilehash: 99371b8618756c196b75858288c5c4785272a7e8
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650458"
---
# <a name="move-an-azure-automanage-virtual-machine-to-a-different-region"></a>Mover uma máquina virtual do Azure autogerenciar para uma região diferente
Este artigo descreve como manter o autogerenci habilitado em uma VM (máquina virtual) ao movê-lo para uma região diferente. Talvez você queira mover suas máquinas virtuais para outra região por vários motivos. Por exemplo, para aproveitar uma nova região do Azure, para atender aos requisitos internos de políticas e governança, ou em resposta aos requisitos de planejamento de capacidade. As VMs que você mover podem ser autogerenciadas no momento e talvez você queira que elas permaneçam autogerenciadas após a movimentação.

## <a name="prerequisites"></a>Pré-requisitos
* Verifique se a sua região de destino tem [suporte do autogerenci](./automanage-virtual-machines.md#prerequisites).
* Verifique se sua região de espaço de trabalho Log Analytics, região de conta de automação e sua região de destino são todas as regiões com suporte nos mapeamentos de região [aqui](../automation/how-to/region-mappings.md).

## <a name="prepare-your-automanaged-vms-for-moving"></a>Preparar suas VMs autogerenciadas para movimentação
Desabilite o autogerenci em suas VMs autogerenciadas. Você pode fazer isso selecionando suas VMs na folha autogerenciar e clicando em **desabilitar autogerenciamento** na folha autogerenciar.

## <a name="move-your-automanaged-vms-and-re-enable-automanage"></a>Mover suas VMs autogerenciadas e reabilitar o autogerenci
Para obter detalhes sobre como mover suas VMs, consulte este [artigo](../resource-mover/tutorial-move-region-virtual-machines.md).

Depois de mover suas VMs entre regiões, você poderá reabilitar novamente o autogerenci nelas. Os detalhes estão disponíveis [aqui](./automanage-virtual-machines.md#enabling-automanage-for-vms-in-azure-portal).

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre o autogerenciamento do Azure](./automanage-virtual-machines.md)
* [Exibir perguntas frequentes sobre o autogerenciamento do Azure](./faq.md)