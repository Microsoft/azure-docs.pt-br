---
title: Visão geral do SQL Server nas Máquinas Virtuais do Azure Linux | Microsoft Docs
description: Saiba mais sobre como executar as edições completas do SQL Server nas máquinas virtuais Linux do Azure. Obtenha links diretos para todas as imagens de VM Linux SQL Server e conteúdo relacionado.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c97793dc28b4ef097d8baa34678aeb92bf123809
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473462"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Visão geral do SQL Server nas Máquinas Virtuais do Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

O SQL Server em máquinas virtuais do Azure permite que você use versões completas do SQL Server na nuvem sem a necessidade de gerenciar qualquer hardware local. As VMs do SQL Server também simplificam os custos de licenciamento quando são pré-pagas.

Máquinas virtuais do Azure executadas em várias [regiões geográficas](https://azure.microsoft.com/regions/) diferentes no mundo inteiro. Eles também oferecem uma variedade de [tamanhos de máquina](../sizes.md). A galeria de imagens de máquina virtual permite que você crie uma VM do SQL Server com a versão, a edição e o sistema operacional corretos. Isso torna a máquinas virtuais uma boa opção para uma das muitas cargas de trabalho diferentes do SQL Server.

## <a id="create"></a> Introdução a VMs do SQL

Para começar, escolha uma imagem de máquina virtual do SQL Server com a versão, a edição e o sistema operacional necessários. As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server.

> [!TIP]
> Para compreender melhor o preço das imagens do SQL, confira [a página de preços para VMs SQL Server do Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Version | Sistema operacional | Edição |
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
| RHEL | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) | ![não](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sim](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Produtos e serviços relacionados

### <a name="linux-virtual-machines"></a>Máquinas Virtuais do Linux

* [Visão geral de Máquinas Virtuais](../overview.md)

### <a name="storage"></a>Armazenamento

* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede

* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server na documentação do Linux](https://docs.microsoft.com/sql/linux)
* [Comparação de Banco de Dados SQL do Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Próximas etapas

Introdução ao SQL Server em Máquinas Virtuais do Linux do Azure:

* [Criar uma VM do SQL Server no portal do Azure](provision-sql-server-linux-virtual-machine.md)

Obtenha respostas para perguntas frequentes sobre máquinas virtuais do SQL no Linux:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Linux do Azure](sql-server-linux-faq.md)
