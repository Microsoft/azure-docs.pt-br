---
title: Guias de início rápido para se conectar e consultar no Banco de Dados SQL do Azure | Microsoft Docs
description: Guias de início rápido de banco de dados SQL do Azure mostrando como se conectar e fazer consultas em um banco de dados do SQL Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 63ed2c5c334aef8f6281ee34ec4ed6e47ca8521a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569108"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Inícios Rápidos: Conexão e consulta do Banco de Dados SQL do Azure

O documento a seguir inclui contém links para exemplos do Azure que mostram como se conectar e consultar um banco de dados SQL do Azure. Ele também fornece algumas recomendações para TLS.

## <a name="quickstarts"></a>Guia de início rápido

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|Este guia rápido demonstra como usar o SSMS para conectar um banco de dados SQL do Azure, em seguida, usar instruções Transact-SQL para consultar, inserir, atualizar e excluir os dados no banco de dados.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|Este início rápido demonstra como usar o Azure Data Studio para se conectar a um banco de dados SQL do Azure e usar instruções T-SQL (Transact-SQL) para criar o TutorialDB usado nos tutoriais do Azure Data Studio.|
|[Portal do Azure](sql-database-connect-query-portal.md)|Este guia rápido demonstra como usar o editor de consultas para se conectar a um banco de dados SQL e depois usar instruções do Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados.|
|[Visual Studio Code](sql-database-connect-query-vscode.md)|Este início rápido demonstra como usar o Visual Studio Code para conectar um banco de dados SQL do Azure e depois usar as instruções Transact-SQL para consultar, inserir, atualizar e excluir os dados no banco de dados.|
|[.NET com Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|Este início rápido demonstra como usar o .NET Framework a fim de criar um programa C# com o Visual Studio para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[.NET Core](sql-database-connect-query-dotnet-core.md)|Este início rápido demonstra como usar o .NET Core no Windows/Linux/macOS a fim de criar um programa C# para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[Go](sql-database-connect-query-go.md)|Este guia de início rápido demonstra como usar Go para se conectar a um Banco de Dados SQL do Azure. As instruções Transact-SQL para consultar e modificar dados também são demonstradas.|
|[Java](sql-database-connect-query-java.md)|Este guia de início rápido demonstra como usar o Java para conectar um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[Node.js](sql-database-connect-query-nodejs.md)|Este início rápido demonstra como usar o Node.js a fim de criar um programa para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[PHP](sql-database-connect-query-php.md)|Este início rápido demonstra como usar o PHP a fim de criar um programa para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[Python](sql-database-connect-query-python.md)|Este início rápido demonstra como usar o Python para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados. |
|[Ruby](sql-database-connect-query-ruby.md)|Este início rápido demonstra como usar o Ruby a fim de criar um programa para se conectar a um banco de dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|[R](sql-database-connect-query-r.md)|Este início rápido demonstra como usar R com os Serviços do Machine Learning do Banco de Dados SQL do Azure para criar um programa e conectar-se a um Banco de Dados SQL do Azure e usar instruções Transact-SQL para consultar dados.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>Considerações sobre TLS para conectividade do Banco de Dados SQL
O TLS é usado por todos os drivers que a Microsoft fornece ou dá suporte para conexão ao Banco de Dados SQL do Azure. Nenhuma configuração especial é necessária. Para todas as conexões com o SQL Server ou o Banco de Dados SQL do Azure, recomendamos que todos os aplicativos definam as seguintes configurações ou seus equivalentes:

 - **Encrypt = On**
 - **TrustServerCertificate = Off**

Alguns sistemas usam palavras-chave diferentes, porém equivalentes, para essas palavras-chave de configuração. Essas configurações garantem que o driver do cliente verifica a identidade do certificado TLS recebida do servidor.

Também recomendamos que você desabilite o TLS 1.1 e 1.0 no cliente, caso precise cumprir o PCI-DSS (Payment Card Industry – Data Security Standard).

Drivers não Microsoft não podem usar o TLS por padrão. Isso pode ser um fator importante ao se conectar ao Banco de Dados SQL do Azure. Aplicativos com drivers inseridos podem não permitir o controle dessas configurações de conexão. Recomendamos que você examine a segurança desses drivers e aplicativos antes de usá-los em sistemas que interagem com os dados confidenciais.

## <a name="libraries"></a>Bibliotecas

Você pode usar várias estruturas e bibliotecas para se conectar ao Banco de Dados SQL do Azure. Confira nossos [Tutoriais de introdução](https://aka.ms/sqldev) para conhecer rapidamente as linguagens de programação como C#, Java, Node.js, PHP e Python. Em seguida, crie um aplicativo usando o SQL Server no Linux ou Windows ou o Docker no macOS.

A tabela a seguir lista as bibliotecas de conectividade ou *drivers* que os aplicativos cliente podem usar de uma variedade de idiomas para conectar e usar o SQL Server em execução no local ou na nuvem. Você pode usá-los no Linux, o Windows ou o Docker e usá-los para se conectar ao Banco de Dados SQL do Azure e o SQL Data Warehouse do Azure. 

| Linguagem | Plataforma | Recursos adicionais | Baixar | Introdução |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET para SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-for-sql-server) | [Baixar](https://www.microsoft.com/net/download/) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC Driver para SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Baixar](https://go.microsoft.com/fwlink/?linkid=852460) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [Driver do SQL de PHP para SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Baixar](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Driver Node.js para SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Instalar](https://msdn.microsoft.com/library/mt652094.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Driver Python SQL](https://msdn.microsoft.com/library/mt652092.aspx) | Opções de instalação: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Introdução](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Ruby Driver para SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Instalar](https://msdn.microsoft.com/library/mt711041.aspx) | [Introdução](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Driver ODBC Microsoft para SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Baixar](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

A tabela a seguir lista exemplos de estruturas de mapeamento relacional de objeto (ORM) e frameworks da web que os aplicativos cliente podem usar com o SQL Server em execução no local ou na nuvem. Você pode usar os frameworks no Linux, o Windows ou o Docker e usá-los para se conectar ao Banco de Dados SQL e o SQL Data Warehouse. 

| Linguagem | Plataforma | ORM(s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre a arquitetura de conectividade, consulte [Arquitetura de conectividade do Banco de Dados SQL do Azure](sql-database-connectivity-architecture.md).
- Encontre [Drivers do SQL Server](https://msdn.microsoft.com/library/mt654049.aspx) que são usados para conexão em aplicativos cliente
- Conectar ao Banco de Dados SQL:
  - [Conectar-se ao Banco de Dados SQL usando .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Conectar-se ao Banco de Dados SQL usando o PHP](sql-database-connect-query-php.md) 
  - [Conectar-se ao Banco de Dados SQL usando Node.js](sql-database-connect-query-nodejs.md) 
  - [Conectar-se ao Banco de Dados SQL usando o Java](sql-database-connect-query-java.md) 
  - [Conectar-se ao Banco de Dados SQL usando Phyton](sql-database-connect-query-python.md)
  - [Conectar-se ao Banco de Dados SQL usando o Ruby](sql-database-connect-query-ruby.md)
- Exemplos de código de lógica de repetição:
  - [Connect resiliently to SQL with ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Connect resiliently to SQL with PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-to-sql-with-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
