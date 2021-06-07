---
title: Problemas conhecidos e limitações com migrações online para o Azure SQL Instância Gerenciada
description: Saiba mais sobre problemas conhecidos/limitações de migração associadas a migrações online para o Azure SQL Instância Gerenciada.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 46c5f5995c7a1d4eb074f6c1b25ecaad7e2da37e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98695519"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>Problemas conhecidos/limitações de migração com migrações online para o Azure SQL Instância Gerenciada

Os problemas conhecidos e as limitações associadas a migrações online do SQL Server para o SQL Instância Gerenciada do Azure são descritos abaixo.

> [!IMPORTANT]
> Com as migrações online do SQL Server para o banco de dados SQL do Azure, não há suporte para a migração de tipos de dado de SQL_variant.

## <a name="backup-requirements"></a>Requisitos de backup

- **Backups com soma de verificação**

    O serviço de migração de banco de dados do Azure usa o método de backup e restauração para migrar seus bancos de dados locais para o SQL Instância Gerenciada. O serviço de migração de banco de dados do Azure só dá suporte a backups criados usando checksum

    [Habilitar ou desabilitar somas de verificação de backup durante backup ou restauração (SQL Server)](/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server).

    > [!NOTE]
    > Se você levar os backups de banco de dados com compactação, a soma de verificação será um comportamento padrão, a menos que explicitamente desabilitado.

    Com as migrações offline, se você escolher, **deixarei o serviço de migração de banco de dados do Azure...**, o serviço de migração de banco de dados do Azure pegará o backup do banco de dados com a opção checksum habilitada

- **Mídia de backup**

    Certifique-se de fazer cada backup em uma mídia de backup separada (arquivos de backup). O serviço de migração de banco de dados do Azure não dá suporte a backups que são anexados a um único arquivo de backup. Faça backups completos de log e logs para separar arquivos de backup.

## <a name="data-and-log-file-layout"></a>Layout do arquivo de dados e log

- **Número de arquivos de log**

    O serviço de migração de banco de dados do Azure não dá suporte a bancos de dados com vários arquivos de log. Se você tiver vários arquivos de log, reduza-os e reorganize-os em um único arquivo de log de transações. Como não é possível realizar o log remoto de arquivos que não estão vazios, você precisa fazer backup do arquivo de log primeiro.

## <a name="sql-server-features"></a>recursos do SQL Server

- **FileStream/filetables**

    Atualmente, o SQL Instância Gerenciada não dá suporte a FileStream e filetables. Para cargas de trabalho dependentes desses recursos, recomendamos que você opte por servidores SQL em execução em VMs do Azure como seu destino do Azure.

- **Tabelas na memória**

    O OLTP na memória está disponível nas camadas Premium e Comercialmente Crítico para SQL Instância Gerenciada; a camada de Uso Geral não dá suporte a OLTP na memória.

## <a name="migration-resets"></a>Redefinições de migração

- **Implantações**

    O SQL Instância Gerenciada é um serviço PaaS com aplicação automática de patches e atualizações de versão. Durante a migração do seu Instância Gerenciada SQL, atualizações não críticas são mantidas por até 36 horas. Posteriormente (e para atualizações críticas), se a migração for interrompida, o processo será redefinido para um estado de restauração completa.

    A transferência de migração só pode ser chamada depois que o backup completo é restaurado e é atualizado com todos os backups de log. Se seus migrações de migração de produção forem afetados, contate o [alias de comentários do Azure DMS](mailto:dmsfeedback@microsoft.com).

## <a name="smb-file-share-connectivity"></a>Conectividade de compartilhamento de arquivos SMB

Problemas de conexão com o compartilhamento de arquivos SMB provavelmente são causados por um problema de permissões. 

Para testar a conectividade do compartilhamento de arquivos SMB, siga estas etapas: 

1. Salve um backup no compartilhamento de arquivos SMB. 
1. Verifique a conectividade de rede entre a sub-rede do serviço de migração de banco de dados do Azure e o SQL Server de origem. A maneira mais fácil de fazer isso é implantar uma máquina virtual SQL Server na sub-rede DMS e conectar-se ao SQL Server de origem usando SQL Server Management Studio. 
1. Restaure o cabeçalho no SQL Server de origem do backup no FileShare: 

   ```sql
   RESTORE HEADERONLY   
   FROM DISK = N'\\<SMB file share path>\full.bak'
   ```

Se você não conseguir se conectar ao compartilhamento de arquivos, configure as permissões com estas etapas: 

1. Navegue até o compartilhamento de arquivos usando o explorador de arquivos. 
1. Clique com o botão direito do mouse no compartilhamento de arquivos e selecione Propriedades. 
1. Escolha a guia **compartilhamento** e selecione **compartilhamento avançado**. 
1. Adicione a conta do Windows usada para migração e atribua a ela acesso de controle total. 
1. Adicione a conta de serviço SQL Server e atribua a ela acesso de controle total. Verifique a **SQL Server Configuration Manager** da conta de serviço do SQL Server se você não tiver certeza de qual conta está sendo usada. 

   :::image type="content" source="media/known-issues-azure-sql-db-managed-instance-online/assign-fileshare-permissions.png" alt-text="Conceda acesso de controle total às contas do Windows usadas para migração e para a conta de serviço SQL Server. ":::

