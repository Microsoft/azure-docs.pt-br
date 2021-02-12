---
title: Criar um FCI com um compartilhamento de arquivos Premium
description: Use um compartilhamento de arquivos Premium (PFS) para criar uma FCI (instância de cluster de failover) com SQL Server em máquinas virtuais do Azure.
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
ms.openlocfilehash: 2fb9677f0874de1fb715082d58a0e354880e654b
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97358064"
---
# <a name="create-an-fci-with-a-premium-file-share-sql-server-on-azure-vms"></a>Criar um FCI com um compartilhamento de arquivos Premium (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma FCI (instância de cluster de failover) com SQL Server em VMs (máquinas virtuais) do Azure usando um [compartilhamento de arquivos Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Os compartilhamentos de arquivos Premium são compartilhamentos de arquivos de baixa latência Espaços de Armazenamento Diretos (SSD) com suporte total para uso com instâncias de cluster de failover do SQL Server 2012 ou posterior no Windows Server 2012 ou posterior. Os compartilhamentos de arquivo premium proporcionam maior flexibilidade, permitindo que você redimensione e escale um compartilhamento de arquivo sem nenhum tempo de inatividade.

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [práticas recomendadas de cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Pré-requisitos

Antes de concluir as instruções neste artigo, você já deve ter:

- Uma assinatura do Azure.
- Uma conta que tenha permissões para criar objetos em máquinas virtuais do Azure e no Active Directory.
- [Duas ou mais máquinas virtuais do Windows Azure](failover-cluster-instance-prepare-vm.md) preparadas em um [conjunto de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) ou [zonas de disponibilidade](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)diferentes.
- Um [compartilhamento de arquivo premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) a ser usado como a unidade clusterizada, com base na cota de armazenamento do seu banco de dados para os arquivos de dados.
- A versão mais recente do [PowerShell](/powershell/azure/install-az-ps). 

## <a name="mount-premium-file-share"></a>Montar compartilhamento de arquivos Premium

1. Entre no [portal do Azure](https://portal.azure.com). e vá para sua conta de armazenamento.
1. Vá para **compartilhamentos de arquivos** em **serviço de arquivo** e selecione o compartilhamento de arquivos premium que você deseja usar para o armazenamento do SQL.
1. Selecione **Conectar** para abrir a cadeia de conexão do compartilhamento de arquivo.
1. Na lista suspensa, selecione a letra da unidade que você deseja usar e copie os dois blocos de código para o bloco de notas.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/premium-file-storage-commands.png" alt-text="Copiar os dois comandos do PowerShell do portal de conexão do compartilhamento de arquivo":::

1. Use protocolo RDP (RDP) para se conectar à VM SQL Server com a conta que seu SQL Server FCI usará para a conta de serviço.
1. Abra um console de comando do PowerShell administrativo.
1. Execute os comandos que você salvou anteriormente enquanto trabalhava no portal.
1. Vá para o compartilhamento usando o explorador de arquivos ou a caixa de diálogo **executar** (selecione Windows + R). Use o caminho de rede `\\storageaccountname.file.core.windows.net\filesharename`. Por exemplo, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie pelo menos uma pasta no compartilhamento de arquivos conectado recentemente para posicionar seus arquivos de dados SQL.
1. Repita essas etapas em cada VM do SQL Server que participará do cluster.

  > [!IMPORTANT]
  > - Considere o uso de um compartilhamento de arquivos separado para arquivos de backup para salvar as operações de entrada/saída por segundo (IOPS) e a capacidade de espaço desse compartilhamento para arquivos de dados e de log. Você pode usar um compartilhamento de arquivos Premium ou Standard para arquivos de backup.
  > - Se você estiver usando o Windows 2012 R2 ou anterior, siga estas mesmas etapas para montar o compartilhamento de arquivos que você pretende usar como a testemunha de compartilhamento de arquivos. 
  > 


## <a name="add-windows-cluster-feature"></a>Adicionar recurso de cluster do Windows

1. Conecte-se à primeira máquina virtual com o RDP usando uma conta de domínio que seja membro dos administradores locais e tenha permissões para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. [Adicionar clustering de failover a cada máquina virtual](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

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

## <a name="validate-cluster"></a>Validar cluster

Valide o cluster na interface do usuário ou usando o PowerShell.

Para validar o cluster usando a interface do usuário, faça o seguinte em uma das máquinas virtuais:

1. Em **Gerenciador do Servidor**, selecione **Ferramentas** e **Gerenciador de Cluster de Failover**.
1. Em **Gerenciador de Cluster de Failover**, selecione **Ação** e **Validar Configuração**.
1. Selecione **Avançar**.
1. Em **Selecionar Servidores ou um Cluster**, insira o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, selecione **Executar apenas os testes selecionados**. 
1. Selecione **Avançar**.
1. Em **Seleção de Teste**, selecione todos os testes, exceto **Armazenamento** e **Espaços de Armazenamento Diretos**, conforme mostrado aqui:

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/cluster-validation.png" alt-text="Selecionar testes de validação de cluster":::

1. Selecione **Avançar**.
1. Em **Confirmação**, selecione **Avançar**.

O assistente para **validar uma configuração** executa os testes de validação.

Para validar o cluster usando o PowerShell, execute o seguinte script em uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
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


## <a name="test-cluster-failover"></a>Testar failover de cluster

Teste o failover do cluster. Em **Gerenciador de cluster de failover**, clique com o botão direito do mouse no cluster, selecione **mais ações**  >  **mover recurso de cluster principal**  >  **selecione nó** e, em seguida, selecione o outro nó do cluster. Mova o recurso principal de cluster para cada nó do cluster e mova-o novamente para o nó primário. Se você puder mover o cluster para cada nó com êxito, estará pronto para instalar o SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testar o failover de cluster movendo o recurso principal para os outros nós":::


## <a name="create-sql-server-fci"></a>Criar a FCI do SQL Server

Depois de configurar o cluster de failover, crie a FCI do SQL Server.

1. Conecte-se à primeira máquina virtual usando o RDP.

1. Em **Gerenciador de cluster de failover**, verifique se todos os recursos de cluster principais estão na primeira máquina virtual. Se necessário, mova todos os recursos para essa máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. 

1. Selecione **instalação**.

1. Na **Central de Instalação do SQL Server**, selecione **Instalação**.

1. Selecione **novo SQL Server instalação de cluster de failover** e siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI precisam estar no compartilhamento de arquivo premium. Insira o caminho completo do compartilhamento, neste formato: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . Um aviso será exibido, informando que você especificou um servidor de arquivos como o diretório de dados. Esse aviso é esperado. Verifique se a conta de usuário usada para acessar a VM via RDP ao persistir o compartilhamento de arquivos é a mesma conta que o serviço de SQL Server usa para evitar possíveis falhas.

   :::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/use-file-share-as-data-directories.png" alt-text="Usar o compartilhamento de arquivo como diretórios de dados do SQL":::

1. Depois que você concluir as etapas no assistente, a Instalação instalará uma FCI do SQL Server no primeiro nó.

1. Depois que a Instalação instalar a FCI com êxito no primeiro nó, conecte-se ao segundo nó usando o RDP.

1. Abra a **central de instalação do SQL Server** e, em seguida, selecione **instalação**.

1. Selecione **Adicionar um nó a um cluster de failover do SQL Server**. Siga as instruções no assistente para instalar o SQL Server e adicionar o servidor à FCI.

   >[!NOTE]
   >Se você usou uma imagem da Galeria do Azure Marketplace com o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se você não usou uma dessas imagens, instale as ferramentas do SQL Server separadamente. Para obter mais informações, consulte [Baixar o SSMS (SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms).

1. Repita essas etapas em todos os outros nós que você deseja adicionar à instância de cluster de failover SQL Server. 

## <a name="register-with-the-sql-vm-rp"></a>Registrar com a VM do SQL RP

Para gerenciar sua VM SQL Server no portal, registre-a com a extensão do SQL IaaS Agent (RP) no [modo de gerenciamento leve](sql-agent-extension-manually-register-single-vm.md#lightweight-management-mode), atualmente o único modo com suporte com FCI e SQL Server em VMs do Azure. 

Registre uma VM de SQL Server no modo leve com o PowerShell (-LicenseType pode ser `PAYG` ou `AHUB` ):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType ???? -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configurar a conectividade 

Para rotear o tráfego adequadamente para o nó primário atual, configure a opção de conectividade adequada para o seu ambiente. Você pode criar um [balanceador de carga do Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou, se você estiver usando SQL Server 2019 Cu2 (ou posterior) e o Windows Server 2016 (ou posterior), poderá usar o recurso de [nome de rede distribuída](failover-cluster-instance-distributed-network-name-dnn-configure.md) em vez disso. 

## <a name="limitations"></a>Limitações

- O Microsoft Coordenador de Transações Distribuídas (MSDTC) não tem suporte no Windows Server 2016 e versões anteriores. 
- Não há suporte para o fluxo de arquivos em um cluster de failover com um compartilhamento de arquivo premium. Para usar FILESTREAM, implante o cluster usando [espaços de armazenamento diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md) ou [discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) em vez disso.
- Há suporte apenas para o registro com a extensão do SQL IaaS Agent no [modo de gerenciamento leve](sql-server-iaas-agent-extension-automate-management.md#management-modes) . 

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, configure a conectividade com o FCI com um [nome de rede virtual e um balanceador de carga do Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou [DNN (nome de rede distribuída)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 


Se os compartilhamentos de arquivos Premium não forem a solução de armazenamento FCI apropriada para você, considere criar o FCI usando os [discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) ou [espaços de armazenamento diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md) em vez disso. 

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [práticas recomendadas de configuração de cluster](hadr-cluster-best-practices.md). 

Para obter mais informações, consulte: 
- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instâncias de cluster de failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
