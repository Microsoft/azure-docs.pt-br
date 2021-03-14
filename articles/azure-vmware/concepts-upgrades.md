---
title: Conceitos – atualizações de nuvem privada e atualizações
description: Saiba mais sobre os principais processos e recursos de atualização da solução Azure VMware.
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: d93453cbf6ad744844a04cd298cc18ad181cc0b0
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634985"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Atualizações e upgrades de nuvem privada da solução Azure VMware

Um benefício das nuvens privadas da solução Azure VMware é que a plataforma é mantida para você. A manutenção inclui atualizações automatizadas para um pacote de software validado pela VMware para ajudar a garantir que você esteja usando a versão mais recente do software de nuvem privada da solução Azure VMware.

Especificamente, uma nuvem privada da solução Azure VMware inclui:

- Nós de servidor bare-metal dedicados provisionados com VMware ESXi hipervisor 
- vCenter Server para gerenciamento do ESXi e vSAN 
- Rede definida pelo software VMware NSX-T para VMs de carga de trabalho vSphere  
- VMware vSAN datastore para VMs de carga de trabalho vSphere  
- VMware HCX para mobilidade de carga de trabalho  

Uma nuvem privada da solução Azure VMware também inclui recursos na underlay do Azure necessários para a conectividade e para operar a nuvem privada. A solução VMware do Azure monitora continuamente a integridade dos componentes do underlay e do VMware. Quando a solução Azure VMware detecta uma falha, ela executa uma ação para reparar os componentes com falha. 

## <a name="what-components-get-updated"></a>Quais componentes são atualizados?   

A solução Azure VMware atualiza os seguintes componentes do VMware: 

- vCenter Server e ESXi em execução nos nós de servidor bare-metal 
- vSAN 
- NSX-T 

A solução Azure VMware também atualiza o software no Underlay, como drivers, software nos comutadores de rede e firmware nos nós bare-metal. 

## <a name="types-of-updates"></a>Tipos de atualizações

A solução Azure VMware aplica os seguintes tipos de atualizações aos componentes do VMware:

- Patches: patches de segurança e correções de bugs liberadas pelo VMware. 
- Atualizações: atualizações de versão secundárias de um ou mais componentes do VMware. 
- Atualizações: atualizações de versão principal de um ou mais componentes do VMware.

Você será notificado antes e depois que os patches forem aplicados às suas nuvens privadas. Também trabalharemos com você para agendar uma janela de manutenção antes de aplicar atualizações ou atualizações à sua nuvem privada. 

## <a name="vmware-appliance-backup"></a>Backup do dispositivo VMware 

A solução Azure VMware também usa um backup de configuração dos seguintes componentes do VMware:

- vCenter Server 
- Gerenciador de NSX-T 

Em momentos de falha, a solução Azure VMware pode restaurar esses componentes do backup de configuração. 

Para obter mais informações sobre versões de software VMware, consulte o [artigo conceito de nuvens privadas e clusters](concepts-private-clouds-clusters.md) e as [perguntas frequentes](faq.yml).

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou os principais processos e recursos de atualização da solução Azure VMware, talvez queira saber mais sobre:

- [Como criar uma nuvem privada](tutorial-create-private-cloud.md).
- [Como habilitar o recurso de solução VMware do Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
