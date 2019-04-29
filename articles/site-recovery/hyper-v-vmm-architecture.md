---
title: Arquitetura para recuperação de desastre do Hyper-V para um site local secundário com o Azure Site Recovery | Microsoft Docs
description: Este artigo apresenta uma visão geral da arquitetura para recuperação de desastre de VMs Hyper-V locais para um site VMM do System Center secundário com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: 553ecefc9f7588de0ee9056f2b2385f9a0ede50f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772087"
---
# <a name="architecture---hyper-v-replication-to-a-secondary-site"></a>Arquitetura – replicação do Hyper-V para um site secundário

Este artigo descreve os componentes e processos envolvidos na replicação de VMs (máquinas virtuais) do Hyper-V locais em nuvens do System Center Virtual Machine Manager (VMM) para um site de VMM secundário usando o serviço [Azure Site Recovery](site-recovery-overview.md) no Portal do Azure.


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para replicação do Hyper-V para um site secundário.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Assinatura do Azure | Crie um cofre dos Serviços de Recuperação na assinatura do Azure para orquestrar e gerenciar a replicação entre os locais do VMM.
**Servidor VMM** | Você precisa de locais primário e secundário para o VMM. | Recomendamos um servidor VMM no site primário e um no site secundário.
**Servidor Hyper-V** |  Um ou mais servidores de host do Hyper-V nas nuvens do VMM primárias e secundárias. | Os dados são replicados entre os servidores de host Hyper-V primários e secundários pela LAN ou VPN usando a autenticação Kerberos ou autenticação de certificado.  
**VMs Hyper-V** | No servidor de host do Hyper-V. | O servidor host de origem deve ter pelo menos uma VM que você deseja replicar.

**Arquitetura de local para local**

![Local para o próprio local](./media/hyper-v-vmm-architecture/arch-onprem-onprem.png)

## <a name="replication-process"></a>Processo de replicação

1. Um [Instantâneo da VM do Hyper-V](https://technet.microsoft.com/library/dd560637.aspx) é tirado quando a replicação inicial é disparada.
2. Discos rígidos virtuais na VM são replicados individualmente para a localização secundária.
3. Se houver alterações no disco durante a replicação inicial, o Rastreador de Replicação de Réplica do Hyper-V mostrará essas alterações como logs de replicação do Hyper-V (.hrl). Esses arquivos de log estão localizados na mesma pasta que os discos. Cada disco tem um arquivo .hrl associado que é enviado para a localização secundária. O instantâneo e os arquivos de log consomem recursos de disco durante a replicação inicial.
4. Quando a replicação inicial é concluída, o instantâneo da VM é excluído e a replicação delta é iniciada.
5. As alterações de disco delta no log são sincronizadas e mescladas para o disco pai.


## <a name="failover-and-failback-process"></a>Processo de failover e failback

- Você pode fazer o failover de um único computador ou criar planos de recuperação para orquestrar o failover de várias máquinas virtuais.
- Você pode executar um failover planejado ou não planejado entre sites locais. Se você executar um failover planejado, as VMs de origem serão desligadas para evitar a perda de dados.
    - Se você realizar um failover não planejado em um site secundário, depois do failover, as máquinas virtuais no local secundário não serão protegidas.
    - Depois de executar um failover planejado, os computadores no local secundário são protegidos.
- Depois da execução do failover inicial, você confirma-o para começar a acessar a carga de trabalho da réplica de VM.
- Quando a localização primária estiver disponível novamente, você poderá fazer failback.
    - Você inicia a replicação inversa, para iniciar a replicação do site secundário para o primário. A replicação inversa coloca as máquinas virtuais em um estado protegido, mas o datacenter secundário permanece sendo o local ativo.
    - Para transformar o site primário em local ativo novamente, inicie um failover planejado do site secundário para o primário, seguido por outra replicação inversa.



## <a name="next-steps"></a>Próximas etapas


Siga [este tutorial](hyper-v-vmm-disaster-recovery.md) para habilitar a replicação de Hyper-V entre nuvens de VMM.
