---
title: Visão geral do SQL Server nas Máquinas Virtuais do Windows do Azure | Microsoft Docs
description: Saiba como executar versões completas do SQL Server das Máquinas Virtuais do Azure na nuvem sem a necessidade de gerenciar nenhum hardware local.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b4b1234b88eafedbd03b78241baaa40231a6cbd6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97356796"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>O que é o SQL Server nas Máquinas Virtuais do Azure (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

O [SQL Server nas Máquinas Virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permite que você use versões completas do SQL Server na nuvem sem a necessidade de gerenciar nenhum hardware local. As VMs (máquinas virtuais) do SQL Server também simplificam os custos de licenciamento quando são pagas conforme o uso.

Máquinas virtuais do Azure executadas em várias [regiões geográficas](https://azure.microsoft.com/regions/) diferentes no mundo inteiro. Eles também oferecem uma variedade de [tamanhos de máquina](../../../virtual-machines/sizes.md). A galeria de imagens de máquina virtual permite que você crie uma VM do SQL Server com a versão, a edição e o sistema operacional corretos. Isso faz com que as máquinas virtuais sejam uma boa opção para muitas diferentes cargas de trabalho do SQL Server.

Se você não está familiarizado com o SQL Server nas VMs do Azure, confira o vídeo *Visão geral do SQL Server nas VMs do Azure* da nossa [série detalhada de vídeos do SQL do Azure](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="automated-updates"></a>Atualizações automáticas

O SQL Server nas Máquinas Virtuais do Azure pode usar a [aplicação de patch automatizada](automated-patching.md) para agendar uma janela de manutenção para instalar automaticamente janelas importantes e atualizações do SQL Server.

## <a name="automated-backups"></a>Backups automatizados

O SQL Server nas Máquinas Virtuais do Azure pode aproveitar o [Backup Automatizado](automated-backup.md), que cria regularmente backups do banco de dados no armazenamento de blobs. Essa técnica também pode ser usada manualmente. Para obter mais informações, consulte [Usar o Armazenamento do Azure para o Backup e a Restauração do SQL Server](azure-storage-sql-server-backup-restore-use.md).

O Azure também fornece uma solução de backup de classe empresarial para SQL Server em execução em VMs do Azure. Uma solução de backup totalmente gerenciada, compatível com grupos de disponibilidade Always On, retenção de longo prazo, recuperação pontual e gerenciamento e monitoramento central. Para obter mais informações, confira [Backup do Azure para o SQL Server nas VMs do Azure](../../../backup/backup-azure-sql-database.md).
  

## <a name="high-availability"></a>Alta disponibilidade

Se você precisar de alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias instâncias do SQL Server nas Máquinas Virtuais do Azure em uma rede virtual. Você pode configurar manualmente sua solução de alta disponibilidade ou usar modelos no portal do Azure para a configuração automática. Para obter uma visão geral de todas as opções de alta disponibilidade, confira [Alta disponibilidade e recuperação de desastre do SQL Server nas Máquinas Virtuais do Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Desempenho

Máquinas virtuais do Azure oferecem tamanhos de máquinas diferentes para atender às demandas de várias cargas de trabalho. As VMs do SQL Server também fornecem a configuração automatizada de armazenamento, que é otimizada para seus requisitos de desempenho. Para obter mais informações sobre como configurar o armazenamento para VMs do SQL Server, confira [Configuração de armazenamento para VMs do SQL Server](storage-configuration.md). Para ajustar o desempenho, confira as [Melhores práticas de desempenho para o SQL Server nas Máquinas Virtuais do Azure](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Introdução às VMs do SQL Server

Para começar, escolha uma imagem de máquina virtual do SQL Server com a versão, a edição e o sistema operacional necessários. As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server.

> [!TIP]
> Para obter mais informações sobre como entender os preços das imagens do SQL Server, confira [Diretrizes de preços para o SQL Server nas Máquinas Virtuais do Azure](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Pré-pagas
A tabela a seguir fornece uma matriz de imagens do SQL Server pré-pagas.

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Para ver as imagens disponíveis de máquina virtual do SQL Server em Linux, confira [Visão geral do SQL Server nas Máquinas Virtuais do Azure (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Agora, é possível alterar o modelo de licenciamento de uma VM do SQL Server paga por uso para usar sua própria licença. Para obter mais informações, confira [Como alterar o modelo de licenciamento para uma VM do SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Traga sua própria licença
Também é possível usar sua própria licença (BYOL). Nesse cenário, você paga apenas pela VM sem encargos adicionais para o licenciamento do SQL Server.  Colocar sua própria licença pode economizar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para obter os requisitos para usar essa opção, veja [Diretrizes de preços para VMs do SQL Server do Azure](pricing-guidance.md#byol).

Para usar sua licença, converta uma VM do SQL Server existente paga conforme o uso ou implante uma imagem com o **{BYOL}** prefixado. Para obter mais informações sobre como alternar seu modelo de licenciamento entre o pagamento conforme o uso e o BYOL, confira [Como alterar o modelo de licenciamento de uma VM do SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Versão | Sistema operacional | Edition |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard  BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

É possível implantar uma imagem mais antiga do SQL Server que não esteja disponível no portal do Azure usando o PowerShell. Para ver todas as imagens disponíveis por meio do PowerShell, use o seguinte comando:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Para obter mais informações sobre como implantar VMs do SQL Server usando o PowerShell, confira [Como provisionar máquinas virtuais do SQL Server com o Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Conectar-se à VM
Depois de criar sua VM do SQL Server, conecte-se a ela de aplicativos ou ferramentas, como o SQL Server Management Studio (SSMS). Para obter instruções, confira [Conectar-se a uma máquina virtual do SQL Server no Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrar seus dados
Se você tiver um banco de dados existente, o ideal será movê-lo para a VM do SQL Server recém-provisionada. Para obter uma lista das opções de migração e diretrizes, consulte [Migrar um Banco de Dados para o SQL Server em uma VM do Azure](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Criar e gerenciar recursos de SQL do Azure com o portal do Azure

O portal do Azure fornece uma página em que você pode gerenciar [todos os seus recursos SQL do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql) incluindo suas máquinas virtuais do SQL.

Para acessar a página de **recursos do SQL do Azure**, selecione **SQL do Azure** no menu do portal do Azure ou pesquise e selecione **SQL do Azure** em qualquer página.

![Pesquisar SQL do Azure](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> O SQL do Azure oferece uma forma rápida e fácil de acessar todos os seus bancos de dados SQL do Azure, pools elásticos, servidores lógicos, instâncias gerenciadas e máquinas virtuais. O SQL do Azure não é um serviço nem um recurso. 

Para gerenciar os recursos existentes, selecione o item desejado na lista. Para criar recursos de SQL do Azure, selecione **+ Adicionar**. 

![Criar recurso de SQL do Azure](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Depois de selecionar **+ Adicionar**, veja informações adicionais sobre as diferentes opções selecionando **Mostrar detalhes** em qualquer bloco.

![detalhes do bloco bancos de dados](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Para obter detalhes, confira:

- [Criar um banco de dados individual](../../database/single-database-create-quickstart.md)
- [Criar um pool elástico](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Criar uma instância gerenciada](../../managed-instance/instance-create-quickstart.md)
- [Criar uma máquina virtual do SQL Server](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> Política de atualização de imagem da VM do SQL Server
O Azure mantém apenas uma imagem de máquina virtual para cada combinação de sistema operacional, versão e edição com suporte. Isso significa que, ao longo do tempo, as imagens são atualizadas e as imagens mais antigas são removidas. Para saber mais, confira a seção **Imagens** das [Perguntas frequentes sobre VMs SQL Server](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Programa de aperfeiçoamento da experiência do usuário (CEIP)
O CEIP (Programa de Aperfeiçoamento da Experiência do Usuário) está habilitado por padrão. Isso envia relatórios periodicamente à Microsoft a fim de ajudar a aprimorar o SQL Server. Nenhuma tarefa de gerenciamento é necessária com o CEIP, a menos que você queira desabilitá-lo após o provisionamento. Você pode personalizar ou desabilitar o CEIP conectando-se à VM com área de trabalho remota. Em seguida, execute o utilitário **Erro do SQL Server e o Relatório de Uso** . Siga as instruções para desabilitar o relatório. Para saber mais sobre coleta de dados, veja a [Declaração de privacidade do SQL Server](/sql/sql-server/sql-server-privacy).

## <a name="related-products-and-services"></a>Produtos e serviços relacionados
### <a name="windows-virtual-machines"></a>Máquinas virtuais do Windows
* [Visão geral das Máquinas Virtuais do Azure](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Armazenamento
* [Introdução ao Armazenamento do Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rede
* [Visão geral da Rede Virtual](../../../virtual-network/virtual-networks-overview.md)
* [Endereços IP no Azure](../../../virtual-network/public-ip-addresses.md)
* [Criar um nome de domínio totalmente qualificado no portal do Azure](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentação do SQL Server](/sql/index)
* [Comparação de Banco de Dados SQL do Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Próximas etapas

Introdução ao SQL Server em Máquinas Virtuais do Azure:

* [Criar uma VM do SQL Server no portal do Azure](sql-vm-create-portal-quickstart.md)

Obtenha respostas para perguntas frequentes sobre máquinas virtuais do SQL Server:

* [Perguntas frequentes sobre o SQL Server em Máquinas Virtuais do Azure](frequently-asked-questions-faq.md)

Ver arquiteturas de referência para executar aplicativos de N camadas no SQL Server em IaaS

* [Aplicativo de N camadas do Windows no Azure com SQL Server](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Executar aplicativo de N camadas em várias regiões do Azure para ter alta disponibilidade](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)