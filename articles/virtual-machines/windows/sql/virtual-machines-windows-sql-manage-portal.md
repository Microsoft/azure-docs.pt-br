---
title: Gerenciar VMs do SQL Server no Azure usando o portal do Azure | Microsoft Docs
description: Saiba como acessar o recurso de máquina virtual do SQL no portal do Azure para uma VM do SQL Server hospedado no Azure.
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
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59a85e855c9fab9f2a3437c83c867b8076f55049
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607219"
---
# <a name="manage-sql-server-vms-in-azure-using-the-azure-portal"></a>Gerenciar VMs do SQL Server no Azure usando o portal do Azure

Há um novo ponto de acesso para gerenciar sua VM do SQL Server no Azure usando o [portal do Azure](https://portal.azure.com). 

O **máquinas virtuais do SQL** recurso agora é um serviço de gerenciamento independente que permite exibir todas as suas VMs do SQL Server simultaneamente e modificar as configurações de dedicado ao SQL Server: 

![Recurso de máquinas virtuais do SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)


## <a name="remarks"></a>Comentários

- O **máquinas virtuais do SQL** recurso é o método recomendado para exibir e gerenciar suas VMs do SQL Server. No entanto, atualmente, o **máquinas virtuais do SQL** recurso não dá suporte para o gerenciamento de [fim do suporte (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) VMs do SQL Server. Para gerenciar configurações para suas VMs do EOS SQL Server, use preteridas [guia de configuração do SQL Server](#access-sql-server-configuration-tab) em vez disso. 
- O **máquinas virtuais do SQL** recurso só está disponível para VMs do SQL Server que têm [registrado com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="access-sql-virtual-machine-resource"></a>Recurso de máquina virtual do SQL Access
Para acessar o recurso de máquinas virtuais do SQL, faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **Todos os Serviços**. 
1. Tipo `SQL virtual machines` na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado **máquinas virtuais do SQL** para adicionar essa opção ao menu Favoritos. 
1. Selecione **máquinas virtuais do SQL**. 

   ![Localizar as máquinas virtuais de VM do SQL em todos os serviços](media/virtual-machines-windows-sql-manage-portal/sql-vm-search.png)

1. Isso listará todas as VMs do SQL Server disponíveis na assinatura. Selecione aquele que você gostaria de gerenciar para iniciar o **máquinas virtuais do SQL** recursos. Use a caixa de pesquisa, se sua VM do SQL Server não está aparente. 

![Todas as VMs do SQL disponíveis](media/virtual-machines-windows-sql-manage-portal/all-sql-vms.png)

Selecionando a VM do SQL Server será aberta a **máquinas virtuais do SQL** recursos: 


![Recurso de máquinas virtuais do SQL](media/virtual-machines-windows-sql-manage-portal/sql-vm-resource.png)

  > [!TIP]
  > O **máquinas virtuais do SQL** recurso é usado para configurações do SQL Server dedicadas. Selecione o nome da VM na **Máquina Virtual** campo para navegar até as configurações que são específicas para a VM, mas não é exclusivo para o SQL Server. 

## <a name="access-sql-server-configuration-tab"></a>Guia de configuração do acesso do SQL Server
Guia de configuração do SQL Server foi preterida. Neste momento, ele é o único método para gerenciar [fim do suporte (EOS)](virtual-machines-windows-sql-server-2008-eos-extend-support.md) VMs do SQL Server e as VMs do SQL Server que não foram [registrado com o provedor de recursos de VM do SQL](virtual-machines-windows-sql-register-with-resource-provider.md).

Para acessar a guia de configuração de servidor preterida do SQL, você precisará navegar até a **máquinas virtuais** recursos. Para fazer isso, faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com). 
1. Selecione **Todos os Serviços**. 
1. Tipo `virtual machines` na caixa de pesquisa.
1. (Opcional): Selecione a estrela ao lado **máquinas virtuais** para adicionar essa opção ao menu Favoritos. 
1. Selecione **Máquinas virtuais**. 

   ![Pesquisar máquinas virtuais](media/virtual-machines-windows-sql-manage-portal/vm-search.png)

1. Isso listará todas as máquinas virtuais na assinatura. Selecione aquele que você gostaria de gerenciar para iniciar o **Máquina Virtual** recursos. Use a caixa de pesquisa, se sua VM do SQL Server não está aparente. 
1. Selecione **configuração do SQL Server** na **configurações** painel para gerenciar o SQL Server. 

![Configuração do SQL Server](media/virtual-machines-windows-sql-manage-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, confira os seguintes artigos: 

* [Visão geral do SQL Server em uma VM do Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server em um FAQ de VM do Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Orientações sobre preço do SQL Server em uma VM Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server em um notas de versão de VM do Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


