---
title: Gerar uma cadeia de conexão com o PowerShell – Banco de Dados do Azure para MySQL
description: Este artigo fornece um exemplo do Azure PowerShell de como gerar uma cadeia de conexão para se conectar ao Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9f960d32dcf1f359327dccc01eeb06825cc3a062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541463"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Como gerar uma cadeia de conexão do Banco de Dados do Azure para MySQL com o PowerShell

Este artigo demonstra como gerar uma cadeia de conexão para um Banco de Dados do Azure para o servidor MySQL. Você pode usar uma cadeia de conexão para se conectar a um Banco de Dados do Azure para MySQL em muitos diferentes aplicativos.

## <a name="requirements"></a>Requisitos

Este artigo usa os recursos criados no seguinte guia como ponto de partida:

* [Início Rápido: Criar um Banco de Dados do Azure para o servidor MySQL usando o PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O cmdlet `Get-AzMySqlConnectionString` é usado para gerar uma cadeia de conexão a fim de conectar aplicativos ao Banco de Dados do Azure para MySQL. O exemplo a seguir retorna a cadeia de conexão para um cliente PHP de **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Personalizar parâmetros do servidor do Banco de Dados do Azure para MySQL usando o PowerShell](howto-configure-server-parameters-using-powershell.md)
