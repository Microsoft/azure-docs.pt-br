---
title: Autoajuda do pool de SQL sem servidor
description: Esta seção contém informações que podem ajudar você a solucionar problemas com o pool de SQL sem servidor.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 05/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: c67b0bab554f363b8389c5557eadeac6e4c577a2
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625224"
---
# <a name="self-help-for-serverless-sql-pool"></a>Autoajuda para o pool de SQL sem servidor

Este artigo contém informações sobre como solucionar os problemas mais frequentes com o pool de SQL sem servidor no Azure Synapse Analytics.

## <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>O pool de SQL sem servidor está esmaecido no Synapse Studio

Se o Synapse Studio não puder estabelecer uma conexão com o pool de SQL sem servidor, você observará que o pool de SQL sem servidor está esmaecido ou mostra o status "Offline". Normalmente, esse problema ocorre quando um dos seguintes casos acontece:

1) A rede impede a comunicação com o back-end do Azure Synapse. O caso mais frequente é o bloqueio da porta 1443. Para que o pool de SQL sem servidor funcione, desbloqueie essa porta. Outros problemas também podem impedir que o pool de SQL sem servidor funcione; [acesse o guia de solução de problemas completo para obter mais informações](../troubleshoot/troubleshoot-synapse-studio.md).
2) Você não tem permissões para fazer logon no pool de SQL sem servidor. Para obter acesso, um dos administradores do workspace do Azure Synapse deverá adicionar você à função de administrador do workspace ou do SQL. [Acesse o guia completo sobre controle de acesso para obter mais informações](../security/synapse-workspace-access-control-overview.md).

## <a name="query-fails-because-file-cannot-be-opened"></a>A consulta falha porque o arquivo não pode ser aberto

Se a consulta falhar com o erro 'O arquivo não pode ser aberto porque ele não existe ou está sendo usado por outro processo' e você tiver certeza de que o arquivo existe e não está sendo usado por outro processo, o pool de SQL sem servidor não poderá acessar o arquivo. Esse problema geralmente ocorre porque a sua identidade do Azure Active Directory não tem direitos para acessar o arquivo. Por padrão, o pool de SQL sem servidor tenta acessar o arquivo usando a sua identidade do Azure Active Directory. Para resolver esse problema, você precisa ter os direitos adequados para acessar o arquivo. A maneira mais fácil é conceder a função 'Colaborador de Dados de Blob de Armazenamento' a você mesmo na conta de armazenamento que está tentando consultar. 
- [Acesse o guia completo sobre controle de acesso para armazenamento do Azure Active Directory para obter mais informações](../../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json). 
- [Acesse Controlar o acesso à conta de armazenamento para o pool de SQL sem servidor no Azure Synapse Analytics](develop-storage-files-storage-access-control.md)

## <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>A consulta falha porque não pode ser executada devido às restrições de recursos atuais 

Se a consulta falhar com a mensagem de erro 'Esta consulta não pode ser executada devido às restrições de recurso atuais', o pool de SQL sem servidor não poderá executá-la no momento devido a restrições de recursos: 

- Use tipos de dados de tamanhos razoáveis. Além disso, especifique o esquema de arquivos Parquet para colunas de cadeia de caracteres, pois elas serão VARCHAR(8000) por padrão. 

- Se a consulta for direcionada a arquivos CSV, considere a possibilidade de [criar estatísticas](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- Acesse [Melhores práticas de desempenho do pool de SQL sem servidor](best-practices-sql-on-demand.md) para otimizar a consulta.  

## <a name="create-statement-is-not-supported-in-master-database"></a>Não há suporte para CREATE 'STATEMENT' no banco de dados mestre

Se a consulta falhar com a mensagem de erro:

> "Falha ao executar a consulta. Erro: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT não tem suporte no banco de dados mestre". 

isso significa que o banco de dados mestre no pool de SQL sem servidor não dá suporte para a criação de:
  - Tabelas externas
  - Fontes de dados externas
  - Credenciais no escopo do banco de dados
  - Formatos de arquivo externos

Solução:

  1. Crie um banco de dados de usuário:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Execute a instrução CREATE no contexto de < NOME_DO_BANCO_DE_DADOS>, que falhou anteriormente para o banco de dados mestre. 
  
  Exemplo de criação de formato de arquivo externo:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

## <a name="next-steps"></a>Próximas etapas

Examine os seguintes artigos para saber mais sobre como usar o pool de SQL sem servidor:

- [Consultar um arquivo CSV individual](query-single-csv-file.md)

- [Consultar pastas e vários arquivos CSV](query-folders-multiple-csv-files.md)

- [Consultar arquivos específicos](query-specific-files.md)

- [Consultar arquivos Parquet](query-parquet-files.md)

- [Consultar tipos aninhados Parquet](query-parquet-nested-types.md)

- [Consultar arquivos JSON](query-json-files.md)

- [Criar e usar exibições](create-use-views.md)

- [Criar e usar tabelas externas](create-use-external-tables.md)

- [Armazenar os resultados da consulta no armazenamento](create-external-table-as-select.md)
