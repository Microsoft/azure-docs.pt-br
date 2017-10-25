---
title: "Automatizar tarefas de gerenciamento Em VMs do SQL (Clássicas) | Microsoft Docs"
description: "Este tópico descreve como gerenciar a extensão do agente do SQL Server, que automatiza tarefas de administração específicas do SQL Server. Entre elas estão o Backup Automatizado, a Aplicação de Patch Automatizada e a Integração do Cofre de Chaves do Azure. Este tópico usa o modo de implantação clássico."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a9bda2e7-cdba-427c-bc30-77cde4376f3a
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/05/2017
ms.author: jroth
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 30fa9128cd51a7498449c991b58500ad9acdd3d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-management-tasks-on-azure-virtual-machines-with-the-sql-server-agent-extension-classic"></a>Automatizar tarefas de gerenciamento em máquinas virtuais do Azure com a Extensão do Agente do SQL Server (Clássica)
> [!div class="op_single_selector"]
> * [Gerenciador de Recursos](../sql/virtual-machines-windows-sql-server-agent-extension.md)
> * [Clássico](../classic/sql-server-agent-extension.md)
> 
>
 
A Extensão de Agente IaaS do SQL Server (SQLIaaSAgent) é executada em máquinas virtuais do Azure para automatizar tarefas de administração. Este tópico fornece uma visão geral dos serviços compatível com a extensão, bem como instruções de instalação, status e remoção.

> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de Recursos e Clássico](../../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o uso do modelo de implantação Clássica. A Microsoft recomenda que a maioria das implantações novas use o modelo do Gerenciador de Recursos. Para exibir a versão do Gerenciador de Recursos deste artigo, consulte [Extensão do SQL Server Agent para o Resource Manager de VMs do SQL Server](../sql/virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços com suporte
A Extensão do Agente IaaS do SQL Server dá suporte às seguintes tarefas de administração:

| Recurso de administração | Descrição |
| --- | --- |
| **Backup Automatizado do SQL** |Automatiza o agendamento de backups para todos os bancos de dados da instância padrão do SQL Server na VM. Para saber mais, veja [Backup automatizado para o SQL Server em Máquinas Virtuais do Azure (Clássico)](../classic/sql-automated-backup.md). |
| **Aplicação de patch automatizada do SQL** |Configura um período de manutenção durante o qual as atualizações para sua VM podem ocorrer, evitando atualizações durante horários de pico de sua carga de trabalho. Para obter mais informações, confira [Aplicação de patch automatizada para SQL Server em máquinas virtuais do Azure (Clássico)](../classic/sql-automated-patching.md). |
| **Integração do Cofre da Chave do Azure** |Permite a instalação e configuração automática do Cofre de Chaves do Azure em sua VM do SQL Server. Para saber mais, confira [Configurar a Integração do Cofre de Chaves do Azure para o SQL Server em VMs do Azure (Clássico)](../classic/ps-sql-keyvault.md). |

## <a name="prerequisites"></a>Pré-requisitos
Requisitos para uso da extensão SQL Server IaaS Agent em sua VM:

### <a name="operating-system"></a>Sistema operacional:
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

### <a name="sql-server-versions"></a>Versões do SQL Server:
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016

### <a name="azure-powershell"></a>PowerShell do Azure:
[Baixe e configure os comandos mais recentes do Azure PowerShell](/powershell/azure/overview).

Em seguida, conecte o Windows PowerShell à sua assinatura do Azure usando o comando **Add-AzureAccount** .

    Add-AzureAccount

Se você tiver várias assinaturas, deverá usar **Select-AzureSubscription** para selecionar a assinatura contendo o destino de VM clássico.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste ponto você pode, por meio do comando **Get-AzureVM** , obter uma lista de máquinas virtuais clássicas e seus nomes de serviço associados.

    Get-AzureVM

## <a name="installation"></a>Instalação
Para VMs clássicas, você deve usar o PowerShell para instalar a extensão do agente do SQL Server IaaS e configurar seus serviços associados. Use o cmdlet **Set-AzureVMSqlServerExtension** do PowerShell para instalar a extensão. Por exemplo, o comando a seguir instala a extensão em uma VM do Windows Server (clássico) e fornece o nome "SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se você atualizar para a versão mais recente da Extensão do Agente IaaS SQL, deverá reiniciar a máquina virtual depois de atualizar a extensão.

> [!NOTE]
> Máquinas virtuais clássicas não têm uma opção para instalar e configurar a extensão do agente SQL IaaS através do portal.
> 
> 

## <a name="status"></a>Status
Uma maneira de verificar se a extensão está instalada é exibir o status do agente no Portal do Azure. Selecione uma máquina virtual listada na folha da máquina virtual e, em seguida, clique em **Extensões**. Você deverá ver a extensão **SQLIaaSAgent** na lista.

![Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Você também pode usar o cmdlet **Get-AzureVMSqlServerExtension** do Azure PowerShell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Remoção
No Portal do Azure, você pode desinstalar a extensão clicando nas reticências na folha **Extensões** das propriedades de sua máquina virtual. Em seguida, clique em **Desinstalar**.

![Desinstalar a Extensão do Agente IaaS do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Você também pode usar o cmdlet **Remove-AzureVMSqlServerExtension** do Powershell.

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Próximas etapas
Comece a usar um dos serviços com suporte da extensão. Para obter mais detalhes, confira os tópicos citados na seção [Serviços com suporte](#supported-services) deste artigo.

Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, veja [Visão geral do SQL Server em Máquinas Virtuais do Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

