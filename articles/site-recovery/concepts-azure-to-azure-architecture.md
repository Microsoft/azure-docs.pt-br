---
title: "Examine a arquitetura para a replicação de máquinas virtuais do Azure entre regiões do Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usados ao replicar VMs do Azure entre regiões do Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: f9cd57e47a8463440148b2bcc6c21beacd54382a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-to-azure-replication-architecture"></a>Arquitetura de replicação do Azure para o Azure


Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de máquinas virtuais (VMs) do Azure entre regiões do Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md).

>[!NOTE]
>Atualmente, a replicação de VM do Azure com o serviço Site Recovery está em versão prévia.



## <a name="architectural-components"></a>Componentes de arquitetura

O gráfico a seguir fornece uma exibição de alto nível de um ambiente de VM do Azure em uma região específica (neste exemplo, a localização Leste dos EUA). Em um ambiente de VM do Azure:
- Os aplicativos podem ser executados em VMs com discos distribuídos em contas de armazenamento.
- As VMs podem ser incluídas em uma ou mais sub-redes de uma rede virtual.


**Replicação do Azure para o Azure**

![ambiente do cliente](./media/concepts-azure-to-azure-architecture/source-environment.png)

## <a name="replication-process"></a>Processo de replicação

### <a name="step-1"></a>Etapa 1

Quando você habilita a replicação de VM do Azure, os recursos mostrados abaixo são criados automaticamente na região de destino, com base nas configurações de região de origem. Você pode personalizar as configurações de recursos de destino conforme necessário. 

![Habilitar o processo de replicação, etapa 1](./media/concepts-azure-to-azure-architecture/enable-replication-step-1.png)

**Recurso** | **Detalhes**
--- | ---
**Grupo de recursos de destino** | O grupo de recursos ao qual as VMs replicadas pertencem após o failover.
**Rede virtual de destino** | A rede virtual na qual as VMs replicadas estão localizadas após o failover. Um mapeamento de rede é criado entre redes virtuais de origem e de destino e vice-versa.
**Contas de armazenamento de cache** | Antes que as alterações nas VMs de origem sejam replicadas para uma conta de armazenamento de destino, elas são acompanhadas e enviadas para a conta de armazenamento de cache no local de destino. Isso garante um impacto mínimo sobre os aplicativos de produção em execução na VM.
**Contas de armazenamento de destino**  | Contas de armazenamento no local de destino para as quais os dados são replicados.
**Conjuntos de disponibilidade de destino**  | Conjuntos de disponibilidade nos quais as VMs replicadas estão localizadas após o failover.

### <a name="step-2"></a>Etapa 2

Conforme a replicação é habilitada, a extensão Serviço de mobilidade do Site Recovery é automaticamente instalada na VM:

1. A VM é registrada no Site Recovery.

2. A replicação contínua é configurada na VM. As gravações de dados nos discos de VM são continuamente transferidas para a conta de armazenamento de cache na localização de origem.

   ![Habilitar o processo de replicação, etapa 2](./media/concepts-azure-to-azure-architecture/enable-replication-step-2.png)

  
 O Site Recovery nunca precisa de conectividade de entrada à VM. Somente a conectividade de saída é necessária para os endereços IP/URLs do serviço Site Recovery, os endereços IP/URLs de autenticação do Office 365 e os endereços IP da conta de armazenamento de cache.

### <a name="step-3"></a>Etapa 3

Depois que a replicação contínua estiver em andamento, as gravações de disco serão transferidas imediatamente para a conta de armazenamento de cache. O Site Recovery processa os dados e envia-os para a conta de armazenamento de destino. Depois que os dados são processados, pontos de recuperação são gerados na conta de armazenamento de destino em intervalos de alguns minutos.

## <a name="failover-process"></a>Processo de failover

Quando você inicia um failover, as VMs são criadas no grupo de recursos de destino, na rede virtual de destino, na sub-rede de destino e no conjunto de disponibilidade de destino. Durante um failover, você pode usar qualquer ponto de recuperação.

![Processo de failover](./media/concepts-azure-to-azure-architecture/failover.png)

## <a name="next-steps"></a>Próximas etapas

Examine a matriz de suporte e siga o tutorial para habilitar a replicação de VM do Azure para uma região secundária.
Execute um failover e um failback.