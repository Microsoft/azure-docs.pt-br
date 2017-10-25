---
title: "Bibliotecas de conexão para Banco de Dados SQL | Microsoft Docs"
description: "Fornece links para downloads de módulos que permitem a conexão ao SQL Server e Banco de Dados SQL de uma ampla variedade de linguagens de programação do cliente. Os módulos são lançados pela comunidade ou pela Microsoft."
services: sql-database
documentationcenter: 
author: MightyPen
manager: jhubbard
editor: genemi
ms.assetid: 13d899d3-cf46-4e4d-8919-cf4b41ca836d
ms.service: sql-database
ms.custom: develop apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: genemi
ms.openlocfilehash: 082abf57b139b9f7d44774dce3a80e20b97f0e3b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connectivity-libraries-and-frameworks-for-microsoft-sql-server"></a>Estruturas e bibliotecas de conectividade para Microsoft SQL Server

Confira nossos [Tutoriais de introdução](http://aka.ms/sqldev) para rapidamente começar a trabalhar com linguagens de programação como C#, Java, Node.js, PHP e Python e criar um aplicativo usando SQL Server no Linux ou Windows ou Docker no macOS.

A tabela a seguir lista as bibliotecas de conectividade ou *drivers* que aplicativos cliente podem usar de uma variedade de linguagens para se conectar e usar o Microsoft SQL Server em execução local ou na nuvem, no Linux, no Windows ou no Docker e também para o Banco de Dados SQL e ao SQL Data Warehouse do Azure. 

| Linguagem | Plataforma | Recursos adicionais | Baixar | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Baixar](https://www.microsoft.com/net/download/) | [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver para SQL Server](http://msdn.microsoft.com/library/mt484311.aspx) | [Baixar](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver do SQL de PHP para SQL Server](http://msdn.microsoft.com/library/dn865013.aspx) | Sistema operacional: <br/> \* [Windows](https://www.microsoft.com/download/details.aspx?id=20098) <br/> \* [Linux](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) <br/> \* [macOS](https://github.com/Microsoft/msphpsql/tree/dev#install-unix) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/php/ubuntu)
| Node.js | Windows, Linux, macOS | [Node.js Driver para SQL Server](http://msdn.microsoft.com/library/mt652093.aspx) | [Instalar](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL Driver](http://msdn.microsoft.com/library/mt652092.aspx) | Opções de instalação: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](http://msdn.microsoft.com/library/mt763257.aspx) |  [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby Driver para SQL Server](http://msdn.microsoft.com/library/mt691981.aspx) | [Instalar](https://msdn.microsoft.com/library/mt711041.aspx) | [Introdução](https://www.microsoft.com/en-us/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Driver ODBC Microsoft para SQL Server](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) | [Baixar](https://msdn.microsoft.com/en-us/library/mt654048(v=sql.1).aspx) |  

A tabela a seguir lista alguns exemplos das estruturas de ORM (Mapeamento Objeto-Relacional) e estruturas de Web que os aplicativos cliente podem usar com o Microsoft SQL Server em execução local ou na nuvem, no Linux, no Windows ou no Docker e também para o Banco de Dados SQL do Azure e ao SQL Data Warehouse do Azure. 

| Linguagem | Plataforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/en-us/ef)<br>[Entity Framework Core](https://docs.microsoft.com/en-us/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](http://hibernate.org/orm)|
| PHP | Windows, Linux | [Laravel (Eloquent)](https://laravel.com/docs/5.0/eloquent) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](http://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](http://rubyonrails.org/) |

## <a name="related-links"></a>Links relacionados
- [SQL Server Drivers](http://msdn.microsoft.com/library/mt654049.aspx) (Drivers do SQL Server) para se conectar de aplicativos cliente
- [Conectar-se ao Banco de Dados SQL usando .NET (C#)](sql-database-connect-query-dotnet.md)
- [Conectar-se ao Banco de Dados SQL usando o PHP](sql-database-connect-query-php.md)
- [Conectar-se ao Banco de Dados SQL usando Node.js](sql-database-connect-query-nodejs.md)
- [Conectar-se ao Banco de Dados SQL usando o Java](sql-database-connect-query-java.md)
- [Conectar-se ao Banco de Dados SQL usando Phyton](sql-database-connect-query-python.md)
- [Conectar-se ao Banco de Dados SQL usando o Ruby](sql-database-connect-query-ruby.md)
