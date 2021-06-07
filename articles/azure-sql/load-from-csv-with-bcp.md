---
title: Carregar dados do arquivo CSV em um banco de dados (BCP)
description: Para um tamanho de dados pequeno, use bcp para importar dados para o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 09ae46ec6455b6998bcf4da5648d2ceaef4d5b19
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644790"
---
# <a name="load-data-from-csv-into-azure-sql-database-or-sql-managed-instance-flat-files"></a>Carregar dados do CSV no banco de dados SQL do Azure ou no SQL Instância Gerenciada (arquivos simples)
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Você pode usar o utilitário de linha de comando bcp para importar dados de um arquivo CSV para o Azure SQL Database ou o Instância Gerenciada do Microsoft Azure.

## <a name="before-you-begin"></a>Antes de começar

### <a name="prerequisites"></a>Pré-requisitos

Para completar as etapas neste artigo, você precisa:

* Um banco de dados no banco de dados SQL do Azure
* O utilitário de linha de comando bcp instalado
* O utilitário de linha de comando sqlcmd instalado

Você pode baixar os utilitários bcp e sqlcmd da [documentação do Microsoft sqlcmd](/sql/tools/sqlcmd-utility?view=sql-server-ver15&preserve-view=true).

### <a name="data-in-ascii-or-utf-16-format"></a>Dados em formato ASCII ou UTF-16

Se você estiver experimentando este tutorial com seus próprios dados, eles precisarão usar a codificação ASCII ou UTF-16, já que bcp não oferece suporte a UTF-8.

## <a name="1-create-a-destination-table"></a>1. criar uma tabela de destino

Defina uma tabela no Banco de Dados SQL como a tabela de destino. As colunas na tabela devem corresponder aos dados em cada linha do arquivo de dados.

Para criar uma tabela, abra um prompt de comando e use sqlcmd.exe para executar o comando a seguir:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```

## <a name="2-create-a-source-data-file"></a>2. criar um arquivo de dados de origem

Abra o Bloco de Notas e copie as seguintes linhas de dados em um novo arquivo de texto. Em seguida, salve esse arquivo em seu diretório temporário local, c:\Temp\DimDate2.txt. Esses dados estão no formato ASCII.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

(Opcional) Para exportar seus próprios dados de um banco de dados do SQL Server, abra um prompt de comando e execute o comando a seguir. Substitua TableName, ServerName, DatabaseName, Username e Password por suas próprias informações.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ,
```

## <a name="3-load-the-data"></a>3. carregar os dados

Para carregar os dados, abra um prompt de comando e execute o comando a seguir, substituindo os valores de Nome do Servidor, Nome do Banco de Dados, Nome de Usuário e Senha por suas próprias informações.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Use este comando para verificar se os dados foram carregados corretamente

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

O resultado deve parecer com o seguinte:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

## <a name="next-steps"></a>Próximas etapas

Para migrar um banco de dados do SQL Server, confira [Migração de banco de dados do SQL Server](database/migrate-to-database-from-sql-server.md).

<!--MSDN references-->
[bcp]: /sql/tools/bcp-utility
[CREATE TABLE syntax]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
