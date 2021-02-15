---
title: Automatizar a replicação de alterações de esquema no Sincronização de Dados SQL
description: Saiba como automatizar a replicação das alterações de esquema na sincronização de dados do SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 11/14/2018
ms.openlocfilehash: f9997ea737b96185b31a7f51996a396fb5fc46c4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92790178"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>Automatize a replicação de alterações de esquema no Azure SQL Data Sync
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Sincronização de Dados SQL permite que os usuários sincronizem dados entre os bancos de dado no banco de dados SQL do Azure e SQL Server instâncias em uma direção ou em ambas as direções. Uma das limitações atuais de sincronização de dados SQL é uma falta de suporte para a replicação de alterações de esquema. Sempre que você alterar o esquema de tabela, você precisa aplicar as alterações manualmente em todos os pontos de extremidade, incluindo o hub e todos os membros e, em seguida, atualize o esquema de sincronização.

Este artigo apresenta uma solução para replicar automaticamente as alterações de esquema para todos os pontos de extremidade de sincronização de dados SQL.

1. Esta solução usa um gatilho DDL para controlar as alterações de esquema.
1. O gatilho insere os comandos de alteração de esquema em uma tabela de controle.
1. A tabela de controle é sincronizada com todos os pontos de extremidade usando o serviço de Sincronização de Dados.
1. Os gatilhos DML após a inserção são usados para aplicar as alterações de esquema nos outros pontos de extremidade.

Este artigo usa ALTER TABLE como um exemplo de uma alteração de esquema, mas essa solução também funciona para outros tipos de alterações de esquema.

> [!IMPORTANT]
> É recomendável que você leia este artigo com cuidado, especialmente as seções sobre [solução de problemas](#troubleshoot) e [outras considerações](#other), antes de começar a implementar a replicação de alteração de esquema automatizada em seu ambiente de sincronização. Também recomendamos que você leia [dados de sincronização em vários bancos de dados locais e de nuvem com sincronização de dados SQL](sql-data-sync-data-sql-server-sql-database.md). Algumas operações de banco de dados podem interromper a solução descrita neste artigo. Conhecimento de domínio adicional do SQL Server e Transact-SQL talvez seja necessário para solucionar esses problemas.

![Automatizar a replicação de alterações de esquema](./media/sql-data-sync-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Configurar a replicação de alteração de esquema automatizada

### <a name="create-a-table-to-track-schema-changes"></a>Criar uma tabela para controlar as alterações de esquema

Crie uma tabela para controlar as alterações de esquema em todos os bancos de dados no grupo de sincronização:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Esta tabela tem uma coluna de identidade para controlar a ordem das alterações de esquema. Você pode adicionar mais campos para obter mais informações de log, se necessário.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Criar uma tabela para acompanhar o histórico de alterações de esquema

Em todos os pontos de extremidade, crie uma tabela para registrar a ID do comando de alteração de esquema aplicada mais recentemente.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Crie um gatilho ALTER TABLE DDL no banco de dados no qual as alterações de esquema são feitas

Crie um gatilho DDL para operações de ALTER TABLE. Você só precisa criar o disparador no banco de dados em que são feitas alterações de esquema. Para evitar conflitos, permitir apenas as alterações de esquema em um banco de dados em um grupo de sincronização.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

O gatilho insere um registro na tabela de controle de mudança de esquema para cada comando ALTER TABLE. Este exemplo adiciona um filtro para evitar a replicação das alterações de esquema feitas no esquema **sincronização de dados** , pois elas são provavelmente feitas pelo serviço de sincronização de dados. Adicione filtros mais se você deseja replicar certos tipos de alterações de esquema.

Você também pode adicionar mais gatilhos para replicar a outros tipos de alterações de esquema. Por exemplo, crie disparadores CREATE_PROCEDURE, ALTER_PROCEDURE e em DROP_PROCEDURE para replicar alterações em procedimentos armazenados.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Criar um gatilho em outros pontos de extremidade para aplicar alterações de esquema durante a inserção

Esse gatilho executa o comando de alteração de esquema quando ele está sincronizado com outros pontos de extremidade. Você precisa criar o disparador em todos os pontos de extremidade, exceto onde são feitas alterações de esquema (ou seja, no banco de dados onde o DDL disparar `AlterTableDDLTrigger` é criado na etapa anterior).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Esse gatilho é executado após a inserção e verifica se o comando atual deve ser executado Avançar. A lógica de código garante que nenhuma instrução de mudança de esquema seja ignorada e que todas as alterações sejam aplicadas mesmo se a inserção estiver fora de ordem.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>Sincronizar a tabela de rastreamento para todos os pontos de extremidade de alteração de esquema

Você pode sincronizar a tabela de rastreamento para todos os pontos de extremidade usando o grupo de sincronização existente ou um novo grupo de sincronização de alteração de esquema. Verifique se que as alterações na tabela de rastreamento podem ser sincronizadas para todos os pontos de extremidade, especialmente quando você estiver usando uma direção sincronização.

Não sincronize a tabela de histórico de alteração de esquema, pois essa tabela mantém o estado diferente em diferentes pontos de extremidade.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>Aplicar as alterações de esquema em um grupo de sincronização

Apenas as alterações de esquema feitas no banco de dados onde o gatilho de DDL é criado são replicadas. Alterações de esquema feitas em outros bancos de dados não são replicadas.

Depois que as alterações de esquema são replicadas para todos os pontos de extremidade, você também precisa realizar etapas adicionais para atualizar o esquema de sincronização para iniciar ou parar a sincronização de novas colunas.

#### <a name="add-new-columns"></a>Adicionar novas colunas

1.  Alterar o esquema.

1.  Evite qualquer alteração de dados em que as novas colunas envolvidas até que você concluiu a etapa que cria o gatilho.

1.  Aguarde até que as alterações de esquema são aplicadas a todos os pontos de extremidade.

1.  Atualize o esquema de banco de dados e adicionar a nova coluna para o esquema de sincronização.

1.  Dados na nova coluna são sincronizados durante a próxima operação de sincronização.

#### <a name="remove-columns"></a>Remover colunas

1.  Remova as colunas do esquema de sincronização. A sincronização de dados interrompe a sincronização de dados nessas colunas.

1.  Alterar o esquema.

1.  Atualize o esquema do banco de dados.

#### <a name="update-data-types"></a>Atualizar tipos de dados

1.  Alterar o esquema.

1.  Aguarde até que as alterações de esquema são aplicadas a todos os pontos de extremidade.

1.  Atualize o esquema do banco de dados.

1.  Se os tipos de dados novos e antigos não são totalmente compatíveis - por exemplo, se você alterar de `int` para `bigint` -sincronização pode falhar antes que as etapas para criar os gatilhos são concluídas. Sincronização bem-sucedida após uma nova tentativa.

#### <a name="rename-columns-or-tables"></a>Renomear colunas ou tabelas

Renomear colunas ou tabelas faz com que a sincronização de dados parar de funcionar. Crie uma nova tabela ou coluna, preencha os dados e exclua a tabela ou coluna antiga em vez de renomear.

#### <a name="other-types-of-schema-changes"></a>Outros tipos de alterações de esquema

Para outros tipos de alterações de esquema - por exemplo, criação de procedimentos armazenados ou descartar um índice - atualizar o esquema de sincronização não é necessária.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a> Solucionar problemas de replicação automatizada de alterações de esquema

A lógica de replicação descrita neste artigo para de funcionar em algumas situações, por exemplo, se você fez um esquema de alteração em um banco de dados local que não há suporte para o banco de dados SQL do Azure. Nesse caso, a tabela de controle de alterações de esquema a sincronização falhará. Você precisa corrigir o problema manualmente:

1.  Desabilitar um gatilho DDL e evitar qualquer alteração de esquema adicional até que o problema seja corrigido.

1.  No banco de dados do terminal em que o problema está ocorrendo, desative o gatilho AFTER INSERT no nó de extremidade no qual a alteração do esquema não pode ser feita. Esta ação permite que o comando de mudança de esquema seja sincronizado.

1.  Acione a sincronização para sincronizar a tabela de controle de alterações do esquema.

1.  No banco de dados de terminal em que o problema está ocorrendo, consulte a tabela de histórico de alterações de esquema para obter o ID do último comando de mudança de esquema aplicado.

1.  Consulte a tabela para listar todos os comandos com uma ID maior que o valor de ID recuperado na etapa anterior de controle de alterações de esquema.

    a.  Ignore os comandos que não podem ser executados no banco de dados do ponto de extremidade. Você precisa lidar com a inconsistência de esquema. Reverta as alterações do esquema original se a inconsistência afetar seu aplicativo.

    b.  Aplica manualmente os comandos que devem ser aplicados.

1.  Atualizar a tabela de histórico de alteração de esquema e definir a última ID aplicada para o valor correto.

1.  Verifique se o esquema é atualizado.

1.  Reabilite o gatilho AFTER INSERT desabilitado na segunda etapa.

1.  Reabilite o gatilho DDL desativado na primeira etapa.

Se você quiser limpar os registros na tabela de rastreamento de alteração de esquema, use DELETE em vez do TRUNCATE. Nunca propagar novamente a coluna de identidade na tabela de alterações de esquema usando DBCC CHECKIDENT. Você pode criar tabelas de controle de alteração de esquema novo e atualize o nome da tabela no gatilho DDL se propagação é necessária.

## <a name="other-considerations"></a><a name="other"></a> Outras considerações

-   Os usuários de banco de dados que configuram os bancos de dados de hub e o membro precisam ter permissões suficientes para executar os comandos de alteração de esquema.

-   Você pode adicionar mais filtros no gatilho DDL para replicar apenas a alteração do esquema em tabelas ou operações selecionadas.

-   Você só pode fazer alterações de esquema no banco de dados em que o gatilho de DDL é criado.

-   Se você estiver fazendo uma alteração em um banco de dados SQL Server, verifique se a alteração de esquema tem suporte no banco de dados SQL do Azure.

-   Se forem feitas alterações de esquema em bancos de dados que não seja o banco de dados em que o gatilho DDL é criado, as alterações não são replicadas. Para evitar esse problema, você pode criar gatilhos DDL para bloquear alterações em outros pontos de extremidade.

-   Se você precisar alterar o esquema da tabela de controle de alterações de esquema, desative o gatilho de DDL antes de fazer a alteração e aplique manualmente a alteração a todos os pontos de extremidade. Atualizando o esquema em um gatilho AFTER INSERT na mesma tabela não funciona.

-   Não propagar novamente a coluna de identidade usando DBCC CHECKIDENT.

-   Não use TRUNCATE para limpar os dados na tabela de controle de alteração de esquema.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Sincronização de Dados SQL, veja:

-   Visão geral - [Sincronize dados em vários bancos de dados locais e na nuvem com o Azure SQL Data Sync](sql-data-sync-data-sql-server-sql-database.md)
-   Configurar sincronização de dados
    - No portal- [tutorial: configurar o sincronização de dados SQL para sincronizar dados entre o Azure SQL Database e SQL Server](sql-data-sync-sql-server-configure.md)
    - Com o PowerShell
        -  [Usar o PowerShell para sincronizar entre vários bancos de dados no banco de dados SQL do Azure](scripts/sql-data-sync-sync-data-between-sql-databases.md)
        -  [Usar o PowerShell para sincronizar entre um banco de dados no banco de dados SQL do Azure e um banco de dados em uma instância do SQL Server](scripts/sql-data-sync-sync-data-between-azure-onprem.md)
-   Agente de Sincronização de Dados - [Agente de Sincronização de Dados para Sincronização de Dados SQL do Azure](sql-data-sync-agent-overview.md)
-   Práticas recomendadas - [Práticas recomendadas para a Sincronização de Dados SQL do Azure](sql-data-sync-best-practices.md)
-   Monitor – [monitore a Sincronização de Dados SQL com logs do Azure Monitor](./monitor-tune-overview.md)
-   Solucionar problemas - [Solucionar problemas com o SQL Data Sync do Azure]()
-   Atualizar o esquema de sincronização
    -   Com o PowerShell - [usar o PowerShell para atualizar o esquema de sincronização em um grupo de sincronização existente](scripts/update-sync-schema-in-sync-group.md)