---
title: Criar um FCI com discos compartilhados do Azure
description: Use os discos compartilhados do Azure para criar uma FCI (instância de cluster de failover) com SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/26/2020
ms.author: mathoma
ms.openlocfilehash: ce77021e74507ead6d225081debc7024cb89a15a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102042395"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Criar um FCI com discos compartilhados do Azure (SQL Server em VMs do Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma FCI (instância de cluster de failover) usando discos compartilhados do Azure com o SQL Server em VMs (máquinas virtuais) do Azure. 

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [práticas recomendadas de cluster](hadr-cluster-best-practices.md). 

## <a name="prerequisites"></a>Pré-requisitos 

Antes de concluir as instruções neste artigo, você já deve ter:

- Uma assinatura do Azure. Comece [gratuitamente](https://azure.microsoft.com/free/). 
- [Duas ou mais máquinas virtuais do Windows Azure](failover-cluster-instance-prepare-vm.md). Os [conjuntos de disponibilidade](../../../virtual-machines/windows/tutorial-availability-sets.md) e PPGs (grupos de posicionamento de [proximidade](../../../virtual-machines/co-location.md#proximity-placement-groups) ) com suporte para zonas de [disponibilidade](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) e SSD Premium têm suporte para ultra discos. Todos os nós devem existir no mesmo [grupo de posicionamento de proximidade](../../../virtual-machines/co-location.md#proximity-placement-groups).
- Uma conta que tenha permissões para criar objetos em máquinas virtuais do Azure e no Active Directory.
- A versão mais recente do [PowerShell](/powershell/azure/install-az-ps). 

## <a name="add-azure-shared-disk"></a>Adicionar disco compartilhado do Azure
Implante um disco SSD Premium gerenciado com o recurso de disco compartilhado habilitado. Defina `maxShares` para **alinhar com o número de nós de cluster** para tornar o disco compartilhável em todos os nós do FCI. 

Adicione um disco compartilhado do Azure fazendo o seguinte: 

1. Salve o seguinte script como *SharedDiskConfig.jsem*: 

   ```JSON
   { 
     "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
       "dataDiskName": {
         "type": "string",
         "defaultValue": "mySharedDisk"
       },
       "dataDiskSizeGB": {
         "type": "int",
         "defaultValue": 1024
       },
       "maxShares": {
         "type": "int",
         "defaultValue": 2
       }
     },
     "resources": [
       {
         "type": "Microsoft.Compute/disks",
         "name": "[parameters('dataDiskName')]",
         "location": "[resourceGroup().location]",
         "apiVersion": "2019-07-01",
         "sku": {
           "name": "Premium_LRS"
         },
         "properties": {
           "creationData": {
             "createOption": "Empty"
           },
           "diskSizeGB": "[parameters('dataDiskSizeGB')]",
           "maxShares": "[parameters('maxShares')]"
         }
       }
     ] 
   }
   ```

2. Execute *SharedDiskConfig.jsno* usando o PowerShell: 

   ```powershell
   $rgName = < specify your resource group name>
       $location = 'westcentralus'
       New-AzResourceGroupDeployment -ResourceGroupName $rgName `
   -TemplateFile "SharedDiskConfig.json"
   ```

3. Para cada VM, inicialize os discos compartilhados anexados como GPT (tabela de partição GUID) e formate-os como um novo sistema de arquivos de tecnologia (NTFS) executando este comando: 

    ```powershell
    $resourceGroup = "<your resource group name>"
    $location = "<region of your shared disk>"
    $ppgName = "<your proximity placement groups name>"
    $vm = Get-AzVM -ResourceGroupName "<your resource group name>" `
        -Name "<your VM node name>"
    $dataDisk = Get-AzDisk -ResourceGroupName $resourceGroup `
        -DiskName "<your shared disk name>"
    $vm = Add-AzVMDataDisk -VM $vm -Name "<your shared disk name>" `
        -CreateOption Attach -ManagedDiskId $dataDisk.Id `
        -Lun <available LUN - check disk setting of the VM>
    Update-AzVm -VM $vm -ResourceGroupName $resourceGroup
    ```

## <a name="create-failover-cluster"></a>Criar cluster de failover

Para criar o cluster de failover, você precisa de:

- Os nomes das máquinas virtuais que se tornarão os nós de cluster.
- Um nome para o cluster de failover.
- Um endereço IP do cluster de failover. Você pode usar um endereço IP que não seja usado na mesma sub-rede e rede virtual do Azure que os nós de cluster.

# <a name="windows-server-2012-2016"></a>[Windows Server 2012-2016](#tab/windows2012)

O script do PowerShell a seguir cria um cluster de failover. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

O script do PowerShell a seguir cria um cluster de failover. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Para obter mais informações, confira: [Cluster de failover: objeto de rede de cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---

## <a name="configure-quorum"></a>Configurar o quorum

Configure a solução de quorum mais adequada às suas necessidades de negócios. Você pode configurar uma testemunha de [disco](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum), uma [testemunha de nuvem](/windows-server/failover-clustering/deploy-cloud-witness)ou uma testemunha de compartilhamento de [arquivos](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum). Para obter mais informações, consulte [quorum com SQL Server VMs](hadr-cluster-best-practices.md#quorum). 

## <a name="validate-cluster"></a>Validar cluster
Valide o cluster na interface do usuário ou usando o PowerShell.

Para validar o cluster usando a interface do usuário, faça o seguinte em uma das máquinas virtuais:

1. Em **Gerenciador do Servidor**, selecione **Ferramentas** e **Gerenciador de Cluster de Failover**.
1. Em **Gerenciador de Cluster de Failover**, selecione **Ação** e **Validar Configuração**.
1. Selecione **Avançar**.
1. Em **Selecionar Servidores ou um Cluster**, insira o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, selecione **Executar apenas os testes selecionados**. 
1. Selecione **Avançar**.
1. Em **seleção de teste**, selecione todos os testes, *exceto* **armazenamento**

## <a name="test-cluster-failover"></a>Testar failover de cluster

Teste o failover do cluster. Em **Gerenciador de cluster de failover**, clique com o botão direito do mouse no cluster, selecione **mais ações**  >  **mover recurso de cluster principal**  >  **selecione nó** e, em seguida, selecione o outro nó do cluster. Mova o recurso principal de cluster para cada nó do cluster e mova-o novamente para o nó primário. Se você puder mover o cluster para cada nó com êxito, estará pronto para instalar o SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Testar o failover de cluster movendo o recurso principal para os outros nós":::

## <a name="create-sql-server-fci"></a>Criar a FCI do SQL Server

Depois de configurar o cluster de failover e todos os componentes do cluster, incluindo o armazenamento, é possível criar a FCI do SQL Server.

1. Conecte-se à primeira máquina virtual usando protocolo RDP (RDP).

1. Em **Gerenciador de cluster de failover**, verifique se todos os recursos de cluster principais estão na primeira máquina virtual. Se necessário, mova todos os recursos para aquela máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. 

1. Selecione **instalação**.

1. Na **Central de Instalação do SQL Server**, selecione **Instalação**.

1. Selecione **Nova instalação de cluster de failover do SQL Server**. Siga as instruções no Assistente para instalar o SQL Server FCI.

Os diretórios de dados do FCI precisam estar nos discos compartilhados do Azure. 

1. Ao concluir as instruções do assistente, a configuração instalará uma FCI do SQL Server no primeiro nó.

1. Depois que a Instalação instalar a FCI com êxito no primeiro nó, conecte-se ao segundo nó usando o RDP.

1. Abra a **central de instalação do SQL Server** e, em seguida, selecione **instalação**.

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

- Há suporte apenas para o registro com a extensão do SQL IaaS Agent no [modo de gerenciamento leve](sql-server-iaas-agent-extension-automate-management.md#management-modes) .

## <a name="next-steps"></a>Próximas etapas

Se você ainda não tiver feito isso, configure a conectividade com o FCI com um [nome de rede virtual e um balanceador de carga do Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) ou [DNN (nome de rede distribuída)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Se os discos compartilhados do Azure não forem a solução de armazenamento FCI apropriada para você, considere criar o FCI usando [compartilhamentos de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md) ou [espaços de armazenamento diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md) em vez disso. 

Para saber mais, confira uma visão geral do [FCI com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) e [práticas recomendadas de configuração de cluster](hadr-cluster-best-practices.md).

Para obter mais informações, consulte: 
- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Instâncias de cluster de failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
