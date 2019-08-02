---
title: Instância Gerenciada do Banco de Dados SQL do Azure diferenças de T-SQL | Microsoft Docs
description: Este artigo aborda as diferenças do T-SQL entre uma instância gerenciada no Banco de Dados SQL do Azure e no SQL Server
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, bonova
manager: craigg
ms.date: 07/07/2019
ms.custom: seoapril2019
ms.openlocfilehash: c9b481e63ecf7a92af679c0f32d4b3ab71486021
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360799"
---
# <a name="azure-sql-database-managed-instance-t-sql-differences-from-sql-server"></a>Diferenças de T-SQL da Instância Gerenciada do Banco de Dados SQL do Azure em relação ao SQL Server

Este artigo resume e explica as diferenças na sintaxe e no comportamento entre Instância Gerenciada do Banco de Dados SQL do Azure e SQL Server local Mecanismo de Banco de Dados. Os seguintes assuntos são discutidos:<a name="Differences"></a>

- A [disponibilidade](#availability) inclui as diferenças em [Always on](#always-on-availability) e [backups](#backup).
- A [segurança](#security) inclui as diferenças de [auditoria](#auditing), [certificados](#certificates), [credenciais](#credential), [provedores criptográficos](#cryptographic-providers), logons [e usuários](#logins-and-users)e a [chave de serviço e a chave mestra de serviço](#service-key-and-service-master-key).
- A [configuração](#configuration) inclui as diferenças na [extensão do pool](#buffer-pool-extension)de buffers, [agrupamento](#collation), [níveis de compatibilidade](#compatibility-levels), espelhamento de [banco de dados](#database-mirroring), opções de [banco de dados](#database-options), [SQL Server Agent](#sql-server-agent)e [Opções de tabela](#tables).
- As [funcionalidades](#functionalities) incluem [BULK INSERT/OPENROWSET](#bulk-insert--openrowset), [CLR](#clr), [DBCC](#dbcc), [transações distribuídas](#distributed-transactions), [eventos estendidos](#extended-events), [bibliotecas externas](#external-libraries), [FileStream e filetable](#filestream-and-filetable), [texto completo Pesquisa semântica](#full-text-semantic-search), [servidores vinculados](#linked-servers), [polybase](#polybase), [replicação](#replication), [restauração](#restore-statement), [Service Broker](#service-broker), [procedimentos armazenados, funções e gatilhos](#stored-procedures-functions-and-triggers).
- [Configurações de ambiente](#Environment) , como VNets e configurações de sub-rede.
- [Recursos que têm comportamento diferente em instâncias gerenciadas](#Changes).
- [Limitações temporárias e problemas conhecidos](#Issues).

A opção de implantação de Instância Gerenciada fornece alta compatibilidade com o Mecanismo de Banco de Dados do SQL Server local. A maioria dos recursos do mecanismo de banco de dados do SQL Server é compatível com uma instância gerenciada.

![Migração](./media/sql-database-managed-instance/migration.png)

## <a name="availability"></a>Disponibilidade

### <a name="always-on-availability"></a>Always On

A [alta disponibilidade](sql-database-high-availability.md) é incorporada ao instância gerenciada e não pode ser controlada por usuários. As instruções a seguir não têm suporte:

- [CRIAR ENDPOINT … PARA DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql)
- [CRIAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/create-availability-group-transact-sql)
- [ALTERAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/alter-availability-group-transact-sql)
- [DESCARTAR GRUPO DE DISPONIBILIDADE](https://docs.microsoft.com/sql/t-sql/statements/drop-availability-group-transact-sql)
- A cláusula [set HADR](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-hadr) da instrução [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql)

### <a name="backup"></a>Backup

Instâncias gerenciadas têm backups automáticos, para que os `COPY_ONLY` usuários possam criar backups de banco de dados completos. Não há suporte para backups diferenciais, de log e de instantâneo de arquivo.

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instância somente para uma conta de armazenamento de BLOBs do Azure:
  - Apenas `BACKUP TO URL` tem suporte.
  - `FILE`, `TAPE`e os dispositivos de backup não têm suporte.
- Há suporte para a `WITH` maioria das opções gerais.
  - `COPY_ONLY`é obrigatório.
  - Não há suporte para `FILE_SNAPSHOT`.
  - Opções de fita `REWIND`: `NOREWIND`, `UNLOAD`, e `NOUNLOAD` não têm suporte.
  - Opções específicas de log: `NORECOVERY`, `STANDBY`e `NO_TRUNCATE` não têm suporte.

Limitações: 

- Com uma instância gerenciada, você pode fazer backup de um banco de dados de instância em um backup com até 32 faixas, o que é suficiente para bancos de dados de até 4 TB se a compactação de backup for usada.
- O tamanho máximo de distribuição de backup usando o `BACKUP` comando em uma instância gerenciada é 195 GB, que é o tamanho máximo do blob. Aumente o número de faixas no comando de backup para reduzir o tamanho de faixas individuais e permanecer dentro desse limite.

    > [!TIP]
    > Para solucionar essa limitação, ao fazer backup de um banco de dados de qualquer SQL Server em um ambiente local ou em uma máquina virtual, você pode:
    >
    > - Faça backup em `DISK` em vez de fazer backup para `URL`.
    > - Carregue os arquivos de backup no armazenamento de BLOBs.
    > - Restaure na instância gerenciada.
    >
    > O `Restore` comando em uma instância gerenciada dá suporte a tamanhos de blob maiores nos arquivos de backup porque um tipo de blob diferente é usado para o armazenamento dos arquivos de backup carregados.

Para obter informações sobre backups usando o T-SQL, consulte [BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql).

## <a name="security"></a>Segurança

### <a name="auditing"></a>Auditoria

As principais diferenças entre a auditoria em bancos de dados no Banco de Dados SQL do Azure e em bancos de dados no SQL Server são:

- Com a opção de implantação Instância Gerenciada no banco de dados SQL do Azure, a auditoria funciona no nível do servidor. Os `.xel` arquivos de log são armazenados no armazenamento de BLOBs do Azure.
- Com as opções de implantação de banco de dados individual e pool elástico no Banco de Dados SQL do Azure, a auditoria funciona no nível do banco de dados.
- Em SQL Server máquinas virtuais ou locais, a auditoria funciona no nível do servidor. Os eventos são armazenados no sistema de arquivos ou nos logs de eventos do Windows.
 
A auditoria XEvent na Instância Gerenciada oferece suporte a destinos de armazenamento de blobs do Azure. Não há suporte para logs de arquivo e do Windows.

As principais diferenças na sintaxe `CREATE AUDIT` para a auditoria do armazenamento de Blobs do Azure são:

- É fornecida uma `TO URL` nova sintaxe que você pode usar para especificar a URL do contêiner de armazenamento de BLOBs do `.xel` Azure em que os arquivos são colocados.
- A sintaxe `TO FILE` não tem suporte porque uma instância gerenciada não pode acessar compartilhamentos de arquivos do Windows.

Para obter mais informações, consulte: 

- [CRIAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/create-server-audit-transact-sql) 
- [ALTERAR AUDITORIA DE SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-audit-transact-sql)
- [Auditoria](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)

### <a name="certificates"></a>Certificados

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- O `CREATE FROM` arquivonãotem`BACKUP TO` suporte para certificados. /
- Não `CREATE` há suporte `FILE` para o / `BACKUP` certificado de ./ `ASSEMBLY` Arquivos de chave privada não podem ser usados. 

Consulte [CRIAR CERTIFICADO](https://docs.microsoft.com/sql/t-sql/statements/create-certificate-transact-sql) e [CERTIFICADO DE BACKUP](https://docs.microsoft.com/sql/t-sql/statements/backup-certificate-transact-sql). 
 
**Solução alternativa**: Script para o certificado ou a chave privada, armazene como arquivo. SQL e crie a partir do binário:

```sql
CREATE CERTIFICATE  
   FROM BINARY = asn_encoded_certificate
WITH PRIVATE KEY (<private_key_options>)
```

### <a name="credential"></a>Credencial

Somente o Azure Key Vault e identidades `SHARED ACCESS SIGNATURE` têm suporte. Não há suporte para usuários do Windows.

Consulte [CRIAR CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) e [ALTERAR CREDENCIAL](https://docs.microsoft.com/sql/t-sql/statements/alter-credential-transact-sql).

### <a name="cryptographic-providers"></a>Provedores criptográficos

Uma instância gerenciada não pode acessar arquivos, portanto, os provedores criptográficos não podem ser criados:

- Não há suporte para `CREATE CRYPTOGRAPHIC PROVIDER`. Consulte [CRIAR PROVEDOR CRIPTOGRÁFICO](https://docs.microsoft.com/sql/t-sql/statements/create-cryptographic-provider-transact-sql).
- Não há suporte para `ALTER CRYPTOGRAPHIC PROVIDER`. Consulte [ALTERAR PROVEDOR CRIPTOGRÁFICO](https://docs.microsoft.com/sql/t-sql/statements/alter-cryptographic-provider-transact-sql).

### <a name="logins-and-users"></a>Logons e usuários

- Os logons do SQL criados `FROM CERTIFICATE`com `FROM ASYMMETRIC KEY`o, `FROM SID` o e têm suporte. Consulte [CREATE LOGIN](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql).
- As entidades de segurança (logons) do servidor do Azure Active Directory (Azure AD) criadas com a sintaxe [Create login](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) ou [Create User from login [Azure ad login]](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current) têm suporte (visualização pública). Esses logons são criados no nível do servidor.

    A Instância Gerenciada oferece suporte a entidades de segurança do banco de dados do Azure AD com a sintaxe `CREATE USER [AADUser/AAD group] FROM EXTERNAL PROVIDER`. Esse recurso também é conhecido como usuários de banco de dados independente do Azure AD.

- Não há suporte para logons `CREATE LOGIN ... FROM WINDOWS` do Windows criados com a sintaxe. Use logons e usuário do Microsoft Azure Active Directory.
- O usuário do Azure AD que criou a instância tem [privilégios de administrador irrestrito](sql-database-manage-logins.md#unrestricted-administrative-accounts).
- Usuários de nível de banco de dados não administrador do Azure ad podem ser criados `CREATE USER ... FROM EXTERNAL PROVIDER` usando a sintaxe. Consulte [CRIAR USUÁRIO ... FROM EXTERNAL PROVIDER](sql-database-manage-logins.md#non-administrator-users).
- As entidades de segurança do servidor do Azure AD (logons) dão suporte a recursos SQL somente em uma instância gerenciada. Recursos que exigem interação entre instâncias, independentemente de estarem dentro do mesmo locatário do Azure AD ou locatários diferentes, não têm suporte para usuários do Azure AD. Exemplos de recursos desse tipo são:

  - Replicação transacional do SQL.
  - Servidor de link.

- Não há suporte para logons do Azure AD mapeados para um grupo do Azure AD como proprietário do banco de dados.
- Há suporte para a representação de entidades de segurança no nível de servidor do Azure AD usando outras entidades do Azure AD, como a cláusula [Execute as](/sql/t-sql/statements/execute-as-transact-sql) . AS limitações de executar como são:

  - Não há suporte para EXECUTE AS USER para usuários do Azure AD quando o nome difere do nome de logon. Um exemplo é quando o usuário é criado por meio da sintaxe Create User [myAadUser] do loginjohn@contoso.com[] e a representação é tentada por meio de exec como user = _myAadUser_. Quando você cria um **usuário** de uma entidade de segurança de servidor do Azure AD (logon), especifique o user_name como o mesmo Login_name do **logon**.
  - Somente as entidades de nível de SQL Server (logons) que fazem parte da `sysadmin` função podem executar as seguintes operações que visam entidades do Azure AD:

    - EXECUTE AS USER
    - EXECUTE AS LOGIN

- Limitações de visualização pública para entidades de segurança do Azure AD Server (logons):

  - Active Directory limitações de administrador para Instância Gerenciada:

    - O administrador do Azure AD usado para configurar a instância gerenciada não pode ser usado para criar uma entidade de segurança de servidor do Azure AD (logon) na instância gerenciada. Você deve criar a primeira entidade de segurança de servidor do Azure AD (logon) usando uma conta de `sysadmin` SQL Server que seja uma função. Essa limitação temporária será removida depois que as entidades de segurança do Azure AD Server (logons) ficarem disponíveis para o público geral. Se você tentar usar uma conta de administrador do Azure AD para criar o logon, você verá o seguinte erro:`Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`
      - Atualmente, o primeiro logon do Azure ad criado no banco de dados mestre deve ser criado pela conta de SQL Server padrão (não Azure AD) que é `sysadmin` uma função usando [criar logon](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) do provedor externo. Após a disponibilidade geral, essa limitação será removida. Em seguida, você pode criar um logon inicial do Azure AD usando o administrador do Active Directory para Instância Gerenciada.
    - DacFx (exportação/importação) usado com SQL Server Management Studio ou SqlPackage não tem suporte para logons do Azure AD. Essa limitação será removida depois que as entidades de segurança do Azure AD Server (logons) ficarem disponíveis para o público geral.
    - Usando entidades de segurança de servidor do Azure AD (logons) com SQL Server Management Studio:

      - Não há suporte para o script de logons do Azure AD que usam qualquer logon autenticado.
      - O IntelliSense não reconhece a instrução CREATE LOGIN do provedor externo e mostra um sublinhado vermelho.

- Somente o logon da entidade de segurança no nível do servidor, que é criado pelo processo de provisionamento do instância gerenciada, os membros das funções de `securityadmin` servidor `sysadmin`, como ou ou outros logons com a permissão ALTER ANY login no nível do servidor, podem criar o Azure AD entidades de segurança do servidor (logons) no banco de dados mestre para Instância Gerenciada.
- Se o logon for uma entidade de segurança SQL, somente os logons que fizerem parte da `sysadmin` função poderão usar o comando Create para criar logons para uma conta do Azure AD.
- O logon do Azure AD deve ser um membro de um Azure AD dentro do mesmo diretório usado para Instância Gerenciada do Banco de Dados SQL do Azure.
- As entidades de segurança do servidor do Azure AD (logons) são visíveis no Pesquisador de objetos, começando com SQL Server Management Studio 18,0 Preview 5.
- É permitida a sobreposição de entidades de segurança do servidor (logons) do Azure AD com uma conta de administrador do Azure AD. As entidades de segurança do servidor do Azure AD (logons) têm precedência sobre o administrador do Azure AD quando você resolve a entidade de segurança e aplica permissões à instância gerenciada.
- Durante a autenticação, a sequência a seguir é aplicada para resolver a entidade de autenticação:

    1. Se a conta do Azure AD existir como mapeada diretamente para a entidade de segurança do servidor do Azure AD (logon), que está presente em sys. server_principals como tipo "E" conceder acesso e aplicar permissões da entidade de segurança do servidor do Azure AD (logon).
    2. Se a conta do Azure AD for um membro de um grupo do Azure AD que é mapeado para a entidade de segurança de servidor do Azure AD (logon), que está presente em sys. server_principals como tipo "X", conceda permissões de acesso e aplique ao logon do grupo do Azure AD.
    3. Se a conta do Azure AD for um administrador especial configurado pelo portal do Azure AD para Instância Gerenciada, que não existe em exibições do sistema Instância Gerenciada, aplique permissões fixas especiais do administrador do Azure AD para Instância Gerenciada (modo herdado).
    4. Se a conta do Azure AD existir como mapeada diretamente para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo "E" conceder acesso e aplicar permissões do usuário de banco de dados do Azure AD.
    5. Se a conta do Azure AD for um membro de um grupo do Azure AD que é mapeado para um usuário do Azure AD em um banco de dados, que está presente em sys. database_principals como tipo "X", conceda permissões de acesso e aplique ao logon do grupo do Azure AD.
    6. Se houver um logon do Azure AD mapeado para uma conta de usuário do Azure AD ou uma conta de grupo do Azure AD, que seja resolvida para o usuário que está Autenticando, todas as permissões desse logon do Azure AD serão aplicadas.

### <a name="service-key-and-service-master-key"></a>Chave de serviço e chave mestra de serviço

- O [backup da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/backup-master-key-transact-sql) não tem suporte (gerenciado pelo serviço do banco de dados SQL).
- Não há suporte para a [restauração da chave mestra](https://docs.microsoft.com/sql/t-sql/statements/restore-master-key-transact-sql) (gerenciada pelo serviço do banco de dados SQL).
- O [backup da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/backup-service-master-key-transact-sql) não tem suporte (gerenciado pelo serviço de banco de dados SQL).
- Não há suporte para a [restauração da chave mestra de serviço](https://docs.microsoft.com/sql/t-sql/statements/restore-service-master-key-transact-sql) (gerenciada pelo serviço do banco de dados SQL).

## <a name="configuration"></a>Configuração

### <a name="buffer-pool-extension"></a>Extensão do pool de buffers

- Não há suporte para a [extensão do pool](https://docs.microsoft.com/sql/database-engine/configure-windows/buffer-pool-extension) de buffers.
- Não há suporte para `ALTER SERVER CONFIGURATION SET BUFFER POOL EXTENSION`. Consulte [ALTERAR CONFIGURAÇÃO DO SERVIDOR](https://docs.microsoft.com/sql/t-sql/statements/alter-server-configuration-transact-sql).

### <a name="collation"></a>Ordenação

O agrupamento de instância padrão é `SQL_Latin1_General_CP1_CI_AS` e pode ser especificado como um parâmetro de criação. Consulte [Ordenações](https://docs.microsoft.com/sql/t-sql/statements/collations).

### <a name="compatibility-levels"></a>Níveis de compatibilidade

- Os níveis de compatibilidade com suporte são 100, 110, 120, 130 e 140.
- Não há suporte para níveis de compatibilidade abaixo de 100.
- O nível de compatibilidade padrão para novos bancos de dados é 140. Para bancos de dados restaurados, o nível de compatibilidade permanece inalterado se foi 100 e superior.

Consulte [ALTERAR Nível de Compatibilidade do BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level).

### <a name="database-mirroring"></a>Espelhamento de banco de dados

Não há suporte para espelhamento de banco de dados.

- As opções `ALTER DATABASE SET PARTNER` e `SET WITNESS` não são suportadas.
- Não há suporte para `CREATE ENDPOINT … FOR DATABASE_MIRRORING`.

Para obter mais informações, consulte [ALTER DATABASE SET PARTNER e SET WITNESS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-database-mirroring) e [CREATE ENDPOINT... FOR DATABASE_MIRRORING](https://docs.microsoft.com/sql/t-sql/statements/create-endpoint-transact-sql).

### <a name="database-options"></a>Opções de banco de dados

- Não há suporte para vários arquivos de log.
- Não há suporte para objetos na memória na camada de serviço de Uso Geral. 
- Há um limite de 280 arquivos por Uso Geral instância, o que implica um máximo de 280 arquivos por banco de dados. Os arquivos de dados e de log na camada de Uso Geral são contados em direção a esse limite. [A camada de comercialmente crítico dá suporte a 32.767 arquivos por banco de dados](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).
- O banco de dados não pode conter grupos de fileque contenham dado FileStream. A restauração falhará se. `FILESTREAM` bak contiver dados. 
- Cada arquivo é colocado no Armazenamento de Blobs do Azure. A E/S e a taxa de transferência por arquivo dependem do tamanho de cada arquivo individual.

#### <a name="create-database-statement"></a>Instrução CRIAR BANCO DE DADOS

As seguintes limitações se aplicam a `CREATE DATABASE`:

- Arquivos e grupos de arquivos não podem ser definidos. 
- Não `CONTAINMENT` há suporte para a opção. 
- `WITH`Não há suporte para as opções. 
   > [!TIP]
   > Como alternativa, use `ALTER DATABASE` depois `CREATE DATABASE` para definir opções de banco de dados para adicionar arquivos ou para definir a contenção. 

- Não `FOR ATTACH` há suporte para a opção.
- Não `AS SNAPSHOT OF` há suporte para a opção.

Para saber mais, confira [CRIAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/create-database-sql-server-transact-sql).

#### <a name="alter-database-statement"></a>Instrução ALTERAR BANCO DE DADOS

Algumas propriedades de arquivo não podem ser definidas ou alteradas:

- Um caminho de arquivo não pode ser especificado `ALTER DATABASE ADD FILE (FILENAME='path')` na instrução t-SQL. Remova `FILENAME` do script porque uma instância gerenciada coloca os arquivos automaticamente. 
- Um nome de arquivo não pode ser alterado usando `ALTER DATABASE` a instrução.

As opções a seguir são definidas por padrão e não podem ser alteradas:

- `MULTI_USER`
- `ENABLE_BROKER ON`
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

Para saber mais, confira [ALTERAR BANCO DE DADOS](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-file-and-filegroup-options).

### <a name="sql-server-agent"></a>SQL Server Agent

- Não há suporte para habilitar e desabilitar SQL Server Agent atualmente na instância gerenciada. O SQL Agent sempre está em execução.
- SQL Server Agent configurações são somente leitura. O procedimento `sp_set_agent_properties` não tem suporte no instância gerenciada. 
- Tarefas (Jobs)
  - As etapas de trabalho T-SQL têm suporte.
  - Os trabalhos de replicação a seguir têm suporte:
    - Leitor do log de transações
    - Instantâneo
    - Distribuidor
  - Há suporte para as etapas de trabalho do SSIS.
  - Atualmente, não há suporte para outros tipos de etapas de trabalho:
    - Não há suporte para a etapa de trabalho de replicação de mesclagem. 
    - Não há suporte para leitor de fila. 
    - Ainda não há suporte para o Shell de comando.
  - As instâncias gerenciadas não podem acessar recursos externos, por exemplo, compartilhamentos de rede via Robocopy. 
  - Não há suporte para SQL Server Analysis Services.
- Há suporte parcial para notificações.
- Há suporte para notificação por email, embora exija que você configure um perfil de Database Mail. SQL Server Agent pode usar apenas um perfil de Database Mail e deve ser chamado `AzureManagedInstance_dbmail_profile`. 
  - Não há suporte para o Pager.
  - Não há suporte para o NetSend.
  - Ainda não há suporte para alertas.
  - Não há suporte para proxies.
- O log de eventos não tem suporte.

Os seguintes recursos do SQL Agent atualmente não têm suporte:

- Proxies
- Agendando trabalhos em uma CPU ociosa
- Habilitando ou desabilitando um agente
- Alertas

Para obter informações sobre o SQL Server Agent, consulte [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent).

### <a name="tables"></a>Tabelas

As tabelas a seguir não têm suporte:

- `FILESTREAM`
- `FILETABLE`
- `EXTERNAL TABLE`
- `MEMORY_OPTIMIZED` 

Para obter informações sobre como criar e alterar tabelas, consulte [CREATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) e [ALTER TABLE](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql).

## <a name="functionalities"></a>Funcionalidades

### <a name="bulk-insert--openrowset"></a>Inserção em massa / openrowset

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, os arquivos devem ser importados do armazenamento de BLOBs do Azure:

- `DATASOURCE`é necessário no `BULK INSERT` comando enquanto você importa arquivos do armazenamento de BLOBs do Azure. Consulte [INSERÇÃO EM MASSA](https://docs.microsoft.com/sql/t-sql/statements/bulk-insert-transact-sql).
- `DATASOURCE`é necessário na `OPENROWSET` função quando você lê o conteúdo de um arquivo do armazenamento de BLOBs do Azure. Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).

### <a name="clr"></a>CLR

Uma instância gerenciada não pode acessar compartilhamentos de arquivos e pastas do Windows, portanto, as seguintes restrições se aplicam:

- Apenas `CREATE ASSEMBLY FROM BINARY` tem suporte. Consulte [CRIAR ASSEMBLY A PARTIR DE BINÁRIO](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql). 
- Não há suporte para `CREATE ASSEMBLY FROM FILE`. Consulte [CRIAR ASSEMBLY A PARTIR DE ARQUIVO](https://docs.microsoft.com/sql/t-sql/statements/create-assembly-transact-sql).
- `ALTER ASSEMBLY` não pode referenciar arquivos. Consulte [ALTERAR ASSEMBLY](https://docs.microsoft.com/sql/t-sql/statements/alter-assembly-transact-sql).

### <a name="dbcc"></a>DBCC

As instruções DBCC não documentadas que estão habilitadas no SQL Server não têm suporte em instâncias gerenciadas.

- Não há suporte para `Trace flags`. Consulte [sinalizadores de rastreamento](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql).
- Não há suporte para `DBCC TRACEOFF`. Consulte [DBCC TRACEOFF](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceoff-transact-sql).
- Não há suporte para `DBCC TRACEON`. Consulte [DBCC TRACEON](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql).

### <a name="distributed-transactions"></a>Transações distribuídas

Atualmente, não há suporte para transações de MSDTC e [elástico](sql-database-elastic-transactions-overview.md) em instâncias gerenciadas.

### <a name="extended-events"></a>Eventos Estendidos

Não há suporte para alguns destinos específicos do Windows para eventos estendidos (XEvents):

- Não `etw_classic_sync` há suporte para o destino. Armazene `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [etw_classic_sync target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#etw_classic_sync_target-target).
- Não `event_file` há suporte para o destino. Armazene `.xel` arquivos no armazenamento de BLOBs do Azure. Consulte [event_file target](https://docs.microsoft.com/sql/relational-databases/extended-events/targets-for-extended-events-in-sql-server#event_file-target).

### <a name="external-libraries"></a>Bibliotecas externas

R e Python no banco de dados, ainda não há suporte para bibliotecas externas. Consulte [Serviços de Machine Learning do SQL Server](https://docs.microsoft.com/sql/advanced-analytics/r/sql-server-r-services).

### <a name="filestream-and-filetable"></a>FileStream e Filetable

- Não há suporte para dados FILESTREAM.
- O banco de dados não pode conter grupos `FILESTREAM` de FileData.
- Não há suporte para `FILETABLE`.
- As tabelas não podem ter tipos `FILESTREAM`.
- Não há suporte para as seguintes funções:
  - `GetPathLocator()`
  - `GET_FILESTREAM_TRANSACTION_CONTEXT()`
  - `PathName()`
  - `GetFileNamespacePat)`
  - `FileTableRootPath()`

Para obter mais informações, consulte [FILESTREAM](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) e [FileTables](https://docs.microsoft.com/sql/relational-databases/blob/filetables-sql-server).

### <a name="full-text-semantic-search"></a>Pesquisa semântica de texto completo

Não há suporte para [Pesquisa semântica](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server).

### <a name="linked-servers"></a>Servidores vinculados

Os servidores vinculados em Instâncias Gerenciadas oferecem suporte a um número limitado de destinos:

- Os destinos com suporte são SQL Server e banco de dados SQL.
- Os destinos que não têm suporte são arquivos, Analysis Services e outros RDBMS.

Operações

- Não há suporte para transações de gravação entre instâncias.
- `sp_dropserver` é compatível com o descarte um servidor vinculado. Consulte [sp_dropserver](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-dropserver-transact-sql).
- A `OPENROWSET` função pode ser usada para executar consultas somente em instâncias de SQL Server. Eles podem ser gerenciados, localmente ou em máquinas virtuais. Consulte [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql).
- A `OPENDATASOURCE` função pode ser usada para executar consultas somente em instâncias de SQL Server. Eles podem ser gerenciados, localmente ou em máquinas virtuais. Somente os `SQLNCLI`valores `SQLNCLI11`, e `SQLOLEDB` têm suporte como um provedor. Um exemplo é `SELECT * FROM OPENDATASOURCE('SQLNCLI', '...').AdventureWorks2012.HumanResources.Employee`. Consulte [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql).

### <a name="polybase"></a>PolyBase

Não há suporte para tabelas externas que fazem referência aos arquivos no HDFS ou no armazenamento de BLOBs do Azure. Para obter informações sobre o polybase, consulte [polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide).

### <a name="replication"></a>Replicação

A [replicação](sql-database-managed-instance-transactional-replication.md) transacional está disponível para visualização pública em instância gerenciada com algumas restrições:
- Os tipos Al de participantes de replicação (Publicador, distribuidor, assinante de pull e assinante push) podem ser colocados em Instância Gerenciada, mas o Publicador e o distribuidor não podem ser colocados em instâncias diferentes.
- Os tipos de replicação transacional, de instantâneo e bidirecional têm suporte. Não há suporte para replicação de mesclagem, replicação ponto a ponto e assinaturas atualizáveis.
- Instância Gerenciada pode se comunicar com as versões recentes do SQL Server. Consulte as versões com suporte [aqui](sql-database-managed-instance-transactional-replication.md#supportability-matrix-for-instance-databases-and-on-premises-systems).
- A replicação transacional tem alguns [requisitos de rede adicionais](sql-database-managed-instance-transactional-replication.md#requirements).

Para obter informações sobre como configurar a replicação, consulte o [tutorial de replicação](replication-with-sql-database-managed-instance.md).

### <a name="restore-statement"></a>Instrução RESTAURAR 

- Sintaxe com suporte:
  - `RESTORE DATABASE`
  - `RESTORE FILELISTONLY ONLY`
  - `RESTORE HEADER ONLY`
  - `RESTORE LABELONLY ONLY`
  - `RESTORE VERIFYONLY ONLY`
- Sintaxe sem suporte:
  - `RESTORE LOG ONLY`
  - `RESTORE REWINDONLY ONLY`
- Fonte: 
  - `FROM URL`(Armazenamento de BLOBs do Azure) é a única opção com suporte.
  - Não há suporte para `FROM DISK`/`TAPE`/dispositivo de backup.
  - Conjuntos de backup não são compatíveis.
- `WITH`Não há suporte para opções, como `DIFFERENTIAL` não `STATS`ou.
- `ASYNC RESTORE`: A restauração continua mesmo que a conexão do cliente seja interrompida. Se a conexão for descartada, você poderá `sys.dm_operation_status` verificar a exibição do status de uma operação de restauração e para criar e remover um banco de dados. Consulte [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database). 

As opções de banco de dados a seguir são definidas ou substituídas e não podem ser alteradas posteriormente: 

- `NEW_BROKER`Se o agente não estiver habilitado no arquivo. bak. 
- `ENABLE_BROKER`Se o agente não estiver habilitado no arquivo. bak. 
- `AUTO_CLOSE=OFF`se um banco de dados no arquivo. bak `AUTO_CLOSE=ON`tiver. 
- `RECOVERY FULL`se um banco de dados no arquivo. bak `SIMPLE` tiver `BULK_LOGGED` o modo de recuperação ou.
- Um grupo de arquivos com otimização de memória é adicionado e chamado de XTP se ele não estava no arquivo Source. bak. 
- Qualquer grupo de arquivos com otimização de memória existente é renomeado para XTP. 
- `SINGLE_USER`as `RESTRICTED_USER` opções e são convertidas em. `MULTI_USER`

Limitações: 

- `.BAK`os arquivos que contêm vários conjuntos de backup não podem ser restaurados. 
- `.BAK`arquivos que contêm vários arquivos de log não podem ser restaurados.
- A restauração falhará se. `FILESTREAM` bak contiver dados.
- Os backups que contêm bancos de dados que têm objetos na memória ativos não podem ser restaurados em uma instância do Uso Geral. Para obter informações sobre instruções RESTORE, consulte [instruções RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql).

### <a name="service-broker"></a>Service broker

Não há suporte para agente de serviços entre instâncias:

- `sys.routes`: Como pré-requisito, você deve selecionar o endereço de sys. routes. O endereço deve ser LOCAL em cada rota. Consulte [sys.routes](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-routes-transact-sql).
- `CREATE ROUTE`: Você não pode `CREATE ROUTE` usar `ADDRESS` com outros `LOCAL`. Consulte [CRIAR ROTA](https://docs.microsoft.com/sql/t-sql/statements/create-route-transact-sql).
- `ALTER ROUTE`: Você não pode `ALTER ROUTE` usar `ADDRESS` com outros `LOCAL`. Consulte [ALTERAR ROTA](https://docs.microsoft.com/sql/t-sql/statements/alter-route-transact-sql). 

### <a name="stored-procedures-functions-and-triggers"></a>Procedimentos armazenados, funções e gatilhos

- `NATIVE_COMPILATION`Não tem suporte na camada de Uso Geral.
- Não há suporte para as seguintes opções [sp_configure](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-configure-transact-sql): 
  - `allow polybase export`
  - `allow updates`
  - `filestream_access_level`
  - `remote data archive`
  - `remote proc trans`
- Não há suporte para `sp_execute_external_scripts`. Consulte [sp_execute_external_scripts](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql#examples).
- Não há suporte para `xp_cmdshell`. Consulte [xp_cmdshell](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/xp-cmdshell-transact-sql).
- `Extended stored procedures`Não tem suporte, que `sp_addextendedproc`inclui `sp_dropextendedproc`  e. Consulte [procedimentos armazenados estendidos](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/general-extended-stored-procedures-transact-sql).
- Não há suporte para `sp_attach_db`, `sp_attach_single_file_db` e `sp_detach_db`. Consulte [sp_attach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-db-transact-sql), [sp_attach_single_file_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-attach-single-file-db-transact-sql) e [sp_detach_db](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-detach-db-transact-sql).

## <a name="Environment"></a>Restrições de ambiente

### <a name="subnet"></a>Subnet
- Na sub-rede reservada para seu Instância Gerenciada você não pode inserir outros recursos (por exemplo, máquinas virtuais). Coloque esses recursos em outras sub-redes.
- A sub-rede deve ter um número suficiente de [endereços IP](sql-database-managed-instance-connectivity-architecture.md#network-requirements)disponíveis. O mínimo é 16, enquanto a recomendação é ter pelo menos 32 endereços IP na sub-rede.
- [Os pontos de extremidade de serviço não podem ser associados à sub-rede da instância gerenciada](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Verifique se a opção pontos de extremidade de serviço está desabilitada quando você cria a rede virtual.
- O número de vCores e tipos de instâncias que você pode implantar em uma região têm algumas [restrições e limites](sql-database-managed-instance-resource-limits.md#regional-resource-limitations).
- Há algumas [regras de segurança que devem ser aplicadas na sub-rede](sql-database-managed-instance-connectivity-architecture.md#network-requirements).

### <a name="vnet"></a>VNET
- A VNet pode ser implantada usando modelo de recurso-modelo clássico para VNet sem suporte.
- Depois que uma instância gerenciada é criada, não há suporte para a movimentação da instância gerenciada ou da VNet para outro grupo de recursos ou assinatura.
- Alguns serviços, como ambientes de serviço de aplicativo, aplicativos lógicos e instâncias gerenciadas (usados para replicação geográfica, replicação transacional ou por meio de servidores vinculados) não podem acessar instâncias gerenciadas em regiões diferentes se seus VNets estiverem conectados usando [global emparelhamento](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Você pode se conectar a esses recursos via ExpressRoute ou VNet a VNet por meio de gateways de VNet.

## <a name="Changes"></a> Alterações de comportamento

As seguintes variáveis, funções e exibições retornam resultados diferentes:

- `SERVERPROPERTY('EngineEdition')`Retorna o valor 8. Essa propriedade identifica exclusivamente uma instância gerenciada. Consulte [SERVERPROPERTY](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `SERVERPROPERTY('InstanceName')`Retorna NULL porque o conceito de instância existente para SQL Server não se aplica a uma instância gerenciada. Consulte [SERVERPROPERTY('InstanceName')](https://docs.microsoft.com/sql/t-sql/functions/serverproperty-transact-sql).
- `@@SERVERNAME`Retorna um nome de DNS "conectável" completo, por exemplo, my-managed-instance.wcus17662feb9ce98.database.windows.net. Consulte [@@SERVERNAME](https://docs.microsoft.com/sql/t-sql/functions/servername-transact-sql). 
- `SYS.SERVERS`Retorna um nome de DNS "conectável" completo, `myinstance.domain.database.windows.net` como para as propriedades "Name" e "data_source". Consulte [SYS.SERVERS](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-servers-transact-sql).
- `@@SERVICENAME`Retorna NULL porque o conceito de serviço existente para SQL Server não se aplica a uma instância gerenciada. Consulte [@@SERVICENAME](https://docs.microsoft.com/sql/t-sql/functions/servicename-transact-sql).
- Há suporte para `SUSER_ID`. Retornará NULL se o logon do Azure AD não estiver em sys. syslogins. Consulte [SUSER_ID](https://docs.microsoft.com/sql/t-sql/functions/suser-id-transact-sql). 
- Não há suporte para `SUSER_SID`. Os dados errados são retornados, o que é um problema temporário conhecido. Consulte [SUSER_SID](https://docs.microsoft.com/sql/t-sql/functions/suser-sid-transact-sql). 

## <a name="Issues"></a> Problemas e limitações conhecidos

### <a name="tempdb-size"></a>Tamanho de TEMPDB

O tamanho máximo de arquivo `tempdb` de não pode ser maior que 24 GB por núcleo em uma camada de uso geral. O tamanho `tempdb` máximo em uma camada de comercialmente crítico é limitado ao tamanho do armazenamento da instância. `tempdb`o tamanho do arquivo de log é limitado a 120 GB em Uso Geral e Comercialmente Crítico camadas. O `tempdb` banco de dados sempre é dividido em 12 arquivos de data. Esse tamanho máximo por arquivo não pode ser alterado e novos arquivos não podem ser adicionados `tempdb`ao. Algumas consultas podem retornar um erro se precisarem de mais de 24 GB por núcleo `tempdb` no ou se produzirem mais do que 120 horas de log. `tempdb`é sempre recriado como um banco de dados vazio quando a instância inicia ou faz failover e qualquer alteração feita no `tempdb` não será preservada. 

### <a name="cant-restore-contained-database"></a>Não é possível restaurar o banco de dados independente

Instância Gerenciada não pode restaurar [bancos de dados independentes](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases). A restauração pontual dos bancos de dados independentes existentes não funciona em Instância Gerenciada. Esse problema será resolvido em breve. Enquanto isso, recomendamos que você remova a opção de confinamento de seus bancos de dados que são colocados em Instância Gerenciada. Não use a opção de confinamento para os bancos de dados de produção. 

### <a name="exceeding-storage-space-with-small-database-files"></a>Excedendo o espaço de armazenamento com arquivos de banco de dados pequenos

`CREATE DATABASE`as `ALTER DATABASE ADD FILE`instruções, `RESTORE DATABASE` e podem falhar porque a instância pode alcançar o limite de armazenamento do Azure.

Cada instância gerenciada Uso Geral tem até 35 TB de armazenamento reservado para o espaço em disco Premium do Azure. Cada arquivo de banco de dados é colocado em um disco físico separado. Tamanhos de disco podem ser 128 GB, 256 GB, 512 GB, 1 TB ou 4 TB. O espaço não utilizado no disco não é cobrado, mas a soma total dos tamanhos de disco Premium do Azure não pode exceder 35 TB. Em alguns casos, uma instância gerenciada que não precisa de 8 TB no total pode exceder o limite de 35 TB do Azure no tamanho do armazenamento devido à fragmentação interna.

Por exemplo, um Uso Geral instância gerenciada pode ter um arquivo de 1,2 TB de tamanho colocado em um disco de 4 TB. Ele também pode ter 248 arquivos com tamanho de 1 GB que são colocados em discos de 128 GB separados. Neste exemplo:

- O tamanho do armazenamento em disco total alocado é de 1 x 4 TB + 248 x 128 GB = 35 TB.
- O total de espaço reservado para os bancos de dados na instância é de 1 x 1,2 TB + 248 x 1 GB = 1,4 TB.

Este exemplo ilustra que, em determinadas circunstâncias, devido a uma distribuição específica de arquivos, uma instância gerenciada pode alcançar o limite de 35 TB reservado para um disco Premium do Azure conectado quando você talvez não o espere.

Neste exemplo, os bancos de dados existentes continuam funcionando e podem crescer sem qualquer problema, desde que novos arquivos não sejam adicionados. Novos bancos de dados não podem ser criados ou restaurados porque não há espaço suficiente para novas unidades de disco, mesmo que o tamanho total de todos os bancos de dados não atinja o limite de tamanho da instância. O erro retornado nesse caso não é claro.

Você pode [identificar o número de arquivos restantes](https://medium.com/azure-sqldb-managed-instance/how-many-files-you-can-create-in-general-purpose-azure-sql-managed-instance-e1c7c32886c1) usando exibições do sistema. Se você atingir esse limite, tente esvaziar [e excluir alguns dos arquivos menores usando a instrução DBCC SHRINKFILE](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-shrinkfile-transact-sql#d-emptying-a-file) ou alterne para a [camada comercialmente crítico, que não tem esse limite](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics).

### <a name="incorrect-configuration-of-the-sas-key-during-database-restore"></a>Configuração incorreta da chave SAS durante a restauração do banco de dados

`RESTORE DATABASE`isso lê que o arquivo. bak pode estar constantemente tentando ler o arquivo. bak e retornar um erro após um longo período de tempo se a assinatura `CREDENTIAL` de acesso compartilhado estiver incorreta. Execute RESTOre HEADERONLY antes de restaurar um banco de dados para certificar-se de que a chave SAS esteja correta.
Certifique-se de remover a entrelinha `?` da chave SAS que é gerada usando o portal do Azure.

### <a name="tooling"></a>Ferramentas

SQL Server Management Studio e SQL Server Data Tools podem ter alguns problemas enquanto acessam uma instância gerenciada.

- Usar entidades de segurança do servidor do Azure AD (logons) e usuários (visualização pública) com SQL Server Data Tools atualmente não tem suporte.
- Não há suporte para scripts para entidades de segurança de servidor do Azure AD (logons) e usuários (visualização pública) no SQL Server Management Studio.

### <a name="incorrect-database-names-in-some-views-logs-and-messages"></a>Nomes de banco de dados incorretos em algumas exibições, logs e mensagens

Várias entradas de exibições do sistema, contadores de desempenho, mensagens de erro, XEvents e logs de erros exibem identificadores do banco de dados GUID em vez dos nomes reais do banco de dados. Não confie nesses identificadores GUID porque eles são substituídos por nomes de banco de dados reais no futuro.

### <a name="database-mail"></a>Database Mail

O `@query` parâmetro no procedimento [sp_send_db_mail](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-send-dbmail-transact-sql) não funciona.

### <a name="database-mail-profile"></a>Perfil de Database Mail

O perfil de Database Mail usado pelo SQL Server Agent deve ser chamado `AzureManagedInstance_dbmail_profile`. Não há restrições para outros nomes de perfil de Database Mail.

### <a name="error-logs-arent-persisted"></a>Os logs de erros não são persistentes

Os logs de erros que estão disponíveis no Instância Gerenciada não são persistidos e seu tamanho não é incluído no limite máximo de armazenamento. Os logs de erros poderão ser apagados automaticamente se ocorrer um failover.

### <a name="error-logs-are-verbose"></a>Os logs de erro são detalhados

Uma instância gerenciada coloca informações detalhadas nos logs de erros e grande parte dela não é relevante. A quantidade de informações nos logs de erros será reduzida no futuro.

**Solução alternativa:** Use um procedimento personalizado para ler logs de erro que filtram algumas entradas irrelevantes. Para obter mais informações, consulte [instância gerenciada – sp_readmierrorlog](https://blogs.msdn.microsoft.com/sqlcat/2018/05/04/azure-sql-db-managed-instance-sp_readmierrorlog/).

### <a name="transaction-scope-on-two-databases-within-the-same-instance-isnt-supported"></a>O escopo da transação em dois bancos de dados na mesma instância não tem suporte

A `TransactionScope` classe no .net não funcionará se duas consultas forem enviadas a dois bancos de dados dentro da mesma instância no mesmo escopo de transação:

```csharp
using (var scope = new TransactionScope())
{
    using (var conn1 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn1.Open();
        SqlCommand cmd1 = conn1.CreateCommand();
        cmd1.CommandText = string.Format("insert into T1 values(1)");
        cmd1.ExecuteNonQuery();
    }

    using (var conn2 = new SqlConnection("Server=quickstartbmi.neu15011648751ff.database.windows.net;Database=b;User ID=myuser;Password=mypassword;Encrypt=true"))
    {
        conn2.Open();
        var cmd2 = conn2.CreateCommand();
        cmd2.CommandText = string.Format("insert into b.dbo.T2 values(2)");        cmd2.ExecuteNonQuery();
    }

    scope.Complete();
}

```

Embora esse código funcione com dados dentro da mesma instância, ele exigia o MSDTC.

**Solução alternativa:** Use [SqlConnection. ChangeDatabase (String)](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlconnection.changedatabase) para usar outro banco de dados em um contexto de conexão em vez de usar duas conexões.

### <a name="clr-modules-and-linked-servers-sometimes-cant-reference-a-local-ip-address"></a>Os módulos CLR e os servidores vinculados às vezes não podem fazer referência a um endereço IP local

Os módulos CLR colocados em uma instância gerenciada e servidores vinculados ou consultas distribuídas que referenciam uma instância atual às vezes não podem resolver o IP de uma instância local. Esse é um problema temporário.

**Solução alternativa:** Use conexões de contexto em um módulo CLR, se possível.

### <a name="tde-encrypted-databases-with-a-service-managed-key-dont-support-user-initiated-backups"></a>Os bancos de dados criptografados por TDE com uma chave gerenciada por serviço não dão suporte a backups iniciados pelo usuário

Não é possível `BACKUP DATABASE ... WITH COPY_ONLY` executar o em um banco de dados criptografado com o TDE (Transparent Data Encryption gerenciamento de serviços gerenciados). O TDE gerenciado por serviço força os backups a serem criptografados com uma chave TDE interna. A chave não pode ser exportada, portanto, não é possível restaurar o backup.

**Solução alternativa:** Use backups automáticos e restauração pontual ou use o [TDE (BYOK) gerenciado pelo cliente](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#customer-managed-transparent-data-encryption---bring-your-own-key) em vez disso. Você também pode desabilitar a criptografia no banco de dados.

### <a name="point-in-time-restore-follows-time-by-the-time-zone-set-on-the-source-instance"></a>A restauração pontual segue a hora do conjunto de fusos horários na instância de origem

A restauração pontual atualmente interpreta o tempo de restauração para o seguindo o fuso horário da instância de origem, em vez do UTC seguinte.
Verifique [instância gerenciada problemas conhecidos de fuso horário](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-timezone#known-issues) para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre instâncias gerenciadas, consulte [o que é uma instância gerenciada?](sql-database-managed-instance.md)
- Para obter uma lista de recursos e comparação, consulte comparação de recursos [do banco de dados SQL do Azure](sql-database-features.md).
- Para obter um guia de início rápido que mostra como criar uma nova instância gerenciada, consulte [criar uma instância gerenciada](sql-database-managed-instance-get-started.md).
