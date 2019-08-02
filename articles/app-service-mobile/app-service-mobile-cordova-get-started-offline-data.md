---
title: Habilitar a sincronização offline para seu Aplicativo Móvel do Azure (Cordova) | Microsoft Docs
description: Aprenda a usar o Aplicativo Móvel do Serviço de Aplicativo para o cache e a sincronização de dados offline no aplicativo Cordova
documentationcenter: cordova
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: 1a3f685d-f79d-4f8b-ae11-ff96e79e9de9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-cordova-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 04c8e7b2b60a60f17c49862d5c17793c16456032
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443515"
---
# <a name="enable-offline-sync-for-your-cordova-mobile-app"></a>Habilitar a sincronização offline para seu aplicativo móvel Cordova
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-cordova-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Visão geral
Este tutorial apresenta o recurso de sincronização offline dos Aplicativos Móveis do Azure para o Cordova. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, &mdash;exibindo, adicionando ou modificando dados&mdash;, mesmo quando não há conexão de rede. As alterações são armazenadas em um banco de dados local.  Quando o dispositivo estiver online novamente, essas alterações serão sincronizadas com o serviço remoto.

Este tutorial se baseia na solução de início rápido do Cordova para os Aplicativos Móveis criados quando você conclui o tutorial [Início rápido do Apache Cordova]. Neste tutorial, você atualizará a solução de início rápido para adicionar recursos offline dos Aplicativos Móveis do Azure.  Também destacamos o código especificamente offline no aplicativo.

Para saber mais sobre o recurso de sincronização offline, confira o tópico [Sincronização de Dados Offline em Aplicativos Móveis do Azure]. Para obter detalhes sobre o uso da API, consulte a [documentação da API](https://azure.github.io/azure-mobile-apps-js-client).

## <a name="add-offline-sync-to-the-quickstart-solution"></a>Adicionar a sincronização offline à solução de início rápido
O código da sincronização offline deve ser adicionado ao aplicativo. A sincronização offline requer o plug-in cordova-sqlite-storage, que é adicionado automaticamente ao seu aplicativo quando o plug-in dos Aplicativos Móveis do Azure está incluído no projeto. O projeto de Início Rápido inclui os dois plug-ins.

1. No Gerenciador de Soluções do Visual Studio, abra o index.js e substitua o código a seguir

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable;      // Reference to a table endpoint on backend

    por este código:

        var client,            // Connection to the Azure Mobile App backend
           todoItemTable,      // Reference to a table endpoint on backend
           syncContext;        // Reference to offline data sync context

2. Em seguida, substitua o código a seguir:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');

    por este código:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net');
        var store = new WindowsAzure.MobileServiceSqliteStore('store.db');

        store.defineTable({
          name: 'todoitem',
          columnDefinitions: {
              id: 'string',
              text: 'string',
              complete: 'boolean',
              version: 'string'
          }
        });

        // Get the sync context from the client
        syncContext = client.getSyncContext();

    As adições de código anteriores inicializam o armazenamento local e definem uma tabela local que corresponde aos valores da coluna usados no back-end do Azure. (Você não precisa incluir todos os valores da coluna nesse código.)  O campo `version` é mantido pelo back-end móvel e é usado para resolução de conflitos.

    Você obtém uma referência para o contexto de sincronização chamando **getSyncContext**. O contexto de sincronização ajuda a preservar relações da tabela controlando e enviando por push as alterações em todas as tabelas que um aplicativo cliente tenha modificado quando o `.push()` é chamado.

3. Atualize a URL do aplicativo para a URL do Aplicativo Móvel.

4. Em seguida, substitua este código:

        todoItemTable = client.getTable('todoitem'); // todoitem is the table name

    por este código:

        // Initialize the sync context with the store
        syncContext.initialize(store).then(function () {

        // Get the local table reference.
        todoItemTable = client.getSyncTable('todoitem');

        syncContext.pushHandler = {
            onConflict: function (pushError) {
                // Handle the conflict.
                console.log("Sync conflict! " + pushError.getError().message);
                // Update failed, revert to server's copy.
                pushError.cancelAndDiscard();
              },
              onError: function (pushError) {
                  // Handle the error
                  // In the simulated offline state, you get "Sync error! Unexpected connection failure."
                  console.log("Sync error! " + pushError.getError().message);
              }
        };

        // Call a function to perform the actual sync
        syncBackend();

        // Refresh the todoItems
        refreshDisplay();

        // Wire up the UI Event Handler for the Add Item
        $('#add-item').submit(addItemHandler);
        $('#refresh').on('click', refreshDisplay);

    O código anterior inicializa o contexto de sincronização e, em seguida, chama getSyncTable (em vez de getTable) para obter uma referência para a tabela local.

    Esse código usa o banco de dados local para todas as operações da tabela para criar, ler, atualizar e excluir (CRUD).

    Este exemplo executa o tratamento de erros simples nos conflitos de sincronização. Um aplicativo real trataria os diversos erros, como condições de rede, conflitos do servidor e outros. Para obter exemplos de código, confira o [exemplo de sincronização offline].

5. Em seguida, adicione esta função para executar a sincronização real.

        function syncBackend() {

          // Sync local store to Azure table when app loads, or when login complete.
          syncContext.push().then(function () {
              // Push completed

          });

          // Pull items from the Azure table after syncing to Azure.
          syncContext.pull(new WindowsAzure.Query('todoitem'));
        }

    Você decide quando enviar as alterações por push para o back-end do Aplicativo Móvel chamando **syncContext.push()** . Por exemplo, você poderia chamar **syncBackend** em um manipulador de eventos ligado a um botão de sincronização.

## <a name="offline-sync-considerations"></a>Considerações sobre a sincronização offline

No exemplo, o método **push** de **syncContext** apenas é chamado na inicialização do aplicativo na função de callback do logon.  Em um aplicativo real, você também poderia disparar manualmente essa funcionalidade de sincronização ou quando o estado da rede muda.

Quando um pull é executado em uma tabela que tenha atualizações locais pendentes controladas pelo contexto, essa operação de pull automaticamente dispara um envio por push. Durante a atualização, a adição e a conclusão de itens neste exemplo, você pode omitir a chamada **push** explícita, pois ela pode ser redundante.

No código fornecido, todos os registros na tabela remota todoItem são consultados, mas também é possível filtrar os registros passando uma id de consulta e uma consulta para **push**. Para obter mais informações, consulte a seção *Sincronização Incremental* em [Sincronização de Dados Offline em Aplicativos Móveis do Azure].

## <a name="optional-disable-authentication"></a>(Opcional) Desabilitar a autenticação

Se você não desejar configurar a autenticação antes da sincronização offline de teste, remova a marca de comentário da função de callback para logon, mas deixe o código dentro dessa função sem comentário.  Após remover as marcas de comentário das linhas de logon, o código será:

      // Login to the service.
      // client.login('twitter')
      //    .then(function () {
        syncContext.initialize(store).then(function () {
          // Leave the rest of the code in this callback function  uncommented.
                ...
        });
      // }, handleError);

Agora, o aplicativo será sincronizado com o back-end do Azure quando você executar o aplicativo.

## <a name="run-the-client-app"></a>Execute o aplicativo cliente
Com a sincronização offline agora habilitada, você poderá executar o aplicativo cliente pelo menos uma vez em cada plataforma para preencher o banco de dados do repositório local. Posteriormente, simule um cenário offline e modificará os dados no armazenamento local enquanto o aplicativo estiver offline.

## <a name="optional-test-the-sync-behavior"></a>(Opcional) Testar o comportamento da sincronização
Nesta seção, você modificará o projeto do cliente para simular um cenário offline usando uma URL de aplicativo inválida para o back-end. Quando você adicionar ou alterar itens de dados, essas alterações são mantidas no repositório local, mas não são sincronizadas com o armazenamento de dados de back-end até que a conexão seja restabelecida.

1. No Gerenciador de Soluções, abra o arquivo de projeto index.js e altere a URL do aplicativo para apontar para uma URL inválida, como no código a seguir:

        client = new WindowsAzure.MobileServiceClient('http://yourmobileapp.azurewebsites.net-fail');

2. Em index.html, atualize o elemento CSP `<meta>` com a mesma URL inválida.

        <meta http-equiv="Content-Security-Policy" content="default-src 'self' data: gap: http://yourmobileapp.azurewebsites.net-fail; style-src 'self'; media-src *">

3. Compile e execute o aplicativo cliente, e observe que uma exceção é registrada no console quando o aplicativo tenta sincronizar com o back-end após o logon. Todos os novos itens adicionados existem apenas no repositório local até que possam ser enviados por push para o back-end móvel. O aplicativo cliente se comporta como se ele estivesse conectado ao back-end.

4. Feche o aplicativo e reinicie-o para verificar se os novos itens que você criou persistem no armazenamento local.

5. (Opcional) Use o Visual Studio para exibir a tabela de Banco de Dados SQL do Azure para ver se os dados no banco de dados do back-end não foram alterados.

    No Visual Studio, abra **Gerenciador de Servidores**. Navegue até o banco de dados em **Azure**->**Bancos de dados SQL**. Clique com o botão direito do mouse em seu banco de dados e selecione **Abrir no Pesquisador de Objetos do SQL Server**. Agora você pode navegar até sua tabela de banco de dados SQL e seu conteúdo.

## <a name="optional-test-the-reconnection-to-your-mobile-backend"></a>(Opcional) Testar a reconexão com seu back-end móvel

Nesta seção, reconecte o aplicativo ao back-end móvel, que simula o aplicativo voltando ao estado online. Quando você fizer logon, os dados serão sincronizados com o back-end móvel.

1. Abra index.js novamente e restaure a URL do aplicativo.
2. Reabra o index.html e corrija a URL do aplicativo no elemento CSP `<meta>` .
3. Recompile e execute o aplicativo cliente. O aplicativo tenta sincronizar com o back-end do aplicativo móvel após fazer logon. Verifique se nenhuma exceção está registrada no console de depuração.
4. (Opcional) Exiba os dados atualizados usando o Pesquisador de Objetos do SQL Server ou uma ferramenta REST, como o Fiddler. Observe que os dados foram sincronizados entre o banco de dados de back-end e o repositório local.

    Observe que os dados foram sincronizados entre o banco de dados e o armazenamento local e contém os itens que você adicionou enquanto seu aplicativo estava desconectado.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de Dados Offline em Aplicativos Móveis do Azure]
* [Ferramentas do Visual Studio para Apache Cordova]

## <a name="next-steps"></a>Próximas etapas
* Analise os recursos mais avançados de sincronização offline, como a resolução de conflitos no [exemplo de sincronização offline]
* Analise a referência da API de sincronização offline na [Documentação de API](https://azure.github.io/azure-mobile-apps-js-client).

<!-- ##Summary -->

<!-- Images -->

<!-- URLs. -->
[Início rápido do Apache Cordova]: app-service-mobile-cordova-get-started.md
[exemplo de sincronização offline]: https://github.com/Azure-Samples/app-service-mobile-cordova-client-conflict-handling
[Sincronização de Dados Offline em Aplicativos Móveis do Azure]: app-service-mobile-offline-data-sync.md
[Cloud Cover: Offline Sync in Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Adding Authentication]: app-service-mobile-cordova-get-started-users.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Visual Studio Community 2015]: https://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[ASP.NET Server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
