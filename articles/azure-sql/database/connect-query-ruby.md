---
title: Usar Ruby em consultas
description: Este tópico mostra como usar o Ruby para criar um programa que se conecta a um banco de dados e consultá-lo usando instruções do Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 0434a99d28b4f71594e0ca9ce312087dee5b0ee2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92672530"
---
# <a name="quickstart-use-ruby-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Início Rápido: Usar o Ruby para consultar um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Este início rápido demonstra como usar o [Ruby](https://www.ruby-lang.org) para se conectar a um banco de dados e consultar dados com instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

- Um banco de dados. Você pode usar um dos seguintes guias de início rápido para criar e configurar o banco de dados:

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
  
- O Ruby e o software relacionado para seu sistema operacional:
  
  - **macOS**: Instale o Homebrew, o rbenv e o ruby-build, o Ruby, o FreeTDS e o TinyTDS. Confira as Etapas 1.2, 1.3, 1.4, 1.5 e 2.1 em [Criar aplicativos Ruby usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instale os pré-requisitos do Ruby, do rbenv e do ruby-build, do Ruby, do FreeTDS e do TinyTDS. Confira as Etapas 1.2, 1.3, 1.4, 1.5 e 2.1 em [Criar aplicativos Ruby usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Instale o Ruby, o Ruby Devkit e o TinyTDS. Confira [Configurar um ambiente de desenvolvimento para desenvolvimento com o Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-server-connection-information"></a>Obter informações de conexão do servidor

Obtenha as informações de conexão necessárias para se conectar a um banco de dados no Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **Instâncias Gerenciadas de SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado ao lado de **Nome do servidor** para obter um banco de dados no Banco de Dados SQL do Azure ou o nome do servidor totalmente qualificado (ou endereço IP) ao lado de **Host** para obter uma instância gerenciada de SQL do Azure ou um SQL Server na VM do Azure. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

> [!NOTE]
> Para obter informações de conexão do SQL Server na VM do Azure, confira [Conectar-se a uma instância do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database-in-azure-sql-database"></a>Criar código para consultar seu banco de dados no Banco de Dados SQL do Azure

1. Em um editor de texto ou de códigos, crie um arquivo chamado *sqltest.rb*.

1. Adicione o código seguinte: Substitua os valores do seu banco de dados no Banco de Dados SQL do Azure para `<server>`, `<database>`, `<username>` e `<password>`.

   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 

   ```ruby
   require 'tiny_tds'
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   client = TinyTds::Client.new username: username, password: password, 
       host: server, port: 1433, database: database, azure: true
   
   puts "Reading data from table"
   tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
           FROM [SalesLT].[ProductCategory] pc
           JOIN [SalesLT].[Product] p
           ON pc.productcategoryid = p.productcategoryid"
   result = client.execute(tsql)
   result.each do |row|
       puts row
   end
   ```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o comando a seguir:

   ```bash
   ruby sqltest.rb
   ```
   
1. Verifique se as 20 primeiras linhas de Categoria/Produto do banco de dados são retornadas. 

## <a name="next-steps"></a>Próximas etapas
- [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure](design-first-database-tutorial.md)
- [Repositório GitHub do TinyTDS](https://github.com/rails-sqlserver/tiny_tds)
- [Relatar problemas ou fazer perguntas sobre o TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues)
- [Ruby Driver para SQL Server](/sql/connect/ruby/ruby-driver-for-sql-server/)