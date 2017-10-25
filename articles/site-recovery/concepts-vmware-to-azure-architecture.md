---
title: "Rever a arquitetura para replicação de VMware no Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usada ao replicar VMs VMware locais para o Azure com o serviço Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d03d2dd3-2455-4ca8-a942-a342030ee6ce
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: raynew
ms.openlocfilehash: ac1151d15a88650f5845cb879cd210e9f7cba0fd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="vmware-to-azure-replication-architecture"></a>Arquitetura de replicação de VMware para o Azure

Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de VMs (máquinas virtuais) de VMware entre um site VMware local e o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para replicação do VMware para o Azure.  

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Uma assinatura do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados de VMs locais são colocados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados quando você faz um failover do local para o Azure. As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Uma única VM de VMware é implantada para executar todos os componentes do Site Recovery locais. A VM executa o servidor em processo, o servidor de configuração e o servidor de destino mestre. | O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
 **Servidor de processo**:  | Instalado por padrão juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.<br/><br/> O servidor de processo também instala o serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs em servidores VMware locais.<br/><br/> À medida que a implantação cresce, você pode adicionar outros servidores de processo separados para lidar com volumes maiores de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por padrão juntamente com o servidor de configuração. | Lida com os dados de replicação durante o failback do Azure.<br/><br/> Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback.
**Servidores VMware** | VMs de VMware são hospedadas em servidores ESXi do vSphere locais. Recomendamos um servidor vCenter para gerenciar os hosts. | Durante a implantação do Site Recovery, você pode adicionar servidores VMware no cofre dos Serviços de Recuperação.
**Computadores replicados** | O serviço de Mobilidade será instalado em cada VM de VMware que você replicar. | É recomendável que você permita a instalação automática do servidor em processo. Como alternativa, você pode instalar o serviço manualmente ou usar um método de implantação automatizado como o System Center Configuration Manager. 

**Arquitetura do VMware para o Azure**

![Componentes](./media/concepts-vmware-to-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configure a implantação, incluindo os componentes locais e os componentes do Azure. No cofre dos Serviços de Recuperação, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação e habilita a replicação.
2. Os computadores são replicados de acordo com a política de replicação e uma cópia inicial dos dados de VM é replicada para o armazenamento do Azure.
3. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações acompanhadas para uma máquina são mantidas em um arquivo .hrl.
    - As máquinas virtuais se comunicam com o servidor de configuração na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - As máquinas virtuais enviam dados de replicação para o servidor em processo na porta HTTPS 9443 de entrada (pode ser modificada).
    - O servidor de configuração coordena o gerenciamento de replicação com o Azure pela porta HTTPS 443 de saída.
    - O servidor de processo recebe dados de máquinas de origem, otimiza-os e criptografa-os e os envia para o armazenamento do Azure pela porta 443 de saída.
    - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004. Várias VMS serão usadas se você agrupar vários computadores em grupos de replicação que compartilham pontos de recuperação consistentes com o aplicativo e com falhas ao realizar o failover. Isso é útil se os computadores estão executando a mesma carga de trabalho e precisam ser consistentes.
4. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, você pode usar o [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) do Azure ExpressRoute. Não há suporte para a replicação do tráfego através de uma VPN de site a site de um site local para o Azure.


**Processo de replicação do VMware para o Azure**

![Processo de replicação](./media/concepts-vmware-to-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

Depois que a replicação é configurada e você executou uma simulação de recuperação de desastre (failover de teste) para verificar se tudo está funcionando conforme o esperado, você pode executar failover e failback conforme necessário.

1. Você pode fazer failover de uma única máquina ou criar planos de recuperação para fazer failover de várias VMs.
2. Ao executar um failover, as VMs do Azure são criadas com base nos dados replicados no Armazenamento do Azure.
3. Depois de disparar o failover inicial, você confirma-o para começar a acessar a carga de trabalho da VM do Azure.

Quando o site primário local estiver disponível novamente, você poderá executar o failback.
1. Você precisa configurar uma infraestrutura de failback, incluindo:
    - **Servidor em processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor em processo, a fim de manipular a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
    - **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou Azure ExpressRoute) da rede do Azure para o site local.
    - **Servidor de destino mestre separado**: por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local manipula o failback. No entanto, se você precisar fazer failback de grandes volumes de tráfego, deverá configurar um servidor de destino mestre local separado para essa finalidade.
    - **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. Isso foi criado automaticamente quando você cria sua política de replicação do local para o Azure.
2. Depois que os componentes estão em vigor, o failback ocorre em três estágios:
    - Estágio 1: proteja novamente as VMs do Azure para que elas comecem a replicação para as VMs de VMware locais.
    - Estágio 2: execute um failover para o site local.
    - Estágio 3: depois que as cargas de trabalho tiverem feito failback, você reabilitará a replicação para as VMs locais.

**Failback de VMware do Azure**

![Failback](./media/concepts-vmware-to-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Próximas etapas

Examine a matriz de suporte e siga o tutorial para habilitar o VMware para a replicação do Azure.
Execute um failover e um failback.