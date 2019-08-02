---
title: 'VS Code: Conectar e consultar dados no Banco de Dados SQL do Azure | Microsoft Docs'
description: Saiba como se conectar a um Banco de Dados SQL no Azure usando o Visual Studio Code. Em seguida, execute instruções T-SQL (Transact-SQL) para consultar e editar dados.
keywords: conectar-se ao banco de dados sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: d8f12e699c17787d897a7f5ed23eccdbf3659921
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569130"
---
# <a name="quickstart-use-visual-studio-code-to-connect-and-query-an-azure-sql-database"></a>Início Rápido: Use o Visual Studio Code para conectar e consultar um Banco de Dados SQL do Azure

O [Visual Studio Code](https://code.visualstudio.com/docs) é um editor de código gráfico para Linux, macOS e Windows. Ele dá suporte a extensões, incluindo a [extensão mssql](https://aka.ms/mssql-marketplace) para consultar o Microsoft SQL Server, o Banco de Dados SQL do Azure e o SQL Data Warehouse. Neste início rápido, você usará o Visual Studio Code para se conectar a um Banco de Dados SQL do Azure e, em seguida, executar instruções Transact-SQL para consultar, inserir, atualizar e excluir dados.

## <a name="prerequisites"></a>Pré-requisitos

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

## <a name="install-visual-studio-code"></a>Instalar o Visual Studio Code

Certifique-se de instalar o [Visual Studio Code](https://code.visualstudio.com/Download) mais recente e carregar a [extensão mssql](https://aka.ms/mssql-marketplace). Para obter diretrizes sobre como instalar a extensão mssql, confira [Instalar o VS Code](https://docs.microsoft.com/sql/linux/sql-server-linux-develop-use-vscode#install-and-start-visual-studio-code) e [mssql para Visual Studio Code ](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql).

## <a name="configure-visual-studio-code"></a>Configurar o código do Visual Studio

### <a name="mac-os"></a>**Mac OS**

Para o macOS, é necessário instalar o OpenSSL, que é um pré-requisito do .NET Core que a extensão mssql usa. Abra seu terminal e digite os seguintes comandos para instalar o **brew** e o **OpenSSL**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install openssl
mkdir -p /usr/local/lib
ln -s /usr/local/opt/openssl/lib/libcrypto.1.0.0.dylib /usr/local/lib/
ln -s /usr/local/opt/openssl/lib/libssl.1.0.0.dylib /usr/local/lib/
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**

Nenhuma configuração especial é necessária.

### <a name="windows"></a>**Windows**

Nenhuma configuração especial é necessária.

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [Portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

## <a name="set-language-mode-to-sql"></a>Definir o modo de linguagem para SQL

No Visual Studio Code, defina o modo de linguagem como **SQL** para permitir comandos mssql e T-SQL IntelliSense.

1. Abra uma nova janela do Visual Studio Code.

2. Pressione **Ctrl**+**N**. Um novo arquivo de texto sem formatação é aberto.

3. Selecione **Texto sem Formatação** no canto inferior direito da barra de status.

4. No menu suspenso **Selecionar modo de linguagem** que for aberto, selecione **SQL**.

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

Use o Visual Studio Code para estabelecer uma conexão com seu servidor do Banco de Dados SQL do Azure.

> [!IMPORTANT]
> Antes de continuar, verifique se o servidor e as informações de entrada estão prontos. Depois de começar a inserir as informações de perfil da conexão, se você mudar o foco do Visual Studio Code, terá que reiniciar a criação do perfil.

1. No Visual Studio Code, pressione **CTRL+Shift+P** (ou **F1**) para abrir a Paleta de Comandos.

2. Selecione **MS SQL:Connect** e escolha **Enter**.

3. Selecione **Criar perfil de conexão**.

4. Siga os prompts para especificar as propriedades de conexão do novo perfil. Depois de especificar cada valor, escolha **Enter** para continuar.

   | Propriedade       | Valor sugerido | DESCRIÇÃO |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome do servidor** | O nome do servidor totalmente qualificado | Algo como: **mynewserver20170313.database.windows.net**. |
   | **Nome do banco de dados** | mySampleDatabase | O banco de dados ao qual se conectar. |
   | **Autenticação** | Logon do SQL| Este tutorial usa a Autenticação do SQL. |
   | **Nome de usuário** | Nome de usuário | O nome de usuário da conta do administrador de servidor usado para criar o servidor. |
   | **Senha (Logon do SQL)** | Senha | A senha de usuário da conta do administrador de servidor usada para criar o servidor. |
   | **Salvar a Senha?** | Sim ou não | Selecione **Sim** se não desejar inserir a senha toda vez. |
   | **Insira um nome para este perfil** | Um nome do perfil, como **mySampleProfile** | Um perfil salvo acelera sua conexão nos logons subsequentes. |

   Se for bem-sucedido, uma notificação será exibida informando que seu perfil foi criado e está conectado.

## <a name="query-data"></a>Consultar dados

Execute a instrução Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) a seguir para consultar os 20 principais produtos por categoria.

1. Na janela do editor, cole a seguinte consulta SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

2. Pressione **Ctrl**+**Shift**+**E** para executar a consulta e exibir os resultados das tabelas `Product` e `ProductCategory`.

    ![Consultar para recuperar dados de 2 tabelas](./media/sql-database-connect-query-vscode/query.png)

## <a name="insert-data"></a>Inserir dados

Execute a instrução Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) a seguir para adicionar um novo produto à tabela `SalesLT.Product`.

1. Substitua a consulta anterior por esta.

   ```sql
   INSERT INTO [SalesLT].[Product]
        ( [Name]
        , [ProductNumber]
        , [Color]
        , [ProductCategoryID]
        , [StandardCost]
        , [ListPrice]
        , [SellStartDate]
        )
     VALUES
        ('myNewProduct'
        ,123456789
        ,'NewColor'
        ,1
         ,100
         ,100
         ,GETDATE() );
   ```

2. Pressione **Ctrl**+**Shift**+**E** para inserir uma nova linha na tabela `Product`.

## <a name="update-data"></a>Atualizar dados

Execute a instrução Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) a seguir para atualizar o produto adicionado.

1. Substitua a consulta anterior por esta:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Pressione **Ctrl**+**Shift**+**E** para atualizar a linha especificada na tabela `Product`.

## <a name="delete-data"></a>Excluir dados

Execute a instrução Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) a seguir para remover o novo produto.

1. Substitua a consulta anterior por esta:

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Pressione **Ctrl**+**Shift**+**E** para excluir a linha especificada na tabela `Product`.

## <a name="next-steps"></a>Próximas etapas

- Para conectar-se e consultar usando o SQL Server Management Studio, confira [Início Rápido: usar o SQL Server Management Studio para conectar-se a um Banco de Dados SQL do Azure e consultar dados](sql-database-connect-query-ssms.md).
- Para se conectar e consultar usando o portal do Azure, confira [Início Rápido: usar o editor de consulta SQL no portal do Azure para conectar e consultar dados](sql-database-connect-query-portal.md).
- Para obter um artigo da MSDN magazine sobre como usar o Visual Studio Code, veja [Criar um banco de dados IDE com a postagem de blog de extensão MSSQL](https://msdn.microsoft.com/magazine/mt809115).
