---
title: Usar PHP para consultar o Banco de Dados SQL do Azure | Microsoft Docs
description: Como usar o PHP para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-lo usando instruções T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 02/12/2019
ms.openlocfilehash: a7da65438ffd7db6c43cf2ede7cc52378af056f1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569218"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Início Rápido: Usar PHP para consultar um banco de dados SQL do Azure

Este artigo demonstra como usar o [PHP](https://php.net/manual/en/intro-whatis.php) para se conectar a um Banco de dados SQL do Azure. Depois você pode usar as instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este modelo, é necessário atender aos seguintes pré-requisitos:

- Um banco de dados SQL do Azure. Você pode usar um destes guias de início rápido para criar e, em seguida, configurar um banco de dados no Banco de Dados SQL do Azure:

  || Banco de dados individual | Instância gerenciada |
  |:--- |:--- |:---|
  | Criar| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configurar | [Regra de firewall de IP no nível do servidor](sql-database-server-level-firewall-rule.md)| [Conectividade de uma VM](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividade do local](sql-database-managed-instance-configure-p2s.md)
  |Carregar dados|Adventure Works carregado por guia de início rápido|[Restaurar o Wide World Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restaurar ou importar o Adventure Works por meio do arquivo [BACPAC](sql-database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar o banco de dados do Adventure Works. Com uma instância gerenciada, você deve importar o banco de dados do Adventure Works para um banco de dados de instância ou modificar os scripts deste artigo para usar o banco de dados da Wide World Importers.

- O software relacionado ao PHP foi instalado no seu sistema operacional:

  - **MacOS**, instalar o PHP, o driver ODBC e, então, instalar o Driver do PHP para SQL Server. Confira [Etapas 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Linux**, instalar o PHP, o driver ODBC e, então, instalar o Driver do PHP para SQL Server. Confira [Etapas 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

  - **Windows**: instale o PHP para IIS Express e Chocolatey, depois instale o driver ODBC e SQLCMD. Confira as [etapas 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

## <a name="add-code-to-query-database"></a>Adicionar código ao banco de dados de consulta

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

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)

- [Drivers PHP Microsoft para SQL Server](https://github.com/Microsoft/msphpsql/)

- [Relatar problemas ou fazer perguntas](https://github.com/Microsoft/msphpsql/issues)

- [Exemplo de lógica de repetição: Conectar-se de forma resiliente ao SQL com PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
