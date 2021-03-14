---
title: Criar um FCI com Espaços de Armazenamento Diretos
description: Use Espaços de Armazenamento Diretos para criar uma FCI (instância de cluster de failover) com SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: aa19cf6b59b1efa4b14501fbf64e319da3e4c0b3
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102048634"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Criar um FCI com Espaços de Armazenamento Diretos (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma FCI (instância de cluster de failover) usando [espaços de armazenamento diretos](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) com SQL Server em VMS (máquinas virtuais) do Azure. Espaços de Armazenamento Diretos atua como uma VSAN (rede de área de armazenamento virtual) baseada em software que sincroniza o armazenamento (discos de dados) entre os nós (VMs do Azure) em um cluster do Windows. 

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [práticas recomendadas de cluster](hadr-cluster-best-practices.md). 


## <a name="overview"></a>Visão geral 

[Espaços de armazenamento diretos (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) dá suporte a dois tipos de arquiteturas: convergido e hiperconvergente. Uma infraestrutura hiperconvergente coloca o armazenamento nos mesmos servidores que hospedam o aplicativo clusterizado, para que o armazenamento esteja em cada nó de SQL Server FCI. 

O diagrama a seguir mostra a solução completa, que usa Espaços de Armazenamento Diretos hiperconvergente com SQL Server em VMs do Azure: 

![Diagrama da solução completa, usando hiperconvergentes Espaços de Armazenamento Diretos](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

O diagrama anterior mostra os seguintes recursos no mesmo grupo de recursos:

- Duas máquinas virtuais em um cluster de failover do Windows Server. Quando uma máquina virtual está em um cluster de failover, ela também é chamada de *nó de cluster* ou *nó*.
- Cada máquina virtual tem dois ou mais discos de dados.
- Os Espaços de Armazenamento Diretos sincronizam os dados no disco de dados e apresentam o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta um CSV (Volume Compartilhado Clusterizado) para o cluster de failover.
- A função de cluster FCI do SQL Server usa o CSV para as unidades de dados.
- Um balanceador de carga do Azure para manter o endereço IP para o FCI do SQL Server.
- Um conjunto de disponibilidade do Azure mantém todos os recursos.

   > [!NOTE]
   > Você pode criar essa solução inteira no Azure por meio de um modelo. Um exemplo de um modelo está disponível na página [modelos de início rápido do Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) github. Este exemplo não foi criado nem testado para qualquer carga de trabalho específica. Você pode executar o modelo para criar uma FCI do SQL Server com armazenamento de Espaços de Armazenamento Diretos conectados ao seu domínio. Você pode avaliar o modelo e modificá-lo de acordo com a necessidade.


## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir as instruções neste artigo, você já deve ter:

- Uma assinatura do Azure. Comece [gratuitamente](https://azure.microsoft.com/free/). 
- [Duas ou mais máquinas virtuais do Windows Azure](failover-cluster-instance-prepare-vm.md) preparadas em um [conjunto de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Uma conta que tenha permissões para criar objetos em máquinas virtuais do Azure e no Active Directory.
- A versão mais recente do [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-the-windows-cluster-feature"></a>Adicionar o recurso de cluster do Windows

1. Conecte-se à primeira máquina virtual usando protocolo RDP (RDP) com uma conta de domínio que seja membro dos administradores locais e que tenha permissão para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. Adicionar clustering de failover a cada máquina virtual.

   Para instalar o clustering de failover da interface do usuário, faça o seguinte em ambas as máquinas virtuais:

   1. No **Gerenciador do Servidor**, selecione **Gerenciar** e **Adicionar Funções e Recursos**.
   1. No assistente **adicionar funções e recursos** , selecione **Avançar** até chegar a **selecionar recursos**.
   1. Em **Selecionar Recursos**, escolha **Clustering de Failover**. Inclua todos os recursos e as ferramentas de gerenciamento. 
   1. Selecione **Adicionar Recursos**.
   1. Selecione **Avançar** e **Concluir** para instalar os recursos.

   Para instalar o clustering de failover usando o PowerShell, execute o seguinte script de uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para obter mais informações sobre as próximas etapas, consulte as instruções na seção "etapa 3: configurar Espaços de Armazenamento Diretos" da [solução hiperconvergente usando espaços de armazenamento diretos no Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).


## <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na interface do usuário ou usando o PowerShell.

Para validar o cluster usando a interface do usuário, faça o seguinte em uma das máquinas virtuais:

1. Em **Gerenciador do Servidor**, selecione **Ferramentas** e **Gerenciador de Cluster de Failover**.
1. Em **Gerenciador de Cluster de Failover**, selecione **Ação** e **Validar Configuração**.
1. Selecione **Avançar**.
1. Em **Selecionar Servidores ou um Cluster**, insira o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, selecione **Executar apenas os testes selecionados**. 
1. Selecione **Avançar**.
1. Em **Seleção de Teste**, selecione todos os testes, exceto de **Armazenamento**, conforme mostrado aqui:

   ![Selecionar testes de validação de cluster](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Selecione **Avançar**.
1. Em **Confirmação**, selecione **Avançar**.

    O assistente para **validar uma configuração** executa os testes de validação.

Para validar o cluster usando o PowerShell, execute o seguinte script em uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.


## <a name="create-failover-cluster"></a>Criar cluster de failover

Para criar o cluster de failover, você precisa de:

- Os nomes das máquinas virtuais que se tornarão os nós de cluster.
- Um nome para o cluster de failover.
- Um endereço IP do cluster de failover. Você pode usar um endereço IP que não seja usado na mesma sub-rede e rede virtual do Azure que os nós de cluster.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 - 2016](#tab/windows2012)

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2012 por meio do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2019.  Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Para obter mais informações, confira: [Cluster de failover: objeto de rede de cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Configurar o quorum

Configure a solução de quorum mais adequada às suas necessidades de negócios. Você pode configurar uma testemunha de [disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), uma [testemunha de nuvem](/windows-server/failover-clustering/deploy-cloud-witness)ou uma testemunha de compartilhamento de [arquivos](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Para obter mais informações, consulte [quorum com SQL Server VMs](hadr-cluster-best-practices.md#quorum). 

## <a name="add-storage"></a>Adicionar armazenamento

Os discos para os Espaços de Armazenamento Diretos precisam estar vazios. Eles não podem conter partições nem outros dados. Para limpar os discos, siga as instruções em [implantar espaços de armazenamento diretos](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives).

1. [Habilitar espaços de armazenamento diretos](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O seguinte script do PowerShell habilita os Espaços de Armazenamento Diretos:  

   ```powershell
   Enable-ClusterS2D
   ```

   No **Gerenciador de Cluster de Failover**, agora você pode ver o pool de armazenamento.

1. [Criar um volume](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   Os Espaços de Armazenamento Diretos criam automaticamente um pool de armazenamento quando você os habilita. Agora você está pronto para criar um volume. O cmdlet do PowerShell `New-Volume` automatiza o processo de criação de volume. Esse processo inclui a formatação, a adição do volume ao cluster e a criação de um CSV. Este exemplo cria um CSV de 800 gigabytes (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Depois de executar o comando anterior, um volume de 800 GB é montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\`.

   Esta captura de tela mostra um CSV com Espaços de Armazenamento Diretos:

   ![Captura de tela de um Volume Compartilhado Clusterizado com Espaços de Armazenamento Diretos](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Testar failover de cluster

Teste o failover do cluster. Em **Gerenciador de cluster de failover**, clique com o botão direito do mouse no cluster, selecione **mais ações**  >  **mover recurso de cluster principal**  >  **selecione nó** e, em seguida, selecione o outro nó do cluster. Mova o recurso principal de cluster para cada nó do cluster e mova-o novamente para o nó primário. Se você puder mover o cluster para cada nó com êxito, estará pronto para instalar o SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testar o failover de cluster movendo o recurso principal para os outros nós":::

## <a name="create-sql-server-fci"></a>Criar a FCI do SQL Server

Depois de configurar o cluster de failover e todos os componentes do cluster, incluindo o armazenamento, é possível criar a FCI do SQL Server.

1. Conecte-se à primeira máquina virtual usando o RDP.

1. Em **Gerenciador de cluster de failover**, verifique se todos os recursos de cluster principais estão na primeira máquina virtual. Se necessário, mova todos os recursos para aquela máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. Selecione **instalação**.

1. Na **Central de Instalação do SQL Server**, selecione **Instalação**.

1. Selecione **Nova instalação de cluster de failover do SQL Server**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados de FCI precisam estar no armazenamento de cluster. Com o Espaços de Armazenamento Diretos, ele não é um disco compartilhado, mas um ponto de montagem para um volume em cada servidor. Os Espaços de Armazenamento Diretos sincronizam o volume entre ambos os nós. O volume é apresentado ao cluster como um CSV. Use o ponto de montagem CSV para os diretórios de dados.

   ![Diretórios de dados](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Depois de concluir as instruções no assistente, a instalação instalará um SQL Server FCI no primeiro nó.

1. Depois que a Instalação instalar a FCI com êxito no primeiro nó, conecte-se ao segundo nó usando o RDP.

1. Abra a **Central de Instalação do SQL Server**. Selecione **Instalação**.

1. Selecione **Adicionar um nó a um cluster de failover do SQL Server**. Siga as instruções no assistente para instalar o SQL Server e adicionar o servidor à FCI.

   >[!NOTE]
   >Se você usou uma imagem da galeria do Azure Marketplace que contém o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se você não usou uma dessas imagens, instale as ferramentas do SQL Server separadamente. Para obter mais informações, consulte [Baixar o SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms).
   >


## <a name="register-with-the-sql-vm-rp"></a>Registrar com a VM do SQL RP

Para gerenciar sua VM SQL Server no portal, registre-a com a extensão do SQL IaaS Agent (RP) no [modo de gerenciamento leve](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), atualmente o único modo com suporte com FCI e SQL Server em VMs do Azure. 


Registrar uma VM SQL Server no modo leve com o PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurar a conectividade 

Para rotear o tráfego adequadamente para o nó primário atual, configure a opção de conectividade adequada para o seu ambiente. Você pode criar um [balanceador de carga do Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou, se você estiver usando SQL Server 2019 Cu2 (ou posterior) e o Windows Server 2016 (ou posterior), poderá usar o recurso de [nome de rede distribuída](failover-cluster-instance-distributed-network-name-dnn-configure.md) em vez disso. 

Para obter mais detalhes sobre as opções de conectividade de cluster, confira [Rotear conexões HADR para o SQL Server em VMs do Azure](hadr-cluster-best-practices.md#connectivity). 

## <a name="limitations"></a>Limitações

- As máquinas virtuais do Azure dão suporte ao Microsoft Coordenador de Transações Distribuídas (MSDTC) no Windows Server 2019 com armazenamento no CSVs e um [balanceador de carga padrão](../../../load-balancer/load-balancer-overview.md).
- Os discos que foram anexados como discos formatados com NTFS podem ser usados com Espaços de Armazenamento Diretos somente se a opção de qualificação de disco estiver desmarcada ou desmarcada quando o armazenamento estiver sendo adicionado ao cluster. 
- Há suporte apenas para o registro com a extensão do SQL IaaS Agent no [modo de gerenciamento leve](sql-server-iaas-agent-extension-automate-management.md#management-modes) .

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, configure a conectividade com o FCI com um [nome de rede virtual e um balanceador de carga do Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou [DNN (nome de rede distribuída)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Se Espaços de Armazenamento Diretos não for a solução de armazenamento FCI apropriada para você, considere criar seu FCI usando os [discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) ou os [compartilhamentos de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md) em vez disso. 

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [práticas recomendadas de configuração de cluster](hadr-cluster-best-practices.md). 

Para obter mais informações, consulte: 
- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instâncias de cluster de failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)