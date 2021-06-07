---
title: Gerar uma cadeia de conexão com o PowerShell – Banco de Dados do Azure para PostgreSQL
description: Este artigo apresenta um exemplo do Azure PowerShell de como gerar uma cadeia de conexão para se conectar ao Banco de Dados do Azure para PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91708162"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Como gerar uma cadeia de conexão do Banco de Dados do Azure para PostgreSQL com o PowerShell

Este artigo demonstra como gerar uma cadeia de conexão para um servidor com Banco de Dados do Azure para PostgreSQL. Você pode usar uma cadeia de conexão para se conectar a um Banco de Dados do Azure para PostgreSQL em vários aplicativos diferentes.

## <a name="requirements"></a>Requisitos

Este artigo usa os recursos criados no seguinte guia como ponto de partida:

* [Início Rápido: Criar um servidor de Banco de Dados do Azure para PostgreSQL usando o PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O `Get-AzPostgreSqlConnectionString`cmdlet é usado para gerar uma cadeia de conexão a fim de conectar aplicativos ao Banco de Dados do Azure para PostgreSQL. O exemplo a seguir retorna a cadeia de conexão para um cliente PHP de **mydemoserver**.

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
> [Personalizar parâmetros do servidor do Banco de Dados do Azure para PostgreSQL usando o PowerShell](howto-configure-server-parameters-using-powershell.md)
