---
title: 'Início Rápido: Conectar-se usando C# – Banco de Dados do Azure para MySQL'
description: Este guia de início rápido fornece um exemplo de código em C# (.NET) que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-csharp
ms.devlang: csharp
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 3b90d8819b5d327c3ccd143257198c7ec8538f03
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535819"
---
# <a name="quickstart-use-net-c-to-connect-and-query-data-in-azure-database-for-mysql"></a>Início Rápido: Usar .NET (C#) para se conectar e consultar dados no Banco de Dados do Azure para MySQL

Este guia de início rápido demonstra como se conectar a um Banco de Dados do Azure para MySQL usando um aplicativo C#. Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. 

## <a name="prerequisites"></a>Pré-requisitos
Para este início rápido você precisa:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free).
- Criar um servidor individual de Banco de Dados do Azure para MySQL usando o [portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md) <br/> ou a [CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md) se ainda não tiver um.
- Com base em se você está usando o acesso público ou privado, conclua **UMA** das ações abaixo para habilitar a conectividade.

|Ação| Método de conectividade|Guia de instruções|
|:--------- |:--------- |:--------- |
| **Configurar regras de firewall** | Público | [Portal](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
| **Configurar Ponto de Extremidade de Serviço** | Público | [Portal](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)| 
| **Configurar link privado** | Privados | [Portal](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) | 

- [Criar um banco de dados e um usuário não administrador](./howto-create-users.md)

[Está com problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)

## <a name="create-a-c-project"></a>Criar um projeto C#
Em um prompt de comando, execute:

```
mkdir AzureMySqlExample
cd AzureMySqlExample
dotnet new console
dotnet add package MySqlConnector
```

## <a name="get-connection-information"></a>Obter informações de conexão
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para MySQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no portal do Azure, clique em **Todos os recursos** e pesquise o servidor que você criou (como **mydemoserver**).
3. Clique no nome do servidor.
4. No painel **Visão Geral** do servidor, anote o **Nome do servidor** e **Nome de logon do administrador do servidor**. Se você esquecer sua senha, também poderá redefini-la nesse painel.
 :::image type="content" source="./media/connect-csharp/1_server-overview-name-login.png" alt-text="Nome do servidor do Banco de Dados do Azure para MySQL":::

## <a name="step-1-connect-and-insert-data"></a>Etapa 1: conectar e inserir dados
Use o código a seguir para conectar-se e carregar os dados usando instruções SQL `CREATE TABLE` e `INSERT INTO`. O código usa os métodos da classe `MySqlConnection`:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma conexão com o MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para definir a propriedade CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) para executar os comandos de banco de dados. 

Substitua os parâmetros `Server`, `Database`, `UserID` e `Password` com os valores que você especificou quando criou o servidor e o banco de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlCreate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DROP TABLE IF EXISTS inventory;";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished dropping table (if existed)");

                    command.CommandText = "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";
                    await command.ExecuteNonQueryAsync();
                    Console.WriteLine("Finished creating table");

                    command.CommandText = @"INSERT INTO inventory (name, quantity) VALUES (@name1, @quantity1),
                        (@name2, @quantity2), (@name3, @quantity3);";
                    command.Parameters.AddWithValue("@name1", "banana");
                    command.Parameters.AddWithValue("@quantity1", 150);
                    command.Parameters.AddWithValue("@name2", "orange");
                    command.Parameters.AddWithValue("@quantity2", 154);
                    command.Parameters.AddWithValue("@name3", "apple");
                    command.Parameters.AddWithValue("@quantity3", 100);

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows inserted={0}", rowCount));
                }

                // connection will be closed by the 'using' block
                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Está com problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)


## <a name="step-2-read-data"></a>Etapa 2: Ler dados

Use o código a seguir para conectar-se e carregar os dados usando uma instrução SQL `SELECT`. O código usa a classe `MySqlConnection` com os métodos:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma conexão com o MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para definir a propriedade CommandText.
- [ExecuteReaderAsync()](/dotnet/api/system.data.common.dbcommand.executereaderasync) para executar os comandos de banco de dados. 
- [ReadAsync()](/dotnet/api/system.data.common.dbdatareader.readasync#System_Data_Common_DbDataReader_ReadAsync) para acessar os registros nos resultados. Em seguida, o código usa GetInt32 e GetString para analisar os valores do registro.


Substitua os parâmetros `Server`, `Database`, `UserID` e `Password` com os valores que você especificou quando criou o servidor e o banco de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlRead
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "SELECT * FROM inventory;";

                    using (var reader = await command.ExecuteReaderAsync())
                    {
                        while (await reader.ReadAsync())
                        {
                            Console.WriteLine(string.Format(
                                "Reading from table=({0}, {1}, {2})",
                                reader.GetInt32(0),
                                reader.GetString(1),
                                reader.GetInt32(2)));
                        }
                    }
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

[Está com problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)

## <a name="step-3-update-data"></a>Etapa 3: Atualizar dados
Use o código a seguir para conectar-se e carregar os dados usando uma instrução SQL `UPDATE`. O código usa a classe `MySqlConnection` com os métodos:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma conexão com o MySQL. 
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para definir a propriedade CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) para executar os comandos de banco de dados. 


Substitua os parâmetros `Server`, `Database`, `UserID` e `Password` com os valores que você especificou quando criou o servidor e o banco de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlUpdate
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "UPDATE inventory SET quantity = @quantity WHERE name = @name;";
                    command.Parameters.AddWithValue("@quantity", 200);
                    command.Parameters.AddWithValue("@name", "banana");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows updated={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```
[Está com problemas? Fale conosco](https://aka.ms/mysql-doc-feedback)

## <a name="step-4-delete-data"></a>Etapa 4: Excluir dados
Use o código a seguir para conectar-se e excluir os dados usando uma instrução SQL `DELETE`. 

O código usa a classe `MySqlConnection` com os métodos:
- [OpenAsync()](/dotnet/api/system.data.common.dbconnection.openasync#System_Data_Common_DbConnection_OpenAsync) para estabelecer uma conexão com o MySQL.
- [CreateCommand()](/dotnet/api/system.data.common.dbconnection.createcommand) para definir a propriedade CommandText.
- [ExecuteNonQueryAsync()](/dotnet/api/system.data.common.dbcommand.executenonqueryasync) para executar os comandos de banco de dados. 


Substitua os parâmetros `Server`, `Database`, `UserID` e `Password` com os valores que você especificou quando criou o servidor e o banco de dados. 

```csharp
using System;
using System.Threading.Tasks;
using MySqlConnector;

namespace AzureMySqlExample
{
    class MySqlDelete
    {
        static async Task Main(string[] args)
        {
            var builder = new MySqlConnectionStringBuilder
            {
                Server = "YOUR-SERVER.mysql.database.azure.com",
                Database = "YOUR-DATABASE",
                UserID = "USER@YOUR-SERVER",
                Password = "PASSWORD",
                SslMode = MySqlSslMode.Required,
            };

            using (var conn = new MySqlConnection(builder.ConnectionString))
            {
                Console.WriteLine("Opening connection");
                await conn.OpenAsync();

                using (var command = conn.CreateCommand())
                {
                    command.CommandText = "DELETE FROM inventory WHERE name = @name;";
                    command.Parameters.AddWithValue("@name", "orange");

                    int rowCount = await command.ExecuteNonQueryAsync();
                    Console.WriteLine(String.Format("Number of rows deleted={0}", rowCount));
                }

                Console.WriteLine("Closing connection");
            }

            Console.WriteLine("Press RETURN to exit");
            Console.ReadLine();
        }
    }
}
```

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar todos os recursos usados durante este guia de início rápido, exclua o grupo de recursos usando o seguinte comando:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando o portal](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [Gerenciar o servidor de Banco de Dados do Azure para MySQL usando a CLI](./how-to-manage-single-server-cli.md)

[Não conseguiu encontrar o que estava procurando? Fale conosco.](https://aka.ms/mysql-doc-feedback)
