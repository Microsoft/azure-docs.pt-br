---
title: Compatibilidade de drivers e ferramentas-banco de dados do Azure para MySQL
description: Esse artigo descreve os drivers MySQL e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: cba9615a8c76387b593109bc2174ab4718f1750a
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609286"
---
# <a name="mysql-drivers-and-management-tools-compatible-with-azure-database-for-mysql"></a>Drivers MySQL e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
Esse artigo descreve os drivers e as ferramentas de gerenciamento compatíveis com o Banco de Dados do Azure para MySQL.

## <a name="mysql-drivers"></a>Drivers MySQL
O Banco de Dados do Azure para MySQL usa a edição de comunidade mais popular do mundo do banco de dados MySQL. Portanto, ele é compatível com uma ampla variedade de drivers e linguagens de programação. A meta é dar suporte às três versões mais recentes de drivers MySQL e os esforços com autores da comunidade de software livre para melhorar constantemente a funcionalidade e usabilidade dos drivers MySQL continuam. Na tabela a seguir, há uma lista de drivers que foram testados e que são compatíveis com o Banco de Dados do Azure para MySQL 5.6 e 5.7:

| **Linguagem de programação** | **Driver** | **Links** | **Versões compatíveis** | **Versões incompatíveis** | **Observações** |
| :----------------------- | :--------- | :-------- | :---------------------- | :------------------------ | :-------- |
| PHP | mysqli, pdo_mysql, mysqlnd | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5,3 | Para a conexão PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de conexão. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO defina a opção ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` como false.|
| .NET | Conector MySQL assíncrono para .NET | https://github.com/mysql-net/MySqlConnector <br> [Pacote de instalação do Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e posterior | 0.26.5 e anterior | |
| .NET | Conector MySQL/NET | https://github.com/mysql/mysql-connector-net | 6.6.3, 7,0, 8,0 |  | Um bug de codificação pode causar falha em alguns sistemas não - UTF8 Windows nas conexões. |
| Node.js | mysqljs | https://github.com/mysqljs/mysql/ <br> Pacote de instalação do NPM:<br> Executar `npm install mysql` do NPM | 2.15 | 2.14.1 e anterior | |
| Node.js | nó-mysql2 | https://github.com/sidorares/node-mysql2 | 1.3.4 + | | |
| Go | Acessar o driver do MySQL | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1.2 e anterior | Use `allowNativePasswords=true` na cadeia de conexão para a versão 1,3. A versão 1,4 contém uma correção e `allowNativePasswords=true` não é mais necessária. |
| Python | Conector do MySQL/Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2,0, 2,1, 2,2, use 8.0.16 + com MySQL 8,0  | 1.2.2 e anterior | |
| Python | PyMySQL | https://pypi.org/project/PyMySQL/ | 0.7.11, 0.8.0, 0.8.1, 0.9.3 + | 0.9.0-0.9.2 (regressão em web2py) | |
| Java | Conector do MariaDB/J | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e anterior | | 
| Java | MySQL Connector/J | https://github.com/mysql/mysql-connector-j | 5.1.21 +, use 8.0.17 + com MySQL 8,0 | 5.1.20 e abaixo | |
| C | Conector do MySQL/C (libmysqlclient) | https://dev.mysql.com/doc/refman/5.7/en/c-api-implementations.html | 6.0.2 + | | |
| C | Conector do MySQL/ODBC (MyODBC) | https://github.com/mysql/mysql-connector-odbc | 3.51.29 + | | |
| C++ | Conector do MySQL/C++ | https://github.com/mysql/mysql-connector-cpp | 1.1.9 + | 1.1.3 e abaixo | | 
| C++ | MySQL + +| https://github.com/tangentsoft/mysqlpp | 3.2.3 + | | |
| Ruby | mysql2 | https://github.com/brianmario/mysql2 | 0.4.10 + | | |
| R | RMySQL | https://github.com/rstats-db/RMySQL | 0.10.16 + | | |
| Swift | MySQL-Swift | https://github.com/novi/mysql-swift | 0.7.2 + | | |
| Swift | vapor/MySQL | https://github.com/vapor/mysql-kit | 2.0.1 + | | |

## <a name="management-tools"></a>Ferramentas de gerenciamento
A vantagem de compatibilidade se estende para as ferramentas de gerenciamento de banco de dados também. Suas ferramentas existentes devem continuar trabalhando com o Banco de Dados do Azure para MySQL, contanto que a manipulação do banco de dados seja operada dentro dos limites das permissões de usuário. Três ferramentas comuns de gerenciamento de banco de dados que foram testadas e identificadas como compatíveis com o Banco de Dados do Azure para MySQL 5.6 e 5.7 estão listadas na tabela a seguir:

|                                     | **MySQL Workbench 6.x e superior** | **Navicat 12** | **PHPMyAdmin 4.x e superior** | **dbForge Studio para MySQL 9,0** |
| :---------------------------------- | :----------------------------- | :------------- | :-------------------------| :------------------------------- |
| **Criar, atualizar, ler, gravar, excluir** | X | X | X | X |
| **Conexão SSL** | X | X | X | X |
| **Preenchimento automático da consulta SQL** | X | X |  | X |
| **Importar e exportar dados** | X | X | X | X |
| **Exportar para vários formatos** | X | X | X | X |
| **Backup e restauração** |  | X |  | X |
| **Exibir parâmetros do servidor** | X | X | X | X |
| **Exibir conexões de cliente** | X | X | X | X |

## <a name="next-steps"></a>Próximas etapas

- [Solucionar problemas de conexão no Banco de Dados do Azure para MySQL](howto-troubleshoot-common-connection-issues.md)
