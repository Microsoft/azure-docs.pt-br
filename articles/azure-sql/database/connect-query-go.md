---
title: Usar Ir para a consulta
description: Use o Go para criar um programa que se conecta a um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure e executa consultas.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: go
ms.topic: quickstart
author: David-Engel
ms.author: sstein
ms.reviewer: MightyPen
ms.date: 04/14/2021
ms.openlocfilehash: 1a479572ba8dbd68ccc072fce32446abcc9b873c
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517773"
---
# <a name="quickstart-use-golang-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Início Rápido: Usar o Golang para consultar um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste guia de início rápido, você usará a linguagem de programação [Golang](https://godoc.org/github.com/denisenkom/go-mssqldb) para se conectar a um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure. Em seguida, você executará instruções Transact-SQL para consultar e modificar dados. O [Golang](https://golang.org/) é uma linguagem de programação de software livre que facilita a criação de softwares simples, confiáveis e eficientes.  

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure. Você pode usar um dos seguintes guias de início rápido para criar um banco de dados:

  || Banco de Dados SQL | Instância Gerenciada do SQL | SQL Server na VM do Azure |
  |:--- |:--- |:---|:---|
  | **Criar**| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  | **Criar** | [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  | **Criar** | [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | **Configurar** | [Regra de firewall de IP no nível do servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de uma VM](../managed-instance/connect-vm-instance-configure.md)|
  | **Configurar** ||[Conectividade do local](../managed-instance/point-to-site-p2s-configure.md) | [Conectar-se a uma Instância do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |**Carregar dados**|Adventure Works carregado por guia de início rápido|[Restaurar o Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar o Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  | **Carregar dados** ||Restaurar ou importar o Adventure Works de um arquivo [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar o Adventure Works de um arquivo [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > Os scripts deste artigo são escritos para usar o banco de dados do Adventure Works. Com uma Instância Gerenciada de SQL, importe o banco de dados Adventure Works para um banco de dados de instância ou altere os scripts deste artigo para usar o banco de dados da Wide World Importers.

- O Golang e o software relacionado para seu sistema operacional instalado:

  - **macOS**: Instale o Homebrew e o Golang. Veja a [Etapa 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/mac/).
  - **Ubuntu**:  Instale o Golang. Veja a [Etapa 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/ubuntu/).
  - **Windows**: Instale o Golang. Veja a [Etapa 1.2](https://www.microsoft.com/sql-server/developer-get-started/go/windows/).

## <a name="get-server-connection-information"></a>Obter informações de conexão do servidor

Obtenha as informações de conexão necessárias para se conectar ao banco de dados. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **Instâncias Gerenciadas de SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado ao lado de **Nome do servidor** para obter um banco de dados no Banco de Dados SQL do Azure ou o nome do servidor totalmente qualificado (ou endereço IP) ao lado de **Host** para obter uma instância gerenciada de SQL do Azure ou um SQL Server na VM do Azure. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

> [!NOTE]
> Para obter informações de conexão do SQL Server na VM do Azure, confira [Conectar-se a uma Instância do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-golang-project-and-dependencies"></a>Criar o projeto Golang e as dependências

1. Do terminal, crie uma nova pasta de projeto chamada **SqlServerSample**. 

   ```bash
   mkdir SqlServerSample
   ```

2. Navegue até **SqlServerSample** e instale o driver do SQL Server para Go.

   ```bash
   cd SqlServerSample
   go get github.com/denisenkom/go-mssqldb
   ```

## <a name="create-sample-data"></a>Criar dados de exemplo

1. Em um editor de texto, crie um arquivo chamado **CreateTestData.sql** na pasta **SqlServerSample**. No arquivo, cole esse código T-SQL, que cria um esquema e uma tabela e insere algumas linhas.

   ```sql
   CREATE SCHEMA TestSchema;
   GO

   CREATE TABLE TestSchema.Employees (
     Id       INT IDENTITY(1,1) NOT NULL PRIMARY KEY,
     Name     NVARCHAR(50),
     Location NVARCHAR(50)
   );
   GO

   INSERT INTO TestSchema.Employees (Name, Location) VALUES
     (N'Jared',  N'Australia'),
     (N'Nikita', N'India'),
     (N'Tom',    N'Germany');
   GO

   SELECT * FROM TestSchema.Employees;
   GO
   ```

2. Use `sqlcmd` para se conectar ao banco de dados e executar o script SQL do Azure recém-criado. Substitua os valores apropriados para o seu servidor, banco de dados, nome de usuário e senha.

   ```bash
   sqlcmd -S <your_server>.database.windows.net -U <your_username> -P <your_password> -d <your_database> -i ./CreateTestData.sql
   ```

## <a name="insert-code-to-query-the-database"></a>Inserir código para consultar o banco de dados

1. Crie um arquivo chamado **sample.go** na pasta **SqlServerSample**.

2. No arquivo, cole este código. Adicione os valores para o servidor, o banco de dados, o nome de usuário e a senha. Este exemplo usa os [métodos de contexto](https://golang.org/pkg/context/) do Golang para verificar se há uma conexão ativa.

   ```go
   package main

   import (
       _ "github.com/denisenkom/go-mssqldb"
       "database/sql"
       "context"
       "log"
       "fmt"
       "errors"
   )

   var db *sql.DB

   var server = "<your_server.database.windows.net>"
   var port = 1433
   var user = "<your_username>"
   var password = "<your_password>"
   var database = "<your_database>"

   func main() {
       // Build connection string
       connString := fmt.Sprintf("server=%s;user id=%s;password=%s;port=%d;database=%s;",
           server, user, password, port, database)

       var err error

       // Create connection pool
       db, err = sql.Open("sqlserver", connString)
       if err != nil {
           log.Fatal("Error creating connection pool: ", err.Error())
       }
       ctx := context.Background()
       err = db.PingContext(ctx)
       if err != nil {
           log.Fatal(err.Error())
       }
       fmt.Printf("Connected!\n")

       // Create employee
       createID, err := CreateEmployee("Jake", "United States")
       if err != nil {
           log.Fatal("Error creating Employee: ", err.Error())
       }
       fmt.Printf("Inserted ID: %d successfully.\n", createID)

       // Read employees
       count, err := ReadEmployees()
       if err != nil {
           log.Fatal("Error reading Employees: ", err.Error())
       }
       fmt.Printf("Read %d row(s) successfully.\n", count)

       // Update from database
       updatedRows, err := UpdateEmployee("Jake", "Poland")
       if err != nil {
           log.Fatal("Error updating Employee: ", err.Error())
       }
       fmt.Printf("Updated %d row(s) successfully.\n", updatedRows)

       // Delete from database
       deletedRows, err := DeleteEmployee("Jake")
       if err != nil {
           log.Fatal("Error deleting Employee: ", err.Error())
       }
       fmt.Printf("Deleted %d row(s) successfully.\n", deletedRows)
   }

   // CreateEmployee inserts an employee record
   func CreateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()
       var err error

       if db == nil {
           err = errors.New("CreateEmployee: db is null")
           return -1, err
       }

       // Check if database is alive.
       err = db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := `
         INSERT INTO TestSchema.Employees (Name, Location) VALUES (@Name, @Location);
         select isNull(SCOPE_IDENTITY(), -1);
       `

       stmt, err := db.Prepare(tsql)
       if err != nil {
          return -1, err
       }
       defer stmt.Close()

       row := stmt.QueryRowContext(
           ctx,
           sql.Named("Name", name),
           sql.Named("Location", location))
       var newID int64
       err = row.Scan(&newID)
       if err != nil {
           return -1, err
       }

       return newID, nil
   }

   // ReadEmployees reads all employee records
   func ReadEmployees() (int, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("SELECT Id, Name, Location FROM TestSchema.Employees;")

       // Execute query
       rows, err := db.QueryContext(ctx, tsql)
       if err != nil {
           return -1, err
       }

       defer rows.Close()

       var count int

       // Iterate through the result set.
       for rows.Next() {
           var name, location string
           var id int

           // Get values from row.
           err := rows.Scan(&id, &name, &location)
           if err != nil {
               return -1, err
           }

           fmt.Printf("ID: %d, Name: %s, Location: %s\n", id, name, location)
           count++
       }

       return count, nil
   }

   // UpdateEmployee updates an employee's information
   func UpdateEmployee(name string, location string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("UPDATE TestSchema.Employees SET Location = @Location WHERE Name = @Name")

       // Execute non-query with named parameters
       result, err := db.ExecContext(
           ctx,
           tsql,
           sql.Named("Location", location),
           sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }

   // DeleteEmployee deletes an employee from the database
   func DeleteEmployee(name string) (int64, error) {
       ctx := context.Background()

       // Check if database is alive.
       err := db.PingContext(ctx)
       if err != nil {
           return -1, err
       }

       tsql := fmt.Sprintf("DELETE FROM TestSchema.Employees WHERE Name = @Name;")

       // Execute non-query with named parameters
       result, err := db.ExecContext(ctx, tsql, sql.Named("Name", name))
       if err != nil {
           return -1, err
       }

       return result.RowsAffected()
   }
   ```

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o comando a seguir.

   ```bash
   go run sample.go
   ```

2. Verifique a saída.

   ```text
   Connected!
   Inserted ID: 4 successfully.
   ID: 1, Name: Jared, Location: Australia
   ID: 2, Name: Nikita, Location: India
   ID: 3, Name: Tom, Location: Germany
   ID: 4, Name: Jake, Location: United States
   Read 4 row(s) successfully.
   Updated 1 row(s) successfully.
   Deleted 1 row(s) successfully.
   ```

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure](design-first-database-tutorial.md)
- [Driver do Golang para SQL Server](https://github.com/denisenkom/go-mssqldb)
- [Relatar problemas ou fazer perguntas](https://github.com/denisenkom/go-mssqldb/issues)

