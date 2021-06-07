---
title: Melhores práticas de desenvolvimento para o SQL do Synapse
description: Recomendações e melhores práticas que você deve saber para o desenvolvimento para o SQL do Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 479b20bcb0803d5483d139939da627d53ceccb49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667626"
---
# <a name="development-best-practices-for-synapse-sql"></a>Melhores práticas de desenvolvimento para o SQL do Synapse

Este artigo descreve orientações e práticas recomendadas à medida que você desenvolve sua solução de data warehouse. 

## <a name="dedicated-sql-pool-development-best-practices"></a>Práticas recomendadas de desenvolvimento do pool SQL dedicado

### <a name="reduce-cost-with-pause-and-scale"></a>Reduzir custos com pausa e dimensionamento

Para obter mais informações sobre como reduzir os custos por meio de pausa e dimensionamento, confira [Gerenciar computação](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="maintain-statistics"></a>Manter as estatísticas

Certifique-se de atualizar suas estatísticas diariamente ou após cada carga.  Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas. Se você achar que está demorando muito para manter todas as suas estatísticas, seja mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de atualização frequente.  

Por exemplo, talvez você queira atualizar as colunas de data, em que novos valores podem ser adicionados diariamente. 

> [!NOTE]
> Você terá mais benefícios se tiver estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.

Confira também [Gerenciar as estatísticas da tabela](develop-tables-statistics.md), [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true), [UPDATE STATISTICS](/sql/t-sql/statements/update-statistics-transact-sql?view=azure-sqldw-latest&preserve-view=true).

### <a name="hash-distribute-large-tables"></a>Tabelas grandes com distribuição Hash

Por padrão, as tabelas são distribuídas pelo método Round Robin. Essa funcionalidade facilita para os usuários começarem a criar tabelas sem a necessidade de decidir como suas tabelas devem ser distribuídas.  As tabelas Round Robin podem ser suficientes para algumas cargas de trabalho. Mas, na maioria dos casos, a seleção de uma coluna de distribuição terá um desempenho muito melhor.  

O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas.  

Por exemplo, se você tiver uma tabela de pedidos distribuída por order_id e uma tabela de transações, também distribuída por order_id, ao unir sua tabela de pedidos à tabela de transações no order_id, essa consulta se tornará uma consulta de passagem. 

Isso significa que as operações de movimentação de dados são eliminadas.  Menos etapas significam uma consulta mais rápida.  Menos movimento de dados também resulta em consultas mais rápidas.

> [!TIP]
> Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas.  

Siga os links a seguir para obter mais detalhes sobre como escolher uma coluna de distribuição pode melhorar o desempenho e também como definir uma tabela distribuída na cláusula WITH da instrução CREATE TABLES.

Confira também [Visão geral das tabelas](develop-tables-overview.md), [Distribuição de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Selecionando a distribuição de tabelas](/archive/blogs/sqlcat/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true) e [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="do-not-over-partition"></a>Não estender a partição
Embora o particionamento dos dados seja eficiente para manter seus dados na troca de partições ou otimizar as digitalizações eliminando partições, ter muitas partições pode reduzir a velocidade de suas consultas.  Geralmente, uma estratégia de particionamento de alta granularidade que pode funcionar bem em SQL Server pode não funcionar bem no pool SQL dedicado.  

> [!NOTE]
> Geralmente, uma estratégia de particionamento de alta granularidade que pode funcionar bem em SQL Server pode não funcionar bem no pool SQL dedicado.  

Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas. O pool SQL dedicado particiona seus dados para você em bancos de dado 60. 

Portanto, se você criar uma tabela com 100 partições, o resultado será de seis mil partições.  Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho.  

Uma opção a considerar é usar uma granularidade inferior ao que pode ter funcionado para você no SQL Server.  Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Confira também [Particionamento de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="minimize-transaction-sizes"></a>Minimizar os tamanhos das transações

As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas.  Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível.  Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes.  

Por exemplo, se você prevê que uma instrução INSERT seja executada em uma hora, divida-a em quatro partes, reduzindo cada execução a apenas 15 minutos.

> [!TIP]
> Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão.  

Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados.  

Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela onde order_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia a partir de outra tabela (consulte exemplos de ALTER TABLE).  

Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE.  Se um CTAS demorar a mesma quantidade de tempo, será uma operação muito mais segura de executar quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Confira também [Compreendendo as transações](develop-transactions.md), [Otimizando as transações](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Particionamento de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql?view=azure-sqldw-latest&preserve-view=true), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) e [CTAS (Create table as select)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="use-the-smallest-possible-column-size"></a>Use o menor tamanho de coluna possível

Ao definir a DDL, usar o menor tipo de dados compatível com seus dados vai melhorar o desempenho da consulta. Essa ação é especialmente importante para colunas CHAR e VARCHAR.  

Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25).  Evite definir todas as colunas de caractere para um tamanho grande padrão.  Além disso, defina colunas como VARCHAR quando isso for necessário, em vez de usar NVARCHAR.

Confira também [Visão geral das tabelas](develop-tables-overview.md), [Tipos de dados da tabela](develop-tables-data-types.md) e [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true).

### <a name="optimize-clustered-columnstore-tables"></a>Otimizar tabelas columnstore clusterizadas

Os índices columnstore clusterizados são uma das maneiras mais eficientes de armazenar seus dados no pool SQL dedicado.  Por padrão, as tabelas no pool SQL dedicado são criadas como ColumnStore clusterizado.  

É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore.  Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada.  

A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado.  Consulte o artigo [causas de baixa qualidade de índice columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) e [índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para obter instruções passo a passo sobre como detectar e melhorar a qualidade de segmento para tabelas columnstore clusterizadas.  

Como os segmentos columnstore de alta qualidade são importantes, é uma boa ideia usar IDs de usuários que estão na classe de recursos de médio ou grande para carregamento de dados. Usar [unidades de data warehouse](resource-consumption-models.md) menores significa que você deseja atribuir uma classe de recursos maior ao usuário que está carregando.

Como as tabelas columnstore geralmente não enviam dados para um segmento columnstore compactado até que haja mais de 1 milhão linhas por tabela, e cada tabela dedicada do pool SQL seja particionada em 60 tabelas, as tabelas columnstore não beneficiarão uma consulta, a menos que a tabela tenha mais de 60 milhões linhas.  

> [!TIP]
> No caso de tabelas com menos de 60 milhões linhas, ter um índice columnstore pode não ser a solução ideal.  

Além disso, se você particionar seus dados, convém considerar que cada partição precisa ter 1 milhão linhas para se beneficiar de um índice columnstore clusterizado.  Se uma tabela tiver 100 partições, será necessário ter, pelo menos, 6 bilhões de linhas para aproveitar uma columnstore clusterizada (60 distribuições *100 partições* 1 milhão de linhas).  

Se a tabela não tiver 6 bilhões de linhas, reduza o número de partições ou considere usar uma tabela heap.  Também pode valer a pena experimentar usar uma tabela heap com índices secundários em vez de uma tabela columnstore para ver se você consegue obter um melhor desempenho.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.  

Confira também [Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Guia dos índices columnstore](/sql/relational-databases/indexes/columnstore-indexes-overview?view=azure-sqldw-latest&preserve-view=true), [Como recriar índices columnstore](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality).

## <a name="serverless-sql-pool-development-best-practices"></a>Práticas recomendadas de desenvolvimento do pool SQL sem servidor

### <a name="general-considerations"></a>Considerações gerais

O pool SQL sem servidor permite consultar arquivos em suas contas de armazenamento do Azure. Ele não tem armazenamento local nem recursos de ingestão, o que significa que todos os arquivos que a consulta tem como destino são externos ao pool SQL sem servidor. Portanto, tudo relacionado à leitura de arquivos do armazenamento pode ter um impacto no desempenho da consulta.

### <a name="colocate-azure-storage-account-and-serverless-sql-pool"></a>Colocar a conta de armazenamento do Azure e o pool SQL sem servidor

Para minimizar a latência, coloque a conta de armazenamento do Azure e o ponto de extremidade do pool SQL sem servidor. As contas de armazenamento e os pontos de extremidade provisionados durante a criação do workspace estão localizados na mesma região.

Para obter um desempenho ideal, se você acessar outras contas de armazenamento com o pool SQL sem servidor, verifique se elas estão na mesma região. Caso contrário, haverá uma latência maior de transferência de rede dos dados da região remota para a região do ponto de extremidade.

### <a name="azure-storage-throttling"></a>Limitação do Armazenamento do Azure

Vários aplicativos e serviços podem acessar sua conta de armazenamento. Quando o IOPS combinado ou a taxa de transferência gerada por aplicativos, serviços e carga de trabalho do pool SQL sem servidor exceder os limites da conta de armazenamento, ocorrerá a limitação de armazenamento. Quando essa limitação ocorrer, haverá um efeito negativo significativo no desempenho da consulta.

Quando a limitação é detectada, o pool do SQL sem servidor tem manipulação interna desse cenário. O pool SQL sem servidor fará solicitações de armazenamento em um ritmo mais lento até que a limitação seja resolvida. 

No entanto, para a execução de consulta ideal, é recomendável que você não enfatize a conta de armazenamento com outras cargas de trabalho durante a execução da consulta.

### <a name="prepare-files-for-querying"></a>Preparar arquivos para consulta

Se possível, você pode preparar arquivos para melhorar o desempenho:

- Converter CSV em Parquet – Parquet é o formato de coluna. Como ele é compactado, ele tem tamanhos de arquivo menores do que arquivos CSV com os mesmos dados, e o pool SQL sem servidor precisará de menos tempo e solicitações de armazenamento para lê-lo.
- Se uma consulta tiver como objetivo um único arquivo grande, dividi-lo em vários arquivos menores será vantajoso para você.
- Tente manter o tamanho do arquivo CSV abaixo de 10 GB.
- É preferível ter arquivos com o mesmo tamanho para um único caminho OPENROWSET ou um local de tabela externa.
- Particione seus dados armazenando partições em diferentes pastas ou nomes de arquivos, marque [usar as funções filename e filepath para partições específicas](#use-fileinfo-and-filepath-functions-to-target-specific-partitions).

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Use as funções fileinfo e filepath para segmentar partições específicas

Os dados geralmente são organizados em partições. Você pode instruir o pool SQL sem servidor a consultar pastas e arquivos específicos. Isso reduzirá o número de arquivos e a quantidade de dados que a consulta precisa ler e processar. 

Consequentemente, você obterá um melhor desempenho. Para obter mais informações, confira as funções [filename](query-data-storage.md#filename-function) e [filepath](query-data-storage.md#filepath-function) e exemplos sobre como [consultar arquivos específicos](query-specific-files.md).

Se os dados no armazenamento não estiverem particionados, considere fazê-lo para que você possa usar essas funções a fim de otimizar as consultas direcionadas para esses arquivos.

Ao [consultar Apache Spark particionadas para tabelas externas do Azure Synapse](develop-storage-files-spark-tables.md) do pool SQL sem servidor, a consulta será automaticamente direcionada somente aos arquivos necessários.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>Use o CETAS para aprimorar o desempenho e as junções de consulta

O [CETAS](develop-tables-cetas.md) é um dos recursos mais importantes disponíveis no pool SQL sem servidor. CETAS é uma operação paralela que cria metadados de tabela externa e exporta os resultados da consulta SELECT para um conjunto de arquivos em sua conta de armazenamento.

Você pode usar a operação CETAS para armazenar partes usadas com frequência de consultas, como tabelas de referência unidas, em um novo conjunto de arquivos. Posteriormente, você pode fazer as junções a esta única tabela externa em vez de repetir junções comuns em várias consultas. 

Como o CETAS gera arquivos parquet, as estatísticas serão criadas automaticamente quando a primeira consulta for direcionada a essa tabela externa e você terá um desempenho aprimorado.

### <a name="next-steps"></a>Próximas etapas

Se você precisar de informações não fornecidas neste artigo, use a função **Pesquisar documento** no lado esquerdo desta página para pesquisar todos os documentos do pool de SQL.  O [Microsoft Q&uma página de perguntas para o Azure Synapse Analytics](/answers/topics/azure-synapse-analytics.html) é um lugar para você fazer perguntas para outros usuários e para o grupo de produtos do Azure Synapse Analytics. Monitoramos ativamente esse fórum para garantir que suas perguntas sejam respondidas por outro usuário ou um de nós.  

Se você preferir fazer suas perguntas em Stack Overflow, também temos um fórum de [Stack Overflow do Azure Synapse Analytics](https://stackoverflow.com/questions/tagged/azure-sqldw).
