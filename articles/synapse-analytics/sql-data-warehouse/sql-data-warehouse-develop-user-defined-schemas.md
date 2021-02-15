---
title: Usando esquemas definidos pelo usuário
description: Dicas para usar esquemas definidos pelo usuário do T-SQL para desenvolver soluções para pools de SQL dedicados no Azure Synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 3204c77dd076d9aac6eb5a60b489280caefcbf4b
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460446"
---
# <a name="user-defined-schemas-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Esquemas definidos pelo usuário para pools de SQL dedicados no Azure Synapse Analytics
Este artigo se concentra em fornecer várias dicas para usar esquemas definidos pelo usuário do T-SQL para desenvolver soluções no pool dedicado do SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicativo

Os data warehouses tradicionais normalmente usam bancos de dados separados para criar limites de aplicativo com base na carga de trabalho, domínio ou segurança. 

Por exemplo, um SQL Server tradicional data warehouse pode incluir um banco de dados de preparo, um data warehouse banco de dados e alguns bancos de data mart. Nessa topologia, cada banco de dados funciona como um limite de carga de trabalho e segurança na arquitetura.

Por outro lado, um pool SQL dedicado executa toda a carga de trabalho de data warehouse dentro de um banco de dados. Junções entre bancos de dados não são permitidas. O pool SQL dedicado espera que todas as tabelas usadas pelo warehouse sejam armazenadas dentro de um banco de dados.

> [!NOTE]
> O pool SQL não dá suporte a consultas entre bancos de dados de qualquer tipo. Consequentemente, implementações de data warehouse que utilizam esse padrão precisarão ser revisadas.
> 
> 

## <a name="recommendations"></a>Recomendações
Veja a seguir as recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais usando esquemas definidos pelo usuário:

- Use um banco de dados em um pool SQL dedicado para executar toda a carga de trabalho de data warehouse.
- Consolide seu ambiente de data warehouse existente para usar um banco de dados do pool SQL dedicado.
- Utilize **esquemas definidos pelo usuário** para fornecer o limite implementado anteriormente usando bancos de dados.

Se os esquemas definidos pelo usuário não tiverem sido usados anteriormente, você terá um Slate limpo. Use o nome do banco de dados antigo como base para os esquemas definidos pelo usuário no banco de dados do pool do SQL dedicado.

Se os esquemas já tiverem sido usados, você terá algumas opções:

- Remova os nomes de esquema herdados e comece a ser atualizado.
- Mantenha os nomes de esquema herdados predependentes do nome do esquema herdado para o nome da tabela.
- Manter os nomes de esquema herdados implementando exibições sobre a tabela em um esquema extra para recriar a estrutura do esquema antigo.

> [!NOTE]
> Na primeira inspeção, a opção 3 pode parecer a opção mais atraente. No entanto, o detalhe é traiçoeiro. As exibições são somente leitura no pool do SQL dedicado. Qualquer modificação nos dados ou na tabela precisariam ser executados em relação à tabela base. A opção 3 também apresenta uma camada de modos de exibição em seu sistema. Talvez você queira pensar um pouco sobre isso se já estiver usando modos de exibição em sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos:
Implementar esquemas definidos pelo usuário com base em nomes de banco de dados:

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

Mantenha os nomes de esquema herdados colocando-os em um nome de tabela. Usar esquemas para o limite de carga de trabalho:

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

Mantenha os nomes de esquema herdados usando exibições:

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
> Qualquer alteração na estratégia de esquema precisa de uma revisão do modelo de segurança para o banco de dados. Em muitos casos, você poderá simplificar o modelo de segurança atribuindo permissões no nível de esquema. Se forem necessárias permissões mais granulares, você poderá usar funções de banco de dados.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).

