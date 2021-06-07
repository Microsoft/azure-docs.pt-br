---
title: 'Problemas conhecidos: Migrar do Oracle para Banco de Dados do Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações online do Oracle para o Banco de Dados do Azure para PostgreSQL – servidor único usando o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 1b331f8e0af452937028c63fba123cb92f57a6b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94962409"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Problemas conhecidos/limitações de migração com migrações online da Oracle para o BD do Azure para PostgreSQL – servidor único

Os problemas e as limitações conhecidos associados às migrações online do Oracle para o Banco de Dados do Azure para PostgreSQL – servidor único são descritos nas seções a seguir.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versões do Oracle com suporte como um banco de dados de origem

O Serviço de Migração de Banco de Dados do Azure dá suporte à conexão com:

- Oracle versão 10g, 11g e 12c.
- Oracle Enterprise, Standard, Express e Personal Edition.

O Serviço de Migração de Banco de Dados do Azure não dá suporte à conexão com CDBs (Bancos de Dados de Contêiner) multilocatário.

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versões do PostgreSQL com suporte como um banco de dados de destino

O Serviço de Migração de Banco de Dados do Azure dá suporte a migrações para o Banco de Dados do Azure para PostgreSQL – servidor único, versões 9.5, 9.6, 10 e 11 do servidor. Confira o artigo [versões do banco de dados PostgreSQL com suporte](../postgresql/concepts-supported-versions.md) para obter informações atuais sobre o suporte de versão no Banco de Dados do Azure para PostgreSQL – servidor único.

## <a name="datatype-limitations"></a>Limitações de tipo de dados

Os seguintes tipos de tipo de dados **não** serão migrados:

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Tabelas aninhadas
- Tipos de dados definidos pelo usuário
- Observações
- Colunas virtuais
- Exibições materializadas com base na coluna ROWID

Além disso, as colunas BLOB/CLOB vazias são mapeadas para NULL no destino.

## <a name="lob-limitations"></a>Limitações de LOB

- Quando o modo LOB de tamanho limitado estiver habilitado, os LOBs vazios na origem do Oracle serão replicados como valores NULL.
- Não há suporte para nomes de objeto longos (mais de 30 bytes).
- Os dados na coluna LONG e LONG RAW não podem exceder 64k. Todos os dados com mais de 64k serão truncados.
- Somente no Oracle 12, não há suporte para alterações nas colunas LOB (migradas).
- Não há suporte para atualizações para colunas XMLTYPE e LOB (migradas).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

- O usuário deve ter o privilégio DBA no Servidor Oracle.
- As alterações de dados resultantes de operações de partição/subpartição (ADD, DROP, EXCHANGE e TRUNCATE) não serão migradas e poderão causar os seguintes erros:
  - Para operações ADD, as atualizações e exclusões nos dados adicionados podem retornar um aviso "0 linha afetada".
  - Para operações DROP e TRUNCATE, novas inserções podem resultar em erros de "duplicatas".
  - Para operações EXCHANGE, um aviso "0 linha afetada" e erros de "duplicatas" podem ocorrer.
- Tabelas cujos nomes contêm apóstrofos não podem ser replicadas.