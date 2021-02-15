---
title: Compatibilidade de drivers e ferramentas – banco de dados do Azure para MariaDB
description: Este artigo descreve os drivers MariaDB e as ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: e1d544004d4361d11b40d8016f38f8bdd01bf5c5
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98659862"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>Drivers MariaDB e ferramentas de gerenciamento compatíveis com o banco de dados do Azure para MariaDB

Este artigo descreve os drivers e as ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MariaDB.

## <a name="mariadb-drivers"></a>Drivers MariaDB

O banco de dados do Azure para MariaDB usa a Community Edition do MariaDB Server. Portanto, ele é compatível com uma ampla variedade de drivers e linguagens de programação. A API e o protocolo do MariaDB são compatíveis com os usados pelo MySQL. Isso significa que os conectores que funcionam com o MySQL também devem funcionar com o MariaDB.

O objetivo é dar suporte às três versões mais recentes do MariaDB drivers e esforços com autores da comunidade de software livre para melhorar constantemente a funcionalidade e a usabilidade dos drivers do MariaDB continuar. Uma lista de drivers que foram testados e considerados compatíveis com o banco de dados do Azure para MariaDB 10,2 é fornecida na tabela a seguir:

**Driver** | **Links** | **Versões compatíveis** | **Versões incompatíveis** | **Observações**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5,3 | Para a conexão PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de conexão. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO defina a opção ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` como false.
.NET | [MySqlConnector no GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacote de instalação do Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e posterior | 0.26.5 e anterior |
Conector MySQL/NET | [Conector MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Um bug de codificação pode causar falha em alguns sistemas não - UTF8 Windows nas conexões.
Node.js |  [MySQLjs no GitHub](https://github.com/mysqljs/mysql/) <br> Pacote de instalação do NPM:<br> Executar `npm install mysql` do NPM | 2.15 | 2.14.1 e anterior
GO | https://github.com/go-sql-driver/mysql/releases | 1,3, 1,4 | 1.2 e anterior | Use `allowNativePasswords=true` na cadeia de conexão para a versão 1,3. A versão 1,4 contém uma correção e `allowNativePasswords=true` não é mais necessária.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e anterior |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e anterior |

## <a name="management-tools"></a>Ferramentas de gerenciamento

A vantagem de compatibilidade se estende para as ferramentas de gerenciamento de banco de dados também. Suas ferramentas existentes devem continuar a trabalhar com o banco de dados do Azure para MariaDB, desde que a manipulação do banco de dados opere dentro dos limites de permissões de usuário. Três ferramentas comuns de gerenciamento de banco de dados que foram testadas e encontradas para serem compatíveis com o banco de dados do Azure para MariaDB 10,2 estão listadas na tabela a seguir:

| Ação | **MySQL Workbench 6.x e superior** | **Navicat 12** | **PHPMyAdmin 4.x e superior**
---|---|---|---
Criar, atualizar, ler, gravar, excluir | X | X | X
Conexão SSL | X | X | X
Preenchimento automático da consulta SQL | X | X |
Importação e exportação de dados | X | X | X
Exportar para vários formatos | X | X | X
Backup e restauração |  | X |
Exibir parâmetros do servidor | X | X | X
Exibir conexões de cliente | X | X | X

## <a name="next-steps"></a>Próximas etapas

- [Solucione problemas de conexão com o Banco de Dados do Azure para MariaDB](howto-troubleshoot-common-connection-issues.md)