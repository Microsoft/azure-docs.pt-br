---
title: Visão geral do SQL Server nas Máquinas Virtuais do Azure para Linux | Microsoft Docs
description: Saiba mais sobre como executar as edições completas do SQL Server nas Máquinas Virtuais do Azure para Linux. Obtenha links diretos para todas as imagens de VM Linux SQL Server e conteúdo relacionado.
services: virtual-machines-sql
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3613b8417c78d0a01582defb7396cc65521e2f4c
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449473"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Visão geral do SQL Server nas Máquinas Virtuais do Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

O SQL Server nas Máquinas Virtuais do Azure permite que você use versões completas do SQL Server na nuvem sem a necessidade de gerenciar nenhum hardware local. As VMs do SQL Server também simplificam os custos de licenciamento quando são pré-pagas.

Máquinas virtuais do Azure executadas em várias [regiões geográficas](https://azure.microsoft.com/regions/) diferentes no mundo inteiro. Eles também oferecem uma variedade de [tamanhos de máquina](../../../virtual-machines/sizes.md). A galeria de imagens de máquina virtual permite que você crie uma VM do SQL Server com a versão, a edição e o sistema operacional corretos. Isso torna a máquinas virtuais uma boa opção para uma das muitas cargas de trabalho diferentes do SQL Server. 

Se você não está familiarizado com o SQL do Azure, confira o vídeo *Visão geral do SQL Server nas VMs do Azure* da nossa [série detalhada de vídeos do SQL do Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Introdução às VMs do SQL Server

Para começar, escolha uma imagem de máquina virtual do SQL Server com a versão, a edição e o sistema operacional necessários. As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server.

> [!TIP]
> Para mais informações sobre como compreender melhor o preço das imagens do SQL Server, confira [a página de preços das VMs Linux que executam o SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) v12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Para ver as imagens de máquina virtual do SQL Server disponíveis para Windows, confira [Visão geral do SQL Server nas Máquinas Virtuais do Azure (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Pacotes instalados

Ao configurar o SQL Server em Linux, você instalará o pacote do Mecanismo de Banco de Dados e, depois, vários pacotes opcionais, dependendo dos requisitos. As imagens de máquina virtual Linux para SQL Server instalam automaticamente a maioria dos pacotes para você. A tabela a seguir mostra quais pacotes são instalados para cada distribuição.

| Distribuição | [Mecanismo de Banco de Dados](/sql/linux/sql-server-linux-setup) | [Ferramentas](/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](/sql/linux/sql-server-linux-setup-sql-agent) | [Pesquisa de texto completo](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [Complemento HA](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL e mecanismo de banco de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e SQL Server Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e complemento de HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES e mecanismo de banco de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e SQL Server Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES e complemento de HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu e mecanismo de banco de dados](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e ferramentas](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e SQL Server Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e pesquisa de texto completo](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e complemento de HA](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Produtos e serviços relacionados

### <a name="linux-virtual-machines"></a>Máquinas virtuais do Linux

* [Visão geral das Máquinas Virtuais do Azure](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Armazenamento

* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede

* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/public-ip-addresses.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [SQL Server na documentação do Linux](/sql/linux)
* [Comparação de Banco de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Próximas etapas

Introdução ao SQL Server em Máquinas Virtuais do Linux:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas frequentes sobre as VMs do SQL Server em Linux:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)