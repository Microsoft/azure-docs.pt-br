---
title: "Visão geral do SQL Server nas Máquinas Virtuais do Windows do Azure | Microsoft Docs"
description: "Saiba mais sobre como executar as edições completas do SQL Server nas máquinas virtuais do Windows do Azure. Obtenha links diretos para todas as imagens de VM do SQL Server e conteúdo relacionado."
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: b10c995fdd8e241d354c62537a0600b393795c1b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-windows"></a>Visão geral do SQL Server em Máquinas Virtuais do Azure (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

Este tópico descreve as opções para executar o SQL Server nas VMs (máquinas virtuais) do Windows do Azure, juntamente com [links para as imagens do portal](#option-1-create-a-sql-vm-with-per-minute-licensing) e uma visão geral das [tarefas comuns](#manage-your-sql-vm).

> [!NOTE]
> Se você já estiver familiarizado com o SQL Server e quiser apenas ver como implantar uma VM do Windows do SQL Server, veja [Provisionar uma máquina virtual do Windows do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md). Ou, se você quiser criar uma VM do Linux com o SQL Server, consulte [Provisionar uma VM do SQL Server do Linux no Azure](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Se você for um administrador de banco de dados ou um desenvolvedor, as VMs do Azure fornecem uma maneira de mover seus aplicativos e cargas de trabalho locais do SQL Server para a nuvem.

## <a name="scenarios"></a>Cenários
Há muitos motivos para você optar por hospedar seus dados no Azure. Se o seu aplicativo estiver mudando para o Azure, isso também melhora o desempenho de movimentação dos dados. Mas há outros benefícios. Você tem acesso automaticamente a vários data centers, visando a recuperação de desastres e uma presença global. Os dados também são altamente seguros e duradouros.

A execução do SQL Server em VMs do Azure é uma opção para armazenar dados relacionais no Azure. É uma boa escolha para vários cenários. Por exemplo, talvez você queira configurar a VM do Azure o mais próximo possível de uma máquina local do SQL Server. Ou talvez você queira executar outros aplicativos e serviços no mesmo servidor de banco de dados. Há dois recursos principais que podem ajudar você a pensar em mais cenários e considerações:

* [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) fornece uma visão geral dos melhores cenários para uso do SQL Server em VMs do Azure. 
* [Escolher uma opção do SQL Server de nuvem: Banco de Dados do SQL Azure (PaaS) ou SQL Server em VMs do Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)fornece uma comparação detalhada entre o Banco de Dados SQL e o SQL Server em execução em uma VM.

## <a name="create-a-new-sql-vm"></a>Criar uma nova VM de SQL
As seções a seguir fornecem links diretos para o Portal do Azure para as imagens da Galeria de máquina virtual do SQL Server. Dependendo da imagem selecionada, você poderá pagar pelos custos de licenciamento do SQL Server por minuto, ou poderá usar sua própria licença (BYOL).

Encontre diretrizes passo a passo para criar uma nova VM do SQL no tutorial [Provisionar uma máquina virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Além disso, revise as [Práticas recomendadas para as VMs do SQL Server](virtual-machines-windows-sql-performance.md), que explicam como selecionar o tamanho da máquina apropriado e outros recursos disponíveis durante o provisionamento.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opção 1: Criar uma VM do SQL com licenciamento por minuto
A tabela a seguir fornece uma matriz das mais recentes imagens do SQL Server na galeria de máquinas virtuais. Clique em qualquer link para começar a criação de uma nova VM do SQL com a versão, a edição e o sistema operacional especificados. 

> [!TIP]
> Para entender os preços da VM e do SQL preços para essas imagens, consulte [Diretrizes para os preços das VMs do SQL Server do Azure](virtual-machines-windows-sql-server-pricing-guidance.md).

| Versão | Sistema operacional | Edição |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |

> [!NOTE]
> Para ver as imagens de máquina virtual do SQL Server do Linux disponíveis, confira [Visão geral do SQL Server em máquinas virtuais do Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

## <a id="BYOL"></a>Opção 2: Criar uma VM do SQL com uma licença existente
Também é possível usar sua própria licença (BYOL). Nesse cenário, você paga apenas pela VM sem encargos adicionais para o licenciamento do SQL Server. Para usar sua própria licença, use a tabela abaixo de versões, edições e sistemas operacionais do SQL Server. No portal, os nomes dessas imagens são prefixados com **{BYOL}**.

> [!TIP]
> Colocar sua própria licença pode economizar dinheiro ao longo do tempo para cargas de trabalho de produção contínua. Para obter mais informações, consulte [Diretrizes para os preço das VMs do Azure do SQL Server](virtual-machines-windows-sql-server-pricing-guidance.md).

| Versão | Sistema operacional | Edição |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

Além dessa lista, outras combinações de sistemas operacionais e versões do SQL Server estão disponíveis. Localize outras imagens por meio de uma pesquisa de mercado no portal do Azure (pesquise "{BYOL} SQL Server").

> [!IMPORTANT]
> Para usar as imagens da VM BYOL, você deve ter um Enterprise Agreement com [License Mobility por meio do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/). Também é necessário uma licença válida para a versão/edição do SQL Server que você deseja usar. Você deve [fornecer as informações necessárias do BYOL à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) em **10** dias de provisionamento da VM. 

> [!NOTE]
> Não é possível alterar o modelo de licenciamento de uma VM do SQL Server paga por minuto para usar sua própria licença. Nesse caso, você deve criar uma nova VM BYOL e migrar seus bancos de dados para a nova VM. 

## <a name="manage-your-sql-vm"></a>Gerenciar sua VM do SQL
Após o provisionamento da VM do SQL Server, várias tarefas de gerenciamento opcionais estarão disponíveis. Em muitos aspectos, você configura e gerencia o SQL Server exatamente como configuraria uma instância local do SQL Server. No entanto, algumas tarefas são específicas do Azure. As seções a seguir destacam algumas dessas áreas com links para obter mais informações.

### <a name="connect-to-the-vm"></a>Conectar-se à VM
Uma das etapas mais básicas do gerenciamento é conectar-se à sua VM do SQL Server por meio de ferramentas, como o SSMS (SQL Server Management Studio). Para obter instruções sobre como se conectar à sua nova VM do SQL Server, confira [Conectar-se a uma Máquina Virtual do SQL Server no Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrar seus dados
Se você tiver um banco de dados existente, deverá movê-lo para a VM do SQL recentemente provisionada. Para obter uma lista das opções de migração e diretrizes, consulte [Migrar um Banco de Dados para o SQL Server em uma VM do Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurar alta disponibilidade
Se você precisar de alta disponibilidade, considere configurar grupos de disponibilidade do SQL Server. Isso envolve várias VMs do Azure em uma rede virtual. O portal do Azure tem um modelo que define essa configuração para você. Para obter mais informações, consulte [Configurar um grupo de disponibilidade AlwaysOn nas máquinas virtuais do Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Se você quiser configurar manualmente o Grupo de Disponibilidade e o ouvinte associado, consulte [Configurar os Grupos de Disponibilidade AlwaysOn na VM do Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Para outras considerações sobre alta disponibilidade, consulte [alta disponibilidade e recuperação de desastres para SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Fazer backup dos dados
As VMs do Azure podem aproveitar o [Backup Automatizado](virtual-machines-windows-sql-automated-backup.md), que cria regularmente backups do banco de dados no armazenamento de blobs. Essa técnica também pode ser usada manualmente. Para obter mais informações, consulte [Usar o Armazenamento do Azure para o Backup e a Restauração do SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Para obter uma visão geral das opções de backup e restauração, consulte [Backup e Restauração do SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatizar as atualizações
As VMs do Azure podem usar a [Aplicação de Patch Automatizada](virtual-machines-windows-sql-automated-patching.md) para agendar uma janela de manutenção para instalar automaticamente janelas importantes e atualizações do SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Programa de aperfeiçoamento da experiência do usuário (CEIP)
O CEIP (Programa de Aperfeiçoamento da Experiência do Usuário) está habilitado por padrão. Isso envia relatórios periodicamente à Microsoft a fim de ajudar a aprimorar o SQL Server. Nenhuma tarefa de gerenciamento é necessária com o CEIP, a menos que você queira desabilitá-lo após o provisionamento. Você pode personalizar ou desabilitar o CEIP conectando-se à VM com área de trabalho remota. Em seguida, execute o utilitário **Erro do SQL Server e o Relatório de Uso** . Siga as instruções para desabilitar o relatório. 

Para saber mais sobre coleta de dados, veja a [Declaração de privacidade do SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx). 

## <a name="next-steps"></a>Próximas etapas

Para ver perguntas sobre preços, consulte [Diretrizes para os preços das VMs do SQL Server do Azure](virtual-machines-windows-sql-server-pricing-guidance.md) e [Página de preços do Azure](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Selecione a edição de destino do SQL Server na lista **SO/Software**. Exiba os preços de máquinas virtuais de tamanhos diferentes.

Mais perguntas? Primeiro, consulte as [Perguntas Frequentes sobre o SQL Server nas Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-faq.md). Mas, adicione também suas perguntas ou comentários à parte inferior de qualquer um dos tópicos da VM do SQL para interagir com a Microsoft e a comunidade.
