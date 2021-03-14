---
title: Migrar VMs do Azure para Managed Disks
description: Migre máquinas virtuais do Azure criadas com discos não gerenciados em contas de armazenamento a fim de usar o Managed Disks.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 05/30/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 65b36db5185390cd6527a66c91eecd4712623df5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564332"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para o Managed Disks no Azure

O Azure Managed Disks simplifica o gerenciamento do armazenamento, acabando com a necessidade de gerenciar as contas de armazenamento de forma separada.  Também é possível migrar suas VMs do Azure para o Managed Disks para aproveitar a melhor confiabilidade das VMs em um conjunto de disponibilidade. Ele garante que os discos de VMs diferentes em um conjunto de disponibilidade estejam suficientemente isolados entre si para evitar um ponto único de falhas. Ele coloca automaticamente os discos de VMs diferentes em um conjunto de disponibilidade em unidades de escala (carimbos) de armazenamentos diferentes, o que limita o impacto de falhas em uma única unidade de escala de armazenamento causadas por falhas de hardware e de software.
Com base em suas necessidades, você pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, consulte nosso artigo [selecionar um tipo de disco](../disks-types.md)

## <a name="migration-scenarios"></a>Cenários de migração

É possível migrar para o Managed Disks nos cenários a seguir:

|Cenário  |Artigo  |
|---------|---------|
|Converter VMs autônomas e VMs em um conjunto de disponibilidade para discos gerenciados     |[Converter VMs para usar discos gerenciados](convert-unmanaged-to-managed-disks.md)         |
|Converter uma única VM do clássico para o Resource Manager em discos gerenciados     |[Criar uma VM de um VHD clássico](create-vm-specialized-portal.md)         |
|Converter todas as VMs em uma vNet do clássico para o Gerenciador de recursos em discos gerenciados     |[Migrar os recursos de IaaS do modo clássico para o modo do Resource Manager](../migration-classic-resource-manager-ps.md) e [Converter uma VM de discos não gerenciados para discos gerenciados](convert-unmanaged-to-managed-disks.md)         |
|Atualizar VMs com discos não gerenciados padrão para VMs com discos Premium gerenciados     | Primeiro, [Converta uma máquina virtual do Windows de discos não gerenciados em discos gerenciados](convert-unmanaged-to-managed-disks.md). Em seguida, [atualize o tipo de armazenamento de um disco gerenciado](convert-disk-storage.md).         |

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Managed disks](../managed-disks-overview.md)
- Confira os [preços dos Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).