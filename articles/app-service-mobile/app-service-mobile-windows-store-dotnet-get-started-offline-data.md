---
title: Habilitar sincronização offline para seu aplicativo UWP (Plataforma Universal do Windows) com os Aplicativos Móveis | Microsoft Docs
description: Aprenda a usar um Aplicativo Móvel do Azure para armazenar em cache e sincronizar dados offline no seu aplicativo UWP (Plataforma Universal do Windows).
documentationcenter: windows
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 8fe51773-90de-4014-8a38-41544446d9b5
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 4970a80b911a1efbc308d48ac4b8a50f774b4d04
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551924"
---
# <a name="enable-offline-sync-for-your-windows-app"></a>Habilitar sincronização offline para o seu aplicativo do Windows
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-windows-store-dotnet-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Visão geral
Este tutorial mostra como adicionar suporte offline para um aplicativo da UWP (Plataforma Universal do Windows) usando o back-end de aplicativos móveis do Azure. Sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local. Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o back-end remoto.

Neste tutorial, você atualizará o projeto de aplicativo da UWP do tutorial [Criar um aplicativo do Windows] para dar suporte aos recursos offline dos Aplicativos Móveis do Azure. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

## <a name="requirements"></a>Requisitos  
Este tutorial exige os seguintes pré-requisitos:

* Visual Studio 2013 em execução no Windows 8.1 ou mais recente.
* Conclusão de [Criar um aplicativo do Windows][Criar um aplicativo do Windows].
* [Azure Mobile Services SQLite Store][sqlite store nuget]
* [SQLite para desenvolvimento na Plataforma Universal do Windows](https://marketplace.visualstudio.com/items?itemName=SQLiteDevelopmentTeam.SQLiteforUniversalWindowsPlatform) 

## <a name="update-the-client-app-to-support-offline-features"></a>Atualize o aplicativo cliente para dar suporte aos recursos offline
Os recursos offline do aplicativo móvel do Azure permitem que você interaja com um banco de dados local quando estiver em um cenário offline. Para usar esses recursos em seu aplicativo, você deve inicializar uma [SyncContext][synccontext] to a local store. Then reference your table through the [IMobileServiceSyncTable][IMobileServiceSyncTable] interface. O SQLite é usado como o repositório local no dispositivo.

1. Instale o [tempo de execução do SQLite para a Plataforma Universal do Windows](https://sqlite.org/2016/sqlite-uwp-3120200.vsix).
2. No Visual Studio, abra o gerenciador de pacotes NuGet para o projeto de aplicativo UWP que você concluiu no tutorial [Criar um aplicativo do Windows].
    Procure e instale o pacote NuGet **Microsoft.Azure.Mobile.Client.SQLiteStore**.
3. No Gerenciador de Soluções, clique com o botão direito do mouse em **Referências** > **Adicionar Referência...** >**Universal do Windows**>**Extensões**; em seguida, habilite o **SQLite para Plataforma Universal do Windows** e o **Tempo de execução do Visual C++ 2015 para aplicativos da Plataforma Universal do Windows**.

    ![Adicionar referência SQLite UWP][1]
4. Abra o arquivo MainPage.xaml.cs e remova a definição `#define OFFLINE_SYNC_ENABLED`.
5. No Visual Studio, pressione a tecla **F5** para recompilar e executar o aplicativo cliente. O aplicativo funciona da mesma forma que antes de habilitar a sincronização offline. No entanto, o banco de dados local é agora populado com dados que podem ser usados em um cenário offline.

## <a name="update-sync"></a>Atualizar o aplicativo para desconectar o back-end
Nesta seção, você interrompe a conexão com seu Aplicativo Móvel de back-end para simular uma situação offline. Quando você adicionar itens de dados, o manipulador de exceção informará que o aplicativo está operando no modo offline. Nesse estado, novos itens são adicionados ao repositório local e serão sincronizados com o back-end do aplicativo móvel da próxima vez que o envio por push for executado em um estado conectado.

1. Edite App.xaml.cs no projeto compartilhado. Comente a inicialização do **MobileServiceClient** e adicione a seguinte linha, que usa uma URL de aplicativo móvel inválida:

         public static MobileServiceClient MobileService = new MobileServiceClient("https://your-service.azurewebsites.fail");

    Você também pode demonstrar o comportamento offline desabilitando redes Wi-Fi e celulares no dispositivo ou usando o modo avião.
2. Pressione **F5** para compilar e executar o aplicativo. Observe a falha de sincronização na atualização, quando o aplicativo é iniciado.
3. Digite os novos itens e observe que o envio falha com um status [CancelledByNetworkError] toda vez que você clicar em **Salvar**. Contudo, os novos itens de tarefas pendentes existem apenas no repositório local até serem enviados por push para o back-end do aplicativo móvel.  Em um aplicativo de produção, se você suprimir essas exceções, o aplicativo cliente se comportará como se ainda estivesse conectado ao back-end do aplicativo móvel.
4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.
5. (Opcional) No Visual Studio, abra o **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Pesquisador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo. Verifique se os dados no banco de dados back-end não foram alterados.
6. (Opcional) Use uma ferramenta REST, como o Fiddler ou Postman, para consultar seu back-end móvel, usando uma consulta GET no formulário `https://<your-mobile-app-backend-name>.azurewebsites.net/tables/TodoItem`.

## <a name="update-online-app"></a>Atualize o aplicativo para reconectar o back-end do Aplicativo Móvel
Nesta seção, você reconecta o aplicativo ao back-end do aplicativo móvel. Essas alterações simulam uma reconexão de rede no aplicativo.

Quando você executar o aplicativo pela primeira vez, o manipulador de eventos de `OnNavigatedTo` chamará `InitLocalStoreAsync`. Esse método, por sua vez, chama `SyncAsync` para sincronizar seu armazenamento local com o banco de dados de back-end. O aplicativo tenta sincronizar na inicialização.

1. Abra o App.xaml.cs no projeto compartilhado e remova o comentário de sua inicialização anterior do `MobileServiceClient` para usar a URL correta do aplicativo móvel.
2. Pressione a tecla **F5** para recompilar e executar o projeto. O aplicativo sincroniza as alterações locais com o back-end do aplicativo móvel do Azure usando operações de push e pull quando o manipulador de eventos de `OnNavigatedTo` é executado.
3. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end do aplicativo móvel do Azure e o armazenamento local.
4. No aplicativo, clique na caixa de seleção ao lado de alguns itens a serem concluídos no repositório local.

   `UpdateCheckedTodoItem` chama `SyncAsync` para sincronizar cada item concluído com o back-end do Aplicativo Móvel. `SyncAsync` chama push e pull. No entanto, **sempre que você executar um pull em uma tabela na qual o cliente fez alterações, um push será sempre executado automaticamente**. Esse comportamento garante que todas as tabelas no repositório local juntamente com os relacionamentos permaneçam consistentes. Esse comportamento pode resultar em um push inesperado.  Para obter mais informações sobre esse comportamento, confira [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

## <a name="api-summary"></a>Resumo da API
Para oferecer suporte a recursos offline dos serviços móveis, usamos o [IMobileServiceSyncTable] interface e inicializado [SyncContext][synccontext] com um banco de dados SQLite local. Quanto estiver offline, as operações CRUD normais nos aplicativos móveis funcionam como se o aplicativo ainda estivesse conectado, porém as operações ocorrem no repositório local. Os métodos a seguir são usados para sincronizar o repositório local com o servidor:

* **[PushAsync]** Como esse método é um membro do [IMobileServicesSyncContext], as alterações feitas em todas as tabelas são enviadas ao back-end. Somente os registros com as alterações locais são enviados para o servidor.
* **[PullAsync]** Um pull é iniciado de um [IMobileServiceSyncTable]. Quando houver alterações controladas na tabela, um envio por push implícito é executado para certificar-se de que todas as tabelas no repositório local, bem como suas relações, permaneçam consistentes. O parâmetro *pushOtherTables* controla se outras tabelas no contexto são enviadas em um push implícito. O *consulta* parâmetro usa um [IMobileServiceTableQuery\<T >][IMobileServiceTableQuery] ou cadeia de caracteres de consulta de OData para filtrar os dados retornados. O parâmetro *queryId* é usado para definir a sincronização incremental. Para saber mais, veja [Sincronização de Dados Offline em Aplicativos Móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).
* **[PurgeAsync]** Seu aplicativo deve chamar esse método periodicamente para limpar os dados obsoletos do repositório local. Use o parâmetro *force* quando for necessário limpar as alterações que ainda não foram sincronizadas.

Para saber mais sobre esses conceitos, veja [Sincronização de Dados Offline em Aplicativos Móveis do Azure](app-service-mobile-offline-data-sync.md#how-sync-works).

## <a name="more-info"></a>Mais informações
Os tópicos a seguir fornecem informações básicas adicionais sobre o recurso de sincronização offline dos Aplicativos Móveis:

* [Sincronização de Dados Offline em Aplicativos Móveis do Azure]
* [SDK do .NET de aplicativos móveis do Azure HOWTO][8]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]:#next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Sincronização de Dados Offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Criar um aplicativo do Windows]: app-service-mobile-windows-store-dotnet-get-started.md
[SQLite for Windows 8.1]: https://go.microsoft.com/fwlink/?LinkID=716919
[SQLite for Windows Phone 8.1]: https://go.microsoft.com/fwlink/?LinkID=716920
[SQLite for Windows 10]: https://go.microsoft.com/fwlink/?LinkID=716921
[synccontext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.synccontext(v=azure.10).aspx
[sqlite store nuget]: https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client.SQLiteStore/
[IMobileServiceSyncTable]: https://msdn.microsoft.com/library/azure/mt691742(v=azure.10).aspx
[IMobileServiceTableQuery]: https://msdn.microsoft.com/library/azure/dn250631(v=azure.10).aspx
[IMobileServicesSyncContext]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynccontext(v=azure.10).aspx
[MobileServicePushFailedException]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushfailedexception(v=azure.10).aspx
[Status]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushcompletionresult.status(v=azure.10).aspx
[CancelledByNetworkError]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.mobileservicepushstatus(v=azure.10).aspx
[PullAsync]: https://msdn.microsoft.com/library/azure/mt667558(v=azure.10).aspx
[PushAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileservicesynccontextextensions.pushasync(v=azure.10).aspx
[PurgeAsync]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.sync.imobileservicesynctable.purgeasync(v=azure.10).aspx
[8]: app-service-mobile-dotnet-how-to-use-client-library.md
