---
title: Migrar VMs do Azure para o Managed Disks | Microsoft Docs
description: Migre máquinas virtuais do Azure criadas com discos não gerenciados em contas de armazenamento a fim de usar o Managed Disks.
services: virtual-machines-windows
documentationcenter: ''
author: roygara
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 12cd1caa4cb96dbd5862776589d4a34aeb294ca1
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763746"
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para o Managed Disks no Azure

O Azure Managed Disks simplifica o gerenciamento do armazenamento, acabando com a necessidade de gerenciar as contas de armazenamento de forma separada.  Também é possível migrar suas VMs do Azure para o Managed Disks para aproveitar a melhor confiabilidade das VMs em um conjunto de disponibilidade. Isso garante um isolamento suficiente dos discos de VMs diferentes em um conjunto de disponibilidade a fim de evitar ponto único de falhas. Ele coloca automaticamente os discos de VMs diferentes em um conjunto de disponibilidade em unidades de escala (carimbos) de armazenamentos diferentes, o que limita o impacto de falhas em uma única unidade de escala de armazenamento causadas por falhas de hardware e de software.
Com base em suas necessidades, você pode escolher entre quatro tipos de opções de armazenamento. Para saber mais sobre os tipos de disco disponíveis, confira nosso artigo [Selecionar um tipo de disco](disks-types.md)

## <a name="migrate-scenarios"></a>Cenários de migração

É possível migrar para o Managed Disks nos cenários a seguir:

| **Migre...**                                            | **Link de documentação**                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Converter VMs autônomas e VMs em um conjunto de disponibilidade para discos gerenciados   | [Converter VMs para usar discos gerenciados](convert-unmanaged-to-managed-disks.md) |
| Uma única VM do modo clássico para o modo do Resource Manager em discos gerenciados     | [Criar uma VM de um VHD clássico](create-vm-specialized-portal.md)  | 
| Todas as VMs em uma vNet do modo clássico para o modo do Resource Manager em discos gerenciados     | [Migrar os recursos de IaaS do modo clássico para o modo do Resource Manager](migration-classic-resource-manager-ps.md) e [Converter uma VM de discos não gerenciados para discos gerenciados](convert-unmanaged-to-managed-disks.md) | 


## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [Managed Disks](managed-disks-overview.md)
- Confira os [preços dos Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).
