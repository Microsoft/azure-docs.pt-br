---
title: Migrar computadores locais com as Migrações para Azure
description: Este artigo resume como migrar computadores locais para o Azure e recomenda Migrações para Azure.
ms.service: site-recovery
ms.topic: tutorial
ms.date: 07/27/2020
ms.openlocfilehash: 5f5a0f65bfbde5ef8513780933cc570a4b5737df
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581144"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas locais para o Azure

Este artigo descreve as opções para migrar computadores locais para o Azure. 

## <a name="migrate-with-azure-migrate"></a>Migrar com as Migrações para Azure

Recomendamos que você migre computadores para o Azure usando o serviço [Migrações para Azure](../migrate/migrate-services-overview.md). O recurso Migrações para Azure é criado especificamente para a migração do servidor. As Migrações para Azure oferecem um hub central para descoberta, avaliação e migração de computadores locais para o Azure.

Siga estes links para migrar usando as Migrações para Azure:

- [Saiba mais](../migrate/server-migrate-overview.md) sobre as opções de migração para VMs do VMware e migre as VMs para o Azure usando a migração [sem agente](../migrate/tutorial-migrate-vmware.md) ou a migração [baseada em agente](../migrate/tutorial-migrate-vmware-agent.md).
- [Migrar VMs do Hyper-V](../migrate/tutorial-migrate-hyper-v.md) para o Azure.
- Migrar [servidores físicos ou outras VMs](../migrate/tutorial-migrate-physical-virtual-machines.md), incluindo [instâncias da AWS](../migrate/tutorial-migrate-aws-virtual-machines.md), para o Azure.

## <a name="migrate-with-site-recovery"></a>Migrar com o Site Recovery
O Site Recovery deve ser usado somente para recuperação de desastre, e não para migração.

Se já estiver usando o Azure Site Recovery e quiser dar continuidade a seu uso para a migração, siga as mesmas etapas que você usaria para a recuperação de desastre.

- VMs do VMware: [Prepare o Azure](tutorial-prepare-azure.md) e o [VMware](vmware-azure-tutorial-prepare-on-premises.md), inicie a [replicação de computadores](vmware-azure-tutorial.md), [verifique](tutorial-dr-drill-azure.md) se tudo está funcionando e [execute um failover](vmware-azure-tutorial-failover-failback.md).
- VMs do Hyper-V: [Prepare o Azure](tutorial-prepare-azure-for-hyperv.md) e o [Hyper-V](hyper-v-prepare-on-premises-tutorial.md), inicie [a replicação de computadores](hyper-v-azure-tutorial.md), [verifique](tutorial-dr-drill-azure.md) se tudo está funcionando e [execute um failover](hyper-v-azure-failover-failback-tutorial.md).
- Servidores físicos: [Siga o passo a passo](physical-azure-disaster-recovery.md) para preparar o Azure, prepare os computadores para a recuperação de desastre e configure a replicação.

> [!NOTE]
> Quando você executa um failover para recuperação de desastre, a última etapa é confirmar o failover. Quando você migra computadores locais, a opção **Confirmar** não é relevante. Em vez disso, selecione a opção **Concluir Migração**. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Veja perguntas comuns](../migrate/resources-faq.md) sobre as Migrações para Azure.

  
