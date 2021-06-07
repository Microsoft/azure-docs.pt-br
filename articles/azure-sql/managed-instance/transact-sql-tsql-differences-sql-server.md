---
title: Diferenças de T-SQL entre SQL Server & SQL do Azure Instância Gerenciada
description: Este artigo discute as diferenças de Transact-SQL (T-SQL) entre um Azure SQL Instância Gerenciada e SQL Server.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.devlang: ''
ms.topic: reference
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, bonova, danil
ms.date: 3/16/2021
ms.custom: seoapril2019, sqldbrb=1
ms.openlocfilehash: 227b573d3771efd3fd36e6d3d6222696647849f7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644922"
---
# <a name="t-sql-differences-between-sql-server--azure-sql-managed-instance"></a>Diferenças de T-SQL entre SQL Server & SQL do Azure Instância Gerenciada
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Este artigo resume e explica as diferenças na sintaxe e no comportamento entre o Azure SQL Instância Gerenciada e o SQL Server. 


O SQL Instância Gerenciada fornece alta compatibilidade com o mecanismo de banco de dados do SQL Server e a maioria dos recursos tem suporte em um Instância Gerenciada SQL.

![Migração fácil de SQL Server](./media/transact-sql-tsql-differences-sql-server/migration.png)

Há algumas limitações de PaaS que são introduzidas no SQL Instância Gerenciada e algumas alterações de comportamento em comparação com SQL Server. As diferenças são divididas nas seguintes categorias: <a name="Differences"></a>

- A [disponibilidade](#availability), incluindo as diferenças nos [Grupos de Disponibilidade AlwaysOn](#always-on-availability-groups) e em [backups](#backup).
- [Segurança](#security) inclui as diferenças em [auditoria](#auditing), [certificados](#certificates), [credenciais](#credential), [provedores de criptografia](#cryptographic-providers), [logons e usuários](#logins-and-users) e [chave de serviço e chave mestra de serviço](#service-key-and-service-master-key).
- [Configuração](#configuration) inclui as diferenças em [extensão do pool de buffers](#buffer-pool-extension), [ordenação](#collation), [níveis de compatibilidade](#compatibility-levels), [espelhamento de banco de dados](#database-mirroring), [opções de banco de dados](#database-options), [SQL Server Agent](#sql-server-agent) e [opções de tabela](#tables).
- [Funcionalidades](#functionalities) incluem [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transações distribuídas](#distributed-transactions), [eventos estendidos](#extended-events), [bibliotecas externas](#external-libraries), [filestream e FileTable](#filestream-and-filetable), [Pesquisa semântica de texto completo](#full-text-semantic-search), [servidores vinculados](#linked-servers), [PolyBase](#polybase), [Replicação](#replication), [RESTORE](#restore-statement), [Service Broker](#service-broker) e [procedimentos armazenados, funções e gatilhos](#stored-procedures-functions-and-triggers).
- [Configurações de ambiente](#Environment) como VNets e configurações de sub-rede.

A maioria desses recursos são restrições de arquitetura e representam recursos de serviço.

Os problemas temporários conhecidos que são descobertos no SQL Instância Gerenciada e serão resolvidos no futuro são descritos na [página notas de versão](../database/doc-changes-updates-release-notes.md).

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability-groups"></a><a name="always-on-availability-groups"></a>Grupos de Disponibilidade AlwaysOn

A [alta disponibilidade](../database/high-availability-sla.md) é incorporada ao SQL instância gerenciada e não pode ser controlada por usuários. As instruções a seguir não são compatíveis:

- [CRIAR ENDPOINT … PARA DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DESCARTAR GRUPO DE DISPONIBILIDADE](/sql/t-sql/statements/drop-availability-group-transact-sql)
- A cláusula [SET HADR](/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da instrução [ALTERAR BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

O SQL Instância Gerenciada tem backups automáticos, para que os usuários possam criar backups de banco de dados completos `COPY_ONLY` . Não há suporte para backups de instantâneo de arquivo, log e diferencial.

- Com um Instância Gerenciada SQL, você pode fazer backup de um banco de dados de instância somente para uma conta de armazenamento de BLOBs do Azure:
  - Apenas `BACKUP TO URL` tem suporte.
  - `FILE`, `TAPE` e dispositivos de backup não são compatíveis.
- A maioria das opções gerais `WITH` são compatíveis.
  - `COPY_ONLY` é obrigatório.
  - Não há suporte para `FILE_SNAPSHOT`.
  - Opções de fita: `REWIND`, `NOREWIND`, `UNLOAD` e `NOUNLOAD` não são compatíveis.
  - Opções específicas de log: `NORECOVERY`, `STANDBY` e `NO_TRUNCATE` não são compatíveis.

Limitações: 

- Com um Instância Gerenciada do SQL, você pode fazer backup de um banco de dados de instância em um backup com até 32 faixas, o que é suficiente para bancos de dados de até 4 TB se a compactação de backup for usada.
- Não é possível executar `BACKUP DATABASE ... WITH COPY_ONLY` em um banco de dados que esteja criptografado com TDE (Transparent Data Encryption) gerenciado por serviços. O TDE gerenciado por serviço força os backups a serem criptografados com uma chave TDE interna. A chave não pode ser exportada, portanto, não é possível restaurar o backup. Use backups automáticos e uma restauração pontual ou use [ TDE (BYOK) gerenciado pelo cliente](../database/transparent-data-encryption-tde-overview.md#customer-managed-transparent-data-encryption---bring-your-own-key) em vez disso. Você também pode desabilitar a criptografia no banco de dados.
- Backups nativos feitos em um Instância Gerenciada não podem ser restaurados para um SQL Server. Isso ocorre porque Instância Gerenciada tem uma versão de banco de dados interna maior em comparação com qualquer versão do SQL Server.
- O tamanho máximo de distribuição de backup usando o `BACKUP` comando no SQL instância gerenciada é 195 GB, que é o tamanho máximo do blob. Aumente o número de faixas no comando de backup para reduzir o tamanho da faixa individual e permaneça dentro desse limite.

    > [!TIP]
    > Para solucionar essa limitação, ao fazer backup de um banco de dados de qualquer SQL Server em um ambiente local ou em uma máquina virtual, você pode:
    >
    > - Faça backup em `DISK` em vez de fazer backup em `URL`.
    > - Carregar os arquivos no armazenamento de Blobs.
    > - Restaurar no SQL Instância Gerenciada.
    >
    > O `Restore` comando no SQL instância gerenciada dá suporte a tamanhos de blob maiores nos arquivos de backup porque um tipo de blob diferente é usado para o armazenamento dos arquivos de backup carregados.

Para obter informações sobre backups usando o T-SQL, consulte [BACKUP](/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre auditoria no Microsoft Azure SQL e no SQL Server são:

- Com o SQL Instância Gerenciada, a auditoria funciona no nível do servidor. Os arquivos de log `.xel` são armazenados no armazenamento de Blob do Azure.
- Com o banco de dados SQL do Azure, a auditoria funciona no nível do banco de dados. Os arquivos de log `.xel` são armazenados no armazenamento de Blob do Azure.
- Com SQL Server, no local ou em máquinas virtuais, a auditoria funciona no nível do servidor. Os eventos são armazenados no sistema de arquivos ou nos logs de eventos do Windows.
 
A auditoria de XEvent no SQL Instância Gerenciada dá suporte a destinos do armazenamento de BLOBs do Azure. Logs de arquivo e do Windows não são compatíveis.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- Uma nova sintaxe `TO URL` é fornecida e permite que você especifique a URL do contêiner de armazenamento de blobs do Azure onde arquivos `.xel` são colocados.
- A sintaxe `TO FILE` não tem suporte porque o SQL instância gerenciada não pode acessar compartilhamentos de arquivos do Windows.

Para obter mais informações, consulte: 

- [CRIAR AUDITORIA DE SERVIDOR](/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTERAR AUDITORIA DE SERVIDOR](/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

O SQL Instância Gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- O arquivo `CREATE FROM`/`BACKUP TO` não é compatível com certificados.
- O certificado `CREATE`/`BACKUP` do `FILE`/`ASSEMBLY` não é compatível. Arquivos de chave privada não podem ser usados. 

Consulte [CRIAR CERTIFICADO](/sql/t-sql/statements/create-certificate-transact-sql) e [CERTIFICADO DE BACKUP](/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solução alternativa**: Em vez de criar backup de certificado e restaurar o backup, [obter o conteúdo binário do certificado e a chave privada, armazená-los como arquivo .sql e criar de binários](/sql/t-sql/functions/certencoded-transact-sql#b-copying-a-certificate-to-another-database):

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Somente o Azure Key Vault e identidades `SHARED ACCESS SIGNATURE` têm suporte. Não há suporte para usuários do Windows.

Consulte [CRIAR CREDENCIAL](/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENCIAL](/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provedores criptográficos

O SQL Instância Gerenciada não pode acessar arquivos, portanto, os provedores criptográficos não podem ser criados:

- Não há suporte para `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [CRIAR PROVEDOR CRIPTOGRÁFICO](/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- Não há suporte para `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [ALTERAR PROVEDOR CRIPTOGRÁFICO](/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Logons e usuários

- Logons do SQL criados usando `FROM CERTIFICATE`, `FROM ASYMMETRIC KEY` e `FROM SID` são compatíveis. Consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql).
- Os principais do servidor do Azure Active Directory (Azure AD) (logons) criadas com a sintaxe [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current&preserve-view=true) ou [CREATE USER FROM LOGIN [Azure AD Login]](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true) não são compatíveis. Esses são logons são criados no nível do servidor.

    O SQL Instância Gerenciada dá suporte a entidades de banco de dados do Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER` . Isso também é conhecido como usuários de banco de dados contidos do Azure AD.

- Logons do Windows criados com a sintaxe `CREATE LOGIN ... FROM WINDOWS` não têm suporte. Use logons e usuário do Microsoft Azure Active Directory.
- O usuário do Azure AD que criou a instância tem [privilégios irrestritos de administrador](../database/logins-create-manage.md).
- Usuários de nível de banco de dados não administradores do Azure AD podem ser criados usando a sintaxe `CREATE USER ... FROM EXTERNAL PROVIDER`. Consulte [CRIAR USUÁRIO ... FROM EXTERNAL PROVIDER](../database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities).
- As entidades de segurança do servidor do Azure AD (logons) dão suporte a recursos SQL em apenas um SQL Instância Gerenciada. Recursos que exigem a interação entre instâncias, independentemente de estarem dentro do mesmo locatário do Azure AD ou em locatários diferentes, para usuários do Azure AD. Exemplos de recursos desse tipo são:

  - Replicação transacional de SQL.
  - Servidor de link.

- Não há suporte para logons do Azure AD mapeados para um grupo do Azure AD como proprietário do banco de dados.
- A representação de entidades de segurança no nível do servidor do Azure AD usando outras entidades de segurança do Azure AD é compatível, como a cláusula [EXECUTE AS](/sql/t-sql/statements/execute-as-transact-sql). As limitações de executar como são:

  - EXECUTE AS USER não é compatível com usuários do Azure AD quando o nome é diferente do nome de logon. Por exemplo, quando o usuário é criado por meio da sintaxe CREATE USER [myAadUser] FROM LOGIN [john@contoso.com], e a tentativa de representação é por meio de EXEC AS USER = _myAadUser_. Ao criar um **USER** a partir de uma entidade de segurança do servidor (logon) do Azure AD, especifique o user_name como o mesmo login_name de **LOGON**.
  - Somente as entidades de segurança no nível do SQL Server (logons) que fazem parte do `sysadmin` podem executar as seguintes operações direcionadas a entidades de segurança do Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

  - Para representar um usuário com a instrução EXECUTE AS, o usuário precisa ser mapeado diretamente para a entidade de segurança de servidor do Azure AD (logon). Os usuários que são membros de grupos do Azure AD mapeados para entidades de segurança de servidor do Azure AD não podem ser efetivamente representados com a instrução EXECUTE AS, mesmo que o chamador tenha as permissões IMPERSONATE no nome de usuário especificado.

- A exportação/importação de banco de dados usando arquivos bacpac tem suporte para usuários do Azure AD no SQL Instância Gerenciada usando o [SSMS v 18.4 ou posterior](/sql/ssms/download-sql-server-management-studio-ssms), ou [SQLPackage.exe](/sql/tools/sqlpackage-download).
  - As seguintes configurações são compatíveis usando um arquivo de banco de dados bacpac: 
    - Exportar/importar um banco de dados entre diferentes instâncias de gerenciamento dentro do mesmo domínio do Azure AD.
    - Exportar um banco de dados do SQL Instância Gerenciada e importar para o banco de dados SQL dentro do mesmo domínio do Azure AD. 
    - Exporte um banco de dados do banco de dados SQL e importe para o SQL Instância Gerenciada no mesmo domínio do Azure AD.
    - Exporte um banco de dados do SQL Instância Gerenciada e importe para SQL Server (versão 2012 ou posterior).
      - Nessa configuração, todos os usuários do Azure AD são criados como SQL Server entidades de banco de dados (usuários) sem logons. O tipo de usuário é listado como `SQL` e é visível como `SQL_USER` em sys.database_principals). Suas permissões e funções permanecem nos metadados do banco de dados do SQL Server e podem ser usadas para representação. No entanto, eles não podem ser usados para acessar e fazer logon no SQL Server usando suas credenciais.

- Somente o logon da entidade de segurança no nível do servidor, que é criado pelo processo de provisionamento do SQL Instância Gerenciada, os membros das funções de servidor, como `securityadmin` ou ou `sysadmin` outros logons com a permissão ALTER ANY login no nível do servidor, podem criar entidades de segurança do Azure Ad Server (logons) no banco de dados mestre para SQL instância gerenciada.
- Se o logon for uma entidade de segurança SQL, somente os logons que fizerem parte da função `sysadmin` poderão usar o comando create para criar logons para uma conta do Azure AD.
- O logon do Azure AD deve ser um membro de um Azure AD dentro do mesmo diretório usado para o Azure SQL Instância Gerenciada.
- Entidades de segurança do servidor do Azure AD (logons) são visíveis no Pesquisador de Objetos a partir do SSMS 18.0 versão prévia 5.
- É permitida a sobreposição de entidades de segurança do servidor (logons) do Azure AD com uma conta de administrador do Azure AD. As entidades de segurança do servidor do Azure AD (logons) têm precedência sobre o administrador do Azure AD quando você resolve a entidade de segurança e aplica permissões ao SQL Instância Gerenciada.
- Durante a autenticação, a sequência a seguir é aplicada para resolver a entidade de segurança de autenticação:

    1. Se a conta do Azure AD existir como mapeada diretamente para a entidade de segurança do servidor do Azure AD (logon) (presente em sys.server_principals como tipo "E"), conceda acesso e aplique permissões da entidade de segurança do servidor do Azure AD (logon).
    2. Se a conta do Azure AD for um membro de um grupo do Azure AD mapeado para a entidade de segurança do servidor do Azure AD (logon) (presente em sys.server_principals como tipo "X"), conceda permissões de acesso e aplique o logon do grupo do Azure AD.
    3. Se a conta do Azure AD for um administrador especial configurado pelo portal do Azure AD para SQL Instância Gerenciada, que não existe em exibições do sistema SQL Instância Gerenciada, aplique permissões fixas especiais do administrador do Azure AD para SQL Instância Gerenciada (modo herdado).
    4. Se a conta do Azure AD existir como mapeada diretamente para um usuário do Azure AD em um banco de dados (em sys.database_principals como tipo "E"), conceda permissões de acesso e aplicação do usuário do banco de dados do Azure AD.
    5. Se a conta do Azure AD for membro de um grupo do Azure AD mapeado para um usuário do Azure AD em um banco de dados (em sys.database_principals como tipo "X"), conceda permissões de acesso e aplicação do logon do grupo do Azure AD.
    6. Se houver um logon do Azure AD mapeado para uma conta de usuário do Azure AD ou uma conta do grupo do Azure AD, resolvendo a autenticação do usuário, todas as permissões desse logon do Azure AD serão aplicadas.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave mestra de serviço

- [Backup da chave mestra](/sql/t-sql/statements/backup-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- A [restauração da chave mestra](/sql/t-sql/statements/restore-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- O [backup da chave mestra de serviço](/sql/t-sql/statements/backup-service-master-key-transact-sql) não é compatível (gerenciado pelo serviço de banco de dados SQL).
- A [restauração da chave mestra de serviço](/sql/t-sql/statements/restore-service-master-key-transact-sql) não é compatível (gerenciado pelo serviço de banco de dados SQL).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do pool de buffers

- A [extensão de pool de buffers](/sql/database-engine/configure-windows/buffer-pool-extension) não é compatível.
- Não há suporte para `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTERAR CONFIGURAÇÃO DO SERVIDOR](/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Collation

A ordenação de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificada como um parâmetro de criação. Consulte [Ordenações](/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade compatíveis são: 100, 110, 120, 130, 140 e 150.
- Não há suporte para níveis de compatibilidade abaixo de 100.
- O nível de compatibilidade padrão para novos bancos de dados é 140. Para bancos de dados restaurados, o nível de compatibilidade permanecerá inalterado se ele for 100 ou superior.

Consulte [ALTERAR Nível de Compatibilidade do BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento de banco de dados

Não há suporte para espelhamento de banco de dados.

- As opções `ALTER DATABASE SET PARTNER` e `SET WITNESS` não são suportadas.
- Não há suporte para `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... FOR DATABASE_MIRRORING](/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de banco de dados

- Não há suporte para vários arquivos de log.
- Não há suporte para objetos na memória na camada de serviço de Uso Geral. 
- Há um limite de 280 arquivos por instância de Uso Geral, o que implica um máximo de 280 arquivos por banco de dados. Os arquivos de log e de dados na camada de Uso Geral são contados para esse limite. [A camada de Comercialmente Crítico é compatível com 32.767 arquivos por banco de dados](./resource-limits.md#service-tier-characteristics).
- O banco de dados não pode conter grupos de arquivos que contenham dados de fluxo de arquivos. A restauração falhará se .bak contiver dados `FILESTREAM`. 
- Cada arquivo é colocado no Armazenamento de Blobs do Azure. A E/S e a taxa de transferência por arquivo dependem do tamanho de cada arquivo individual.

#### <a name="create-database-statement"></a>Instrução CREATE DATABASE

As seguintes limitações se aplicam a `CREATE DATABASE`:

- Arquivos e grupos de arquivos não podem ser definidos. 
- A opção `CONTAINMENT` não é compatível. 
- As opções `WITH` não são compatíveis. 
   > [!TIP]
   > Como alternativa, use `ALTER DATABASE` depois `CREATE DATABASE` para definir opções de banco de dados para adicionar arquivos ou para definir a contenção. 

- A opção `FOR ATTACH` não é compatível.
- A opção `AS SNAPSHOT OF` não é compatível.

Para saber mais, confira [CRIAR BANCO DE DADOS](/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>instrução ALTER DATABASE

Algumas propriedades de arquivo não podem ser definidas ou alteradas:

- O caminho do arquivo não pode ser especificado na instrução T-SQL `ALTER DATABASE ADD FILE (FILENAME='path')`. Remova `FILENAME` do script porque o SQL instância gerenciada coloca os arquivos automaticamente. 
- O nome do arquivo não pode ser alterado usando a instrução `ALTER DATABASE`.

As opções a seguir são definidas por padrão e não podem ser alteradas:

- `MULTI_USER`
- `ENABLE_BROKER`
- `AUTO_CLOSE OFF`

As seguintes opções não podem ser modificadas:

- `AUTO_CLOSE`
- `AUTOMATIC_TUNING(CREATE_INDEX=ON|OFF)`
- `AUTOMATIC_TUNING(DROP_INDEX=ON|OFF)`
- `DISABLE_BROKER`
- `EMERGENCY`
- `ENABLE_BROKER`
- `FILESTREAM`
- `HADR`
- `NEW_BROKER`
- `OFFLINE`
- `PAGE_VERIFY`
- `PARTNER`
- `READ_ONLY`
- `RECOVERY BULK_LOGGED`
- `RECOVERY_SIMPLE`
- `REMOTE_DATA_ARCHIVE` 
- `RESTRICTED_USER`
- `SINGLE_USER`
- `WITNESS`

Algumas `ALTER DATABASE` instruções (por exemplo, [set contenção](/sql/relational-databases/databases/migrate-to-a-partially-contained-database#converting-a-database-to-partially-contained-using-transact-sql)) podem falhar transitóriamente, por exemplo, durante o backup automatizado do banco de dados ou logo após a criação de um banco de dados. Nesse caso, a `ALTER DATABASE` instrução deve ser repetida. Para obter mais informações sobre mensagens de erro relacionadas, consulte a [seção comentários](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&tabs=sqlpool&view=azuresqldb-mi-current#remarks-2).

Para saber mais, confira [ALTERAR BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- No momento, a habilitação e a desabilitação do SQL Server Agent não é compatível com a Instância Gerenciada de SQL. O SQL Agent sempre está em execução.
- Não há suporte para o gatilho de agenda de trabalho com base em uma CPU ociosa.
- As configurações do SQL Server Agent são somente leitura. O procedimento `sp_set_agent_properties` não tem suporte no SQL instância gerenciada. 
- Trabalhos
  - As etapas de trabalho T-SQL têm suporte.
  - Os trabalhos de replicação a seguir têm suporte:
    - Leitor do log de transações
    - Instantâneo
    - Distribuidor
  - As etapas de trabalho SSIS são compatíveis.
  - Outros tipos de etapas de trabalho não são compatíveis no momento:
    - A etapa de trabalho de replicação de mesclagem não é compatível. 
    - Não há suporte para leitor de fila. 
    - O shell de comando ainda não é compatível.
  - O SQL Instância Gerenciada não pode acessar recursos externos, por exemplo, compartilhamentos de rede via Robocopy. 
  - Não há suporte para SQL Server Analysis Services.
- Há suporte parcial para notificações.
- Notificações por email são compatíveis, embora seja necessário configurar um perfil de Database Mail. O SQL Server Agent pode usar apenas um perfil do Database Mail e ele deve ser chamado de `AzureManagedInstance_dbmail_profile`. 
  - Não há suporte para o Pager.
  - Não há suporte para o NetSend.
  - Ainda não há suporte para alertas.
  - Não há suporte para proxies.
- Não há suporte para o EventLog.
- O usuário deve ser mapeado diretamente para a entidade de segurança do servidor do Azure AD (logon) para criar, modificar ou executar trabalhos do SQL Agent. Os usuários que não são mapeados diretamente, por exemplo, os usuários que pertencem a um grupo do Azure AD que tem os direitos de criar, modificar ou executar trabalhos do SQL Agent, não poderão executar essas ações efetivamente. Isso ocorre devido à Instância Gerenciada representação e [às limitações de executar como](#logins-and-users).
- Não há suporte para o recurso de administração multisservidor para trabalhos mestre/de destino (MSX/TSX).

Para obter informações sobre o SQL Server Agent, consulte [SQL Server Agent](/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

Não há suporte para os seguintes tipos de tabela:

- [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server)
- [FILETABLE](/sql/relational-databases/blob/filetables-sql-server)
- [EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) (Polybase)
- [MEMORY_OPTIMIZED](/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables) (sem suporte apenas na camada Uso Geral)

Para obter informações sobre a criação e alteração de tabelas, consulte [CRIAR TABELA](/sql/t-sql/statements/create-table-transact-sql) e [ALTERAR TABELA](/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa / OPENROWSET

O SQL Instância Gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto os arquivos devem ser importados do armazenamento de BLOBs do Azure:

- `DATASOURCE` é necessário no comando `BULK INSERT` durante a importação de arquivos do armazenamento de blobs do Azure. Consulte [INSERÇÃO EM MASSA](/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE` é necessário na função `OPENROWSET` quando você ler um conteúdo de um arquivo do armazenamento de blobs do Azure. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- `OPENROWSET` pode ser usado para ler dados de instâncias do banco de dados SQL do Azure, do Azure SQL Instância Gerenciada ou do SQL Server. Não há suporte para outras fontes, como bancos de dados Oracle ou arquivos do Excel.

### <a name="clr"></a>CLR

Um SQL Instância Gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- Apenas `CREATE ASSEMBLY FROM BINARY` tem suporte. Consulte [CRIAR ASSEMBLY A PARTIR DE BINÁRIO](/sql/t-sql/statements/create-assembly-transact-sql). 
- Não há suporte para `CREATE ASSEMBLY FROM FILE`. Consulte [CRIAR ASSEMBLY A PARTIR DE ARQUIVO](/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Consulte [ALTERAR ASSEMBLY](/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="database-mail-db_mail"></a>Database Mail (db_mail)
 - `sp_send_dbmail` não pode enviar anexos usando o parâmetro @file_attachments. O sistema de arquivos local e os compartilhamentos externos ou o armazenamento de Blobs do Azure não são acessíveis a partir desse procedimento.
 - Consulte os problemas conhecidos relacionados ao parâmetro `@query` e à autenticação.
 
### <a name="dbcc"></a>DBCC

Não há suporte para instruções DBCC não documentadas habilitadas no SQL Server no SQL Instância Gerenciada.

- Há suporte apenas para um número limitado de sinalizadores de rastreamento globais. Não há suporte para `Trace flags` em nível de sessão. Consulte [Sinalizadores de rastreamento](/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- [DBCC TRACEOFF](/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql) e [DBCC TRACEON](/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql) funcionam com o número limitado de sinalizadores de rastreamento global.
- O [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql) com opções REPAIR_ALLOW_DATA_LOSS, REPAIR_FAST e REPAIR_REBUILD não pode ser usado porque o banco de dados não pode ser definido no modo `SINGLE_USER` - consulte [diferenças de ALTER DATABASE](#alter-database-statement). A possível corrupção do banco de dados é tratada pela equipe de suporte do Azure. Entre em contato com o suporte do Azure se houver qualquer indicação de corrupção do banco de dados.

### <a name="distributed-transactions"></a>Transações distribuídas

O suporte parcial para [transações distribuídas](../database/elastic-transactions-overview.md) está atualmente em visualização pública. Os cenários com suporte são:
* Transações em que os participantes são apenas instâncias gerenciadas do SQL do Azure que fazem parte do [grupo de confiança do servidor](./server-trust-group-overview.md).
* Transações iniciadas do .NET (classe TransactionScope) e Transact-SQL.

O Azure SQL Instância Gerenciada atualmente não dá suporte a outros cenários que são suportados regularmente pelo MSDTC local ou em máquinas virtuais do Azure.

### <a name="extended-events"></a>Eventos estendidos

Não há suporte para alguns destinos específicos do Windows para XEvents:

- Não há suporte para o destino `etw_classic_sync`. Armazenar arquivos `.xel` no armazenamento de blobs do Azure. Consulte [etw_classic_sync target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Não há suporte para o destino `event_file`. Armazenar arquivos `.xel` no armazenamento de blobs do Azure. Consulte [event_file target](/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

As bibliotecas externas do banco de dados R e Python têm suporte em visualização pública limitada. Consulte [serviços de Machine Learning no SQL instância gerenciada do Azure (versão prévia)](machine-learning-services-overview.md).

### <a name="filestream-and-filetable"></a>Fluxo de arquivos e FileTable

- Não há suporte para dados de fluxo de arquivos.
- O banco de dados não pode conter grupos de arquivos com dados `FILESTREAM`.
- Não há suporte para `FILETABLE`.
- As tabelas não podem ter tipos `FILESTREAM`.
- Não há suporte para as seguintes funções:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FILESTREAM](/sql/relational-databases/blob/filestream-sql-server) e [FileTables](/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

Não há suporte para [Pesquisa semântica](/sql/relational-databases/search/semantic-search-sql-server).

### <a name="linked-servers"></a>Servidores vinculados

Os servidores vinculados no SQL Instância Gerenciada dão suporte a um número limitado de destinos:

- Os destinos com suporte são SQL Instância Gerenciada, banco de dados SQL, pools dedicados e sem SQL [Server](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) do Azure Synapse e instâncias de SQL Server. 
- As transações graváveis distribuídas são possíveis somente entre instâncias gerenciadas. Para obter mais informações, consulte [transações distribuídas](../database/elastic-transactions-overview.md). No entanto, não há suporte para MS DTC.
- Destinos sem suporte: arquivos, Analysis Services e outros RDBMS. Tente usar a importação de CSV nativo do armazenamento de BLOBs do Azure usando `BULK INSERT` `OPENROWSET` o ou como uma alternativa para importação de arquivo ou carregue arquivos usando um [pool SQL sem servidor no Azure Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/).

Operações: 

- As transações de gravação [entre](../database/elastic-transactions-overview.md) instâncias são suportadas apenas para instância gerenciadas.
- `sp_dropserver` é compatível com o descarte um servidor vinculado. Consulte [sp_dropserver](/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A função `OPENROWSET` pode ser usada para executar consultas somente em instâncias SQL Server. Eles podem ser gerenciados localmente ou em máquinas virtuais. Consulte [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql).
- A função `OPENDATASOURCE` pode ser usada para executar consultas somente em instâncias SQL Server. Eles podem ser gerenciados localmente ou em máquinas virtuais. Somente os valores `SQLNCLI`, `SQLNCLI11` e `SQLOLEDB` têm suporte como provedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql).
- Os servidores vinculados não podem ser usados para ler arquivos (Excel, CSV) dos compartilhamentos de rede. Tente usar [BULK INSERT](/sql/t-sql/statements/bulk-insert-transact-sql#e-importing-data-from-a-csv-file), [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql#g-accessing-data-from-a-csv-file-with-a-format-file) que lê arquivos CSV do armazenamento de BLOBs do Azure ou um [servidor vinculado que faz referência a um pool SQL sem servidor no Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/). Acompanhar essas solicitações no [item de comentário sobre o SQL instância gerenciada](https://feedback.azure.com/forums/915676-sql-managed-instance/suggestions/35657887-linked-server-to-non-sql-sources)|

### <a name="polybase"></a>PolyBase

O único tipo disponível de fonte externa é RDBMS (em visualização pública) para o banco de dados SQL do Azure, instância gerenciada do SQL do Azure e pool Synapse do Azure. Você pode usar [uma tabela externa que faz referência a um pool SQL sem servidor no Synapse Analytics](https://devblogs.microsoft.com/azure-sql/read-azure-storage-files-using-synapse-sql-external-tables/) como uma solução alternativa para tabelas externas do polybase que o lê diretamente do armazenamento do Azure. Na instância gerenciada do SQL do Azure, você pode usar servidores vinculados em [um pool SQL sem servidor no Synapse Analytics](https://devblogs.microsoft.com/azure-sql/linked-server-to-synapse-sql-to-implement-polybase-like-scenarios-in-managed-instance/) ou SQL Server para ler dados do armazenamento do Azure.
Para obter informações sobre o Polybase, consulte [Polybase](/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

- Há suporte para os tipos de replicação de instantâneo e bidirecional. Não há suporte para replicação de mesclagem, replicação ponto a ponto e assinaturas atualizáveis.
- A [replicação transacional](replication-transactional-overview.md) está disponível para visualização pública no SQL instância gerenciada com algumas restrições:
    - Todos os tipos de participantes de replicação (Publicador, distribuidor, assinante de pull e assinante push) podem ser colocados no SQL Instância Gerenciada, mas o Publicador e o distribuidor devem estar tanto na nuvem quanto no local.
    - O SQL Instância Gerenciada pode se comunicar com as versões recentes do SQL Server. Para obter mais informações sobre [matriz de versões suportadas](replication-transactional-overview.md#supportability-matrix), consulte.
    - A replicação transacional tem alguns [requisitos de rede adicionais](replication-transactional-overview.md#requirements).

Para obter mais informações sobre como configurar a replicação transacional, consulte os seguintes tutoriais:
- [Replicação entre um Publicador de MI SQL e um assinante do SQL MI](replication-between-two-instances-configure-tutorial.md)
- [Replicação entre um Publicador de MI SQL, um distribuidor de MI SQL e um Assinante SQL Server](replication-two-instances-and-sql-server-configure-tutorial.md)

### <a name="restore-statement"></a>Instrução RESTAURAR 

- Sintaxe suportada:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe não suportada:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Origem: 
  - `FROM URL` (Armazenamento de Blobs do Azure) é a única opção compatível.
  - Não há suporte para `FROM DISK`/`TAPE`/dispositivo de backup.
  - Conjuntos de backup não são compatíveis.
- As opções `WITH` não são compatíveis. As tentativas de restauração, incluindo `WITH` como `DIFFERENTIAL` , `STATS` , `REPLACE` , etc., falharão.
- `ASYNC RESTORE`: A restauração continuará mesmo se houver quedas de conexão do cliente. Se a conexão for interrompida, você pode verificar a exibição `sys.dm_operation_status` do status de uma operação de restauração (bem como para CRIAR e DESCARTAR o banco de dados). Consulte [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

As seguintes opções de banco de dados são definidas/substituídas e não podem ser alteradas posteriormente: 

- `NEW_BROKER` se o agente não estiver habilitado no arquivo .bak. 
- `ENABLE_BROKER` se o agente não estiver habilitado no arquivo .bak. 
- `AUTO_CLOSE=OFF` se um banco de dados no arquivo .bak tiver `AUTO_CLOSE=ON`. 
- `RECOVERY FULL`se um banco de dados no arquivo .bak tiver `SIMPLE` ou modo de recuperação `BULK_LOGGED`.
- O grupo de arquivos otimizados por memória é adicionado e chamado de XTP caso não estivesse no arquivo .bak de origem. 
- Qualquer grupo de arquivos otimizado para memória existente é renomeado para XTP. 
- As opções `SINGLE_USER` e `RESTRICTED_USER` são convertidas em `MULTI_USER`.

Limitações: 

- Os backups dos bancos de dados corrompidos podem ser restaurados dependendo do tipo de corrupção, mas os backups automatizados não serão feitos até que a corrupção seja corrigida. Certifique-se de executar `DBCC CHECKDB` o SQL de origem instância gerenciada e use o backup `WITH CHECKSUM` para evitar esse problema.
- A restauração do `.BAK` arquivo de um banco de dados que contém qualquer limitação descrita neste documento (por exemplo, `FILESTREAM` ou `FILETABLE` objetos) não pode ser restaurada no SQL instância gerenciada.
- Os arquivos `.BAK` contendo vários conjuntos de backup não podem ser restaurados. 
- Os arquivos `.BAK` contendo vários arquivos de log não podem ser restaurados.
- Os backups que contêm bancos de dados maiores que 8 TB, objetos OLTP na memória ativas ou o número de arquivos que excedem 280 arquivos por instância não podem ser restaurados em uma instância de Uso Geral. 
- Os backups que contêm bancos de dados maiores que 4 TB ou objetos OLTP na memória com o tamanho total maior do que o tamanho descrito em [limites de recursos](resource-limits.md) não podem ser restaurados na instância Comercialmente Crítico.
Para obter informações sobre instruções de restauração, consulte [instruções RESTAURAR](/sql/t-sql/statements/restore-statements-transact-sql).

 > [!IMPORTANT]
 > As mesmas limitações se aplicam à operação de restauração pontual interna. Por exemplo, banco de dados de Uso Geral maior que 4 TB não pode ser restaurado na instância Comercialmente Crítico. O banco de dados comercialmente crítico com arquivos OLTP na memória ou mais de 280 arquivos não podem ser restaurados na instância Uso Geral.

### <a name="service-broker"></a>Service Broker

A troca de mensagens do Service Broker entre instâncias tem suporte apenas entre Instâncias Gerenciadas de SQL do Azure:

- `CREATE ROUTE`: Você não pode usar `CREATE ROUTE` com `ADDRESS` outro `LOCAL` nome DNS ou de outro instância gerenciada do SQL.
- `ALTER ROUTE`: Você não pode usar `ALTER ROUTE` com `ADDRESS` outro `LOCAL` nome DNS ou de outro instância gerenciada do SQL.

Há suporte para a segurança do transporte, mas não para a segurança da caixa de diálogo:
- Não há suporte para `CREATE REMOTE SERVICE BINDING`.

O Service Broker está habilitado por padrão e não pode ser desabilitado. Não há suporte para as seguintes opções ALTER bancos:
- `ENABLE_BROKER`
- `DISABLE_BROKER`

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos, funções, gatilhos armazenados

- `NATIVE_COMPILATION` não tem suporte na camada de Uso Geral.
- Não há suporte para as seguintes opções [sp_configure](/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql): 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote access`
  - `remote data archive`
  - `remote proc trans`
  - `scan for startup procs`
- Não há suporte para `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- Não há suporte para `xp_cmdshell`. Consulte [xp_cmdshell](/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures` Não tem suporte, e isso inclui `sp_addextendedproc` e `sp_dropextendedproc` . Essa funcionalidade não terá suporte porque está em um caminho de substituição para SQL Server. Para obter mais detalhes, consulte [procedimentos armazenados estendidos](/sql/relational-databases/extended-stored-procedures-programming/database-engine-extended-stored-procedures-programming).
- Não há suporte para `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db`. Consulte [sp_attach_db](/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

### <a name="system-functions-and-variables"></a>Função do sistema e variáveis

As seguintes variáveis, funções e exibições retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')` retorna o valor 8. Essa propriedade identifica exclusivamente um Instância Gerenciada do SQL. Consulte [SERVERPROPERTY](/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')` Retorna NULL porque o conceito de instância existente para SQL Server não se aplica ao SQL Instância Gerenciada. Consulte [SERVERPROPERTY('InstanceName')](/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME` returna o nome completo 'conectável' do DNS, por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consulte [@@SERVERNAME](/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS` retorna o nome completo 'conectável' do DNS, como `myinstance.domain.database.windows.net`, para propriedades 'name' e 'data_source'. Consulte [SYS.SERVERS](/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME` Retorna NULL porque o conceito de serviço existente para SQL Server não se aplica ao SQL Instância Gerenciada. Consulte [@@SERVICENAME](/sql/t-sql/functions/servicename-transact-sql).
- `SUSER_ID` é compatível. Retornará NULL se o logon do Azure AD não estiver em sys.syslogins. Consulte [SUSER_ID](/sql/t-sql/functions/suser-id-transact-sql). 
- Não há suporte para `SUSER_SID`. Os dados errados são retornados, o que é um problema temporário conhecido. Consulte [SUSER_SID](/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="environment-constraints"></a><a name="Environment"></a>Restrições de ambiente

### <a name="subnet"></a>Sub-rede
-  Não é possível inserir outros recursos (por exemplo, máquinas virtuais) na sub-rede em que você implantou o SQL Instância Gerenciada. Implante esses recursos usando uma sub-rede diferente.
- A sub-rede deve ter um número suficiente de [endereços IP](connectivity-architecture-overview.md#network-requirements)disponíveis. O mínimo é ter pelo menos 32 endereços IP na sub-rede.
- O número de vCores e tipos de instâncias que podem ser implantadas em uma região têm algumas [restrições e limites](resource-limits.md#regional-resource-limitations).
- Há uma [configuração de rede](connectivity-architecture-overview.md#network-requirements) que deve ser aplicada na sub-rede.

### <a name="vnet"></a>VNET
- A VNet pode ser implantada usando modelo de recurso - modelo clássico para VNet sem suporte.
- Após a criação de um Instância Gerenciada SQL, não há suporte para a movimentação do SQL Instância Gerenciada ou VNet para outro grupo de recursos ou assinatura.
- Para instâncias gerenciadas do SQL hospedadas em clusters virtuais que são criados antes de 9/22/2020 o [emparelhamento global](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) não tem suporte. Você pode se conectar a esses recursos via ExpressRoute ou VNet a VNet por meio de gateways de VNet.

### <a name="failover-groups"></a>Grupos de failover
Os bancos de dados do sistema não são replicados para a instância secundária em um grupo de failover. Portanto, os cenários que dependem de objetos dos bancos de dados do sistema serão impossíveis na instância secundária, a menos que os objetos sejam criados manualmente no secundário.

### <a name="tempdb"></a>TEMPDB
- O tamanho máximo do arquivo de `tempdb` não pode ser maior que 24 GB por núcleo em uma camada de Uso Geral. O `tempdb` tamanho máximo em uma camada de comercialmente crítico é limitado pelo tamanho do armazenamento de instância gerenciada do SQL. O tamanho do arquivo de log `Tempdb` é limitado a 120 GB na camada uso geral. Algumas consultas podem retornar um erro se precisarem de mais de 24 GB por núcleo em `tempdb` ou se produzirem mais de 120 GB de dados de log.
- `Tempdb` sempre é dividido em 12 arquivos de dados: 1 primário, também chamado de mestre, arquivo de dados e 11 arquivos de dados não primários. A estrutura do arquivo não pode ser alterada e novos arquivos não podem ser adicionados ao `tempdb` . 
- Não há suporte para os [ `tempdb` metadados com otimização de memória](/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15&preserve-view=true#memory-optimized-tempdb-metadata), um novo SQL Server o recurso de banco de dados em memória do 2019.
- Os objetos criados no banco de dados modelo não podem ser criados automaticamente no `tempdb` após uma reinicialização ou um failover porque `tempdb` o não obtém sua lista inicial de objetos do banco de dados modelo. Você deve criar objetos `tempdb` manualmente após cada reinicialização ou um failover.

### <a name="msdb"></a>MSDB

Os seguintes esquemas MSDB no SQL Instância Gerenciada devem ser de propriedade de suas respectivas funções predefinidas:

- Funções gerais
  - TargetServersRole
- [Funções de banco de dados fixas](/sql/ssms/agent/sql-server-agent-fixed-database-roles?view=sql-server-ver15&preserve-view=true)
  - SQLAgentUserRole
  - SQLAgentReaderRole
  - SQLAgentOperatorRole
- [Funções de DatabaseMail](/sql/relational-databases/database-mail/database-mail-configuration-objects?view=sql-server-ver15&preserve-view=true#DBProfile):
  - DatabaseMailUserRole
- [Funções do Integration Services](/sql/integration-services/security/integration-services-roles-ssis-service?view=sql-server-ver15&preserve-view=true):
  - db_ssisadmin
  - db_ssisltduser
  - db_ssisoperator
  
> [!IMPORTANT]
> Alterar os nomes de função predefinidos, nomes de esquema e proprietários de esquema por clientes afetará a operação normal do serviço. Todas as alterações feitas a elas serão revertidas para os valores predefinidos assim que forem detectadas ou na próxima atualização de serviço, na versão mais recente, para garantir a operação normal de serviço.

### <a name="error-logs"></a>Logs de erros

O SQL Instância Gerenciada coloca informações detalhadas nos logs de erros. Há muitos eventos internos do sistema que são registrados no log de erros. Usar um procedimento personalizado para a leitura de logs de erros que filtra algumas entradas não relevantes. Para obter mais informações, consulte [sql instância gerenciada – sp_readmierrorlog](/archive/blogs/sqlcat/azure-sql-db-managed-instance-sp_readmierrorlog) ou [extensão do SQL instância gerenciada (versão prévia)](/sql/azure-data-studio/azure-sql-managed-instance-extension#logs) para Azure Data Studio.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o SQL Instância Gerenciada, consulte [o que é o sql instância gerenciada?](sql-managed-instance-paas-overview.md)
- Para obter uma lista de recursos e comparação, confira comparação de recursos [do Azure SQL instância gerenciada](../database/features-comparison.md).
- Para ver as atualizações de versão e o estado de problemas conhecidos, consulte [notas de versão do SQL instância gerenciada](../database/doc-changes-updates-release-notes.md)
- Para obter um guia de início rápido que mostra como criar um novo Instância Gerenciada de SQL, consulte [criar um sql instância gerenciada](instance-create-quickstart.md).
