---
title: Compatibilidade das ferramentas de banco de dados do Azure para MariaDB drivers e gerenciamento
description: Este artigo descreve o MariaDB drivers e ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/19/2019
ms.openlocfilehash: 7a3d9a5f87a565625052fc54e3ecccc99fd928a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386794"
---
# <a name="mariadb-drivers-and-management-tools-compatible-with-azure-database-for-mariadb"></a>MariaDB drivers e ferramentas de gerenciamento compatíveis com o banco de dados do Azure para MariaDB

Este artigo descreve os drivers e ferramentas de gerenciamento que são compatíveis com o banco de dados do Azure para MariaDB.

## <a name="mariadb-drivers"></a>Drivers do MariaDB

Banco de dados do Azure para MariaDB usa a community edition do servidor MariaDB. Portanto, ele é compatível com uma ampla variedade de drivers e linguagens de programação. API e o protocolo do MariaDB são compatíveis com aqueles usados pelo MySQL. Isso significa que os conectores que trabalham com o MySQL também devem funcionar com MariaDB.

O objetivo é oferecer suporte as três versões mais recentes drivers MariaDB, e os esforços com autores da comunidade de software livre para melhorar constantemente a funcionalidade e a usabilidade dos drivers MariaDB continuam. Uma lista de drivers que foram testadas e consideradas compatíveis com o banco de dados do Azure para MariaDB 10.2 é fornecida na tabela a seguir:

**Driver** | **Links** | **Versões compatíveis** | **Versões incompatíveis** | **Observações**
---|---|---|---|---
PHP | https://secure.php.net/downloads.php | 5.5, 5.6, 7.x | 5,3 | Para a conexão PHP 7.0 com o SSL MySQLi, adicione MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT na cadeia de conexão. <br> ```mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL_DONT_VERIFY_SERVER_CERT);```<br> PDO defina a opção ```PDO::MYSQL_ATTR_SSL_VERIFY_SERVER_CERT``` como false.
.NET | [MySqlConnector no GitHub](https://github.com/mysql-net/MySqlConnector) <br> [Pacote de instalação do Nuget](https://www.nuget.org/packages/MySqlConnector/) | 0.27 e posterior | 0.26.5 e anterior |
Conector MySQL/NET | [Conector MySQL/NET](https://github.com/mysql/mysql-connector-net) | 8.0, 7.0, 6.10 |  | Um bug de codificação pode causar falha em alguns sistemas não - UTF8 Windows nas conexões.
Node.js |  [MySQLjs no GitHub](https://github.com/mysqljs/mysql/) <br> Pacote de instalação do NPM:<br> Executar `npm install mysql` do NPM | 2.15 | 2.14.1 e anterior
GO | https://github.com/go-sql-driver/mysql/releases | 1.3, 1.4 | 1.2 e anterior | Use `allowNativePasswords=true` na cadeia de conexão para a versão 1.3. Versão 1.4 contém uma correção e `allowNativePasswords=true` não é mais necessário.
Python | https://pypi.python.org/pypi/mysql-connector-python | 1.2.3, 2.0, 2.1, 2.2 | 1.2.2 e anterior |
Java | https://downloads.mariadb.org/connector-java/ | 2.1, 2.0, 1.6 | 1.5.5 e anterior |

## <a name="management-tools"></a>Ferramentas de gerenciamento

A vantagem de compatibilidade se estende para as ferramentas de gerenciamento de banco de dados também. Suas ferramentas existentes devem continuar a trabalhar com o banco de dados do Azure para MariaDB, desde que a manipulação de banco de dados opera dentro dos limites das permissões do usuário. Três comuns ferramentas de gerenciamento de banco de dados que foram testadas e consideradas compatíveis com o banco de dados do Azure para MariaDB 10.2 são listadas na tabela a seguir:

| | **MySQL Workbench 6.x e superior** | **Navicat 12** | **PHPMyAdmin 4.x e superior**
---|---|---|---
Criar, atualizar, ler, gravar, excluir | X | X | X
Conexão SSL | X | X | X
Preenchimento automático da consulta SQL | X | X |
Importar e exportar dados | X | X | X
Exportar para vários formatos | X | X | X
Backup e restauração |  | X |
Exibir parâmetros do servidor | X | X | X
Exibir conexões de cliente | X | X | X

## <a name="next-steps"></a>Próximas etapas

- [Solucione problemas de conexão com o Banco de Dados do Azure para MariaDB](howto-troubleshoot-common-connection-issues.md)