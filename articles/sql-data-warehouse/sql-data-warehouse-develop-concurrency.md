---
title: Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse | Microsoft Docs
description: "Compreender o gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: sqlmojo
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 08/23/2017
ms.author: joeyong;barbkess;kavithaj
ms.openlocfilehash: eaf2d43286dbaa52ada1430fbb7ce1e37f41c0d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="concurrency-and-workload-management-in-sql-data-warehouse"></a>Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse
Para oferecer um desempenho previsível em escala, o SQL Data Warehouse do Microsoft Azure ajuda a controlar os níveis de simultaneidade e as alocações de recursos como priorização de CPU e memória. Este artigo apresenta os conceitos de gerenciamento de simultaneidade e carga de trabalho, explicando como os dois recursos foram implementados e como controlá-los no data warehouse. O gerenciamento de carga de trabalho do SQL Data Warehouse destina-se a ajudá-lo a dar suporte a ambientes com vários usuários. Ele não se destina a cargas de trabalho com vários locatários.

## <a name="concurrency-limits"></a>Limites de simultaneidade
O SQL Data Warehouse permite até 1.024 conexões simultâneas. Todas as 1.024 conexões podem enviar consultas ao mesmo tempo. No entanto, para otimizar a taxa de transferência, o SQL Data Warehouse pode enfileirar algumas consultas para garantir que cada consulta receba uma concessão de memória mínima. O enfileiramento ocorre no tempo de execução de consulta. Ao enfileirar consultas quando os limites de simultaneidade são atingidos, o SQL Data Warehouse pode aumentar a taxa de transferência total, garantindo que as consultas ativas obtenham acesso aos recursos de memória muito necessários.  

Os limites de simultaneidade são regidos por dois conceitos: *consultas simultâneas* e *slots de simultaneidade*. Para a execução de uma consulta, ela deve ser executada tanto no limite de simultaneidade de consulta quanto na alocação de slot de simultaneidade.

* As consultas simultâneas são as consultas em execução ao mesmo tempo. O SQL Data Warehouse dá suporte a até 32 consultas simultâneas em tamanhos maiores de DWU.
* Os slots de simultaneidade são alocados com base em DWU. Cada DWU 100 fornece quatro slots de simultaneidade. Por exemplo, um DW100 aloca quatro slots de simultaneidade e um DW1000 aloca 40. Cada consulta consome um ou mais slots de simultaneidade, dependendo da [classe de recurso](#resource-classes) da consulta. As consultas em execução na classe de recurso smallrc consomem um slot de simultaneidade. As consultas em execução em uma classe de recurso superior consomem mais slots de simultaneidade.

A tabela a seguir descreve os limites das consultas simultâneas e dos slots de simultaneidade em vários tamanhos de DWU.

### <a name="concurrency-limits"></a>Limites de simultaneidade
| DWU | Máximo de consultas simultâneas | Slots de simultaneidade alocados |
|:--- |:---:|:---:|
| DW100 |4 |4 |
| DW200 |8 |8 |
| DW300 |12 |12 |
| DW400 |16 |16 |
| DW500 |20 |20 |
| DW600 |24 |24 |
| DW1000 |32 |40 |
| DW1200 |32 |48 |
| DW1500 |32 |60 |
| DW2000 |32 |80 |
| DW3000 |32 |120 |
| DW6000 |32 |240 |

Quando um desses limites for atingido, novas consultas serão enfileiradas e executadas em uma base de primeira a entrar, primeira a sair.  Conforme uma consulta for concluída e o número de consultas e slots ficar abaixo dos limites, as consultas em fila serão lançadas. 

> [!NOTE]  
> *Select* em execução exclusivamente nas DMVs (exibições de gerenciamento dinâmico) ou nas exibições de catálogo não são regidas por nenhum dos limites de simultaneidade. Você pode monitorar o sistema independentemente do número de consultas em execução nele.
> 
> 

## <a name="resource-classes"></a>Classes de recursos
As classes de recurso ajudam a controlar a alocação de memória e os ciclos de CPU fornecidos para uma consulta. Você pode atribuir dois tipos de classes de recurso para um usuário na forma de funções de banco de dados. Os dois tipos de classes de recurso são os seguintes:
1. Classes de recursos dinâmicos (**smallrc, mediumrc, largerc, xlargerc**) alocam uma quantidade variável de memória dependendo da DWU. Isso significa que quando você escala verticalmente para uma maior DWU, as consultas automaticamente recebem mais memória. 
2. Classes de recursos estáticos (**staticrc10, staticrc20, staticrc30, staticrc40, staticrc50, staticrc60, staticrc70, staticrc80**) alocam a mesma quantidade de memória, independentemente da DWU atual (desde que o DWU em si tenha memória suficiente). Isso significa que em DWUs maiores, você pode executar mais consultas em cada classe de recursos simultaneamente.

Os usuários em **smallrc** e **staticrc10** recebem uma quantidade menor de memória e podem tirar proveito da simultaneidade superior. Por outro lado, os usuários atribuídos a **xlargerc** ou **staticrc80** recebem grandes quantidades de memória e, assim, um número menor dessas consultas pode ser executado simultaneamente.

Por padrão, cada usuário é um membro da pequena classe de recursos, **smallrc**. O procedimento `sp_addrolemember` é usado para aumentar a classe de recurso e `sp_droprolemember` é usado para diminuir a classe de recurso. Por exemplo, este comando aumentaria a classe de recursos de loaduser para **largerc**:

```sql
EXEC sp_addrolemember 'largerc', 'loaduser'
```


### <a name="queries-that-do-not-honor-resource-classes"></a>Consultas que não consideram classes de recursos

Existem alguns tipos de consultas que não se beneficiam de uma alocação de memória maior. O sistema ignora a alocação de classe de recurso e sempre executa essas consultas na classe de recurso pequena. Se essas consultas são sempre executadas na classe de recurso pequena, elas podem ser executadas quando os slots de simultaneidade estão sob pressão e não consomem mais slots do que o necessário. Consulte as [exceções de classe de recurso](#query-exceptions-to-concurrency-limits) para obter mais informações.

## <a name="details-on-resource-class-assignment"></a>Detalhes sobre a atribuição de classe de recursos


Mais alguns detalhes sobre classes de recurso:

* *Alter role* é necessária para alterar a classe de recurso de um usuário.
* Embora seja possível adicionar um usuário a uma ou mais das classes de recursos mais altas, as classes de recursos dinâmicos têm prioridade sobre as classes de recursos estáticos. Ou seja, se um usuário for atribuído a **mediumrc** (dinâmico) e **staticrc80** (estático), **mediumrc** é a classe de recursos que será respeitada.
 * Quando um usuário é atribuído a mais de uma classe de recursos em um tipo de classe de recursos específico (mais de uma classe de recursos dinâmicos ou mais de uma classe de recursos estáticos), a classe de recursos mais alta é respeitada. Ou seja, se um usuário for atribuído a mediumrc e largerc, a classe de recurso mais elevada (largerc) será respeitada. E se um usuário for atribuído a **staticrc20** e **statirc80**, **staticrc80** será respeitada.
* A classe de recurso do usuário administrativo do sistema não pode ser alterada.

Para obter um exemplo detalhado, consulte [Alterando o exemplo de classe de recurso de usuário](#changing-user-resource-class-example).

## <a name="memory-allocation"></a>Alocação de memória
Há prós e contras quanto ao aumento da classe de recurso do usuário. O aumento de uma classe de recurso para um usuário concede às suas consultas acesso a mais memória, o que pode significar que as consultas serão executadas mais rapidamente.  No entanto, classes de recursos superiores também reduzem o número de consultas simultâneas que podem ser executadas. Essa é a compensação entre alocar grandes quantidades de memória para uma única consulta ou permitir que outras consultas, que também precisam de alocações de memória, sejam executadas simultaneamente. Se um usuário receber alocações de memória altas para uma consulta, outros usuários não terão acesso a essa mesma memória para executar uma consulta.

A tabela a seguir mapeia a memória alocada para cada distribuição por DWU e classe de recurso.

### <a name="memory-allocations-per-distribution-for-dynamic-resource-classes-mb"></a>Alocações de memória por distribuição de classes de recursos dinâmicos (MB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |100 |100 |200 |400 |
| DW200 |100 |200 |400 |800 |
| DW300 |100 |200 |400 |800 |
| DW400 |100 |400 |800 |1.600 |
| DW500 |100 |400 |800 |1.600 |
| DW600 |100 |400 |800 |1.600 |
| DW1000 |100 |800 |1.600 |3.200 |
| DW1200 |100 |800 |1.600 |3.200 |
| DW1500 |100 |800 |1.600 |3.200 |
| DW2000 |100 |1.600 |3.200 |6.400 |
| DW3000 |100 |1.600 |3.200 |6.400 |
| DW6000 |100 |3.200 |6.400 |12.800 |

A tabela a seguir mapeia a memória alocada para cada distribuição por DWU e classe de recursos estáticos. Observe que as classes de recursos superiores têm sua memória reduzida para atender os limites de DWU globais.

### <a name="memory-allocations-per-distribution-for-static-resource-classes-mb"></a>Alocações de memória por distribuição de classes de recursos estáticos (MB)
| DWU | staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |100 |200 |400 |400 |400 |400 |400 |400 |
| DW200 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW300 |100 |200 |400 |800 |800 |800 |800 |800 |
| DW400 |100 |200 |400 |800 |1.600 |1.600 |1.600 |1.600 |
| DW500 |100 |200 |400 |800 |1.600 |1.600 |1.600 |1.600 |
| DW600 |100 |200 |400 |800 |1.600 |1.600 |1.600 |1.600 |
| DW1000 |100 |200 |400 |800 |1.600 |3.200 |3.200 |3.200 |
| DW1200 |100 |200 |400 |800 |1.600 |3.200 |3.200 |3.200 |
| DW1500 |100 |200 |400 |800 |1.600 |3.200 |3.200 |3.200 |
| DW2000 |100 |200 |400 |800 |1.600 |3.200 |6.400 |6.400 |
| DW3000 |100 |200 |400 |800 |1.600 |3.200 |6.400 |6.400 |
| DW6000 |100 |200 |400 |800 |1.600 |3.200 |6.400 |12.800 |

Na tabela anterior, você pode ver que uma consulta em execução em um DW2000 na classe de recursos **xlargerc** teria acesso a 6.400 MB de memória dentro de cada um dos 60 bancos de dados distribuídos.  Há 60 distribuições no SQL Data Warehouse. Portanto, para calcular a alocação de memória total para uma consulta em uma determinada classe de recurso, os valores acima devem ser multiplicados por 60.

### <a name="memory-allocations-system-wide-gb"></a>Alocações de memória em todo o sistema (GB)
| DWU | smallrc | mediumrc | largerc | xlargerc |
|:--- |:---:|:---:|:---:|:---:|
| DW100 |6 |6 |12 |23 |
| DW200 |6 |12 |23 |47 |
| DW300 |6 |12 |23 |47 |
| DW400 |6 |23 |47 |94 |
| DW500 |6 |23 |47 |94 |
| DW600 |6 |23 |47 |94 |
| DW1000 |6 |47 |94 |188 |
| DW1200 |6 |47 |94 |188 |
| DW1500 |6 |47 |94 |188 |
| DW2000 |6 |94 |188 |375 |
| DW3000 |6 |94 |188 |375 |
| DW6000 |6 |188 |375 |750 |

Nesta tabela com as alocações de memória de todo o sistema, você pode ver que uma consulta em execução em um DW2000 na classe de recurso xlargerc recebe a alocação de um total de 375 GB de memória (6.400 MB * 60 distribuições / 1.024 para converter em GB) por todo o SQL Data Warehouse.

O mesmo cálculo se aplica a classes de recursos estáticos.
 
## <a name="concurrency-slot-consumption"></a>Consumo de slot de simultaneidade  
O SQL Data Warehouse concede mais memória para consultas em execução em classes de recursos mais elevadas. A memória é um recurso fixo.  Portanto, quanto mais memória for alocada por consulta, menos consultas simultâneas poderão ser executadas. A tabela a seguir reitera todos os conceitos anteriores em uma única exibição que mostra o número de slots de simultaneidade disponíveis por DWU, bem como os slots consumidos por cada classe de recurso.  

### <a name="allocation-and-consumption-of-concurrency-slots-for-dynamic-resource-classes"></a>Alocação e consumo de slots de simultaneidade para classes de recursos dinâmicos  
| DWU | Máximo de consultas simultâneas | Slots de simultaneidade alocados | Slots usados pelo smallrc | Slots usados pelo mediumrc | Slots usados pelo largerc | Slots usados pelo xlargerc |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |1 |2 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |
| DW400 |16 |16 |1 |4 |8 |16 |
| DW500 |20 |20 |1 |4 |8 |16 |
| DW600 |24 |24 |1 |4 |8 |16 |
| DW1000 |32 |40 |1 |8 |16 |32 |
| DW1200 |32 |48 |1 |8 |16 |32 |
| DW1500 |32 |60 |1 |8 |16 |32 |
| DW2000 |32 |80 |1 |16 |32 |64 |
| DW3000 |32 |120 |1 |16 |32 |64 |
| DW6000 |32 |240 |1 |32 |64 |128 |

### <a name="allocation-and-consumption-of-concurrency-slots-for-static-resource-classes"></a>Alocação e consumo de slots de simultaneidade para classes de recursos estáticos  
| DWU | Máximo de consultas simultâneas | Slots de simultaneidade alocados |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:--- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| DW100 |4 |4 |1 |2 |4 |4 |4 |4 |4 |4 |
| DW200 |8 |8 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW300 |12 |12 |1 |2 |4 |8 |8 |8 |8 |8 |
| DW400 |16 |16 |1 |2 |4 |8 |16 |16 |16 |16 |
| DW500 | 20| 20| 1| 2| 4| 8| 16| 16| 16| 16|
| DW600 | 24| 24| 1| 2| 4| 8| 16| 16| 16| 16|
| DW1000 | 32| 40| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1200 | 32| 48| 1| 2| 4| 8| 16| 32| 32| 32|
| DW1500 | 32| 60| 1| 2| 4| 8| 16| 32| 32| 32|
| DW2000 | 32| 80| 1| 2| 4| 8| 16| 32| 64| 64|
| DW3000 | 32| 120| 1| 2| 4| 8| 16| 32| 64| 64|
| DW6000 | 32| 240| 1| 2| 4| 8| 16| 32| 64| 128|

Nestas tabelas, você pode ver que um SQL Data Warehouse em execução como DW1000 aloca uma quantidade máxima de 32 consultas simultâneas e um total de 40 slots de simultaneidade. Se todos os usuários estivessem em execução no smallrc, seriam permitidas 32 consultas simultâneas, pois cada consulta consumiria um slot de simultaneidade. Se todos os usuários em um DW1000 estivessem em execução na mediumrc, cada consulta receberia a alocação de 800 MB por distribuição de uma alocação de memória total de 47 GB por consulta e a simultaneidade seria limitada a cinco usuários (40 slots de simultaneidade/oito slots por usuário mediumrc).

## <a name="selecting-proper-resource-class"></a>Selecionando a classe de recursos apropriada  
Uma prática recomendada é atribuir usuários permanentemente a uma classe de recurso em vez de alterar suas classes de recurso. Por exemplo, cargas para tabelas columnstore clusterizadas cria índices de qualidade superiores quando mais memória é alocada. Para garantir que as cargas tenham acesso a mais memória, crie um usuário especificamente para o carregamento de dados e atribua esse usuário permanentemente a uma classe de recurso mais elevada.
Há algumas melhores práticas a serem seguidas aqui. Como mencionado acima, o SQL DW dá suporte a dois tipos de classes de recursos: classes de recursos estáticos e classes de recursos dinâmicos.
### <a name="loading-best-practices"></a>Melhores práticas de carregamento
1.  Se as expectativas forem de carregamentos com uma quantidade de dados regular, uma classe de recursos estáticos é uma boa opção. Posteriormente, ao escalar verticalmente para obter mais capacidade de computação, o data warehouse poderá executar consultas mais simultâneas de imediato, uma vez que o usuário do carregamento não consome mais memória.
2.  Se as expectativas forem de carregamentos maiores em algumas ocasiões, uma classe de recursos dinâmicos será uma boa opção. Posteriormente, ao escalar verticalmente para obter mais capacidade de computação, o usuário do carregamento obterá mais memória de pronto, permitindo assim que o carregamento seja realizado mais rápido.

A memória necessária para processar cargas com eficiência depende da natureza da tabela carregada e da quantidade de dados processada. Por exemplo, carregar dados em tabelas CCI requer memória para permitir que os rowgroups CCI atinjam o nível ideal. Para obter mais detalhes, consulte os Índices columnstore – diretrizes de carregamento de dados.

Como uma melhor prática, é recomendável usar pelo menos 200 MB de memória para os carregamentos.

### <a name="querying-best-practices"></a>Melhores práticas de consulta
As consultas têm requisitos diferentes dependendo de sua complexidade. Aumentar a memória por consulta ou aumentar a simultaneidade são duas formas válidas para aumentar a produtividade geral dependendo das necessidades da consulta.
1.  Se as expectativas forem consultas complexas e regulares (por exemplo, para gerar relatórios diários e semanais) e não precisarem aproveitar a simultaneidade, uma classe de recursos dinâmicos será uma boa opção. Se o sistema tiver mais dados a serem processados, escalar verticalmente o data warehouse fornecerá automaticamente mais memória para o usuário executando a consulta.
2.  Se as expectativas forem padrões de simultaneidade variáveis ou cotidianas (por exemplo, se o banco de dados for consultado por meio de uma interface do usuário Web amplamente acessível), uma classe de recursos estáticos será uma boa opção. Posteriormente, ao escalar verticalmente para o data warehouse, o usuário associado à classe de recursos estáticos automaticamente poderá executar mais consultas simultâneas.

Selecionar a concessão de memória apropriada dependendo da necessidade de sua consulta não é simples, uma vez que depende de muitos fatores, como a quantidade de dados consultada, a natureza dos esquemas de tabela e vários predicados de união, seleção e agrupamento. Do ponto de vista geral, alocar mais memória permitirá que consultas sejam concluídas mais rapidamente, mas reduzirá a simultaneidade geral. Se a simultaneidade não for um problema, a alocação excessiva de memória não será prejudicial. Para ajustar a taxa de transferência, a tentativa de vários tipos de classes de recursos pode ser necessária.

Você pode usar o procedimento armazenado a seguir para descobrir a concessão de memória e a simultaneidade por classe de recursos em um determinado SLO e a melhor classe de recursos mais próxima para operações de CCI de uso intenso da memória em uma tabela CCI não particionada em uma determinada classe de recursos:

#### <a name="description"></a>Descrição:  
Aqui está a finalidade deste procedimento armazenado:  
1. Ajudar o usuário a descobrir a simultaneidade e a concessão de memória por classe de recursos em um determinado SLO. O usuário precisa fornecer NULL para o esquema e tablename para isso, conforme mostrado no exemplo a seguir.  
2. Ajudar o usuário a descobrir a melhor classe de recursos mais próxima para operações de CCI de uso intenso de memória (carregar, copiar tabela, recompilar índice etc.) na tabela CCI não particionada em uma determinada classe de recursos. O procedimento armazenado usa o esquema da tabela para descobrir a concessão de memória necessária para isso.

#### <a name="dependencies--restrictions"></a>Dependências e restrições:
- Esse procedimento armazenado não foi projetado para calcular os requisitos de memória para a tabela CCI particionada.    
- Esse procedimento armazenado não considera os requisitos de memória para a parte SELECT de CTAS/INSERT-SELECT e pressupõe que sejam um simples SELECT.
- Esse procedimento armazenado usa uma tabela temporária para que possa ser usada na sessão em que esse procedimento armazenado foi criado.    
- Esse procedimento armazenado depende das ofertas atuais (por exemplo, a configuração de hardware, a configuração DMS) e se qualquer uma delas for alterada, esse procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende do limite de simultaneidade oferecidos existente e se isso mudar, o procedimento armazenado não funcionará corretamente.  
- Esse procedimento armazenado depende das ofertas de classe de recursos existentes e se isso mudar, o procedimento armazenado não funcionará corretamente.  

>  [!NOTE]  
>  Se nenhuma saída aparecer após a execução do procedimento armazenado com parâmetros fornecidos, isso poderá ser consequência de dois problemas. <br />1. Um parâmetro de DW contém o valor inválido de SLO <br />2. OU não há nenhuma classe de recurso correspondente para a operação CCI se o nome de tabela tiver sido fornecido. <br />Por exemplo, no DW100, a concessão de memória mais alta disponível é 400 MB e se o esquema de tabela for grande o suficiente para atravessar o requisito de 400 MB.
      
#### <a name="usage-example"></a>Exemplo de uso:
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

A Table1 usada nos exemplos acima pode ser criado como abaixo:  
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

#### <a name="heres-the-stored-procedure-definition"></a>Aqui está a definição do procedimento armazenado:
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
(   @DWU VARCHAR(7),
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

-- Creating ref. temptable (CTAS) to hold mapping info.
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
                     WHEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 * (CAST (@DWU_NUM AS FLOAT)/6000)) 
                     ELSE 1 
              END AS multipliplication_factor
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
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB       
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multipliplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="query-importance"></a>Importância da consulta
O SQL Data Warehouse implementa classes de recursos usando grupos de carga de trabalho. Há um total de oito grupos de carga de trabalho que controlam o comportamento das classes de recurso entre os vários tamanhos de DWU. Para qualquer DWU, o SQL Data Warehouse usa somente quatro dos oito grupos de carga de trabalho. Isso faz sentido, pois cada grupo de carga de trabalho é atribuído a uma das quatro classes de recurso: smallrc, mediumrc, largerc ou xlargerc. A importância de entender esses grupos de carga de trabalho é que alguns deles são definidos com *importância*maior. A importância é usada para agendamento de CPU. As consultas executadas com importância alta obterão três vezes mais ciclos de CPU do que aquelas com importância média. Portanto, os mapeamentos de slot de simultaneidade também determinam a prioridade da CPU. Quando uma consulta consome 16 ou mais slots, ela é executada com alta importância.

A tabela a seguir mostra os mapeamentos de importância para cada grupo de carga de trabalho.

### <a name="workload-group-mappings-to-concurrency-slots-and-importance"></a>Mapeamentos de grupo de carga de trabalho para slots de simultaneidade e importância
| Grupos de carga de trabalho | Mapeamento do slot de simultaneidade | MB / Distribuição | Mapeamento de importância |
|:--- |:---:|:---:|:--- |
| SloDWGroupC00 |1 |100 |Média |
| SloDWGroupC01 |2 |200 |Média |
| SloDWGroupC02 |4 |400 |Média |
| SloDWGroupC03 |8 |800 |Média |
| SloDWGroupC04 |16 |1.600 |Alto |
| SloDWGroupC05 |32 |3.200 |Alto |
| SloDWGroupC06 |64 |6.400 |Alto |
| SloDWGroupC07 |128 |12.800 |Alto |

Da tabela **Alocação e consumo de slots de simultaneidade** , podemos ver que um DW500 usa um, quatro, oito ou 16 slots de simultaneidade para smallrc, mediumrc, largerc e xlargerc, respectivamente. Você pode procurar esses valores na tabela anterior para localizar a importância de cada classe de recurso.

### <a name="dw500-mapping-of-resource-classes-to-importance"></a>Mapeamento do DW500 para obter a importância das classes de recurso
| classe de recurso | Grupo de carga de trabalho | Slots de simultaneidade usados | MB / Distribuição | importância |
|:--- |:--- |:---:|:---:|:--- |
| smallrc |SloDWGroupC00 |1 |100 |Média |
| mediumrc |SloDWGroupC02 |4 |400 |Média |
| largerc |SloDWGroupC03 |8 |800 |Média |
| xlargerc |SloDWGroupC04 |16 |1.600 |Alto |
| staticrc10 |SloDWGroupC00 |1 |100 |Média |
| staticrc20 |SloDWGroupC01 |2 |200 |Média |
| staticrc30 |SloDWGroupC02 |4 |400 |Média |
| staticrc40 |SloDWGroupC03 |8 |800 |Média |
| staticrc50 |SloDWGroupC03 |16 |1.600 |Alto |
| staticrc60 |SloDWGroupC03 |16 |1.600 |Alto |
| staticrc70 |SloDWGroupC03 |16 |1.600 |Alto |
| staticrc80 |SloDWGroupC03 |16 |1.600 |Alto |

Você pode usar a seguinte consulta DMV para examinar as diferenças na alocação de recurso de memória em detalhes da perspectiva do administrador de recursos ou para analisar o uso ativo e histórico dos grupos de carga de trabalho ao solucionar problemas.

```sql
WITH rg
AS
(   SELECT  
     pn.name                        AS node_name
    ,pn.[type]                        AS node_type
    ,pn.pdw_node_id                    AS node_id
    ,rp.name                        AS pool_name
    ,rp.max_memory_kb*1.0/1024                AS pool_max_mem_MB
    ,wg.name                        AS group_name
    ,wg.importance                    AS group_importance
    ,wg.request_max_memory_grant_percent        AS group_request_max_memory_grant_pcnt
    ,wg.max_dop                        AS group_max_dop
    ,wg.effective_max_dop                AS group_effective_max_dop
    ,wg.total_request_count                AS group_total_request_count
    ,wg.total_queued_request_count            AS group_total_queued_request_count
    ,wg.active_request_count                AS group_active_request_count
    ,wg.queued_request_count                AS group_queued_request_count
    FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
    JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    
            ON  wg.pdw_node_id  = rp.pdw_node_id
            AND wg.pool_id      = rp.pool_id
    JOIN    sys.dm_pdw_nodes pn
            ON    wg.pdw_node_id    = pn.pdw_node_id
    WHERE   wg.name like 'SloDWGroup%'
        AND     rp.name = 'SloDWPool'
)
SELECT    pool_name
,        pool_max_mem_MB
,        group_name
,        group_importance
,        (pool_max_mem_MB/100)*group_request_max_memory_grant_pcnt AS max_memory_grant_MB
,        node_name
,        node_type
,       group_total_request_count
,       group_total_queued_request_count
,       group_active_request_count
,       group_queued_request_count
FROM    rg
ORDER BY
    node_name
,    group_request_max_memory_grant_pcnt
,    group_importance
;
```

## <a name="queries-that-honor-concurrency-limits"></a>Consultas que respeitam os limites de simultaneidade
A maioria das consultas é governada pelas classes de recurso. Essas consultas devem se ajustar tanto aos limites de consultas de simultaneidade quanto aos de slots de simultaneidade. Um usuário não pode optar por excluir uma consulta do modelo de slot de simultaneidade.

Para reiterar, as instruções a seguir respeitam as classes de recurso:

* INSERT-SELECT
* UPDATE
* EXCLUIR
* SELECT (ao consultar tabelas de usuário)
* ALTER INDEX REBUILD
* ALTER INDEX REORGANIZE
* ALTER TABLE REBUILD
* CREATE INDEX
* CREATE CLUSTERED COLUMNSTORE INDEX
* CREATE TABLE AS SELECT (CTAS)
* Carregamento de dados
* Operações de movimentação de dados realizadas pelo Serviço de Movimentação de Dados (DMS)

## <a name="query-exceptions-to-concurrency-limits"></a>Exceções de consulta para limites de simultaneidade
Algumas consultas não respeitam a classe de recurso à qual o usuário é atribuído. Essas exceções para os limites de simultaneidade são feitas quando os recursos de memória necessários para um determinado comando estão baixos, geralmente porque o comando é uma operação de metadados. O objetivo dessas exceções é evitar alocações de memória maiores para consultas que jamais precisarão delas. Nesses casos, a classe de recurso padrão pequena (smallrc) sempre é usada, independentemente da classe de recurso real atribuída ao usuário. Por exemplo, `CREATE LOGIN` sempre será executado em smallrc. Os recursos necessários para atender essa operação são muito baixos, portanto, não faz sentido incluir a consulta no modelo de slot de simultaneidade.  Essas consultas também não são limitadas pelo limite de simultaneidade de 32 usuários, um número ilimitado dessas consultas pode ser executado até o limite de sessão de 1.024 sessões.

As instruções a seguir não respeitam classes de recursos:

* CREATE ou DROP TABLE
* ALTER TABLE ... SWITCH, SPLIT ou MERGE PARTITION
* ALTER INDEX DISABLE
* DROP INDEX
* CREATE, UPDATE ou DROP STATISTICS
* TRUNCATE TABLE
* ALTER AUTHORIZATION
* CREATE LOGIN
* CREATE, ALTER ou DROP USER
* CREATE, ALTER ou DROP PROCEDURE
* CREATE ou DROP VIEW
* INSERT VALUES
* SELECT de exibições do sistema e DMVs
* EXPLAIN
* DBCC

<!--
Removed as these two are not confirmed / supported under SQLDW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

##  <a name="changing-user-resource-class-example"></a> Alterar um exemplo de classe de recursos de usuário
1. **Criar logon:** abra uma conexão com o banco de dados **mestre** no SQL Server hospedando seu banco de dados do SQL Data Warehouse e execute os comandos a seguir.
   
    ```sql
    CREATE LOGIN newperson WITH PASSWORD = 'mypassword';
    CREATE USER newperson for LOGIN newperson;
    ```
   
   > [!NOTE]
   > É uma boa ideia criar um usuário no banco de dados mestre para os usuários do Azure SQL Data Warehouse. A criação de um usuário mestre permite que um usuário faça logon usando ferramentas, como o SSMS, sem especificar um nome de banco de dados.  Ela também permite que o usuário utilize o pesquisador de objetos para exibir todos os bancos de dados em um SQL Server.  Para obter mais detalhes sobre como criar e gerenciar usuários, consulte [Proteger um banco de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse].
   > 
   > 
2. **Criar um usuário do SQL Data Warehouse:** abra uma conexão com o banco de dados **SQL Data Warehouse** e execute o comando a seguir.
   
    ```sql
    CREATE USER newperson FOR LOGIN newperson;
    ```
3. **Conceder permissões:** o exemplo a seguir concede `CONTROL` no banco de dados **SQL Data Warehouse**. `CONTROL` no nível do banco de dados é o equivalente de db_owner no SQL Server.
   
    ```sql
    GRANT CONTROL ON DATABASE::MySQLDW to newperson;
    ```
4. **Aumentar a classe do recurso:** use a consulta a seguir para adicionar um usuário a uma função de gerenciamento de carga de trabalho maior.
   
    ```sql
    EXEC sp_addrolemember 'largerc', 'newperson'
    ```
5. **Diminuir a classe de recurso:** use a consulta a seguir para remover um usuário de uma função de gerenciamento de carga de trabalho.
   
    ```sql
    EXEC sp_droprolemember 'largerc', 'newperson';
    ```
   
   > [!NOTE]
   > Não é possível remover um usuário de smallrc.
   > 
   > 

## <a name="queued-query-detection-and-other-dmvs"></a>Detecção de consulta enfileirada e outros DMVs
Você pode usar o DMV `sys.dm_pdw_exec_requests` para identificar consultas que estão aguardando em uma fila de simultaneidade. As consultas que estão aguardando um slot de simultaneidade terão um status de **suspensas**.

```sql
SELECT      r.[request_id]                 AS Request_ID
        ,r.[status]                 AS Request_Status
        ,r.[submit_time]             AS Request_SubmitTime
        ,r.[start_time]                 AS Request_StartTime
        ,DATEDIFF(ms,[submit_time],[start_time]) AS Request_InitiateDuration_ms
        ,r.resource_class                         AS Request_resource_class
FROM    sys.dm_pdw_exec_requests r;
```

As funções de gerenciamento de carga de trabalho podem ser exibidas com `sys.database_principals`.

```sql
SELECT  ro.[name]           AS [db_role_name]
FROM    sys.database_principals ro
WHERE   ro.[type_desc]      = 'DATABASE_ROLE'
AND     ro.[is_fixed_role]  = 0;
```

A consulta a seguir mostra a qual função cada usuário está atribuído.

```sql
SELECT     r.name AS role_principal_name
        ,m.name AS member_principal_name
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r            ON rm.role_principal_id        = r.principal_id
JOIN    sys.database_principals AS m            ON rm.member_principal_id    = m.principal_id
WHERE    r.name IN ('mediumrc','largerc', 'xlargerc');
```

O SQL Data Warehouse tem os seguintes tipos de espera:

* **LocalQueriesConcurrencyResourceType**: consultas que ficam fora da estrutura de slot de simultaneidade. Consultas DMV e funções de sistema, como `SELECT @@VERSION` , são exemplos de consultas de locais.
* **UserConcurrencyResourceType**: consultas que ficam dentro da estrutura de slot de simultaneidade. Consultas em tabelas do usuário final representam exemplos que usariam esse tipo de recurso.
* **DmsConcurrencyResourceType**: esperas resultantes de operações de movimentação de dados.
* **BackupConcurrencyResourceType**: essa espera indica que está sendo feito backup de um banco de dados. O valor máximo para esse tipo de recurso é 1. Se vários backups foram solicitados ao mesmo tempo, os outros serão colocados em fila.

O DMV `sys.dm_pdw_waits` pode ser usado para ver quais recursos uma solicitação está aguardando.

```sql
SELECT  w.[wait_id]
,       w.[session_id]
,       w.[type]            AS Wait_type
,       w.[object_type]
,       w.[object_name]
,       w.[request_id]
,       w.[request_time]
,       w.[acquire_time]
,       w.[state]
,       w.[priority]
,    SESSION_ID()            AS Current_session
,    s.[status]            AS Session_status
,    s.[login_name]
,    s.[query_count]
,    s.[client_id]
,    s.[sql_spid]
,    r.[command]            AS Request_command
,    r.[label]
,    r.[status]            AS Request_status
,    r.[submit_time]
,    r.[start_time]
,    r.[end_compile_time]
,    r.[end_time]
,    DATEDIFF(ms,r.[submit_time],r.[start_time])        AS Request_queue_time_ms
,    DATEDIFF(ms,r.[start_time],r.[end_compile_time])    AS Request_compile_time_ms
,    DATEDIFF(ms,r.[end_compile_time],r.[end_time])        AS Request_execution_time_ms
,    r.[total_elapsed_time]
FROM    sys.dm_pdw_waits w
JOIN    sys.dm_pdw_exec_sessions s  ON w.[session_id] = s.[session_id]
JOIN    sys.dm_pdw_exec_requests r  ON w.[request_id] = r.[request_id]
WHERE    w.[session_id] <> SESSION_ID();
```

O DMV `sys.dm_pdw_resource_waits` mostra apenas as esperas de recursos consumidas por determinada consulta. O tempo de espera do recurso mede apenas o tempo de espera dos recursos a serem fornecidos, não o tempo de espera do sinal, que é o tempo necessário para o SQL Server subjacente agendar a consulta para a CPU.

```sql
SELECT  [session_id]
,       [type]
,       [object_type]
,       [object_name]
,       [request_id]
,       [request_time]
,       [acquire_time]
,       DATEDIFF(ms,[request_time],[acquire_time])  AS acquire_duration_ms
,       [concurrency_slots_used]                    AS concurrency_slots_reserved
,       [resource_class]
,       [wait_id]                                   AS queue_position
FROM    sys.dm_pdw_resource_waits
WHERE    [session_id] <> SESSION_ID();
```

O DMV `sys.dm_pdw_wait_stats` pode ser usado para análise de tendências históricas de espera.

```sql
SELECT    w.[pdw_node_id]
,        w.[wait_name]
,        w.[max_wait_time]
,        w.[request_count]
,        w.[signal_time]
,        w.[completed_count]
,        w.[wait_time]
FROM    sys.dm_pdw_wait_stats w;
```

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre como gerenciar usuários de banco de dados e segurança, confira [Proteger um banco de dados no SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obter mais informações sobre como classes de recurso maiores podem melhorar a qualidade do índice columnstore clusterizado, consulte [Recriando índices para melhorar a qualidade de segmento].

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Recriando índices para melhorar a qualidade de segmento]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
