---
title: Alterações na documentação das máquinas virtuais do SQL Server no Azure
description: Saiba mais sobre os novos recursos e aprimoramentos para diferentes versões do SQL Server em máquinas virtuais do Azure.
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: ff4e6e0451b57046fb8f07f5a1051235e1f6d0f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325716"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Alterações na documentação das máquinas virtuais do SQL Server no Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

O Azure permite implantar uma VM (máquina virtual) com uma imagem do SQL Server inserida. Este artigo resume as alterações na documentação associadas aos novos recursos e aprimoramentos nas versões recentes das [Máquinas Virtuais do SQL Server no Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). 

## <a name="october-2020"></a>Outubro de 2020

| Alterações | Detalhes |
| --- | --- |
| **DNN para AG** | Agora você pode configurar um [ouvinte de DNN (nome de rede distribuída)](availability-group-distributed-network-name-dnn-listener-configure.md) para SQL Server 2019 CU8 e posterior para substituir o [ouvinte VNN](availability-group-overview.md#connectivity)tradicional, negando a necessidade de um Azure Load Balancer.   | 

## <a name="september-2020"></a>Setembro de 2020

| Alterações | Detalhes |
| --- | --- |
| **Registro de extensão automática** | Agora você pode habilitar o recurso de [registro automático](sql-agent-extension-automatic-registration-all-vms.md) para registrar automaticamente todas as VMs SQL Server já implantadas em sua assinatura com a [extensão do SQL IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Isso se aplica a todas as VMs existentes e também registrará automaticamente todas as VMs SQL Server adicionadas no futuro.   | 


## <a name="august-2020"></a>Agosto de 2020

| Alterações | Detalhes |
| --- | --- |
| **Configurar o AG no portal** | Agora é possível [configurar seu grupo de disponibilidade por meio do portal do Azure](availability-group-azure-portal-configure.md). Este recurso está atualmente em visualização e sendo implantado, portanto, se a região desejada não estiver disponível, verifique novamente em breve. | 


## <a name="july-2020"></a>Julho de 2020


| Alterações | Detalhes |
| --- | --- |
| **Migrar log para ultra Disk** | Saiba como você pode [migrar seu arquivo de log para um ultra Disk](storage-migrate-to-ultradisk.md) para aproveitar o alto desempenho e baixa latência. | 
| **Criar AG usando Azure PowerShell** | Agora é possível simplificar a criação de um grupo de disponibilidade usando [Azure PowerShell](availability-group-az-commandline-configure.md) , bem como o CLI do Azure. | 


## <a name="june-2020"></a>Junho de 2020

| Alterações | Detalhes |
| --- | --- |
| **DNN (Nome de rede distribuída)** | SQL Server 2019 no Windows Server 2016 + agora está visualizando o suporte para roteamento de tráfego para a FCI (instância de cluster de failover) usando um [nome de rede distribuída](./failover-cluster-instance-distributed-network-name-dnn-configure.md) em vez de usar Azure Load Balancer. Esse suporte simplifica e simplifica a conexão à sua solução HA (alta disponibilidade) no Azure. | 
| **FCI com discos compartilhados do Azure** | Agora é possível implantar a [FCI (instância de cluster de failover)](failover-cluster-instance-overview.md) usando os [discos compartilhados do Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md). |
| **Documentos reorganizados do FCI** | A documentação sobre as [instâncias de cluster de failover com SQL Server em VMs do Azure](failover-cluster-instance-overview.md) foi reescrita e reorganizada para fins de clareza. Separamos alguns dos conteúdos de configuração, como as [práticas recomendadas de configuração de cluster](hadr-cluster-best-practices.md), como preparar uma [máquina virtual para um SQL Server FCI](failover-cluster-instance-prepare-vm.md)e como configurar [Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md). | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>Maio de 2020 

| Alterações | Detalhes |
| --- | --- |
| **Família do Azure SQL** | SQL Server em máquinas virtuais do Azure agora é uma parte da [família de produtos SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md). Confira nossa [nova aparência](../index.yml)! Nada mudou no produto, mas a documentação tem o objetivo de tornar a decisão do produto SQL do Azure mais fácil. | 


## <a name="january-2020"></a>Janeiro de 2020

| Alterações | Detalhes |
| --- | --- |
| **Suporte do Azure Government** | Agora é possível registrar SQL Server máquinas virtuais com a extensão do agente IaaS do SQL para máquinas virtuais hospedadas na nuvem [do Azure governamental](https://azure.microsoft.com/global-infrastructure/government/) . | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|Alterações | Detalhes |
 --- | --- |
| **Réplica de DR gratuita no Azure** | Você poderá hospedar uma [instância passiva gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) para recuperação de desastre no Azure na sua instância do SQL Server local se tiver o [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3). | 
| **Registro de extensão de IaaS do SQL em massa** | Agora você pode registrar SQL Server máquinas virtuais [em massa](sql-agent-extension-manually-register-vms-bulk.md) com a [extensão do agente IaaS do SQL](sql-server-iaas-agent-extension-automate-management.md). | 
|**Configuração de armazenamento com otimização de desempenho** | Agora você pode [personalizar totalmente a configuração de armazenamento](storage-configuration.md#new-vms) ao criar uma VM do SQL Server. |
|**Compartilhamento de arquivo Premium para a FCI** | Agora você pode criar uma instância de cluster de failover usando um [compartilhamento de arquivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md) em vez do método original de [espaços de armazenamento diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md). 
| **Host Dedicado do Azure** | Você pode executar sua VM SQL Server no [host dedicado do Azure](dedicated-host.md). | 
| **SQL Server migração de VM para uma região diferente** | Use o Azure Site Recovery para [migrar sua VM do SQL Server de uma região para outra](move-sql-vm-different-region.md). |
|  **Novos modos de instalação do SQL IaaS** | Agora é possível instalar a extensão SQL Server IaaS no [modo leve](sql-server-iaas-agent-extension-automate-management.md) para evitar a reinicialização do serviço SQL Server.  |
| **Modificação da edição do SQL Server** | Agora você pode alterar a [propriedade de edição](change-sql-server-edition.md) da sua VM do SQL Server. |
| **Alterações na extensão do agente IaaS do SQL** | Você pode [registrar sua VM SQL Server com a extensão do agente IaaS do SQL](sql-agent-extension-manually-register-single-vm.md) usando os novos modos SQL IaaS. Essa funcionalidade inclui imagens do [Windows Server 2008](sql-server-iaas-agent-extension-automate-management.md#management-modes).|
| **Imagens do tipo traga sua própria licença por meio do Benefício Híbrido do Azure** | As imagens do tipo traga sua própria licença implantadas por meio do Azure Marketplace agora podem alternar o [tipo de licença para pagamento conforme o uso](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks).| 
| **Novo SQL Server gerenciamento de VM no portal do Azure** | Agora há uma forma de gerenciar sua VM do SQL Server no portal do Azure. Para obter mais informações, confira [Gerenciar VMs do SQL Server no portal do Azure](manage-sql-vm-portal.md).  | 
| **Suporte estendido para SQL Server 2008 e 2008 R2** | [Estenda o suporte](sql-server-2008-extend-end-of-support.md) para o SQL Server 2008 e o SQL Server 2008 R2 fazendo a migração do *estado em que se encontra* para uma VM do Azure. | 
| **Capacidade de suporte a imagens personalizadas** | Agora você pode instalar a [extensão SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) para o sistema operacional personalizado e SQL Server imagens, o que oferece a funcionalidade limitada de [licenciamento flexível](licensing-model-azure-hybrid-benefit-ahb-change.md). Quando você estiver registrando sua imagem personalizada com a extensão do agente IaaS do SQL, especifique o tipo de licença como "AHUB". Caso contrário, o registro falhará. | 
| **Capacidade de suporte à instância nomeada** | Agora você poderá usar a extensão [SQL Server IaaS](sql-server-iaas-agent-extension-automate-management.md#installation) com uma instância nomeada se a instância padrão tiver sido desinstalada corretamente. | 
| **Melhoria do portal** | A experiência do portal do Azure para implantação de uma VM do SQL Server foi renovada para aprimorar a usabilidade. Para obter mais informações, confira o breve [início rápido](sql-vm-create-portal-quickstart.md) e o [guia de instruções](create-sql-vm-portal.md) mais detalhado para implantar uma VM do SQL Server.|
| **Aprimoramento do portal** | Agora é possível alterar o modelo de licenciamento de uma VM do SQL Server de pagamento conforme o uso para traga sua própria licença usando o [portal do Azure](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model).|
| **Simplificação da implantação do grupo de disponibilidade para uma VM SQL Server por meio do CLI do Azure** | Agora ficou mais fácil do que nunca implantar um grupo de disponibilidade em uma VM do SQL Server no Azure. Você pode usar o [CLI do Azure](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) para criar o cluster de failover do Windows, o balanceador de carga interno e os ouvintes do grupo de disponibilidade, tudo na linha de comando. Para obter mais informações, consulte [usar o CLI do Azure para configurar um grupo de disponibilidade Always on para SQL Server em uma VM do Azure](./availability-group-az-commandline-configure.md). | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 Alterações | Detalhes |
| --- | --- |
|  **Novo provedor de recursos para um cluster do SQL Server** | Um novo provedor de recursos (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups) define os metadados do cluster de failover do Windows. O ingresso de uma VM do SQL Server em *SqlVirtualMachineGroups* inicializa o serviço WSFC (Cluster de Failover do Windows Server) e ingressa a VM no cluster.  |
| **Configuração automatizada de uma implantação de grupo de disponibilidade com os modelos de início rápido do Azure** |Agora é possível criar o cluster de failover do Windows, ingressar SQL Server VMs a ele, criar o ouvinte e configurar o balanceador de carga interno usando dois modelos de início rápido do Azure. Para obter mais informações, confira [Usar modelos de início rápido do Azure para configurar um grupo de disponibilidade Always On para o SQL Server em uma VM do Azure](availability-group-quickstart-template-configure.md). | 
| **Registro automático para a extensão do agente IaaS do SQL** | SQL Server VMs implantadas após este mês são automaticamente registradas com a nova extensão do SQL IaaS Agent. As VMs do SQL Server implantadas antes deste mês ainda precisarão ser registradas manualmente. Para obter mais informações, consulte [registrar uma máquina virtual SQL Server no Azure com a extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md).|
|**Nova extensão do SQL IaaS Agent** |  Um novo provedor de recursos (Microsoft.SqlVirtualMachine) fornece um melhor gerenciamento das VMs do SQL Server. Para obter mais informações sobre como registrar suas VMs, consulte [registrar um SQL Server máquina virtual no Azure com a extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). |
|**Alternar o modelo de licenciamento** | Agora você pode alternar entre o modelo de pagamento conforme o uso e traga sua própria licença para a sua VM do SQL Server usando a CLI do Azure ou o PowerShell. Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma máquina virtual do SQL Server no Azure](licensing-model-azure-hybrid-benefit-ahb-change.md). | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>Recursos adicionais

**VMs do Windows**:

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Provisionar SQL Server em uma VM do Windows](create-sql-vm-portal.md)
* [Migrar um banco de dados para SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md)
* [Alta disponibilidade e recuperação de desastre para Máquinas Virtuais do SQL Server no Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Melhores práticas de desempenho para as Máquinas Virtuais do SQL Server no Azure](performance-guidelines-best-practices.md)
* [Padrões de aplicativo e estratégias de desenvolvimento para Máquinas Virtuais do SQL Server no Azure](application-patterns-development-strategies.md)

**VMs Linux**:

* [Visão geral do SQL Server em uma VM Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Provisionar SQL Server em uma máquina virtual Linux](../linux/sql-vm-create-portal-quickstart.md)
* [Perguntas Frequentes (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server na documentação do Linux](/sql/linux/sql-server-linux-overview)