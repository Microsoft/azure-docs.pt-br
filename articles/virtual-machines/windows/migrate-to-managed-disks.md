---
title: Migrar VMs do Azure para o Managed Disks | Microsoft Docs
description: "Migre máquinas virtuais do Azure criadas com discos não gerenciados em contas de armazenamento a fim de usar o Managed Disks."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/15/2017
ms.author: cynthn
ms.openlocfilehash: e23697b390e03bd2b71f2c905882070d864d62ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-azure-vms-to-managed-disks-in-azure"></a>Migrar VMs do Azure para o Managed Disks no Azure

O Azure Managed Disks simplifica o gerenciamento do armazenamento, acabando com a necessidade de gerenciar as contas de armazenamento de forma separada.  Também é possível migrar suas VMs do Azure para o Managed Disks para aproveitar a melhor confiabilidade das VMs em um conjunto de disponibilidade. Isso garante um isolamento suficiente dos discos de VMs diferentes em um conjunto de disponibilidade a fim de evitar pontos de falhas. Ele coloca automaticamente os discos de VMs diferentes em um conjunto de disponibilidade em unidades de escala (carimbos) de armazenamentos diferentes, o que limita o impacto de falhas em uma única unidade de escala de armazenamento causadas por falhas de hardware e de software.
Com base em suas necessidades, você pode escolher entre dois tipos de opções de armazenamento:

- Os [Managed Disks Premium](../../storage/common/storage-premium-storage.md) são uma mídia de armazenamento com base em unidade de estado sólido (SSD), que fornece alto desempenho, suporte a disco de baixa latência para máquinas virtuais com cargas de trabalho de E/S intensas. Você pode tirar proveito da velocidade e do desempenho desses discos migrando o Premium Managed Disks.

- [Standard Managed Disks](../../storage/common/storage-standard-storage.md) usa mídia de armazenamento com base em HDD (Unidade de Disco Rígido) e são apropriados para desenvolvimento/teste e outras cargas de trabalho de acesso infrequente menos sensíveis à variabilidade de desempenho.

É possível migrar para o Managed Disks nos cenários a seguir:

| Migrar...                                            | Link da documentação                                                                                                                                                                                                                                                                  |
|----------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Converter VMs autônomas e VMs em um conjunto de disponibilidade para discos gerenciados   | [Converter VMs para usar discos gerenciados](convert-unmanaged-to-managed-disks.md) |
| Uma única VM do modo clássico para o modo do Resource Manager em discos gerenciados     | [Migrar uma única VM](migrate-single-classic-to-resource-manager.md)  | 
| Todas as VMs em uma vNet do modo clássico para o modo do Resource Manager em discos gerenciados     | [Migrar os recursos de IaaS do modo clássico para o modo do Resource Manager](migration-classic-resource-manager-ps.md) e [Converter uma VM de discos não gerenciados para discos gerenciados](convert-unmanaged-to-managed-disks.md) | 






## <a name="plan-for-the-conversion-to-managed-disks"></a>Planejar-se para a conversão para Managed Disks

Esta seção ajuda você a tomar a melhor decisão sobre VM e tipos de disco.


## <a name="location"></a>Local padrão

Escolha um local onde o Azure Managed Disks estão disponíveis. Se você estiver mudando para o Premium Managed Disks, verifique também se o armazenamento Premium está disponível na região para onde você planeja mudar. Confira [Serviços do Azure por região](https://azure.microsoft.com/regions/#services) para obter informações atualizadas sobre as localizações disponíveis.

## <a name="vm-sizes"></a>Tamanhos de VM

Se você estiver migrando para Managed Disks Premium, será necessário atualizar o tamanho da VM para um tamanho compatível com o armazenamento premium disponível na região onde a VM está localizada. Examine os tamanhos de VM que são compatíveis com o armazenamento premium. As especificações de tamanho de VM do Azure são listadas em [Tamanhos para máquinas virtuais](sizes.md).
Examine as características de desempenho das máquinas virtuais que funcionam com o Armazenamento Premium e escolha o tamanho de VM mais apropriado que melhor atende à sua carga de trabalho. Certifique-se de que há largura de banda suficiente disponível na sua VM para direcionar o tráfego de disco.

## <a name="disk-sizes"></a>Tamanhos do disco

**Managed Disks Premium**

Há sete tipos de discos gerenciados premium que podem ser usados com sua VM e cada um tem IOPs e limites de taxa de transferência específicos. Leve em consideração esses limites ao escolher o tipo de disco Premium para sua VM com base nas necessidades de seu aplicativo em termos de capacidade, desempenho, escalabilidade e cargas de pico.

| Tipo de discos premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 128 GB| 512 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS por disco       | 120   | 240   | 500   | 2.300              | 5.000              | 7500              | 7500              | 
| Taxa de transferência por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo |

**Managed Disks Standard**

Há sete tipos de discos gerenciados standard que podem ser usados com sua VM. Cada um deles tem uma capacidade diferente, mas com os mesmos limites de taxa de transferência e IOPS. Escolha o tipo de discos gerenciados Standard com base nas necessidades de capacidade do seu aplicativo.

| Tipo de disco Standard  | S4               | S6               | S10              | S20              | S30              | S40              | S50              | 
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------| 
| Tamanho do disco           | 30 GB            | 64 GB            | 128 GB           | 512 GB           | 1024 GB (1 TB)   | 2048 GB (2TB)    | 4095 GB (4 TB)   | 
| IOPS por disco       | 500              | 500              | 500              | 500              | 500              | 500             | 500              | 
| Taxa de transferência por disco | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 60 MB por segundo | 

## <a name="disk-caching-policy"></a>Política de cache de disco

**Managed Disks Premium**

Por padrão, a política de cache de disco é *Somente leitura* para todos os discos de dados Premium e *Leitura e gravação* para o disco de sistema operacional Premium anexado à VM. Esta definição de configuração é recomendável para atingir o desempenho ideal de leituras de entrada e saída dos seus aplicativos. Para discos de dados de gravação intensa ou somente gravação (como arquivos de log do SQL Server), desabilite o cache de disco para que possa obter o melhor desempenho do aplicativo.

## <a name="pricing"></a>Preços

Confira os [preços dos Managed Disks](https://azure.microsoft.com/en-us/pricing/details/managed-disks/). Os preços dos Managed Disks Premium são iguais aos dos discos não gerenciados premium. No entanto, os preços do Standard Managed Disks são diferentes dos preços do Standard Unmanaged Disks.



## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [Managed Disks](managed-disks-overview.md)
