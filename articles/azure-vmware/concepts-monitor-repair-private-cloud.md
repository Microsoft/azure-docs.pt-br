---
title: Conceitos-monitorar e reparar nuvens privadas da solução Azure VMware
description: Saiba como a solução Azure VMware monitora e repara VMware ESXi servidores em uma nuvem privada da solução Azure VMware.
ms.topic: conceptual
ms.custom: contperf-fy21q2
ms.date: 02/16/2021
ms.openlocfilehash: 59319b5598be9770e82b9676a28444648230a019
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633131"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Monitorar e reparar nuvens privadas da solução Azure VMware

A solução VMware do Azure monitora continuamente os servidores VMware ESXi em uma nuvem privada da solução Azure VMware. 

## <a name="what-azure-vmware-solution-monitors"></a>O que a solução do Azure VMware monitora

A solução Azure VMware monitora as seguintes condições no host:  

- Status do processador 
- Status da memória 
- Estado de conexão e energia 
- Status do ventilador de hardware 
- Perda de conectividade de rede 
- Status da placa do sistema de hardware 
- Ocorreram erros nos discos de um host vSAN 
- Tensão de hardware 
- Status de temperatura do hardware 
- Status de energia de hardware 
- Status do armazenamento 
- Falha de conexão 

> [!NOTE]
> Os administradores de locatários da solução Azure VMware não devem editar ou excluir os alarmes do VMware vCenter definidos acima, pois são gerenciados pelo plano de controle da solução VMware do Azure no vCenter. Esses alarmes são usados pelo monitoramento da solução Azure VMware para disparar o processo de remediação do host da solução Azure VMware.

## <a name="azure-vmware-solution-host-remediation"></a>Correção do host da solução Azure VMware  

Quando a solução Azure VMware detecta uma degradação ou falha em um nó de solução do Azure VMware, ela dispara o processo de correção do host. A correção do host envolve a substituição do nó com falha por um novo nó íntegro.  

A correção do host começa adicionando um novo nó íntegro no cluster. Em seguida, quando possível, o host com falha é colocado no modo de manutenção VMware vSphere. O VMware vMotion move as VMs para fora do host com falha para outros servidores disponíveis no cluster, potencialmente permitindo um tempo de inatividade zero para a migração dinâmica de cargas de trabalho. Se o host com falha não puder ser colocado no modo de manutenção, o host será removido do cluster.

## <a name="next-steps"></a>Próximas etapas

Agora que você já abordou como a solução do Azure VMware monitora e repara nuvens privadas, talvez você queira aprender sobre:

- [Atualizações da nuvem privada da solução Azure VMware](concepts-upgrades.md).
- [Como habilitar o recurso de solução VMware do Azure](enable-azure-vmware-solution.md).
