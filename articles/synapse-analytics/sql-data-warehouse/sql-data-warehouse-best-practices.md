---
title: Práticas recomendadas para o pool do SQL dedicado (antigo SQL DW)
description: Recomendações e práticas recomendadas para o desenvolvimento de soluções para o pool de SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d62ea594916dbf5656569b7e1a3afe9ee81d8f89
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685286"
---
# <a name="best-practices-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Práticas recomendadas para o pool do SQL dedicado (anteriormente conhecido como SQL DW) no Azure Synapse Analytics

Este artigo é uma coleção de práticas recomendadas para ajudá-lo a obter o desempenho ideal da sua implantação do [pool SQL dedicado (anteriormente conhecido como SQL DW)](sql-data-warehouse-overview-what-is.md) .  A finalidade deste artigo é fornecer algumas diretrizes básicas e destacar áreas de foco importantes.  

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](sql-data-warehouse-manage-compute-overview.md).

## <a name="maintain-statistics"></a>Manter as estatísticas

O pool dedicado do SQL (anteriormente conhecido como SQL DW) pode ser configurado para detectar e criar estatísticas automaticamente em colunas.  Os planos de consulta criados pelo otimizador são tão bons quanto as estatísticas disponíveis.  

Recomendamos que você habilite AUTO_CREATE_STATISTICS para os bancos de dados e mantenha as estatísticas atualizadas diariamente ou após cada carregamento para garantir que as estatísticas nas colunas usadas em suas consultas estejam sempre atualizadas.

Se você achar que está demorando muito para atualizar todas as estatísticas, convém tentar ser mais seletivo sobre quais colunas precisam de atualizações frequentes. Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente.

> [!TIP]
> Você terá mais benefícios se tiver estatísticas atualizadas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.

Confira também [Gerenciar as estatísticas da tabela](sql-data-warehouse-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Usar DMVs para monitorar e otimizar suas consultas

O pool dedicado do SQL (anteriormente conhecido como SQL DW) tem várias DMVs que podem ser usadas para monitorar a execução da consulta.  O artigo [monitorar sua carga de trabalho usando DMVs](sql-data-warehouse-manage-monitor.md) fornece detalhes passo a passo sobre como examinar os detalhes de uma consulta em execução.  

Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar.

Confira também [Monitore sua carga de trabalho usando DMVs](sql-data-warehouse-manage-monitor.md), [LABEL](sql-data-warehouse-develop-label.md), [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="tune-query-performance-with-new-product-enhancements"></a>Ajustar o desempenho de consultas com novos aprimoramentos de produtos

- [Ajuste de desempenho com exibições materializadas](performance-tuning-materialized-views.md)
- [Ajuste de desempenho com o índice columnstore clusterizado ordenado](performance-tuning-ordered-cci.md)
- [Ajuste de desempenho com cache de conjunto de resultados](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Uma única carga para uma pequena tabela com uma instrução INSERT ou mesmo uma recarga periódica de uma pesquisa pode ser útil para satisfazer suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  

No entanto, se você precisar carregar milhares ou milhões de linhas ao longo do dia, poderá achar que os INSERTS individuais poderão não dar conta.  Em vez disso, desenvolva seus processos de modo que eles gravem em um arquivo e outro processo adiante-se periodicamente e carregue esse arquivo.

Confira também [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Use o PolyBase para carregar e exportar dados rapidamente

O pool dedicado do SQL (anteriormente conhecido como SQL DW) dá suporte ao carregamento e à exportação de dados por meio de várias ferramentas, incluindo Azure Data Factory, polybase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades.  No entanto, quando você estiver carregando ou exportando grandes volumes de dados ou um desempenho rápido for necessário, o PolyBase será a melhor opção.  

O polybase foi projetado para aproveitar a natureza distribuída do sistema e carregar e exportar as magnitudes de dados mais rápido do que qualquer outra ferramenta.  As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO.   

> [!TIP]
> Usar o CTAS minimizará o registro em log das transações e será o modo mais rápido de carregar os dados.

Além disso, o Azure Data Factory dá suporte a cargas de PolyBase e pode alcançar um desempenho semelhante como CTAS.  O PolyBase oferece suporte a vários formatos de arquivo, incluindo os arquivos Gzip.  
  
> [!NOTE]
> Para maximizar a taxa de transferência ao usar os arquivos de texto gzip, divida os arquivos em 60 ou mais arquivos para maximizar o paralelismo de sua carga.  Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente.

Consulte também [carregar dados](design-elt-data-loading.md), [guia para usar o polybase](guidance-for-loading-data.md), [padrões e estratégias de carregamento de pools SQL dedicados](/archive/blogs/sqlcat/), [carregar dados com Azure data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [mover dados com Azure data Factory](../../data-factory/transform-data-using-machine-learning.md), [criar formato de arquivo externo](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)e [criar tabela como SELECT (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

Embora o Polybase, também conhecido como tabelas externas, possa ser a maneira mais rápida de carregar dados, ele não é o ideal para consultas. Atualmente, as tabelas Polybase são compatíveis apenas com os arquivos de blob do Azure e o Azure Data Lake Storage. Esses arquivos não tem quaisquer recursos de computação que os assegure.  

Como resultado, o pool SQL dedicado não pode descarregar esse trabalho e, portanto, deve ler o arquivo inteiro carregando-o para tempdb a fim de ler os dados.  Desse modo, se você tiver várias consultas que usarão esses dados, é melhor carregá-los apenas uma vez e fazer com que as consultas usem a tabela local.

Confira também [Guia para usar o PolyBase](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.  Isso facilita para os usuários começar a criar tabelas sem ter de decidir sobre como as tabelas deverão ser distribuídas.  Tabelas Round Robin podem apresentar desempenho suficiente para algumas cargas de trabalho, mas, na maioria das vezes, o desempenho será muito melhor se uma coluna de distribuição for escolhida.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  

Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order_id, e uma tabela de transações, que também é distribuída por order_id, quando você unir a tabela de pedidos e a tabela de transações em order_id, a consulta se tornará uma consulta de passagem, o que significa que podemos eliminar as operações de movimentação de dados.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.  

> [!TIP]
> Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  

Consulte os links a seguir para obter mais detalhes sobre como escolher uma coluna de distribuição pode melhorar o desempenho e também como definir uma tabela distribuída na cláusula WITH da instrução CREATE TABLE.

Confira também [Visão geral das tabelas](sql-data-warehouse-tables-overview.md), [Distribuição de tabelas](sql-data-warehouse-tables-distribute.md), [Selecionando a distribuição de tabelas](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="do-not-over-partition"></a>Não estender a partição

Embora o particionamento dos dados possa ser eficiente para manter seus dados na troca de partições ou otimizar as varreduras com a eliminação de partições, ter muitas partições pode reduzir a velocidade de suas consultas.  Muitas vezes, uma estratégia de particionamento de alta granularidade, que pode funcionar bem em SQL Server pode não funcionar bem no pool dedicado do SQL (anteriormente conhecido como SQL DW).  

Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas.  Tenha em mente que, em segundo plano, o pool dedicado do SQL particiona seus dados para você em bancos de dado 60, portanto, se você criar uma tabela com 100 partições, isso realmente resultará em 6000 partições.  

Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  Considere uma granularidade menor do que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Confira também [Particionamento de tabelas](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se você tiver uma instrução INSERT e esperar que ela demore uma hora, se possível, divida-a em quatro partes, sendo cada uma executada em 15 minutos.  Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela em que order_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia de outra tabela (consulte exemplos de [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)).  

Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS demorar a mesma quantidade de tempo, será uma operação muito mais segura de executar quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Confira também [Compreendendo as transações](sql-data-warehouse-develop-transactions.md), [Otimizando as transações](sql-data-warehouse-develop-best-practices-transactions.md), [Particionamento de tabelas](sql-data-warehouse-tables-partition.md), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [CTAS (Create table as select)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Reduzir tamanhos de resultados de consulta

Esta etapa ajuda a evitar problemas do lado do cliente causados por grandes resultados de consulta.  Você pode editar a consulta para reduzir o número de linhas retornadas. Algumas ferramentas de geração de consulta permitem que você adicione a sintaxe "primeiras N" a cada consulta.  Você também pode executar CETAS no resultado da consulta para uma tabela temporária e, em seguida, usar a exportação do PolyBase para o processamento de nível inferior.

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir a DDL, usar o menor tipo de dados compatível com seus dados vai melhorar o desempenho da consulta.  Essa abordagem é particularmente importante para colunas CHAR e VARCHAR.  

Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Confira também [Visão geral das tabelas](sql-data-warehouse-tables-overview.md), [Tipos de dados da tabela](sql-data-warehouse-tables-data-types.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Usar tabelas de heap temporárias para dados transitórios

Quando você estiver descarregando temporariamente os dados, poderá achar que usar uma tabela de heap tornará o processo geral mais rápido.  Se você estiver carregando dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap será muito mais rápido do que carregar os dados em uma tabela columnstore clusterizada.  

Além disso, o carregamento de dados em uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela em um armazenamento permanente.  As tabelas temporárias começam por "#" e só podem ser acessadas pela sessão que as criou, portanto, elas podem funcionar em cenários limitados.

As tabelas de heap são definidas na cláusula WITH de CREATE TABLE.  Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Confira também [Tabelas temporárias](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices columnstore clusterizados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL dedicado.  Por padrão, as tabelas no pool SQL dedicado são criadas como ColumnStore clusterizado.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  

Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Confira as [Causas da má qualidade de índice columnstore](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) no artigo sobre [Índices de tabela](sql-data-warehouse-tables-index.md) para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para as tabelas columnstore clusterizadas.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos de médio ou grande para carregamento de dados. Usar [unidades de data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Como as tabelas columnstore geralmente não enviam dados para um segmento columnstore compactado até que haja mais de 1 milhão linhas por tabela e cada tabela dedicada do pool SQL seja particionada em 60 tabelas, como uma regra geral, as tabelas columnstore não beneficiarão uma consulta, a menos que a tabela tenha mais de 60 milhões linhas.  Para a tabela com menos de 60 milhões de linhas, talvez não faça sentido ter um índice columnstore.  Também pode não prejudicar.  

Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, será necessário ter, pelo menos, 6 bilhões de linhas para aproveitar uma columnstore clusterizada (60 distribuições *100 partições* 1 milhão de linhas).  

Se a tabela não tiver as 6 bilhões de linhas neste exemplo, reduza o número de partições ou considere usar uma tabela de heap.  Também pode valer a pena testar para ver se um melhor desempenho pode ser obtido com uma tabela de heap com índices secundários em vez de uma tabela columnstore.

> [!TIP]
> Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Confira também [Índices de tabela](sql-data-warehouse-tables-index.md), [Guia dos índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [Recriando índices columnstore](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality)

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Usar uma classe maior de recursos para melhorar o desempenho da consulta

O pool SQL dedicado usa grupos de recursos como uma maneira de alocar memória para consultas.  Para o uso imediato, todos os usuários são atribuídos à classe de recurso pequena, que concede 100 MB de memória por distribuição.  Como sempre há 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total de todo o sistema é de 6.000 MB ou menos de 6 GB.  

Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores.  Algumas consultas, como as varreduras puras, não oferecerão nenhum benefício.  No entanto, usar classes de recurso maiores reduz a simultaneidade, portanto, você deve levar esse impacto em consideração antes de mover todos os usuários para uma classe de recurso grande.

Veja também [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usar uma Classe de Recurso Menor para Aumentar a Simultaneidade

Se você observar que as consultas do usuário parecem atrasar muito, é possível que os usuários estejam executando classes de recurso maiores e consumindo muitos slots de simultaneidade, fazendo com que outras consultas entrem na fila.  Para saber se as consultas dos usuários estão em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se alguma linha é retornada.

Veja também [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="other-resources"></a>Outros recursos

Confira também nosso artigo de [Solução de problemas](sql-data-warehouse-troubleshoot.md) para conhecer os problemas e as soluções comuns.

Se você não encontrar o que está procurando nesse artigo, tente usar a "Pesquisa de documentos" à esquerda da página para pesquisar todos os documentos do Azure Synapse.  A [página de P e R da Microsoft para o Azure Synapse](/answers/topics/azure-synapse-analytics.html) é um lugar para você postar perguntas para outros usuários e para o grupo de produto do Azure Synapse. Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  

Caso você prefira fazer perguntas sobre o Stack Overflow, também temos um [Fórum sobre o Stack Overflow do Azure Synapse](https://stackoverflow.com/questions/tagged/azure-sqldw).

Use a página [Comentários do Azure Synapse](https://feedback.azure.com/forums/307516-sql-data-warehouse) para fazer solicitações de recursos.  Adicionar suas solicitações ou recomendar outras solicitações realmente nos ajudará a priorizar os recursos.