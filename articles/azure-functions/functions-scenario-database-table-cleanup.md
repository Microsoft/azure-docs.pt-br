---
title: Usar Azure Functions para executar uma tarefa de limpeza do banco de dados
description: Use o Azure Functions para agendar uma tarefa que se conecta ao banco de dados SQL do Azure para limpar linhas periodicamente.
ms.assetid: 076f5f95-f8d2-42c7-b7fd-6798856ba0bb
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 10/02/2019
ms.openlocfilehash: 0b5e255d7d108eb063ece4e5489a8762261a0bed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88207252"
---
# <a name="use-azure-functions-to-connect-to-an-azure-sql-database"></a>Usar o Azure Functions para conectar a um banco de dados SQL do Azure

Este artigo mostra como usar Azure Functions para criar um trabalho agendado que se conecta a um banco de dados SQL do Azure ou Instância Gerenciada SQL do Azure. O código de função limpa as linhas em uma tabela no banco de dados. A nova função C# é criada com base em um modelo de gatilho de temporizador predefinido no Visual Studio 2019. Para dar suporte a esse cenário, você também precisa definir uma cadeia de conexão de banco de dados como uma configuração de aplicativo no aplicativo de funções. Para o Azure SQL Instância Gerenciada você precisa [habilitar o ponto de extremidade público](../azure-sql/managed-instance/public-endpoint-configure.md) para poder se conectar de Azure functions. Esse cenário usa uma operação em massa no banco de dados. 

Se esta for sua primeira experiência trabalhando com funções C#, você deverá ler a [Referência do desenvolvedor de C# do Azure Functions](functions-dotnet-class-library.md).

## <a name="prerequisites"></a>Pré-requisitos

+ Conclua as etapas no artigo [criar sua primeira função usando o Visual Studio](functions-create-your-first-function-visual-studio.md) para criar um aplicativo de função local que tenha como destino a versão 2. x ou uma versão posterior do tempo de execução. Você também precisa ter publicado seu projeto em um aplicativo de funções no Azure.

+ Este artigo demonstra um comando Transact-SQL que executa uma operação de limpeza em massa na tabela **SalesOrderHeader** no banco de dados de amostra AdventureWorksLT. Para criar o banco de dados de exemplo AdventureWorksLT, conclua as etapas no artigo [criar um banco de dados no banco de dados SQL do Azure usando o portal do Azure](../azure-sql/database/single-database-create-quickstart.md).

+ Você precisa adicionar uma [regra de firewall no nível do servidor](../azure-sql/database/firewall-create-server-level-portal-quickstart.md) para o endereço IP público do computador que usou para este início rápido. Essa regra é necessária para poder acessar a instância do banco de dados SQL do computador local.  

## <a name="get-connection-information"></a>Obter informações de conexão

Você precisa obter a cadeia de conexão para o banco de dados criado quando concluiu a [criação de um banco de dados no banco de dados SQL do Azure usando o portal do Azure](../azure-sql/database/single-database-create-quickstart.md).

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **Bancos de Dados SQL** no menu à esquerda e selecione seu banco de dados na página **Bancos de Dados SQL**.

1. Selecione **Cadeias de conexão** em **Configurações** e copie a cadeia de conexão completa do **ADO.NET**. Para Azure SQL Instância Gerenciada copiar cadeia de conexão para o ponto de extremidade público.

    ![Copie a cadeia de conexão ADO.NET.](./media/functions-scenario-database-table-cleanup/adonet-connection-string.png)

## <a name="set-the-connection-string"></a>Definir a cadeia de conexão

Um aplicativo de funções hospeda a execução de suas funções no Azure. Como uma prática de segurança recomendada, armazene cadeias de conexão e outros segredos nas configurações do seu aplicativo de funções. Usar as configurações do aplicativo impede a divulgação acidental da cadeia de conexão com seu código. Você pode acessar as configurações do aplicativo para seu aplicativo de funções diretamente do Visual Studio.

Você precisa ter publicado o aplicativo anteriormente no Azure. Se você ainda não fez isso, [publique o aplicativo de funções no Azure](functions-develop-vs.md#publish-to-azure).

1. Em Gerenciador de soluções, clique com o botão direito do mouse no projeto do aplicativo de funções e escolha **publicar**  >  **Editar Azure app configurações do serviço**. Selecione **Adicionar configuração**, em **Novo nome de configuração do aplicativo**, digite `sqldb_connection` e selecione **OK**.

    ![Configurações de aplicativo para o aplicativo de funções.](./media/functions-scenario-database-table-cleanup/functions-app-service-add-setting.png)

1. Na configuração **sqldb_connection**, cole a cadeia de conexão que você copiou na seção anterior no campo **Local** e substitua os espaços reservados `{your_username}` e `{your_password}` por valores reais. Selecione **Inserir o valor do local** para copiar o valor atualizado para o campo **remoto** e, em seguida, selecione **OK**.

    ![Adicione uma configuração de cadeia de conexão SQL.](./media/functions-scenario-database-table-cleanup/functions-app-service-settings-connection-string.png)

    As cadeias de conexão são armazenadas criptografadas no Azure (**Remoto**). Para evitar o vazamento de segredos, o arquivo de projeto local.settings.json (**Local**) deve ser excluídos do controle do código-fonte, por exemplo, por meio de um arquivo .gitignore.

## <a name="add-the-sqlclient-package-to-the-project"></a>Adicione o pacote do SqlClient ao projeto

Você precisa adicionar o pacote do NuGet que contém a biblioteca do SqlClient. Essa biblioteca de acesso a dados é necessária para conectar-se ao SQL Database.

1. Abra seu projeto de aplicativo de função local no Visual Studio 2019.

1. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto do aplicativo de funções e escolha **Gerenciar Pacotes NuGet**.

1. Na guia **Procurar**, pesquise ```System.Data.SqlClient``` e quando encontrar, selecione.

1. Na página **System.Data.SqlClient**, selecione a versão `4.5.1` e clique em **Instalar**.

1. Quando a instalação for concluída, revise as alterações e então clique em **OK** para fechar a janela **Visualização**.

1. Se uma janela **Aceitação da Licença** for exibida, clique em **Aceito**.

Agora, você pode adicionar o código de função C# que conecta ao Banco de Dados SQL.

## <a name="add-a-timer-triggered-function"></a>Adicionar uma função disparada por temporizador

1. Em Gerenciador de soluções, clique com o botão direito do mouse no projeto do aplicativo de funções e escolha **Adicionar**  >  **nova função do Azure**.

1. Com o modelo do **Azure Functions** selecionado, nomeie o novo item algo semelhante a `DatabaseCleanup.cs` e selecione **Adicionar**.

1. Na caixa de diálogo **Nova função do Azure**, escolha **Gatilho de temporizador** e, em seguida, **OK**. Essa caixa de diálogo cria um arquivo de código para a função disparada por temporizador.

1. Abra o novo arquivo de código e adicione o seguinte, usando as instruções na parte superior do arquivo:

    ```cs
    using System.Data.SqlClient;
    using System.Threading.Tasks;
    ```

1. Substitua a função `Run` existente por este código:

    ```cs
    [FunctionName("DatabaseCleanup")]
    public static async Task Run([TimerTrigger("*/15 * * * * *")]TimerInfo myTimer, ILogger log)
    {
        // Get the connection string from app settings and use it to create a connection.
        var str = Environment.GetEnvironmentVariable("sqldb_connection");
        using (SqlConnection conn = new SqlConnection(str))
        {
            conn.Open();
            var text = "UPDATE SalesLT.SalesOrderHeader " +
                    "SET [Status] = 5  WHERE ShipDate < GetDate();";

            using (SqlCommand cmd = new SqlCommand(text, conn))
            {
                // Execute the command and log the # rows affected.
                var rows = await cmd.ExecuteNonQueryAsync();
                log.LogInformation($"{rows} rows were updated");
            }
        }
    }
    ```

    Esta função é executada a cada 15 segundos para atualizar a coluna `Status` com base na data de envio. Para saber mais sobre o Gatilho de temporizador, veja [Gatilho de temporizador para o Azure Functions](functions-bindings-timer.md).

1. Pressione **F5** para iniciar o aplicativo de funções. A janela de execução das [Azure Functions Core Tools](functions-develop-local.md) é aberta atrás do Visual Studio.

1. Passados 15 segundos da inicialização, a função é executada. Inspecione a saída e anote o número de linhas atualizadas na tabela **SalesOrderHeader**.

    ![Exibir os logs da função.](./media/functions-scenario-database-table-cleanup/function-execution-results-log.png)

    Na primeira execução, você deve atualizar 32 linhas de dados. As execuções seguintes não atualizam nenhuma linha de dados, a menos que você faça alterações aos dados da tabela SalesOrderHeader para que mais linhas sejam selecionadas pela instrução `UPDATE`.

Se você planeja [publicar essa função](functions-develop-vs.md#publish-to-azure), lembre-se de alterar o atributo `TimerTrigger` para uma [agenda de Cron](functions-bindings-timer.md#ncrontab-expressions) mais razoável do que a cada 15 segundos.

## <a name="next-steps"></a>Próximas etapas

Em seguida, aprenda como usar. Functions com Aplicativos Lógicos para integração com outros serviços.

> [!div class="nextstepaction"]
> [Criar uma função que se integra nos Aplicativos Lógicos](functions-twitter-email.md)

Para obter mais informações sobre o Functions, veja os seguintes artigos:

+ [Referência do desenvolvedor do Azure Functions](functions-reference.md)  
   Referência do programador para codificação de funções e definição de gatilhos e de associações.
+ [Testando Azure Functions](functions-test-a-function.md)  
   Descreve várias ferramentas e técnicas para testar suas funções.  
