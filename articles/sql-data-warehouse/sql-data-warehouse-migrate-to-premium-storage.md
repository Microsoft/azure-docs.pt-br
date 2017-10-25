---
title: Migrar seu data warehouse existente do Azure para o armazenamento premium | Microsoft Docs
description: "Instruções para migrar um data warehouse existente para o armazenamento premium"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: barbkess
editor: 
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 11/29/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 860e50b532b4b0a21d3be54f087730070b0e56bb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-your-data-warehouse-to-premium-storage"></a>Migrar seu data warehouse para o armazenamento premium
Recentemente, o Azure SQL Data Warehouse apresentou o [Armazenamento premium para maior previsibilidade de desempenho][premium storage for greater performance predictability]. Agora, os data warehouses existentes atualmente no armazenamento standard podem ser migrados para o armazenamento premium. Você pode aproveitar a migração automática ou, se preferir controlar quando migrar (o que envolve algum tempo de inatividade), você mesmo pode fazer a migração.

Se você tiver mais de um data warehouse, use o [agendamento de migração automática][automatic migration schedule] para determinar quando ele será migrado também.

## <a name="determine-storage-type"></a>Determinar o tipo de armazenamento
Se você criou um data warehouse antes das datas abaixo, estará usando o armazenamento standard.

| **Região** | **Data warehouse criado antes desta data** |
|:--- |:--- |
| Leste da Austrália |Armazenamento premium ainda não disponível |
| Leste da China |1º de novembro de 2016 |
| Norte da China |1º de novembro de 2016 |
| Alemanha Central |1º de novembro de 2016 |
| Nordeste da Alemanha |1º de novembro de 2016 |
| Oeste da Índia |Armazenamento premium ainda não disponível |
| Oeste do Japão |Armazenamento premium ainda não disponível |
| Centro-Norte dos EUA |10 de novembro de 2016 |

## <a name="automatic-migration-details"></a>Detalhes da migração automática
Por padrão, migraremos seu banco de dados para você entre 18h e 6h na hora do local de sua região durante o [agendamento de migração automática][automatic migration schedule]. Seu data warehouse existente ficará inutilizável durante a migração. A migração levará cerca de uma hora por terabyte de armazenamento por data warehouse. Não haverá cobrança durante qualquer parte da migração automática.

> [!NOTE]
> Após a conclusão da migração, o data warehouse ficará online e utilizável novamente.
>
>

A Microsoft está executando as etapas a seguir para concluir a migração (elas não exigem qualquer envolvimento de sua parte). Neste exemplo, imagine que o data warehouse existente no armazenamento standard é chamado atualmente de "MyDW".

1. A Microsoft renomeia “MyDW” para “MyDW_DO_NOT_USE_[Carimbo de data/hora]”
2. A Microsoft pausa o “MyDW_DO_NOT_USE_[carimbo de data/hora]”. Durante esse tempo, um backup é feito. Você poderá ver várias pausas e retomadas se encontrarmos problemas durante o processo.
3. A Microsoft cria um novo data warehouse chamado "MyDW" no armazenamento premium do backup feito na etapa 2. "MyDW" não será exibido até a restauração ser concluída.
4. Após a conclusão da restauração, "MyDW" retornará para as mesmas unidades de data warehouse e para o mesmo estado (em pausa ou ativo) em que estava antes da migração.
5. Após a conclusão da migração, a Microsoft excluirá "MyDW_DO_NOT_USE_ [Carimbo de data/hora]".

> [!NOTE]
> As configurações a seguir não são transferidas como parte da migração:
>
> * A auditoria no nível do banco de dados precisa ser habilitada novamente.
> * As regras de firewall no nível do banco de dados precisam ser adicionadas novamente. As regras de firewall no nível do servidor não deverão ser afetadas.
>
>

### <a name="automatic-migration-schedule"></a>cronograma de migração automática
As migrações automáticas ocorrem das 18h às 6h (hora local por região) durante o cronograma de interrupção a seguir.

| **Região** | **Data de início estimada** | **Data de término estimada** |
|:--- |:--- |:--- |
| Leste da Austrália |Ainda não foi determinado |Ainda não foi determinado |
| Leste da China |9 de janeiro de 2017 |13 de janeiro de 2017 |
| Norte da China |9 de janeiro de 2017 |13 de janeiro de 2017 |
| Alemanha Central |9 de janeiro de 2017 |13 de janeiro de 2017 |
| Nordeste da Alemanha |9 de janeiro de 2017 |13 de janeiro de 2017 |
| Oeste da Índia |Ainda não foi determinado |Ainda não foi determinado |
| Oeste do Japão |Ainda não foi determinado |Ainda não foi determinado |
| Centro-Norte dos EUA |9 de janeiro de 2017 |13 de janeiro de 2017 |

## <a name="self-migration-to-premium-storage"></a>Automigração para o armazenamento premium
Se quiser controlar quando o tempo de inatividade deverá ocorrer, você poderá usar as etapas a seguir para migrar um data warehouse existente no armazenamento standard para o armazenamento premium. Se você escolher essa opção, deverá concluir a migração antes de a migração automática começar nessa região. Isso evita qualquer risco de a migração automática causar um conflito (consulte a [agendamento de migração automática][automatic migration schedule]).

### <a name="self-migration-instructions"></a>Instruções de automigração
Para você mesmo migrar seu data warehouse, use os recursos de backup e restauração. A parte de restauração da migração deve demorar cerca de uma hora por terabyte de armazenamento por data warehouse. Se você desejar manter o mesmo nome após a conclusão da migração, execute as [etapas para renomear durante a migração][steps to rename during migration].

1. [Pause][Pause] seu data warehouse. Isso leva a um backup automático.
2. [Restaure][Restore] usando o instantâneo mais recente.
3. Exclua seu data warehouse existente do armazenamento standard. **Se você não conseguir fazer isso, será cobrado pelos dois data warehouses.**

> [!NOTE]
> As configurações a seguir não são transferidas como parte da migração:
>
> * A auditoria no nível do banco de dados precisa ser habilitada novamente.
> * As regras de firewall no nível do banco de dados precisam ser adicionadas novamente. As regras de firewall no nível do servidor não deverão ser afetadas.
>
>

#### <a name="rename-data-warehouse-during-migration-optional"></a>Renomear o data warehouse durante a migração (opcional)
Dois bancos de dados no mesmo servidor lógico não podem ter o mesmo nome. O SQL Data Warehouse agora dá suporte para a capacidade de renomear um data warehouse.

Neste exemplo, imagine que o data warehouse existente no armazenamento standard é chamado atualmente de "MyDW".

1. Renomeie "MyDW" usando o seguinte comando ALTER DATABASE. (Neste exemplo, o renomearemos como "MyDW_BeforeMigration".) Esse comando interrompe todas as transações existentes e deve ser feito no banco de dados mestre para ter êxito.
   ```
   ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
   ```
2. [Pause][Pause] "MyDW_BeforeMigration." Isso leva a um backup automático.
3. [Restaure][Restore], usando o instantâneo mais recente, um novo banco de dados com o nome antigo (por exemplo, “MyDW”).
4. Exclua "MyDW_BeforeMigration". **Se você não conseguir fazer isso, será cobrado pelos dois data warehouses.**


## <a name="next-steps"></a>Próximas etapas
Com a alteração para o armazenamento premium, também aumentamos o número de arquivos de blob do banco de dados na arquitetura subjacente do seu data warehouse. Para maximizar os benefícios de desempenho dessa mudança, recompile seus índices columnstore clusterizados usando o script a seguir. O script funciona forçando alguns dos seus dados existentes para os blobs adicionais. Se você não tomar nenhuma ação, os dados serão redistribuídos naturalmente com o tempo, conforme você carregar mais dados nas tabelas.

**Pré-requisitos:**

- O data warehouse deve ser executado com 1.000 unidades de data warehouse ou mais (consulte [capacidade de computação de escala][scale compute power]).
- O usuário que executa o script deve estar na [função mediumrc][mediumrc role] ou superior. Para adicionar um usuário a essa função, execute o seguinte: ````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Step 1: Create table to control index rebuild
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------
create table sql_statements
WITH (distribution = round_robin)
as select
    'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement,
    row_number() over (order by s.name, t.name) as sequence
from
    sys.schemas s
    inner join sys.tables t
        on s.schema_id = t.schema_id
where
    is_external = 0
;
go

--------------------------------------------------------------------------------
-- Step 2: Execute index rebuilds. If script fails, the below can be re-run to restart where last left off.
-- Run as user in mediumrc or higher
--------------------------------------------------------------------------------

declare @nbr_statements int = (select count(*) from sql_statements)
declare @i int = 1
while(@i <= @nbr_statements)
begin
      declare @statement nvarchar(1000)= (select statement from sql_statements where sequence = @i)
      print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement
      exec (@statement)
      delete from sql_statements where sequence = @i
      set @i += 1
end;
go
-------------------------------------------------------------------------------
-- Step 3: Clean up table created in Step 1
--------------------------------------------------------------------------------
drop table sql_statements;
go
````

Se você tiver algum problema com o data warehouse, [crie um tíquete de suporte][create a support ticket] e faça referência à “migração para o armazenamento premium” como a possível causa.

<!--Image references-->

<!--Article references-->
[automatic migration schedule]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: best-practices-availability-paired-regions.md
[main documentation site]: services/sql-data-warehouse.md
[Pause]: sql-data-warehouse-manage-compute-portal.md#pause-compute
[Restore]: sql-data-warehouse-restore-database-portal.md
[steps to rename during migration]: #optional-steps-to-rename-during-migration
[scale compute power]: sql-data-warehouse-manage-compute-portal.md#scale-compute-power
[mediumrc role]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->


<!--Other Web references-->
[Premium Storage for greater performance predictability]: https://azure.microsoft.com/en-us/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Azure Portal]: https://portal.azure.com
