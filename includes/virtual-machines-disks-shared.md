---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/14/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 74c77356df4f35461a8b9f1459712cdcf7f77cbf
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564378"
---
Os discos compartilhados do Azure são um novo recurso para discos gerenciados do Azure que permite anexar um disco gerenciado a várias VMs (máquinas virtuais) simultaneamente. Anexar um disco gerenciado a várias VMs permite implantar novos aplicativos clusterizados ou migrar os existentes para o Azure.

## <a name="how-it-works"></a>Como ele funciona

As VMs no cluster podem ler ou gravar no disco anexado com base na reserva escolhida pelo aplicativo clusterizado usando [reservas persistentes de SCSI](https://www.t10.org/members/w_spc3.htm) (PR. SCSI). A PR SCSI é um padrão do setor utilizado por aplicativos executados na Rede de Área de Armazenamento (SAN) local. Habilitar a PR SCSI em um disco gerenciado permite migrar esses aplicativos para o Azure no estado em que se encontram.

Os Managed disks compartilhados oferecem armazenamento de bloco compartilhado que pode ser acessado de várias VMs, eles são expostos como LUNs (números de unidade lógica). Os LUNs são apresentados a um iniciador (VM) de um destino (disco). Esses LUNs são semelhantes ao DAS (armazenamento de conexão direta) ou a uma unidade local para a VM.

Os discos gerenciados compartilhados não oferecem nativamente um sistema de arquivos totalmente gerenciado que pode ser acessado usando SMB/NFS. Você precisa usar um Gerenciador de cluster, como o WSFC (cluster de failover do Windows Server) ou pacemaker, que manipula a comunicação do nó de cluster e o bloqueio de gravação.

## <a name="limitations"></a>Limitações

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

### <a name="operating-system-requirements"></a>Requisitos do sistema operacional

Os discos compartilhados dão suporte a vários sistemas operacionais. Consulte as seções do [Windows](#windows) ou [Linux](#linux) para os sistemas operacionais com suporte.

## <a name="disk-sizes"></a>Tamanhos do disco

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Exemplo de cargas de trabalho

### <a name="windows"></a>Windows

Os discos compartilhados do Azure têm suporte no Windows Server 2008 e mais recentes. A maioria dos clusters baseados em Windows se baseia no WSFC, que lida com toda a infraestrutura básica para comunicação de nó de cluster, permitindo que seus aplicativos aproveitem os padrões de acesso paralelo. O WSFC permite opções de CSV e não baseadas em CSV, dependendo da sua versão do Windows Server. Para obter mais detalhes, consulte [Criar um cluster de failover](/windows-server/failover-clustering/create-failover-cluster).

Alguns aplicativos populares em execução no WSFC incluem:

- [Criar um FCI com discos compartilhados do Azure (SQL Server em VMs do Azure)](../articles/azure-sql/virtual-machines/windows/failover-cluster-instance-azure-shared-disks-manually-configure.md)
- Servidor de arquivos de escalabilidade horizontal (SoFS) [modelo] (https://aka.ms/azure-shared-disk-sofs-template)
- SAP ASCS/SCS [modelo] (https://aka.ms/azure-shared-disk-sapacs-template)
- Servidor de arquivos para uso geral (Carga de trabalho de IW)
- Disco de perfil de usuário do servidor da área de trabalho remota (RDS UPD)

### <a name="linux"></a>Linux

Os discos compartilhados do Azure têm suporte em:
- [SUSE EPU para SAP e SUSE EPU HA 15 SP1 e superior](https://www.suse.com/c/azure-shared-disks-excercise-w-sles-for-sap-or-sle-ha/)
- [Ubuntu 18, 4 e superior](https://discourse.ubuntu.com/t/ubuntu-high-availability-corosync-pacemaker-shared-disk-environments/14874)
- [Versão prévia de desenvolvedor do RHEL em qualquer uma das versões RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/index?lb_target=production#azure-configuring-shared-block-storage_configuring-rhel-high-availability-on-azure)
- [Oracle Enterprise Linux](https://docs.oracle.com/en/operating-systems/oracle-linux/8/availability/hacluster-1.html)

Os clusters do Linux podem utilizar os gerenciadores de cluster, como o [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). O Pacemaker se baseia no [Corosync](http://corosync.github.io/corosync/), o que permite a comunicação de cluster para aplicativos implantados em ambientes altamente disponíveis. Alguns sistemas de arquivos clusterizados comuns incluem [ocfs2](https://oss.oracle.com/projects/ocfs2/) e [gfs2](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2). Você pode usar os modelos de clustering de reserva persistente de SCSI (RP) e/ou SBD (dispositivo de bloco STONITH) para arbitrar o acesso ao disco. Ao usar o SCSI PR, você pode manipular reservas e registros usando utilitários como [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) e [sg_persist](https://linux.die.net/man/8/sg_persist).

## <a name="persistent-reservation-flow"></a>Fluxo de reserva persistente

O diagrama a seguir ilustra um exemplo de aplicativo de banco de dados clusterizado de 2 nós, que utiliza a PR SCSI para habilitar o failover de um nó para o outro.

![Cluster de dois nós. Um aplicativo em execução no cluster está controlando o acesso ao disco](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

O fluxo é da seguinte maneira:

1. O aplicativo clusterizado em execução no Azure VM1 e VM2 registra sua intenção de ler ou gravar no disco.
1. A instância do aplicativo na VM1 usa a reserva exclusiva para gravar no disco.
1. Essa reserva é aplicada ao disco do Azure e agora o banco de dados pode gravar exclusivamente no disco. As gravações da instância do aplicativo na VM2 não serão realizadas com sucesso.
1. Se a instância do aplicativo na VM1 ficar inativa, a instância na VM2 agora poderá iniciar um failover do banco de dados e a tomada do controle do disco.
1. Agora essa reserva é aplicada ao disco do Azure e o disco não aceita mais gravações da VM1. Ele só aceitará gravações da VM2.
1. O aplicativo clusterizado pode concluir o failover do banco de dados e atender às solicitações da VM2.

O diagrama a seguir ilustra outra carga de trabalho clusterizada comum, que consiste em vários nós que leem os dados do disco para executar processos paralelos, como o treinamento de modelos de aprendizado de máquina.

![Cluster de VM de quatro nós. Cada nó registra a intenção de escrever. O aplicativo usa a reserva exclusiva para manipular os resultados de gravação corretamente](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

O fluxo é da seguinte maneira:

1. O aplicativo clusterizado em execução em todas as VMs registra a intenção de ler ou gravar no disco.
1. A instância do aplicativo na VM1 usa uma reserva exclusiva para gravar no disco, ao abrir as leituras no disco de outras VMs.
1. Essa reserva é aplicada ao disco do Azure.
1. Todos os nós no cluster agora podem ler no disco. Somente um nó grava os resultados no disco, em nome de todos os nós no cluster.

### <a name="ultra-disks-reservation-flow"></a>Fluxo de reserva de discos ultra

Os discos ultra oferecem uma restrição adicional, para um total de duas restrições. Em virtude disso, o fluxo de reserva de discos ultra pode funcionar conforme descrito na seção anterior ou pode restringir e distribuir o desempenho de forma mais granular.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-reservation-table.png" alt-text="Uma imagem de uma tabela que descreve o acesso ' ReadOnly ' ou ' leitura/gravação ' para o detentor da reserva, registrado e outros.":::

## <a name="performance-throttles"></a>Acelerações de desempenho

### <a name="premium-ssd-performance-throttles"></a>SSD Premium acelerações de desempenho

Com o SSD Premium, o IOPS de disco e a taxa de transferência são fixos, por exemplo, IOPS de um p30 é 5000. Esse valor permanece se o disco é compartilhado entre duas VMs ou 5 VMs. Os limites de disco podem ser alcançados de uma única VM ou divididas em duas ou mais VMs. 

### <a name="ultra-disk-performance-throttles"></a>Restrições de desempenho de discos ultra

Os discos ultra têm o recurso exclusivo de permitir que você defina seu desempenho, expondo atributos modificáveis e permitindo modificá-los. Por padrão, há apenas dois atributos modificáveis, mas os discos ultra compartilhados têm dois atributos adicionais.


|Atributo  |Descrição  |
|---------|---------|
|DiskIOPSReadWrite     |O número total de IOPS permitido em todas as VMs que montam o disco de compartilhamento com acesso de gravação.         |
|DiskMBpsReadWrite     |A taxa de transferência total (MB/s) permitida em todas as VMs que montam o disco compartilhado com acesso de gravação.         |
|DiskIOPSReadOnly*     |O número total de IOPS permitidos em todas as VMs que montam o disco compartilhado como `ReadOnly` .         |
|DiskMBpsReadOnly*     |A taxa de transferência total (MB/s) permitida em todas as VMs que montam o disco compartilhado como `ReadOnly` .         |

\* Aplica-se somente aos discos ultra compartilhados

As fórmulas a seguir explicam como os atributos de desempenho podem ser definidos, pois são usuários modificáveis:

- DiskIOPSReadWrite/DiskIOPSReadOnly: 
    - Limites de IOPS de 300 IOPS/GiB, até no máximo 160 mil IOPS por disco
    - No mínimo 100 IOPS
    - DiskIOPSReadWrite + DiskIOPSReadOnly tem pelo menos 2 IOPS/GiB
- DiskMBpsRead    Write/DiskMBpsReadOnly:
    - O limite de taxa de transferência de um único disco é de 256 KiB/s para cada IOPS provisionado, até no máximo 2.000 MBps por disco
    - A taxa de transferência mínima garantida por disco é 4KiB/s para cada IOPS provisionado, com uma linha de base geral mínima de 1 MBps

#### <a name="examples"></a>Exemplos

Os exemplos a seguir descrevem alguns cenários que mostram como a restrição pode funcionar com discos ultra compartilhados, especificamente.

##### <a name="two-nodes-cluster-using-cluster-shared-volumes"></a>Cluster de dois nós usando volumes compartilhados do cluster

Veja a seguir um exemplo de um WSFC de dois nós usando volumes compartilhados clusterizados. Com essa configuração, ambas as VMs têm acesso simultâneo de gravação ao disco, o que resulta na `ReadWrite` divisão da limitação entre as duas VMs e a `ReadOnly` limitação não ser usada.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-example.png" alt-text="Exemplo de ultra de dois nós do CSV":::

##### <a name="two-node-cluster-without-cluster-share-volumes"></a>Cluster de dois nós sem volumes de compartilhamento de cluster

Este é um exemplo de um WSFC de 2 nós que não está usando volumes compartilhados clusterizados. Com essa configuração, apenas uma VM tem acesso de gravação no disco. Isso faz com que o `ReadWrite` acelerador esteja sendo usado exclusivamente para a VM primária e a `ReadOnly` limitação somente seja usada pelo secundário.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-two-node-no-csv.png" alt-text="Exemplo de dois nós de CSV sem disco ultra csv":::

##### <a name="four-node-linux-cluster"></a>Cluster Linux de quatro nós

Este é um exemplo de um cluster Linux de 4 nós com um único gravador e três leitores de expansão. Com essa configuração, apenas uma VM tem acesso de gravação no disco. Isso faz com que o `ReadWrite` acelerador esteja sendo usado exclusivamente para a VM primária e o `ReadOnly` acelerador que está sendo dividido pelas VMs secundárias.

:::image type="content" source="media/virtual-machines-disks-shared-disks/ultra-four-node-example.png" alt-text="Exemplo de restrição ultra de quatro nós":::

#### <a name="ultra-pricing"></a>Ultra preços

Os discos ultra compartilhados são cobrados com base na capacidade provisionada, total de IOPS provisionadas (diskIOPSReadWrite + diskIOPSReadOnly) e total de taxa de transferência provisionada (diskMBpsReadWrite + diskMBpsReadOnly). Não há custo adicional para cada montagem de VM adicional. Por exemplo, um disco ultra compartilhado com a seguinte configuração (diskSizeGB: 1024, DiskIOPSReadWrite: 10000, DiskMBpsReadWrite: 600, DiskIOPSReadOnly: 100, DiskMBpsReadOnly: 1) é cobrado com 1024 GiB, 10100 IOPS e 601 MBps, independentemente de ser montado em duas VMs ou cinco VMs.