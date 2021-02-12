---
title: Esquemas definidos pelo usuário dentro de Synapse SQL
description: Nas seções a seguir, você encontrará várias dicas para usar esquemas definidos pelo usuário do T-SQL para desenvolver soluções com o recurso Synapse SQL da análise de Synapse do Azure.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6920d6cd190927747a69fc4dc0f0791278665abe
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451694"
---
# <a name="user-defined-schemas-within-synapse-sql"></a>Esquemas definidos pelo usuário dentro de Synapse SQL

Nas seções a seguir, você encontrará várias dicas para usar esquemas definidos pelo usuário do T-SQL para desenvolver soluções no Synapse SQL.

## <a name="schemas-for-application-boundaries"></a>Esquemas para limites de aplicativo

A arquitetura de análise tradicional geralmente usa bancos de dados separados para criar limites de aplicativos com base na carga de trabalho, domínio ou segurança. Por exemplo, uma infraestrutura tradicional de análise de SQL Server pode incluir um banco de dados de preparo, um banco de dados de análise e bancos de data mart. Nessa topologia, cada banco de dados funciona como um limite de carga de trabalho e segurança na arquitetura.

Em vez disso, Synapse SQL executa toda a carga de trabalho de análise em um banco de dados. Junções entre bancos de dados não são permitidas. Synapse SQL espera que todas as tabelas usadas pelo warehouse sejam armazenadas dentro de um banco de dados.

> [!NOTE]
> Os pools dedicados do SQL não dão suporte a consultas entre bancos de dados de qualquer tipo. Consequentemente, as implementações de análise que aproveitam esse padrão precisarão ser revisadas. O pool SQL sem servidor dá suporte a consultas entre bancos de dados.

## <a name="user-defined-schema-recommendations"></a>Recomendações de esquema definidas pelo usuário

Estão incluídas recomendações para consolidar cargas de trabalho, segurança, domínio e limites funcionais usando esquemas definidos pelo usuário:

- Use um banco de dados para executar toda a carga de trabalho de análise.
- Consolide seu ambiente de análise existente para usar um banco de dados.
- Utilize **esquemas definidos pelo usuário** para fornecer o limite implementado anteriormente usando bancos de dados.

Se os esquemas definidos pelo usuário ainda não tiverem sido usados anteriormente, você terá um Slate limpo. Use o nome do banco de dados antigo como base para os esquemas definidos pelo usuário no banco de dados SQL do Synapse.

Se os esquemas já tiverem sido usados, você terá algumas opções:

- Remover os nomes de esquema herdados e começar do zero
- Manter os nomes de esquema herdados predependentes do nome do esquema herdado para o nome da tabela
- Mantenha os nomes de esquema herdados implementando exibições na tabela em um esquema extra, que recria a estrutura de esquema antiga.

> [!NOTE]
> Na primeira inspeção, a opção 3 pode parecer com a opção mais atraente. As exibições são somente leitura no Synapse SQL. Qualquer modificação nos dados ou na tabela precisariam ser executados em relação à tabela base. A opção 3 também apresenta uma camada de modos de exibição em seu sistema. Talvez você queira dar algumas idéias adicionais se já estiver usando modos de exibição em sua arquitetura.
> 
> 

### <a name="examples"></a>Exemplos

Implemente esquemas definidos pelo usuário com base em nomes de banco de dados.

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the analytics
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create analytics tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha os nomes de esquema herdados colocando-os em um nome de tabela. Usar esquemas para o limite de carga de trabalho.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the analytics boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the analytics boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantenha os nomes de esquema herdados usando exibições.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the analytics boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey    BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base analytics tables in the analytics boundary
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
> Qualquer alteração na estratégia de esquema requer uma revisão do modelo de segurança para o banco de dados. Em muitos casos, você poderá simplificar o modelo de segurança atribuindo permissões no nível de esquema.

Se forem necessárias permissões mais granulares, você poderá usar funções de banco de dados. Para obter mais informações sobre funções de banco de dados, consulte o artigo [Manage Database Roles and Users](../../analysis-services/analysis-services-database-users.md) .

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte [Visão geral de desenvolvimento do SQL do Synapse](develop-overview.md).
