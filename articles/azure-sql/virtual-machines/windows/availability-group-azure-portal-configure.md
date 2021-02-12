---
title: Configurar um grupo de disponibilidade (portal do Azure)
description: Use o portal do Azure para criar o cluster de failover do Windows, o ouvinte do grupo de disponibilidade e o balanceador de carga interno em uma VM SQL Server no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/20/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 14760b4244d42e57aaed7f7d96f487a66147a554
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359499"
---
# <a name="use-azure-portal-to-configure-an-availability-group-preview-for-sql-server-on-azure-vm"></a>Use portal do Azure para configurar um grupo de disponibilidade (versão prévia) para SQL Server na VM do Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo descreve como usar o [portal do Azure](https://portal.azure.com) para configurar um grupo de disponibilidade para SQL Server em VMs do Azure. 

Use o portal do Azure para criar um novo cluster ou carregar um cluster existente e, em seguida, crie o grupo de disponibilidade, o ouvinte e o balanceador de carga interno. 

Esse recurso está atualmente na visualização. 

Embora este artigo use o portal do Azure para configurar o ambiente do grupo de disponibilidade, também é possível fazer isso usando [o PowerShell ou o CLI do Azure](availability-group-az-commandline-configure.md), [modelos de início rápido do Azure](availability-group-quickstart-template-configure.md)ou [manualmente](availability-group-manually-configure-tutorial.md) também. 


## <a name="prerequisites"></a>Pré-requisitos

Para configurar um grupo de disponibilidade Always On usando o portal do Azure, você deve ter os seguintes pré-requisitos: 

- Uma [assinatura do Azure](https://azure.microsoft.com/free/).
- Um grupo de recursos com um controlador de domínio. 
- Uma ou mais VMs ingressadas [no domínio no Azure que executam o SQL Server 2016 (ou posterior) Enterprise Edition](./create-sql-vm-portal.md) no *mesmo* conjunto de disponibilidade ou zonas de disponibilidade *diferentes* que foram [registradas com a extensão do SQL IaaS Agent no modo de gerenciamento completo](sql-agent-extension-manually-register-single-vm.md) e estão usando a mesma conta de domínio para o serviço de SQL Server em cada VM.
- Dois endereços IP disponíveis (não utilizados por nenhuma entidade). Um é para o balanceador de carga interno. O outro é para o ouvinte do grupo de disponibilidade na mesma sub-rede que o grupo de disponibilidade. Se você estiver usando um balanceador de carga existente, precisará apenas de um endereço IP disponível para o ouvinte do grupo de disponibilidade. 

## <a name="permissions"></a>Permissões

Você precisa das seguintes permissões de conta para configurar o grupo de disponibilidade usando o portal do Azure: 

- Uma conta de usuário de domínio existente com permissão para **Criar Objeto de Computador** no domínio. Por exemplo, uma conta de administrador do domínio normalmente tem permissão suficiente (por exemplo: account@domain.com). _Essa conta também deve fazer parte do grupo administrador local em cada VM para criar o cluster._
- A conta de usuário do domínio que controla o SQL Server. Essa deve ser a mesma conta para cada SQL Server VM que você pretende adicionar ao grupo de disponibilidade. 

## <a name="configure-cluster"></a>Configurar cluster

Configure o cluster usando o portal do Azure. Você pode criar um novo cluster ou, se já tiver um cluster existente, poderá integrá-lo à extensão do agente IaaS do SQL para a capacidade de gerenciamento do Portal.


### <a name="create-a-new-cluster"></a>Criar um cluster

Se você já tiver um cluster, pule esta seção e mova para a [integração do cluster existente](#onboard-existing-cluster) . 

Se você ainda não tiver um cluster existente, crie-o usando o portal do Azure com estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até o recurso de [máquinas virtuais do SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecione **alta disponibilidade** em **configurações**. 
1. Selecione **+ novo cluster de failover do Windows Server** para abrir a página **Configurar cluster de failover do Windows** .  

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-cluster.png" alt-text="Criar novo cluster selecionando o novo cluster no portal":::

1. Nomeie o cluster e forneça uma conta de armazenamento para usar como a testemunha de nuvem. Use uma conta de armazenamento existente ou selecione **criar nova** para criar uma nova conta de armazenamento. O nome da conta de armazenamento deve ter entre 3 e 24 caracteres, usar números e apenas letras minúsculas.

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-1.png" alt-text="Forneça o nome, a conta de armazenamento e as credenciais para o cluster":::

1. Expanda **as credenciais do cluster de failover do Windows Server** para fornecer [credenciais](/rest/api/sqlvm/sqlvirtualmachinegroups/createorupdate#wsfcdomainprofile) para a conta de serviço SQL Server, bem como o operador de cluster e as contas de inicialização se elas forem diferentes da conta usada para o serviço SQL Server. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-cluster-2.png" alt-text="Forneça credenciais para a conta de serviço do SQL, a conta de operador de cluster e a conta de inicialização de cluster":::

1. Selecione as VMs SQL Server que você deseja adicionar ao cluster. Observe se uma reinicialização é necessária ou não, e continue com cuidado. Somente as VMs registradas com a extensão do SQL IaaS Agent no modo de gerenciamento completo e estão no mesmo local, domínio e na mesma rede virtual que a VM de SQL Server primária estarão visíveis. 
1. Selecione **aplicar** para criar o cluster. Você pode verificar o status de sua implantação no **log de atividades** , que é acessível do ícone de sino na barra de navegação superior. 
1. Para que um cluster de failover seja suportado pela Microsoft, ele deve passar na validação do cluster. Conecte-se à VM usando seu método preferido (como protocolo RDP (RDP)) e valide se o cluster passa pela validação antes de continuar. Deixar de fazer isso deixa o cluster em um estado sem suporte. Você pode validar o cluster usando Gerenciador de Cluster de Failover (FCM) ou o seguinte comando do PowerShell:

    ```powershell
    Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
    ```
    


### <a name="onboard-existing-cluster"></a>Carregar cluster existente

Se você já tiver um cluster configurado em seu ambiente de VM SQL Server, poderá integrá-lo da portal do Azure.

Para fazer isso, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até o recurso de [máquinas virtuais do SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecione **alta disponibilidade** em **configurações**. 
1. Selecione **integrado cluster de failover do Windows Server existente** para abrir a página **integrado do cluster de failover do Windows Server** . 

   :::image type="content" source="media/availability-group-az-portal-configure/onboard-existing-cluster.png" alt-text="Carregar um cluster existente da página de alta disponibilidade no recurso de máquinas virtuais do SQL":::

1. Examine as configurações do cluster. 
1. Selecione **aplicar** para carregar o cluster e, em seguida, selecione **Sim** no prompt para continuar.

## <a name="create-availability-group"></a>Criar grupo de disponibilidade

Depois que o cluster foi criado ou integrado, crie o grupo de disponibilidade usando o portal do Azure. Para fazer isso, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até o recurso de [máquinas virtuais do SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecione **alta disponibilidade** em **configurações**. 
1. Selecione **+ novo Always on grupo de disponibilidade** para abrir a página **Criar grupo de disponibilidade** .

   :::image type="content" source="media/availability-group-az-portal-configure/create-new-availability-group.png" alt-text="Selecione novo grupo de disponibilidade AlwaysOn para abrir a página Criar grupo de disponibilidade.":::

1. Insira um nome para o grupo de disponibilidade. 
1. Selecione **Configurar ouvinte** para abrir a página **Configurar ouvinte do grupo de disponibilidade** . 

   :::image type="content" source="media/availability-group-az-portal-configure/create-availability-group.png" alt-text="Forneça um nome para o grupo de disponibilidade e configure um ouvinte":::

1. Preencha os valores e use um balanceador de carga existente ou selecione **criar novo** para criar um novo balanceador de carga.  Selecione **aplicar** para salvar as configurações e criar o ouvinte e o balanceador de carga. 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-new-listener.png" alt-text="Preencha os valores no formulário para criar o novo ouvinte e o balanceador de carga":::

1. Escolha **+ selecionar réplica** para abrir a página **Configurar réplicas do grupo de disponibilidade** .
1. Selecione as máquinas virtuais que você deseja adicionar ao grupo de disponibilidade e escolha as configurações do grupo de disponibilidade que melhor atendam às suas necessidades de negócios. Selecione **aplicar** para salvar suas configurações. 

   :::image type="content" source="media/availability-group-az-portal-configure/add-replicas.png" alt-text="Escolha as VMs a serem adicionadas ao seu grupo de disponibilidade e defina as configurações apropriadas para seu negócio":::

1. Verifique suas configurações de grupo de disponibilidade e, em seguida, selecione **aplicar** para criar seu grupo de disponibilidade. 

Você pode verificar o status de sua implantação no **log de atividades** , que é acessível do ícone de sino na barra de navegação superior. 

  > [!NOTE]
  > A **integridade da sincronização** na página de **alta disponibilidade** do portal do Azure será mostrada como **não íntegra** até que você adicione bancos de dados ao seu grupo de disponibilidade. 


## <a name="add-database-to-availability-group"></a>Adicionar Banco de dados ao grupo de disponibilidade

Adicione seus bancos de dados ao seu grupo de disponibilidade após a conclusão da implantação. As etapas a seguir usam SQL Server Management Studio (SSMS), mas você também pode usar o [Transact-SQL ou o PowerShell](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) . 

Para adicionar bancos de dados ao seu grupo de disponibilidade usando SQL Server Management Studio, siga estas etapas:

1. Conecte-se a uma de suas VMs SQL Server usando seu método preferido, como Conexão de Área de Trabalho Remota (RDP). 
1. Abra o SSMS (SQL Server Management Studio).
1. Conecte-se à sua instância do SQL Server. 
1. Expanda **Always on alta disponibilidade** no **pesquisador de objetos**.
1. Expanda **grupos de disponibilidade**, clique com o botão direito do mouse no grupo de disponibilidade e escolha **Adicionar Banco de dados...**.

   :::image type="content" source="media/availability-group-az-portal-configure/add-database.png" alt-text="Clique com o botão direito do mouse no grupo de disponibilidade no Pesquisador de objetos e escolha Adicionar Banco de dados":::

1. Siga os prompts para selecionar os bancos de dados que você deseja adicionar ao seu grupo de disponibilidade. 
1. Selecione **OK** para salvar as configurações e adicionar seu banco de dados ao grupo de disponibilidade. 
1. Depois que o banco de dados for adicionado, atualize o **pesquisador de objetos** para confirmar o status do banco de dados como `synchronized` . 

Depois que os bancos de dados são adicionados, você pode verificar o status do seu grupo de disponibilidade no portal do Azure: 

:::image type="content" source="media/availability-group-az-portal-configure/healthy-availability-group.png" alt-text="Verifique o status do seu grupo de disponibilidade na página de alta disponibilidade do portal do Azure depois que os bancos de dados forem sincronizados":::

## <a name="add-more-vms"></a>Adicionar mais VMs

Para adicionar mais SQL Server VMs ao cluster, siga estas etapas: 

1. Faça logon no [Portal do Azure](https://portal.azure.com). 
1. Navegue até o recurso de [máquinas virtuais do SQL](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) . 
1. Selecione **alta disponibilidade** em **configurações**. 
1. Selecione **Configurar cluster de failover do Windows Server** para abrir a página **Configurar cluster de failover do Windows Server** . 

   :::image type="content" source="media/availability-group-az-portal-configure/configure-existing-cluster.png" alt-text="Selecione configurar cluster de failover do Windows Server para adicionar VMs ao cluster.":::

1. Expanda **as credenciais do cluster de failover do Windows Server** e insira nas contas usadas para o serviço de SQL Server, o operador de cluster e as contas de inicialização de cluster. 
1. Selecione as VMs SQL Server que você deseja adicionar ao cluster. 
1. Selecione **Aplicar**. 

Você pode verificar o status de sua implantação no **log de atividades** , que é acessível do ícone de sino na barra de navegação superior. 


## <a name="modify-availability-group"></a>Modificar Grupo de disponibilidade 


Você pode **adicionar mais réplicas** ao grupo de disponibilidade, **Configurar o ouvinte** ou **excluir o ouvinte** da página de **alta disponibilidade** no portal do Azure selecionando as reticências (...) ao lado do seu grupo de disponibilidade: 

:::image type="content" source="media/availability-group-az-portal-configure/configure-listener.png" alt-text="Selecione as reticências ao lado do grupo de disponibilidade e, em seguida, selecione Adicionar réplica para adicionar mais réplicas ao grupo de disponibilidade.":::

## <a name="remove-cluster"></a>Remover cluster

Remova todas as SQL Server VMs do cluster para destruí-las e, em seguida, remova os metadados do cluster da extensão do agente IaaS do SQL. Você pode fazer isso usando a versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli) ou do PowerShell. 

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Primeiro, remova todas as VMs SQL Server do cluster. Isso removerá fisicamente os nós do cluster e destruirá o cluster:  

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

az sql vm group delete --name <cluster name> --resource-group <resource group name>
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

Remove-AzSqlVMGroup -ResourceGroupName "<resource group name>" -Name "<cluster name>"
```

---

## <a name="troubleshooting"></a>Solução de problemas

Se você tiver problemas, poderá verificar o histórico de implantação e examinar os erros comuns, bem como suas resoluções. 

### <a name="check-deployment-history"></a>Verificar histórico de implantação

As alterações no cluster e no grupo de disponibilidade por meio do portal são feitas por meio de implantações. O histórico de implantação pode fornecer mais detalhes se houver problemas com a criação ou a integração do cluster ou com a criação do grupo de disponibilidade.

Para exibir os logs da implantação e verificar o histórico de implantação, siga estas etapas:

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Navegue até o grupo de recursos.
1. Selecione **Implantações** em **Configurações**.
1. Selecione a implantação de interesse para saber mais sobre a implantação. 


   :::image type="content" source="media/availability-group-az-portal-configure/failed-deployment.png" alt-text="Selecione a implantação em que você está interessado em saber mais." :::

### <a name="common-errors"></a>Erros comuns

Examine os seguintes erros comuns e suas resoluções. 

#### <a name="the-account-which-is-used-to-start-up-sql-service-is-not-a-domain-account"></a>A conta que é usada para inicializar o serviço SQL não é uma conta de domínio

Essa é uma indicação de que o provedor de recursos não pôde acessar o serviço de SQL Server com as credenciais fornecidas. Algumas resoluções comuns:
- Verifique se o controlador de domínio está em execução.
- Valide se as credenciais fornecidas no portal correspondem às do serviço de SQL Server. 


## <a name="next-steps"></a>Próximas etapas


Para obter mais informações sobre grupos de disponibilidade, consulte:

- [Visão geral dos grupos de disponibilidade](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
* [Administração de um grupo de disponibilidade](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Monitoramento de grupos de disponibilidade &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Instruções Transact-SQL do grupo de disponibilidade ](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Comandos do PowerShell de grupos de disponibilidade](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  

Para obter mais informações sobre VMs SQL Server, consulte: 

* [Visão geral das VMs do SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Notas de versão para VMs do SQL Server](../../database/doc-changes-updates-release-notes.md)
* [Perguntas frequentes para VMs do SQL Server](frequently-asked-questions-faq.md)
