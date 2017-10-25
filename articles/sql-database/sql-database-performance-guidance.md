---
title: Diretrizes de ajuste de desempenho do Banco de Dados SQL do Azure | Microsoft Docs
description: "Este artigo pode ajudar você a escolher a camada de serviço para seu aplicativo. Nele, também recomendamos formas de ajustar seu aplicativo para aproveitar ao máximo o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: dd8d95fa-24b2-4233-b3f1-8e8952a7a22b
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 02/09/2017
ms.author: carlrab
ms.openlocfilehash: dc0244f0e0949b172c391825057f5c14893a5158
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tuning-performance-in-azure-sql-database"></a>Ajustando o desempenho no Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure fornece [recomendações](sql-database-advisor.md) que podem ser usadas para melhorar o desempenho do banco de dados ou você pode permitir que o Banco de Dados SQL do Azure [adapte-se ao seu aplicativo automaticamente](sql-database-automatic-tuning.md) e aplique as alterações que melhorarão o desempenho da carga de trabalho.

Se você não tiver nenhuma recomendação aplicável e ainda tiver problemas de desempenho, poderá usar os seguintes métodos para melhorar o desempenho:
1. Aumentar as [camadas de serviço](sql-database-service-tiers.md) e fornecer mais recursos para o banco de dados.
2. Ajustar o aplicativo e aplicar algumas melhores práticas que podem melhorar o desempenho. 
3. Ajustar o banco de dados alterando índices e consultas para trabalhar com os dados de forma mais eficiente.

Esses são métodos manuais, porque você precisa decidir quais [camadas de serviço](sql-database-service-tiers.md) serão escolhidas ou precisa reescrever o código do aplicativo ou do banco de dados e implantar as alterações.

## <a name="increasing-performance-tier-of-your-database"></a>Aumentando o nível de desempenho do banco de dados

O Banco de Dados SQL do Azure oferece quatro [camadas de serviço](sql-database-service-tiers.md) que estão à sua disposição: Básico, Standard, Premium e Premium RS (o desempenho é medido em unidades de produtividade do banco de dados ou [DTUs](sql-database-what-is-a-dtu.md). Cada camada de serviço isola os recursos que o banco de dados SQL pode usar e garante um desempenho previsível para o nível de serviço em questão. Neste artigo, oferecemos orientações que podem ajudá-lo a escolher a camada de serviço para seu aplicativo. Também abordamos de que formas você pode ajustar seu aplicativo para aproveitar ao máximo o Banco de Dados SQL do Azure.

> [!NOTE]
> Este artigo se concentra em fornecer orientações sobre o desempenho de bancos de dados únicos no Banco de Dados SQL do Azure. Para obter as diretrizes de desempenho relacionadas aos pools elásticos, consulte [Considerações de preço e desempenho para pools elásticos](sql-database-elastic-pool-guidance.md). No entanto, observe que você pode aplicar muitas das recomendações de ajuste neste artigo a bancos de dados em um pool elástico e obter benefícios de desempenho semelhantes.
> 

* **Básico**: a camada de serviço Básica oferece boa previsibilidade de desempenho para cada banco de dados, hora após hora. Em um banco de dados Básico, recursos suficientes dão suporte ao bom desempenho em um banco de dados pequeno que não tem várias solicitações simultâneas. Os casos de uso típicos em que você usará a camada de serviço Básica são:
  * **Você está apenas começando a usar o Banco de Dados SQL do Azure**. Aplicativos que estão em desenvolvimento normalmente não precisam de altos níveis de desempenho. Os bancos de dados Básicos são um ambiente ideal para o desenvolvimento ou teste de banco de dados, por uma faixa de preço reduzida.
  * **Você tem um banco de dados com um único usuário**. Aplicativos que associam um único usuário a um banco de dados normalmente não têm requisitos altos em termos de simultaneidade e desempenho. Esses aplicativos são candidatos à camada de serviço Básica.
* **Standard**: a camada de serviço Standard oferece melhor previsibilidade de desempenho e fornece bom desempenho para bancos de dados que têm várias solicitações simultâneas, como aplicativos Web e de grupo de trabalho. Quando escolhe um banco de dados da camada de serviço Standard, você pode dimensionar seu aplicativo de banco de dados com base em um desempenho previsível, minuto a minuto.
  * **Seu banco de dados tem várias solicitações simultâneas**. Aplicativos que atendem mais de um usuário por vez normalmente precisam de níveis de desempenho mais altos. Por exemplo, aplicativos Web ou de grupo de trabalho que têm requisitos de tráfego de E/S de baixo a médio e que dão suporte a várias consultas simultâneas são bons candidatos para a camada de serviço Standard.
* **Premium**: a camada de serviço Premium fornece um desempenho previsível, segundo após segundo, para cada banco de dados Premium. Quando escolhe a camada de serviço Premium, você pode dimensionar seu aplicativo de banco de dados com base na carga de pico desse banco de dados. O plano remove casos em que a variação de desempenho pode fazer com que pequenas consultas demorem mais do que o esperado em operações sensíveis à latência. Esse modelo pode simplificar muito os ciclos de desenvolvimento e de validação de produtos para aplicativos que precisam criar instruções fortes sobre as necessidades de recursos de pico, a variação de desempenho ou a latência da consulta. A maioria dos casos de uso da camada de serviço Premium tem uma ou mais destas características:
  * **Alta carga de pico**. Um aplicativo que exige muita CPU, memória ou entrada/saída (E/S) para concluir suas operações requer um nível de alto desempenho dedicado. Por exemplo, uma operação de banco de dados conhecida por consumir vários núcleos de CPU por um longo período é uma candidata para a camada de serviço Premium.
  * **Muitas solicitações simultâneas**. Alguns aplicativos de banco de dados atendem muitas solicitações simultâneas, por exemplo, ao servir um site com alto volume de tráfego. As camadas de serviço Básica e Standard limitam a quantidade de solicitações simultâneas por banco de dados. Aplicativos que exigem mais conexões precisariam escolher um tamanho de reserva apropriado para lidar com o número máximo de solicitações necessárias.
  * **Baixa latência**. Alguns aplicativos precisam garantir uma resposta do banco de dados em tempo mínimo. Se um procedimento armazenado específico for chamado como parte de uma operação de cliente mais ampla, poderá haver o requisito de ter um retorno daquela chamada em não mais do que 20 milissegundos, 99% do tempo. Esse tipo de aplicativo se beneficia da camada de serviço Premium para certificar-se de que a capacidade de computação exigida esteja disponível.
* **Premium RS**: a camada Premium RS foi projetada para cargas de trabalho com uso intensivo de E/S que não exigem garantias da mais alta disponibilidade. Exemplos de cargas de trabalho de alto desempenho ou uma carga de trabalho analítica onde o banco de dados não é o sistema de registro.

O nível de serviço necessário para seu banco de dados SQL depende dos requisitos de carga de pico para cada dimensão de recurso. Alguns aplicativos usam quantidades comuns de um recurso, mas têm requisitos significativos para outros recursos.

### <a name="service-tier-capabilities-and-limits"></a>Limites e recursos da camada de serviço

Em cada camada de serviço, você define o nível de desempenho, de modo que você tem a flexibilidade de pagar apenas pela capacidade de que precisa. Você pode [ajustar a capacidade](sql-database-service-tiers.md), aumentando ou reduzindo-a, conforme a carga de trabalho mudar. Por exemplo, se a carga de trabalho do banco de dados for intensa durante a temporada de compras de volta às aulas, você poderá aumentar o nível de desempenho do banco de dados durante um período definido, de julho a setembro. Você pode reduzi-lo quando sua temporada de pico terminar. Você pode minimizar o que pagará otimizando seu ambiente de nuvem para a periodicidade dos seus negócios. Esse modelo também funciona bem para ciclos de lançamento de produto de software. Uma equipe de teste pode alocar capacidade ao executar testes e liberar essa capacidade quando os testes forem concluídos. Em um modelo de solicitação de capacidade, você paga pela capacidade conforme precisa dela e evita gastos com recursos dedicados que raramente são usados.

### <a name="why-service-tiers"></a>Por que usar camadas de serviço?
Embora cada carga de trabalho de banco de dados possa ser diferente, a finalidade das camadas de serviço é fornecer previsibilidade de desempenho em vários níveis de desempenho. Clientes com requisitos de recursos de bancos de dados de grande escala podem trabalhar em um ambiente de computação mais dedicado.

## <a name="tune-your-application"></a>Ajustar seu aplicativo
No SQL Server local tradicional, o processo de planejamento de capacidade inicial frequentemente é separado do processo de executar um aplicativo em produção. Hardwares e licenças de produtos são comprados primeiro e o ajuste de desempenho é feito posteriormente. Quando você usa o Banco de Dados SQL do Azure, é uma boa ideia integrar o processo de execução de um aplicativo e de seu ajuste. Com o modelo de pagamento pela capacidade sob demanda, você pode ajustar seu aplicativo para usar os recursos mínimos necessários no momento, em vez de provisionar excessivamente o hardware com base em palpites sobre os planos de crescimento futuro de um aplicativo, que frequentemente estão incorretos. Alguns clientes podem optar por não ajustar o aplicativo e, em vez disso, provisionar excessivamente os recursos de hardware. Essa abordagem poderá ser uma boa ideia se você não quiser alterar um aplicativo importante durante um período movimentado. Entretanto, ajustar o aplicativo pode minimizar os requisitos de recurso e diminuir as contas mensais quando você usar as camadas de serviço no Banco de Dados SQL do Azure.

### <a name="application-characteristics"></a>Características do aplicativo
Embora as camadas de serviço do Banco de Dados SQL do Azure sejam projetadas para melhorar a estabilidade e a previsibilidade de desempenho de um aplicativo, algumas práticas recomendadas podem ajudá-lo a ajustar seu aplicativo para aproveitar melhor os recursos dentro de um nível de desempenho. Embora vários aplicativos tenham ganhos de desempenho significativos simplesmente passando para um nível de desempenho e/ou camada de serviço superior, nem todos os aplicativos precisam de ajuste adicional para se beneficiar de um nível de serviço mais elevado. Para melhorar o desempenho, considere realizar ajustes adicionais em aplicativos que têm estas características:

* **Aplicativos que têm desempenho lento devido a um comportamento "barulhento"**. Aplicativos barulhentos geram um excesso de operações de acesso a dados sensíveis à latência de rede. Pode ser necessário modificar esses tipos de aplicativos para reduzir o número de operações de acesso a dados no banco de dados SQL. Por exemplo, você pode melhorar o desempenho do aplicativo usando técnicas como o envio em lote de consultas ad hoc ou a movimentação das consultas em procedimentos armazenados. Para obter mais informações, consulte [Consultas em lote](#batch-queries).
* **Bancos de dados com uma carga de trabalho intensiva que não podem ter suporte de um único computador inteiro**. Bancos de dados que ultrapassam os recursos do nível mais alto de desempenho Premium podem se beneficiar de escalar horizontalmente a carga de trabalho. Para obter mais informações, consulte [Fragmentação entre banco de dados](#cross-database-sharding) e [Particionamento funcional](#functional-partitioning).
* **Aplicativos com consultas de qualidade inferior**. Aplicativos que têm consultas mal ajustadas, especialmente aqueles na camada de acesso a dados, podem não se beneficiar de um nível de desempenho superior. Isso inclui consultas sem uma cláusula WHERE, com índices ausentes ou com estatísticas desatualizadas. Esses aplicativos se beneficiam de técnicas de ajuste de desempenho de consulta padrão. Para obter mais informações, consulte [Índices ausentes](#identifying-and-adding-missing-indexes) e [Ajuste e dicas de consulta](#query-tuning-and-hinting).
* **Aplicativos com design de acesso a dados de qualidade inferior**. Aplicativos com problemas de simultaneidade de acesso a dados intrínsecos, por exemplo, deadlock, talvez não se beneficiem da seleção de um nível mais alto de desempenho. Considere reduzir as viagens de ida e volta ao Banco de Dados SQL do Azure armazenando em cache os dados no lado do cliente usando o serviço de Caching do Azure ou outras tecnologias de cache. Consulte [Cache da camada de aplicativo](#application-tier-caching).

## <a name="tune-your-database"></a>Ajustar o banco de dados
Nesta seção, vemos algumas técnicas que você pode usar para ajustar o Banco de Dados SQL do Azure para obter o melhor desempenho do seu aplicativo e executá-lo no menor nível de desempenho possível. Algumas dessas técnicas correspondem às práticas recomendadas tradicionais de ajuste do SQL Server, mas outras são específicas ao Banco de Dados SQL do Azure. Em alguns casos, você pode examinar os recursos consumidos por um banco de dados para localizar áreas para ajustar ainda mais, bem como ampliar técnicas tradicionais do SQL Server para que elas funcionem no Banco de Dados SQL do Azure.

### <a name="identify-performance-issues-using-azure-portal"></a>Identificar problemas de desempenho usando o portal do Azure
As ferramentas a seguir no Portal do Azure podem ajudar você a analisar e corrigir problemas de desempenho com seu Banco de Dados SQL:

* [Visão de Desempenho de Consulta](sql-database-query-performance.md)
* [Advisor do Banco de Dados SQL](sql-database-advisor.md)

O portal do Azure tem mais informações sobre essas ferramentas e como usá-las. Recomendamos que primeiro você experimente as ferramentas no portal do Azure a fim de diagnosticar e corrigir os problemas com mais eficiência. Recomendamos que você use as abordagens de ajuste que discutiremos a seguir, para índices ausentes e ajustes de consultas, em casos especiais.

Encontre mais informações sobre como identificar problemas no Banco de Dados SQL do Azure no artigo [Monitoramento de desempenho](sql-database-single-database-monitor.md).

### <a name="identifying-and-adding-missing-indexes"></a>Identificando e adicionando índices ausentes
Um problema comum no desempenho do banco de dados OLTP está relacionado ao design do banco de dados físico. Com frequência, os esquemas de banco de dados são projetados e enviados sem testes em escala (seja na carga ou no volume de dados). Infelizmente, o desempenho de um plano de consulta pode ser aceitável em pequena escala, mas pode ser comprometido substancialmente diante de volumes de dados de nível de produção. A origem mais comum desse problema é a falta de índices apropriados para satisfazer filtros ou outras restrições em uma consulta. Geralmente, os índices ausentes se manifestam como uma verificação de tabela quando uma busca de índice poderia bastar.

Neste exemplo, o plano de consulta selecionado usa uma verificação, quando uma pesquisa seria suficiente:

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

![Plano de consulta com índices ausentes](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

O Banco de Dados SQL do Azure pode ajudá-lo a localizar e corrigir condições comuns de índice ausente. DMVs incorporadas ao Banco de Dados SQL do Azure examinam compilações de consulta em que um índice reduziria significativamente o custo estimado para executar uma consulta. Durante a execução da consulta, o Banco de Dados SQL monitora com que frequência cada plano de consulta é executado, bem como a lacuna estimada entre o plano de consulta em execução e o plano imaginado, em que esse índice existia. Você pode usar essas DMVs para adivinhar rapidamente quais alterações de design do banco de dados físico podem melhorar o custo da carga de trabalho geral para um banco de dados e sua carga de trabalho real.

Você pode usar esta consulta para avaliar possíveis índices ausentes:

    SELECT CONVERT (varchar, getdate(), 126) AS runtime,
        mig.index_group_handle, mid.index_handle,
        CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
                (migs.user_seeks + migs.user_scans)) AS improvement_measure,
        'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
                  CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
                  (' + ISNULL (mid.equality_columns,'')
                  + CASE WHEN mid.equality_columns IS NOT NULL
                              AND mid.inequality_columns IS NOT NULL
                         THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '')
                  + ')'
                  + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement,
        migs.*,
        mid.database_id,
        mid.[object_id]
    FROM sys.dm_db_missing_index_groups AS mig
    INNER JOIN sys.dm_db_missing_index_group_stats AS migs
        ON migs.group_handle = mig.index_group_handle
    INNER JOIN sys.dm_db_missing_index_details AS mid
        ON mig.index_handle = mid.index_handle
    ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC

Neste exemplo, a consulta resultou nesta sugestão:

    CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  

Após a criação, a mesma instrução SELECT escolhe um plano diferente, que usa uma busca em vez de uma verificação e executa o plano de forma mais eficiente:

![Plano de consulta com índices corrigidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

A ideia principal é que a capacidade de E/S de um sistema de mercadorias compartilhado é mais limitada do que a de um computador de servidor dedicado. Há o bônus de minimizar a E/S desnecessária e, assim, tirar o máximo proveito do sistema na DTU de cada nível de desempenho das camadas de serviço no Banco de Dados SQL do Azure. Opções adequadas de design de banco de dados físico podem melhorar significativamente a latência de consultas individuais, melhorar a produtividade de solicitações simultâneas tratadas por unidade de escala e minimizar os custos necessários para satisfazer a consulta. Para obter mais informações sobre os DMVs de índice ausente, veja [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Ajuste e dicas de consulta
O otimizador de consulta do Banco de Dados SQL do Azure é semelhante ao otimizador de consulta do SQL Server tradicional. A maioria das práticas recomendadas para o ajuste de consultas e a compreensão das limitações do modelo de raciocínio para o otimizador de consulta também se aplicam ao Banco de Dados SQL do Azure. Se ajustar consultas no Banco de Dados SQL, você poderá obter o benefício adicional de reduzir as demandas de recursos agregados. Seu aplicativo poderá ser executado com um custo menor do que um equivalente não ajustado, porque poderá ser executado em um nível de desempenho inferior.

Um exemplo que é comum no SQL Server e que também se aplica ao Banco de Dados SQL do Azure é como o otimizador de consulta "detecta" parâmetros. Durante a compilação, o otimizador de consulta avalia o valor atual de um parâmetro para determinar se ele pode gerar um plano de consulta melhor. Embora muitas vezes essa estratégia possa levar a um plano de consulta significativamente mais rápido do que um plano compilado sem valores de parâmetro conhecidos, atualmente ele a funciona de modo imperfeito SQL Server e no Banco de Dados SQL do Azure. Algumas vezes, o parâmetro não é detectado, e algumas vezes é detectado mas o plano gerado está abaixo do ideal para o conjunto completo de valores de parâmetro em uma carga de trabalho. A Microsoft inclui dicas (diretivas) de consulta para que você possa especificar a intenção mais deliberadamente e substituir o comportamento padrão da detecção de parâmetro. Frequentemente, usando as dicas você consegue corrigir casos em que o comportamento padrão do Banco de Dados SQL do Azure ou do SQL Server é imperfeito para uma carga de trabalho específica do cliente.

O exemplo a seguir demonstra como o processador de consultas pode gerar um plano que está abaixo do ideal para os requisitos de desempenho e recursos. Este exemplo também mostra que, se usar uma dica de consulta, você poderá reduzir requisitos de recursos e tempo de execução da consulta para o banco de dados SQL:

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

O código de configuração cria uma tabela que contém a distribuição de dados distorcida. O plano de consulta ideal varia dependendo do parâmetro selecionado. Infelizmente, o comportamento do cache de plano nem sempre recompila a consulta com base no valor do parâmetro mais comum. Portanto, é possível que um plano de qualidade inferior seja armazenado em cache e usado para vários valores, mesmo quando um plano diferente poderia ser uma opção melhor de modo geral. Em seguida, o plano de consulta cria dois procedimentos armazenados idênticos, com exceção de um que tem uma dica de consulta especial.

**Exemplo, parte 1**

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

**Exemplo, parte 2**

(Recomendamos que você espere pelo menos 10 minutos antes de começar a parte 2 do exemplo, para que os resultados sejam diferentes nos dados de telemetria resultantes).

    EXEC psp2 @param2=1;
    TRUNCATE TABLE t1;

    DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END

Cada parte deste exemplo tenta executar uma instrução insert com parâmetros 1.000 vezes (para gerar uma carga suficiente para usar como um conjunto de dados de teste). Ao executar procedimentos armazenados, o processador de consultas examina o valor do parâmetro passado ao procedimento durante a primeira compilação ("detecção" de parâmetros). O processador armazena em cache o plano resultante e o usa para invocações posteriores, mesmo que o valor do parâmetro seja diferente. É possível que o plano ideal não seja usado em todos os casos. Às vezes, você precisa orientar o otimizador a escolher um plano que seja melhor para o caso médio em vez do caso específico de quando a consulta foi compilada pela primeira vez. Neste exemplo, o plano inicial gera um plano de "verificação" que lê todas as linhas para localizar cada valor que corresponda ao parâmetro:

![Ajuste de consulta usando um plano de verificação](./media/sql-database-performance-guidance/query_tuning_1.png)

Como executamos o procedimento usando o valor 1, o plano resultante era ideal para o valor 1, mas de qualidade inferior para todos os outros valores da tabela. O resultado provavelmente não é o que você desejaria se fosse escolher cada plano aleatoriamente, porque o plano é mais lento e usa mais recursos.

Se você executar o teste com `SET STATISTICS IO` definido como `ON`, o trabalho de verificação lógica neste exemplo será feito em segundo plano. Você pode ver que há 1.148 leituras feitas pelo plano (o que não será eficiente, se o caso médio retornar apenas uma linha):

![Ajuste de consulta usando uma verificação lógica](./media/sql-database-performance-guidance/query_tuning_2.png)

A segunda parte do exemplo usa uma dica de consulta para dizer ao otimizador para usar um valor específico durante o processo de compilação. Nesse caso, ele força o processador de consulta a ignorar o valor que é passado como parâmetro, e em vez disso pressupõe `UNKNOWN`. Isso se refere a um valor que tem a frequência média na tabela (ignorando a distorção). O plano resultante é um plano baseado em busca que é mais rápido e usa menos recursos, em média, do que o plano da parte 1 do exemplo:

![Ajuste de consulta usando uma dica de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Você pode ver o efeito na tabela **sys.resource_stats** (há um atraso entre o momento em que você executa o teste e quando os dados populam a tabela). Para este exemplo, a parte 1 foi executada durante a janela de tempo 22:25:00 e a parte 2 foi executada em 22:35:00. A janela de tempo anterior usou mais recursos do que a mais recente (devido a melhorias na eficiência do plano).

    SELECT TOP 1000 *
    FROM sys.resource_stats
    WHERE database_name = 'resource1'
    ORDER BY start_time DESC

![Exemplos de resultados de ajustes de consulta](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Embora o volume neste exemplo seja propositalmente pequeno, o efeito de parâmetros de qualidade inferior pode ser significativo, especialmente em bancos de dados maiores. A diferença, em casos extremos, pode ficar entre segundos, para casos rápidos, e horas, para casos lentos.
> 
> 

Você pode examinar **sys.resource_stats** para determinar se o recurso para um teste usa mais ou menos recursos do que outro teste. Ao comparar dados, separe os testes por tempo de modo que eles não fiquem na mesma janela de tempo de 5 minutos na exibição **sys.resource_stats**. O objetivo deste exercício é minimizar a quantidade total de recursos usados, não minimizar os recursos de pico. Em geral, a otimização de uma parte do código de latência também reduz o consumo de recursos. Certifique-se de que as alterações feitas em um aplicativo sejam necessárias e de que elas não afetem negativamente a experiência do cliente para alguém que possa estar usando dicas de consulta no aplicativo.

Se uma carga de trabalho tem um conjunto de consultas repetidas, normalmente faz sentido capturar e validar a natureza ideal dessas opções de plano, pois ela orienta a unidade de tamanho mínima de recursos necessária para hospedar o banco de dados. Depois de validar, volte a examinar os planos periodicamente para ter certeza de que eles não degradaram. Saiba mais sobre [dicas de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Fragmentação entre bancos de dados
Como o Banco de Dados SQL do Azure é executado no hardware de mercadoria, há limites inferiores de capacidade para um banco de dados individual em relação a uma instalação do SQL Server local tradicional. Alguns clientes usam técnicas de fragmentação para distribuir operações de banco de dados por vários bancos de dados quando elas não se ajustarem aos limites de um banco de dados individual no Banco de Dados SQL do Azure. A maioria dos clientes que usam técnicas de fragmentação no Banco de Dados SQL do Azure divide seus dados em uma única dimensão entre vários bancos de dados. Para esta abordagem, você precisa compreender que aplicativos OLTP executam com frequência transações que se aplicam somente a uma linha ou a um pequeno grupo de linhas no esquema.

> [!NOTE]
> Agora, o Banco de Dados SQL fornece uma biblioteca para auxiliar com a fragmentação. Para saber mais, confira [Visão geral da biblioteca de cliente de Banco de Dados Elástico](sql-database-elastic-database-client-library.md).
> 
> 

Por exemplo, se um banco de dados contiver o nome do cliente, o pedido e os detalhes do pedido (como o exemplo tradicional da Northwind fornecido no SQL Server), você poderá dividir esses dados em vários bancos de dados agrupando um cliente com o pedido relacionado e as informações de detalhes do pedido. Você pode garantir que os dados do cliente permaneçam em um banco de dados individual. O aplicativo dividiria clientes diferentes entre os bancos de dados, distribuindo efetivamente a carga entre vários bancos de dados. Com a fragmentação, não apenas os clientes podem evitar o limite de tamanho máximo do banco de dados, mas o Banco de Dados SQL do Azure também pode processar cargas de trabalho significativamente maiores do que os limites dos diferentes níveis de desempenho, desde que cada banco de dados individual se ajuste à sua DTU.

Embora a fragmentação do banco de dados não reduza a capacidade agregada de recursos de uma solução, ela é altamente eficaz para dar suporte a soluções muito grandes que são distribuídas em vários bancos de dados. Cada banco de dados pode ser executado em um nível de desempenho diferente para dar suporte a bancos de dados "efetivos" muito grandes, com altos requisitos de recursos.

### <a name="functional-partitioning"></a>Particionamento funcional
Com frequência, os usuários do SQL Server combinam muitas funções um banco de dados individual. Por exemplo, se um aplicativo tiver lógica para gerenciar o inventário de uma loja, esse banco de dados poderá ter lógica associada ao controle de inventário, acompanhamento de ordens de compra, procedimentos armazenados e exibições indexadas/materializadas que gerenciam os relatórios de final de mês. Essa técnica facilita a administração do banco de dados para operações como backup, mas também requer que você dimensione o hardware para lidar com a carga de pico em todas as funções de um aplicativo.

Se você usar uma arquitetura de escala horizontal no Banco de Dados SQL do Azure, será útil dividir funções diferentes de um aplicativo em bancos de dados diferentes. Usando essa técnica, cada aplicativo é dimensionado de forma independente. À medida que um aplicativo se torna mais ocupado (e a carga no banco de dados aumenta), o administrador pode escolher níveis de desempenho independentes para cada função do aplicativo. No limite, com essa arquitetura, um aplicativo pode ficar maior do que a capacidade de um único computador de mercadoria porque a carga está distribuída por várias máquinas.

### <a name="batch-queries"></a>Consultas em lote
Para aplicativos que acessam dados usando consultas ad-hoc frequentes de alto volume, uma grande parte do tempo de resposta é gasto na comunicação de rede entre a camada do aplicativo e a camada do Banco de Dados SQL. Mesmo quando o aplicativo e o Banco de Dados SQL do Azure estiverem no mesmo datacenter, a latência de rede entre os dois poderá ser aumentada por um grande número de operações de acesso a dados. Para reduzir as viagens de ida e volta da rede para as operações de acesso a dados, considere usar a opção de processar as consultas ad hoc em lote ou compilá-las como procedimentos armazenados. Se processar as consultas ad hoc, você poderá enviar várias consultas como um lote grande em uma única viagem ao Banco de Dados SQL do Azure. Se compilar consultas ad hoc em um procedimento armazenado, você poderá alcançar o mesmo resultado que teria processando-as em lote. O uso de um procedimento armazenado também oferece o benefício de aumentar as chances de armazenar em cache os planos de consulta no Banco de Dados SQL do Azure para usar o procedimento armazenado novamente.

Alguns aplicativos apresentam gravação intensa. Às vezes, você pode reduzir a carga de E/S total em um banco de dados considerando como fazer as gravações em lote. Frequentemente, isso é tão simples quanto usar transações explícitas em vez de transações de confirmação automática em procedimentos armazenados e lotes ad hoc. Para ver uma avaliação das diferentes técnicas que você pode usar, consulte [Técnicas de envio em lote para aplicativos do Banco de Dados SQL no Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Teste sua própria carga de trabalho para encontrar o modelo certo para envio em lote. Tenha em mente que um modelo pode ter garantias de consistência transacional ligeiramente diferentes. Para encontrar a carga de trabalho certa que minimiza o uso de recursos, é necessário encontrar a combinação certa de compensações de consistência e de desempenho.

### <a name="application-tier-caching"></a>Cache de camada de aplicativo
Alguns aplicativos de banco de dados têm cargas de trabalho de leitura pesada. Camadas de cache podem reduzir a carga no banco de dados e, potencialmente, reduzir o nível de desempenho necessário para dar suporte a um banco de dados usando o Banco de Dados SQL do Azure. Com o [Cache Redis do Azure](https://azure.microsoft.com/services/cache/), se tiver uma carga de trabalho de leitura pesada, você poderá ler os dados de uma vez (ou talvez uma vez por computador da camada de aplicativo, dependendo de como estiver configurado) e armazenar esses dados fora do banco de dados SQL. Esta é uma forma de reduzir a carga do banco de dados (CPU e E/S de leitura), mas há um impacto na consistência transacional, porque os dados lidos do cache podem estar fora de sincronia com os dados no banco de dados. Embora em muitos aplicativos algum nível de inconsistência seja aceitável, isso não vale para todas as cargas de trabalho. É necessário compreender totalmente todos os requisitos do aplicativo antes de implementar uma estratégia de cache da camada do aplicativo.

## <a name="next-steps"></a>Próximas etapas
* Para obter mais informações sobre as camadas de serviço, consulte [Opções e desempenho de Banco de Dados SQL](sql-database-service-tiers.md)
* Para saber mais sobre pools elásticos, consulte [O que é um pool elástico do Azure?](sql-database-elastic-pool.md)
* Para obter informações sobre desempenho e pools elásticos, consulte [Quando considerar um pool elástico](sql-database-elastic-pool-guidance.md)

