---
title: Arquitetura para recuperação de desastre do servidor físico para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece uma visão geral dos componentes e da arquitetura usados durante a recuperação de desastres de servidores físicos locais para o Azure com o serviço Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: raynew
ms.openlocfilehash: 99aec3be893693e523dffefbb3c422222ac19a2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60947452"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Servidor físico para a arquitetura de recuperação de desastre do Azure

Este artigo descreve a arquitetura e os processos usados quando você faz a replicação, o failover e a recuperação de servidores Windows e Linux físicos entre um site local e o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela e o gráfico a seguir fornecem uma visão geral dos componentes usados para replicação do servidor físico para o Azure.  

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Uma assinatura do Azure e uma rede do Azure. | Os dados replicados do local máquinas físicas é armazenada no Azure managed disks. As VMs do Azure são criadas com os dados replicados quando você faz um failover do local para o Azure. As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Um único computador físico local ou VM de VMware é implantada para executar todos os componentes do Site Recovery locais. A VM executa o servidor em processo, o servidor de configuração e o servidor de destino mestre. | O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
 **Servidor de processo**:  | Instalado por padrão juntamente com o servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.<br/><br/> O servidor em processo também instala o serviço de Mobilidade nos servidores que você deseja replicar.<br/><br/> À medida que a implantação cresce, você pode adicionar outros servidores de processo separados para lidar com volumes maiores de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por padrão juntamente com o servidor de configuração. | Lida com os dados de replicação durante o failback do Azure.<br/><br/> Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback.
**Servidores replicados** | O serviço de Mobilidade é instalado em cada servidor que você deseja replicar. | É recomendável que você permita a instalação automática do servidor em processo. Como alternativa, você pode instalar o serviço manualmente ou usar um método de implantação automatizado como o System Center Configuration Manager.

**Arquitetura do físico para o Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configure a implantação, incluindo os componentes locais e os componentes do Azure. No cofre dos Serviços de Recuperação, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação e habilita a replicação.
2. As máquinas virtuais são replicadas de acordo com a política de replicação e uma cópia inicial dos dados do servidor é replicada para o armazenamento do Azure.
3. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações acompanhadas para uma máquina são mantidas em um arquivo .hrl.
    - As máquinas virtuais se comunicam com o servidor de configuração na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - As máquinas virtuais enviam dados de replicação para o servidor em processo na porta HTTPS 9443 de entrada (pode ser modificada).
    - O servidor de configuração coordena o gerenciamento de replicação com o Azure pela porta HTTPS 443 de saída.
    - O servidor de processo recebe dados de máquinas de origem, otimiza-os e criptografa-os e os envia para o armazenamento do Azure pela porta 443 de saída.
    - Se você habilitar a consistência de várias VMs, as máquinas virtuais no grupo de replicação se comunicarão entre si pela porta 20004. Várias VMS serão usadas se você agrupar vários computadores em grupos de replicação que compartilham pontos de recuperação consistentes com o aplicativo e com falhas ao realizar o failover. Isso é útil se os computadores estão executando a mesma carga de trabalho e precisam ser consistentes.
4. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, você pode usar o [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#publicpeering) do Azure ExpressRoute. Não há suporte para a replicação do tráfego através de uma VPN de site a site de um site local para o Azure.


**Processo de replicação do físico para o Azure**

![Processo de replicação](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

Depois que a replicação é configurada e você executou uma simulação de recuperação de desastre (failover de teste) para verificar se tudo está funcionando conforme o esperado, você pode executar failover e failback conforme necessário. Observe que:

- Não há suporte para failover planejado.
- Você deve fazer failback para uma VM do VMware local. Isso significa que você precisa de uma infraestrutura VMware local, mesmo ao replicar servidores físicos locais para o Azure.
- Faça failover de uma única máquina ou crie planos de recuperação para fazer failover de várias máquinas virtuais juntas.
- Ao executar um failover, as VMs do Azure são criadas com base nos dados replicados no Armazenamento do Azure.
- Depois de disparar o failover inicial, você confirma-o para começar a acessar a carga de trabalho da VM do Azure.
- Quando o site primário local estiver disponível novamente, você poderá executar o failback.
- Você precisa configurar uma infraestrutura de failback, incluindo:
    - **Servidor de processo temporário no Azure**: Para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor de processo e manipular a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
    - **Conexão VPN**: Para executar failback, você precisa de uma conexão VPN (ou Azure ExpressRoute) da rede do Azure para o site local.
    - **Servidor de destino mestre separado**: Por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local manipula o failback. No entanto, se você precisar fazer failback de grandes volumes de tráfego, deverá configurar um servidor de destino mestre local separado para essa finalidade.
    - **Política de failback**: Para replicar ao site local, você precisará de uma política de failback. Isso foi criado automaticamente quando você cria sua política de replicação do local para o Azure.
    - **Infraestrutura VMware**: Você precisa de uma infraestrutura de VMware para failback. Você não pode realizar o failback para um servidor físico.
- Depois que os componentes estão em vigor, o failback ocorre em três estágios:
    - Etapa 1: Proteja novamente as VMs do Azure para que elas sejam replicadas do Azure de volta para as VMs de VMware locais.
    - Etapa 2: Execute um failover no site local.
    - Etapa 3: Depois que as cargas de trabalho tiverem feito failback, você reabilitará a replicação.

**Failback de VMware do Azure**

![Failback](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Próximas etapas

Siga [este tutorial](physical-azure-disaster-recovery.md) para habilitar a replicação de servidor físico para o Azure.
