---
title: Habilitar sincronização offline para seu aplicativo móvel do Azure (Xamarin.Forms) | Microsoft Docs
description: Aprenda a usar o aplicativo móvel do Serviço de Aplicativo para armazenar em cache e sincronizar dados offline no aplicativo Xamarin.Forms
documentationcenter: xamarin
author: elamalani
manager: yochayk
editor: ''
services: app-service\mobile
ms.assetid: acf0f874-3ea5-4410-bd22-b0e72140f3b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 53f339d5450965c992f6528ff294e0d37ec2f7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446292"
---
# <a name="enable-offline-sync-for-your-xamarinforms-mobile-app"></a>Habilitar sincronização offline para seu aplicativo móvel Xamarin.Forms
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-forms-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Visão geral
Este tutorial apresenta o recurso de sincronização offline de Aplicativos móveis do Azure para Xamarin.Forms. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local. Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o serviço remoto.

Este tutorial se baseia na solução de início rápido do Xamarin.Forms para aplicativos móveis criados quando você conclui o tutorial [Criar um aplicativo iOS Xamarin]. A solução de início rápido para Xamarin.Forms contém o código para dar suporte à sincronização offline, que só precisa ser habilitada. Neste tutorial, você atualiza a solução de início rápido para ativar os recursos offline dos Aplicativos Móveis do Azure. Também destacamos o código especificamente offline no aplicativo. Se você não usar a solução de início rápido baixada, deverá adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure][1].

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de Dados Offline em Aplicativos Móveis do Azure][2].

## <a name="enable-offline-sync-functionality-in-the-quickstart-solution"></a>Habilitar a funcionalidade de sincronização offline na solução de início rápido
O código de sincronização offline é incluído no projeto usando diretivas de pré-processador C#. Quando o símbolo **OFFLINE\_SYNC\_ENABLED** é definido, esses caminhos de código são incluídos na compilação. Para aplicativos do Windows, você deve instalar também a plataforma SQLite.

1. No Visual Studio, clique com o botão direito do mouse na solução > **Gerenciar Pacotes NuGet para a Solução...** , procure e instale o pacote NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** para todos os projetos na solução.
2. No Gerenciador de Soluções, abra o arquivo TodoItemManager.cs do projeto com **Portable** no nome, que é o projeto da Biblioteca de Classes Portátil e remova o comentário da seguinte diretiva de pré-processador:

        #define OFFLINE_SYNC_ENABLED
3. (Opcional) Para dar suporte a dispositivos Windows, instale um dos seguintes pacotes de tempo de execução do SQLite:

   * **Tempo de Execução do Windows 8.1:** Instale [SQLite para Windows 8.1][3].
   * **Windows Phone 8.1:** Instale [SQLite para Windows Phone 8.1][4].
   * **Plataforma Universal do Windows** Instale o [SQLite para a Plataforma Universal do Windows][5].

     Embora o início rápido não contenha um projeto Universal do Windows, a plataforma Universal do Windows tem suporte com formulários do Xamarin.
4. (Opcional) Em cada projeto de aplicativo do Windows, clique com o botão direito do mouse em **Referências** > **Adicionar referência…** e expanda a pasta **Windows** > **Extensões**.
    Habilite o SDK do **SQLite para Windows** apropriado, junto com o SDK do **Tempo de execução do Visual C++ 2013 para Windows**.
    Os nomes do SDK do SQLite variam ligeiramente de acordo com cada plataforma Windows.

## <a name="review-the-client-sync-code"></a>Examine o código de sincronização do cliente
Aqui há uma breve visão geral do que já está incluído no código do tutorial nas diretivas do `#if OFFLINE_SYNC_ENABLED` . A funcionalidade de sincronização offline está no arquivo de projeto TodoItemManager.cs no projeto da Biblioteca de Classes Portátil. Para obter uma visão geral conceitual do recurso, confira [Sincronização de Dados Offline em Aplicativos Móveis do Azure][2].

* Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. O banco de dados de armazenamento local é inicializado no construtor da classe **TodoItemManager** usando o seguinte código:

        var store = new MobileServiceSQLiteStore(OfflineDbPath);
        store.DefineTable<TodoItem>();

        //Initializes the SyncContext using the default IMobileServiceSyncHandler.
        this.client.SyncContext.InitializeAsync(store);

        this.todoTable = client.GetSyncTable<TodoItem>();

    Este código cria um novo banco de dados SQLite local usando a classe **MobileServiceSQLiteStore**.

    O método **DefineTable** cria uma tabela no repositório local que corresponde aos campos no tipo fornecido.  O tipo não precisa incluir todas as colunas que estão no banco de dados remoto. É possível armazenar um subconjunto de colunas.
* O campo **todoTable** no **TodoItemManager** é um tipo **IMobileServiceSyncTable** em vez de **IMobileServiceTable**. Essa classe usa o banco de dados local para todas as operações de criação, leitura, atualização e exclusão (CRUD) de tabela. Você decide quando essas alterações são enviadas por push ao back-end do Aplicativo Móvel ao chamar **PushAsync** no **IMobileServiceSyncContext**. O contexto de sincronização ajuda a preservar relações da tabela controlando e enviando por push as alterações em todas as tabelas que um aplicativo cliente tenha modificado quando o **PushAsync** é chamado.

    O método **SyncAsync** a seguir é chamado para sincronização com o back-end do Aplicativo Móvel:

        public async Task SyncAsync()
        {
            ReadOnlyCollection<MobileServiceTableOperationError> syncErrors = null;

            try
            {
                await this.client.SyncContext.PushAsync();

                await this.todoTable.PullAsync(
                    "allTodoItems",
                    this.todoTable.CreateQuery());
            }
            catch (MobileServicePushFailedException exc)
            {
                if (exc.PushResult != null)
                {
                    syncErrors = exc.PushResult.Errors;
                }
            }

            // Simple error/conflict handling.
            if (syncErrors != null)
            {
                foreach (var error in syncErrors)
                {
                    if (error.OperationKind == MobileServiceTableOperationKind.Update && error.Result != null)
                    {
                        //Update failed, reverting to server's copy.
                        await error.CancelAndUpdateItemAsync(error.Result);
                    }
                    else
                    {
                        // Discard local change.
                        await error.CancelAndDiscardItemAsync();
                    }

                    Debug.WriteLine(@"Error executing sync operation. Item: {0} ({1}). Operation discarded.",
                        error.TableName, error.Item["id"]);
                }
            }
        }

    Este exemplo usa o tratamento de erros simples com o manipulador de sincronização padrão. Um aplicativo real trataria os diversos erros como condições de rede e conflitos de servidor usando uma personalização da implementação de **IMobileServiceSyncHandler**.

## <a name="offline-sync-considerations"></a>Considerações sobre a sincronização offline
No exemplo, o método **SyncAsync** é chamado somente na inicialização e quando a sincronização é solicitada.  Para iniciar uma sincronização em um aplicativo Android ou iOS, puxe para baixo a lista de itens; para o Windows, use o botão **Sincronizar**. Em um aplicativo do mundo real, você também poderia fazer a sincronização disparar quando o estado da rede mudasse.

Quando um pull é executado em uma tabela que tenha atualizações locais pendentes controladas pelo contexto, e essa operação de pull automaticamente dispara um envio por push de contexto anterior. Durante a atualização, a adição e a conclusão de itens neste exemplo, você pode omitir a chamada explícita a **PushAsync**.

No código fornecido, todos os registros na tabela remota TodoItem são solicitados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta ao **PushAsync**. Para obter mais informações, consulte a seção *Sincronização Incremental* em [Sincronização de Dados Offline em Aplicativos Móveis do Azure][2].

## <a name="run-the-client-app"></a>Execute o aplicativo cliente
Com a sincronização offline agora habilitada, execute o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados do armazenamento local. Posteriormente, simule um cenário offline e modificará os dados no armazenamento local enquanto o aplicativo estiver offline.

## <a name="update-the-sync-behavior-of-the-client-app"></a>Atualize o comportamento de sincronização do aplicativo cliente
Nesta seção, modifique o projeto do cliente para simular um cenário offline usando uma URL de aplicativo inválida para o back-end. Como alternativa, você pode desativar conexões de rede mudando o dispositivo para o "Modo de avião".  Quando você adiciona ou alterar itens de dados, essas alterações são mantidas no armazenamento local, mas não sincronizadas com o armazenamento de dados back-end até que a conexão seja restabelecida.

1. No Gerenciador de Soluções, abra o arquivo de projeto Constants.cs do projeto **Portable** e altere o valor de `ApplicationURL` para apontar para uma URL inválida:

        public static string ApplicationURL = @"https://your-service.azurewebsites.net/";
2. Abra o arquivo TodoItemManager.cs do projeto **Portable** e adicione um **catch** à classe base **Exception** para o bloco **try...catch** em **SyncAsync**. Esse bloco **catch** grava a mensagem de exceção no console, da seguinte maneira:

            catch (Exception ex)
            {
                Console.Error.WriteLine(@"Exception: {0}", ex.Message);
            }
3. Compile e execute o aplicativo cliente.  Adicione alguns novos itens. Observe que uma exceção será registrada no console para cada tentativa de sincronização com o back-end. Esses novos itens existem apenas no repositório local até que possam ser enviados por push para o serviço móvel. O aplicativo cliente se comporta como se estivesse conectado ao back-end, dando suporte a todas as operações CRUD (criar, ler, atualizar e excluir).
4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.
5. (Opcional) Use o Visual Studio para exibir a tabela de Banco de Dados SQL do Azure para ver se os dados no banco de dados do back-end não foram alterados.

    No Visual Studio, abra **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Pesquisador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo.

## <a name="update-the-client-app-to-reconnect-your-mobile-backend"></a>Atualize o aplicativo cliente para reconectar seu back-end móvel
Nesta seção, reconecte o aplicativo ao back-end móvel, que simula o aplicativo voltando ao estado online. Quando você executa o gesto de atualização, os dados são sincronizados com o serviço móvel.

1. Reabra Constants.cs. Corrija o `applicationURL` de modo que ele aponte para a URL correta.
2. Recompile e execute o aplicativo cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel após iniciar. Verifique se nenhuma exceção está registrada no console de depuração.
3. (Opcional) Exibir os dados atualizados usando o Pesquisador de objetos do SQL Server ou uma ferramenta REST, como o Fiddler ou [Postman][6]. Observe que os dados foram sincronizados entre o banco de dados de back-end e o repositório local.

    Observe que os dados foram sincronizados entre o banco de dados e o armazenamento local e contém os itens que você adicionou enquanto seu aplicativo estava desconectado.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de Dados Offline em Aplicativos Móveis do Azure][2]
* [SDK do .NET de aplicativos móveis do Azure HOWTO][8]

<!-- URLs. -->
[1]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[2]: app-service-mobile-offline-data-sync.md
[3]: https://go.microsoft.com/fwlink/p/?LinkID=716919
[4]: https://go.microsoft.com/fwlink/p/?LinkID=716920
[5]: https://sqlite.org/2016/sqlite-uwp-3120200.vsix
[6]: https://www.getpostman.com/
[7]: https://www.telerik.com/fiddler
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
