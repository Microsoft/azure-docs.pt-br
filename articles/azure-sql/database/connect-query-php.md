---
title: Usar o PHP para pesquisas
description: Como usar o PHP não só para criar um programa que se conecta a um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure, mas também para consultá-lo usando as instruções T-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 05/29/2020
ms.custom: sqldbrb=2
ms.openlocfilehash: e0ad8e9e71a0ee2c23412d535fe79955edb39ba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99576643"
---
# <a name="quickstart-use-php-to-query-a-database-in-azure-sql-database"></a>Início Rápido: Usar o PHP para consultar um banco de dados no Banco de Dados SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este artigo demonstra como usar o [PHP](https://php.net/manual/en/intro-whatis.php) para se conectar a um banco de dados no Banco de Dados SQL do Azure e na Instância Gerenciada de SQL do Azure. Depois você pode usar as instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure. Você pode usar um dos seguintes guias de início rápido para criar e configurar um banco de dados:

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




  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar o banco de dados do Adventure Works. Com uma Instância Gerenciada de SQL, importe o banco de dados Adventure Works para um banco de dados de instância ou altere os scripts deste artigo para usar o banco de dados da Wide World Importers.

- O software relacionado ao PHP foi instalado no seu sistema operacional:

  - **macOS**: instale o PHP, o driver ODBC e o driver do PHP para SQL Server. Confira [Etapas 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, instalar o PHP, o driver ODBC e, então, instalar o Driver do PHP para SQL Server. Confira [Etapas 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**: instale o PHP e os drivers PHP e, depois, o driver ODBC e o SQLCMD. Confira as [etapas 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-server-connection-information"></a>Obter informações de conexão do servidor

Obtenha as informações de conexão necessárias para se conectar ao banco de dados no Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **Instâncias Gerenciadas de SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado ao lado de **Nome do servidor** para obter um banco de dados no Banco de Dados SQL do Azure ou o nome do servidor totalmente qualificado (ou endereço IP) ao lado de **Host** para obter uma instância gerenciada de SQL do Azure ou um SQL Server na VM do Azure. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

> [!NOTE]
> Para obter informações de conexão do SQL Server na VM do Azure, confira [Conectar-se a uma Instância do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="add-code-to-query-the-database"></a>Adicionar código para consultar o banco de dados

1. Em seu editor de texto favorito, crie um novo arquivo, *sqltest.php*.  

1. Substitua o conteúdo pelo código a seguir. Depois adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
       );
       //Establishes the connection
       $conn = sqlsrv_connect($serverName, $connectionOptions);
       $tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
            FROM [SalesLT].[ProductCategory] pc
            JOIN [SalesLT].[Product] p
            ON pc.productcategoryid = p.productcategoryid";
       $getResults= sqlsrv_query($conn, $tsql);
       echo ("Reading data from table" . PHP_EOL);
       if ($getResults == FALSE)
           echo (sqlsrv_errors());
       while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
        echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
       }
       sqlsrv_free_stmt($getResults);
   ?>
   ```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o aplicativo.

   ```bash
   php sqltest.php
   ```

1. Verifique se as 20 linhas superiores são retornadas, depois feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure](design-first-database-tutorial.md)
- [Drivers PHP Microsoft para SQL Server](https://github.com/Microsoft/msphpsql/)
- [Relatar problemas ou fazer perguntas](https://github.com/Microsoft/msphpsql/issues)
- [Exemplo de lógica de repetição: Conectar-se de maneira resiliente ao Azure SQL com o PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
