---
title: Classes de recursos para gerenciamento de carga de trabalho no Azure SQL Data Warehouse | Microsoft Docs
description: Diretrizes para usar classes de recursos para gerenciar a simultaneidade e computar recursos para consultas no SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 06/20/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 548271e888344eeb0d111c074153ef7492af5b33
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67595528"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Gerenciamento de carga de trabalho com classes de recursos no SQL Data Warehouse do Azure

Diretrizes para usar classes de recursos para gerenciar a memória e simultaneidade para consultas no SQL Data Warehouse do Azure.  

## <a name="what-is-workload-management"></a>O que é gerenciamento de carga de trabalho

Gerenciamento de carga de trabalho fornece a capacidade de otimizar o desempenho geral de todas as consultas. Uma carga de trabalho bem ajustada executa consultas e operações de carregamento com eficiência, independentemente de estarem intensivo de e/s ou computação intensiva. O SQL Data Warehouse fornece recursos de gerenciamento de carga de trabalho para ambientes de vários usuários. Um data warehouse não se destina a cargas de trabalho com vários locatários.

A capacidade de desempenho de um data warehouse é determinada pelo [unidades do data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Para exibir os limites de memória e simultaneidade para todos os perfis de desempenho, consulte [limites de memória e simultaneidade](memory-and-concurrency-limits.md).
- Para ajustar a capacidade de desempenho, você pode [expandir ou reduzir](quickstart-scale-compute-portal.md).

A capacidade de desempenho de uma consulta é determinada pela classe de recurso da consulta. O restante deste artigo explica o que são classes de recursos e como ajustá-las.

## <a name="what-are-resource-classes"></a>Quais são as classes de recursos

A capacidade de desempenho de uma consulta é determinada pela classe de recurso do usuário.  Classes de recursos são limites de recursos predeterminados no Azure SQL Data Warehouse que controlam recursos de computação e simultaneidade para execução da consulta. Classes de recursos podem ajudá-lo a gerenciar sua carga de trabalho definindo limites no número de consultas executadas simultaneamente e nos recursos de computação atribuídos a cada consulta.  Há uma compensação entre a memória e simultaneidade.

- Classes de recursos menores reduzem a memória máxima por consulta, mas aumentam a simultaneidade.
- Classes de recursos maiores aumentam a memória máxima por consulta, mas reduzem a simultaneidade.

Há dois tipos de classes de recursos:

- Classes de recursos estáticos, que são adequadas para aprimoramento de simultaneidade em um tamanho de conjunto de dados é fixa.
- Classes de recursos dinâmicos, são bem adequadas para conjuntos de dados que estão crescendo em tamanho e precisa aumentar o desempenho, como o nível de serviço é dimensionado.

As classes de recursos usam slots de simultaneidade para medir o consumo de recursos.  Os [slots de simultaneidade](#concurrency-slots) são explicados mais adiante neste artigo.

- Para exibir a utilização de recursos para as classes de recursos, consulte [Limites de memória e simultaneidade](memory-and-concurrency-limits.md#concurrency-maximums).
- Para ajustar a classe de recurso, você pode executar a consulta em um usuário diferente ou [alterar a associação da classe de recurso do usuário](#change-a-users-resource-class) atual.

### <a name="static-resource-classes"></a>Classes de recursos estáticos

Classes de recursos estáticos alocam a mesma quantidade de memória, independentemente do nível de serviço atual, que é medido em [unidades do data warehouse](what-is-a-data-warehouse-unit-dwu-cdwu.md). Já que as consultas obtêm a mesma alocação de memória, independentemente do nível de desempenho, [dimensionar o data warehouse](quickstart-scale-compute-portal.md) permite que mais consultas sejam executadas em uma classe de recurso.  Classes de recursos estáticos são ideais se o volume de dados é conhecido e constante.

As classes de recursos estáticos são implementadas com essas funções de banco de dados predefinidos:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Classes de recursos dinâmicos

Classes de recursos dinâmicos alocam uma quantidade variável de memória dependendo do nível de serviço atual. Embora as classes de recursos estáticos são benéficas para maior simultaneidade e volumes de dados estáticos, classes de recursos dinâmicos são mais adequados para um volume crescente ou variável de dados.  Quando você aumenta para um nível de serviço maior, as consultas automaticamente recebem mais memória.  

As classes de recursos dinâmicos são implementadas com essas funções de banco de dados predefinidos:

- smallrc
- mediumrc
- largerc
- xlargerc

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Classes de recursos dinâmicos do Gen2 são verdadeiramente dinâmicos

Ao aprofundar os detalhes das classes de recursos dinâmicos em Gen1, há alguns detalhes que adicionam complexidade adicional para entender seu comportamento:

**On Gen1**
- A classe de recursos smallrc opera com um modelo de memória fixo como uma classe de recurso estático.  Consultas smallrc não têm mais memória dinamicamente, pois o nível de serviço é aumentado. 
- Como alterar os níveis de serviço, a simultaneidade de consultas disponíveis pode ir para cima ou para baixo.
- Dimensionar os níveis de serviço não oferece uma alteração proporcional para a memória alocada para as mesmas classes de recursos.

**Em Gen2**, classes de recursos dinâmicos são realmente dinâmicas endereçamento pontos mencionados acima.  A nova regra é 3-10-22-70 para alocações de porcentagem de memória para classes de recursos pequeno médio-grande-extragrande, **independentemente do nível de serviço**.  A tabela abaixo mostra os detalhes consolidados de porcentagens de alocação de memória e o número mínimo de consultas simultâneas que são executadas, independentemente do nível de serviço.

| Classe de recursos | Porcentagem de Memória | Mínimo de consultas simultâneas |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3%                | 32                     |
| mediumrc       | 10%               | 10                     |
| largerc        | 22%               | 4                      |
| xlargerc       | 70%               | 1                      |

### <a name="default-resource-class"></a>Classe de recurso padrão

Por padrão, cada usuário é um membro da classe de recursos dinâmicos **smallrc**.

A classe de recurso do administrador de serviços é fixada em smallrc e não pode ser alterada.  O administrador de serviços é o usuário criado durante o processo de provisionamento.  O administrador de serviços nesse contexto é o logon especificado para o "Logon de administrador do servidor" ao criar uma instância do SQL Data Warehouse com um novo servidor.

> [!NOTE]
> Usuários ou grupos definidos como administrador do Active Directory também são administradores de serviços.
>
>

## <a name="resource-class-operations"></a>Operações de classe de recurso

As classes de recursos são projetadas para melhorar o desempenho para atividades de gerenciamento e manipulação de dados. As consultas complexas também podem se beneficiar de ser executado em uma grande classe de recurso. Por exemplo, a consulta de desempenho para grandes junções e classificações podem aumentar quando a classe de recurso é grande o suficiente para habilitar a consulta a ser executada na memória.

### <a name="operations-governed-by-resource-classes"></a>Operações governadas por classes de recurso

Essas operações são governadas por classes de recurso:

- INSERT-SELECT, UPDATE, DELETE
- SELECT (ao consultar tabelas de usuário)
- ALTER INDEX - REBUILD ou REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- Carregamento de dados
- Operações de movimentação de dados realizadas pelo Serviço de Movimentação de Dados (DMS)

> [!NOTE]  
> Instruções SELECT nas DMVs (exibições de gerenciamento dinâmico) ou em outras exibições do sistema não são regidas por nenhum dos limites de simultaneidade. Você pode monitorar o sistema independentemente do número de consultas em execução nele.

### <a name="operations-not-governed-by-resource-classes"></a>Operações não governadas por classes de recurso

Algumas consultas sempre são executadas na classe de recurso smallrc, mesmo se o usuário for um membro de uma classe de recursos maior. Essas consultas isentas não são consideradas no limite de simultaneidade. Por exemplo, se o limite de simultaneidade for 16, muitos usuários poderão estar selecionando de exibições do sistema sem afetar os slots de simultaneidade disponíveis.

As instruções a seguir estão isentas das classes de recursos e sempre são executadas em smallrc:

- CREATE ou DROP TABLE
- ALTER TABLE ... SWITCH, SPLIT ou MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE, UPDATE ou DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE, ALTER ou DROP USER
- CREATE, ALTER ou DROP PROCEDURE
- CREATE ou DROP VIEW
- INSERT VALUES
- SELECT de exibições do sistema e DMVs
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Slots de simultaneidade

Slots de simultaneidade são uma maneira conveniente para controlar os recursos disponíveis para execução da consulta. Eles são como tíquetes que você compra para reservar assentos em um concerto, pois a capacidade é limitada. O número total de slots de simultaneidade por data warehouse é determinado pelo nível de serviço. Antes de iniciar a execução de uma consulta, ela deve ser capaz de reserva slots de simultaneidade suficientes. Quando uma consulta for concluída, ela libera seus slots de simultaneidade.  

- Uma consulta em execução com 10 slots de simultaneidade pode acessar 5 vezes mais recursos de computação que uma consulta em execução com 2 slots de simultaneidade.
- Se cada consulta exige 10 slots de simultaneidade e houver 40 slots de simultaneidade, então, apenas 4 consultas podem ser executados simultaneamente.

Apenas consultas governadas por recurso consomem slots de simultaneidade. Consultas de sistema e algumas consultas triviais não consomem nenhum slot. O número exato de slots de simultaneidade consumidos é determinado pela classe de recurso da consulta.

## <a name="view-the-resource-classes"></a>Exibir as classes de recursos

As classes de recursos são implementadas como funções de banco de dados predefinidos. Há dois tipos de classes de recursos: dinâmico e estático. Para exibir uma lista das classes de recursos, use a seguinte consulta:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Alterar uma classe de recursos de usuário

Classes de recursos são implementadas atribuindo usuários a funções de banco de dados. Quando um usuário executa uma consulta, a consulta é executada com a classe de recurso do usuário. Por exemplo, se um usuário for um membro da função de banco de dados de staticrc10, suas consultas são executadas com pequenas quantidades de memória. Se um usuário de banco de dados for um membro das funções de banco de dados xlargerc ou staticrc80, suas consultas são executadas com grandes quantidades de memória.

Para aumentar a classe de recurso do usuário, use [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) para adicionar o usuário a uma função de banco de dados de uma classe de recurso grande.  O código abaixo adiciona um usuário à função de banco de dados largerc.  Cada solicitação obtém 22% da memória do sistema.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Para diminuir a classe de recurso, use [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Se 'loaduser' não é um membro ou quaisquer outras classes de recursos, eles entram a classe de recurso smallrc padrão com uma concessão de memória de % 3.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Precedência de classe de recurso

Os usuários podem ser membros de várias classes de recursos. Quando um usuário pertence a mais de uma classe de recurso:

- Classes de recursos dinâmicos têm precedência sobre classes de recursos estáticos. Por exemplo, se um usuário for membro de mediumrc (dinâmico) e staticrc80 (estático), as consultas serão executadas com mediumrc.
- Classes de recursos maiores têm precedência sobre classes de recursos menores. Por exemplo, se um usuário for membro de mediumrc e largerc, as consultas serão executadas com largerc. Da mesma forma, se um usuário for membro de staticrc20 e statirc80, as consultas serão executadas com alocações de recursos staticrc80.

## <a name="recommendations"></a>Recomendações

Recomendamos a criação de um usuário que seja dedicado à execução de um tipo específico de consulta ou operação de carregamento. Ofereça uma classe de recurso permanente em vez de alterar a classe de recurso com frequência a esse usuário. Classes de recursos estáticos arcar com maior controle geral sobre a carga de trabalho, portanto, sugerimos o uso de classes de recursos estáticos antes de considerar classes de recursos dinâmicos.

### <a name="resource-classes-for-load-users"></a>Classes de recursos para usuários de carga

O `CREATE TABLE` usa índices columnstore clusterizados por padrão. A compactação de dados em um índice columnstore é uma operação de uso intensivo de memória e a pressão na memória pode reduzir a qualidade do índice. Pressão de memória pode levar à necessidade de uma classe de recurso maior ao carregar os dados. Para garantir que as cargas tenham memória suficiente, você pode criar um usuário que seja designado para que execute cargas e atribuir esse usuário a uma classe de recurso maior.

A memória necessária para processar cargas com eficiência depende da natureza da tabela carregada e do tamanho dos dados. Para obter mais informações sobre requisitos de memória, consulte [Maximizando a qualidade do grupo de linhas](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Depois de determinar o requisito de memória, escolha se deseja atribuir o usuário de carga a uma classe de recurso estático ou dinâmico.

- Use uma classe de recurso estático quando os requisitos de memória de tabela estiverem em um intervalo específico. As cargas são executadas com memória apropriada. Quando você dimensiona o data warehouse, as cargas não precisam de mais memória. Ao usar uma classe de recurso estático, as alocações de memória permanecem constantes. Essa consistência economiza a memória e permite que mais consultas sejam executadas simultaneamente. É recomendável que novas soluções usem as classes de recursos estáticos primeiro, pois eles fornecem maior controle.
- Use uma classe de recursos dinâmicos quando os requisitos de memória de tabela variarem muito. As cargas podem exigir mais memória do que a DWU atual ou que o nível de cDWU fornece. Dimensionar o data warehouse adiciona mais memória para operações de carregamento, o que permite que cargas tenham um desempenho mais rápido.

### <a name="resource-classes-for-queries"></a>Classes de recursos para consultas

Algumas consultas são computação intensiva e outros não.  

- Escolha uma classe de recursos dinâmicos quando consultas forem complexas, mas não precisam de alta simultaneidade.  Por exemplo, a geração de relatórios diários ou semanais é uma necessidade ocasional para recursos. Se os relatórios estiverem processando grandes quantidades de dados, dimensionar o data warehouse fornece mais memória à classe de recurso existente do usuário.
- Escolha uma classe de recurso estático quando as expectativas de recursos variarem ao longo do dia. Por exemplo, uma classe de recurso estático funciona bem quando o data warehouse é consultado por várias pessoas. Ao dimensionar o data warehouse, não altera a quantidade de memória alocada para o usuário. Consequentemente, mais consultas podem ser executadas em paralelo no sistema.

Concessões de memória apropriada dependem de muitos fatores, como a quantidade de dados consultada, a natureza dos esquemas de tabela e várias junções, selecionar e agrupar os predicados. Em geral, alocar mais memória permitirá que consultas sejam concluídas mais rapidamente, mas reduzirá a simultaneidade geral. Se a simultaneidade não for um problema, a alocação excessiva de memória não prejudicará a taxa de transferência.

Para ajustar o desempenho, use classes de recursos diferentes. A próxima seção fornece um procedimento armazenado que ajuda você a descobrir a melhor classe de recurso.

## <a name="example-code-for-finding-the-best-resource-class"></a>Exemplo de código para encontrar a melhor classe de recurso

Você pode usar o seguinte procedimento armazenado especificado para [Gen1](#stored-procedure-definition-for-gen1) ou [Gen2](#stored-procedure-definition-for-gen2)-descobrir simultaneidade e concessão de memória por classe de recurso em um determinado SLO e a melhor classe de recurso de memória intensiva CCI operações na tabela CCI não particionada em uma determinada classe de recursos:

Aqui está a finalidade deste procedimento armazenado:

1. Visualizar a simultaneidade e a concessão de memória por classe de recursos em um determinado SLO. O usuário precisa fornecer NULL para o esquema e tablename, conforme mostrado neste exemplo.  
2. Para ver a melhor classe de recurso para as operações de CCI intensivo de memória (carregar, copiar tabela, recompilar o índice, etc.) na tabela CCI não particionada em uma classe de recurso determinado. O procedimento armazenado usa o esquema da tabela para descobrir a concessão de memória necessária.

### <a name="dependencies--restrictions"></a>Dependências e restrições

- Esse procedimento armazenado não foi projetado para calcular os requisitos de memória para uma tabela cci particionada.
- Esse procedimento armazenado não considera os requisitos de memória em consideração para a parte SELECT de CTAS/INSERT-SELECT e pressupõe que sejam um SELECT.
- Esse procedimento armazenado usa uma tabela temporária que está disponível na sessão em que esse procedimento armazenado foi criado.
- Esse procedimento armazenado depende das ofertas atuais (por exemplo, configuração de hardware, configuração DMS) e se qualquer uma delas for alterada, em seguida, esse procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende das ofertas existentes de limite de simultaneidade e se eles alterar, em seguida, esse procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende das ofertas de classe de recursos existente e se eles alterar, em seguida, esse procedimento armazenado não funcionará corretamente.  

>[!NOTE]  
>Se você não estiver obtendo a saída após executar o procedimento armazenado com parâmetros fornecidos, então poderá ser dois casos.
>
>1. Um parâmetro de DW contém um valor inválido de SLO
>2. Ou, não há nenhuma classe de recurso correspondente para a operação CCI na tabela.
>
>Por exemplo, no DW100, a concessão de memória mais alta disponível é de 400 MB e se o esquema de tabela for grande o suficiente para atravessar o requisito de 400 MB.

### <a name="usage-example"></a>Exemplo de uso

Sintaxe:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Forneça um parâmetro NULL para extrair a DWU atual do BD do DW ou forneça uma DWU compatível na forma de 'DW100'
2. @SCHEMA_NAME: Forneça um nome de esquema da tabela
3. @TABLE_NAME: Forneça um nome de tabela dos juros

Exemplos executando esse procedimento armazenado:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

A instrução a seguir cria Table1 que é usada nos exemplos anteriores.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition-for-gen1"></a>Definição do procedimento armazenado para Gen1

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
                     WHEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000))
                     ELSE 1
              END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

### <a name="stored-procedure-definition-for-gen2"></a>Definição do procedimento armazenado para Gen2

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
  SELECT @DWU = 'DW'+CAST(Nodes*CASE WHEN CPUVer>6 THEN 500 ELSE 100 END AS VARCHAR(10))+CASE WHEN CPUVer>6 THEN 'c' ELSE '' END
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), CPUVer=max(i.cpu_count)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE'
         )A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW1000c', 32, 40, 1, 4, 8, 28, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500c', 32, 60, 1, 6, 13, 42, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000c', 48, 80, 2, 8, 17, 56, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW2500c', 48, 100, 3, 10, 22, 70, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000c', 64, 120, 3, 12, 26, 84, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW5000c', 64, 200, 6, 20, 44, 140, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW6000c', 128, 240, 7, 24, 52, 168, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW7500c', 128, 300, 9, 30, 66, 210, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW10000c', 128, 400, 12, 40, 88, 280, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW15000c', 128, 600, 18, 60, 132, 420, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW30000c', 128, 1200, 36, 120, 264, 840, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-step"></a>Próxima etapa

Para obter mais informações sobre como gerenciar usuários de banco de dados e segurança, confira [Proteger um banco de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como classes de recursos maiores podem melhorar a qualidade do índice columnstore clusterizado, consulte [Otimizações de memória para compressão de columnstore](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
