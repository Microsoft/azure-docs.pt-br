---
title: Configurar um grupo de disponibilidade (PowerShell & AZ CLI)
description: Use o PowerShell ou o CLI do Azure para criar o cluster de failover do Windows, o ouvinte do grupo de disponibilidade e o balanceador de carga interno em uma VM SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 865ee3a5aeb8a2dd06d8759ba04d02259d2b4bee
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359958"
---
# <a name="use-powershell-or-az-cli-to-configure-an-availability-group-for-sql-server-on-azure-vm"></a>Use o PowerShell ou AZ CLI para configurar um grupo de disponibilidade para SQL Server na VM do Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como usar o [PowerShell](/powershell/scripting/install/installing-powershell) ou o [CLI do Azure](/cli/azure/sql/vm) para implantar um cluster de failover do Windows, adicionar SQL Server VMs ao cluster e criar o balanceador de carga interno e o ouvinte para um grupo de disponibilidade Always on. 

A implantação do grupo de disponibilidade ainda é feita manualmente por meio de SQL Server Management Studio (SSMS) ou Transact-SQL (T-SQL). 

Embora este artigo use o PowerShell e a CLI do AZ para configurar o ambiente do grupo de disponibilidade, também é possível fazer isso na [portal do Azure](availability-group-azure-portal-configure.md), usando [modelos de início rápido do Azure](availability-group-quickstart-template-configure.md)ou [manualmente](availability-group-manually-configure-tutorial.md) também. 

## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade Always On, você deve ter os seguintes pré-requisitos: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs ingressadas [no domínio no Azure que executam o SQL Server 2016 (ou posterior) Enterprise Edition](./create-sql-vm-portal.md) no *mesmo* conjunto de disponibilidade ou em *diferentes* zonas de disponibilidade que foram [registradas com a extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md).  
- A versão mais recente do [PowerShell](/powershell/scripting/install/installing-powershell) ou o [CLI do Azure](/cli/azure/install-azure-cli). 
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade). Um é para o balanceador de carga interno. O outro é para o ouvinte do grupo de disponibilidade na mesma sub-rede que o grupo de disponibilidade. Se você estiver usando um balanceador de carga existente, precisará apenas de um endereço IP disponível para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões

Você precisa das seguintes permissões de conta para configurar o grupo de disponibilidade Always On usando a CLI do Azure: 

- Uma conta de usuário de domínio existente com permissão para **Criar Objeto de Computador** no domínio. Por exemplo, uma conta de administrador do domínio normalmente tem permissão suficiente (por exemplo: account@domain.com). _Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster._
- A conta de usuário do domínio que controla o SQL Server. 
 
## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento 

O cluster precisa de uma conta de armazenamento do Azure para atuar como a testemunha da nuvem. Você pode usar qualquer conta de armazenamento existente ou criar uma. Se você deseja usar uma conta de armazenamento existente, pule para a próxima seção. 

O seguinte snippet de código cria a conta de armazenamento: 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Você poderá ver o erro `az sql: 'vm' is not in the 'az sql' command group` se estiver usando uma versão desatualizada da CLI do Azure. Baixe a [versão mais recente da CLI do Azure](/cli/azure/install-azure-cli-windows) para superar esse erro.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the storage account
# example: New-AzStorageAccount -ResourceGroupName SQLVM-RG -Name cloudwitness `
#    -SkuName Standard_LRS -Location West US -Kind StorageV2 `
#    -AccessTier Hot -EnableHttpsTrafficOnly

New-AzStorageAccount -ResourceGroupName <resource group name> -Name <name> `
    -SkuName Standard_LRS -Location <region> -Kind StorageV2 `
    -AccessTier Hot -EnableHttpsTrafficOnly
```

---

## <a name="define-cluster-metadata"></a>Definir metadados do cluster

O grupo de comandos da CLI do Azure [az sql vm group](/cli/azure/sql/vm/group) gerencia os metadados do serviço WSFC (Windows Server Failover Cluster) que hospeda o grupo de disponibilidade. Os metadados do cluster incluem o domínio do Active Directory, as contas de cluster, as contas de armazenamento a serem usadas como testemunha de nuvem e a versão do SQL Server. Use [az sql vm group create](/cli/azure/sql/vm/group#az-sql-vm-group-create) para definir os metadados do WSFC para que, quando a primeira VM do SQL Server for adicionada, o cluster seja criado conforme definido. 

O snippet de código a seguir define os metadados para o cluster:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Define the cluster metadata
# example: $group = New-AzSqlVMGroup -Name Cluster -Location West US ' 
#  -ResourceGroupName SQLVM-RG -Offer SQL2017-WS2016
#  -Sku Enterprise -DomainFqdn domain.com -ClusterOperatorAccount vmadmin@domain.com
#  -ClusterBootstrapAccount vmadmin@domain.com  -SqlServiceAccount sqlservice@domain.com 
#  -StorageAccountUrl '<ex:https://cloudwitness.blob.core.windows.net/>' `
#  -StorageAccountPrimaryKey '4Z4/i1Dn8/bpbseyWX'

$group = New-AzSqlVMGroup -Name <name> -Location <regio> 
  -ResourceGroupName <resource group name> -Offer <SQL201?-WS201?> 
  -Sku Enterprise -DomainFqdn <FQDN> -ClusterOperatorAccount <domain account> 
  -ClusterBootstrapAccount <domain account>  -SqlServiceAccount <service account> 
  -StorageAccountUrl '<ex:StorageAccountUrl>' `
  -StorageAccountPrimaryKey '<PublicKey>'
```

---

## <a name="add-vms-to-the-cluster"></a>Adicionar VMs ao cluster

A adição da primeira VM do SQL Server ao cluster cria o cluster. O comando [az sql vm add-to-group](/cli/azure/sql/vm#az-sql-vm-add-to-group) cria o cluster com o nome dado anteriormente, instala a função de cluster nas VMs do SQL Server e as adiciona ao cluster. Os usos subsequentes do comando `az sql vm add-to-group` adicionam mais VMs do SQL Server ao cluster recém-criado. 

O seguinte snippet de código cria o cluster e adiciona a primeira VM do SQL Server a ele: 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Use este comando para adicionar outras VMs do SQL Server ao cluster. Modifique apenas o parâmetro `-n` para o nome da VM do SQL Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Add SQL Server VMs to cluster
# example: $sqlvm1 = Get-AzSqlVM -Name SQLVM1 -ResourceGroupName SQLVM-RG
# $sqlvm2 = Get-AzSqlVM -Name SQLVM2  -ResourceGroupName SQLVM-RG

# $sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

# $sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
#  -SqlVMGroup $group -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
#  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
#  - ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

$sqlvm1 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
$sqlvm2 = Get-AzSqlVM -Name <VM2 Name> -ResourceGroupName <Resource Group Name>

$sqlvmconfig1 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm1 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm1.ResourceId -SqlVM $sqlvmconfig1

$sqlvmconfig2 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm2 `
   -SqlVMGroup $group -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

Update-AzSqlVM -ResourceId $sqlvm2.ResourceId -SqlVM $sqlvmconfig2
```

---


## <a name="validate-cluster"></a>Validar cluster 

Para que um cluster de failover seja suportado pela Microsoft, ele deve passar na validação do cluster. Conecte-se à VM usando seu método preferido, como protocolo RDP (RDP) e valide se o cluster passa pela validação antes de continuar. Deixar de fazer isso deixa o cluster em um estado sem suporte. 

Você pode validar o cluster usando Gerenciador de Cluster de Failover (FCM) ou o seguinte comando do PowerShell:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

## <a name="create-availability-group"></a>Criar grupo de disponibilidade

Crie manualmente o grupo de disponibilidade, como de costume, usando o [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), o [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell) ou o [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> *Não* crie um ouvinte no momento, porque isso é feito por meio da CLI do Azure nas seções a seguir.  

## <a name="create-internal-load-balancer"></a>Criar balanceador de carga interno

[!INCLUDE [sql-ag-use-dnn-listener](../../includes/sql-ag-use-dnn-listener.md)]

O ouvinte do grupo de disponibilidade AlwaysOn requer uma instância interna do Azure Load Balancer. O balanceador de carga interno fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade, o que permite failover e reconexão mais rápidos. Se as VMs do SQL Server em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, você poderá usar um balanceador de carga básico. Caso contrário, precisará usar um balanceador de carga padrão.  

> [!NOTE]
> O balanceador de carga interno deve estar na mesma rede virtual que as instâncias de VM do SQL Server. 

O seguinte snippet de código cria o balanceador de carga interno:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Create the internal load balancer
# example: New-AzLoadBalancer -name sqlILB -ResourceGroupName SQLVM-RG  `
#  -Sku Standard -Location West US

New-AzLoadBalancer -name sqlILB -ResourceGroupName <resource group name> `
   -Sku Standard -Location <region>
```

---

>[!IMPORTANT]
> O recurso de IP público de cada VM do SQL Server deve ter uma SKU padrão para ser compatível com o balanceador de carga padrão. Para determinar a SKU do recurso de IP público da sua VM, acesse **Grupo de Recursos**, selecione o recurso **Endereço IP Público** para a VM do SQL Server desejada e localize o valor em **SKU** no painel **Visão geral**.  

## <a name="create-listener"></a>Criar ouvinte

Depois de criar manualmente o grupo de disponibilidade, você pode criar o ouvinte usando [az sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-create). 

A *ID do recurso de sub-rede* é o valor de `/subnets/<subnetname>` anexado à ID do recurso da rede virtual. Para identificar a ID do recurso de sub-rede:
   1. Vá para o seu grupo de recursos no [portal do Azure](https://portal.azure.com). 
   1. Selecione o recurso de rede virtual. 
   1. Selecione **Propriedades** no painel **Configurações**. 
   1. Identifique a ID do recurso para a rede virtual e anexe `/subnets/<subnetname>` ao final dela para criar a ID do recurso de sub-rede. Por exemplo:
      - A ID do recurso de rede virtual é: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - O nome da sua sub-rede é: `default`
      - Portanto, a ID do recurso de sub-rede é: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O seguinte snippet de código cria o ouvinte do grupo de disponibilidade:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive

# example: New-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
#    -AvailabilityGroupName SQLAG  -GroupName Cluster `
#    -IpAddress 10.0.0.27 -LoadBalancerResourceId sqlilb `
#    -ProbePort 59999 `
#    -SubnetId /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#    -SqlVirtualMachineId sqlvm1 sqlvm2


New-AzAvailabilityGroupListener -Name <listener name> -ResourceGroupName <resource group name> `
   -AvailabilityGroupName <availability group name> -GroupName <cluster name> `
   -IpAddress <ag listener IP address> -LoadBalancerResourceId <lbid> `
   -ProbePort <Load Balancer probe port, default 59999> `
   -SubnetId <subnet resource id> `
   -SqlVirtualMachineId <names of SQL VM's hosting AG replicas>
```

---

## <a name="modify-number-of-replicas"></a>Modificar o número de réplicas 
Há uma camada adicional de complexidade ao implantar um grupo de disponibilidade nas VMs do SQL Server hospedadas no Azure. O provedor de recursos e o grupo de máquinas virtuais agora gerenciam os recursos. Assim, ao adicionar ou remover réplicas no grupo de disponibilidade, há uma etapa adicional de atualização dos metadados do ouvinte com informações sobre as VMs do SQL Server. Ao modificar o número de réplicas no grupo de disponibilidade, você também deve usar o comando [az sql vm group ag-listener update](/cli/azure/sql/vm/group/ag-listener#az-sql-vm-group-ag-listener-update) para atualizar o ouvinte com os metadados das VMs do SQL Server. 


### <a name="add-a-replica"></a>Adicionar uma réplica

Para adicionar uma nova réplica ao grupo de disponibilidade:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Adicione a VM SQL Server ao grupo de clusters:
   ```azurecli-interactive

   # Add the SQL Server VM to the cluster group
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```

1. Use o SQL Server Management Studio para adicionar a instância do SQL Server como uma réplica no grupo de disponibilidade.
1. Adicione os metadados da VM do SQL Server ao ouvinte:

   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Adicione a VM SQL Server ao grupo de clusters:

   ```powershell-interactive
   # Add the SQL Server VM to the cluster group
   # example: $sqlvm3 = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG 
   # $group = Get-AzSqlVMGroup -ResourceGroupName SQLVM-RG -Name Cluster
   # $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   #  -ClusterOperatorAccountPassword Str0ngAzur3P@ssword! `
   #  -SqlServiceAccountPassword Str0ngAzur3P@ssword! `
   #  -ClusterBootstrapAccountPassword Str0ngAzur3P@ssword!

   $sqlvm3 = Get-AzSqlVM -Name <VM1 Name> -ResourceGroupName <Resource Group Name>
   $group = Get-AzSqlVMGroup  -ResourceGroupName <resource group name> -Name <name>
   $sqlvmconfig3 = Set-AzSqlVMConfigGroup -SqlVM $sqlvm3 -SqlVMGroup $group `
   -ClusterOperatorAccountPassword <operator account password> `
   -SqlServiceAccountPassword <service account password> `
   -ClusterBootstrapAccountPassword <bootstrap account password>

   Update-AzSqlVM -ResourceId $sqlvm3.ResourceId -SqlVM $sqlvmconfig3
   ```

1. Use o SQL Server Management Studio para adicionar a instância do SQL Server como uma réplica no grupo de disponibilidade.
1. Adicione os metadados da VM do SQL Server ao ouvinte:

   ```powershell-interactive
   # Update the listener metadata with the new VM
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #   -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs, along with new SQL VM> 

   ```

---

### <a name="remove-a-replica"></a>Remover uma réplica

Para remover uma réplica especificada do grupo de disponibilidade:

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Remova a réplica do grupo de disponibilidade usando o SQL Server Management Studio. 
1. Remova os metadados da VM do SQL Server do ouvinte:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Remova a VM do SQL Server do cluster:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Remova a réplica do grupo de disponibilidade usando o SQL Server Management Studio. 
1. Remova os metadados da VM do SQL Server do ouvinte:

   ```powershell-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: Update-AzAvailabilityGroupListener -Name AGListener -ResourceGroupName SQLVM-RG `
   #  -SqlVMGroupName Cluster -SqlVirtualMachineId SQLVM3

   Update-AzAvailabilityGroupListener -Name <Listener> -ResourceGroupName <Resource Group Name> `
      -SqlVMGroupName <cluster name> -SqlVirtualMachineId <SQL VMs that remain>

   ```
1. Remova a VM do SQL Server do cluster:

   ```powershell-interactive
   # Remove the SQL VM from the cluster
   # example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
   #  $sqlvm. SqlVirtualMachineGroup = ""
   #  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

   $sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
      $sqlvm. SqlVirtualMachineGroup = ""
    
    Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
   ```

---

## <a name="remove-listener"></a>Remover ouvinte
Se posteriormente você precisar remover o ouvinte do grupo de disponibilidade configurado com o CLI do Azure, deverá passar pela extensão do agente IaaS do SQL. Como o ouvinte é registrado por meio da extensão do agente IaaS do SQL, apenas excluí-lo via SQL Server Management Studio é insuficiente. 

O melhor método é excluí-lo por meio da extensão do agente IaaS do SQL usando o trecho de código a seguir no CLI do Azure. Isso remove os metadados do ouvinte do grupo de disponibilidade da extensão do SQL IaaS Agent. Também exclui fisicamente o ouvinte do grupo de disponibilidade. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell-interactive
# Remove the availability group listener
# example: Remove-AzAvailabilityGroupListener -Name AGListener `
#   -ResourceGroupName SQLVM-RG -SqlVMGroupName Cluster

Remove-AzAvailabilityGroupListener -Name <Listener> `
   -ResourceGroupName <Resource Group Name> -SqlVMGroupName <cluster name>
```

---

## <a name="remove-cluster"></a>Remover cluster

Remova todos os nós do cluster para destruí-lo e, em seguida, remova os metadados do cluster da extensão do agente IaaS do SQL. Você pode fazer isso usando o CLI do Azure ou o PowerShell. 


# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, remova todas as VMs SQL Server do cluster: 

```azurecli-interactive
# Remove the VM from the cluster metadata
# example: az sql vm remove-from-group --name SQLVM2 --resource-group SQLVM-RG

az sql vm remove-from-group --name <VM1 name>  --resource-group <resource group name>
az sql vm remove-from-group --name <VM2 name>  --resource-group <resource group name>
```

Se essas são as únicas VMs no cluster, o cluster será destruído. Se houver outras VMs no cluster além das VMs SQL Server que foram removidas, as outras VMs não serão removidas e o cluster não será destruído. 

Em seguida, remova os metadados do cluster da extensão do agente IaaS do SQL: 

```azurecli-interactive
# Remove the cluster from the SQL VM RP metadata
# example: az sql vm group delete --name Cluster --resource-group SQLVM-RG

az sql vm group delete --name <cluster name> Cluster --resource-group <resource group name>
```



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Primeiro, remova todas as VMs SQL Server do cluster. Isso removerá fisicamente os nós do cluster e destruirá o cluster: 

```powershell-interactive
# Remove the SQL VM from the cluster
# example: $sqlvm = Get-AzSqlVM -Name SQLVM3 -ResourceGroupName SQLVM-RG
#  $sqlvm. SqlVirtualMachineGroup = ""
#  Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm

$sqlvm = Get-AzSqlVM -Name <VM Name> -ResourceGroupName <Resource Group Name>
   $sqlvm. SqlVirtualMachineGroup = ""
   
   Update-AzSqlVM -ResourceId $sqlvm -SqlVM $sqlvm
```

Se essas são as únicas VMs no cluster, o cluster será destruído. Se houver outras VMs no cluster além das VMs SQL Server que foram removidas, as outras VMs não serão removidas e o cluster não será destruído. 

Em seguida, remova os metadados do cluster da extensão do agente IaaS do SQL: 

```powershell-interactive
# Remove the cluster metadata
# example: Remove-AzSqlVMGroup -ResourceGroupName "SQLVM-RG" -Name "Cluster"

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name> "
```

---

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral das VMs do SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes para VMs do SQL Server](frequently-asked-questions-faq.md)
* [Notas de versão para VMs do SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Alternar modelos de licenciamento para uma VM do SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md)
* [Visão geral dos grupos de disponibilidade AlwaysOn do &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuração de uma instância de servidor para grupos de disponibilidade AlwaysOn do &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administração de um grupo de disponibilidade do &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoramento de grupos de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Visão geral de instruções Transact-SQL para grupos de disponibilidade AlwaysOn do &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Visão geral de cmdlets do PowerShell para grupos de disponibilidade AlwaysOn do &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)