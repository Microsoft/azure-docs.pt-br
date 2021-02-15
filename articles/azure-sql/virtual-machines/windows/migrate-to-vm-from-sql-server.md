---
title: Migrar um banco de dados do SQL Server para o SQL Server em uma máquina virtual | Microsoft Docs
description: Saiba mais sobre como migrar um banco de dados de usuário local para o SQL Server em uma máquina virtual do Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 00fd08c6-98fa-4d62-a3b8-ca20aa5246b1
ms.service: virtual-machines-sql
ms.workload: iaas-sql-server
ms.tgt_pltfrm: vm-windows-sql-server
ms.subservice: migration
ms.topic: how-to
ms.date: 08/18/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f6e9009040d2d02702f8a71c352716491d07d1f7
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704297"
---
# <a name="migrate-a-sql-server-database-to-sql-server-on-an-azure-virtual-machine"></a>Migrar um banco de dados do SQL Server para o SQL Server em uma máquina virtual do Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Existem várias maneiras para migrar um banco de dados de usuário do SQL Server local para o SQL Server em uma VM (máquina virtual) do Azure. Este artigo discute brevemente diversos métodos e recomenda o melhor método para vários cenários.


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

  > [!NOTE]
  > O SQL Server 2008 e o SQL Server 2008 R2 estão se aproximando do [fim do ciclo de vida de suporte](https://www.microsoft.com/sql-server/sql-server-2008) para instâncias locais. Para estender o suporte, você pode migrar sua Instância do SQL Server para uma VM do Azure ou comprar Atualizações de Segurança Estendidas para mantê-las localmente. Para saber mais, confira [Estender o suporte para SQL Server 2008 e 2008 R2 com o Azure](sql-server-2008-extend-end-of-support.md)

## <a name="what-are-the-primary-migration-methods"></a>Quais são os principais métodos de migração?

Os principais métodos de migração são:

* Executar um backup local usando a compactação e, em seguida, copiar manualmente o arquivo de backup para VM do Azure.
* Execute um backup para URL e, em seguida, restaure na VM do Azure a partir da URL.
* Desanexar os arquivos de log e de dados, copiá-los no Armazenamento de Blobs do Azure e, em seguida, anexá-los ao SQL Server, da URL para a VM do Azure.
* Converter o computador físico local em um VHD do Hyper-V, carregá-lo no Armazenamento de Blobs do Azure e, em seguida, implantá-lo como nova VM usando o VHD carregado.
* Remeter a unidade de disco rígido usando o Serviço de Importação/Exportação do Windows.
* Caso você tenha uma implantação local do Grupo de Disponibilidade AlwaysOn, use o [Assistente para Adicionar Réplica do Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) para criar uma réplica no Azure, executar o failover e apontar os usuários para a instância do banco de dados do Azure.
* Usar a [replicação transacional](/sql/relational-databases/replication/transactional/transactional-replication) do SQL Server para configurar a instância do SQL Server do Azure como um assinante, desabilitar a replicação e apontar os usuários para a instância de banco de dados do Azure.

> [!TIP]
> Você também pode usar essas mesmas técnicas para mover bancos de dados entre VMs do SQL Server no Azure. Por exemplo, não há suporte para atualizar uma VM da imagem da galeria do SQL Server de uma versão/edição para outra. Nesse caso, você deve criar uma nova VM do SQL Server com a nova versão/edição e, em seguida, usar uma das técnicas de migração neste artigo para mover seus bancos de dados. 

## <a name="choose-a-migration-method"></a>Escolher um método de migração

Para obter o melhor desempenho de transferência de dados, migre os arquivos de dados para a VM do Azure usando um arquivo de backup compactado.

Para minimizar o tempo de inatividade durante o processo de migração do banco de dados, use a opção AlwaysOn ou a opção de replicação transacional.

Se não for possível usar os métodos acima, migre seu banco de dados manualmente. Em geral, você começa com um backup de banco de dados, segue uma cópia do backup do banco de dados no Azure e, em seguida, restaura o banco de dados. Você também pode copiar os próprios arquivos do banco de dados para o Azure e anexá-los. Existem vários métodos pelos quais você pode realizar esse processo manual de migrar um banco de dados para uma VM do Azure.

> [!NOTE]
> Quando você atualizar para o SQL Server 2014 ou para o SQL Server 2016 de versões anteriores do SQL Server, deverá considerar se as alterações são necessárias. É recomendável resolver todas as dependências nos recursos sem suporte da nova versão do SQL Server como parte do seu projeto de migração. Para saber mais sobre os cenários e as edições com suporte, consulte [Atualizar para o SQL Server](/sql/database-engine/install-windows/upgrade-sql-server).

A tabela a seguir lista os principais métodos de migração e discute quando o uso de cada método é mais apropriado.

| Método | Versão do banco de dados de origem | Versão do banco de dados de destino | Restrição de tamanho do backup do banco de dados de origem | Observações |
| --- | --- | --- | --- | --- |
| [Executar um backup local usando compactação e copiar manualmente o arquivo de backup para a máquina virtual do Azure](#back-up-and-restore) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](../../../index.yml) | Essa técnica é simples e bem testada para mover bancos de dados entre computadores. |
| [Execute um backup para URL e restaure na máquina virtual do Azure desde a URL](#backup-to-url-and-restore-from-url) |SQL Server 2012 SP1 CU2 ou posterior | SQL Server 2012 SP1 CU2 ou posterior | < 12,8 TB para SQL Server 2016, caso contrário, < 1 TB | Este método é somente outra forma de mover o arquivo de backup para a VM usando o armazenamento do Azure. |
| [Desanexar e copiar os dados e os arquivos de log para o Armazenamento de Blobs do Azure e anexar da URL ao SQL Server na máquina virtual do Azure](#detach-and-attach-from-a-url) | SQL Server 2005 ou posterior |SQL Server 2014 ou posterior | [Limite de armazenamento da VM do Azure](../../../index.yml) | Use este método quando pretender [armazenar esses arquivos usando o serviço de armazenamento de Blob do Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) e anexá-los ao SQL Server em execução em uma VM do Azure, especialmente com bancos de dados muito grandes. |
| [Converta a máquina local em VHDs do Hyper-V, carregue no armazenamento de Blob do Azure e, em seguida, implante uma nova máquina virtual usando o VHD carregado](#convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](../../../index.yml) |Use ao [trazer sua própria licença de SQL Server](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md), ao migrar um banco de dados que será executado em uma versão mais antiga do SQL Server, ou ao migrar os bancos de dados do sistema e do usuário juntos como parte da migração do Database dependente de outros bancos de dados de usuário e/ou bancos de dados do sistema. |
| [Remeter o disco rígido usando o Serviço de Importação/Exportação do Windows](#ship-a-hard-drive) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](../../../index.yml) |Use o [Serviço de Importação/Exportação do Windows](../../../import-export/storage-import-export-service.md) quando o método de cópia manual for muito lento, como com bancos de dados muito grandes |
| [Use o Assistente para Adicionar Réplica do Azure](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-onprem-availability) |SQL Server 2012 ou posterior |SQL Server 2012 ou posterior |[Limite de armazenamento da VM do Azure](../../../index.yml) |Minimize o tempo de inatividade, use quando tiver uma implantação local do AlwaysOn |
| [Usar a replicação transacional do SQL Server](/sql/relational-databases/replication/transactional/transactional-replication) |SQL Server 2005 ou posterior |SQL Server 2005 ou posterior |[Limite de armazenamento da VM do Azure](../../../index.yml) |Use quando precisar minimizar o tempo de inatividade e não tiver uma implantação local Always On |

## <a name="back-up-and-restore"></a>Fazer backup e restaurar

Faça o backup do seu banco de dados com a compactação, copie o backup para a VM e, em seguida, restaure o banco de dados. Se o arquivo de backup tiver mais de 1 TB, você deverá criar um conjunto dividi-lo, pois o tamanho máximo de um disco de VM é de 1 TB. Use as seguintes etapas gerais para migrar um banco de dados do usuário usando este método manual:

1. Execute um backup de banco de dados completo para um caminho local.
2. Crie ou carregue uma máquina virtual com a versão desejada do SQL Server.
3. Configure a conectividade com base em seus requisitos de instalação. Consulte [Conectar-se a uma Máquina Virtual do SQL Server no Azure (Gerenciador de Recursos)](ways-to-connect-to-sql.md).
4. Copie os arquivos de backup para sua VM usando a área de trabalho remota, o Windows Explorer ou o comando de cópia de um prompt de comando.

## <a name="backup-to-url-and-restore-from-url"></a>Backup para URL e restauração da URL

Em vez de fazer backup em um arquivo local, você pode usar [backup para URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) e, em seguida, restaurar da URL para a VM. O SQL Server 2016 é compatível com conjuntos de backup distribuídos. Eles são recomendados por questões de desempenho e necessários para exceder os limites de tamanho por blob. Para bancos de dados muito grandes, é recomendado o uso do [Serviço de Importação/Exportação do Windows](../../../import-export/storage-import-export-service.md) .

## <a name="detach-and-attach-from-a-url"></a>Desanexar e anexar de uma URL

Desanexar os banco de dados e arquivos de log e transferi-los para [Armazenamento de Blobs do Azure](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure). Em seguida, anexe o banco de dados da URL na sua VM do Azure. Use esse método se desejar que os arquivos de banco de dados físicos residam no armazenamento de BLOBs, o que pode ser útil para bancos de dados muito grandes. Use as seguintes etapas gerais para migrar um banco de dados do usuário usando este método manual:

1. Desanexe os arquivos de banco de dados da instância de banco de dados local.
2. Copie os arquivos desanexados do banco de dados no Armazenamento de Blobs do Azure usando o [utilitário de linha de comando AZCopy](../../../storage/common/storage-use-azcopy-v10.md).
3. Anexe os arquivos de banco de dados da URL do Azure para a instância do SQL Server na VM do Azure.

## <a name="convert-to-a-vm-upload-to-a-url-and-deploy-as-a-new-vm"></a>Converter para uma VM, carregar em uma URL e implantar como uma nova VM

Use esse método para migrar todos os sistemas e bancos de dados de usuário em uma instância local do SQL Server para uma máquina virtual do Azure. Use as seguintes etapas gerais para migrar de uma instância inteira do SQL Server usando este método manual:

1. Converta máquinas físicas ou virtuais em VHDs do Hyper-V.
2. Carregue arquivos de VHD no armazenamento do Azure usando o [cmdlet Add-AzureVHD](/previous-versions/azure/dn495173(v=azure.100)).
3. Implante uma nova máquina virtual usando o VHD carregado.

> [!NOTE]
> Para migrar um aplicativo inteiro, use o [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md).

## <a name="ship-a-hard-drive"></a>Enviar uma unidade de disco rígido

Use o [método do Serviço de Importação/Exportação do Windows](../../../import-export/storage-import-export-service.md) para transferir grandes quantidades de dados de arquivo para o armazenamento de Blob do Azure em situações em que o carregamento pela rede seja extremamente caro ou inviável. Com esse serviço, você envia um ou mais unidades de disco rígido contendo esses dados para um data center do Azure, em que os dados serão carregados na sua conta de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, veja [SQL Server na visão geral de Máquinas Virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

> [!TIP]
> Em caso de dúvidas sobre máquinas virtuais do SQL Server, consulte as [Perguntas frequentes](frequently-asked-questions-faq.md).

Para obter instruções sobre como criar SQL Server em uma Máquina Virtual do Azure com base em uma imagem capturada, confira [Dicas e truques para "clonar" máquinas virtuais do Azure SQL com base em imagens capturadas](/archive/blogs/psssql/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images) no blog dos Engenheiros do CSS SQL Server.