---
title: Banco de dados compartilhado
description: O Azure Synapse Analytics fornece um modelo de metadados compartilhado em que a criação de um banco de dados no Pool do Apache Spark sem servidor o tornará acessível do pool de SQL sem servidor e dos mecanismos de pool de SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 93602e522338166abac98c3e4a198e1aff392d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934961"
---
# <a name="azure-synapse-analytics-shared-database"></a>Banco de dados compartilhado do Azure Synapse Analytics

O Azure Synapse Analytics permite que os diferentes mecanismos computacionais do workspace compartilhem bancos de dados e tabelas. Atualmente, os bancos de dados e as tabelas Parquet criadas nos pools do Apache Spark são compartilhados automaticamente com o mecanismo de pool de SQL sem servidor.

Um banco de dados criado com um trabalho do Spark ficará visível com esse mesmo nome para todos os pools do Spark, atuais e futuros, no workspace, incluindo o mecanismo do pool de SQL sem servidor. Você não pode adicionar objetos personalizados (tabelas externas, exibições, procedimentos) diretamente neste banco de dados replicado usando o pool de SQL sem servidor.

O banco de dados padrão do Spark, chamado `default`, também ficará visível no contexto do pool de SQL sem servidor como um banco de dados chamado `default`.

Como os banco de dados são sincronizados com o pool de SQL sem servidor de maneira assíncrona, haverá um atraso até que eles sejam exibidos.

## <a name="manage-a-spark-created-database"></a>Gerenciar um banco de dados criado pelo Spark

Use o Spark para gerenciar bancos de dados criados pelo Spark. Por exemplo, exclua-o por meio de um trabalho do Pool do Spark e crie tabelas nele por meio do Spark.

Se você criar objetos em um banco de dados criado pelo Spark usando o pool de SQL sem servidor ou tentar remover o banco de dados, a operação será bem sucedida. Porém, o banco de dados do Spark original não será alterado.

## <a name="how-name-conflicts-are-handled"></a>Como os conflitos de nome são tratados

Se o nome de um banco de dados do Spark entrar em conflito com o nome de outro banco de dados existente no pool de SQL sem servidor, um sufixo será acrescento ao banco de dados do Spark no pool de SQL sem servidor. O sufixo no pool de SQL sem servidor é `_<workspace name>-ondemand-DefaultSparkConnector`.

Por exemplo, se um banco de dados do Spark chamado `mydb` for criado no workspace `myws` do Azure Synapse e já existir um banco de dados no pool de SQL sem servidor com esse nome, então o banco de dados do Spark no pool de SQL sem servidor precisará ser referenciado usando o nome `mydb_myws-ondemand-DefaultSparkConnector`.

> [!CAUTION]
> Cuidado: você não deverá usar uma dependência nesse comportamento.

## <a name="security-model"></a>Modelo de segurança

Os bancos de dados e as tabelas do Spark, juntamente com as respectivas representações sincronizadas no mecanismo SQL, serão protegidos no nível de armazenamento subjacente.

A entidade de segurança que cria um banco de dados é considerada o proprietário desse banco de dados e tem todos os direitos ao banco de dados e aos respectivos objetos.

Para permitir a uma entidade de segurança, como um usuário ou um grupo de segurança, acesso a um banco de dados, forneça as permissões de arquivo e pasta POSIX apropriadas nas pastas e nos arquivos subjacentes do diretório `warehouse`. 

Por exemplo, para que uma entidade de segurança leia uma tabela em um banco de dados, todas as pastas começando na pasta do banco de dados do diretório `warehouse` precisam ter as permissões `X` e `R` atribuídas a essa entidade de segurança. Além disso, os arquivos (como os arquivos de dados subjacentes da tabela) exigem permissões `R`. 

Se uma entidade de segurança exigir a capacidade de criar ou remover objetos de um banco de dados, serão necessárias permissões `W` adicionais nas pastas e nos arquivos da pasta `warehouse`.

## <a name="examples"></a>Exemplos

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Criar um banco de dados do Spark e se conectar a ele com o pool de SQL sem servidor

Primeiro, crie um banco de dados do Spark chamado `mytestdb` usando um cluster Spark que você já criou no workspace. Você pode fazer isso, por exemplo, usando um notebook C# do Spark com a seguinte instrução .NET para Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Após um pequeno atraso, você poderá ver o banco de dados no pool de SQL sem servidor. Por exemplo, execute a instrução a seguir no pool de SQL sem servidor.

```sql
SELECT * FROM sys.databases;
```

Verifique se `mytestdb` está incluída nos resultados.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os metadados compartilhados do Azure Synapse Analytics](overview.md)
- [Saiba mais sobre as tabelas de metadados compartilhados do Azure Synapse Analytics](table.md)
