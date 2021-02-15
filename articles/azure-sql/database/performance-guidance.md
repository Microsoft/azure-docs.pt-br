---
title: Diretrizes de ajuste de desempenho para aplicativos e bancos de dados
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Saiba mais sobre como ajustar bancos de dados e aplicativos de banco de dados para desempenho no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/10/2020
ms.openlocfilehash: 40657ad2f3b69d62e0e0d9c7d9e0f0be7343547b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96490594"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Ajustar aplicativos e bancos de dados para desempenho no banco de dados SQL do Azure e Azure SQL Instância Gerenciada
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Depois de identificar um problema de desempenho que você está enfrentando com o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada, este artigo foi criado para ajudá-lo a:

- Ajustar o aplicativo e aplicar algumas melhores práticas que podem melhorar o desempenho.
- Ajustar o banco de dados alterando índices e consultas para trabalhar com os dados de forma mais eficiente.

Este artigo pressupõe que você já tenha trabalhado por meio das recomendações do [supervisor](database-advisor-implement-performance-recommendations.md) de banco de dados SQL do Azure e das [recomendações de ajuste automático](automatic-tuning-overview.md)do banco de dados SQL do Azure, se aplicável. Também pressupõe que você tenha revisado [Uma visão geral de monitoramento e ajuste](monitor-tune-overview.md) e seus artigos relacionados relacionados à solução de problemas de desempenho. Além disso, este artigo pressupõe que você não tenha recursos de CPU, problemas de desempenho relacionados à execução que possam ser resolvidos aumentando o tamanho da computação ou a camada de serviço para fornecer mais recursos ao seu banco de dados.

## <a name="tune-your-application"></a>Ajustar seu aplicativo

No SQL Server local tradicional, o processo de planejamento de capacidade inicial frequentemente é separado do processo de executar um aplicativo em produção. Hardwares e licenças de produtos são comprados primeiro e o ajuste de desempenho é feito posteriormente. Ao usar o SQL do Azure, é uma boa ideia interajustar o processo de execução de um aplicativo e de seu ajuste. Com o modelo de pagamento por capacidade sob demanda, é possível ajustar o aplicativo para usar os recursos mínimos necessários atualmente, em vez de provisionamento excessivo em hardware com base em suposições de planos de crescimento futuros para um aplicativo, que geralmente estão incorretos. Alguns clientes podem optar por não ajustar um aplicativo e optar por provisionar recursos de hardware em excesso. Essa abordagem poderá ser uma boa ideia se você não quiser alterar um aplicativo importante durante um período movimentado. Mas, o ajuste de um aplicativo pode minimizar os requisitos de recursos e reduzir as contas mensais quando você usa as camadas de serviço no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada.

### <a name="application-characteristics"></a>Características do aplicativo

Embora o banco de dados SQL do Azure e as camadas de serviço do Azure SQL Instância Gerenciada sejam projetados para melhorar a estabilidade e a previsibilidade do desempenho de um aplicativo, algumas práticas recomendadas podem ajudá-lo a ajustar seu aplicativo para aproveitar melhor os recursos em um tamanho de computação. Embora vários aplicativos apresentem ganhos de desempenho significativos simplesmente passando para um tamanho da computação ou camada de serviço superior, outros precisam de ajustes adicionais para se beneficiarem de um nível de serviço mais elevado. Para melhorar o desempenho, considere realizar ajustes adicionais em aplicativos que têm estas características:

- **Aplicativos com desempenho lento devido ao comportamento "informativo"**

  Aplicativos barulhentos geram um excesso de operações de acesso a dados sensíveis à latência de rede. Talvez seja necessário modificar esses tipos de aplicativos para reduzir o número de operações de acesso a dados para o banco de dado. Por exemplo, você pode melhorar o desempenho do aplicativo usando técnicas como o envio em lote de consultas ad hoc ou a movimentação das consultas para procedimentos armazenados. Para obter mais informações, consulte [Consultas em lote](#batch-queries).

- **Bancos de dados com uma carga de trabalho intensiva que não tem suporte em um único computador inteiro**

   Bancos de dados que ultrapassam os recursos do maior tamanho da computação Premium podem se beneficiar de escalar horizontalmente a carga de trabalho. Para obter mais informações, consulte [Fragmentação entre banco de dados](#cross-database-sharding) e [Particionamento funcional](#functional-partitioning).

- **Aplicativos que têm consultas abaixo do ideal**

  Aplicativos que têm consultas mal ajustadas, especialmente aqueles na camada de acesso a dados, podem não se beneficiar de um tamanho maior da computação. Isso inclui consultas sem uma cláusula WHERE, com índices ausentes ou com estatísticas desatualizadas. Esses aplicativos se beneficiam de técnicas de ajuste de desempenho de consulta padrão. Para obter mais informações, consulte [Índices ausentes](#identifying-and-adding-missing-indexes) e [Ajuste e dicas de consulta](#query-tuning-and-hinting).

- **Aplicativos com design de acesso a dados abaixo do ideal**

   Aplicativos com problemas de simultaneidade de acesso a dados intrínsecos, por exemplo, deadlock, talvez não se beneficiem de um tamanho da computação maior. Considere a possibilidade de reduzir viagens de ida e volta no banco de dados, armazenando em cache no lado do cliente o serviço de cache do Azure ou outra tecnologia de cache. Consulte [Cache da camada de aplicativo](#application-tier-caching).

## <a name="tune-your-database"></a>Ajustar o banco de dados

Nesta seção, vamos examinar algumas técnicas que você pode usar para ajustar o banco de dados para obter o melhor desempenho para seu aplicativo e executá-lo com o menor tamanho de computação possível. Algumas dessas técnicas correspondem às práticas recomendadas de ajuste de SQL Server tradicionais, mas outras são específicas do banco de dados SQL do Azure e do SQL Instância Gerenciada do Azure. Em alguns casos, você pode examinar os recursos consumidos de um banco de dados para encontrar áreas para ajustar ainda mais e estender técnicas tradicionais de SQL Server para trabalhar no banco de dados SQL do Azure e no SQL Instância Gerenciada do Azure.

### <a name="identifying-and-adding-missing-indexes"></a>Identificando e adicionando índices ausentes

Um problema comum no desempenho do banco de dados OLTP está relacionado ao design do banco de dados físico. Com frequência, os esquemas de banco de dados são projetados e enviados sem testes em escala (seja na carga ou no volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em pequena escala, mas pode ser comprometido substancialmente diante de volumes de dados de nível de produção. A origem mais comum desse problema é a falta de índices apropriados para satisfazer filtros ou outras restrições em uma consulta. Geralmente, os índices ausentes se manifestam como uma verificação de tabela quando uma busca de índice poderia bastar.

Neste exemplo, o plano de consulta selecionado usa uma verificação, quando uma pesquisa seria suficiente:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Plano de consulta com índices ausentes](./media/performance-guidance/query_plan_missing_indexes.png)

O banco de dados SQL do Azure e o Azure SQL Instância Gerenciada podem ajudá-lo a encontrar e corrigir condições de índice ausentes comuns. As DMVs criadas no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada examinam as compilações de consulta nas quais um índice reduziria significativamente o custo estimado para executar uma consulta. Durante a execução da consulta, o mecanismo de banco de dados rastreia com que frequência cada plano de consulta é executado e controla a lacuna estimada entre o plano de consulta em execução e o imaginado em que o índice existia. Você pode usar essas DMVs para adivinhar rapidamente quais alterações de design do banco de dados físico podem melhorar o custo da carga de trabalho geral para um banco de dados e sua carga de trabalho real.

Você pode usar esta consulta para avaliar possíveis índices ausentes:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

Neste exemplo, a consulta resultou nesta sugestão:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Após a criação, a mesma instrução SELECT escolhe um plano diferente, que usa uma busca em vez de uma verificação e executa o plano de forma mais eficiente:

![Plano de consulta com índices corrigidos](./media/performance-guidance/query_plan_corrected_indexes.png)

A ideia principal é que a capacidade de E/S de um sistema de mercadoria compartilhado é mais limitada que a de um computador de servidor dedicado. Há um prêmio de minimizar a e/s desnecessária para tirar o máximo proveito do sistema nos recursos de cada tamanho de computação das camadas de serviço. Opções adequadas de design de banco de dados físico podem melhorar significativamente a latência de consultas individuais, melhorar a produtividade de solicitações simultâneas tratadas por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para obter mais informações sobre os DMVs de índice ausente, veja [sys.dm_db_missing_index_details](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql).

### <a name="query-tuning-and-hinting"></a>Ajuste e dicas de consulta

O otimizador de consulta no banco de dados SQL do Azure e o Azure SQL Instância Gerenciada é semelhante ao otimizador de consulta SQL Server tradicional. A maioria das práticas recomendadas para ajustar consultas e entender as limitações do modelo de raciocínio para o otimizador de consulta também se aplica ao banco de dados SQL do Azure e ao Azure SQL Instância Gerenciada. Se você ajustar as consultas no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada, poderá obter o benefício adicional de reduzir as demandas de recursos agregados. Seu aplicativo poderá ser executado com um custo menor do que um equivalente não ajustado, porque ele poderá ser executado em um tamanho da computação menor.

Um exemplo comum em SQL Server e que também se aplica ao banco de dados SQL do Azure e ao SQL do Azure Instância Gerenciada é como os parâmetros de "farejamentos" do otimizador de consulta. Durante a compilação, o otimizador de consulta avalia o valor atual de um parâmetro para determinar se ele pode gerar um plano de consulta melhor. Embora essa estratégia muitas vezes possa levar a um plano de consulta significativamente mais rápido do que um plano compilado sem valores de parâmetro conhecidos, atualmente ele funciona de forma inperfeita em SQL Server, no banco de dados SQL do Azure e no SQL Instância Gerenciada do Azure. Algumas vezes, o parâmetro não é detectado, e algumas vezes é detectado mas o plano gerado está abaixo do ideal para o conjunto completo de valores de parâmetro em uma carga de trabalho. A Microsoft inclui dicas (diretivas) de consulta para que você possa especificar a intenção mais deliberadamente e substituir o comportamento padrão da detecção de parâmetro. Geralmente, se você usar dicas, poderá corrigir casos em que o SQL Server padrão, o banco de dados SQL do Azure e o comportamento do SQL Instância Gerenciada do Azure são imperfeitos para uma carga de trabalho específica do cliente.

O exemplo a seguir demonstra como o processador de consultas pode gerar um plano que está abaixo do ideal para os requisitos de desempenho e recursos. Este exemplo também mostra que, se você usar uma dica de consulta, poderá reduzir o tempo de execução de consulta e os requisitos de recursos para seu banco de dados:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

O código de configuração cria uma tabela que contém a distribuição de dados distorcida. O plano de consulta ideal varia dependendo do parâmetro selecionado. Infelizmente, o comportamento do cache de plano nem sempre recompila a consulta com base no valor do parâmetro mais comum. Portanto, é possível que um plano de qualidade inferior seja armazenado em cache e usado para vários valores, mesmo quando um plano diferente poderia ser uma opção melhor de modo geral. Em seguida, o plano de consulta cria dois procedimentos armazenados idênticos, com exceção de um que tem uma dica de consulta especial.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

É recomendável que você aguarde pelo menos 10 minutos antes de começar a parte 2 do exemplo para que os resultados sejam distintos nos dados de telemetria resultantes.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Cada parte deste exemplo tenta executar uma instrução insert com parâmetros 1.000 vezes (para gerar uma carga suficiente para usar como um conjunto de dados de teste). Ao executar procedimentos armazenados, o processador de consultas examina o valor do parâmetro passado ao procedimento durante a primeira compilação ("detecção" de parâmetros). O processador armazena em cache o plano resultante e o usa para invocações posteriores, mesmo que o valor do parâmetro seja diferente. É possível que o plano ideal não seja usado em todos os casos. Às vezes, você precisa orientar o otimizador a escolher um plano que seja melhor para o caso médio em vez do caso específico de quando a consulta foi compilada pela primeira vez. Neste exemplo, o plano inicial gera um plano de "verificação" que lê todas as linhas para localizar cada valor que corresponda ao parâmetro:

![Ajuste de consulta usando um plano de verificação](./media/performance-guidance/query_tuning_1.png)

Como executamos o procedimento usando o valor 1, o plano resultante era ideal para o valor 1, mas de qualidade inferior para todos os outros valores da tabela. O resultado provavelmente não é o que você desejaria se fosse escolher cada plano aleatoriamente, porque o plano é mais lento e usa mais recursos.

Se você executar o teste com `SET STATISTICS IO` definido como `ON`, o trabalho de verificação lógica neste exemplo será feito em segundo plano. Você pode ver que há 1.148 leituras feitas pelo plano (o que não será eficiente, se o caso médio retornar apenas uma linha):

![Ajuste de consulta usando uma verificação lógica](./media/performance-guidance/query_tuning_2.png)

A segunda parte do exemplo usa uma dica de consulta para dizer ao otimizador para usar um valor específico durante o processo de compilação. Nesse caso, ele força o processador de consulta a ignorar o valor que é passado como parâmetro, e em vez disso pressupõe `UNKNOWN`. Isso se refere a um valor que tem a frequência média na tabela (ignorando a distorção). O plano resultante é um plano baseado em busca que é mais rápido e usa menos recursos, em média, do que o plano da parte 1 do exemplo:

![Ajuste de consulta usando uma dica de consulta](./media/performance-guidance/query_tuning_3.png)

Você pode ver o efeito na tabela **sys.resource_stats** (há um atraso entre o momento em que você executa o teste e quando os dados populam a tabela). Para este exemplo, a parte 1 foi executada durante a janela de tempo 22:25:00 e a parte 2 foi executada em 22:35:00. A janela de tempo anterior usou mais recursos do que a mais recente (devido a melhorias na eficiência do plano).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Exemplos de resultados de ajustes de consulta](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> Embora o volume neste exemplo seja propositalmente pequeno, o efeito de parâmetros de qualidade inferior pode ser significativo, especialmente em bancos de dados maiores. A diferença, em casos extremos, pode ficar entre segundos, para casos rápidos, e horas, para casos lentos.

Você pode examinar **sys.resource_stats** para determinar se o recurso para um teste usa mais ou menos recursos do que outro teste. Ao comparar dados, separe os testes por tempo de modo que eles não fiquem na mesma janela de tempo de 5 minutos na exibição **sys.resource_stats**. O objetivo deste exercício é minimizar a quantidade total de recursos usados, não minimizar os recursos de pico. Em geral, a otimização de uma parte do código de latência também reduz o consumo de recursos. Certifique-se de que as alterações feitas em um aplicativo sejam necessárias e de que elas não afetem negativamente a experiência do cliente para alguém que possa estar usando dicas de consulta no aplicativo.

Se uma carga de trabalho tem um conjunto de consultas repetidas, normalmente faz sentido capturar e validar a natureza ideal dessas opções de plano, pois ela orienta a unidade de tamanho mínima de recursos necessária para hospedar o banco de dados. Depois de validar, volte a examinar os planos periodicamente para ter certeza de que eles não degradaram. Saiba mais sobre [dicas de consulta (Transact-SQL)](/sql/t-sql/queries/hints-transact-sql-query).

### <a name="very-large-database-architectures"></a>Arquiteturas de banco de dados muito grandes

Antes do lançamento da camada de serviço de [hiperescala](service-tier-hyperscale.md) para bancos de dados individuais no banco de dados SQL do Azure, os clientes usaram para atingir os limites de capacidade para bancos de dados individual. Esses limites de capacidade ainda existem para bancos de dados em pool em pools elásticos do banco de dados SQL do Azure e bancos de dados de instância em instâncias gerenciadas do SQL do Azure. As duas seções a seguir discutem duas opções para a solução de problemas com bancos de dados muito grandes no Azure SQL Database e no Azure SQL Instância Gerenciada quando você não pode usar a camada de serviço de hiperescala.

### <a name="cross-database-sharding"></a>Fragmentação entre bancos de dados

Como o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada são executados em hardware de mercadoria, os limites de capacidade para um banco de dados individual são menores do que para uma instalação de SQL Server local tradicional. Alguns clientes usam técnicas de fragmentação para espalhar operações de banco de dados em vários bancos de dados quando as operações não se ajustam dentro dos limites de um banco de dados individual no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada. A maioria dos clientes que usam técnicas de fragmentação no banco de dados SQL do Azure e no SQL do Azure Instância Gerenciada dividir os seus dados em uma única dimensão entre vários bancos de dados. Para esta abordagem, você precisa compreender que aplicativos OLTP executam com frequência transações que se aplicam somente a uma linha ou a um pequeno grupo de linhas no esquema.

> [!NOTE]
> O banco de dados SQL do Azure agora fornece uma biblioteca para auxiliar na fragmentação. Para saber mais, confira [Visão geral da biblioteca de cliente de Banco de Dados Elástico](elastic-database-client-library.md).

Por exemplo, se um banco de dados contiver o nome do cliente, o pedido e os detalhes do pedido (como o exemplo tradicional da Northwind fornecido no SQL Server), você poderá dividir esses dados em vários bancos de dados agrupando um cliente com o pedido relacionado e as informações de detalhes do pedido. É possível garantir que os dados do cliente permaneçam em um banco de dados individual. O aplicativo dividiria clientes diferentes entre os bancos de dados, distribuindo efetivamente a carga entre vários bancos de dados. Com a fragmentação, os clientes não só podem evitar o limite máximo de tamanho do banco de dados, mas o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada também podem processar cargas de trabalho significativamente maiores do que os limites dos diferentes tamanhos de computação, desde que cada banco de dados individual caiba em seus limites de camada de serviço.

Embora a fragmentação do banco de dados não reduza a capacidade agregada de recursos de uma solução, ela é altamente eficaz para dar suporte a soluções muito grandes que são distribuídas em vários bancos de dados. Cada banco de dados pode ser executado em um tamanho da computação diferente para compatibilidade com bancos de dados “efetivos” muito grandes, com altos requisitos de recursos.

#### <a name="functional-partitioning"></a>Particionamento funcional

Os usuários geralmente combinam muitas funções em um banco de dados individual. Por exemplo, se um aplicativo tiver lógica para gerenciar o inventário de uma loja, esse banco de dados poderá ter lógica associada ao controle de inventário, acompanhamento de ordens de compra, procedimentos armazenados e exibições indexadas/materializadas que gerenciam os relatórios de final de mês. Essa técnica facilita a administração do banco de dados para operações como backup, mas também requer que você dimensione o hardware para lidar com a carga de pico em todas as funções de um aplicativo.

Se você usar uma arquitetura de expansão no banco de dados SQL do Azure e no Azure SQL Instância Gerenciada, é uma boa ideia dividir funções diferentes de um aplicativo em bancos de dados diferentes. Usando essa técnica, cada aplicativo é dimensionado de forma independente. À medida que um aplicativo se torna mais ocupado (e a carga no banco de dados aumenta), o administrador pode escolher tamanhos da computação independentes para cada função do aplicativo. No limite, com essa arquitetura, um aplicativo pode ficar maior do que a capacidade de um único computador de mercadoria porque a carga está distribuída por várias máquinas.

### <a name="batch-queries"></a>Consultas em lote

Para aplicativos que acessam dados usando consultas ad hoc, frequentes e de alto volume, uma quantidade significativa de tempo de resposta é gasta na comunicação de rede entre a camada de aplicativo e a camada de banco de dados. Mesmo quando o aplicativo e o banco de dados estão no mesmo data center, a latência de rede entre os dois pode ser ampliada por um grande número de operações de acesso a dados. Para reduzir as viagens de ida e volta da rede para as operações de acesso a dados, considere usar a opção de processar as consultas ad hoc em lote ou compilá-las como procedimentos armazenados. Se você enviar em lote as consultas ad hoc, poderá enviá-las com várias consultas como um lote grande em uma única viagem para o banco de dados. Se compilar consultas ad hoc em um procedimento armazenado, você poderá alcançar o mesmo resultado que teria processando-as em lote. O uso de um procedimento armazenado também oferece o benefício de aumentar as chances de armazenar em cache os planos de consulta no banco de dados para que você possa usar o procedimento armazenado novamente.

Alguns aplicativos apresentam gravação intensa. Às vezes, você pode reduzir a carga de E/S total em um banco de dados considerando como fazer as gravações em lote. Frequentemente, isso é tão simples quanto usar transações explícitas em vez de transações de confirmação automática em procedimentos armazenados e lotes ad hoc. Para obter uma avaliação das diferentes técnicas que você pode usar, consulte [técnicas de envio em lote para aplicativos de banco de dados no Azure](../performance-improve-use-batching.md). Teste sua própria carga de trabalho para encontrar o modelo certo para envio em lote. Tenha em mente que um modelo pode ter garantias de consistência transacional ligeiramente diferentes. Para encontrar a carga de trabalho certa que minimiza o uso de recursos, é necessário encontrar a combinação certa de compensações de consistência e de desempenho.

### <a name="application-tier-caching"></a>Cache de camada de aplicativo

Alguns aplicativos de banco de dados têm cargas de trabalho de leitura pesada. As camadas de cache podem reduzir a carga no banco de dados e podem reduzir potencialmente o tamanho de computação necessário para dar suporte a um banco de dados usando o banco de dados SQL do Azure e o Azure SQL Instância Gerenciada. Com o [cache do Azure para Redis](https://azure.microsoft.com/services/cache/), se você tiver uma carga de trabalho com muita leitura, poderá ler os dados uma vez (ou talvez uma vez por computador da camada de aplicativo, dependendo de como ele está configurado) e, em seguida, armazenar esses dados fora do seu banco. Esta é uma forma de reduzir a carga do banco de dados (CPU e E/S de leitura), mas há um impacto na consistência transacional, porque os dados lidos do cache podem estar fora de sincronia com os dados no banco de dados. Embora em muitos aplicativos algum nível de inconsistência seja aceitável, isso não vale para todas as cargas de trabalho. É necessário compreender totalmente todos os requisitos do aplicativo antes de implementar uma estratégia de cache da camada do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as camadas de serviço baseadas em DTU, consulte [modelo de compra baseado em DTU](service-tiers-dtu.md).
- Para saber mais sobre as camadas de serviço baseadas em vCore, consulte [modelo de compra baseado em vCore](service-tiers-vcore.md).
- Para saber mais sobre pools elásticos, consulte [O que é um pool elástico do Azure?](elastic-pool-overview.md)
- Para obter informações sobre desempenho e pools elásticos, consulte [Quando considerar um pool elástico](elastic-pool-overview.md)