---
title: Migrar disco de log para ultra Disk
description: Saiba como migrar seu SQL Server no disco de log de VM (máquina virtual) do Azure para um Ultradisk do Azure para tirar proveito do alto desempenho e baixa latência.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: e8410b4e0997798eba5ee91f361c3a5f1ce47ef1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586294"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>Migrar disco de log para ultra Disk
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Os ultra discos do Azure fornecem alta taxa de transferência, IOPS alta e armazenamento de disco consistentemente de baixa latência para SQL Server na VM (máquina virtual) do Azure. 

Este artigo ensina a migrar seu disco de log para um ultra SSD para aproveitar os benefícios de desempenho oferecidos por ultra discos. 

## <a name="back-up-database"></a>Backup do banco de dados

Conclua um [backup completo](backup-restore.md) do seu banco de dados. 

## <a name="attach-disk"></a>Anexar disco

Anexe o SSD Ultra à sua máquina virtual depois de habilitar a compatibilidade de ultradisk na VM. 

O ultra Disk tem suporte em um subconjunto de regiões e tamanhos de VM. Antes de continuar, valide se sua VM está em uma região, zona e tamanho que dá suporte a ultra Disk. Você pode [determinar e validar o tamanho e a região da VM](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) usando o CLI do Azure ou o PowerShell. 

### <a name="enable-compatibility"></a>Habilitar compatibilidade

Para habilitar a compatibilidade, siga estas etapas:

1. Vá para sua máquina virtual no [portal do Azure](https://portal.azure.com/). 
1. Pare/Desaloque a máquina virtual. 
1. Selecione **discos** em **configurações** e, em seguida, selecione **configurações adicionais**. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Selecione configurações adicionais para discos em configurações na portal do Azure":::

1. Selecione **Sim** para **habilitar a compatibilidade de ultra Disk**. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/enable-ultra-disks-existing-vm.png" alt-text="Captura de tela que mostra a opção Sim.":::

1. Selecione **Salvar**. 



### <a name="attach-disk"></a>Anexar disco

Use o portal do Azure para anexar um ultra Disk à sua máquina virtual. Para obter detalhes, consulte [anexar um disco ultra](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk).

Depois que o disco for anexado, inicie sua VM mais uma vez usando o portal do Azure. 



## <a name="format-disk"></a>Formatar disco

Conecte-se à sua máquina virtual e formate o ultra Disk.  

Para formatar o ultra Disk, siga estas etapas:

1. Conecte-se à sua VM usando protocolo RDP (RDP).
1. Use o [Gerenciamento de disco](/windows-server/storage/disk-management/overview-of-disk-management) para formatar e particionar o ultra disco recentemente anexado. 


## <a name="use-disk-for-log"></a>Usar disco para o log

Configure SQL Server para usar a nova unidade de log. Você pode fazer isso usando Transact-SQL (T-SQL) ou SQL Server Management Studio (SSMS). A conta usada para a conta de serviço de SQL Server deve ter controle total do novo local do arquivo de log. 

### <a name="configure-permissions"></a>Configurar permissões

1. Verifique a conta de serviço usada pelo SQL Server. Você pode fazer isso usando SQL Server Configuration Manager ou Services. msc.
1. Navegue até o novo disco. 
1. Crie uma pasta (ou várias pastas) a ser usada para o arquivo de log. 
1. Clique com o botão direito do mouse na pasta e selecione **Propriedades**.
1. Na guia **segurança** , conceda acesso de controle total à conta de serviço SQL Server. 
1. Selecione **OK**  para salvar suas configurações. 
1. Repita isso para cada pasta de nível raiz em que você planeja ter dados SQL. 

### <a name="use-new-log-drive"></a>Usar nova unidade de log 

Depois que a permissão tiver sido concedida, use o Transact-SQL (T-SQL) ou o SQL Server Management Studio (SSMS) para desanexar o banco de dados e mover os arquivos de log existentes para o novo local.

   > [!CAUTION]
   > Desanexar o banco de dados o colocará offline, fechará as conexões e reverterá as transações em andamento. Continue com cautela e durante uma janela de manutenção em tempo de inatividade. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL (T-SQL)](#tab/tsql)

Use o T-SQL para mover os arquivos existentes para um novo local:

1. Conecte-se ao banco de dados no SQL Server Management Studio e abra uma nova janela de **consulta** . 
1. Obter os arquivos e locais existentes:

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. Desanexar o banco de dados: 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. Use o explorador de arquivos para mover o arquivo de log para o novo local no disco Ultra. 

1. Anexe o banco de dados, especificando os novos locais de arquivo: 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

Neste ponto, o banco de dados fica online com o log no novo local. 



# <a name="sql-server-management-studio-ssms"></a>[SQL Server Management Studio (SSMS)](#tab/ssms)

Use o SSMS para mover os arquivos existentes para um novo local:

1. Conecte-se ao seu banco de dados no SQL Server Management Studio (SSMS). 
1. Clique com o botão direito do mouse no banco de dados, selecione **Propriedades** e selecione **arquivos**. 
1. Anote o caminho dos arquivos existentes. 
1. Selecione **OK** para fechar a caixa de diálogo. 
1. Clique com o botão direito do mouse no banco de dados, selecione **tarefas**  >  **desanexar**. 
1. Siga o assistente para desanexar o banco de dados. 
1. Use o explorador de arquivos para mover manualmente o arquivo de log para o novo local.
1. Anexar o banco de dados no SQL Server Management Studio
   1. **Clique com** o botão direito do mouse no Pesquisador de **objetos** e selecione **anexar banco de dados**. 
   1. Usando a caixa de diálogo, adicione cada arquivo, incluindo o arquivo de log em seu novo local. 
   1. Selecione **OK** para anexar o banco de dados. 

Neste ponto, o banco de dados fica online com o log no novo local.

---


## <a name="next-steps"></a>Próximas etapas

Examine as [práticas recomendadas de desempenho](performance-guidelines-best-practices.md) para obter configurações adicionais para melhorar o desempenho. 

Para obter uma visão geral de SQL Server em máquinas virtuais do Azure, consulte os seguintes artigos:

- [Visão geral do SQL Server em VMs do Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Visão geral do SQL Server em VMs do Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
