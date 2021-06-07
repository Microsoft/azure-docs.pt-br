---
title: Gerenciar dados históricos com a política de retenção-Azure SQL Edge
description: Saiba como gerenciar dados históricos com a política de retenção no Azure SQL Edge
keywords: Borda do SQL, retenção de dados
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392360"
---
# <a name="manage-historical-data-with-retention-policy"></a>Gerenciar dados históricos com a política de retenção

A retenção de dados pode ser habilitada no banco de dado e qualquer uma das tabelas subjacentes individualmente, permitindo que os usuários criem políticas de envelhecimento flexíveis para suas tabelas e bancos de dados. A aplicação da retenção de dados é simples: ela requer que apenas um parâmetro seja definido durante a criação da tabela ou como parte de uma operação ALTER TABLE. 

Depois que a política de retenção de dados é definidas pelo para um banco de dado e a tabela subjacente, uma tarefa de temporizador de tempo em segundo plano é executada para remover registros obsoletos da tabela habilitada para retenção de dados. A identificação de linhas correspondentes e sua remoção da tabela ocorrem de forma transparente, na tarefa em segundo plano que é agendada e executada pelo sistema. A condição de idade para as linhas da tabela é verificada com base na coluna usada como `filter_column` na definição da tabela. Se o período de retenção, por exemplo, for definido como uma semana, as linhas da tabela qualificadas para limpeza atenderão a uma das seguintes condições: 

- Se a coluna de filtro usar o tipo de dados DATETIMEOFFSET, a condição será `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- Caso contrário, a condição será `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Fases de limpeza de retenção de dados

A operação de limpeza de retenção de dados é composta por duas fases. 
- Fase de descoberta – nesta fase, a operação de limpeza identifica todas as tabelas nos bancos de dados de usuário para criar uma lista para limpeza. A descoberta é executada uma vez por dia.
- Fase de limpeza – nesta fase, a limpeza é executada em todas as tabelas com retenção de dados finita, identificada na fase de descoberta. Se a operação de limpeza não puder ser executada em uma tabela, essa tabela será ignorada na execução atual e será repetida na próxima iteração. Os princípios a seguir são usados durante a limpeza
    - Se uma linha obsoleta estiver bloqueada por outra transação, essa linha será ignorada. 
    - A limpeza é executada com uma configuração padrão de tempo limite de bloqueio de 5 segundos. Se os bloqueios não puderem ser adquiridos nas tabelas dentro da janela de tempo limite, a tabela será ignorada na execução atual e será repetida na próxima iteração.
    - Se houver um erro durante a limpeza de uma tabela, essa tabela será ignorada e será selecionada na próxima iteração.

## <a name="manual-cleanup"></a>Limpeza manual

Dependendo das configurações de retenção de dados em uma tabela e da natureza da carga de trabalho no banco de dados, é possível que o thread de limpeza automática não remova completamente todas as linhas obsoletas durante sua execução. Para ajudá-lo e permitir que os usuários removam manualmente as linhas obsoletas, o `sys.sp_cleanup_data_retention` procedimento armazenado foi introduzido no Azure SQL Edge. 

Esse procedimento armazenado usa três parâmetros. 
    - Nome do esquema-nome do esquema de propriedade da tabela. Esse é um parâmetro necessário. 
    - Nome da tabela-nome da tabela para a qual a limpeza manual está sendo executada. Esse é um parâmetro necessário. 
    - RowCount (saída)-variável de saída. Retorna o número de linhas limpas pelo SP de limpeza manual. Esse é um parâmetro opcional. 

O exemplo a seguir mostra a execução do SP de limpeza manual para a tabela `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Como as linhas obsoletas são excluídas

O processo de limpeza depende do layout do índice da tabela. Uma tarefa em segundo plano é criada para executar a limpeza de dados obsoletos para todas as tabelas com um período de retenção finito. Limpar a lógica para o índice de rowgroup (árvore B ou heap) exclui a linha antiga em partes menores (até 10K) minimizando a pressão no log do banco de dados e no subsistema de e/s. Embora a lógica de limpeza utilize o índice de árvore B necessário, a ordem das exclusões para as linhas mais antigas que o período de retenção não pode ser garantido com certeza. Portanto, não assuma nenhuma dependência na ordem de limpeza em seus aplicativos.

A tarefa de limpeza para o columnstore clusterizado remove grupos de linhas inteiros de uma vez (normalmente contém 1 milhão de linhas cada), o que é muito eficiente, especialmente quando os dados são gerados e expiram em um ritmo alto.

![Limpeza de retenção de dados](./media/data-retention-cleanup/data-retention-cleanup.png)

A excelente compactação de dados e a limpeza de retenção eficiente tornam o índice columnstore clusterizado uma opção perfeita para cenários quando sua carga de trabalho gera rapidamente uma grande quantidade de dados.

> [!Note]
> No caso de índices de árvore B e heaps, a retenção de dados executa uma consulta de exclusão nas tabelas subjacentes, que podem entrar em conflito com os gatilhos de exclusão nas tabelas. É recomendável remover os gatilhos de exclusão das tabelas ou não habilitar a retenção de dados em tabelas que tenham o gatilho excluir DML.

## <a name="monitoring-data-retention-cleanup"></a>Monitorando a limpeza de retenção de dados

As operações de limpeza da política de retenção de dados podem ser monitoradas usando eventos estendidos (XEvents) no Azure SQL Edge. Para obter mais informações sobre eventos estendidos, consulte [visão geral do XEvents](/sql/relational-databases/extended-events/extended-events). 

Os seis eventos estendidos a seguir ajudam a acompanhar o estado das operações de limpeza. 

| Nome | Descrição |
|------| ------------|
| data_retention_task_started  | Ocorre quando uma tarefa em segundo plano para limpeza de tabelas com a política de retenção é iniciada. |
| data_retention_task_completed  | Ocorre quando a tarefa em segundo plano para limpeza de tabelas com política de retenção termina. |
| data_retention_task_exception  | Ocorre quando a tarefa em segundo plano para limpeza de tabelas com política de retenção falha fora do processo de limpeza de retenção específico da tabela. |
| data_retention_cleanup_started  | Ocorre quando o processo de limpeza da tabela com a política de retenção de dados é iniciado. |
| data_retention_cleanup_exception  | Ocorre falha no processo de limpeza da tabela com a política de retenção. |
| data_retention_cleanup_completed  | Ocorre quando o processo de limpeza da tabela com a política de retenção de dados termina. |  

Além disso, um novo tipo de buffer de anéis chamado `RING_BUFFER_DATA_RETENTION_CLEANUP` foi adicionado ao sys.dm_os_ring_buffers exibição de gerenciamento dinâmico. Este modo de exibição pode ser usado para monitorar as operações de limpeza de retenção de dados. 


## <a name="next-steps"></a>Próximas etapas
- [Política de retenção de dados](data-retention-overview.md)
- [Habilitar e desabilitar políticas de retenção de dados](data-retention-enable-disable.md)