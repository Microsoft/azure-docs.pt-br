---
title: "O que é o Azure Site Recovery? | Microsoft Docs"
description: "Fornece uma visão geral do serviço Azure Site Recovery e resume os cenários de implantação."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/25/2017
ms.author: raynew
ms.openlocfilehash: aa657c92f347f7529affee78ad1842e5e066b74d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-site-recovery"></a>O que é a Recuperação de Site?

Bem-vindo ao Azure Site Recovery! Este artigo fornece uma visão geral rápida do serviço.

## <a name="business-continuity-and-disaster-recovery-bcdr-with-azure-recovery-services"></a>Continuidade de negócios e recuperação de desastre (BCDR) com os Serviços de Recuperação do Azure

Como uma organização, você precisa descobrir como pretende manter seus dados seguros e os aplicativos/ascargas de trabalho em execução durantes interrupções planejadas e não planejadas.

Os Serviços de Recuperação do Azure contribuem para sua estratégia BCDR:

- **O serviço Site Recovery**: o Site Recovery ajuda a garantir a continuidade dos negócios, mantendo seus aplicativos em execução em VMs e servidores físicos disponíveis se um site fica inoperante. O Site Recovery replica as cargas de trabalho em execução em VMs e servidores físicos para que eles permaneçam disponíveis em um local secundário se o site primário não estiver disponível. Ele recupera as cargas de trabalho para o site primário quando ele está ativo e em execução novamente.
- **Serviço de Backup**: além disso, o serviço [Backup do Azure](https://docs.microsoft.com/azure/backup/) mantém seus dados seguros e recuperáveis fazendo o backup em do Azure.

O Site Recovery pode gerenciar a replicação para:

- VMs do Azure que replicam entre regiões do Azure.
- As máquinas virtuais e servidores físicos locais que replicam no Azure ou em um site secundário.


## <a name="what-does-site-recovery-provide"></a>O que o Site Recovery fornece?

**Recurso** | **Detalhes**
--- | ---
**Implantar uma solução simples de BCDR** | Com o Site Recovery, você pode configurar e gerenciar replicação, failover e failback em um único local no portal do Azure.
**Replicar máquinas virtuais do Azure** | Você pode configurar sua estratégia de BCDR para que as VMs do Azure sejam replicadas entre regiões do Azure.
**Replicar VMs locais fora do site** | Você pode replicar VMs locais e servidores físicos no Azure ou em um site secundário local. A replicação no Azure elimina o custo e a complexidade de manter um data center secundário.
**Replicar carga de trabalho** | Replicar qualquer carga de trabalho em execução em VMs do Azure, VMs locais do Hyper-V e VMs do VMware com suporte, bem como em servidores físicos Windows/Linux.
**Manter dados flexíveis e seguros** | A recuperação de site gerencia a replicação e o failover, sem interceptar dados de aplicativo. Os dados replicados são armazenados no armazenamento do Azure, com toda a resiliência que ele oferece. Quando ocorrer um failover, as VMs do Azure serão criadas com base nos dados replicados.
**Atender aos RTOs e RPOs** | Manter os RTO (objetivos de tempo de recuperação) e os RPO (objetivos de ponto de recuperação) dentro dos limites da organização. O Site Recovery fornece uma frequência da replicação tão baixa quanto 30 segundos para o Hyper-V, assim como a replicação contínua para o as VMs do Azure e VMs do VMware. Você pode reduzir os RTO (objetivos de tempo de recuperação) ainda mais integrando o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Manter os aplicativos consistentes durante um failover** | Você pode configurar pontos de recuperação com instantâneos consistentes no aplicativo. Os instantâneos consistentes com os aplicativos capturam dados de disco, todos os dados na memória e todas as transações em andamento.
**Testar sem interrupção** | Você pode facilmente executar failovers de teste para dar suporte à recuperação de desastre, sem afetar a replicação em andamento.
**Executar failovers flexíveis** | Você pode executar failovers planejados para interrupções esperadas com perda de dados zero ou failovers não planejados com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. É possível executar facilmente o failback para o site primário quando ele estiver disponível novamente.
**Criar planos de recuperação** | Você pode personalizar e sequenciar o failover e a recuperação de aplicativos de várias camadas em várias VMs com planos de recuperação. Agrupe computadores em planos e adicione scripts e ações manuais. Os planos de recuperação podem ser integrados com runbooks de automação do Azure.
**Integrar com tecnologias BCDR existentes** | O Site Recovery se integra a outras tecnologias BCDR. Por exemplo, você pode usar o Site Recovery para proteger o back-end do SQL Server das cargas de trabalho corporativas, incluindo o suporte nativo para o SQL Server AlwaysOn, a fim de gerenciar o failover dos grupos de disponibilidade.
**Integrar com a biblioteca de automação** | Uma biblioteca de Automação do Azure avançada fornece scripts prontos para a produção e específicos do aplicativo que podem ser baixados e integrados na Recuperação de Site.
**Definir configurações de rede** | O Site Recovery se integra com o Azure para gerenciamento de rede de aplicativo simples, incluindo a reserva de endereços IP, a configuração de balanceadores de carga e a integração do Gerenciador de Tráfego do Azure para ter uma troca de rede eficiente.


## <a name="what-can-i-replicate"></a>O que posso replicar?

**Com suporte** | **Detalhes**
--- | ---
**O que posso replicar?** | VMs do Azure entre regiões do Azure (em versão prévia)<br/><br/>  VMs locais do VMware, VMs do Hyper-V, servidores físicos (Windows e Linux) para Azure<br/<br/> VMs VMware, VMs do Hyper-V, servidores físicos para um site secundário. Para VMs do Hyper-V, somente há suporte para replicação em um site secundário se os hosts Hyper-V são gerenciados pelo System Center VMM.
**Quais são as regiões têm suporte para o Site Recovery?** | [Regiões com suporte](https://azure.microsoft.com/regions/services/) |
**De quais sistemas operacionais os computadores replicados precisam?** | [Requisitos de VM do Azure](site-recovery-support-matrix-azure-to-azure.md#support-for-replicated-machine-os-versions)s<br></br>[Requisitos de VM do VMware](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)<br/><br/> Para VMs do Hyper-V, há suporte para qualquer [SO convidado](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) com suporte do Azure e do Hyper-V.<br/><br/> [Requisitos de servidor físico](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)
**Quais servidores/hosts VMware são necessários?** | As VMs VMware pode estar localizadas em [servidores vCenter/hosts vSphere com suporte](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Quais cargas de trabalho posso replicar?** | Você pode replicar qualquer carga de trabalho em execução em um computador de replicação com suporte. Além disso, a equipe do Site Recovery realizou testes específicos do aplicativo para [alguns aplicativos](site-recovery-workload.md#workload-summary).


## <a name="azure-portal-considerations"></a>Considerações sobre o portal do Azure

* O Site Recovery pode ser implantado no [portal do Azure](https://portal.azure.com).
* No Portal Clássico do Azure, você pode gerenciar o Site Recovery com o modelo de gerenciamento de serviços clássico.
- O portal clássico deve ser usado somente para manter as implantações do Site Recovery existentes. Não é possível criar novos cofres no portal clássico.

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre [Suporte a carga de trabalho](site-recovery-workload.md)
* Introdução a [replicação de VM do Azure entre regiões](site-recovery-azure-to-azure.md), [replicação VMware no Azure](vmware-walkthrough-overview.md) ou [replicação do Hyper-V no Azure](hyper-v-site-walkthrough-overview.md).
