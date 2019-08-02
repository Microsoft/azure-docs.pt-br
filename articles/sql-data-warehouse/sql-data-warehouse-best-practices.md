---
title: Práticas recomendadas para o SQL Data Warehouse | Microsoft Docs
description: Recomendações e práticas recomendadas que você deve saber quando for desenvolver soluções para o SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/26/2018
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9c9e293a6e9c8126f2b82f68d591aee56ec32aec
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672289"
---
# <a name="best-practices-for-azure-sql-data-warehouse"></a>Práticas recomendadas para o Azure SQL Data Warehouse
Este artigo é uma coleção de melhores práticas para ajudá-lo a obter o desempenho ideal do SQL Data Warehouse do Azure.  Alguns conceitos neste artigo são básicos e fáceis de explicar, outros são mais avançados e os abordamos apenas superficialmente neste artigo.  A finalidade deste artigo é dar algumas orientações básicas e aumentar o reconhecimento dos pontos importantes a serem considerados ao criar seu data warehouse.  Cada seção apresenta um conceito e aponta para artigos mais detalhados que abordam o conceito com maior profundidade.

Se você está apenas começando a usar o SQL Data Warehouse, não deixe que este artigo assuste você.  A sequência dos tópicos está, em sua maioria, na ordem de importância.  Se você começar focando nos primeiros conceitos, já está de bom tamanho.  Conforme você se familiariza e se sentir confortável usando o SQL Data Warehouse, volte e examine mais alguns conceitos.  Não irá demorar muito para que tudo faça sentido.

Para orientações sobre carregamento, consulte [Diretrizes para carregar dados](guidance-for-loading-data.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento
Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md). 


## <a name="maintain-statistics"></a>Manter as estatísticas
Ao contrário do SQL Server, que detecta e cria automaticamente ou atualiza as estatísticas em colunas, o SQL Data Warehouse requer uma manutenção manual das estatísticas.  Embora estejamos planejando alterar isso no futuro, por enquanto você deve manter as estatísticas para assegurar que os planos do SQL Data Warehouse sejam otimizados.  Os planos criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  **Criar estatísticas de exemplo em cada coluna é uma maneira fácil de começar a usar as estatísticas.**  É igualmente importante atualizar as estatísticas quando ocorrem alterações significativas em seus dados.  Pode ser uma abordagem conservadora atualizar as estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas. Se você achar que está demorando muito para manter todas as estatísticas, convém tentar ser mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de uma atualização frequente.  Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente. **Você terá mais benefícios se tiver estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.**

Consulte também [gerenciar estatísticas de tabela][Manage table statistics], [CREATE STATISTICS][CREATE STATISTICS], [UPDATE STATISTICS][UPDATE STATISTICS]

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes
Uma única carga para uma pequena tabela com uma instrução INSERT ou mesmo uma recarga periódica de uma pesquisa pode ser útil para satisfazer suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  No entanto, se você precisar carregar milhares ou milhões de linhas ao longo do dia, poderá achar que os INSERTS individuais poderão não dar conta.  Em vez disso, desenvolva seus processos de modo que eles gravem em um arquivo e outro processo adiante-se periodicamente e carregue esse arquivo.

Confira também [INSERT][INSERT]

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Use o PolyBase para carregar e exportar dados rapidamente
O SQL Data Warehouse oferece suporte ao carregamento e exportação dos dados por meio de várias ferramentas, incluindo o Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades.  No entanto, quando você estiver carregando ou exportando grandes volumes de dados ou um desempenho rápido for necessário, o PolyBase será a melhor opção.  O PolyBase foi projetado para aproveitar a arquitetura MPP (Processamento Paralelo Massivo) do SQL Data Warehouse e, portanto, carregará e exportará grandes quantidades de dados mais rapidamente do que qualquer outra ferramenta.  As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO.  **Usar o CTAS minimizará o registro em log das transações e será o modo mais rápido de carregar os dados.**  Além disso, o Azure Data Factory dá suporte a cargas de PolyBase e pode alcançar um desempenho semelhante como CTAS.  O PolyBase oferece suporte a vários formatos de arquivo, incluindo os arquivos Gzip.  **Para maximizar a taxa de transferência ao usar os arquivos de texto gzip, divida os arquivos em 60 ou mais arquivos para maximizar o paralelismo de sua carga.**  Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente.

Consulte também [carregar dados][Load data], [Guide for using PolyBase][Guide for using PolyBase], [padrões e estratégias de carregamento do Azure SQL Data Warehouse][padrões e estratégias de carregamento do Azure SQL Data Warehouse], [ Carregar dados com o Azure Data Factory][Load Data with Azure Data Factory], [Move data with Azure Data Factory][Move data with Azure Data Factory], [CREATE EXTERNAL FILE FORMAT][CREATE EXTERNAL FILE FORMAT], [criar tabela como select (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas
Embora o Polybase, também conhecido como tabelas externas, possa ser a maneira mais rápida de carregar dados, ele não é o ideal para consultas. Atualmente, as tabelas Polybase do SQL Data Warehouse são compatíveis apenas com arquivos de blob do Azure e armazenamento do Azure Data Lake. Esses arquivos não tem quaisquer recursos de computação que os assegure.  Consequentemente, o SQL Data Warehouse não pode descarregar essa carga e, portanto, deve ler o arquivo todo carregando-o no tempdb a fim de ler os dados.  Desse modo, se você tiver várias consultas que usarão esses dados, é melhor carregá-los apenas uma vez e fazer com que as consultas usem a tabela local.

Confira também [Guia para usar o PolyBase][Guide for using PolyBase]

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash
Por padrão, as tabelas são distribuídas pelo método Round Robin.  Isso facilita para os usuários começar a criar tabelas sem ter de decidir sobre como as tabelas deverão ser distribuídas.  Tabelas Round Robin podem apresentar desempenho suficiente para algumas cargas de trabalho, mas, na maioria das vezes, o desempenho será muito melhor se uma coluna de distribuição for escolhida.  O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando você unir a tabela de pedidos e a tabela de transações em order_id, a consulta se tornará uma consulta de passagem, o que significa que podemos eliminar as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.  Essa explicação apenas trata superficialmente. Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  Consulte os links abaixo para muito mais detalhes sobre como selecionar uma coluna de distribuição pode melhorar o desempenho, bem como para definir uma tabela distribuída na cláusula WITH da instrução CREATE TABLE.

Consulte também [visão geral da tabela][Table overview], [Table distribution][Table distribution], [selecionando a distribuição da tabela][selecionando a distribuição de tabela], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="do-not-over-partition"></a>Não estender a partição
Embora o particionamento dos dados possa ser muito eficiente para manter seus dados na troca de partições ou otimizar as varreduras com a eliminação de partições, ter muitas partições pode reduzir a velocidade de suas consultas.  Geralmente, uma estratégia de particionamento com alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem no SQL Data Warehouse.  Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas.  Lembre-se que, internamente, o SQL Data Warehouse particiona os dados em 60 bancos de dados, portanto, se você criar uma tabela com 100 partições, isso realmente resultará em 6.000 partições.  Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  Considere uma granularidade menor do que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Confira também [Particionamento de tabelas][Table partitioning]

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações
As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  Por exemplo, se você tiver uma instrução INSERT e esperar que ela demore uma hora, se possível, divida-a em quatro partes, sendo cada uma executada em 15 minutos.  Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela onde order_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia a partir de outra tabela (consulte exemplos de ALTER TABLE).  Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS demorar a mesma quantidade de tempo, será uma operação muito mais segura de executar quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Consulte também [Noções básicas sobre transações][Understanding transactions], [Optimizing transactions][Optimizing transactions], [particionamento de tabelas][particionamento de tabela], [TRUNCATE TABLE][TRUNCATE TABLE] , [ALTER TABLE][ALTER TABLE], [Criar tabela como select (CTAS)][criar tabela como select (CTAS)]

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível
Ao definir a DDL, usar o menor tipo de dados que oferece suporte a seus dados melhorará o desempenho da consulta.  Isso é especialmente importante para as colunas CHAR e VARCHAR.  Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Consulte também [visão geral da tabela][Table overview], [Table data types][Table data types], [CREATE TABLE][CREATE TABLE]

## <a name="use-temporary-heap-tables-for-transient-data"></a>Usar tabelas de heap temporárias para dados transitórios
Quando você estiver descarregando temporariamente os dados no SQL Data Warehouse, poderá achar que usar uma tabela de heap tornará o processo geral mais rápido.  Se estiver carregando os dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap será muito mais rápido que carregar os dados em uma tabela columnstore clusterizado.  Além disso, o carregamento de dados em uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela em um armazenamento permanente.  As tabelas temporárias começam por "#" e só podem ser acessadas pela sessão que as criou, portanto, elas podem funcionar em cenários limitados.   As tabelas de heap são definidas na cláusula WITH de CREATE TABLE.  Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Consulte também [tabelas temporárias][Temporary tables], [CREATE TABLE][CREATE TABLE], [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT]

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas
Os índices columnstore clusterizados são uma das maneiras mais eficientes para poder armazenar os dados no SQL Data Warehouse.  Por padrão, as tabelas no SQL Data Warehouse são criadas como ColumnStore Clusterizado.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Consulte a [causas da baixo índice columnstore qualidade][Causes of poor columnstore index quality] in the [Table indexes][Table indexes] para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para as tabelas columnstore clusterizadas.  Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos de médio ou grande para carregamento de dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Como as tabelas columnstore geralmente não enviam os dados em um segmento columnstore compactado até haver mais de 1 milhão de linhas por tabela e cada tabela do SQL Data Warehouse é particionada em 60 tabelas, como regra geral, as tabelas columnstore não aproveitarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  Para a tabela com menos de 60 milhões de linhas, talvez não faça sentido ter um índice columnstore.  Também pode não prejudicar.  Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, será necessário ter pelo menos 6 bilhões de linhas para se beneficiar de um columnstore clusterizado (60 distribuições * 100 partições * 1 milhão de linhas).  Se a tabela não tiver as 6 bilhões de linhas neste exemplo, reduza o número de partições ou considere usar uma tabela de heap.  Também pode valer a pena testar para ver se um melhor desempenho pode ser obtido com uma tabela de heap com índices secundários em vez de uma tabela columnstore.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Consulte também [índices de tabela][Table indexes], [Columnstore indexes guide][Columnstore indexes guide], [recriando índices columnstore][recriando índices columnstore]

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Usar uma classe maior de recursos para melhorar o desempenho da consulta
O SQL Data Warehouse usa grupos de recursos como uma maneira de alocar memória para as consultas.  Para o uso imediato, todos os usuários são atribuídos à classe de recurso pequena, que concede 100 MB de memória por distribuição.  Como sempre há 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total de todo o sistema é de 6.000 MB ou menos de 6 GB.  Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores.  Algumas consultas, como as varreduras puras, não terão qualquer benefício.  Por outro lado, usar classes de recurso maiores afeta a simultaneidade, portanto, você deve levar isso em consideração antes de mover todos os usuários para uma classe de recurso grande.

Veja também [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usar uma Classe de Recurso Menor para Aumentar a Simultaneidade
Se você estiver percebendo que as consultas do usuário parecem atrasar muito, é possível que os usuários estejam executando classes de recurso maiores e consumindo muitos slots de simultaneidade, fazendo com que outras consultas entrem na fila.  Para saber se as consultas dos usuários estão em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se alguma linha é retornada.

Consulte também [classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md), [DM pdw_waits][sys.dm_pdw_waits]

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Usar DMVs para monitorar e otimizar suas consultas
O SQL Data Warehouse tem várias DMVs que podem ser usadas para monitorar a execução da consulta.  O artigo de monitoramento abaixo apresenta instruções passo a passo sobre como analisar os detalhes de uma consulta em execução.  Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar.

Consulte também [monitorar sua carga de trabalho usando DMVs][Monitor your workload using DMVs], [LABEL][LABEL], [opção][opção], [DM exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [DM pdw_request_steps][DM pdw_request_steps], [DM pdw_sql_requests][sys.dm_pdw_sql_requests], [DM pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN], [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Outros recursos
Confira também nosso artigo de [Solução de problemas][Troubleshooting] para conhecer os problemas e as soluções comuns.

Se você não encontrar o que estava procurando nesse artigo, tente usar a "Pesquisa de documentos" à esquerda da página para pesquisar todos os documentos do SQL Data Warehouse do Azure.  O [Fórum do Azure SQL Data Warehouse][Azure SQL Data Warehouse MSDN Forum] is a place for you to ask questions to other users and to the SQL Data Warehouse Product Group.  We actively monitor this forum to ensure that your questions are answered either by another user or one of us.  If you prefer to ask your questions on Stack Overflow, we also have an [Azure SQL Data Warehouse Stack Overflow Forum][Azure SQL Data Warehouse Stack Overflow Forum].

Por fim, use a página [Comentários do Azure SQL Data Warehouse][Azure SQL Data Warehouse Feedback] para fazer solicitações de recurso.  Adicionar suas solicitações ou recomendar outras solicitações realmente nos ajudará a priorizar os recursos.

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
[DM pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
