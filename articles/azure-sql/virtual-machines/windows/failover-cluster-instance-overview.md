---
title: Instâncias de cluster de failover
description: Saiba mais sobre as FCIs (instâncias de cluster de failover) com o SQL Server nas Máquinas Virtuais do Microsoft Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: a7735de9763f3924cd6baae6af1258f6448c874e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690916"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Instâncias de cluster de failover com o SQL Server nas Máquinas Virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo apresenta as diferenças de recursos quando você está trabalhando com FCIs (instâncias de cluster de failover) para o SQL Server em VMs (máquinas virtuais) do Azure. 

## <a name="overview"></a>Visão geral

O SQL Server em VMs do Azure usa a funcionalidade de WSFC (Windows Server Failover Clustering) para fornecer alta disponibilidade local por meio de redundância na instância de nível de servidor: uma instância de cluster de failover. Uma FCI é uma instância única do SQL Server instalada em nós do WSFC (ou simplesmente do cluster) e, possivelmente, em várias sub-redes. Na rede, uma FCI parece ser uma instância do SQL Server em execução em um só computador. No entanto, a FCI fornece failover de um nó do WSFC para outro quando o nó atual fica indisponível.

O restante do artigo se concentra nas diferenças das instâncias de cluster de failover quando elas são usadas com o SQL Server em VMs do Azure. Para saber mais sobre a tecnologia de clustering de failover, confira: 

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de cluster de failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

As instâncias de cluster de failover com o SQL Server nas Máquinas Virtuais do Azure dão suporte ao uso de uma testemunha de disco, uma testemunha de nuvem ou uma testemunha de compartilhamento de arquivo para quorum do cluster.

Para saber mais, confira [Melhores práticas de quorum com VMs do SQL Server no Azure](hadr-cluster-best-practices.md#quorum). 


## <a name="storage"></a>Armazenamento

Em ambientes clusterizados tradicionais locais, um cluster de failover do Windows usa uma SAN (rede de área de armazenamento) que é acessível por ambos os nós como o armazenamento compartilhado. Os arquivos do SQL Server são hospedados no armazenamento compartilhado, e somente o nó ativo pode acessar os arquivos. 

O SQL Server nas VMs do Azure oferece várias opções como solução de armazenamento compartilhado para uma implantação de instâncias de cluster de failover do SQL Server: 

||[Discos compartilhados do Azure](../../../virtual-machines/disks-shared.md)|[Compartilhamentos de arquivos Premium](../../../storage/files/storage-how-to-create-file-share.md) |[S2D (Espaços de Armazenamento Diretos)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**Versão mínima do SO**| Todos |Windows Server 2012|Windows Server 2016|
|**Versão mínima do SQL Server**|Todos|SQL Server 2012|SQL Server 2016|
|**Disponibilidade de VM com suporte** |Conjuntos de disponibilidade com grupos de posicionamento por proximidade (para SSD Premium) </br> Mesma zona de disponibilidade (para SSD Ultra) |Conjuntos de disponibilidade e zonas de disponibilidade|Conjuntos de disponibilidade |
|**Dá suporte ao FileStream**|Sim|Não|Sim |
|**Cache de blob do Azure**|Não|Não|Sim|

O restante desta seção lista os benefícios e as limitações de cada opção de armazenamento disponível para o SQL Server nas VMs do Azure. 

### <a name="azure-shared-disks"></a>Discos compartilhados do Azure

Os [discos compartilhados do Azure](../../../virtual-machines/disks-shared.md) são um recurso dos [discos gerenciados do Azure](../../../virtual-machines/managed-disks-overview.md). O Clustering de failover do Windows Server dá suporte ao uso de discos compartilhados do Azure com uma instância de cluster de failover. 

**SO com suporte**: Todos   
**Versão do SQL com suporte**: Todos     

**Benefícios:** 
- útil para aplicativos que buscam migrar para o Azure mantendo sua arquitetura de HADR (alta disponibilidade e recuperação de desastres) como está. 
- Pode migrar aplicativos em cluster para o Azure como estão devido ao suporte para SCSI PR (reservas persistentes de SCSI). 
- Dá suporte ao SSD Premium do Azure compartilhado e ao armazenamento de Disco Ultra do Azure.
- Pode usar um só disco compartilhado ou distribuir vários discos compartilhados para criar um pool de armazenamento compartilhado. 
- Dá suporte ao FileStream.
- Conjuntos de disponibilidade de suporte de SSDs Premium. 


**Limitações**: 
- é recomendável colocar as máquinas virtuais no mesmo conjunto de disponibilidade e no mesmo grupo de posicionamento por proximidade.
- Discos Ultra não dão suporte a conjuntos de disponibilidade. 
- As zonas de disponibilidade têm suporte para Discos Ultra, mas as VMs precisam estar na mesma zona de disponibilidade, o que reduz a disponibilidade da máquina virtual. 
- Independentemente da solução de disponibilidade de hardware escolhida, a disponibilidade do cluster de failover sempre será de 99,9% quando usar os Discos Compartilhados do Azure. 
- Não há suporte para o cache de disco SSD Premium.

 
Para começar, confira [Instância de cluster de failover do SQL Server com discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). 

### <a name="storage-spaces-direct"></a>Espaços de Armazenamento Direct

Os [Espaços de Armazenamento Diretos](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) são um recurso do Windows Server que têm suporte com clustering de failover nas Máquinas Virtuais do Azure. Ele fornece uma SAN virtual baseada em software.

**SO com suporte**: Windows Server 2016 e posterior   
**Versão do SQL com suporte**: SQL Server 2016 e posterior   


**Benefícios:** 
- Largura de banda de rede suficiente habilita uma solução de armazenamento compartilhado robusta e altamente funcional. 
- Dá suporte ao cache de blob do Azure, portanto, as leituras podem ser servidas localmente do cache. (As atualizações são replicadas simultaneamente para ambos os nós.) 
- Dá suporte ao FileStream. 

**Limitações:**
- disponível somente para o Windows Server 2016 e posterior. 
- Não há suporte para zonas de disponibilidade.
- Requer a mesma capacidade de disco anexada às duas máquinas virtuais. 
- Alta largura de banda de rede é necessária para alcançar alto desempenho devido à replicação em disco contínua. 
- Requer um tamanho de VM maior e pagamento duplo pelo armazenamento, pois o armazenamento é anexado a cada VM. 

Para começar, confira [Instância de cluster de failover do SQL Server com os Espaços de Armazenamento Diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 

### <a name="premium-file-share"></a>Compartilhamento de arquivo Premium

Os [compartilhamentos de arquivos Premium](../../../storage/files/storage-how-to-create-file-share.md) são um recurso dos [Arquivos do Azure](../../../storage/files/index.yml). Os compartilhamentos de arquivos Premium têm suporte de SSD e têm baixa latência de maneira consistente. Eles têm suporte total para uso com as instâncias de cluster de failover para o SQL Server 2012 ou posterior no Windows Server 2012 ou posterior. Os compartilhamentos de arquivo Premium proporcionam maior flexibilidade, porque você pode redimensionar e escalar um compartilhamento de arquivo sem nenhum tempo de inatividade.

**SO com suporte**: Windows Server 2012 e posterior   
**Versão do SQL com suporte**: SQL Server 2012 e posterior   

**Benefícios:** 
- única solução de armazenamento compartilhado para máquinas virtuais espalhadas em várias zonas de disponibilidade. 
- Sistema de arquivos totalmente gerenciado com latências de dígito único e desempenho de E/S com capacidade de intermitência. 

**Limitações:**
- disponível somente para o Windows Server 2012 e posterior. 
- Não há suporte para FileStream. 


Para começar, confira [Instância de cluster de failover do SQL Server com um compartilhamento de arquivo Premium](failover-cluster-instance-premium-file-share-manually-configure.md). 

### <a name="partner"></a>Parceiro

Há soluções de clustering de parceiros com armazenamento com suporte. 

**SO com suporte**: Todos   
**Versão do SQL com suporte**: Todos   

Um exemplo usa o SIOS DataKeeper como armazenamento. Para obter mais informações, confira a entrada de blog [Clustering de failover e SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).

### <a name="iscsi-and-expressroute"></a>iSCSI e ExpressRoute

Você também pode expor um armazenamento em bloco compartilhado de destino iSCSI por meio do Azure ExpressRoute. 

**SO com suporte**: Todos   
**Versão do SQL com suporte**: Todos   

Por exemplo, o NPS (Armazenamento Privado do NetApp) expõe um destino iSCSI por meio do ExpressRoute com o Equinix para VMs do Azure.

Para soluções de replicação de dados e armazenamento compartilhado de parceiros da Microsoft, contate o fornecedor para solucionar problemas relacionados ao acesso a dados no failover.

## <a name="connectivity"></a>Conectividade

As instâncias de cluster de failover com o SQL Server nas Máquinas Virtuais do Azure usam um [DNN (nome de rede distribuída)](failover-cluster-instance-distributed-network-name-dnn-configure.md) ou um [VNN (nome de rede virtual) com o Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) para rotear o tráfego para a instância do SQL Server, independentemente de qual nó tem os recursos em cluster. Há considerações adicionais ao usar determinados recursos e o DNN com uma FCI do SQL Server. Confira [Interoperabilidade de DNN com a FCI do SQL Server](failover-cluster-instance-dnn-interoperability.md) para saber mais. 

Para obter mais detalhes sobre as opções de conectividade de cluster, confira [Rotear conexões HADR para o SQL Server em VMs do Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitações

Considere as limitações a seguir das instâncias de cluster de failover com o SQL Server nas Máquinas Virtuais do Azure. 

### <a name="lightweight-extension-support"></a>Suporte de extensão leve   

No momento, as instâncias de cluster de failover do SQL Server em máquinas virtuais do Azure têm suporte somente com o [modo de gerenciamento leve](sql-server-iaas-agent-extension-automate-management.md#management-modes) da Extensão SQL Server IaaS Agent. Para alterar do modo de extensão completo para leve, exclua o recurso **máquina virtual do SQL** das VMs correspondentes e registre-as na extensão SQL IaaS Agent no modo leve. Ao excluir o recurso **máquina virtual do SQL** usando o portal do Azure, desmarque a caixa de seleção ao lado da máquina virtual correta para evitar a exclusão da máquina virtual. 

A extensão completa dá suporte a recursos como backup automatizado, aplicação de patch e gerenciamento avançado do portal. Esses recursos não funcionarão para VMs do SQL Server registradas no modo de gerenciamento leve.

### <a name="msdtc"></a>MSDTC 

As Máquinas Virtuais do Azure dão suporte ao MSDTC (Coordenador de Transações Distribuídas da Microsoft) no Windows Server 2019 com armazenamento em CSVs (Volumes Compartilhados Clusterizados) e um [Standard Load Balancer do Azure](../../../load-balancer/load-balancer-overview.md) ou em VMs do SQL Server que estão usando discos compartilhados do Azure. 

Nas Máquinas Virtuais do Azure, o MSDTC não tem suporte para o Windows Server 2016 ou versões anteriores com Volumes Compartilhados Clusterizados porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. No Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver disponível. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.


## <a name="next-steps"></a>Próximas etapas

Examine as [melhores práticas de configuração de cluster](hadr-cluster-best-practices.md) para [preparar sua VM do SQL Server para a FCI](failover-cluster-instance-prepare-vm.md). 

Para obter mais informações, consulte: 

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instâncias de cluster de failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)