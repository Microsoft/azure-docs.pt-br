---
title: Ajuste de desempenho com exibições materializadas
description: Recomendações e considerações para exibições materializadas para melhorar o desempenho da consulta.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: d10b7084cfc49d60e9d14c3c857d1ade839398ac
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305110"
---
# <a name="performance-tuning-with-materialized-views-using-dedicated-sql-pool-in-azure-synapse-analytics"></a>Ajuste de desempenho com exibições materializadas usando o pool SQL dedicado no Azure Synapse Analytics

No pool SQL dedicado, as exibições materializadas fornecem um método de manutenção baixo para consultas analíticas complexas para obter um desempenho rápido sem nenhuma alteração de consulta. Este artigo discute as orientações gerais sobre o uso de exibições materializadas.

## <a name="materialized-views-vs-standard-views"></a>Exibições materializadas vs. exibições padrão

O pool de SQL é compatível com exibições padrão e materializadas.  Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas às consultas como tabelas lógicas.  As exibições revelam a complexidade da computação de dados comum e adicionam uma camada de abstração às alterações de computação para que não seja necessário regravar consultas.  

Uma exibição padrão calcula os dados sempre que a exibição é usada.  Não há dados armazenados no disco. Normalmente, as pessoas usam exibições padrão como uma ferramenta que ajuda a organizar os objetos lógicos e as consultas em um banco de dados.  Para usar a exibição padrão, uma consulta precisa fazer referência direta a ela.

Uma exibição materializada computa previamente, armazena e mantém seus dados no pool SQL dedicado, assim como uma tabela.  A recomputação não é necessária sempre que uma exibição materializada é usada.  É por isso que as consultas que usam todos ou um subconjunto dos dados em exibições materializadas podem obter desempenho mais rápido.  Melhor ainda, as consultas podem usar uma exibição materializada sem fazer referência direta a ela. Portanto, não há necessidade de alterar o código do aplicativo.  

A maioria dos requisitos de exibição padrão ainda se aplica a uma exibição materializada. Para obter detalhes sobre a sintaxe de exibição materializada e outros requisitos, consulte [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

| Comparação                     | Visualizar                                         | Exibição Materializada
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Exibir definição                 | Armazenado no data warehouse do Azure.              | Armazenado no data warehouse do Azure.
|Exibir conteúdo                    | Gerado sempre que a exibição é usada.   | Pré-processado e armazenado no data warehouse do Azure durante a criação da exibição. Atualizado conforme os dados são adicionados às tabelas subjacentes.
|Atualização dedados                    | Sempre atualizado                               | Sempre atualizado
|Velocidade para recuperar dados de exibição de consultas complexas     | Lento                                         | Rápido  
|Armazenamento extra                   | Não                                           | Sim
|Sintaxe                          | CREATE VIEW                                  | CREATE MATERIALIZED VIEW AS SELECT

## <a name="benefits-of-materialized-views"></a>Benefícios de exibições materializadas

Uma exibição materializada projetada adequadamente fornece os seguintes benefícios:

- Tempo de execução reduzido para consultas complexas com JOINs e funções de agregação. Quanto mais complexa a consulta, maior o potencial de economia no tempo de execução. O maior benefício é obtido quando o custo de computação de uma consulta é alto e o conjunto de dados resultante é pequeno.  

- O otimizador de consulta no pool do SQL dedicado pode usar automaticamente exibições materializadas implantadas para melhorar os planos de execução de consulta.  Esse processo é transparente para os usuários, oferecendo desempenho de consulta mais rápido e não requer consultas para fazer referência direta às exibições materializadas.

- Requer pouca manutenção nas exibições.  Uma exibição materializada armazena dados em dois locais, um índice columnstore clusterizado para os dados iniciais no momento da criação da exibição e um armazenamento delta para as alterações de dados incrementais.  Todas as alterações de dados das tabelas base são adicionadas automaticamente ao armazenamento delta de maneira síncrona.  Um processo em segundo plano (motor de tupla) move periodicamente os dados do armazenamento delta para o índice columnstore da exibição.  Esse design permite que a consulta de exibições materializadas retorne os mesmos dados que a consulta direta das tabelas base.
- Os dados em uma exibição materializada podem ser distribuídos de maneira diferente das tabelas base.  
- Os dados em exibições materializadas obtêm os mesmos benefícios de alta disponibilidade e resiliência que os dados em tabelas regulares.  

Em comparação com outros provedores de data warehouse, as exibições materializadas implementadas no pool SQL dedicado também fornecem os seguintes benefícios adicionais:

- Atualização automática e síncrona de dados com alterações de dados nas tabelas base. Não é necessária nenhuma ação do usuário.
- Amplo suporte à função de agregação. Consulte [CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- O suporte para recomendação de exibição materializada específica de consulta.  Consulte [EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Cenários comuns  

As exibições materializadas normalmente são usadas nos seguintes cenários:

**Necessidade de melhorar o desempenho de consultas analíticas complexas em relação a dados grandes em tamanho**

As consultas analíticas complexas normalmente usam mais funções de agregação e junções de tabela, causando operações de computação mais pesadas, como embaralhamentos e junções na execução da consulta.  É por isso que essas consultas demoram mais para serem concluídas, principalmente em tabelas grandes.  

Os usuários podem criar exibições materializadas para os dados retornados dos cálculos comuns de consultas. Portanto, não há necessidade de recálculo quando esses dados são necessários para as consultas, permitindo menor custo de computação e resposta mais rápida às consultas.

**Necessidade de desempenho mais rápido, sem ou com mínimas alterações na consulta**

As alterações de esquema e consulta nos data warehouses geralmente são reduzidas ao mínimo para oferecer suporte a operações e relatórios regulares de ETL.  As pessoas podem usar exibições materializadas para ajuste do desempenho de consulta se o custo incorrido pelas exibições puder ser compensado pelo ganho no desempenho da consulta.

Em comparação com outras opções de ajuste, como gerenciamento de escala e dimensionamento, é uma mudança de produção muito menos impactante criar e manter uma exibição materializada e seu potencial ganho de desempenho também é maior.

- Criar ou manter exibições materializadas não afeta as consultas em execução nas tabelas base.
- O otimizador de consulta pode usar as exibições materializadas implantadas automaticamente sem referência direta à exibição em uma consulta. Esse recurso reduz a necessidade de alteração de consulta no ajuste do desempenho.

**Precisa de uma estratégia de distribuição de dados diferente para obter um desempenho de consulta mais rápido**

O data warehouse do Azure é um sistema de processamento paralelo e massivamente distribuído (MPP).  

O Synapse SQL é um sistema de consulta distribuída que permite que as empresas implementem cenários de data warehouse e de virtualização de dados usando experiências padrão de T-SQL conhecidas para os engenheiros de dados. Ele também expande as funcionalidades do SQL para lidar com cenários de streaming e aprendizado de máquina. Os dados em uma tabela do data warehouse são distribuídos entre 60 nós usando uma das três [estratégias de distribuição](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (hash, round_robin ou replicado).  

A distribuição de dados é especificada na hora da criação da tabela e permanece inalterada até que a tabela seja descartada. A exibição materializada, sendo uma tabela virtual em disco, é compatível com as distribuições de dados de hash e round_robin.  Os usuários podem escolher uma distribuição de dados diferente das tabelas base, mas ideal para o desempenho de consultas que usam as exibições frequentemente.  

## <a name="design-guidance"></a>Diretrizes de design

Aqui temos a orientação geral sobre o uso de exibições materializadas para melhorar o desempenho da consulta:

**Design para carga de trabalho**

Antes de começar a criar exibições materializadas, é importante ter uma compreensão profunda de sua carga de trabalho em termos de padrões de consulta, a importância, a frequência e o tamanho dos dados resultantes.  

Os usuários podem executar EXPLAIN WITH_RECOMMENDATIONS <SQL_statement> para as exibições materializadas recomendadas pelo otimizador de consulta.  Como essas recomendações são específicas à consulta, uma exibição materializada que beneficia uma única consulta pode não ser ideal para outras consultas na mesma carga de trabalho.  

Avalie essas recomendações com as necessidades da carga de trabalho em mente.  As exibições materializadas ideais são aquelas que beneficiam o desempenho da carga de trabalho.  

**Esteja atento à compensação entre consultas mais rápidas e o custo**

Para cada exibição materializada, há um custo de armazenamento de dados e um custo para manter a exibição.  À medida que os dados são alterados nas tabelas base, o tamanho da exibição materializada aumenta e sua estrutura física também muda.  

Para evitar a degradação do desempenho da consulta, cada exibição materializada é mantida separadamente pelo mecanismo da data warehouse, incluindo a movimentação de linhas do armazenamento delta para os segmentos de índice columnstore e a consolidação de alterações de dados.  

A carga de trabalho de manutenção aumenta mais quando o número de exibições materializadas e alterações na tabela base aumenta.   Os usuários devem verificar se o custo incorrido de todas as exibições materializadas pode ser compensado pelo ganho de desempenho da consulta.  

Você pode executar esta consulta para a lista de exibições materializadas em um banco de dados:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opções para reduzir o número de exibições materializadas:

- Identifique conjuntos de dados comuns usados frequentemente pelas consultas complexas em sua carga de trabalho.  Crie exibições materializadas para armazenar esses conjuntos de dados para que o otimizador possa usá-los como blocos de construção quando for criar planos de execução.  

- Descarte as exibições materializadas com pouco uso ou que não são mais necessárias.  Uma exibição materializada desabilitada não é mantida, mas incorre em custo de armazenamento mesmo assim.  

- Combine visualizações materializadas criadas nas tabelas base iguais ou semelhantes, mesmo que seus dados não se sobreponham.  A combinação de exibições materializadas pode resultar em uma exibição maior do que a soma das exibições separadas. No entanto, o custo da manutenção da exibição deve ser reduzido.  Por exemplo:

```sql
-- Query 1 would benefit from having a materialized view created with this SELECT statement
SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement
SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form
SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Nem todo ajuste de desempenho exige alteração de consulta**

O otimizador de data warehouse pode usar automaticamente as exibições materializadas implantadas para melhorar o desempenho da consulta.  Esse suporte é aplicado de forma transparente a consultas que não fazem referência às exibições e consultas que usam agregações sem suporte na criação de exibições materializadas.  Nenhuma alteração de consulta é necessária. É possível verificar o plano de execução estimado de uma consulta para confirmar se uma exibição materializada é usada.  

**Monitorar exibições materializadas**

Uma exibição materializada é armazenada no data warehouse assim como uma tabela com o CCI (índice columnstore clusterizado).  A leitura de dados de uma exibição materializada inclui a verificação do índice e a aplicação de alterações no armazenamento delta.  Quando o número de linhas no armazenamento delta é muito alto, resolver uma consulta de uma exibição materializada pode levar mais tempo do que consultar diretamente as tabelas base.  

Para evitar a degradação do desempenho da consulta, recomenda-se executar [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) para monitorar o overhead_ratio da exibição (total_rows/base_view_row).  Se a overhead_ratio for muito alta, considere recompilar a exibição materializada para que todas as linhas no armazenamento delta sejam movidas para o índice columnstore.  

**Cache de exibição materializada e conjunto de resultados**

Esses dois recursos são introduzidos no pool SQL dedicado ao mesmo tempo para o ajuste de desempenho de consulta. O cache do conjunto de resultados é usado para obter simultaneidade alta e tempos de resposta rápidos a partir de consultas repetitivas em dados estáticos.  

Para usar o resultado em cache, a forma da consulta de solicitação de cache deve corresponder à consulta que o cache produziu.  Além disso, o resultado em cache deve se aplicar à toda a consulta.  

Exibições materializadas permitem alterações de dados nas tabelas base.  Dados em exibições materializadas podem ser aplicados a uma parte de uma consulta.  Esse suporte permite que as mesmas exibições materializadas sejam usadas por diferentes consultas que compartilham alguma computação para um desempenho mais rápido.

## <a name="example"></a>Exemplo

Este exemplo usa uma consulta semelhante ao TPCDS que localiza clientes que gastam mais dinheiro em catálogo em vez de lojas. Ele também identifica os clientes preferenciais e seu país/região de origem.   A consulta envolve a seleção dos 100 principais registros da UNION de três instruções do sub-SELECT que envolva SUM() e GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Verifique o plano de execução estimado da consulta.  Há 18 operações aleatórias e 17 junções, que levam mais tempo para serem executadas. Agora vamos criar uma exibição materializada para cada uma das três instruções de sub-SELECT.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Verifique novamente o plano de execução da consulta original.  Agora o número de junções muda de 17 para 5 e não há mais ordem aleatória.  Selecione o ícone de operação de filtro no plano. Sua lista de saída mostra que os dados são lidos nas exibições materializadas em vez de nas tabelas base.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

Com as exibições materializadas, a mesma consulta é executada muito mais rapidamente sem qualquer alteração de código.  

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL do Synapse](develop-overview.md).
 