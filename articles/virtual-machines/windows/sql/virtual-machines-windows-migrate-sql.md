---
title: Migrar um banco de dados do SQL Server para o SQL Server em uma VM | Microsoft Docs
description: "Saiba mais sobre como migrar um banco de dados de usuário local para o SQL Server em uma máquina virtual do Azure."
services: virtual-machines-windows
documentationcenter: 
author: sabotta
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: carlasab
ms.openlocfilehash: 68767534298783083a441aa295611914d0df9db0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar um banco de dados do SQL Server para o SQL Server em uma VM do Azure

Existem vários métodos para migrar um banco de dados de usuário do SQL Server local para o SQL Server em uma VM do Azure. Este artigo discute brevemente diversos métodos e recomenda o melhor método para vários cenários.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="what-are-the-primary-migration-methods"></a>Quais são os principais métodos de migração?
Os principais métodos de migração são:

* Executar backup local usando a compactação e copiar manualmente o arquivo de backup para a máquina virtual do Azure
* Executar um backup para URL e restaurar na máquina virtual do Azure desde a URL
* Desanexar e copiar os arquivos de log e de dados para o armazenamento de blobs do Azure e, em seguida, anexar ao SQL Server em uma VM do Azure desde a URL
* Converter a máquina física local em VHD do Hyper-V, carregar no armazenamento de Blob do Azure e, em seguida, implantar como nova máquina virtual usando o VHD carregado
* Remeter o disco rígido usando o Serviço de Importação/Exportação do Windows
* Caso você tenha uma implantação local do AlwaysOn, use o [Assistente para Adicionar uma Réplica do Azure](../classic/sql-onprem-availability.md) para criar uma réplica no Azure e executar o failover, apontando os usuários para a instância do banco de dados do Azure
* Use a [replicação transacional](https://msdn.microsoft.com/library/ms151176.aspx) do SQL Server para configurar a instância do SQL Server do Azure como um assinante e, em seguida, desabilite a replicação, apontando os usuários para a instância de banco de dados do Azure

> [!TIP]
> Você também pode usar essas mesmas técnicas para mover bancos de dados entre VMs do SQL Server no Azure. Por exemplo, não há suporte para atualizar uma VM da imagem da galeria do SQL Server de uma versão/edição para outra. Nesse caso, você deve criar uma nova VM do SQL Server com a nova versão/edição e, em seguida, usar uma das técnicas de migração neste artigo para mover seus bancos de dados. 

## <a name="choosing-your-migration-method"></a>Escolhendo o método de migração
Para obter um desempenho de transferência de dados ideal, migre os arquivos de banco de dados na VM do Azure usando um arquivo de backup compactado.

Para minimizar o tempo de inatividade durante o processo de migração do banco de dados, use a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível usar os métodos acima, migre seu banco de dados manualmente. Usando esse método, você geralmente começa com um backup do banco de dados seguido por uma cópia do banco de dados de backup no Azure e, em seguida, executa uma restauração de banco de dados. Você também pode copiar os próprios arquivos do banco de dados para o Azure e anexá-los. Existem vários métodos pelos quais você pode realizar esse processo manual de migrar um banco de dados para uma VM do Azure.

> [!NOTE]
> Quando você atualizar para o SQL Server 2014 ou para o SQL Server 2016 de versões anteriores do SQL Server, deverá considerar se as alterações são necessárias. É recomendável resolver todas as dependências nos recursos sem suporte da nova versão do SQL Server como parte do seu projeto de migração. Para saber mais sobre os cenários e as edições com suporte, consulte [Atualizar para o SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela a seguir lista os principais métodos de migração e discute quando o uso de cada método é mais apropriado.

| Método | Versão do banco de dados de origem | Versão do banco de dados de destino | Restrição de tamanho do backup do banco de dados de origem | Observações |
| --- | --- | --- | --- | --- |
| [Execute o backup local usando a compactação e copie manualmente o arquivo de backup para a máquina virtual do Azure](#backup-and-restore) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Essa é uma técnica muito simples e bem testada para mover bancos de dados entre máquinas. |
| [Execute um backup para URL e restaure na máquina virtual do Azure desde a URL](#backup-to-url-and-restore) |SQL Server 2012 SP1 CU2 ou posterior |SQL Server 2012 SP1 CU2 ou posterior |< 12,8 TB para SQL Server 2016, caso contrário, < 1 TB | Este método é somente outra forma de mover o arquivo de backup para a VM usando o armazenamento do Azure. |
| [Desanexe e copie os arquivos de log e dados para o armazenamento de blob do Azure e anexe à máquina virtual do SQL Server no Azure desde a URL](#detach-and-attach-from-url) |SQL Server 2005 ou posterior |SQL Server 2014 ou posterior |[Limite de armazenamento da VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Use este método quando pretender [armazenar esses arquivos usando o serviço de armazenamento de Blob do Azure](https://msdn.microsoft.com/library/dn385720.aspx) e anexá-los ao SQL Server em execução em uma VM do Azure, especialmente com bancos de dados muito grandes. |
| [Converta a máquina local em VHDs do Hyper-V, carregue no armazenamento de Blob do Azure e, em seguida, implante uma nova máquina virtual usando o VHD carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Use quando estiver [trazendo sua própria licença do SQL Server](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) ao migrar um banco de dados que você executará em uma versão anterior do SQL Server ou ao migrar bancos de dados do sistema e do usuário como parte da migração de banco de dados dependente de outros bancos de dados do usuário e/ou bancos de dados do sistema. |
| [Remeter o disco rígido usando o Serviço de Importação/Exportação do Windows](#ship-hard-drive) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Use o [Serviço de Importação/Exportação do Windows](../../../storage/common/storage-import-export-service.md) quando o método de cópia manual for muito lento, como com bancos de dados muito grandes |
| [Usar o Assistente para Adicionar uma Réplica do Azure](../classic/sql-onprem-availability.md) |SQL Server 2012 ou posterior |SQL Server 2012 ou posterior |[Limite de armazenamento da VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Minimiza o tempo de inatividade, use quando tiver uma implantação local do AlwaysOn |
| [Usar a replicação transacional do SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) |Use quando precisar minimizar o tempo de inatividade e não tiver uma implantação local do AlwaysOn |

## <a name="backup-and-restore"></a>Backup e restauração
Faça o backup do seu banco de dados com a compactação, copie o backup para a VM e, em seguida, restaure o banco de dados. Se o arquivo de backup tiver mais de 1 TB, você deverá dividi-lo, já que o tamanho máximo de um disco de VM é de 1 TB. Use as seguintes etapas gerais para migrar um banco de dados do usuário usando este método manual:

1. Execute um backup de banco de dados completo para um caminho local.
2. Crie ou carregue uma máquina virtual com a versão do SQL Server desejada.
3. Configure a conectividade com base em seus requisitos de instalação. Consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure (Gerenciador de Recursos)](virtual-machines-windows-sql-connect.md).
4. Copie os arquivos de backup para sua VM usando a área de trabalho remota, Windows Explorer ou comando de cópia em um prompt de comando.

## <a name="backup-to-url-and-restore"></a>Fazer backup para URL e restaurar
Em vez de fazer o backup em um arquivo local, é possível usar o [backup para URL](https://msdn.microsoft.com/library/dn435916.aspx) e, em seguida, restaurar da URL para a VM. Com o SQL Server 2016, conjuntos de backup divididos têm suporte, são recomendados para um melhor desempenho e são necessários para exceder os limites de tamanho por blob. Para bancos de dados muito grandes, é recomendado o uso do [Serviço de Importação/Exportação do Windows](../../../storage/common/storage-import-export-service.md) .

## <a name="detach-and-attach-from-url"></a>Desanexar e anexar da URL
Desanexar os banco de dados e arquivos de log e transferi-los para [Armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/dn385720.aspx). Em seguida, anexe o banco de dados da URL na sua VM do Azure. Use esta opção se desejar que os arquivos de banco de dados físicos residam no armazenamento de Blobs. Isso pode ser útil para bancos de dados muito grandes. Use as seguintes etapas gerais para migrar um banco de dados do usuário usando este método manual:

1. Desanexe os arquivos de banco de dados da instância de banco de dados local.
2. Copie os arquivos desanexados do banco de dados no armazenamento de blob do Azure usando o [utilitário de linha de comando AZCopy](../../../storage/common/storage-use-azcopy.md).
3. Anexe os arquivos de banco de dados da URL do Azure para a instância do SQL Server na VM do Azure.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converter a VM e carregar a URL e implantar como uma nova VM
Use esse método para migrar todos os sistemas e bancos de dados de usuário em uma instância local do SQL Server para a máquina virtual do Azure. Use as seguintes etapas gerais para migrar de uma instância inteira do SQL Server usando este método manual:

1. Converta máquinas físicas ou virtuais em VHDs do Hyper-V usando o [Conversor de Máquina Virtual da Microsoft](https://technet.microsoft.com/library/dn874008(v=ws.11).aspx).
2. Carregue arquivos de VHD no armazenamento do Azure usando o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3. Implante uma nova máquina virtual usando o VHD carregado.

> [!NOTE]
> Para migrar um aplicativo inteiro, use o [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md).

## <a name="ship-hard-drive"></a>Remeter a unidade de disco rígido
Use o [método do Serviço de Importação/Exportação do Windows](../../../storage/common/storage-import-export-service.md) para transferir grandes quantidades de dados de arquivo para o armazenamento de Blob do Azure em situações em que o carregamento pela rede seja extremamente caro ou inviável. Com esse serviço, você envia um ou mais discos rígidos contendo esses dados para um data center do Azure, onde os dados serão carregados para sua conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como executar o SQL Server em Máquinas Virtuais do Azure, veja [Visão geral do SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

Para obter instruções sobre como criar uma Máquina Virtual do Azure SQL Server de uma imagem capturada, confira [Tips & Tricks on ‘cloning’ Azure SQL virtual machines from captured images](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) (Dicas e truques ao 'clonar' as máquinas virtuais do Azure SQL de imagens capturadas) no blog dos Engenheiros do CSS SQL Server.

