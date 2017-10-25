---
title: "Visão geral do SQL Server nas Máquinas Virtuais do Azure Linux | Microsoft Docs"
description: "Saiba mais sobre como executar as edições completas do SQL Server nas máquinas virtuais Linux do Azure. Obtenha links diretos para todas as imagens de VM Linux SQL Server e conteúdo relacionado."
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: 787e696edd2a446bec280b763fe5ac591782c8ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Visão geral do SQL Server nas Máquinas Virtuais do Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Este tópico descreve as opções para executar o SQL Server nas VMs (máquinas virtuais) Linux do Azure, juntamente com [links para as imagens do portal](#option-1-create-a-sql-vm-with-per-minute-licensing).

> [!NOTE]
> Se você já estiver familiarizado com o SQL Server e quiser apenas ver como implantar uma VM Linux do SQL Server, consulte [Provisionar uma VM Linux do SQL Server no Azure](provision-sql-server-linux-virtual-machine.md). Ou, se você quiser criar uma VM do Windows com o SQL Server, consulte [Provisionar uma VM do Windows SQL Server no Azure](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Se você for um administrador de banco de dados ou um desenvolvedor, as VMs do Azure fornecem uma maneira de mover seus aplicativos e cargas de trabalho locais do SQL Server para a nuvem.

## <a name="scenarios"></a>Cenários

Há muitos motivos para você optar por hospedar seus dados no Azure. Se você estiver desenvolvendo ou migrando seu aplicativo para o Azure, isso melhorará o desempenho para localizar os dados de back-end no Azure. Você tem acesso automaticamente a vários data centers, visando a recuperação de desastres e uma presença global. Os dados também são altamente seguros e duradouros.

A execução do SQL Server em VMs do Azure é uma opção para armazenar dados relacionais no Azure. Você também tem a opção de usar o serviço de Banco de Dados SQL do Azure. Para saber mais sobre como escolher entre o SQL Server em máquinas virtuais versus o Banco de Dados SQL do Azure, consulte [Escolher uma opção de nuvem do SQL Server: Banco de Dados SQL do Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a name="create-a-new-sql-vm"></a>Criar uma nova VM de SQL

Encontre orientações passo a passo para criar uma nova VM do SQL no tutorial [Provisionar uma VM Linux do SQL Server no Azure](provision-sql-server-linux-virtual-machine.md).

A tabela a seguir fornece uma matriz das mais recentes imagens do SQL Server na galeria de máquinas virtuais. Clique em qualquer link para começar a criação de uma nova VM do SQL com a versão, a edição e o sistema operacional especificados.

> [!TIP]
> Para entender os preços da VM e do SQL dessas imagens, consulte [a página de preços das VMs Linux do SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versão | Sistema operacional | Edição |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver as imagens de máquina virtual do Windows SQL Server disponíveis, consulte [Visão geral do SQL Server em máquinas virtuais do Azure (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Pacotes instalados

Quando você configura o SQL Server no Linux, você instala o pacote do mecanismo de banco de dados e, depois, vários pacotes opcionais, dependendo dos seus requisitos. As imagens de máquina virtual Linux para SQL Server instalam automaticamente a maioria dos pacotes para você. A tabela a seguir mostra quais pacotes são instalados para cada distribuição.

| Distribuição | [Mecanismo de Banco de Dados](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Ferramentas](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Pesquisa de texto completo](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Complemento HA](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sim](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar e usar o SQL Server no Linux, consulte [Visão geral do SQL Server no Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
