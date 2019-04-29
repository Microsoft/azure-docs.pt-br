---
title: Proteger um servidor de arquivos usando o Azure Site Recovery
description: Este artigo descreve como proteger um servidor de arquivos usando o Azure Site Recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.custom: mvc
ms.openlocfilehash: 51754021f5029a751be90bfc4194ac6347c1e278
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772122"
---
# <a name="protect-a-file-server-by-using-azure-site-recovery"></a>Proteger um servidor de arquivos usando o Azure Site Recovery 

O [Azure Site Recovery](site-recovery-overview.md) contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) mantendo seus aplicativos de negócios em execução durante interrupções planejadas e não planejadas. O Site Recovery gerencia e orquestra a recuperação de desastre de máquinas locais e de VMs (máquinas virtuais) do Azure. A recuperação de desastres inclui replicação, failover e recuperação de diversas cargas de trabalho.

Este artigo descreve como proteger um servidor de arquivos usando o Site Recovery e dá outras recomendações para atender a vários ambientes. 

- [Replicar computadores de servidor de arquivos IaaS do Azure](#disaster-recovery-recommendation-for-azure-iaas-virtual-machines)
- [Replicar um servidor de arquivos local usando o Site Recovery](#replicate-an-on-premises-file-server-by-using-site-recovery)

## <a name="file-server-architecture"></a>Arquitetura do servidor de arquivos
O objetivo de um sistema de compartilhamento de arquivos distribuído é oferecer um ambiente em que um grupo de usuários distribuídos geograficamente possa colaborar para trabalhar eficientemente em arquivos e ter a certeza de que seus requisitos de integridade serão impostos. Um ecossistema de servidor de arquivos local típico que dá suporte a um grande número de usuários simultâneos e a um grande número de itens de conteúdo usa DFSR (Replicação de Sistema de Arquivos Distribuído) para programação de replicação e limitação de largura de banda. 

A DFSR usa um algoritmo de compactação conhecido como RDC (Compactação Diferencial Remota) que pode ser usado para atualizar com eficiência os arquivos em uma rede com largura de banda limitada. Ele detecta inserções, remoções e reorganizações de dados em arquivos. A DFSR está habilitada para replicar apenas os blocos de arquivo alterados quando os arquivos são atualizados. Também existem ambientes de servidor de arquivos em que os backups diários são feitos em intervalos fora de pico, que atendem às necessidades de desastre. A DFSR não está implementada.

O diagrama a seguir ilustra o ambiente de servidor de arquivos com DFSR implementada.
                
![Arquitetura de DFSR](media/site-recovery-file-server/dfsr-architecture.JPG)

No diagrama anterior, vários servidores de arquivos chamados de membros participam ativamente da replicação de arquivos em um grupo de replicação. O conteúdo na pasta replicada está disponível para todos os clientes que enviam solicitações para um dos membros, mesmo que um dos membros fique offline.

## <a name="disaster-recovery-recommendations-for-file-servers"></a>Recomendações de recuperação de desastre para servidores de arquivos

* **Replicar um servidor usando o Site Recovery**: Servidores de arquivos podem ser replicados para o Azure usando o Site Recovery. Quando um ou mais servidores de arquivos locais estiverem inacessíveis, as VMs de recuperação podem ser ativadas no Azure. As VMs podem então atender a solicitações de clientes, no local, desde que haja conectividade VPN site a site e que o Active Directory esteja configurado no Azure. Você pode usar esse método em caso de um ambiente com DFSR configurada ou em um ambiente de servidor de arquivos simples sem DFSR. 

* **Estender a DFSR para uma VM IaaS do Azure**: Em um ambiente de servidor de arquivos em cluster com DFSR implementada, você pode estender a DFSR local ao Azure. Uma VM do Azure é habilitada para executar a função de servidor de arquivos. 

    * Depois que as dependências de conectividade VPN site a site e o Active Directory são tratadas e a DFSR está ativa, quando um ou mais os servidores de arquivo no local ficam inacessíveis, os clientes podem se conectar à VM do Azure, que atende às solicitações.

    * Você pode usar essa abordagem se suas VMs têm configurações sem suporte no Site Recovery. Um exemplo é um disco de cluster compartilhado, que às vezes é comumente usado em ambientes de servidor de arquivos. A DFSR também funciona bem em ambientes de baixa largura de banda com taxa de cancelamento média. Você precisa considerar o custo adicional de ter uma VM do Azure em execução o tempo todo. 

* **Use a Sincronização de Arquivos para replicar seus arquivos**: Se você planeja usar a nuvem ou já usa uma VM do Azure, você pode usar a Sincronização de Arquivos. A Sincronização de Arquivos do Azure oferece sincronização de compartilhamentos de arquivos completamente gerenciados na nuvem que são acessíveis via o protocolo padrão da indústria [Bloquear Mensagem de Servidor](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) (SMB). Os compartilhamentos de Arquivos do Azure podem ser montados de maneira simultânea por implantações locais ou na nuvem do Windows, do Linux e do MacOS. 

O diagrama a seguir o ajudará a determinar qual estratégia deve ser usada para o seu ambiente de servidor de arquivos.

![Árvore de decisão](media/site-recovery-file-server/decisiontree.png)


### <a name="factors-to-consider-in-your-decisions-about-disaster-recovery-to-azure"></a>Fatores a considerar ao decidir sobre a recuperação de desastre no Azure

|Ambiente  |Recomendações  |Considere o seguinte |
|---------|---------|---------|
|Ambiente de servidor de arquivos com ou sem DFSR|   [Usar o Site Recovery para replicação](#replicate-an-on-premises-file-server-by-using-site-recovery)   |    O Site Recovery não oferece suporte a clusters de discos compartilhados ou armazenamento anexado à rede (NAS). Se seu ambiente usa essas configurações, use um dos outros métodos, conforme apropriado. <br> O Site Recovery não oferece suporte a SMB 3.0. A VM replicada incorpora as alterações somente quando as alterações feitas nos arquivos são atualizadas no local original dos arquivos.
|Ambiente de servidor de arquivos com DFSR     |  [Estender a DFSR para uma máquina virtual IaaS do Azure](#extend-dfsr-to-an-azure-iaas-virtual-machine)  |      A DFSR funciona bem em ambientes de largura de banda extremamente fragmentada. Essa abordagem requer uma VM do Azure que esteja em execução o tempo todo. Você precisa levar em conta o custo da VM no planejamento.         |
|VM IaaS do Azure     |     Sincronização de Arquivos    |     Se você usar a Sincronização de Arquivos em um cenário de recuperação de desastre, durante o failover, você deve realizar ações manuais para certificar-se de que os compartilhamentos de arquivos podem ser acessados pelo computador cliente de forma transparente. A Sincronização de Arquivos requer que a porta 445 esteja aberta no computador cliente.     |


### <a name="site-recovery-support"></a>Suporte do Site Recovery
Como a replicação do Site Recovery é independente do aplicativo, estas recomendações devem servir também para os cenários a seguir.

| Fonte    |Para um site secundário    |Para o Azure
|---------|---------|---------|
|Azure| -|Sim|
|Hyper-V|   Sim |Sim
|VMware |Sim|   Sim
|Servidor físico|   Sim |Sim
 

> [!IMPORTANT]
> Antes de continuar com uma das três abordagens a seguir, verifique se as estas dependências serão atendidas.

**Conectividade site a site**: a conexão direta entre o site local e a rede do Azure deve ser estabelecida para permitir a comunicação entre servidores. Use uma conexão de VPN site a site segura a uma rede virtual do Azure que seja usada como local de recuperação de desastres. Para obter mais informações, consulte [Estabelecer uma conexão de VPN site a site entre um site local e uma rede virtual do Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Active Directory**: A DFSR depende do Active Directory. Isso significa que a floresta do Active Directory com controladores de domínio locaia é estendida para o local de recuperação de desastre no Azure. Mesmo se você não estiver usando a DFSR, se os usuários pretendidos precisam obter acesso ou ser verificados para o acesso, você deve realizar essas etapas. Para obter mais informações, consulte [Estender o Active Directory no local para o Azure](https://docs.microsoft.com/azure/site-recovery/site-recovery-active-directory).

## <a name="disaster-recovery-recommendation-for-azure-iaas-virtual-machines"></a>Recomendação de recuperação de desastre para máquinas virtuais IaaS do Azure

Se você estiver configurando e gerenciando a recuperação de desastre de servidores de arquivos hospedados em VMs IaaS do Azure, poderá escolher entre duas opções, dependendo da sua vontade de migrar para os [Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction):

* [Usar a Sincronização de Arquivos](#use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine)
* [Usar o Site Recovery](#replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery)

## <a name="use-file-sync-to-replicate-files-hosted-on-an-iaas-virtual-machine"></a>Usar a Sincronização de Arquivos para replicar arquivos hospedados em uma máquina virtual IaaS

Os Arquivos do Azure podem ser usados para substituir completamente ou complementar os servidores de arquivos tradicionais no local ou dispositivos NAS. Os compartilhamentos dos arquivos do Azure também podem ser replicados com a Sincronização de Arquivos para Windows Servers, no local ou na nuvem, para armazenamento dos dados em cache distribuído e com desempenho onde são usados. As etapas a seguir descrevem a recomendação de recuperação de desastre para VMs do Azure que executam a mesma funcionalidade que os servidores de arquivos tradicionais:
* Proteger máquinas por meio do Site Recovery. Sigas as etapas em [Replicar uma VM do Azure para outra região do Azure](azure-to-azure-quickstart.md).
* Use a Sincronização de Arquivos para replicar arquivos da VM que atua como servidor de arquivos para a nuvem.
* Use o recurso de [plano de recuperação](site-recovery-create-recovery-plans.md) do Site Recovery para adicionar scripts a fim de [montar o compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e acessar o compartilhamento em sua máquina virtual.

As etapas a seguir descrevem brevemente como usar a Sincronização de Arquivos:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se você tiver escolhido armazenamento com redundância geográfica com acesso de leitura para suas contas de armazenamento, terá acesso de leitura aos dados da região secundária em caso de desastres. Para obter mais informações, confira [Recuperação de desastre e failover forçado (versão prévia) no Armazenamento do Azure](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Crie um compartilhamento de arquivos](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Inicie a sincronização de arquivos](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no servidor de arquivos do Azure.
4. Criar um grupo de sincronização. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Um grupo de sincronização deve conter pelo menos um ponto de extremidade de nuvem, que representa um compartilhamento de arquivos do Azure. Um grupo de sincronização também deve conter um ponto de extremidade de servidor, que representa um caminho em um servidor Windows.
5. Os arquivos agora são mantidos em sincronia entre o compartilhamento de arquivos do Azure e seu servidor local.
6. Em caso de desastre no seu ambiente local, execute um failover usando um [plano de recuperação](site-recovery-create-recovery-plans.md). Adicione o script para [montar o compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) e acessar o compartilhamento em sua máquina virtual.

### <a name="replicate-an-iaas-file-server-virtual-machine-by-using-site-recovery"></a>Replicar uma máquina virtual de servidor de arquivos IaaS usando o Site Recovery

Se você tiver clientes locais que acessam a máquina virtual do servidor de arquivos de IaaS, realize todas as seguintes etapas. Caso contrário, pule para a Etapa 3.

1. Estabelecer uma conexão VPN site a site entre o site local e a rede do Azure.
2. Estender o Active Directory local.
3. [Configurar a recuperação de desastre](azure-to-azure-tutorial-enable-replication.md) para o computador do servidor de arquivos IaaS em uma região secundária.


Para saber mais sobre recuperação de desastre em uma região secundária, consulte [este artigo](concepts-azure-to-azure-architecture.md).


## <a name="replicate-an-on-premises-file-server-by-using-site-recovery"></a>Replicar um servidor de arquivos local usando o Site Recovery

As etapas a seguir descrevem a replicação para uma VM do VMware. Para conferir as etapas para replicar uma VM do Hyper-V, consulte [este tutorial](tutorial-hyper-v-to-azure.md).

1. [Preparar recursos do Azure](tutorial-prepare-azure.md) para replicação de máquinas locais.
2. Estabelecer uma conexão VPN site a site entre o site local e a rede do Azure. 
3. Estender o Active Directory local.
4. [Preparar servidores VMware locais](tutorial-prepare-on-premises-vmware.md).
5. [Configurar a recuperação de desastre](tutorial-vmware-to-azure.md) de VMs locais para o Azure.

## <a name="extend-dfsr-to-an-azure-iaas-virtual-machine"></a>Estender a DFSR para uma máquina virtual IaaS do Azure

1. Estabelecer uma conexão VPN site a site entre o site local e a rede do Azure. 
2. Estender o Active Directory local.
3. [Criar e provisionar uma VM do servidor de arquivos](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) na rede virtual do Azure.
Adicione a máquina virtual à mesma rede virtual do Azure que tem conexão cruzada com o ambiente local. 
4. Instalar e [configurar a DFSR](https://blogs.technet.microsoft.com/b/filecab/archive/2013/08/21/dfs-replication-initial-sync-in-windows-server-2012-r2-attack-of-the-clones.aspx) no Windows Server.
5. [Implementar um namespace do DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/deploying-dfs-namespaces).
6. Com o namespace do DFS implementado, o failover das pastas compartilhadas de produção para sites de recuperação de desastre pode ser feito atualizando os destinos de pasta do namespace do DFS. Depois que essas alterações de namespace do DFS replicam por meio do Active Directory, os usuários estão conectados aos destinos de pasta apropriada de forma transparente.

## <a name="use-file-sync-to-replicate-your-on-premises-files"></a>Use a Sincronização de Arquivos para replicar seus arquivos locais
Você pode usar a Sincronização de Arquivos para replicar arquivos para a nuvem. Em caso de desastre e indisponibilidade do servidor de arquivos local, você pode montar os locais de arquivo desejados da nuvem e continuar a atender a solicitações dos computadores clientes.
Para integrar a Sincronização de Arquivos com o Site Recovery:

* Proteger máquinas do servidor de arquivos por meio do Site Recovery. Siga as etapas [deste tutorial](tutorial-vmware-to-azure.md).
* Use a Sincronização de Arquivos para replicar arquivos do computador que atua como um servidor de arquivos para a nuvem.
* Use o recurso de plano de recuperação no Site Recovery para adicionar scripts a fim de montar o compartilhamento de arquivos do Azure na VM do servidor de arquivos com failover no Azure.

Siga estas etapas para usar a Sincronização de Arquivos:

1. [Criar uma conta de armazenamento no Azure](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Se você escolheu armazenamento com redundância geográfica com acesso de leitura (recomendado) para suas contas de armazenamento, terá acesso de leitura aos dados da região secundária em caso de desastre. Para obter mais informações, confira [Recuperação de desastre e failover forçado (versão prévia) no Armazenamento do Azure](../storage/common/storage-disaster-recovery-guidance.md?toc=%2fazure%2fstorage%2ffiless%2ftoc.json).
2. [Crie um compartilhamento de arquivos](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).
3. [Implantar a Sincronização de Arquivos](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide) no seu servidor de arquivos local.
4. Criar um grupo de sincronização. Os pontos de extremidade em um grupo de sincronização são mantidos em sincronização entre si. Um grupo de sincronização deve conter pelo menos um ponto de extremidade de nuvem, que representa um compartilhamento de arquivos do Azure. O grupo de sincronização também deve conter um ponto de extremidade de servidor, que representa um caminho em um servidor Windows no local.
5. Os arquivos agora são mantidos em sincronia entre o compartilhamento de arquivos do Azure e seu servidor local.
6. Em caso de desastre no seu ambiente local, execute um failover usando um [plano de recuperação](site-recovery-create-recovery-plans.md). Adicione o script para montar o compartilhamento de arquivos do Azure e acessar o compartilhamento em sua máquina virtual.

> [!NOTE]
> Certifique-se de que a porta 445 esteja aberta. Os arquivos do Azure usam o protocolo SMB. SMB se comunica pela porta TCP 445. Verifique se o firewall não está bloqueando a porta TCP 445 em um computador cliente.


## <a name="do-a-test-failover"></a>Execute um teste de failover

1. Acesse o Portal do Azure e selecione seu cofre do Serviço de Recuperação.
2. Selecione o plano de recuperação criado para o ambiente de servidor de arquivos.
3. Selecione **Failover de Teste**.
4. Selecione o ponto de recuperação e a rede virtual do Azure para iniciar o processo de failover de teste.
5. Depois que o ambiente secundário estiver funcionando, execute as validações.
6. Depois que as validações forem concluídas, selecione **Limpar failover de teste** no plano de recuperação e o ambiente de failover de teste será limpo.

Para obter mais informações sobre como executar um failover de teste, consulte [Failover de teste no Site Recovery](site-recovery-test-failover-to-azure.md).

Para obter diretrizes sobre como fazer failover de teste em Active Directory e DNS, consulte [Considerações sobre failover de teste em AD e DNS](site-recovery-active-directory.md).

## <a name="do-a-failover"></a>Execute um failover

1. Vá para o Portal do Azure e selecione seu cofre dos Serviços de Recuperação.
2. Selecione o plano de recuperação criado para o ambiente de servidor de arquivos.
3. Selecione **Failover**.
4. Selecione o ponto de recuperação para iniciar o processo de failover.

Para obter mais informações sobre como fazer um failover, consulte [Failover no Site Recovery](site-recovery-failover.md).
