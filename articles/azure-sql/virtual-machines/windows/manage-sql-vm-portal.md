---
title: Gerenciar SQL Server máquinas virtuais no Azure usando o portal do Azure | Microsoft Docs
description: Saiba como acessar o recurso de máquina virtual do SQL no portal do Azure para uma VM do SQL Server hospedada no Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 68c7805136a7361a64a6ff6dfbb9e7d910b2ea9b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97357935"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Gerenciar VMs do SQL Server no Azure usando o portal do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

No [portal do Azure](https://portal.azure.com), o recurso de [**máquinas virtuais do SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) é um serviço de gerenciamento independente para gerenciar SQL Server em VMs do Azure. Você pode usá-lo para exibir todas as suas VMs do SQL Server simultaneamente e modificar as configurações dedicadas para o SQL Server: 

![Recurso de máquinas virtuais do SQL](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>Comentários

- Recomendamos que você use o recurso de [**máquinas virtuais do SQL**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) para exibir e gerenciar suas VMs SQL Server no Azure. No momento, os recursos das **máquinas virtuais do SQL** não oferecem suporte ao gerenciamento de [fim de suporte](sql-server-2008-extend-end-of-support.md) de VMs do SQL Server. Para gerenciar as configurações de fim de suporte de VMs do SQL Server, use a [guia de configuração do SQL Server](#access-the-sql-server-configuration-tab) preterida. 
- O recurso de **máquinas virtuais do SQL** está disponível somente para VMs SQL Server que foram [registradas com a extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). 


## <a name="access-the-sql-virtual-machines-resource"></a>Acessar o recurso de máquinas virtuais do SQL
Para acessar o recurso de **máquinas virtuais do SQL**, faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **Todos os Serviços**. 
1. Digite **máquinas virtuais do SQL** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado de **máquinas virtuais do SQL** para adicionar essa opção ao menu **Favoritos**. 
1. Selecione **máquinas virtuais do SQL**. 

   ![Localizar máquinas virtuais do SQL Server em todos os serviços](./media/manage-sql-vm-portal/sql-vm-search.png)

1. O portal lista todas as VMs do SQL Server disponíveis na assinatura. Selecione aquele que você deseja gerenciar para abrir o recurso de **máquinas virtuais do SQL**. Use a caixa de pesquisa caso sua VM do SQL Server não esteja aparecendo. 

   ![Todas as VMs do SQL Server disponíveis](./media/manage-sql-vm-portal/all-sql-vms.png)

   A seleção da sua VM do SQL Server abre o recurso de **máquinas virtuais do SQL**: 


   ![Exibir o recurso de máquinas virtuais do SQL](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> O recurso de **máquinas virtuais do SQL** serve para configurações de SQL Server dedicadas. Selecione o nome da VM na caixa **Máquina virtual** para abrir as configurações específicas para a VM, mas não exclusivas para o SQL Server. 

## <a name="access-the-sql-server-configuration-tab"></a>Acessar a guia de configuração do SQL Server
A guia **configuração do SQL Server** foi preterida. Neste momento, é o único método para gerenciar VMs SQL Server [de fim de suporte](sql-server-2008-extend-end-of-support.md) e SQL Server VMs que não foram [registradas com a extensão do SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md).

Para acessar a guia de **configuração do SQL Server** preterida, acesse o recurso de **Máquinas virtuais**. Use as seguintes etapas:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **Todos os Serviços**. 
1. Digite **máquinas virtuais** na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado de **máquinas virtuais** para adicionar essa opção ao menu **Favoritos**. 
1. Selecione **Máquinas virtuais**. 

   ![Pesquisar máquinas virtuais](./media/manage-sql-vm-portal/vm-search.png)

1. O portal lista todas as máquinas virtuais na assinatura. Selecione aquela que você deseja gerenciar para abrir o recurso de **Máquinas virtuais**. Use a caixa de pesquisa caso sua VM do SQL Server não esteja aparecendo. 
1. Selecione **configuração do SQL Server** no painel **Configurações** para gerenciar sua VM do SQL Server. 

   ![Configuração do SQL Server](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Perguntas frequentes sobre o SQL Server em uma VM do Windows](frequently-asked-questions-faq.md)
* [Diretrizes de preço para o SQL Server em uma VM do Windows](pricing-guidance.md)
* [Notas sobre a versão do SQL Server em uma VM do Windows](doc-changes-updates-release-notes.md)


