---
title: Usar o Visual Studio com .NET e C# em consultas
description: Use o Visual Studio para criar um aplicativo C# que se conecta a um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure e executa consultas.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: devx-track-csharp, sqldbrb=2
ms.devlang: dotnet
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/10/2020
ms.openlocfilehash: 1d8859f4790610e72ad517f74bbbbf0cf77d9316
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97705193"
---
# <a name="quickstart-use-net-and-c-in-visual-studio-to-connect-to-and-query-a-database"></a>Início Rápido: Usar .NET e C# no Visual Studio para se conectar a um banco de dados e consultá-lo
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi-asa.md)]

Este início rápido mostra como usar o [.NET Framework](https://www.microsoft.com/net/) e o código C# no Visual Studio para consultar um banco de dados no SQL do Azure ou no SQL do Synapse com as instruções Transact-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Visual Studio 2019](https://www.visualstudio.com/downloads/) Community, Professional ou Enterprise Edition.
- Um banco de dados no qual você pode executar uma consulta.

  [!INCLUDE[create-configure-database](../includes/create-configure-database.md)]

## <a name="create-code-to-query-the-database-in-azure-sql-database"></a>Criar código para consultar o banco de dados no Banco de Dados SQL do Azure

1. No Visual Studio, crie um novo projeto. 
   
1. Na caixa de diálogo **Novo Projeto**, selecione **Visual C#** , **Aplicativo de Console (.NET Framework)** .
   
1. Insira *sqltest* para o nome do projeto e selecione **OK**. Quando um novo projeto é criado. 
   
1. Selecione **Projeto** > **Gerenciar Pacotes do NuGet**. 
   
1. Na **Gerenciador de Pacotes NuGet**, selecione a guia **Procurar** e pesquise e selecione **Microsoft.Data.SqlClient**.
   
1. Na página **Microsoft.Data.SqlClient**, selecione **Instalar**. 
   - Se solicitado, selecione **OK** para continuar com a instalação. 
   - Se uma janela **Aceitação da Licença** for exibida, selecione **Aceito**.
   
1. Quando a instalação for concluída, você poderá fechar **Gerenciador de Pacotes NuGet**. 
   
1. No editor de códigos, substitua o conteúdo de **Program.cs** pelo código a seguir. Substitua os valores para `<your_server>`, `<your_username>`, `<your_password>` e `<your_database>`.
   
   ```csharp
   using System;
   using Microsoft.Data.SqlClient;
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
                   builder.DataSource = "<your_server>.database.windows.net"; 
                   builder.UserID = "<your_username>";            
                   builder.Password = "<your_password>";     
                   builder.InitialCatalog = "<your_database>";
   
                   using (SqlConnection connection = new SqlConnection(builder.ConnectionString))
                   {
                       Console.WriteLine("\nQuery data example:");
                       Console.WriteLine("=========================================\n");
                       
                       String sql = "SELECT name, collation_name FROM sys.databases";
   
                       using (SqlCommand command = new SqlCommand(sql, connection))
                       {
                           connection.Open();
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
               Console.ReadLine();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Executar o código

1. Para executar o aplicativo, selecione **Depurar** > **Iniciar Depuração** ou **Iniciar** na barra de ferramentas ou pressione **F5**.
1. Verifique se nomes e agrupamentos de bancos de dados são retornados e feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- Saiba como [se conectar a um banco de dados no Banco de Dados SQL do Azure e consultá-lo usando o .NET Core](connect-query-dotnet-core.md) no Windows/Linux/macOS.  
- Saiba mais sobre a [Introdução ao .NET Core no Windows/Linux/macOS usando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli).
- Saiba como [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure usando o SSMS](design-first-database-tutorial.md) ou [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure usando o .NET](design-first-database-csharp-tutorial.md).
- Para saber mais sobre o .NET, veja a [documentação do .NET](/dotnet/).
- Exemplo de lógica de repetição: [Conectar-se de maneira resiliente ao Azure SQL com o ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n].


<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net
