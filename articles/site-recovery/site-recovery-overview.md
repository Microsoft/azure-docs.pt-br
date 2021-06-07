---
title: Sobre o Azure Site Recovery
description: Fornece uma visão geral do serviço Azure Site Recovery e resume os cenários de implantação de migração e de recuperação de desastre.
ms.topic: overview
ms.date: 03/17/2020
ms.custom: MVC
ms.openlocfilehash: 19860e64182cd73fe9f9fa1246f440a03109d465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92281873"
---
# <a name="about-site-recovery"></a>Sobre o Azure Site Recovery

Bem-vindo ao Azure Site Recovery! Este artigo fornece uma visão geral rápida do serviço.

Como uma organização, você precisa adotar uma estratégia de BCDR (continuidade dos negócios e recuperação de desastres) que mantenha seus dados seguros e seus aplicativos e cargas de trabalho online quando ocorrerem interrupções planejadas e não planejadas.

Os Serviços de Recuperação do Azure contribuem para sua estratégia BCDR:

- **Serviço do Site Recovery**: o Site Recovery ajuda a garantir a continuidade dos negócios por manter os aplicativos de negócios e cargas de trabalho funcionado durante interrupções. O Site Recovery replica as cargas de trabalho em execução em máquinas físicas e virtuais (VMs) de um site primário para um local secundário. Quando uma interrupção ocorre no seu site primário, você faz failover para o local secundário e acessa os aplicativos a partir daí. Depois que a localização primária estiver novamente em execução, você poderá fazer failback a ela.
- **Serviço de backup**: o serviço de [Backup do Azure](../backup/index.yml) mantém seus dados seguros e recuperáveis.

O Site Recovery pode gerenciar a replicação para:

- VMs do Azure que replicam entre regiões do Azure.
- VMs locais, VMs do Azure Stack e servidores físicos.

## <a name="what-does-site-recovery-provide"></a>O que o Site Recovery fornece?

**Recurso** | **Detalhes**
--- | ---
**Solução simples de BCDR** | Com o Site Recovery, você pode configurar e gerenciar replicação, failover e failback em um único local no portal do Azure.
**Replicação de VM do Azure** | Você pode configurar a recuperação de desastre de VMs do Azure de uma região primária para uma região secundária.
**Replicação de VM local** | Você pode replicar VMs locais e servidores físicos no Azure ou em um datacenter secundário local. A replicação no Azure elimina o custo e a complexidade de manter um data center secundário.
**Replicação de carga de trabalho** | Replicar qualquer carga de trabalho em execução em VMs do Azure, Hyper-V local e VMs do VMware com suporte, bem como em servidores físicos Windows/Linux.
**Resiliência de dados** | O Site Recovery gerencia a replicação sem interceptar dados do aplicativo. Ao replicar ao Azure, os dados são armazenados no armazenamento do Azure, com toda a resiliência que ele oferece. Quando ocorrer um failover, as VMs do Azure serão criadas com base nos dados replicados.
**Destinos de RTO e RPO** | Mantenha os objetivos de tempo de recuperação (RTO) e os objetivos de ponto de recuperação (RPO) dentro dos limites da organização. O Site Recovery fornece uma frequência da replicação tão baixa quanto 30 segundos para o Hyper-V, assim como a replicação contínua para o as VMs do Azure e VMs do VMware. Você pode reduzir ainda mais os RTO por meio da integração com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/).
**Manter os aplicativos consistentes durante um failover** | Você pode replicar usando pontos de recuperação com instantâneos consistentes no aplicativo. Esses instantâneos capturam dados de disco, todos os dados na memória e todas as transações em andamento.
**Testar sem interrupção** | Você pode executar facilmente análises de recuperação de desastre, sem afetar a replicação em andamento.
**Failovers flexíveis** | Você pode executar failovers planejados para interrupções esperadas sem nenhuma perda de dados. Ou pode executar failovers não planejados com perda de dados mínima, dependendo da frequência de replicação, para desastres inesperados. É possível executar facilmente o failback para o site primário quando ele estiver disponível novamente.
**Planos de recuperação personalizados** | Usando planos de recuperação, você pode personalizar e sequenciar o failover e a recuperação de aplicativos de várias camadas em execução em várias VMs. Você agrupa computadores em um plano de recuperação e opcionalmente adiciona scripts e ações manuais. Os planos de recuperação podem ser integrados com runbooks de automação do Azure.
**Integração de BCDR** | O Site Recovery se integra a outras tecnologias BCDR. Por exemplo, é possível usar o Site Recovery para proteger o back-end do SQL Server das cargas de trabalho corporativas, com o suporte nativo para o SQL Server AlwaysOn, a fim de gerenciar o failover dos grupos de disponibilidade.
**Integração de automação do Azure** | Uma biblioteca de Automação do Azure avançada fornece scripts prontos para a produção e específicos do aplicativo que podem ser baixados e integrados na Recuperação de Site.
**Integração de rede** | O Site Recovery integra-se com o Azure para fins de gerenciamento da rede de aplicativos. Por exemplo, para reservar endereços IP, configurar balanceadores de carga e usar o Gerenciador de Tráfego do Azure para alternâncias de rede eficientes.

## <a name="what-can-i-replicate"></a>O que posso replicar?

**Com suporte** | **Detalhes**
--- | ---
**Cenários de replicação** | Replicar VMs do Azure de uma região do Azure para outra.<br/><br/>  Replique VMs locais da VMware, VMs do Hyper-V, servidores físicos (Windows e Linux), VMs do Azure Stack no Azure.<br/><br/> Replique instâncias do AWS Windows no Azure.<br/><br/> Replique VMs locais da VMware, VMs do Hyper-V gerenciado pelo System Center VMM e servidores físicos para um site secundário.
**Regiões** | Examine as [regiões com suporte](https://azure.microsoft.com/global-infrastructure/services/?products=site-recovery) para o Site Recovery. |
**Computadores replicados** | Analise os requisitos de replicação para a replicação de [VM do Azure](azure-to-azure-support-matrix.md#replicated-machine-operating-systems), de [VMs VMware locais e de servidores físicos](vmware-physical-azure-support-matrix.md#replicated-machines) e de [VMs Hyper-V local](hyper-v-azure-support-matrix.md#replicated-vms).
**Cargas de trabalho** | Você pode replicar qualquer carga de trabalho em execução em um computador com suporte para replicação. E a equipe do Site Recovery realizou testes específicos para [vários aplicativos](site-recovery-workload.md#workload-summary).

## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre o [suporte à carga de trabalho](site-recovery-workload.md).
- Introdução à [replicação de VM no Azure entre regiões](azure-to-azure-quickstart.md).
