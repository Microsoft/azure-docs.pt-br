---
title: Habilitar sincronização offline para seu aplicativo móvel do Azure (Xamarin Android)
description: Aprenda a usar o aplicativo móvel do Serviço de Aplicativo para armazenar em cache e sincronizar dados offline no aplicativo Xamarin Android
documentationcenter: xamarin
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 91d59e4b-abaa-41f4-80cf-ee7933b32568
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 9409260af0fd9739ae68545eca102ee420e9b2e8
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440139"
---
# <a name="enable-offline-sync-for-your-xamarinandroid-mobile-app"></a>Habilitar sincronização offline para seu aplicativo móvel Xamarin.Android

[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-xamarin-android-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Visão geral

Este tutorial apresenta o recurso de sincronização offline de Aplicativos móveis do Azure para Xamarin.Android. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local.
Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o serviço remoto.

Neste tutorial, você atualizará o projeto de cliente do tutorial [Criar um aplicativo Android do Xamarin] para dar suporte aos recursos offline dos Aplicativos Móveis do Azure. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

## <a name="update-the-client-app-to-support-offline-features"></a>Atualize o aplicativo cliente para dar suporte aos recursos offline

Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você deve inicializar um [SyncContext] para um repositório local. Em seguida, referencie sua tabela através da interface [IMobileServiceSyncTable](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable?view=azure-dotnet). O SQLite é usado como o repositório local no dispositivo.

1. No Visual Studio, abra o gerenciador de pacotes do NuGet no projeto que você concluiu no tutorial [Criar um aplicativo Android do Xamarin] tutorial.  Procure e instale o pacote NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
2. Abra o arquivo ToDoActivity.cs e remova o comentário da definição `#define OFFLINE_SYNC_ENABLED`.
3. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo cliente. O aplicativo funciona da mesma forma que antes de habilitar a sincronização offline. No entanto, o banco de dados local é agora populado com dados que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo para desconectar o back-end

Nesta seção, você interrompe a conexão com seu Aplicativo Móvel de back-end para simular uma situação offline. Quando você adicionar itens de dados, o manipulador de exceção informará que o aplicativo está operando no modo offline. Nesse estado, novos itens são adicionados ao repositório local e serão sincronizados com o back-end do aplicativo móvel da próxima vez que o envio por push for executado em um estado conectado.

1. Edite ToDoActivity.cs no projeto compartilhado. Altere a **applicationURL** para apontar para uma URL inválida:

         const string applicationURL = @"https://your-service.azurewebsites.fail";

    Você também pode demonstrar o comportamento offline desabilitando redes Wi-Fi e celulares no dispositivo ou usando o modo avião.
2. Pressione **F5** para compilar e executar o aplicativo. Observe a falha de sincronização na atualização, quando o aplicativo é iniciado.
3. Insira os novos itens e observe que o envio falha com um status [CancelledByNetworkError] toda vez que você clicar em **Salvar**. Contudo, os novos itens de tarefas pendentes existem apenas no repositório local até serem enviados por push para o back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções, o aplicativo cliente se comportará como se ainda estivesse conectado ao back-end do aplicativo móvel.
4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.
5. (Opcional) No Visual Studio, abra o **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Pesquisador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foram alterados.
6. (Opcional) Use uma ferramenta REST, como o Fiddler ou Postman, para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualize o aplicativo para reconectar o back-end do Aplicativo Móvel

Nesta seção, reconecte o aplicativo ao back-end do aplicativo móvel. Quando você executar o aplicativo pela primeira vez, o manipulador de eventos de `OnCreate` chamará `OnRefreshItemsSelected`. Esse método chama `SyncAsync` para sincronizar seu armazenamento local com o banco de dados de back-end.

1. Abra ToDoActivity.cs no projeto compartilhado e reverta a alteração da propriedade **applicationURL**.
2. Pressione a tecla **F5** para recompilar e executar o projeto. O aplicativo sincroniza as alterações locais com o back-end do aplicativo móvel do Azure usando operações de push e pull quando o método `OnRefreshItemsSelected` é executado.
3. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end do aplicativo móvel do Azure e o armazenamento local.
4. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local.

   `CheckItem` chama `SyncAsync` para sincronizar cada item concluído com o back-end do Aplicativo Móvel. `SyncAsync` chama push e pull. **Sempre que você executar um pull em uma tabela na qual o cliente fez alterações, um push será sempre executado automaticamente**. Isso garante que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes. Esse comportamento pode resultar em um push inesperado. Para obter mais informações sobre esse comportamento, confira [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

## <a name="review-the-client-sync-code"></a>Examine o código de sincronização do cliente

O projeto de cliente Xamarin que você baixou ao concluir o tutorial [Criar um aplicativo Android do Xamarin] já contém a sincronização offline do código de suporte usando um banco de dados SQLite local. Aqui há um resumo breve do que já está incluído no código do tutorial. Para obter uma visão geral conceitual do recurso, confira [Sincronização de dados Offline em aplicativos móveis do Azure].

* Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. O banco de dados do armazenamento local é inicializado quando o `ToDoActivity.OnCreate()` executa o `ToDoActivity.InitLocalStoreAsync()`. Isso cria um banco de dados SQLite local usando a classe `MobileServiceSQLiteStore` fornecida pelo SDK do cliente de Aplicativos Móveis do Azure.

    O método `DefineTable` cria uma tabela no repositório local que corresponde aos campos no tipo fornecido, `ToDoItem`, neste caso. O tipo não precisa incluir todas as colunas que estão no banco de dados remoto. É possível armazenar apenas um subconjunto de colunas.

        // ToDoActivity.cs
        private async Task InitLocalStoreAsync()
        {
            // new code to initialize the SQLite store
            string path = Path.Combine(System.Environment
                .GetFolderPath(System.Environment.SpecialFolder.Personal), localDbFilename);

            if (!File.Exists(path))
            {
                File.Create(path).Dispose();
            }

            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            // To use a different conflict handler, pass a parameter to InitializeAsync.
            // For more details, see https://go.microsoft.com/fwlink/?LinkId=521416.
            await client.SyncContext.InitializeAsync(store);
        }
* O membro `toDoTable` do `ToDoActivity` é do tipo `IMobileServiceSyncTable` em vez do `IMobileServiceTable`. IMobileServiceSyncTable instrui todas as operações de tabela CRUD (criar, ler, atualizar e excluir) no banco de dados do armazenamento local.

    Você decide quando alterações são enviadas para o back-end do aplicativo móvel do Azure chamando `IMobileServiceSyncContext.PushAsync()`. O contexto de sincronização ajuda a preservar relações da tabela controlando e enviando por push as alterações em todas as tabelas que um aplicativo cliente tenha modificado quando o `PushAsync` é chamado.

    O código fornecido chama o `ToDoActivity.SyncAsync()` para sincronizar sempre que a lista todoitem é atualizada ou um todoitem é adicionado ou concluído. As sincronizações de código após cada alteração local.

    No código fornecido, todos os registros na tabela remota `TodoItem` são solicitados, mas também é possível filtrar os registros passando uma ID de consulta e uma consulta ao `PushAsync`. Para obter mais informações, consulte a seção *Sincronização Incremental* em [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

        // ToDoActivity.cs
        private async Task SyncAsync()
        {
            try {
                await client.SyncContext.PushAsync();
                await toDoTable.PullAsync("allTodoItems", toDoTable.CreateQuery()); // query ID is used for incremental sync
            } catch (Java.Net.MalformedURLException) {
                CreateAndShowDialog (new Exception ("There was an error creating the Mobile Service. Verify the URL"), "Error");
            } catch (Exception e) {
                CreateAndShowDialog (e, "Error");
            }
        }

## <a name="additional-resources"></a>Recursos adicionais

* [Sincronização de Dados Offline em Aplicativos Móveis do Azure]
* [SDK do .NET de aplicativos móveis do Azure HOWTO][8]

<!-- URLs. -->
[Criar um aplicativo Android do Xamarin]: ./app-service-mobile-xamarin-android-get-started.md
[Sincronização de Dados Offline em Aplicativos Móveis do Azure]: ./app-service-mobile-offline-data-sync.md

<!-- Images -->

<!-- URLs. -->
[Criar um aplicativo Android do Xamarin]: app-service-mobile-xamarin-android-get-started.md
[Sincronização de dados offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Xamarin Studio]: https://xamarin.com/download
[Xamarin extension]: https://xamarin.com/visual-studio
[SyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
