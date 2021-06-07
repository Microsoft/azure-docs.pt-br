---
title: Eventos estendidos
description: Descreve eventos estendidos (XEvents) no Banco de Dados SQL do Azure e como as sessões de eventos diferem ligeiramente das sessões de eventos no Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 12/19/2018
ms.openlocfilehash: 139673e46421aa0dc19298697872fbff5fe587af
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "96501202"
---
# <a name="extended-events-in-azure-sql-database"></a>Eventos estendidos no Banco de Dados SQL do Azure 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

O conjunto de recursos de eventos estendidos no banco de dados SQL do Azure é um subconjunto robusto dos recursos em SQL Server e SQL Instância Gerenciada do Azure.

*XEvents* é um apelido informal usado às vezes para "eventos estendidos" em blogs e outras fontes de informações.

Informações adicionais sobre eventos estendidos estão disponíveis em:

- [Início rápido: eventos estendidos no SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Eventos estendidos](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Pré-requisitos

Este tópico pressupõe que você já tem algum conhecimento de:

- [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
- [Eventos estendidos](/sql/relational-databases/extended-events/extended-events)

- A grande parte de nossa documentação sobre eventos estendidos se aplica a SQL Server, banco de dados SQL do Azure e Instância Gerenciada do SQL do Azure.

A exposição prévia aos itens a seguir é útil ao escolher o Arquivo de Evento como o [destino](#AzureXEventsTargets):

- [Serviço de Armazenamento do Azure](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell com o armazenamento do Azure](/powershell/module/az.storage/)

## <a name="code-samples"></a>Exemplos de código

Os tópicos relacionados fornecem dois exemplos de código:

- [Código de destino do Buffer de Anéis para eventos estendidos no Banco de Dados SQL do Azure](xevent-code-ring-buffer.md)

  - Script curto e simples de Transact-SQL.
  - Enfatizamos no tópico do exemplo de código que, quando você concluir um destino de Buffer de Anéis, será necessário liberar seus recursos executando uma instrução alter-drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Mais tarde, você poderá adicionar outra instância do Buffer de Anéis com `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.

- [Código de destino do arquivo de evento para eventos estendidos no Banco de Dados SQL do Azure](xevent-code-event-file.md)

  - A Fase 1 é PowerShell a fim de criar o contêiner de Armazenamento do Azure
  - Fase 2 é Transact-SQL que usa o contêiner de Armazenamento do Azure.

## <a name="transact-sql-differences"></a>Diferenças do Transact-SQL

- Ao executar o comando [CREATE EVENT SESSION](/sql/t-sql/statements/create-event-session-transact-sql) no SQL Server, você usa a cláusula **ON SERVER** . Porém, no banco de dados SQL do Azure, use a cláusula **on Database** .
- A cláusula **ON DATABASE** também se aplica aos comandos Transact-SQL [ALTER EVENT SESSION](/sql/t-sql/statements/alter-event-session-transact-sql) e [DROP EVENT SESSION](/sql/t-sql/statements/drop-event-session-transact-sql).

- Uma prática recomendada é incluir a opção de sessão de evento de **STARTUP_STATE = ON** em sua instrução **CREATE EVENT SESSION** ou **ALTER EVENT SESSION**.
  - O valor **= ON** oferece suporte à reinicialização automática após a reconfiguração do banco de dados lógico devido a um failover.

## <a name="new-catalog-views"></a>Novas exibições do catálogo

O recurso de eventos estendidos recebe suporte de várias [exibições do catálogo](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql). As exibições do catálogo mostram *metadados ou definições* de sessões de eventos criadas pelo usuário no banco de dados atual. As exibições não retornam informações sobre as instâncias de sessões de eventos ativas.

| Nome da<br/>exibição do catálogo | Descrição |
|:--- |:--- |
| **sys.database_event_session_actions** |Retorna uma linha para cada ação em cada evento de uma sessão de eventos. |
| **sys.database_event_session_events** |Retorna uma linha para cada evento em uma sessão de eventos. |
| **sys.database_event_session_fields** |Retorna uma linha para cada coluna personalizável que foi explicitamente definida em eventos e destinos. |
| **sys.database_event_session_targets** |Retorna uma linha para cada destino de evento em uma sessão de evento. |
| **sys.database_event_sessions** |Retorna uma linha para cada sessão de evento no banco de dados. |

No Microsoft SQL Server, exibições do catálogo semelhantes têm nomes que incluem *.server\_* em vez de *.database\_*. O nome padrão é **sys.server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Novas exibições de gerenciamento dinâmico [(DMVs)](/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views)

O Banco de Dados SQL do Azure tem [exibições de gerenciamento dinâmico (DMVs)](/sql/relational-databases/system-dynamic-management-views/extended-events-dynamic-management-views) que dão suporte a eventos estendidos. DMVs mostram as sessões de evento *ativas* .

| Nome da DMV | Descrição |
|:--- |:--- |
| **sys.dm_xe_database_session_event_actions** |Retorna informações sobre ações da sessão de evento. |
| **sys.dm_xe_database_session_events** |Retorna informações sobre os eventos da sessão. |
| **sys.dm_xe_database_session_object_columns** |Mostra os valores de configuração de objetos associados a uma sessão. |
| **sys.dm_xe_database_session_targets** |Retorna informações sobre os destinos da sessão. |
| **sys.dm_xe_database_sessions** |Retorna uma linha para cada sessão de evento com escopo no banco de dados atual. |

No Microsoft SQL Server, exibições de catálogo semelhantes são nomeadas sem a parte do *\_ banco de dados* do nome, como:

- **sys.dm_xe_sessions**, em vez de nome<br/>**sys.dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>DMVs comuns a ambos

Para eventos estendidos, há DMVs adicionais que são comuns ao banco de dados SQL do Azure, Azure SQL Instância Gerenciada e Microsoft SQL Server:

- **sys.dm_xe_map_values**
- **sys.dm_xe_object_columns**
- **sys.dm_xe_objects**
- **sys.dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Localizar os eventos estendidos, ações e destinos disponíveis

Você pode executar um simples SQL **SELECT** para obter uma lista dos evento, ações e destino disponíveis.

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Destinos para suas sessões de evento do banco de dados SQL do Azure

Aqui estão os destinos que podem capturar resultados de suas sessões de evento no banco de dados SQL do Azure:

- [Destino de Buffer de Anéis](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)) - armazena dados na memória por um curto período.
- [Destino de Contador de eventos](/previous-versions/sql/sql-server-2016/ff878025(v=sql.130)) - conta todos os eventos que ocorrem durante uma sessão de eventos estendidos.
- [Destino de Arquivo de evento](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) - grava buffers completos em um contêiner de Armazenamento do Azure.

A API do [ETW (rastreamento de eventos para Windows)](/dotnet/framework/wcf/samples/etw-tracing) não está disponível para eventos estendidos no banco de dados SQL do Azure.

## <a name="restrictions"></a>Restrições

Há algumas diferenças relacionadas à segurança ao se ajustar ao ambiente de nuvem do banco de dados SQL do Azure:

- Os eventos estendidos são baseados no modelo de isolamento de locatário único. Uma sessão de eventos em um banco de dados não pode acessar dados ou eventos de outro banco de dados.
- Não é possível emitir uma instrução **CREATE EVENT SESSION** no contexto do banco de dados **mestre**.

## <a name="permission-model"></a>Modelo de permissão

Você deve ter permissão de **Controle** no banco de dados para emitir uma instrução **CREATE EVENT SESSION**. O proprietário do banco de dados (dbo) tem a permissão de **Controle** .

### <a name="storage-container-authorizations"></a>Autorizações de contêiner de armazenamento

O token SAS gerado para o contêiner de Armazenamento do Azure deve especificar **rwl** para as permissões. O valor **rwl** fornece as seguintes permissões:

- Ler
- Gravar
- Lista

## <a name="performance-considerations"></a>Considerações sobre o desempenho

Há situações nas quais o uso intensivo de eventos estendidos pode acumular mais memória ativa do que o recomendado para a integridade geral do sistema. Portanto, o banco de dados SQL do Azure define e ajusta os limites dinamicamente na quantidade de memória ativa que pode ser acumulada por uma sessão de evento. Muitos fatores pesam no cálculo dinâmico.

Se você receber uma mensagem de erro informando que há uma imposição de quantidade máxima de memória, estas são algumas das ações corretivas possíveis:

- Executar menos sessões simultâneas do evento.
- Por meio das instruções **CREATE** e **ALTER** das sessões de evento, reduza a quantidade de memória que você especifica na cláusula **MAX\_MEMORY**.

### <a name="network-latency"></a>Latência da rede

O destino **Arquivo de Evento** pode enfrentar latência de rede ou falhas ao persistir dados para blobs de Armazenamento do Azure. Outros eventos no banco de dados SQL do Azure podem ser atrasados enquanto esperam a conclusão da comunicação de rede. Esse atraso pode retardar sua carga de trabalho.

- Para reduzir esse risco de desempenho, evite configurar a opção **EVENT_RETENTION_MODE** como **NO_EVENT_LOSS** nas definições de sua sessão de eventos.

## <a name="related-links"></a>Links relacionados

- [Usando Azure PowerShell com o armazenamento do Azure](/powershell/module/az.storage/).
- [Cmdlets do Armazenamento do Azure](/powershell/module/Azure.Storage)
- [Usando o PowerShell do Azure com o Armazenamento do Azure](/powershell/module/az.storage/)
- [Como usar o Armazenamento de blob do .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](/sql/t-sql/statements/create-credential-transact-sql)
- [CREATE EVENT SESSION (Transact-SQL)](/sql/t-sql/statements/create-event-session-transact-sql)
- [Postagens do blog de Jonathan Kehayias sobre eventos estendidos no Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- A página Web *Atualizações de Serviço* do Azure, limitada com o parâmetro para o Banco de Dados SQL do Azure:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->