---
title: Usar o Azure Functions para executar uma tarefa de limpeza de banco de dados | Microsoft Docs
description: Use o Azure Functions para agendar uma tarefa que se conecta ao banco de dados SQL do Azure para limpar linhas periodicamente.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/22/2017
ms.author: glenga
ms.openlocfilehash: ac0399867e0cdab1825022c4ed73ce003cc8c7e6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Usar o Azure Functions para conectar a um banco de dados SQL do Azure
Este tópico mostra como usar o Azure Functions para criar um trabalho agendado que limpa linhas em uma tabela em um banco de dados SQL do Azure. A nova função C# é criada com base em um gatilho de temporizador predefinido no Portal do Azure. Para dar suporte a esse cenário, você também precisa definir uma cadeia de conexão de banco de dados como uma configuração no aplicativo de funções. Esse cenário usa uma operação em massa no banco de dados. Para que sua função processe operações CRUD individuais em uma tabela dos Aplicativos Móveis, você deve usar as [Associações de aplicativos móveis](functions-bindings-mobile-apps.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Este tópico usa uma função disparada por temporizador. Conclua as etapas no tópico [Criar uma função no Azure que seja disparada por um temporizador](functions-create-scheduled-function.md) para criar uma versão C# dessa função.   

+ Este tópico demonstra um comando Transact-SQL que executa uma operação de limpeza em massa na tabela **SalesOrderHeader** no banco de dados de amostra AdventureWorksLT. Para criar o banco de dados de amostra AdventureWorksLT, conclua as etapas no tópico [Criar um Banco de Dados SQL do Azure no Portal do Azure](../sql-database/sql-database-get-started-portal.md). 

## <a name="get-connection-information"></a>Obter informações de conexão

Você precisa obter a cadeia de conexão para o banco de dados que você criou quando concluiu [Criar um Banco de Dados SQL do Azure no Portal do Azure](../sql-database/sql-database-get-started-portal.md).

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
 
3. Selecione **Bancos de Dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de Dados SQL**.

4. Selecione **Mostrar cadeias de conexão do banco de dados** e copie a cadeia de conexão completa do **ADO.NET**.

    ![Copie a cadeia de conexão ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de conexão 

Um aplicativo de função hospeda a execução de suas funções no Azure. É uma prática recomendada armazenar cadeias de conexão e outros segredos nas configurações do seu aplicativo de funções. Usar as configurações do aplicativo impede a divulgação acidental da cadeia de conexão com seu código. 

1. Navegue até seu aplicativo de funções criado em [Criar uma função no Azure que é disparada por um temporizador](functions-create-scheduled-function.md).

2. Selecione **Recursos da plataforma** > **Configurações de aplicativo**.
   
    ![Configurações de aplicativo para o aplicativo de funções.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings.png)

2. Role para baixo até **Cadeias de caracteres de conexão** e adicione uma cadeia de conexão usando as configurações especificadas na tabela.
   
    ![Adicione uma cadeia de conexão às configurações do aplicativo de funções.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-strings.png)

    | Configuração       | Valor sugerido | Descrição             | 
    | ------------ | ------------------ | --------------------- | 
    | **Nome**  |  sqldb_connection  | Usado para acessar a cadeia de conexão armazenada no seu código de função.    |
    | **Valor** | Cadeia de caracteres copiada  | Cole a cadeia de conexão que você copiou na seção anterior. |
    | **Tipo** | Banco de Dados SQL | Use a conexão do Banco de Dados SQL. |   

3. Clique em **Salvar**.

Agora, você pode adicionar o código de função C# que conecta ao Banco de Dados SQL.

## <a name="update-your-function-code"></a>Atualizar o código de função

1. Em seu aplicativo de funções, selecione a Função disparada por temporizador.
 
3. Adicione as seguintes referências de assembly na parte superior do código de função existente:

    ```cs
    #r "System.Configuration"
    #r "System.Data"
    ```

3. Adicione as instruções `using` a seguir à função:
    ```cs
    using System.Configuration;
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

4. Substitua a função **Run** existente por este código:
    ```cs
    public static async Task Run(TimerInfo myTimer, TraceWriter log)
    {
        var str = ConfigurationManager.ConnectionStrings["sqldb_connection"].ConnectionString;
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " + 
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.Info($"{rows} rows were updated");
            }
        }
    }
    ```

    Este comando atualiza a coluna **Status** com base na data de envio. Ele deve atualizar 32 linhas de dados.

5. Clique em **Salvar**, observe nas janelas de **Logs** a execução da próxima função e observe o número de linhas atualizadas na tabela **SalesOrderHeader**.

    ![Exibir os logs da função.](./media/functions-scenario-database-table-cleanup/functions-logs.png)

## <a name="next-steps"></a>Próximas etapas

Em seguida, saiba como usar o Functions com Aplicativos Lógicos para integração com outros serviços.

> [!div class="nextstepaction"] 
> [Criar uma função que se integra nos Aplicativos Lógicos](functions-twitter-email.md)

Confira estes tópicos para obter mais informações sobre o Functions:

* [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
  Referência do programador para codificação de funções e definição de gatilhos e de associações.
* [Testando o Azure Functions](functions-test-a-function.md)  
  Descreve várias ferramentas e técnicas para testar suas funções.  
