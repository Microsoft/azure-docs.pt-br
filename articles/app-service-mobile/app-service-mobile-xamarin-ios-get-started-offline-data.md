---
title: Habilitar sincronização offline para seu aplicativo móvel do Azure (Xamarin iOS)
description: Aprenda a usar o aplicativo móvel do Serviço de Aplicativo para armazenar em cache e sincronizar dados offline no aplicativo iOS
documentationcenter: xamarin
author: elamalani
manager: cfowler
editor: ''
services: app-service\mobile
ms.assetid: 828a287c-5d58-4540-9527-1309ebb0f32b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: b87a1d86370e3abdb200b691d5216b1262512b3e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440052"
---
# <a name="enable-offline-sync-for-your-xamarinios-mobile-app"></a>Habilitar sincronização offline para seu aplicativo móvel Xamarin.iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-ios-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Visão geral
Este tutorial apresenta o recurso de sincronização offline de Aplicativos móveis do Azure para Xamarin.iOS. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local. Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o serviço remoto.

Neste tutorial, atualize o projeto de aplicativo do Xamarin.iOS de [Criar um aplicativo Xamarin.iOS] para dar suporte aos recursos offline dos Aplicativos Móveis do Azure. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualize o aplicativo cliente para dar suporte aos recursos offline
Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você deve inicializar um [SyncContext] para um repositório local. Referencie sua tabela pela interface [IMobileServiceSyncTable]. O SQLite é usado como o repositório local no dispositivo.

1. Abra o gerenciador de pacotes do NuGet para o projeto que você concluiu no tutorial de [Criar um aplicativo Xamarin.iOS] , procure o pacote NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore** e instale-o.
2. Abra o arquivo QSTodoService.cs e remova o comentário da definição `#define OFFLINE_SYNC_ENABLED`.
3. Recompile e execute o aplicativo cliente. O aplicativo funciona da mesma forma que antes de habilitar a sincronização offline. No entanto, o banco de dados local é agora populado com dados que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo para desconectar o back-end
Nesta seção, você interrompe a conexão com seu Aplicativo Móvel de back-end para simular uma situação offline. Quando você adicionar itens de dados, o manipulador de exceção informará que o aplicativo está operando no modo offline. Nesse estado, novos itens são adicionados ao repositório local e serão sincronizados com o back-end do aplicativo móvel da próxima vez que o envio por push for executado em um estado conectado.

1. Edite QSToDoService.cs no projeto compartilhado. Altere a **applicationURL** para apontar para uma URL inválida:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Você também pode demonstrar o comportamento offline desabilitando redes Wi-Fi e celulares no dispositivo ou usando o modo avião.
2. Compile e execute o aplicativo. Observe a falha de sincronização na atualização, quando o aplicativo é iniciado.
3. Insira os novos itens e observe que o envio falha com um status [CancelledByNetworkError] toda vez que você clicar em **Salvar**. Contudo, os novos itens de tarefas pendentes existem apenas no repositório local até serem enviados por push para o back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções, o aplicativo cliente se comportará como se ainda estivesse conectado ao back-end do aplicativo móvel.
4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.
5. (Opcional) Se você tiver o Visual Studio instalado em um computador, abra o **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**-> **Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Pesquisador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foram alterados.
6. (Opcional) Use uma ferramenta REST, como o Fiddler ou Postman, para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualize o aplicativo para reconectar o back-end do Aplicativo Móvel
Nesta seção, reconecte o aplicativo ao back-end do aplicativo móvel. Isso simula o aplicativo mudando de um estado offline para um estado online com o back-end do aplicativo móvel.   Se você tiver simulado a ruptura da rede ao desativar a conectividade de rede, não será necessária nenhuma alteração no código.
Ative a rede novamente.  Quando você executar o aplicativo pela primeira vez, o método `RefreshDataAsync` será chamado. Isso, por sua vez, chama `SyncAsync` para sincronizar seu armazenamento local com o banco de dados de back-end.

1. Abra QSToDoService.cs no projeto compartilhado e reverta a alteração da propriedade **applicationURL**.
2. Recompile e execute o aplicativo. O aplicativo sincroniza as alterações locais com o back-end do aplicativo móvel do Azure usando operações de push e pull quando o método `OnRefreshItemsSelected` é executado.
3. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end do aplicativo móvel do Azure e o armazenamento local.
4. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local.

   `CompleteItemAsync` chama `SyncAsync` para sincronizar cada item concluído com o back-end do Aplicativo Móvel. `SyncAsync` chama push e pull.
   **Sempre que você executar um pull em comparação a uma tabela em que o cliente fez alterações, um push no contexto de sincronização do cliente sempre será executado primeiro automaticamente**. O push implícito garante que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes. Para obter mais informações sobre esse comportamento, confira [Sincronização de dados offline em Aplicativos Móveis do Azure].

## <a name="review-the-client-sync-code"></a>Examine o código de sincronização do cliente
O projeto de cliente Xamarin que você baixou ao concluir o tutorial [Criar um aplicativo Xamarin.iOS] já contém a sincronização offline do código de suporte usando um banco de dados SQLite local. Aqui há um resumo breve do que já está incluído no código do tutorial. Para obter uma visão geral conceitual do recurso, confira [Sincronização de dados Offline em aplicativos móveis do Azure].

* Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. O banco de dados do armazenamento local é inicializado quando o `QSTodoListViewController.ViewDidLoad()` executa o `QSTodoService.InitializeStoreAsync()`. Este método cria um novo banco de dados SQLite local usando a classe `MobileServiceSQLiteStore` fornecida pelo SDK do cliente de Aplicativos Móveis do Azure.

    O método `DefineTable` cria uma tabela no repositório local que corresponde aos campos no tipo fornecido, `ToDoItem`, neste caso. O tipo não precisa incluir todas as colunas que estão no banco de dados remoto. É possível armazenar apenas um subconjunto de colunas.

        // QSTodoService.cs

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }
* O membro `todoTable` do `QSTodoService` é do tipo `IMobileServiceSyncTable` em vez do `IMobileServiceTable`. IMobileServiceSyncTable instrui todas as operações de tabela CRUD (criar, ler, atualizar e excluir) no banco de dados do armazenamento local.

    Você decide quando essas alterações são enviadas para o back-end do aplicativo móvel do Azure chamando `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a preservar relações da tabela controlando e enviando por push as alterações em todas as tabelas que um aplicativo cliente tenha modificado quando o `PushAsync` é chamado.

    O código fornecido chama o `QSTodoService.SyncAsync()` para sincronizar sempre que a lista todoitem é atualizada ou um todoitem é adicionado ou concluído. O aplicativo é sincronizado a cada alteração local. Se um pull for executado em uma tabela que tenha atualizações locais pendentes controladas pelo contexto, e essa operação de pull automaticamente disparará um envio por push de contexto primeiro.

    No código fornecido, todos os registros na tabela remota `TodoItem` são solicitados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta ao `PushAsync`. Para obter mais informações, consulte a seção *Sincronização Incremental* em [Sincronização de dados offline em Aplicativos Móveis do Azure].

        // QSTodoService.cs
        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de Dados Offline em Aplicativos Móveis do Azure]
* [SDK do .NET de aplicativos móveis do Azure HOWTO][8]

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Sincronização de dados offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
