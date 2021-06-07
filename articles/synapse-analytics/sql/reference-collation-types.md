---
title: Suporte a ordenações
description: Suporte a tipos de agrupamento para Synapse SQL no Azure Synapse Analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 436dbac814197556385a33d956928f97fd4716bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93311915"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>Suporte a agrupamento de banco de dados para Synapse SQL no Azure Synapse Analytics 

Os agrupamentos fornecem a localidade, a página de código, a ordem de classificação e as regras de sensibilidade de caracteres para tipos de dados baseados em caracteres. Depois de escolhido, todas as colunas e expressões que exigem informações de agrupamento herdam o agrupamento escolhido da configuração do banco de dados. A herança padrão pode ser substituída informando explicitamente um agrupamento diferente para um tipo de dados baseado em caractere.

Você pode alterar o agrupamento de banco de dados padrão do portal do Azure ao criar um novo banco de dados do pool SQL dedicado. Esse recurso facilita ainda mais a criação de um novo banco de dados usando um dos agrupamentos de banco de dados com suporte 3800.

Você pode especificar o agrupamento de banco de dados do pool SQL sem servidor padrão no momento da criação usando a instrução CREATE DATABASE.

## <a name="change-collation"></a>Alterar agrupamento
Para alterar o agrupamento padrão do banco de dados do pool SQL dedicado, atualize para o campo agrupamento na experiência de provisionamento. Por exemplo, se você quisesse alterar o agrupamento padrão para diferenciar maiúsculas de minúsculas, renomearia o agrupamento de SQL_Latin1_General_CP1_CI_AS para SQL_Latin1_General_CP1_CS_AS. 

Para alterar o agrupamento padrão de um banco de dados do pool SQL sem servidor, você pode usar a instrução ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Lista de tipos de agrupamentos sem suporte
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Além disso, o pool SQL dedicado não dá suporte aos seguintes tipos de agrupamento:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>Verificar o agrupamento atual
Para verificar o agrupamento atual do banco de dados, você pode executar o seguinte trecho T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Quando passou ' collation ' como o parâmetro Property, a função DatabasePropertyEx retorna o agrupamento atual para o banco de dados especificado. Você pode saber mais sobre a função DatabasePropertyEx no MSDN.

## <a name="next-steps"></a>Próximas etapas

Encontre mais informações sobre as melhores práticas do pool de SQL dedicado e do pool de SQL sem servidor nos seguintes artigos:

- [Melhores práticas do pool de SQL dedicado](best-practices-sql-pool.md)
- [Melhores práticas do pool de SQL sem servidor](best-practices-sql-on-demand.md)


