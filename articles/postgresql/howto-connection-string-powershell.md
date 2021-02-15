---
title: Gerar uma cadeia de conexão com o PowerShell-banco de dados do Azure para PostgreSQL
description: Este artigo fornece um exemplo de Azure PowerShell para gerar uma cadeia de conexão para se conectar ao banco de dados do Azure para PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91708162"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Como gerar uma cadeia de conexão do banco de dados do Azure para PostgreSQL com o PowerShell

Este artigo demonstra como gerar uma cadeia de conexão para um servidor de banco de dados do Azure para PostgreSQL. Você pode usar uma cadeia de conexão para se conectar a um banco de dados do Azure para PostgreSQL de vários aplicativos diferentes.

## <a name="requirements"></a>Requisitos

Este artigo usa os recursos criados no seguinte guia como ponto de partida:

* [Início rápido: criar um servidor de banco de dados do Azure para PostgreSQL usando o PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O `Get-AzPostgreSqlConnectionString` cmdlet é usado para gerar uma cadeia de conexão para conectar aplicativos ao banco de dados do Azure para PostgreSQL. O exemplo a seguir retorna a cadeia de conexão para um cliente PHP de **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Os valores válidos para o parâmetro `Client` incluem:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Personalizar os parâmetros de servidor do banco de dados do Azure para PostgreSQL usando o PowerShell](howto-configure-server-parameters-using-powershell.md)
