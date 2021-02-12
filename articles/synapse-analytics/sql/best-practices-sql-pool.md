---
title: Práticas recomendadas para pools SQL dedicados
description: Recomendações e práticas recomendadas que você deve saber ao trabalhar com pools SQL dedicados.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 242e5d042aa14e3b7bd92ebb37ae1be61b1b2c8f
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120964"
---
# <a name="best-practices-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Práticas recomendadas para pools de SQL dedicados no Azure Synapse Analytics

Este artigo fornece uma coleção de práticas recomendadas para ajudá-lo a obter o desempenho ideal para pools de SQL dedicados no Azure Synapse Analytics. Abaixo você encontrará orientações básicas e áreas de enfoque importantes ao criar sua solução. Cada seção apresenta um conceito e sugere artigos mais detalhados que abordam o conceito com maior profundidade.

## <a name="dedicated-sql-pools-loading"></a>Carregamento de pools SQL dedicados

Para obter diretrizes de carregamento de pools SQL dedicados, consulte [a orientação para carregar dados](data-loading-best-practices.md).

## <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e escalonamento, confira [Gerenciar computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="maintain-statistics"></a>Manter as estatísticas

Embora SQL Server automaticamente detecte e crie ou atualize estatísticas em colunas, os pools SQL dedicados exigem a manutenção manual de estatísticas. Convém manter as estatísticas para garantir que os planos do pool de SQL sejam otimizados.  Os planos criados pelo otimizador são tão bons quanto as estatísticas disponíveis.

> [!TIP]
> Criar estatísticas de exemplo em cada coluna é uma maneira fácil de começar a usar as estatísticas.  

É igualmente importante atualizar as estatísticas quando ocorrem alterações significativas em seus dados.  Pode ser uma abordagem conservadora atualizar as estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas.

Para reduzir o tempo de manutenção das estatísticas, seja seletivo quanto a quais colunas terão estatísticas ou precisarão de atualizações mais frequentes. Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados diariamente. Concentre-se em gerar estatísticas para colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.

Para ver informações adicionais sobre estatísticas, leia os artigos [Gerenciar estatísticas de tabela](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="group-insert-statements-into-batches"></a>Agrupar instruções INSERT em lotes

Um carregamento único para uma tabela pequena com uma instrução INSERT como `INSERT INTO MyLookup VALUES (1, 'Type 1')` pode ser a melhor abordagem, dependendo de suas necessidades. No entanto, se você precisar carregar milhares ou milhões de linhas em um dia, é provável que instruções INSERTS singleton não sejam ideais.

Para resolver esse problema, você pode desenvolver um processo em que seja feita a gravação em um arquivo e outro que carregue esse arquivo periodicamente. Leia o artigo [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obter mais informações.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Use o PolyBase para carregar e exportar dados rapidamente

O pool de SQL é compatível com o carregamento e exportação dos dados por meio de várias ferramentas, incluindo o Azure Data Factory, PolyBase e BCP.  Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades.  

> [!NOTE]
> O Polybase é a melhor opção de carregamento ou exportação de grandes volumes de dados ou há a necessidade de um desempenho mais rápido.

As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO. Nesses casos, o CTAS minimiza o registro em log das transações e é o modo mais rápido de carregar os dados. O Azure Data Factory também dá suporte a cargas de PolyBase e pode alcançar um desempenho semelhante ao do CTAS. O PolyBase dá suporte a vários formatos de arquivo, incluindo Gzip.

Para maximizar a taxa de transferência ao usar os arquivos de texto Gzip, divida os arquivos em 60 ou mais arquivos para maximizar o paralelismo de sua carga. Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente. Para ver informações adicionais sobre tópicos relevantes desta seção, leia os seguintes artigos:

- [Carregar dados](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Guia para usar o PolyBase](data-loading-best-practices.md)
- [Estratégias e padrões de carregamento de pool de SQL do Azure](/archive/blogs/sqlcat/azure-sql-data-warehouse-loading-patterns-and-strategies)
- [Carregar dados com o Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Mover dados com o Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Carregar e consultar tabelas externas

O Polybase não é a melhor opção para consultas. As tabelas do polybase para pools de SQL dedicados atualmente só dão suporte a arquivos de blob do Azure e armazenamento de Azure Data Lake. Esses arquivos não têm quaisquer recursos de computação de backup. Como resultado, os pools SQL dedicados não podem descarregar esse trabalho e devem ler o arquivo inteiro carregando-o para tempdb para que possa ler os dados.

Caso você tenha muitas consultas desses dados, será melhor carregá-los uma vez e fazer com que as consultas usem a tabela local. Para ver mais orientações sobre Polybase, leia o artigo [Guia de uso de PolyBase](data-loading-best-practices.md).

## <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin.   Esse padrão facilita que os usuários comecem a criar tabelas sem precisar decidir sobre como as tabelas deverão ser distribuídas. As tabelas Round Robin podem ser suficientes para algumas cargas de trabalho. Mas, na maioria dos casos, uma coluna de distribuição tem um melhor desempenho.  

O exemplo mais comum que demonstra como a tabela de distribuição por uma coluna tem melhor desempenho que uma Round Robin é quando duas tabelas de fatos grandes são unidas.  

Ou seja, quando há tabela de pedidos distribuída por order_id, e uma tabela de transações também distribuída por order_id, unir essas duas tabelas no order_id faz com que essa consulta se torne uma consulta passagem. As operações de movimentação de dados então são eliminadas. Menos etapas significam uma consulta mais rápida. Menos movimento de dados também resulta em consultas mais rápidas.

> [!NOTE]
> Quando uma tabela distribuída é carregada, seus dados de entrada não devem ser classificados na chave de distribuição. Pois isso retardará as cargas.

Os links de artigo fornecidos abaixo fornecerão detalhes adicionais sobre como melhorar o desempenho por meio da seleção de uma coluna de distribuição. Você também pode encontrar informações sobre como definir uma tabela distribuída na cláusula WITH da instrução CREATE TABLE:

- [Visão geral da tabela](develop-tables-overview.md)
- [Distribuição da tabela](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Selecionando a distribuição de tabelas](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="do-not-over-partition"></a>Não estender a partição

Embora o particionamento dos dados seja eficiente para manter seus dados na troca de partições ou otimizar as digitalizações eliminando partições, ter muitas partições pode reduzir a velocidade de suas consultas.  Geralmente, uma estratégia de particionamento de alta granularidade que pode funcionar bem em SQL Server pode não funcionar bem no pool SQL dedicado.  

Ter muitas partições poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas. os pools dedicados do SQL particionam automaticamente seus dados em bancos de dado 60. Portanto, se você criar uma tabela com 100 partições, o resultado será de 6.000 partições. Cada carga de trabalho é diferente. Assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  

Uma opção a ser considerada é usar uma granularidade menor do que a implementada usando SQL Server. Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Mais informações sobre particionamento são detalhadas no artigo [Particionamento de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação. Em caso de falha, elas devem ser revertidas. Para reduzir possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes. Por exemplo, se você prevê que uma instrução INSERT seja executada em uma hora, divida-a em quatro partes. Assim, cada execução levará apenas 15 minutos.  

> [!TIP]
> Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas de uma tabela em que a order_date constava em outubro de 2001, você pode particionar esses dados por mês. Em seguida, você pode alternar a partição com os dados de uma partição vazia de outra tabela (veja os exemplos de ALTER TABLE).  

Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS demorar a mesma quantidade de tempo, será muito mais seguro executá-lo quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Para ver informações adicionais sobre o conteúdo relacionado a esta seção, leia os artigos abaixo:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Noções básicas sobre transações](develop-transactions.md)
- [Otimização de transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Partições de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="reduce-query-result-sizes"></a>Reduzir tamanhos de resultados de consulta

Reduzir os tamanhos dos resultados da consulta ajuda a evitar problemas do lado do cliente causados por resultados de consulta grandes.  É possível editar a consulta para reduzir o número de linhas retornadas. Algumas ferramentas de geração de consulta permitem que você adicione a sintaxe "primeiras N" a cada consulta.  Você também pode executar CETAS no resultado da consulta para uma tabela temporária e, em seguida, usar a exportação do PolyBase para o processamento de nível inferior.

## <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Para melhorar o desempenho da consulta, use o menor tipo de dados compatível com seus dados quando definir uma DDL.  Essa recomendação é particularmente importante para colunas CHAR e VARCHAR.  Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Também é importante salientar que, em vez de usar colunas NVARCHAR, você deverá definir colunas como VARCHAR somente quando estritamente necessário.

Leia os artigos [Visão geral da tabela](develop-tables-overview.md), [Tipos de dados de tabela](develop-tables-data-types.md) e [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para obter uma visão mais detalhada dos conceitos essenciais relevantes às informações acima.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Usar tabelas de heap temporárias para dados transitórios

Quando você estiver temporariamente descarregando dados em pools SQL dedicados, as tabelas de heap geralmente farão o processo geral mais rápido.  Se você estiver carregando dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela heap será muito mais rápido do que carregar os dados em uma tabela columnstore clusterizada.  

O carregamento de dados em uma tabela temporária também será muito mais rápido que o carregamento em um armazenamento permanente.  As tabelas temporárias começam com um "#" e só podem ser acessadas por meio da sessão que a criou. Consequentemente, elas só podem funcionar em cenários limitados. As tabelas de heap são definidas na cláusula WITH de CREATE TABLE.  Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Para obter orientação adicional, leia os artigos [Tabelas temporárias](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices columnstore clusterizados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL dedicado.  Por padrão, as tabelas no pool SQL dedicado são criadas como ColumnStore clusterizado.  É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  

A qualidade de segmento pode ser medida pelo número de linhas em um Grupo de Linhas compactado. Confira as [Causas da má qualidade de índice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) no artigo sobre [Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para as tabelas columnstore clusterizadas.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos de médio ou grande para carregamento de dados. Usar [unidades de data warehouse](resource-consumption-models.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

As tabelas columnstore geralmente não enviam dados para um segmento columnstore compactado até que haja mais de 1 milhão linhas por tabela. Cada tabela de pools SQL dedicada é particionada em tabelas 60. Sendo assim, as tabelas de columstore não serão úteis para uma consulta a menos que a tabela tenha mais de 60 milhões de linhas.  

> [!TIP]
> No caso de tabelas com menos de 60 milhões linhas, ter um índice columnstore pode não ser a solução ideal.  

Se você particionar seus dados, cada partição precisará ter 1 milhão linhas para se beneficiar de um índice columnstore clusterizado.  No caso de uma tabela com 100 partições, ela precisa ter pelo menos 6 bilhões de linhas para se beneficiar de um columnstore clusterizado (60 distribuições *100 partições* 1 milhão de linhas).  

Se sua tabela não tem 6 bilhões de linhas, você tem duas opções principais. Reduzir o número de partições ou considerar usar uma tabela heap.  Também pode valer a pena experimentar usar uma tabela heap com índices secundários em vez de uma tabela columnstore para ver se você consegue obter um melhor desempenho.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  Para ver mais informações sobre índices columnstore e tabelas, leia os artigos [Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Guia de índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) e [Como reconstruir índices columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true#rebuilding-indexes-to-improve-segment-quality).

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Usar uma classe maior de recursos para melhorar o desempenho da consulta

Os pools de SQL usam grupos de recursos como uma forma de alocar memória para as consultas. Inicialmente, todos os usuários são atribuídos à classe de recurso pequena, que concede 100 MB de memória por distribuição.  Sempre haverá 60 distribuições. Cada distribuição recebe um mínimo de 100 MB. A alocação total de memória em todo o sistema é de 6.000 MB, ou pouco abaixo de 6 GB.  

Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores.  Algumas consultas não serão beneficiadas, como as de digitalização pura. A utilização de classes de recursos maiores afeta a simultaneidade. Portanto, convém lembrar disso antes de mover todos os seus usuários para uma classe de recursos grande.

Para obter informações adicionais sobre classes de recursos, leia o artigo [Classes de recursos para gerenciamento de carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Usar uma classe de recurso menor para aumentar a simultaneidade

Se você notar um longo atraso nas consultas de usuário, os usuários poderão estar sendo executados em classes de recursos maiores. Esse cenário promove o consumo de slots de simultaneidade, o que pode fazer com que outras consultas sejam colocadas em fila.  Para determinar se as consultas dos usuários estão em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se alguma linha é retornada.

Os artigos [Classes de recursos para gerenciamento da carga de trabalho](../sql-data-warehouse/resource-classes-for-workload-management.md) e [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) têm mais informações sobre esse processo.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>Usar DMVs para monitorar e otimizar suas consultas

Os pools de SQL têm várias DMVs que podem ser usadas para monitorar a execução da consulta.  O artigo de acompanhamento abaixo apresenta instruções passo a passo sobre como visualizar os detalhes de uma consulta em execução.  Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar. Para obter mais informações detalhadas, leia os artigos incluídos na lista abaixo:

- [Monitore sua carga de trabalho usando DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [LABEL](develop-label.md)
- [OPTION](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="next-steps"></a>Próximas etapas

Leia também artigo de [Solução de problemas](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para conhecer os problemas e as soluções comuns.

Se você está buscando informações que não encontrou neste artigo, pesquise a [Página de perguntas frequentes sobre o Microsoft Azure Synapse](/answers/topics/azure-synapse-analytics.html), onde você poderá publicar perguntas para outros usuários e no grupo de produtos do pool de SQL.  

Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  Caso você prefira fazer perguntas sobre o Stack Overflow, confira também o [Fórum sobre o Stack Overflow do pool de SQL do Azure](https://stackoverflow.com/questions/tagged/azure-sqldw).

Para solicitações de recursos, acesse a página de [Comentários sobre o pool de SQL do Azure](https://feedback.azure.com/forums/307516-sql-data-warehouse).  Seus comentários e votos a favor de outras solicitações nos ajudam a saber quais são os recursos que têm mais demanda.