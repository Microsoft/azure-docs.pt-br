---
title: Práticas recomendadas de desenvolvimento do Azure SQL Data Warehouse | Microsoft Docs
description: Recomendações e práticas recomendadas que você deve saber quando for desenvolver soluções para o SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 09/04/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1682a26ba713db564484e8984010e9c12ce9d79e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421419"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Práticas recomendadas de desenvolvimento do Azure SQL Data Warehouse
Este artigo descreve orientações e práticas recomendadas à medida que você desenvolve sua solução de data warehouse. 

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento
Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md). 


## <a name="maintain-statistics"></a>Manter as estatísticas
Certifique-se de que atualizar as estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas. Se você achar que está demorando muito para manter todas as estatísticas, convém tentar ser mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de uma atualização frequente.  Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente. **Você terá mais benefícios se tiver estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.**

Confira também [Gerenciar as estatísticas da tabela][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash
Por padrão, as tabelas são distribuídas pelo método Round Robin.  Isso facilita para os usuários começar a criar tabelas sem ter de decidir sobre como as tabelas deverão ser distribuídas.  Tabelas Round Robin podem apresentar desempenho suficiente para algumas cargas de trabalho, mas, na maioria das vezes, o desempenho será muito melhor se uma coluna de distribuição for escolhida.  O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando você unir a tabela de pedidos e a tabela de transações em order_id, a consulta se tornará uma consulta de passagem, o que significa que podemos eliminar as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.  Essa explicação apenas trata superficialmente. Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  Consulte os links abaixo para obter muito mais detalhes sobre como escolher uma coluna de distribuição pode melhorar o desempenho e também como definir uma tabela distribuída na cláusula WITH da instrução CREATE TABLES.

Confira também [Visão geral das tabelas][Table overview], [Distribuição de tabelas][Table distribution], [Selecionando a distribuição de tabelas][Selecting table distribution], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Não estender a partição
Embora o particionamento dos dados possa ser muito eficiente para manter seus dados na troca de partições ou otimizar as varreduras com a eliminação de partições, ter muitas partições pode reduzir a velocidade de suas consultas.  Geralmente, uma estratégia de particionamento com alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem no SQL Data Warehouse.  Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas.  Lembre-se que, internamente, o SQL Data Warehouse particiona os dados em 60 bancos de dados, portanto, se você criar uma tabela com 100 partições, isso realmente resultará em 6.000 partições.  Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  Considere uma granularidade menor do que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Confira também [Particionamento de tabelas][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações
As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  Por exemplo, se você tiver uma instrução INSERT e esperar que ela demore uma hora, se possível, divida-a em quatro partes, sendo cada uma executada em 15 minutos.  Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela onde order_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia a partir de outra tabela (consulte exemplos de ALTER TABLE).  Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS demorar a mesma quantidade de tempo, será uma operação muito mais segura de executar quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Confira também [Compreendendo as transações][Understanding transactions], [Otimizando as transações][Optimizing transactions], [Particionamento de tabelas][Table partitioning], [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE], [Create table as select (CTAS)][Create table as select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível
Ao definir a DDL, usar o menor tipo de dados que oferece suporte a seus dados melhorará o desempenho da consulta.  Isso é especialmente importante para as colunas CHAR e VARCHAR.  Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Confira também [Visão geral das tabelas][Table overview], [Tipos de dados da tabela][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas
Os índices columnstore clusterizados são uma das maneiras mais eficientes para poder armazenar os dados no SQL Data Warehouse.  Por padrão, as tabelas no SQL Data Warehouse são criadas como ColumnStore Clusterizado.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Confira as [Causas da má qualidade de índice columnstore][Causes of poor columnstore index quality] no artigo sobre [Índices de tabela][Table indexes] para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para as tabelas columnstore clusterizadas.  Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos de médio ou grande para carregamento de dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Como as tabelas columnstore geralmente não enviam os dados em um segmento columnstore compactado até haver mais de 1 milhão de linhas por tabela e cada tabela do SQL Data Warehouse é particionada em 60 tabelas, como regra geral, as tabelas columnstore não aproveitarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  Para a tabela com menos de 60 milhões de linhas, talvez não faça sentido ter um índice columnstore.  Também pode não prejudicar.  Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, será necessário ter pelo menos 6 bilhões de linhas para se beneficiar de um columnstore clusterizado (60 distribuições * 100 partições * 1 milhão de linhas).  Se a tabela não tiver as 6 bilhões de linhas neste exemplo, reduza o número de partições ou considere usar uma tabela de heap.  Também pode valer a pena testar para ver se um melhor desempenho pode ser obtido com uma tabela de heap com índices secundários em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Confira também [Índices de tabela][Table indexes], [Guia dos índices columnstore][Columnstore indexes guide], [Recriando índices columnstore][Rebuilding columnstore indexes]

## <a name="next-steps"></a>Próximas etapas
Se você não encontrar o que estava procurando nesse artigo, tente usar a "Pesquisa de documentos" à esquerda da página para pesquisar todos os documentos do SQL Data Warehouse do Azure.  O [Fórum do SQL Data Warehouse do Azure][Azure SQL Data Warehouse MSDN Forum] é um local para você fazer perguntas a outros usuários e ao Grupo de Produtos do SQL Data Warehouse.  Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  Caso você prefira fazer perguntas sobre o Stack Overflow, também temos um [Fórum sobre o Stack Overflow do SQL Data Warehouse do Azure][Azure SQL Data Warehouse Stack Overflow Forum].

Por fim, use a página [Comentários do SQL Data Warehouse do Azure][Azure SQL Data Warehouse Feedback] para fazer solicitações de recurso.  Adicionar suas solicitações ou recomendar outras solicitações realmente nos ajudará a priorizar os recursos.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
