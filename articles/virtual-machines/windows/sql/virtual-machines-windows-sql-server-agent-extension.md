---
title: Automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a extensão do SQL Server IaaS Agent | Microsoft Docs
description: Este artigo descreve como gerenciar a extensão do agente do SQL Server, que automatiza tarefas de administração específicas do SQL Server. Entre elas estão o Backup Automatizado, a Aplicação de Patch Automatizada e a Integração do Cofre de Chaves do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41023103dc30d16f599e847f9d324bc7bb4be11c
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798048"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-iaas-agent-extension"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a extensão do SQL Server IaaS Agent
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

A extensão do SQL Server IaaS Agent (SqlIaasExtension) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este artigo fornece uma visão geral e serviços compatíveis com a extensão, bem como instruções de instalação, status e remoção.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Para exibir a versão clássica deste artigo, confira [Extensão do SQL Server Agent para VMs do SQL Server (Clássico)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Há três modos de capacidade de gerenciamento do SQL para a extensão SQL IaaS: **Completa**, **Lightweight**, e **NoAgent**. 

- **Completo** modo oferece toda a funcionalidade, mas requer uma reinicialização das permissões do SQL Server e SA. Essa é a opção que é instalada por padrão e deve ser usada para gerenciar uma VM com uma única instância do SQL Server. 

- **Lightweight** não requer a reinicialização do SQL Server, mas só oferece suporte à alteração do tipo de licença e a edição do SQL Server. Esta opção deve ser usado para VMs do SQL Server com várias instâncias, ou participando de uma instância de cluster de failover (FCI). 

- **NoAgent** é dedicado para o SQL Server 2008 e SQL Server 2008 R2, instalado no Windows Server 2008. Para obter informações sobre como utilizar `NoAgent` modo para a sua imagem do Windows Server 2008, consulte [registro do Windows Server 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms). 

## <a name="supported-services"></a>Serviços com suporte
A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | DESCRIÇÃO |
| --- | --- |
| **Backup Automatizado do SQL** |Automatiza o agendamento de backups para todos os bancos de dados para uma instância padrão ou um [corretamente instalado](virtual-machines-windows-sql-server-iaas-faq.md#administration) instância nomeada do SQL Server na VM. Para saber mais, veja [Backup automatizado para SQL Server em máquinas virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-backup.md). |
| **Aplicação de patch automatizada do SQL** |Configura uma janela de manutenção durante a qual as atualizações importantes do Windows para a VM podem ocorrer, evitando atualizações da carga de trabalho durante horários de pico. Para saber mais, veja [Aplicação de patch automatizada para o SQL Server em Máquinas Virtuais do Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integração do Cofre da Chave do Azure** |Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a integração do Cofre de Chaves do Azure para SQL Server em VMs do Azure (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md). |

Uma vez instalada e em execução, a Extensão do Agente IaaS do SQL Server disponibiliza esses recursos de administração no painel do SQL Server da máquina virtual no portal do Azure e através do Azure PowerShell para as imagens do marketplace do SQL Server e, através do Azure PowerShell para as instalações manuais da extensão. 

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para uso da extensão SQL Server IaaS Agent em sua VM:

**Sistema operacional**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**Versões do SQL Server**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* Microsoft SQL Server 2017

**Azure PowerShell**:

* [Baixar e configurar os comandos mais recentes do Azure PowerShell](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Alterar os modos de gerenciamento

Você pode exibir o modo atual do seu agente SQL IaaS usando o PowerShell: 

  ```powershell-interactive
     //Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

Para VMs do SQL Server que têm o *NoAgent* ou *leve* extensão IaaS do instalado, você pode atualizar o modo de ser *completo* usando o portal do Azure. Não é possível fazer downgrade - para fazer isso, você precisará desinstalar a extensão SQL IaaS completamente e instalá-lo novamente. 

Para o modo de agente de atualização para *completo*, faça o seguinte: 

1. Faça logon no [Portal do Azure](https://portal.azure.com).
1. Navegue até sua [máquinas virtuais do SQL](virtual-machines-windows-sql-manage-portal.md#access-sql-virtual-machine-resource) recursos. 
1. Selecione sua máquina de virtual do SQL Server e selecione **visão geral**. 
1. Para VMs do SQL com o *NoAgent* ou *leve* modos de IaaS, selecione a mensagem para **somente atualizações de edição e de tipo de licença estão disponíveis com a extensão SQL IaaS**.

    ![Iniciar a alteração do modo do portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Concorde com **reinicie o serviço do SQL Server** , selecionando a caixa de seleção e, em seguida, selecione **confirmar** para atualizar o seu modo de IaaS como 'full'. 

    ![Habilitar o gerenciamento completo para a extensão IaaS](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

##  <a name="installation"></a>Instalação
A extensão IaaS do SQL é instalada quando você registra sua VM do SQL Server com o [provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider). No entanto, se necessário, o agente IaaS do SQL também pode ser instalado manualmente usando *completo* ou *leve* instalação no modo. 

O *completo* a extensão do SQL Server IaaS Agent é instalado automaticamente quando você provisiona uma das imagens de galeria da máquina virtual do SQL Server usando o portal do Azure. 

### <a name="full-mode-installation"></a>Instalação no modo completo
O *completo* extensão SQL IaaS oferece a capacidade de gerenciamento completa para uma única instância na VM do SQL Server. Se houver uma instância padrão, em seguida, a extensão funcionará com a instância padrão e ele não oferecerá suporte a gerenciamento de outras instâncias. Se não houver nenhuma instância padrão, mas apenas uma instância nomeada, em seguida, ele irá gerenciar a instância nomeada. Se não houver nenhuma instância padrão e houver várias instâncias nomeadas, a extensão conseguirá instalar. 

Instalando o *completo* modo de SQL IaaS reiniciará o serviço do SQL Server. Para evitar a reinicialização do serviço do SQL Server, instale o *leve* modo com limitado a capacidade de gerenciamento em vez disso. 

Instalar o agente IaaS do SQL com *completo* modo usando o PowerShell:

  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Full' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parâmetro | Valores aceitáveis                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, ou `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


> [!WARNING]
> - Se a extensão já não estiver instalada, instalar o **completo** extensão reinicia o serviço do SQL Server. Use **leve** modo para evitar reiniciar o serviço SQL Server. 
> - Atualizando a extensão SQL IaaS não reinicia o serviço SQL Server. 

#### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Instalar em uma VM com uma única instância nomeada do SQL Server
A extensão SQL IaaS funcionará com uma instância nomeada em um SQL Server se a instância padrão é desinstalada e a extensão IaaS é reinstalada.

Para usar uma instância nomeada do SQL Server, faça o seguinte:
   1. Implante uma VM do SQL Server do marketplace. 
   1. Desinstalar a extensão IaaS de dentro de [portal do Azure](https://portal.azure.com).
   1. Desinstale o SQL Server completamente dentro de VM do SQL Server.
   1. Instale o SQL Server com uma instância nomeada em VM do SQL Server. 
   1. Instale a extensão de IaaS de dentro do portal do Azure.  


### <a name="install-in-lightweight-mode"></a>Instalar no modo leve
Modo leve não reiniciará o serviço do SQL Server, mas oferece funcionalidade limitada. 

Instalar o agente IaaS do SQL com *leve* modo usando o PowerShell:


  ```powershell-interactive
     // Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     // Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parâmetro | Valores aceitáveis                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `'AHUB'`, ou `'PAYG'`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-status-of-sql-iaas-extension"></a>Obter Status da extensão SQL IaaS
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no portal do Azure. Escolha **Todas as configurações** na janela da máquina virtual e clique em **Extensões**. Você deverá ver a extensão **SqlIaasExtension** na lista.

![Extensão do Agente IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzVMSqlServerExtension** do Azure PowerShell.

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

O comando anterior confirma se o agente está instalado e fornece informações gerais sobre o status. Você também pode obter informações específicas de status do Backup automatizado e aplicação de patch com os comandos a seguir.

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Remoção
No portal do Azure, você pode desinstalar a extensão clicando no botão de reticências na **extensões** janela de propriedades de sua máquina virtual. Em seguida, clique em **Excluir**.

![Desinstalar a Extensão do Agente IaaS do SQL Server no portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet **Remove-AzVMSqlServerExtension** do PowerShell.

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Próximas etapas
Comece a usar um dos serviços com suporte da extensão. Para obter mais informações, consulte os artigos citados na [serviços com suporte](#supported-services) seção deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, veja [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

