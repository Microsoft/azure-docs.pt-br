---
title: Como usar o Ruby para consultar o Banco de Dados SQL do Azure| Microsoft Docs
description: Este tópico mostra como usar o Ruby para criar um programa que se conecta a um Banco de Dados SQL do Azure e consultá-los usando instruções do Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ruby
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 5b47ddc2d865108e03b3c649536bfaa700e4a59d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569112"
---
# <a name="quickstart-use-ruby-to-query-an-azure-sql-database"></a>Início Rápido: Como usar o Ruby para consultar um banco de dados SQL do Azure

Este início rápido demonstra como usar o [Ruby](https://www.ruby-lang.org) para se conectar a um Banco de Dados SQL do Azure e consultar dados com instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisará dos seguintes pré-requisitos:

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
  
- O Ruby e o software relacionado para seu sistema operacional:
  
  - **MacOS**: Instale o Homebrew, o rbenv e o ruby-build, o Ruby, o FreeTDS e o TinyTDS. Confira as Etapas 1.2, 1.3, 1.4, 1.5 e 2.1 em [Criar aplicativos Ruby usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/ruby/mac/).
  
  - **Ubuntu**: Instale os pré-requisitos do Ruby, do rbenv e do ruby-build, do Ruby, do FreeTDS e do TinyTDS. Confira as Etapas 1.2, 1.3, 1.4, 1.5 e 2.1 em [Criar aplicativos Ruby usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu/).
  
  - **Windows**: Instale o Ruby, o Ruby Devkit e o TinyTDS. Confira [Configurar um ambiente de desenvolvimento para desenvolvimento com o Ruby](/sql/connect/ruby/step-1-configure-development-environment-for-ruby-development).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**. 

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu Banco de Dados SQL

1. Em um editor de texto ou de códigos, crie um arquivo chamado *sqltest.rb*.
   
1. Adicione os códigos a seguir. Substitua os valores do Banco de Dados SQL do Azure de `<server>`, `<database>`, `<username>` e `<password>`.
   
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
- [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-design-first-database.md).
- [Repositório GitHub do TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Relatar problemas ou fazer perguntas sobre o TinyTDS](https://github.com/rails-sqlserver/tiny_tds/issues).
- [Ruby Driver para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
