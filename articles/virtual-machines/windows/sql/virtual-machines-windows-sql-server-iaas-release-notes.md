---
title: SQL Server nas notas de versão VM do Azure | Microsoft Docs
description: Aprenda sobre os novos recursos e aprimoramentos do SQL Server em uma VM do Azure
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
manager: craigg
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 2/13/2019
ms.openlocfilehash: e7dea69b507117ba8a3765b5e9bc1fad46be4c53
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444200"
---
# <a name="sql-server-on-azure-virtual-machine-release-notes"></a>Notas de versão do SQL Server no Virtual Machine do Azure

O Azure permite implantar uma máquina virtual com uma imagem do SQL Server incorporada. Este artigo resume os novos recursos e melhorias nas versões recentes do [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). O artigo também lista importantes atualizações de conteúdo que não estão diretamente vinculadas à versão, mas foram publicadas no mesmo período. Para encontrar melhorias em outros serviços do Azure, confira [Atualizações de serviço](https://azure.microsoft.com/updates)


## <a name="june-2019"></a>Junho de 2019

### <a name="service-improvements"></a>Melhorias nos serviços

| Melhorias nos serviços | Detalhes |
| --- | --- |
| **Novos modos de instalação do SQL IaaS** | Agora é possível instalar a extensão SQL IaaS no [modo leve](virtual-machines-windows-sql-server-agent-extension.md) para evitar a reinicialização do serviço SQL Server.  |
| **Modificação da edição do SQL Server** | Agora você pode alterar a [propriedade de edição](virtual-machines-windows-sql-change-edition.md) para sua VM SQL Server. |
| **Alterações de RP da VM do SQL** | Você pode [registrar sua VM de SQL Server com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) -até mesmo [imagens do Windows 2008](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008r2-on-windows-server-2008-vms) – usando os novos modos de IaaS do SQL. |
| **BYOL imagens usando AHUB** | As imagens BYOL implantadas no Marketplace agora podem mudar seu [tipo de licença para ' PAYG '](virtual-machines-windows-sql-ahb.md#remarks).| 
| &nbsp; | &nbsp; |


## <a name="may-2019"></a>Maio de 2019

### <a name="service-improvements"></a>Melhorias nos serviços

| Melhorias nos serviços | Detalhes |
| --- | --- |
| **Novo gerenciamento de VM do SQL no portal do Azure** | Agora há uma nova maneira de gerenciar sua VM SQL Server no portal do Azure. Para obter mais informações, consulte [gerenciar SQL Server VM no portal do Azure](virtual-machines-windows-sql-manage-portal.md).  |
| &nbsp; | &nbsp; |

### <a name="documentation-improvements"></a>Melhorias na documentação

| Documentação | Detalhes |
| --- | --- |
| **Novo gerenciamento do portal de VM do SQL** | Cerca de dezenas de artigos foram atualizados para a nova experiência do portal de gerenciamento de VM do SQL. | 
| &nbsp; | &nbsp; |




## <a name="april-2019"></a>Abril de 2019

### <a name="service-improvements"></a>Melhorias nos serviços

| Melhorias nos serviços | Detalhes |
| --- | --- |
| **Estender o suporte para SQL Server 2008/2008R2** | [Estenda o suporte](virtual-machines-windows-sql-server-2008-eos-extend-support.md) para SQL Server 2008 e SQL Server 2008 R2 migrando *o estado em que se encontra* para uma VM do Azure. | 
| &nbsp; | &nbsp; |


## <a name="march-2019"></a>Março de 2019

| Melhorias nos serviços | Detalhes |
| --- | --- |
| **Suporte a imagens personalizadas** | Agora você pode instalar a [extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md#installation) para imagens do sistema operacional personalizado e do SQL, que oferece a funcionalidade limitada de [licenciamento flexível](virtual-machines-windows-sql-ahb.md). Ao registrar sua imagem personalizada com o provedor de recursos do SQL, especifique o tipo de licença como ' AHUB ', como caso contrário, o registro falhará. | 
| **Suporte à instância nomeada** | Agora você pode utilizar a [extensão de IaaS do SQL](virtual-machines-windows-sql-server-agent-extension.md#installation) com uma instância nomeada, se a instância padrão tiver sido desinstalada corretamente. | 
| **Aprimoramento do portal** | A experiência de portal do Azure para implantar uma VM SQL Server foi renovada para melhorar a usabilidade. Para obter mais informações, consulte o breve [início rápido](quickstart-sql-vm-create-portal.md) e o guia de [instruções](virtual-machines-windows-portal-sql-server-provision.md) mais abrangentes para implantar uma VM SQL Server.|
| &nbsp; | &nbsp; |


## <a name="february-2019"></a>fevereiro de 2019

| Melhorias nos serviços | Detalhes |
| --- | --- |
| **Aprimoramento do portal** | Agora é possível alterar o modelo de licenciamento de uma VM SQL Server de pré-pago para o traga sua própria licença usando o [portal do Azure](virtual-machines-windows-sql-ahb.md#change-license-for-vms-already-registered-with-resource-provider)|
|**Simplificação da implantação do AG com a CLI de VM do SQL do Azure** | Agora é mais fácil do que nunca implantar um grupo de disponibilidade em uma VM SQL Server no Azure. A [CLI de VM do SQL do Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid) permite que você crie o ouvinte WSFC, ILB e AG da linha de comando e no horário do registro! Para obter mais informações, consulte [usar a CLI de VM do SQL do Azure para configurar Always on grupo de disponibilidade para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-cli.md). | 
| &nbsp; | &nbsp; |


## <a name="december-2018"></a>Dezembro de 2018

| Melhorias nos serviços | Detalhes |
| --- | --- |
| **Novo provedor de recursos de grupo de clusters do SQL** | Um novo provedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) que define os metadados do Cluster de Failover do Windows. Ingressar em uma VM do SQL Server para o *SqlVirtualMachineGroups* inicializa o serviço de Cluster de Failover do Windows e ingressa a VM no cluster.  |
|**Automatizar a configuração de uma implantação de grupo de disponibilidade com modelos de Início Rápido do Azure** |Agora é possível criar o Cluster de Failover do Windows, ingressar VMs do SQL Server a ele, criar o ouvinte e configurar o Internal Load Balancer com dois modelos de Início Rápido do Azure. Para obter mais informações, consulte [usar o modelo de início rápido do Azure para configurar Always on grupo de disponibilidade para SQL Server em uma VM do Azure](virtual-machines-windows-sql-availability-group-quickstart-template.md). | 
| **Registro automático do provedor de recursos de VM do SQL** | VMs do SQL Server implantadas depois deste mês são automaticamente registradas com o novo provedor de recursos do SQL Server. As VMs do SQL Server implantadas antes deste mês ainda precisarão ser registradas manualmente. Para obter mais informações, consulte [registrar VM do SQL existente com o provedor de recursos da VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).|
| &nbsp; | &nbsp; |


## <a name="november-2018"></a>Novembro de 2018

| Melhorias nos serviços | Detalhes |
| --- | --- |
| **Novo provedor de recursos de VM do SQL** |  Um novo provedor de recursos para VMs do SQL Server (Microsoft.SqlVirtualMachine) que fornece um melhor gerenciamento da VM do SQL Server. Para obter mais informações sobre como registrar sua VM, consulte [Registrar a VM do SQL existente com o novo provedor de recursos](virtual-machines-windows-sql-register-with-resource-provider.md). |
|**Alternar o modelo de licenciamento** | Agora você pode alternar entre o modelo de pagamento por uso e o de trazer sua própria licença para sua VM do SQL usando a CLI do Azure ou o PowerShell do Azure. Para saber mais, confira [Como alterar o modelo de licenciamento de uma VM do SQL](virtual-machines-windows-sql-ahb.md). | 
| &nbsp; | &nbsp; |


## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md).
* [Provisionar uma VM do Windows do SQL Server](virtual-machines-windows-portal-sql-server-provision.md)
* [Migração de um banco de dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md)
* [Alta disponibilidade e recuperação de desastres para SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
* [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
* [Estratégias de Desenvolvimento e Padrões de Aplicativo para o SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)
* [Provisionar uma máquina virtual Linux do SQL Server](../../linux/sql/provision-sql-server-linux-virtual-machine.md)
* [Perguntas Frequentes (Linux)](../../linux/sql/sql-server-linux-faq.md)
* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview)
