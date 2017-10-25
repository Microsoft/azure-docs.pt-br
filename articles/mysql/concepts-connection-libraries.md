---
title: "Bibliotecas de conexão para o Banco de Dados do Azure para MySQL | Microsoft Docs"
description: Este artigo lista cada biblioteca ou driver que os programas de cliente podem usar ao se conectarem ao Banco de Dados do Azure para MySQL.
services: mysql
author: mswutao
ms.author: wutao
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 06/08/2017
ms.openlocfilehash: e746f28829063f8437ad408c4681f90aec6c0e0a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connection-libraries-for-azure-database-for-mysql"></a>Bibliotecas de conexão para o Banco de Dados do Azure para MySQL
Este tópico lista cada biblioteca ou driver que os programas de cliente podem usar ao se conectarem ao Banco de Dados do Azure para MySQL.

## <a name="client-interfaces"></a>Interfaces de cliente
O MySQL oferece conectividade de driver de banco de dados padrão para usar o MySQL com aplicativos e ferramentas compatíveis com ODBC e JDBC padrões do setor. Qualquer sistema que funciona com ODBC ou JDBC pode usar o MySQL.

| **Linguagem** | **Plataforma** | **Recursos adicionais** | **Baixar** |
| :----------- | :------------| :-----------------------| :------------|
| PHP | Windows, Linux | [Driver nativo do MySQL para PHP - mysqlnd](https://dev.mysql.com/downloads/connector/php-mysqlnd/) | [Baixar](http://php.net/downloads.php) |
| ODBC | Plataformas Windows, Linux, Mac OS X e Unix | [Guia do desenvolvedor do MySQL Connector/ODBC](https://dev.mysql.com/doc/connector-odbc/en/) | [Baixar](https://dev.mysql.com/downloads/connector/odbc/) |
| ADO.NET | Windows | [Guia do desenvolvedor do MySQL Connector/Net](https://dev.mysql.com/doc/connector-net/en/) | [Baixar](https://dev.mysql.com/downloads/connector/net/) |
| JDBC | Independente de plataforma | [Guia do desenvolvedor do MySQL Connector/J 5.1](https://dev.mysql.com/doc/connector-j/5.1/en/) | [Baixar](https://dev.mysql.com/downloads/connector/j/) |
| Node.js | Windows, Linux, Mac OS X | [sidorares/node-mysql2](https://github.com/sidorares/node-mysql2/tree/master/documentation) | [Baixar](https://github.com/sidorares/node-mysql2) |
| Python | Windows, Linux, Mac OS X | [Guia do desenvolvedor do MySQL Connector/Python](https://dev.mysql.com/doc/connector-python/en/) | [Baixar](https://dev.mysql.com/downloads/connector/python/) |
| C++ | Windows, Linux, Mac OS X | [Guia do desenvolvedor do MySQL Connector/C++](https://dev.mysql.com/doc/connector-cpp/en/) | [Baixar](https://dev.mysql.com/downloads/connector/python/) |
| C | Windows, Linux, Mac OS X | [Guia do desenvolvedor do MySQL Connector/C](https://dev.mysql.com/doc/connector-c/en/) | [Baixar](https://dev.mysql.com/downloads/connector/c/)
| Perl | Plataformas Windows, Linux, Mac OS X e Unix | [DBD::MySQL](https://metacpan.org/pod/DBD::mysql) | [Baixar](https://metacpan.org/pod/DBD::mysql) |


## <a name="next-steps"></a>Próximas etapas
[Visão geral do Banco de Dados do Azure para MySQL](./overview.md)
[Conceitos de servidor no Banco de Dados para MySQL](./concepts-servers.md)
