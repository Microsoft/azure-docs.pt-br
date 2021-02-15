---
title: Bibliotecas de conexões-banco de dados do Azure para PostgreSQL-servidor único
description: Este artigo descreve várias bibliotecas e drivers que você pode usar ao codificar aplicativos para se conectar e consultar o banco de dados do Azure para PostgreSQL-servidor único.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 45081c6ba161686498398f2c4ccae8b4cff4c0d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91704303"
---
# <a name="connection-libraries-for-azure-database-for-postgresql---single-server"></a>Bibliotecas de conexões do banco de dados do Azure para PostgreSQL-servidor único
Este artigo lista as bibliotecas e os drivers que os desenvolvedores podem usar para desenvolver aplicativos para conexão e consulta do Banco de Dados do Azure para PostgreSQL.

## <a name="client-interfaces"></a>Interfaces de cliente
A maioria das bibliotecas de cliente de linguagem usadas para se conectar ao servidor PostgreSQL são projetos externos distribuídos de forma independente. As bibliotecas listadas têm suporte em plataformas Windows, Linux e Mac para conexão ao Banco de Dados do Azure para PostgreSQL. Vários exemplos de início rápido são listados na seção Próximas etapas.

| **Idioma** | **Interface do cliente** | **Informações adicionais** | **Baixar** |
|--------------|----------------------------------------------------------------|-------------------------------------|--------------------------------------------------------------------|
| Python | [psycopg](http://initd.org/psycopg/) | Em confomridade com a DB API 2.0 | [Baixar](http://initd.org/psycopg/download/) |
| PHP | [php-pgsql](https://secure.php.net/manual/en/book.pgsql.php) | Extensão de banco de dados | [Instalar](https://secure.php.net/manual/en/pgsql.installation.php) |
| Node.js | [Pg npm package](https://www.npmjs.com/package/pg) | Cliente puro do JavaScript sem bloqueio | [Instalar](https://www.npmjs.com/package/pg) |
| Java | [JDBC](https://jdbc.postgresql.org/) | Driver JDBC do tipo 4 | [Baixar](https://jdbc.postgresql.org/download.html)  |
| Ruby | [Pg gem](https://deveiate.org/code/pg/) | Interface Ruby | [Baixar](https://rubygems.org/downloads/pg-0.20.0.gem) |
| Go | [Package pq](https://godoc.org/github.com/lib/pq) | Driver de postgres Go puro | [Instalar](https://github.com/lib/pq/blob/master/README.md) |
| C\#/ .NET | [Npgsql](https://www.npgsql.org/) | Provedor de dados ADO.NET | [Baixar](https://www.microsoft.com/net/) |
| ODBCODBC | [psqlODBC](https://odbc.postgresql.org/) | Driver ODBC | [Baixar](https://www.postgresql.org/ftp/odbc/versions/) |
| C | [libpq](https://www.postgresql.org/docs/9.6/static/libpq.html) | Interface primária de linguagem C | Incluso |
| C++ | [libpqxx](http://pqxx.org/) | Interface de C++ com novo estilo | [Baixar](http://pqxx.org/download/software/) |

## <a name="next-steps"></a>Próximas etapas
Leia estes guias de início rápido para saber como se conectar e consultar o Banco de Dados do Azure para PostgreSQL usando a linguagem de sua escolha:

[Python](./connect-python.md)  |  [Node.JS](./connect-nodejs.md)  |  [Java](./connect-java.md)  |  [Ruby](./connect-ruby.md)  |  [Php](./connect-php.md)  |  [.net (C#)](./connect-csharp.md)  |  [Ir](./connect-go.md) para
