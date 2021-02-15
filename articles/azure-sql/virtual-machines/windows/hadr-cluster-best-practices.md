---
title: Práticas recomendadas de configuração de cluster
description: Saiba mais sobre as configurações de cluster com suporte ao configurar a alta disponibilidade e a recuperação de desastres (HADR) para SQL Server em máquinas virtuais do Azure, como quorum com suporte ou opções de roteamento de conexão.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 5a2540aeb36cfcb2048ec994bbb486badc8a68d1
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358802"
---
# <a name="cluster-configuration-best-practices-sql-server-on-azure-vms"></a>Práticas recomendadas de configuração de cluster (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Um cluster é usado para a alta disponibilidade e a recuperação de desastres (HADR) com SQL Server em VMs (máquinas virtuais) do Azure. 

Este artigo fornece as práticas recomendadas de configuração de cluster para [FCIs (instâncias de cluster de failover)](failover-cluster-instance-overview.md) e [grupos de disponibilidade](availability-group-overview.md) ao usá-las com SQL Server em VMs do Azure. 


## <a name="networking"></a>Rede

Use uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física, o que torna as NICs e sub-redes adicionais desnecessárias em um cluster de convidado da máquina virtual do Azure. O relatório de validação de cluster avisará que os nós podem ser acessados apenas em uma rede. Você pode ignorar esse aviso em clusters de failover de convidados da máquina virtual do Azure.

### <a name="tuning-failover-cluster-network-thresholds"></a>Ajustando limites de rede de cluster de failover

Ao executar nós de cluster de failover do Windows em VMs do Azure com o SQL Server AlwaysOn, é recomendável alterar a configuração de cluster para um estado de monitoramento mais relaxado.  Isso tornará o cluster muito mais estável e confiável.  Para obter detalhes sobre isso, consulte [IaaS with SQL AlwaysOn-ajustando limites de rede de cluster de failover](/windows-server/troubleshoot/iaas-sql-failover-cluster).

## <a name="quorum"></a>Quorum

Embora um cluster de dois nós funcione sem um [recurso de quorum](/windows-server/storage/storage-spaces/understand-quorum), os clientes são estritamente obrigados a usar um recurso de quorum para ter suporte de produção. A validação de cluster não passará nenhum cluster sem um recurso de quorum. 

Tecnicamente, um cluster de três nós pode sobreviver a uma única perda de nó (até dois nós) sem um recurso de quorum. Mas, depois que o cluster estiver em dois nós, há um risco de que os recursos clusterizados fiquem offline no caso de uma falha de comunicação ou perda de nó para evitar um cenário de divisão-cérebro.

Configurar um recurso de quorum permitirá que o cluster continue online apenas com um nó online.

A tabela a seguir lista as opções de quorum disponíveis na ordem recomendada para usar com uma VM do Azure, com a testemunha de disco sendo a opção preferida: 


||[Testemunha de disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |[Testemunha da nuvem](/windows-server/failover-clustering/deploy-cloud-witness)  |[Testemunha de compartilhamento de arquivos](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**SO com suporte**| Tudo |Windows Server 2016 e posterior| Tudo|


### <a name="disk-witness"></a>Testemunha de disco

Uma testemunha de disco é um pequeno disco clusterizado no grupo de armazenamento disponível do cluster. Esse disco é altamente disponível e pode fazer failover entre nós. Ele contém uma cópia do banco de dados do cluster, com um tamanho padrão que geralmente é menor que 1 GB. A testemunha de disco é a opção de quorum preferencial para qualquer cluster que usa discos compartilhados do Azure (ou qualquer solução de disco compartilhado, como SCSI compartilhado, iSCSI ou SAN de canal de fibra).  Um volume compartilhado clusterizado não pode ser usado como uma testemunha de disco.

Configure um disco compartilhado do Azure como a testemunha de disco. 

Para começar, consulte [Configurar uma testemunha de disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**SO com suporte**: Todos   


### <a name="cloud-witness"></a>Testemunha da nuvem

Uma testemunha de nuvem é um tipo de testemunha de quorum de cluster de failover que usa Microsoft Azure para fornecer um voto no quorum de cluster. O tamanho padrão é de cerca de 1 MB e contém apenas o carimbo de data/hora. Uma testemunha em nuvem é ideal para implantações em vários sites, zonas múltiplas e várias regiões.

Para começar, consulte [Configurar uma testemunha de nuvem](/windows-server/failover-clustering/deploy-cloud-witness#CloudWitnessSetUp).


**SO com suporte**: Windows Server 2016 e posterior   


### <a name="file-share-witness"></a>Testemunha de compartilhamento de arquivos

Uma testemunha de compartilhamento de arquivos é um compartilhamento de arquivos SMB que normalmente é configurado em um servidor de arquivos que executa o Windows Server. Ele mantém informações de clustering em um arquivo testemunha. log, mas não armazena uma cópia do banco de dados do cluster. No Azure, você pode configurar um compartilhamento de [arquivos do Azure](../../../storage/files/storage-how-to-create-file-share.md) para usar como a testemunha de compartilhamento de arquivos ou pode usar um compartilhamento de arquivos em uma máquina virtual separada.

Se você for usar um compartilhamento de arquivos do Azure, poderá montá-lo com o mesmo processo usado para [montar o compartilhamento de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md#mount-premium-file-share). 

Para começar, consulte [Configurar uma testemunha de compartilhamento de arquivos](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum).


**SO com suporte**: Windows Server 2012 e posterior   

## <a name="connectivity"></a>Conectividade

Em um ambiente de rede local tradicional, um SQL Server instância de cluster de failover parece ser uma única instância do SQL Server em execução em um único computador. Como a instância de cluster de failover faz failover do nó para o nó, o VNN (nome da rede virtual) da instância do fornece um ponto de conexão unificado e permite que os aplicativos se conectem à instância de SQL Server sem saber qual nó está ativo no momento. Quando ocorre um failover, o nome da rede virtual é registrado no novo nó ativo depois que ele é iniciado. Esse processo é transparente para o cliente ou aplicativo que está se conectando ao SQL Server, e isso minimiza o tempo de inatividade que o cliente ou o aplicativo enfrenta durante uma falha. Da mesma forma, o ouvinte do grupo de disponibilidade usa um VNN para rotear o tráfego para a réplica apropriada. 

Use um VNN com Azure Load Balancer ou um DNN (nome de rede distribuída) para rotear o tráfego para o VNN da instância de cluster de failover com SQL Server em VMs do Azure ou para substituir o ouvinte de VNN existente em um grupo de disponibilidade. 


A tabela a seguir compara a suporte à conexão HADR: 

| |**Nome da rede virtual (VNN)**  |**Nome de rede distribuída (DNN)**  |
|---------|---------|---------|
|**Versão mínima do SO**| Todos | Windows Server 2016 |
|**Versão mínima do SQL Server** |Todos |SQL Server 2019 CU2 (para FCI)<br/> SQL Server 2019 CU8 (para AG)|
|**Solução HADR com suporte** | Instância de cluster de failover <br/> grupo de disponibilidade | Instância de cluster de failover <br/> grupo de disponibilidade|


### <a name="virtual-network-name-vnn"></a>VNN (nome da rede virtual)

Como o ponto de acesso IP virtual funciona de modo diferente no Azure, você precisa configurar [Azure Load Balancer](../../../load-balancer/index.yml) para rotear o tráfego para o endereço IP dos nós FCI ou o ouvinte do grupo de disponibilidade. Em máquinas virtuais do Azure, um balanceador de carga mantém o endereço IP para o VNN em que os recursos clusterizados SQL Server dependem. O balanceador de carga distribui os fluxos de entrada que chegam ao front-end e, em seguida, roteia esse tráfego para as instâncias definidas pelo pool de back-end. Você configura o fluxo de tráfego usando regras de balanceamento de carga e investigações de integridade. Com SQL Server FCI, as instâncias de pool de back-end são as máquinas virtuais do Azure em execução SQL Server. 

Há um pequeno atraso de failover quando você está usando o balanceador de carga, pois a investigação de integridade realiza verificações ativas a cada 10 segundos por padrão. 

Para começar, saiba como configurar Azure Load Balancer para instância de [cluster de failover](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou um [grupo de disponibilidade](availability-group-vnn-azure-load-balancer-configure.md)

**SO com suporte**: Todos   
**Versão do SQL com suporte**: Todos   
**Solução HADR com suporte**: instância de cluster de failover e grupo de disponibilidade   


### <a name="distributed-network-name-dnn"></a>DNN (nome da rede distribuída)

O nome da rede distribuída é um novo recurso do Azure para o SQL Server 2019. O DNN fornece uma maneira alternativa para os clientes SQL Server se conectarem à instância de cluster de failover SQL Server ou ao grupo de disponibilidade sem usar um balanceador de carga. 

Quando um recurso DNN é criado, o cluster associa o nome DNS com os endereços IP de todos os nós no cluster. O cliente SQL tentará se conectar a cada endereço IP nesta lista para localizar a qual recurso se conectar.  Você pode acelerar esse processo especificando `MultiSubnetFailover=True` na cadeia de conexão. Essa configuração informa ao provedor para testar todos os endereços IP em paralelo, para que o cliente possa se conectar ao FCI ou ao ouvinte instantaneamente. 

Um nome de rede distribuído é recomendado em um balanceador de carga quando possível porque: 
- A solução de ponta a ponta é mais robusta, pois você não precisa mais manter o recurso de balanceador de carga. 
- A eliminação das investigações do balanceador de carga minimiza a duração do failover. 
- O DNN simplifica o provisionamento e o gerenciamento da instância de cluster de failover ou do ouvinte do grupo de disponibilidade com SQL Server em VMs do Azure. 

A maioria dos recursos de SQL Server funciona de forma transparente com FCI e grupos de disponibilidade ao usar o DNN, mas há alguns recursos que podem exigir consideração especial. Consulte interoperabilidade [FCI e DNN Interoperability](failover-cluster-instance-dnn-interoperability.md) e [AG e DNN](availability-group-dnn-interoperability.md) para saber mais. 

Para começar, saiba como configurar um recurso de nome de rede distribuída para [uma instância de cluster de failover](failover-cluster-instance-distributed-network-name-dnn-configure.md) ou um [grupo de disponibilidade](availability-group-distributed-network-name-dnn-listener-configure.md)

**SO com suporte**: Windows Server 2016 e posterior   
**Versão do SQL com suporte**: SQL Server 2019 Cu2 (FCI) e SQL Server 2019 CU8 (AG)   
**Solução HADR com suporte**: instância de cluster de failover e grupo de disponibilidade   


## <a name="limitations"></a>Limitações

Considere as seguintes limitações quando estiver trabalhando com FCI ou grupos de disponibilidade e SQL Server em máquinas virtuais do Azure. 

### <a name="msdtc"></a>MSDTC 

As Máquinas Virtuais do Azure dão suporte ao MSDTC (Coordenador de Transações Distribuídas da Microsoft) no Windows Server 2019 com armazenamento em CSVs (Volumes Compartilhados Clusterizados) e um [Standard Load Balancer do Azure](../../../load-balancer/load-balancer-overview.md) ou em VMs do SQL Server que estão usando discos compartilhados do Azure. 

Nas Máquinas Virtuais do Azure, o MSDTC não tem suporte para o Windows Server 2016 ou versões anteriores com Volumes Compartilhados Clusterizados porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. No Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver disponível. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.


## <a name="next-steps"></a>Próximas etapas

Depois de determinar as práticas recomendadas apropriadas para sua solução, comece [preparando sua VM SQL Server para FCI](failover-cluster-instance-prepare-vm.md) ou criando seu grupo de disponibilidade usando o [portal do Azure](availability-group-azure-portal-configure.md), os [modelos de início rápido](availability-group-quickstart-template-configure.md)do [CLI do Azure/PowerShell](./availability-group-az-commandline-configure.md)ou do Azure.