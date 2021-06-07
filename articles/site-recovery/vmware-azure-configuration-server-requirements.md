---
title: Recuperação de desastre do VMware – Requisitos do servidor de configuração no Azure Site Recovery
description: Este artigo descreve o suporte e os requisitos ao implantar o servidor de configuração para recuperação de desastre do VMware no Azure com Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 0b0942b517c8dc83c048bd1203a58d9861515dfb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95997794"
---
# <a name="configuration-server-requirements-for-vmware-disaster-recovery-to-azure"></a>Requisitos do servidor de configuração para recuperação de desastre do VMware para Azure

Você implanta um servidor de configuração local quando você usa o [Azure Site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure.

- O servidor de configuração coordena a comunicação entre o ambiente de VMware local e o Azure. Ele também gerencia a replicação de dados.
- [Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

## <a name="configuration-server-deployment"></a>Implantação do servidor de configuração

Para recuperação de desastre de VMs do VMware para Azure, você implanta o servidor de configuração como uma VM do VMware.

- O Site Recovery fornece um modelo OVA que você baixa do portal do Azure e importa para o vCenter Server para configurar a VM do servidor de configuração.
- Quando você implanta o servidor de configuração usando o modelo OVA, a VM cumpre automaticamente os requisitos listados neste artigo.
- É altamente recomendável que você configure o servidor de configuração usando o modelo OVA. No entanto, se estiver configurando a recuperação de desastre para VMs do VMware e não puder usar o modelo OVA, você poderá implantar o servidor de configuração usando [estas instruções fornecidas](physical-azure-set-up-source.md).
- Se você estiver implantando o servidor de configuração para recuperação de desastre de computadores físicos locais no Azure, siga as instruções [neste artigo](physical-azure-set-up-source.md). 

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="next-steps"></a>Próximas etapas
Configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) para o Azure.
