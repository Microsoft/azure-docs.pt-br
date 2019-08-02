---
title: Usar a CLI de VM de SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server em uma VM do Azure
description: 'Usar a CLI do Azure para criar o Cluster de Failover do Windows, o ouvinte do grupo de disponibilidade e balanceador de carga interno em uma VM do SQL Server no Azure. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b5015f00d3c6dfe0e1e5c2466af777cc0f1bc509
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607145"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Usar a CLI de VM de SQL do Azure para configurar o grupo de disponibilidade Always On do SQL Server em uma VM do Azure
Este artigo descreve como usar [da CLI de VM do SQL Azure](/cli/azure/sql/vm?view=azure-cli-latest/) para implantar um Cluster de Failover de Windows (WSFC) e adicionar VMs do SQL Server ao cluster, bem como criar o balanceador de carga interno e o ouvinte para um grupo de disponibilidade Always On.  A implantação real do grupo de disponibilidade Always On ainda é feita manualmente por meio do SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Pré-requisitos
Para automatizar a instalação de um grupo de disponibilidade Always On usando a CLI de VM do SQL Azure, você já deve ter os seguintes pré-requisitos: 
- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Um ou mais ingressado no domínio [VMs no Azure em execução do SQL Server 2016 (ou superior) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) na *mesmo conjunto de disponibilidade ou zonas de disponibilidade diferentes* que tenham sido [registrado com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).  
- [CLI do Azure](/cli/azure/install-azure-cli). 
- Dois disponíveis (não usado por qualquer entidade) endereços IP, um para o balanceador de carga interno e outro para o ouvinte do grupo de disponibilidade na mesma sub-rede que o grupo de disponibilidade. Se um balanceador de carga existente estiver sendo usado, apenas um endereço IP disponível é necessária para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões
As seguintes permissões de conta são necessárias para configurar o grupo de disponibilidade Always On usando a CLI de VM do SQL Azure. 

- Uma domínio conta de usuário existente que tenha permissão para 'Criar o objeto de computador' no domínio.  Por exemplo, uma conta do administrador de domínio normalmente tem permissão suficiente (ex.: account@domain.com). _Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster._
- A conta de usuário de domínio que controla o serviço SQL Server. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>Etapa 1 - Criar conta de armazenamento como uma testemunha de nuvem
O cluster precisa de uma conta de armazenamento para atuar como a testemunha de nuvem. Você pode usar qualquer conta de armazenamento existente, ou você pode criar uma nova conta de armazenamento. Se você quiser usar uma conta de armazenamento existente, pule para a próxima seção. 

O trecho de código a seguir cria a conta de armazenamento: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Você pode ver o erro `az sql: 'vm' is not in the 'az sql' command group` se você estiver usando uma versão desatualizada do CLI do Azure. Baixe o [a versão mais recente da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) para ultrapassar esse erro.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>Etapa 2: definir metadados de Cluster de Failover do Windows
A CLI de VM do Azure SQL [grupo de vm az sql](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) comando grupo gerencia os metadados do serviço de Cluster de Failover do Windows (WSFC) que hospeda o grupo de disponibilidade. Metadados de cluster incluem o domínio do AD, contas de cluster, as contas de armazenamento a ser usado como a testemunha de nuvem e a versão do SQL Server. Use [criar grupo de vm az sql](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) para definir os metadados para o WSFC para que quando a primeira VM do SQL Server é adicionado, o cluster é criado, conforme definido. 

O trecho de código a seguir define os metadados para o cluster:
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

## <a name="step-3---add-sql-server-vms-to-cluster"></a>Etapa 3 - adicionar VMs do SQL Server ao cluster
Adicionar a primeira VM do SQL Server para o cluster cria o cluster. O [az vm Adicionar-para-grupo de sql](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) comando cria o cluster com o nome fornecido anteriormente, instala a função de cluster em VMs do SQL Server e os adiciona ao cluster. Usos subsequentes do `az sql vm add-to-group` comando adiciona VMs adicionais do SQL Server para o cluster recém-criado. 

O trecho de código a seguir cria o cluster e adiciona a primeira VM do SQL Server a ele: 

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
Use este comando para adicionar quaisquer outras VMs do SQL Server para o cluster, somente modificando o `-n` parâmetro para o nome da VM do SQL Server. 

## <a name="step-4---create-availability-group"></a>Etapa 4: criar o grupo de disponibilidade
Criar manualmente o grupo de disponibilidade, como faria normalmente, usando um [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), ou [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Fazer **não** criar um ouvinte no momento, pois isso é feito por meio da CLI do Azure nas seções a seguir.  

## <a name="step-5---create-internal-load-balancer"></a>Etapa 5: criar o balanceador de carga interno

O ouvinte de AG (grupo) de disponibilidade AlwaysOn exige um balanceador de carga do Azure interno (ILB). O ILB fornece um endereço IP "flutuante" para o ouvinte do grupo de disponibilidade, que oferece failover e reconexão mais rápidos. Se as VMs do SQL Server em um grupo de disponibilidade fizerem parte do mesmo conjunto de disponibilidade, use um balanceador de carga básico; caso contrário, use um balanceador de carga padrão.  **O ILB deve estar na mesma vNet que as instâncias de VM do SQL Server.** 

O trecho de código a seguir cria o balanceador de carga interno:

```azurecli-interactive
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > O recurso de IP público de cada VM do SQL Server deve ter uma SKU padrão para ser compatível com o balanceador de carga padrão. Para determinar a SKU do recurso de IP público da sua VM, navegue até seu **Grupo de recursos**, selecione seu recurso de **Endereço IP público** para a VM do SQL Server desejada e localize o valor em **SKU** no dashboard **Visão geral**.  

## <a name="step-6---create-availability-group-listener"></a>Etapa 6: criar o ouvinte do grupo de disponibilidade
Depois que o grupo de disponibilidade tiver sido criado manualmente, você pode criar o ouvinte usando [ouvinte de grupo de disponibilidade de vm de sql de az](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- O **ID do recurso de sub-rede** é o valor do `/subnets/<subnetname>` acrescentado à ID de recurso do recurso de rede virtual. Para identificar a ID de recurso da sub-rede, faça o seguinte:
   1. Navegue até o grupo de recursos na [portal do Azure](https://portal.azure.com). 
   1. Selecione o recurso de rede virtual. 
   1. Selecione **propriedades** na **configurações** painel. 
   1. Identificar a ID de recurso para a rede virtual e acrescente `/subnets/<subnetname>`ao final para criar a ID do recurso de sub-rede. Por exemplo:
        - Minha ID de recurso de rede virtual é: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Meu nome de sub-rede é `default`.
        - Portanto, é minha ID de recurso da sub-rede: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


O trecho de código a seguir cria o ouvinte do grupo de disponibilidade:

```azurecli-interactive
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>Modificar o número de réplicas no grupo de disponibilidade
Há uma camada adicional de complexidade ao implantar um grupo de disponibilidade em VMs do SQL Server hospedados no Azure, como recursos agora são gerenciados pelo provedor de recursos e pelo `virtual machine group`. Dessa forma, quando adicionar ou remover réplicas para o grupo de disponibilidade, é uma etapa adicional de atualizar os metadados de ouvinte com informações sobre VMs do SQL Server. Portanto, ao modificar o número de réplicas no grupo de disponibilidade, você também deve usar o [atualização do ouvinte do ag do az sql vm grupo](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) comando para atualizar o ouvinte com os metadados das VMs do SQL Server. 


### <a name="add-a-replica"></a>Adicionar uma réplica

Para adicionar uma nova réplica ao grupo de disponibilidade, faça o seguinte:

1. Adicione VM do SQL Server para o cluster:
   ```azurecli-interactive
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Use o SQL Server Management Studio (SSMS) para adicionar a instância do SQL Server como uma réplica no grupo de disponibilidade.
1. Adicione os metadados de VM do SQL Server para o ouvinte:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Remover uma réplica

Para remover uma réplica do grupo de disponibilidade, faça o seguinte:

1. Remova a réplica do grupo de disponibilidade usando o SQL Server Management Studio (SSMS). 
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
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>Remover o ouvinte do grupo de disponibilidade
Se você precisar posteriormente remover ouvinte do grupo de disponibilidade configurado com a CLI do Azure, você deve percorrer o provedor de recursos de VM do SQL. Uma vez que o ouvinte é registrado por meio do provedor de recursos de VM do SQL, apenas excluí-lo por meio do SQL Server Management Studio é insuficiente. Na verdade, deve ser excluído por meio do provedor de recursos de VM do SQL usando a CLI do Azure. Isso remove os metadados do ouvinte do grupo de disponibilidade do provedor de recursos de VM do SQL e exclui fisicamente o ouvinte do grupo de disponibilidade. 

O trecho de código a seguir exclui o ouvinte de grupo de disponibilidade do SQL do provedor de recursos do SQL e do seu grupo de disponibilidade: 

```azurecli-interactive
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira os seguintes artigos: 

* [Visão geral da VM do SQL Server](virtual-machines-windows-sql-server-iaas-overview.md)
* [Perguntas frequentes da VM do SQL Server](virtual-machines-windows-sql-server-iaas-faq.md)
* [Notas de versão da VM do SQL Server](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Alternar modelos de licenciamento para uma VM do SQL Server](virtual-machines-windows-sql-ahb.md)
* [Visão geral dos grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Configuração de uma instância de servidor para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administração de um grupo de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoramento de grupos de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Visão geral de instruções de Transact-SQL para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Visão geral dos Cmdlets do PowerShell para grupos de disponibilidade Always On &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
