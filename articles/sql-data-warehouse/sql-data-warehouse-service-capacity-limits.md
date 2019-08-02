---
title: Limites de capacidade do SQL Data Warehouse do Azure | Microsoft Docs
description: Valores máximos permitidos para vários componentes do Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/14/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a8f4412861eeaf2cbec360b13c0fe75e99d4fc1d
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839638"
---
# <a name="sql-data-warehouse-capacity-limits"></a>Limites de capacidade do SQL Data Warehouse
Valores máximos permitidos para vários componentes do Azure SQL Data Warehouse.

## <a name="workload-management"></a>Gerenciamento de carga de trabalho
| Categoria | DESCRIÇÃO | Máximo |
|:--- |:--- |:--- |
| [DWU (Unidades de Data Warehouse)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DWU máxima para um único Data Warehouse do SQL | Gen1: DW6000<br></br>Gen2: DW30000c |
| [DWU (Unidades de Data Warehouse)](what-is-a-data-warehouse-unit-dwu-cdwu.md) |DTU padrão por servidor |54.000<br></br>Por padrão, cada SQL Server (por exemplo, myserver.database.windows.net) tem uma Cota de DTU de 54.000, que permite até DW6000c. Essa cota é simplesmente um limite de segurança. Você pode aumentar sua cota [criando um tíquete de suporte](sql-data-warehouse-get-started-create-support-ticket.md) e selecionando *Cota* como o tipo de solicitação.  Para calcular suas necessidades de DTU, multiplique 7,5 pelo total de DWU necessário ou 9,0 pelo total de cDWU necessário. Por exemplo:<br></br>DW6000 x 7,5 = 45.000 DTUs<br></br>DW6000c x 9,0 = 54.000 DTUs.<br></br>Exiba seu consumo atual de DTU na opção SQL Server no portal. Os bancos de dados em pausa e que não estão em pausa contam como a cota de DTU. |
| Conexão de banco de dados |Máximo simultâneas abrir sessões |1024<br/><br/>O número de sessões abertas simultâneas variará com base em DWU o selecionado. DWU600c e acima suportam um máximo de 1024 abrir sessões. DWU500c e suporte a seguir, um limite máximo de sessão aberta simultâneas de 512. Observe que há limites no número de consultas que podem ser executadas simultaneamente. Quando o limite de simultaneidade for excedido, a solicitação irá para uma fila interna onde aguardará seu processamento. |
| Conexão de banco de dados |Memória máxima para instruções preparadas |20 MB |
| [Gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) |Máximo de consultas simultâneas |128<br/><br/> O SQL Data Warehouse pode executar um máximo de 128 consultas simultâneas e coloca as consultas restantes na fila.<br/><br/>O número de consultas simultâneas pode diminuir quando os usuários são atribuídos a classes de recurso superiores ou quando o SQL Data Warehouse tem uma [unidade de data warehouse](memory-and-concurrency-limits.md) inferior. Algumas consultas, como consultas DMV, sempre podem ser executadas e não afetam o limite de consultas simultâneas. Para obter mais detalhes sobre a execução de consultas simultâneas, consulte o artigo [Limites máximos de simultaneidade](memory-and-concurrency-limits.md#concurrency-maximums). |
| [tempdb](sql-data-warehouse-tables-temporary.md) |GB máximo |399 GB por DW100. Portanto, em DWU1000, o tempdb é dimensionado para 3,99 TB. |

## <a name="database-objects"></a>Objetos de banco de dados
| Categoria | DESCRIÇÃO | Máximo |
|:--- |:--- |:--- |
| Banco de dados |Tamanho máx. | Gen1: 240 TB compactados em disco. Este espaço é independente do espaço de tempdb ou de log, portanto, é dedicado às tabelas permanentes.  A compactação do columnstore clusterizado é estimada em cinco vezes.  Essa compactação permite que o banco de dados aumente até aproximadamente 1 PB quando todas as tabelas são de columnstore clusterizado (o tipo de tabela padrão). <br/><br/> Gen2: 240 TB para rowstore e armazenamento ilimitado para tabelas columnstore |
| Tabela |Tamanho máx. |60 TB compactados em disco |
| Tabela |Tabelas por banco de dados | 100.000 |
| Tabela |Colunas por tabela |1024 colunas |
| Tabela |Bytes por coluna |Dependente do [tipo de dados](sql-data-warehouse-tables-data-types.md)da coluna. O limite é de 8000 para tipos de dados char, 4000 para nvarchar ou 2 GB para tipos de dados MAX. |
| Tabela |Bytes por linha, tamanho definido |8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma maneira que no SQL Server, com a compactação de página. Assim como o SQL Server, o SQL Data Warehouse dá suporte ao armazenamento de estouro de linha, que permite que as **colunas de comprimento variável** sejam empurradas para fora da linha. Quando as linhas de comprimento variável são colocadas para fora da linha, apenas a raiz de 24 bytes é armazenada no registro principal. Para obter mais informações, consulte [Dados de estouro de linha que excedem 8 KB](https://msdn.microsoft.com/library/ms186981.aspx). |
| Tabela |Partições por tabela |15.000<br/><br/>Para alto desempenho, recomendamos minimizar o número de partições necessárias e, ao mesmo tempo, dar suporte aos seus requisitos de negócios. À medida que o número de partições aumenta, a sobrecarga de operações de DDL (Linguagem de Definição de Dados) e DML (Linguagem de Manipulação de Dados) também aumenta e faz com que o desempenho fique mais lento. |
| Tabela |Caracteres por valor de limite de partição. |4000 |
| Índice |Índices não clusterizados por tabela. |50<br/><br/>Aplica-se somente a tabelas rowstore. |
| Índice |Índices clusterizados por tabela. |1<br><br/>Aplica-se a tabelas rowstore e columnstore. |
| Índice |Tamanho da chave de índice. |900 bytes.<br/><br/>Aplica-se somente a índices rowstore.<br/><br/>Poderão ser criados índices em colunas varchar com um tamanho máximo de mais de 900 bytes se os dados existentes nas colunas não excederem 900 bytes durante a criação do índice. No entanto, as ações INSERT ou UPDATE posteriores nas colunas que excederem os 900 bytes de tamanho total falharão. |
| Índice |Colunas de chave por índice. |16<br/><br/>Aplica-se somente a índices rowstore. Os índices columnstore clusterizados incluem todas as colunas. |
| Estatísticas |O tamanho dos valores de coluna combinados. |900 bytes. |
| Estatísticas |As colunas por objeto de estatísticas. |32 |
| Estatísticas |As estatísticas criadas em colunas por tabela. |30.000 |
| Procedimentos Armazenados |Os níveis máximos de aninhamento. |8 |
| Exibir |Colunas por exibição |1\.024 |

## <a name="loads"></a>Cargas
| Categoria | DESCRIÇÃO | Máximo |
|:--- |:--- |:--- |
| Cargas de Polybase |MB por segundo |1<br/><br/>O Polybase carrega linhas menores que 1 MB. Não há suporte para carregar tipos de dados LOB em tabelas com um índice de Columnstore clusterizado (CCI).<br/><br/> |

## <a name="queries"></a>Consultas
| Categoria | DESCRIÇÃO | Máximo |
|:--- |:--- |:--- |
| Consultar |Consultas em fila em tabelas de usuário. |1000 |
| Consultar |Consultas simultâneas em exibições do sistema. |100 |
| Consultar |Consultas em fila em exibições do sistema |1000 |
| Consultar |Máximo de parâmetros |2098 |
| Lote |Tamanho máximo |65.536*4096 |
| Resultados de SELECT |Colunas por linha |4096<br/><br/>Nunca será possível ter mais de 4.096 colunas por linha no resultado de SELECT. Não há garantia de que você sempre terá 4096. Se o plano de consulta exigir uma tabela temporária, poderão ser aplicadas no máximo 1024 colunas por tabela. |
| SELECIONAR |Subconsultas aninhadas |32<br/><br/>Nunca será possível ter mais de 32 subconsultas aninhadas em uma instrução SELECT. Não há garantia de que você sempre terá 32. Por exemplo, JOIN pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível. |
| SELECIONAR |Colunas por JOIN |1024 colunas<br/><br/>Nunca será possível ter mais de 1.024 colunas em JOIN. Não há garantia de que você sempre terá 1024. Se o plano JOIN exigir uma tabela temporária com mais colunas do que o resultado de JOIN, o limite de 1024 se aplicará à tabela temporária. |
| SELECIONAR |Bytes por colunas GROUP BY. |8\.060<br/><br/>As colunas na cláusula GROUP BY podem ter, no máximo, 8.060 bytes. |
| SELECIONAR |Bytes por colunas ORDER BY |8060 bytes<br/><br/>As colunas na cláusula ORDER BY podem ter, no máximo, 8060 bytes |
| Identificadores por instrução |Número de identificadores referenciados |65.535<br/><br/>O SQL Data Warehouse do Azure limita o número de identificadores que podem estar contidos em uma única expressão de uma consulta. Exceder esse número resulta no erro 8632 do SQL Server. Para obter mais informações, confira [Erro interno: Foi atingido um limite dos serviços de expressão](https://support.microsoft.com/en-us/help/913050/error-message-when-you-run-a-query-in-sql-server-2005-internal-error-a). |
| Literais de cadeia de caracteres | Número de literais de cadeia de caracteres em uma instrução | 20.000 <br/><br/>O SQL Data Warehouse do Azure limita o número de constantes da cadeias de caracteres em uma única expressão de uma consulta. Exceder esse número resulta no erro 8632 do SQL Server.|

## <a name="metadata"></a>Metadados
| Exibição do sistema | Máximo de linhas |
|:--- |:--- |
| sys.dm_pdw_component_health_alerts |10.000 |
| sys.dm_pdw_dms_cores |100 |
| sys.dm_pdw_dms_workers |O número total de trabalhos DMS para as 1000 solicitações de SQL mais recentes. |
| sys.dm_pdw_errors |10.000 |
| sys.dm_pdw_exec_requests |10.000 |
| sys.dm_pdw_exec_sessions |10.000 |
| sys.dm_pdw_request_steps |O número total de etapas para as 1000 solicitações de SQL mais recentes armazenadas em sys.dm_pdw_exec_requests. |
| sys.dm_pdw_os_event_logs |10.000 |
| sys.dm_pdw_sql_requests |As 1000 solicitações de SQL mais recentes armazenadas em sys.dm_pdw_exec_requests. |

## <a name="next-steps"></a>Próximas etapas
Para obter recomendações sobre como usar o SQL Data Warehouse, consulte o [Cheat Sheet](cheat-sheet.md).
