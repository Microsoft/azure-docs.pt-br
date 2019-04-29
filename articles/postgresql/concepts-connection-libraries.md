---
title: Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL
description: Este artigo descreve várias bibliotecas e vários drivers que os desenvolvedores podem usar ao codificar aplicativos para se conectar e consultar o Banco de Dados do Azure para PostgreSQL.
services: postgresql
author: WenJason
ms.author: v-jay
manager: digimobile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
origin.date: 02/28/2018
ms.date: 12/03/2018
ms.openlocfilehash: 0e762a2d7cf82e2957fb276fcea0a20553f719e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60559765"
---
# <a name="connection-libraries-for-azure-database-for-postgresql"></a>Bibliotecas de conexão para o Banco de Dados do Azure para PostgreSQL
Este artigo lista as bibliotecas e os drivers que os desenvolvedores podem usar para desenvolver aplicativos para conexão e consulta do Banco de Dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de linguagem usadas para se conectar ao servidor PostgreSQL são projetos externos distribuídos de forma independente. As bibliotecas listadas têm suporte em plataformas Windows, Linux e Mac para conexão ao Banco de Dados do Azure para PostgreSQL. Vários exemplos de início rápido são listados na seção Próximas etapas.

| **Linguagem** | **Interface do cliente** | **Informações adicionais** | **Baixar** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Em confomridade com a DB API 2.0 | [Baixar](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extensão de banco de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | Cliente puro do JavaScript sem bloqueio | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Driver JDBC do tipo 4 | [Baixar](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interface Ruby | [Baixar](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Driver de postgres Go puro | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Provedor de dados ADO.NET | [Baixar](https://www.microsoft.com/net/) |
| ODBC | [psqlODBC](https://odbc.postgresql.org/) | Driver ODBC | [Baixar](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface primária de linguagem C | Incluso |
| C++ | [libpqxx](http://pqxx.org/) | Interface de C++ com novo estilo | [Baixar](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Próximas etapas
Leia estes guias de início rápido para saber como se conectar e consultar o Banco de Dados do Azure para PostgreSQL usando a linguagem de sua escolha:

[Python](./connect-python.md) | [Node.JS](./connect-nodejs.md) | [Java](./connect-java.md) | [Ruby](./connect-ruby.md) | [PHP](./connect-php.md) | [.NET (C#)](./connect-csharp.md) | [Go](./connect-go.md)
