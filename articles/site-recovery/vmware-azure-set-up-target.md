---
title: Preparar o destino de replicação de VM VMware no Azure Site Recovery
description: Este artigo descreve como preparar o ambiente de destino do Azure para replicação de uma VM do VMware no Azure.
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/03/2019
ms.author: mayg
ms.openlocfilehash: c721234f2e1d806d51d31f3466e441bf8360f6b8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "73693177"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparar o ambiente de destino para recuperação de desastre de VMs VMware ou servidores físicos para o Azure

Este artigo descreve como configurar o ambiente de destino do Azure para iniciar a replicação de máquinas virtuais do VMware ou de servidores físicos no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo supõe que:
- Você criou um cofre dos serviços de recuperação no [portal do Azure](https://portal.azure.com "Portal do Azure") para proteger seus computadores de origem
- Você configurou seu ambiente local para replicar as [máquinas virtuais do VMware](vmware-azure-set-up-source.md) ou [servidores físicos](physical-azure-set-up-source.md) de origem no Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir a **Etapa 1: Selecionar meta de proteção** e a **Etapa 2: Preparar origem**, você segue para a **Etapa 3: Destino**

![Preparar o destino](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Assinatura:** no menu suspenso, escolha a assinatura na qual deseja replicar suas máquinas virtuais ou servidores físicos.
2. **Modelo de Implantação:** escolha o modelo de implantação (Clássico ou Resource Manager)

Com base no modelo de implantação escolhido, uma validação é executada para garantir que você tenha pelo menos uma rede virtual na assinatura de destino para replicar e fazer failover de sua máquina virtual ou servidor físico para o.

Depois que as validações são concluídas com êxito, clique em OK de modo a passar para a próxima etapa.

Se você não tiver uma rede virtual, poderá criar uma clicando no botão **+ rede** na parte superior da página.

## <a name="next-steps"></a>Próximas etapas
[Defina as configurações de replicação](vmware-azure-set-up-replication.md).
