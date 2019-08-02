---
title: Usando esquemas definidos pelo usuário no SQL Data Warehouse | Microsoft Docs
description: Dicas para usar esquemas definidos pelo usuário do T-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e0ae00e0fca5ed4c6fba04444e5c50424462d297
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479574"
---
# <a name="using-user-defined-schemas-in-sql-data-warehouse"></a>Usando esquemas definidos pelo usuário no SQL Data Warehouse
Dicas para usar esquemas definidos pelo usuário do T-SQL no Azure SQL Data Warehouse para desenvolvimento de soluções.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicativo

Os data warehouses tradicionais normalmente usam bancos de dados separados para criar limites de aplicativo com base na carga de trabalho, domínio ou segurança. Por exemplo, um data warehouse tradicional do SQL Server pode incluir um banco de dados de preparo, um banco de dados do data warehouse e alguns bancos de dados do data mart. Nesta topologia, cada banco de dados funciona como uma carga de trabalho e limite de segurança na arquitetura.

Por outro lado, o SQL Data Warehouse executa toda a carga de trabalho do data warehouse em um só banco de dados. Uniões cruzadas de banco de dados não são permitidas. Portanto, o SQL Data Warehouse espera que todas as tabelas usadas pelo warehouse sejam armazenadas em um só banco de dados.

> [!NOTE]
> O SQL Data Warehouse não oferece suporte a consultas cruzadas de banco de dados de qualquer tipo. Consequentemente, implementações de data warehouse que utilizam esse padrão precisarão ser revisadas.
> 
> 

## <a name="recommendations"></a>Recomendações
Estas são recomendações para consolidar limites funcionais, de carga de trabalho, segurança e domínio usando esquemas definidos pelo usuário

1. Use um banco de dados do SQL Data Warehouse para executar toda a carga de trabalho do data warehouse
2. Consolide seu ambiente de data warehouse existente para usar um banco de dados do SQL Data Warehouse
3. Utilize **esquemas definidos pelo usuário** para fornecer o limite implementado anteriormente usando bancos de dados.

Se esquemas definidos pelo usuário não tiverem sido usados anteriormente, então você tem uma área limpa. Basta usar o nome antigo do banco de dados como base para os esquemas definidos pelo usuário no banco de dados do SQL Data Warehouse.

Se já tiverem sido usados esquemas, então você tem algumas opções:

1. Remover os nomes de esquema herdados e começar do zero
2. Manter os nomes de esquema herdados acrescentando o nome do esquema herdado ao nome da tabela
3. Manter os nomes de esquema herdados implementando exibições sobre a tabela em um esquema extra para recriar a estrutura do esquema antigo.

> [!NOTE]
> Na primeira inspeção, a opção 3 pode parecer a opção mais atraente. No entanto, o detalhe é traiçoeiro. Os modos de exibição são somente leitura no SQL Data Warehouse. Qualquer modificação nos dados ou na tabela precisariam ser executados em relação à tabela base. A opção 3 também apresenta uma camada de modos de exibição em seu sistema. Talvez você queira pensar um pouco sobre isso se já estiver usando modos de exibição em sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos:
Implementar esquemas definidos pelo usuário com base em nomes de banco de dados

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Manter nomes de esquema herdados acrescentando-os ao nome da tabela. Usar esquemas para o limite de carga de trabalho.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Manter nomes de esquema herdados usando modos de exibição

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [!NOTE]
> Qualquer alteração na estratégia de esquema precisa de uma revisão do modelo de segurança para o banco de dados. Em muitos casos, você poderá simplificar o modelo de segurança ao atribuir permissões no nível do esquema. Se forem necessárias permissões mais granulares, então você poderá usar funções de banco de dados.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

