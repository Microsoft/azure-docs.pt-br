---
title: Usar o .NET Core para se conectar a um banco de dados e consultá-lo
description: Este tópico mostra como usar o .NET Core não só para criar um programa que se conecta a um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure, mas também para consultá-lo usando as instruções Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=2, devx-track-csharp
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 1d25f43ef5a694d8b94710055bf1be72a7fcb45c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705200"
---
# <a name="quickstart-use-net-core-c-to-query-a-database"></a>Início Rápido: Usar o .NET Core (C#) para consultar um banco de dados
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Neste guia de início rápido, você usará o [.NET Core](https://www.microsoft.com/net/) e o código C# para se conectar a um banco de dados. Em seguida, você executará uma instrução Transact-SQL para consultar dados.

> [!TIP]
> O módulo do Microsoft Learn a seguir ajuda você a aprender gratuitamente a [Desenvolver e configurar um aplicativo ASP.NET que consulta um banco de dados no Banco de Dados SQL do Azure](/learn/modules/develop-app-that-queries-azure-sql/)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [.NET Core para seu sistema operacional](https://www.microsoft.com/net/core) instalado.
- Um banco de dados no qual você pode executar a consulta. 

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]
  
## <a name="create-a-new-net-core-project"></a>Criar um novo projeto do .NET Core

1. Abra um prompt de comando e crie uma pasta chamada **sqltest**. Navegue até essa pasta e execute este comando.

    ```cmd
    dotnet new console
    ```

    Esse comando cria os arquivos de projeto do aplicativo, incluindo um arquivo de código C# inicial (**Program.cs**), um arquivo de configuração XML (**sqltest.csproj**) e os binários necessários.

2. Em um editor de texto, abra **sqltest.csproj** e cole o seguinte XML entre as marcas `<Project>`. Esse XML adiciona `System.Data.SqlClient` como uma dependência.

    ```xml
    <ItemGroup>
        <PackageReference Include="System.Data.SqlClient" Version="4.6.0" />
    </ItemGroup>
    ```

## <a name="insert-code-to-query-the-database-in-azure-sql-database"></a>Inserir código para consultar o banco de dados no Banco de Dados SQL do Azure

1. Em um editor de texto, abra **Program.cs**.

2. Substitua o conteúdo pelo código a seguir e adicione os valores apropriados para seu servidor, banco de dados, nome de usuário e senha.

> [!NOTE]
> Para usar uma cadeia de conexão do ADO.NET, substitua as 4 linhas no código de configuração de servidor, banco de dados, nome de usuário e senha pela linha abaixo. Na cadeia de caracteres, defina seu nome de usuário e senha.
>
>    `builder.ConnectionString="<your_ado_net_connection_string>";`

```csharp
using System;
using System.Data.SqlClient;
using System.Text;

namespace sqltest
{
    class Program
    {
        static void Main(string[] args)
        {
            try 
            { 
                SqlConnectionStringBuilder builder = new SqlConnectionStringBuilder();

                builder.DataSource = "<your_server.database.windows.net>"; 
                builder.UserID = "<your_username>";            
                builder.Password = "<your_password>";     
                builder.InitialCatalog = "<your_database>";
         
                using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                {
                    Console.WriteLine("\nQuery data example:");
                    Console.WriteLine("=========================================\n");
                    
                    connection.Open();       

                    String sql = "SELECT name, collation_name FROM sys.databases";

                    using (SqlCommand command = new SqlCommand(sql, connection))
                    {
                        using (SqlDataReader reader = command.ExecuteReader())
                        {
                            while (reader.Read())
                            {
                                Console.WriteLine("{0} {1}", reader.GetString(0), reader.GetString(1));
                            }
                        }
                    }                    
                }
            }
            catch (SqlException e)
            {
                Console.WriteLine(e.ToString());
            }
            Console.WriteLine("\nDone. Press enter.");
            Console.ReadLine(); 
        }
    }
}
```

## <a name="run-the-code"></a>Executar o código

1. No prompt, execute os comandos a seguir.

   ```cmd
   dotnet restore
   dotnet run
   ```

2. Verifique se as linhas são retornadas.

   ```text
   Query data example:
   =========================================

   master   SQL_Latin1_General_CP1_CI_AS
   tempdb   SQL_Latin1_General_CP1_CI_AS
   WideWorldImporters   Latin1_General_100_CI_AS

   Done. Press enter.
   ```

3. Pressione **Enter** para fechar a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao .NET Core no Windows/Linux/macOS usando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba como [se conectar ao Banco de Dados SQL do Azure ou à Instância Gerenciada de SQL do Azure e consultá-los usando o .NET Framework e o Visual Studio](connect-query-dotnet-visual-studio.md).  
- Saiba como [Criar seu primeiro banco de dados com o SSMS](design-first-database-tutorial.md) ou [Criar um banco de dados e se conectar com o C# e com o ADO.NET](design-first-database-csharp-tutorial.md).
- Para saber mais sobre o .NET, veja a [documentação do .NET](/dotnet/).
