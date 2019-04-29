---
title: FCI do SQL Server - máquinas virtuais do Azure | Microsoft Docs
description: Este artigo explica como criar a instância de Cluster de Failover do SQL Server em Máquinas Virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: a758cce85645e72bfd9434a69393133d3da6b57d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60591555"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurar a instância de Cluster de Failover do SQL Server em máquinas virtuais do Azure

Este artigo explica como criar uma FCI (Instância de Cluster de Failover) do SQL Server em máquinas virtuais do Azure no modelo do Gerenciador de Recursos. Esta solução usa [Espaços de Armazenamento Direto do Windows Server 2016 Datacenter Edition \(S2D\) ](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como uma SAN virtual baseada em software que sincroniza o armazenamento (discos de dados) entre os nós (VMs do Azure) em um Cluster do Windows. S2D é novo no Windows Server 2016.

O diagrama a seguir mostra a solução completa em máquinas virtuais do Azure:

![Grupo de disponibilidade](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

O diagrama acima mostra:

- Duas máquinas virtuais do Azure em um Cluster de Failover do Windows. Quando uma máquina virtual está em um cluster de failover, ela também é chamada de *nó de cluster* ou *nós*.
- Cada máquina virtual tem dois ou mais discos de dados.
- O S2D sincroniza os dados no disco de dados e apresenta o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta um CSV (volume compartilhado clusterizado) para o cluster de failover.
- A função de cluster FCI do SQL Server usa o CSV para as unidades de dados.
- Um balanceador de carga do Azure para manter o endereço IP para o FCI do SQL Server.
- Um conjunto de disponibilidade do Azure mantém todos os recursos.

   >[!NOTE]
   >Todos os recursos do Azure que estão no diagrama estão no mesmo grupo de recursos.

Para obter detalhes sobre S2D, confira [Espaços de Armazenamento Direto do Windows Server 2016 Datacenter Edition \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

O S2D dá suporte a dois tipos de arquiteturas: convergida e hiperconvergida. A arquitetura neste documento é hiperconvergida. Uma infraestrutura hiperconvergida coloca o armazenamento nos mesmos servidores que hospedam o aplicativo em cluster. Nessa arquitetura, o armazenamento está em cada nó de FCI do SQL Server.

## <a name="licensing-and-pricing"></a>Licenciamento e preço

Em Máquinas Virtuais do Microsoft Azure você pode licenciar o SQL Server usando imagens de VM pré-pagas (PAYG) ou Traga sua própria licença (BYOL). O tipo de imagem que você escolheu afeta como você é cobrado.

Com licenciamento PAYG, uma instância de cluster de failover (FCI) do SQL Server em Máquinas Virtuais do Microsoft Azure, incorre em encargos de todos os nós de FCI, incluindo os nós passivos. Para mais informações, consulte [Preços de Máquinas Virtuais do SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Os clientes com Contrato Enterprise com Software Assurance têm o direito de usar um nó FCI passivo livre para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens de VM BYOL e, em seguida, use a mesma licença nos nós ativo e passivo da FCI. Para obter mais informações, consulte [Contrato Enterprise](https://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Para comparar licenciamento PAYG e BYOL para SQL Server em Máquinas Virtuais do Microsoft Azure, consulte [Introdução às VMs do SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [Preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exemplo de modelo do Azure

Você pode criar a solução inteira no Azure por meio de um modelo. Um exemplo de um modelo está disponível no GitHub em [Modelos de início rápido do Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Este exemplo não é criado ou testado para qualquer carga de trabalho específica. Você pode executar o modelo para criar uma FCI do SQL Server com armazenamento S2D conectado ao seu domínio. Você pode avaliar o modelo e modificá-lo para seus propósitos.

## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e fazer antes de continuar.

### <a name="what-to-know"></a>O que é preciso saber
Você deve ter uma compreensão operacional das seguintes tecnologias:

- [Tecnologias de cluster do Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de Cluster de Failover do SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Uma diferença importante é que, em um cluster de failover de convidado de VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster de convidado de uma VM de IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só são acessíveis em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de convidado de uma VM de IaaS do Azure. 

Além disso, você deve ter uma compreensão geral das tecnologias a seguir:

- [Solução hiperconvergida usando Espaços de Armazenamento Direto no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Grupos de recursos do Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, a [extensão do SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md) não é compatível com a FCI do SQL Server no Azure. É recomendável que você desinstale a extensão de VMs que participam da FCI. Essa extensão dá suporte a recursos, como Backup Automatizado e Aplicação de Patch Automatizada, além de alguns recursos do portal para SQL. Esses recursos não funcionarão para VMs do SQL depois que o agente for desinstalado.

### <a name="what-to-have"></a>O que é preciso ter

Antes de seguir as instruções neste artigo, você já deve ter:

- Uma assinatura do Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta com permissão para criar objetos na máquina virtual do Azure.
- Uma rede virtual do Azure e uma sub-rede com espaço de endereço IP suficiente para os seguintes componentes:
   - As duas máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na Rede Azure, apontando para os controladores de domínio.

Com esses pré-requisitos em vigor, é possível continuar com a criação do cluster de failover. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-virtual-machines"></a>Etapa 1: Criar máquinas virtuais

1. Faça logon na [portal do Azure](https://portal.azure.com) com sua assinatura.

1. [Criar um conjunto de disponibilidade do Azure](../tutorial-availability-sets.md).

   O conjunto de disponibilidade agrupa máquinas virtuais em domínios de falha e domínios de atualização. O conjunto de disponibilidade garante que o aplicativo não seja afetado por pontos únicos de falha, como o comutador de rede ou a unidade de energia de um rack de servidores.

   Se não criou o grupo de recursos para as máquinas virtuais, faça-o quando você criar um conjunto de disponibilidade do Azure. Se estiver usando o portal do Azure para criar o conjunto de disponibilidade, siga estas etapas:

   - No portal do Azure, clique em **+** para abrir o Azure Marketplace. Procure **Conjunto de disponibilidade**.
   - Clique em **Conjunto de disponibilidade**.
   - Clique em **Criar**.
   - Na folha **Criar conjunto de disponibilidade**, defina os seguintes valores:
      - **Nome**: Um nome para o conjunto de disponibilidade.
      - **Assinatura**: Sua assinatura do Azure.
      - **Grupo de recursos**: Se quiser usar um grupo existente, clique em **Usar existente** e selecione o grupo na lista suspensa. Caso contrário, escolha **Criar Novo** e digite um nome para o grupo.
      - **Localização**: Defina a localização em que você planeja criar as máquinas virtuais.
      - **Domínios de falha**: Use o padrão (3).
      - **Domínios de atualização**: Use o (5) padrão.
   - Clique em **Criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Provisione duas máquinas virtuais do SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, confira [Provisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque as duas máquinas virtuais:

   - No mesmo grupo de recursos do Azure em que o conjunto de disponibilidade está.
   - Na mesma rede que o controlador de domínio.
   - Em uma sub-rede com espaço suficiente de endereços IP para ambas as máquinas virtuais e todos ps FCIs que você pode vir a usar nesse cluster.
   - No conjunto de disponibilidade do Azure.   

      >[!IMPORTANT]
      >Você não pode definir nem alterar a disponibilidade definida depois que uma máquina virtual é criada.

   Escolha uma imagem do Azure Marketplace. Você pode usar uma imagem do Marketplace com isso que inclua apenas o Windows Server e o SQL Server ou o Windows Server. Para obter detalhes, confira [Visão geral do SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   As imagens oficiais do SQL Server na Galeria do Azure incluem uma instância do SQL Server instalada, mais o software de instalação do SQL Server e a chave necessária.

   Escolha a imagem correta de acordo com a maneira como deseja pagar a licença do SQL Server:

   - **Licenciamento de pagamento por uso**: O custo por segundo dessas imagens inclui o licenciamento do SQL Server:
      - **SQL Server 2016 Enterprise no Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard no Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer no Windows Server Datacenter 2016**

   - **BYOL (Traga sua própria licença)**

      - **{BYOL} SQL Server 2016 Enterprise no Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard no Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Após criar a máquina virtual, remova a instância do SQL Server autônomo pré-instalado. Você usará a mídia pré-instalada do SQL Server para criar a FCI do SQL Server depois de configurar o cluster de failover e o S2D.

   Como alternativa, você pode usar imagens do Azure Marketplace apenas com o sistema operacional. Escolha uma imagem do **Datacenter do Windows Server 2016** e instale a FCI do SQL Server depois de configurar o cluster de failover e o S2D. Esta imagem não contém a mídia de instalação do SQL Server. Coloque a mídia de instalação em um local onde você possa executar a instalação do SQL Server para cada servidor.

1. Depois que o Azure criar máquinas virtuais, conecte-se a cada máquina virtual com o RDP.

   Quando você se conecta pela primeira vez a uma máquina virtual com o RDP, o computador perguntará se deseja você permitir que esse computador seja detectável na rede. Clique em **Sim**.

1. Se estiver usando uma das imagens de máquina virtual baseada no SQL Server, remova a instância do SQL Server.

   - Em **Programas e Recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 2016 (64 bits)** e clique em **Desinstalar/Alterar**.
   - Clique em **Remover**.
   - Selecione a instância padrão.
   - Remova todos os recursos em **Serviços de Mecanismo de Banco de Dados**. Não remova **Recursos Compartilhados**. Confira a seguinte figura:

      ![Remover Recursos](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Clique em **Avançar** e em **Remover**.

1. <a name="ports"></a>Abra as portas do firewall.

   Em cada máquina virtual, abra as portas a seguir no Firewall do Windows.

   | Finalidade | Porta TCP | Observações
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se você tiver usado uma imagem da galeria, essa porta será aberta automaticamente.
   | Investigação de integridade | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.  

1. Adicione armazenamento à máquina virtual. Para obter informações detalhadas, confira [adicionar armazenamento](../disks-types.md).

   As duas máquinas virtuais precisam de pelo menos dois discos de dados.

   Anexe discos brutos - não discos formatado com NTFS.
      >[!NOTE]
      >Se anexar discos formatados com NTFS, você só poderá habilitar S2D sem verificação de qualificação do disco.  

   Anexe no mínimo dois SSDs premium a cada VM. É recomendável ter pelo menos discos P30 (1 TB).

   Defina o cache de host como **Somente leitura**.

   A capacidade de armazenamento que você usa em ambientes de produção depende de sua carga de trabalho. Os valores descritos neste artigo são para teste e demonstração.

1. [Adicione as máquinas virtuais ao domínio pré-existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois que as máquinas virtuais forem criadas e configuradas, você poderá configurar o cluster de failover.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Etapa 2: Configurar o cluster de failover do Windows com S2D

A próxima etapa é configurar o cluster de failover com o S2D. Nesta etapa, você executará as seguintes subetapas:

1. Adicionar o recurso Clustering de Failover do Windows
1. Validar o cluster
1. Criar o cluster de failover
1. Criar a testemunha de nuvem
1. Adicionar armazenamento

### <a name="add-windows-failover-clustering-feature"></a>Adicionar o recurso Clustering de Failover do Windows

1. Para começar, conecte-se à primeira máquina virtual com o RDP usando uma conta de domínio que seja membro do grupo local Administradores e tenha permissões para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. [Adicionar o recurso de Cluster de Failover para cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar o recurso de Cluster de Failover por meio da interface do usuário, execute as etapas a seguir nas duas máquinas virtuais.
   - Em **Gerenciador de Servidor**, clique em **Gerenciar** e clique em **Adicionar Funções e Recursos**.
   - No **Assistente para Adicionar Funções e Recursos**, clique em **Avançar** até chegar a **Selecionar Recursos**.
   - Em **Selecionar Recursos**, clique em **Clustering de Failover**. Inclua todos os recursos e as ferramentas de gerenciamento. Clique em **Adicionar Recursos**.
   - Clique em **Avançar** e em **Concluir** para instalar os recursos.

   Para instalar o recurso de Cluster de Failover com o PowerShell, execute o script a seguir em uma sessão do PowerShell de administrador em uma das máquinas virtuais.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para referência, as próximas etapas seguem as instruções da etapa 3 de [Solução hiperconvergente usando Espaços de Armazenamento Direto no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Este guia se refere a instruções em [validar cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Valide o cluster na interface do usuário ou com o PowerShell.

Para validar o cluster com a interface do usuário, execute as etapas a seguir em uma das máquinas virtuais.

1. Em **Gerenciador do Servidor**, clique em **Ferramentas** e clique em **Gerenciador de Cluster de Failover**.
1. Em **Gerenciador de Cluster de Failover**, clique em **Ação** e clique em **Validar Configuração...**.
1. Clique em **Avançar**.
1. Em **Selecionar Servidores ou um Cluster**, digite o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, escolha **Executar apenas os testes selecionados**. Clique em **Avançar**.
1. Em **Testar seleção**, inclua todos os testes, exceto **Armazenamento**. Confira a seguinte figura:

   ![Validar Testes](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Clique em **Avançar**.
1. Em **Confirmação**, clique em **Avançar**.

O **Assistente para Validar uma Configuração** executa os testes de validação.

Para validar o cluster com o PowerShell, execute o seguinte script em uma sessão do PowerShell de administrador em uma das máquinas virtuais.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Este guia refere-se a [Criar o cluster de failover](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornam os nós do cluster.
- Um nome para o cluster de failover
- Um endereço IP do cluster de failover. Você pode usar um endereço IP que não seja usado na mesma sub-rede e rede virtual do Azure que os nós de cluster.

O PowerShell a seguir cria um cluster de failover. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP da VNET do Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem

A Testemunha de Nuvem é um novo tipo de testemunha de quorum de cluster armazenado em um Azure Storage Blob. Isso elimina a necessidade de uma VM separada que hospeda uma testemunha de compartilhamento.

1. [Crie uma testemunha de nuvem para o cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Criar um contêiner de blob.

1. Salve as chaves de acesso e a URL do contêiner.

1. Configure a testemunha de quorum do cluster de failover. Consulte, [Configurar a testemunha de quorum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) na IU.

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para S2D precisam estar vazios e sem partições ou outros dados. Para limpar os discos, siga [as etapas neste guia](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Habilitar Espaços de Armazenamento Diretos \(S2D\)](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O PowerShell a seguir habilita os espaços de armazenamento direto.  

   ```powershell
   Enable-ClusterS2D
   ```

   No **Gerenciador de Cluster de Failover**, agora você pode ver o pool de armazenamento.

1. [Criar um volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Um dos recursos do S2D é que ele cria automaticamente um pool de armazenamento quando você o habilita. Agora você está pronto para criar um volume. O commandlet `New-Volume` do PowerShell automatiza o processo de criação do volume, incluindo formatação, adição ao cluster e criação de um CSV (volume compartilhado do cluster). O exemplo a seguir cria um CSV de 800 GB (gigabytes).

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Após a conclusão do comando, um volume de 800 GB é montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\`.

   O diagrama a seguir mostra um volume compartilhado do cluster com S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Etapa 3: Testar o failover do cluster de failover

No Gerenciador de Cluster de Failover, verifique se você pode mover o recurso de armazenamento para outro nó do cluster. Se você conseguir se conectar ao cluster de failover com o **Gerenciador de Cluster de Failover** e mover o armazenamento de um nó para o outro, você estará pronto para configurar a FCI.

## <a name="step-4-create-sql-server-fci"></a>Etapa 4: Criar a FCI do SQL Server

Depois de configurar o cluster de failover e todos os componentes do cluster, incluindo o armazenamento, é possível criar a FCI do SQL Server.

1. Conecte-se à primeira máquina virtual com o RDP.

1. No **Gerenciador de Cluster de Failover**, verifique se todos os recursos de cluster principais estão na primeira máquina virtual. Se necessário, mova todos os recursos para essa máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. Clique em **Instalação**.

1. Na **Central de Instalação do SQL Server**, clique em **Instalação**.

1. Clique em **Nova instalação de cluster de failover do SQL Server**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados de FCI precisam estar no armazenamento de cluster. Com S2D, não é um disco compartilhado, mas o ponto de montagem para um volume em cada servidor. O S2D sincroniza o volume entre ambos os nós. O volume é apresentado ao cluster como um volume compartilhado do cluster. Use o ponto de montagem CSV para os diretórios de dados.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. Depois que você concluir o assistente, a configuração instalará um FCI do SQL Server no primeiro nó.

1. Após a configuração instalar com êxito o FCI no primeiro nó, conecte-se ao segundo nó com o RDP.

1. Abra a **Central de Instalação do SQL Server**. Clique em **Instalação**.

1. Clique em **Adicionar nó a um cluster de failover do SQL Server**. Siga as instruções no assistente para instalar o SQL server e adicionar esse servidor ao FCI.

   >[!NOTE]
   >Se você usou uma imagem da Galeria do Azure Marketplace com o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se não usou essa imagem, instale as ferramentas do SQL Server separadamente. Confira [Baixar o SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Etapa 5: Criar o balanceador de carga do Azure

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster de cada vez. Nessa solução, o balanceador de carga mantém o endereço IP para o FCI do SQL Server.

[Criar e configurar um balanceador de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. No portal do Azure, vá para o Grupo de Recursos com as máquinas virtuais.

1. Clique em **+ Adicionar**. Procure o **balanceador de carga** no Marketplace. Clique em **Balanceador de Carga**.

1. Clique em **Criar**.

1. Configure o balanceador de carga com:

   - **Nome**: um nome que identifica o balanceador de carga.
   - **Tipo**: o balanceador de carga pode ser público ou privado. Um balanceador de carga privado pode ser acessado na mesma VNET. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se o aplicativo precisa ter acesso ao SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **Rede virtual**: a mesma rede que a das máquinas virtuais.
   - **Sub-rede**: a mesma sub-rede que a das máquinas virtuais.
   - **Endereço IP privado**: o mesmo endereço IP que foi atribuído ao recurso de rede de cluster de FCI do SQL Server.
   - **Assinatura**: Sua assinatura do Azure.
   - **Grupo de recursos**: use o mesmo grupo de recursos que suas máquinas virtuais.
   - **Localização**: use a mesma localização do Azure que suas máquinas virtuais.
   Confira a seguinte figura:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao Grupo de Recursos do Azure com as máquinas virtuais e localize o novo balanceador de carga. Talvez você precise atualizar o modo de exibição no Grupo de Recursos. Clique no balanceador de carga.

1. Clique em **Pools de back-end** e clique em **+ Adicionar** para adicionar um pool de back-end.

1. Associe o pool de back-end com o conjunto de disponibilidade que contém as VMs.

1. Em **Configurações de IP de rede de destino**, marque **MÁQUINA VIRTUAL** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão FCI. 

1. Clique em **OK** para criar o pool de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar um teste de integridade do balanceador de carga

1. Na folha de balanceador de carga, clique em **Investigações de integridade**.

1. Clique em **+ Adicionar**.

1. Na folha **Adicionar investigação de integridade**, <a name="probe"></a>Definir os parâmetros de investigação de integridade:

   - **Nome**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: Configurar como a porta que você criou no firewall para a investigação de integridade no [esta etapa](#ports). Neste artigo, o exemplo usa a porta TCP `59999`.
   - **Intervalo**: 5 segundos.
   - **Limite não íntegro**: duas falhas consecutivas.

1. Clique em OK.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. Na folha de balanceador de carga, clique em **Regras de balanceamento de carga**.

1. Clique em **+ Adicionar**.

1. Defina parâmetros de regras de balanceamento de carga:

   - **Nome**: um nome para a regras de balanceamento de carga.
   - **Endereço IP de front-end**: use o endereço IP do recurso de rede de cluster de FCI do SQL Server.
   - **Porta**: definido para a porta TCP da FCI do SQL Server. A porta de instância padrão é 1433.
   - **Porta de back-end**: esse valor usa a mesma porta que o valor de **Porta** quando você habilita **IP flutuante (retorno de servidor direto)**.
   - **Pool de back-end**: use o nome do pool de back-end que você já configurou.
   - **Investigação de integridade**: use a investigação de integridade que você já configurou.
   - **Persistência de sessão**: Nenhuma.
   - **Tempo limite de ociosidade (minutos)**: 4.
   - **IP flutuante (retorno de servidor direto)**: Enabled

1. Clique em **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Etapa 6: Configurar o cluster para investigação

Defina o parâmetro de porta de investigação de cluster no PowerShell.

Para definir o parâmetro de porta de investigação do cluster, atualize as variáveis no script a seguir usando valores do seu ambiente. Remova os colchetes angulares `<>` do script. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

No script anterior, defina os valores para o seu ambiente. A lista a seguir descreve os valores:

   - `<Cluster Network Name>`: nome do cluster de failover do Windows Server para a rede. Em **Gerenciador de Cluster de Failover** > **Redes**, clique com o botão direito do mouse na rede e clique em **Propriedades**. O valor correto está em **Nome**, na guia **Geral**. 

   - `<SQL Server FCI IP Address Resource Name>`: nome do recurso de endereço IP da FCI do SQL Server. Em **Gerenciador de Cluster de Failover** > **Funções**, na função FCI do SQL Server, em **Nome do Servidor**, clique com o botão direito do mouse no recurso de endereço IP e clique em **Propriedades**. O valor correto está em **Nome**, na guia **Geral**. 

   - `<ILBIP>`: o endereço IP do ILB. Esse endereço é configurado no Portal do Azure como o endereço front-end do ILB. Esse também é o endereço IP de FCI do SQL Server. Você pode localizá-lo no **Gerenciador de Cluster de Failover**, na mesma página de propriedades em que você localizou o `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: é a porta de investigação configurada na investigação de integridade do balanceador de carga. Qualquer porta TCP não usada é válida. 

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro de cluster deve ser o endereço de difusão de TCP IP: `255.255.255.255`.

Depois de você configurar a investigação de cluster, você pode ver todos os parâmetros de cluster no PowerShell. Execute o seguinte script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Etapa 7: Testar o failover da FCI

Teste o failover de FCI para validar a funcionalidade do cluster. Execute as seguintes etapas:

1. Conecte-se a um de nós do cluster FCI do SQL Server com o RDP.

1. Abra o **Gerenciador de Cluster de Failover**. Clique em **Funções**. Observe qual nó possui a função FCI do SQL Server.

1. Clique com o botão direito do mouse na função FCI do SQL Server.

1. Clique em **Mover** e em **Melhor Nó Possível**.

**Gerenciador de Cluster de Failover** mostra a função, e seus recursos ficam offline. Os recursos são movidos e ficam online no outro nó.

### <a name="test-connectivity"></a>Testar a conectividade

Para testar a conectividade, faça logon em outra máquina virtual na mesma rede virtual. Abra o **SQL Server Management Studio** e conecte-se ao nome de FCI do SQL Server.

>[!NOTE]
>Se necessário, você pode [baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

As Máquinas Virtuais do Azure dão suporte ao Coordenador de Transações Distribuídas da Microsoft (MSDTC) no Windows Server 2019 com o armazenamento nos volumes compartilhados clusterizados (CSV) e um [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais do Azure, o MSDTC não é suportado no Windows Server 2016 e anteriores porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. Com o Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver lá. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.

## <a name="see-also"></a>Veja também

[Instalação S2D com área de trabalho remota (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solução hiperconvergida com espaços de armazenamento diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Visão geral direta de espaço de armazenamento](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Suporte do SQL Server para S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
