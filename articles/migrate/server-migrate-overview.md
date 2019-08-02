---
title: Selecione uma opção de migração de VMware com a migração de servidor de migrar do Azure | Microsoft Docs
description: Fornece uma visão geral das opções para migração de VMs VMware no Azure com a migração de servidor de migrar do Azure
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811564"
---
# <a name="select-a-vmware-migration-option"></a>Selecione uma opção de migração de VMware

Você pode migrar VMs VMware no Azure usando a ferramenta de migração do servidor de migrar do Azure. Essa ferramenta oferece duas opções para a migração de VM do VMware:

- Migração usando a replicação sem agente. Migre VMs sem precisar instalar nada neles.
- Migração com um agente de replicação. Instale um agente na VM para replicação.


Embora a replicação sem agente é mais fácil de uma perspectiva de implantação, ele atualmente tem várias limitações.

## <a name="agentless-migration-limitations"></a>Limitações de migração sem agente

Limitações são da seguinte maneira:

- **Replicação simultânea**: Um máximo de 50 VMs pode ser replicado simultaneamente de um servidor vCenter.<br/> Se você tiver mais de 50 VMs para a migração, crie vários lotes de VMs.<br/> Replicando mais de uma vez, afetar o desempenho.
- **Discos de VM**: Uma VM que você deseja migrar deve ter os discos de 60 ou menos.
- **Sistemas operacionais VM**: Em geral, as migrações para Azure pode migrar qualquer Windows Server ou o sistema operacional Linux, mas pode exigir alterações nas VMs para que eles podem ser executados no Azure. As migrações para Azure faz as alterações automaticamente para esses sistemas operacionais:
    - Red Hat Enterprise Linux 6.5+, 7.0+
    - CentOS 6.5+, 7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS, 16.04LTS, 18.04LTS
    - Debian 7,8
    - Para outros sistemas operacionais, você precisa fazer ajustes manualmente antes da migração. O [migrar tutorial](tutorial-migrate-vmware.md) explica como fazer isso.
- **Inicialização do Linux**: Se /boot estiver em uma partição dedicada, ele deve residir no disco do SO e não sejam distribuído em vários discos.<br/> Se /boot faz parte da partição raiz (/), em seguida, a partição '/' deve estar no disco do sistema operacional e não abrangem outros discos.
- **Inicialização de UEFI**: Máquinas virtuais com inicialização UEFI não têm suporte para a migração.
- **(O BitLocker, cryptfs) de discos/volumes criptografados**: VMs com discos/volumes criptografados não têm suporte para a migração.
- **Discos de passagem/RDM**: Se as VMs tiverem discos RDM ou passagem, esses discos não serão duplicados para o Azure
- **NFS**: Volumes NFS montados como volumes nas máquinas virtuais não serão duplicados.
- **Armazenamento de destino**: Você pode apenas migrar VMs VMware para VMs do Azure com discos gerenciados (padrão de HDD, SSD Premium).



## <a name="deployment-steps-comparison"></a>Comparação de etapas de implantação

Depois de revisar as limitações, Noções básicas sobre as etapas envolvidas na implantação de cada solução pode ajudar você a decidir qual opção escolher.

**Tarefa** | **Detalhes** |**Sem agente** | **Com base em agente**
--- | --- | --- | ---
**Preparar servidores VMware e VMs para migração** | Configure diversas configurações em servidores VMware e VMs. | Obrigatório | Obrigatório
**Adicionar a ferramenta de migração do servidor** | Adicione a ferramenta de migração do servidor de migrar do Azure no projeto de migrações para Azure. | Obrigatório | Obrigatório
**Implantar o dispositivo de migrações para Azure** | Configure um dispositivo leve em uma VM VMware para descoberta VM e avaliação. | Obrigatório | Não obrigatório.
**Instalar o serviço de mobilidade em VMs** | Instalar o serviço de mobilidade em cada VM que você deseja replicar | Não requerido | Obrigatório
**Implantar o dispositivo de replicação de migração do servidor de migrar do Azure** | Configurar um dispositivo em uma VM VMware para descobrir VMs e criar uma ponte entre o serviço de mobilidade em execução em VMs e migração do servidor de migrar do Azure | Não requerido | Obrigatório
**Replicar VMs**. Habilite a replicação de VM. | Definir configurações de replicação e selecione VMs para replicar | Obrigatório | Obrigatório
**Executar um teste de migração** | Execute uma migração de teste para verificar se que tudo está funcionando conforme o esperado. | Obrigatório | Obrigatório
**Executar uma migração completa** | Migre as máquinas virtuais. | Obrigatório | Obrigatório




## <a name="next-steps"></a>Próximas etapas

[Migrar VMs VMware](tutorial-migrate-vmware.md) usando a migração sem agente.



