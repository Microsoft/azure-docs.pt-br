---
title: Modelo de metadados compartilhados
description: O Azure Synapse Analytics permite que os diferentes mecanismos computacionais do workspace compartilhem bancos de dados e tabelas entre os pools do Apache Spark sem servidor, o pool de SQL sem servidor e os pools de SQL dedicados.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: b10b6f011fa7daee4094f0cc7b819d36127fedcd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96460344"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Metadados compartilhados do Azure Synapse Analytics

O Azure Synapse Analytics permite que os diferentes mecanismos computacionais do workspace compartilhem bancos de dados e tabelas entre os pools do Apache Spark sem servidor e o pool de SQL sem servidor.

O compartilhamento dá suporte ao chamado padrão de data warehouse moderno e fornece aos mecanismos SQL do workspace acesso às tabelas e aos bancos de dados criados com o Spark. Ele também permite que os mecanismos SQL criem os próprios objetos que não estão sendo compartilhados com os outros mecanismos.

## <a name="support-the-modern-data-warehouse"></a>Dar suporte ao data warehouse moderno

O modelo de metadados compartilhados dá suporte ao padrão de data warehouse moderno da seguinte maneira:

1. Os dados do data lake são preparados e estruturados de maneira eficiente com o Spark, armazenando os dados preparados em tabelas com suporte do Parquet (possivelmente particionadas) contidas, provavelmente, em vários bancos de dados.

2. Os bancos de dados criados pelo Spark e todas as tabelas se tornam visíveis em uma das instâncias do Pool do Spark do workspace do Azure Synapse e podem ser usados em um dos trabalhos do Spark. Essa funcionalidade está sujeita às [permissões](#security-model-at-a-glance), pois todos os pools do Spark em um workspace compartilham o armazenamento de metadados de catálogo subjacente.

3. Os bancos de dados criados pelo Spark e as tabelas de backup do Parquet correspondentes se tornam visíveis no pool de SQL sem servidor do workspace. Os [bancos de dados](database.md) são criados automaticamente nos metadados do pool de SQL sem servidor e as [tabelas externas e gerenciadas](table.md) criadas por um trabalho do Spark ficam acessíveis como tabelas externas nos metadados do pool de SQL sem servidor no esquema `dbo` do banco de dados correspondente. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

A sincronização de objeto ocorre de maneira assíncrona. Os objetos terão um pequeno atraso de alguns segundos até que sejam exibidos no contexto de SQL. Depois de serem exibidos, eles poderão ser consultados, mas não atualizados nem alterados pelos mecanismos SQL que têm acesso a eles.

## <a name="shared-metadata-objects"></a>Objetos de metadados compartilhados

O Spark permite criar bancos de dados, tabelas externas, tabelas gerenciadas e exibições. Como as exibições do Spark exigem um mecanismo do Spark para processar a instrução SQL de definição do Spark e não podem ser processadas por um mecanismo SQL, somente os bancos de dados e as tabelas externas e gerenciadas independentes que usam o formato de armazenamento Parquet são compartilhados com o mecanismo SQL do workspace. As exibições do Spark são compartilhadas apenas entre as instâncias do Pool do Spark.

## <a name="security-model-at-a-glance"></a>Visão rápida do modelo de segurança

Os bancos de dados e as tabelas do Spark, juntamente com as respectivas representações sincronizadas no mecanismo SQL, são protegidos no nível de armazenamento subjacente. Quando a tabela é consultada por um dos mecanismos que o emissor da consulta tem o direito de usar, a entidade de segurança do emissor da consulta é transmitida para os arquivos subjacentes. As permissões são verificadas no nível do sistema de arquivos.

Para obter mais informações, confira [Banco de dados compartilhado do Azure Synapse Analytics](database.md).

## <a name="change-maintenance"></a>Alterar manutenção

Se um objeto de metadados for excluído ou alterado com o Spark, as alterações serão coletadas e propagadas para o pool de SQL sem servidor. A sincronização é assíncrona e as alterações são refletidas no mecanismo SQL após um pequeno atraso.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os bancos de dados de metadados compartilhados do Azure Synapse Analytics](database.md)
- [Saiba mais sobre as tabelas de metadados compartilhados do Azure Synapse Analytics](table.md)

