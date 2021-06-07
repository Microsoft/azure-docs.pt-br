---
title: Usar o Node.js para consultar um banco de dados
description: Como usar o Node.js não só para criar um programa que se conecta a um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure, mas também para consultá-lo usando as instruções T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: seo-javascript-september2019, seo-javascript-october2019, sqldbrb=2, devx-track-js
ms.openlocfilehash: 44530577972839aacb803d1722fa97716088fa0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325429"
---
# <a name="quickstart-use-nodejs-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Início Rápido: Usar o Node.js para consultar um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste guia de início rápido, você usará o Node.js para se conectar a um banco de dados e consultar os dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | Ação | Banco de Dados SQL | Instância Gerenciada do SQL | SQL Server na VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall de IP no nível do servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de uma VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade do local](../managed-instance/point-to-site-p2s-configure.md) | [Conectar-se a uma Instância do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Adventure Works carregado por guia de início rápido|[Restaurar o Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar o Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar o Adventure Works de um arquivo [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar o Adventure Works de um arquivo [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||


- Software relacionado ao [Node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Instale o Homebrew, o Node.js, o driver ODBC e o SQLCMD usando as etapas **1.2** e **1.3** em [Criar aplicativos Node.js usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Instale o Node.js, o driver ODBC e o SQLCMD usando as etapas **1.2** e **1.3** em [Criar aplicativos Node.js usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Instale o Chocolatey, o Node.js, o driver ODBC e o SQLCMD usando as etapas **1.2** e **1.3** em [Criar aplicativos Node.js usando o SQL Server no Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar o banco de dados do **Adventure Works**.

> [!NOTE]
> Você pode optar por usar uma instância gerenciada de SQL do Azure.
>
> Para a criação e a configuração, use o [portal do Azure](../managed-instance/instance-create-quickstart.md), o [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) ou a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) e configure a conectividade [local](../managed-instance/point-to-site-p2s-configure.md) ou de [VM](../managed-instance/connect-vm-instance-configure.md).
>
> Para carregar dados, confira [restaurar com o BACPAC](database-import.md) com o arquivo [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou [restaurar o banco de dados do Wide World Importers](../managed-instance/restore-sample-database-quickstart.md).

## <a name="get-server-connection-information"></a>Obter informações de conexão do servidor

Obtenha as informações de conexão necessárias para se conectar ao banco de dados no Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Acesse a página **Bancos de Dados SQL** ou **Instâncias Gerenciadas de SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado ao lado de **Nome do servidor** para obter um banco de dados no Banco de Dados SQL do Azure ou o nome do servidor totalmente qualificado (ou endereço IP) ao lado de **Host** para obter uma instância gerenciada de SQL do Azure ou um SQL Server na VM do Azure. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

> [!NOTE]
> Para obter informações de conexão do SQL Server na VM do Azure, confira [Conectar-se ao SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-the-project"></a>Criar o projeto

Abra um prompt de comando e crie uma pasta chamada *sqltest*. Abra a pasta que você criou e execute o seguinte comando:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-the-database"></a>Adicionar código para consultar o banco de dados

1. Em seu editor de texto favorito, crie um novo arquivo, *sqltest.js*.

1. Substitua o conteúdo pelo código a seguir. Depois adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });

      connection.execSql(request);
    }
    ```

> [!NOTE]
> O exemplo de código usa o banco de dados de exemplo **AdventureWorksLT** no Banco de Dados SQL do Azure.

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o programa.

    ```bash
    node sqltest.js
    ```

1. Verifique se as 20 linhas superiores são retornadas, depois feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Driver do Microsoft Node.js para SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Conectar e consultar no Windows/Linux/macOS com [.NET core](connect-query-dotnet-core.md), [Visual Studio Code](connect-query-vscode.md) ou [SSMS](connect-query-ssms.md) (somente Windows)

- [Introdução ao .NET Core no Windows/Linux/macOS usando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli)

- Criar seu primeiro banco de dados no Banco de Dados SQL do Azure usando o [.NET](design-first-database-csharp-tutorial.md) ou o [SSMS](design-first-database-tutorial.md)
