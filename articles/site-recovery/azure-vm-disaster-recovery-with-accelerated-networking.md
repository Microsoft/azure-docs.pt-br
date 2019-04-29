---
title: Rede acelerada com a recuperação de desastre de máquina virtual do Azure | Microsoft Docs
description: Descreve como habilitar a Rede acelerada com o Azure Site Recovery para recuperação de desastre de máquina virtual do Azure
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: mayg
ms.openlocfilehash: c7edc7979636ced8697aa5ad724f9c6600d840bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772446"
---
# <a name="accelerated-networking-with-azure-virtual-machine-disaster-recovery"></a>Rede acelerada com a recuperação de desastre de máquina virtual do Azure

A Rede acelerada permite SR-IOV (virtualização de E/S de raiz única) para uma VM, melhorando muito seu desempenho de rede. Esse caminho de alto desempenho ignora o host do caminho de dados, reduzindo a latência, a tremulação e a utilização da CPU para uso com as cargas de trabalho de rede mais exigentes nos tipos de VM compatíveis. A figura abaixo mostra a comunicação entre duas VMs com e sem rede acelerada:

![Comparação](./media/azure-vm-disaster-recovery-with-accelerated-networking/accelerated-networking-benefit.png)

O Azure Site Recovery permite que você utilize os benefícios da Rede acelerada para máquinas virtuais do Azure que fizeram failover em uma região diferente do Azure. Este artigo descreve como habilitar a Rede acelerada para máquinas virtuais do Azure replicadas com o Azure Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você compreende:
-   A [arquitetura de replicação](azure-to-azure-architecture.md) da máquina virtual do Azure
-   A [configuração da replicação](azure-to-azure-tutorial-enable-replication.md) nas máquinas virtuais do Azure
-   O [failover](azure-to-azure-tutorial-failover-failback.md) das máquinas virtuais do Azure

## <a name="accelerated-networking-with-windows-vms"></a>Rede acelerada com VMs do Windows

O Azure Site Recovery dá suporte a habilitar a Rede acelerada para máquinas virtuais replicadas somente se a máquina virtual de origem tiver a Rede acelerada habilitada. Se sua máquina virtual de origem não tem a Rede acelerada habilitada, saiba como habilitar a Rede acelerada para máquinas virtuais do Windows [aqui](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
As seguintes distribuições têm suporte imediato da Galeria do Azure:
* **Windows Server 2016 Datacenter**
* **Windows Server 2012 R2 Datacenter**

### <a name="supported-vm-instances"></a>Instâncias de VM compatíveis
A Rede Acelerada é compatível com os tamanhos de instância de uso geral e de computação otimizada com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/Fs

Em instâncias que são compatíveis com hyperthreading, a Rede Acelerada é compatível com instâncias de VM com 4 ou mais vCPUs. Essas séries com suporte são: D/DSV3, E/esv3, Fsv2 e Ms/Mms

Para obter mais informações sobre instâncias de VM, consulte [Tamanhos de VM do Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="accelerated-networking-with-linux-vms"></a>Rede acelerada com VMs do Linux

O Azure Site Recovery dá suporte a habilitar a Rede acelerada para máquinas virtuais replicadas somente se a máquina virtual de origem tiver a Rede acelerada habilitada. Se sua máquina virtual de origem não tem a Rede acelerada habilitada, saiba como habilitar a Rede acelerada para máquinas virtuais do Linux [aqui](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte
As seguintes distribuições têm suporte imediato da Galeria do Azure:
* **Ubuntu 16.04**
* **SLES 12 SP3**
* **RHEL 7.4**
* **CentOS 7.4**
* **CoreOS Linux**
* **Debian "Stretch" com kernel de portas traseiras**
* **Oracle Linux 7.4**

### <a name="supported-vm-instances"></a>Instâncias de VM compatíveis
A Rede Acelerada é compatível com os tamanhos de instância de uso geral e de computação otimizada com 2 ou mais vCPUs.  Essas séries com suporte são: D/DSv2 e F/Fs

Em instâncias que são compatíveis com hyperthreading, a Rede Acelerada é compatível com instâncias de VM com 4 ou mais vCPUs. Essas séries com suporte são: D/DSv3, E/ESv3, Fsv2 e Ms/Mms.

Para obter mais informações sobre instâncias de VM, consulte [Tamanhos de VM do Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enabling-accelerated-networking-for-replicated-vms"></a>Habilitando a Rede acelerada para VMs replicadas

Quando você [habilitar a replicação](azure-to-azure-tutorial-enable-replication.md) para máquinas virtuais do Azure, o Site Recovery detectará automaticamente se os adaptadores de rede da máquina virtual têm Rede acelerada habilitada. Se a Rede acelerada já estiver habilitada, o Site Recovery vai configurar automaticamente a Rede acelerada nos adaptadores de rede da máquina virtual replicada.

O status da Rede acelerada pode ser verificado na seção **Adaptadores de rede** das configurações de **Computação e Rede** da máquina virtual replicada.

![Configuração de Rede acelerada](./media/azure-vm-disaster-recovery-with-accelerated-networking/compute-network-accelerated-networking.png)

Se tiver habilitado a Rede acelerada na máquina virtual de origem depois de habilitar a replicação, você pode habilitar a Rede acelerada nos adaptadores de rede da máquina virtual replicada pelo seguinte processo:
1. Abra as configurações **Computação e Rede** da máquina virtual replicada
2. Clique no nome do adaptador de rede na seção **Adaptadores de rede**
3. Selecione **Habilitado** na lista suspensa para a Rede acelerada na coluna **Destino**

![Habilitar Rede acelerada](./media/azure-vm-disaster-recovery-with-accelerated-networking/network-interface-accelerated-networking-enabled.png)

O processo acima também deve ser seguido para máquinas virtuais existentes replicadas que não tinham anteriormente a Rede acelerada habilitada automaticamente pelo Site Recovery.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [benefícios de Rede acelerada](../virtual-network/create-vm-accelerated-networking-powershell.md#benefits).
- Saiba mais sobre as limitações e restrições da Rede acelerada para [máquinas de virtuais do Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#limitations-and-constraints) e [máquinas virtuais do Linux](../virtual-network/create-vm-accelerated-networking-cli.md#limitations-and-constraints).
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativo.
