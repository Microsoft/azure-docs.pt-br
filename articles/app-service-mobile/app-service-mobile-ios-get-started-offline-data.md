---
title: Habilitar a sincronização offline com aplicativos móveis do iOS | Microsoft Docs
description: Aprenda a usar os aplicativos móveis do Serviço de Aplicativo do Azure para colocar em cache e sincronizar dados offline em aplicativos iOS.
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: f7ae3e7a33ae7df70214ed171b00cc2accbaccb5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446366"
---
# <a name="enable-offline-syncing-with-ios-mobile-apps"></a>Habilitar a sincronização offline com aplicativos móveis do iOS
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está investindo em novos e integrados serviços essenciais para o desenvolvimento de aplicativos móveis. Os desenvolvedores podem usar **construir**, **teste** e **distribuir** services para configurar o pipeline de integração contínua e entrega. Depois que o aplicativo é implantado, os desenvolvedores podem monitorar o status e o uso do seu aplicativo usando o **Analytics** e **diagnóstico** serviços e entre em contato com usuários usando o **enviar por Push** serviço. Os desenvolvedores também podem aproveitar **Auth** autenticar seus usuários e **dados** serviço para manter e sincronizar dados do aplicativo na nuvem. Fazer check-out [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-offline-data) hoje mesmo.
>

## <a name="overview"></a>Visão geral
Este tutorial aborda a sincronização offline com o recurso Aplicativos Móveis do Serviço de Aplicativo do Azure para iOS. Com a sincronização offline, os usuários podem interagir com um aplicativo móvel para exibir, adicionar ou alterar dados, mesmo quando não têm conexão com a rede. As alterações são armazenadas em um banco de dados local. Quando o dispositivo estiver online novamente, as alterações são sincronizadas com o back-end remoto.

Se essa for sua primeira experiência com Aplicativos Móveis, você deve primeiro concluir o tutorial [Criar um aplicativo do iOS]. Se você não usar o projeto baixado do início rápido do servidor, deve adicionar os pacotes de extensão de acesso de dados ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para saber mais sobre o recurso de sincronização offline, confira [Sincronização de dados offline nos Aplicativos Móveis].

## <a name="review-sync"></a>Examine o código de sincronização do cliente
O projeto cliente que você baixou para o tutorial [Criar um aplicativo do iOS] já contém o código que oferece suporte à sincronização offline usando um banco de dados local baseado em Dados Básicos. Esta seção resume o que já está incluso no código do tutorial. Para obter uma visão geral conceitual do recurso, confira [Sincronização de dados offline nos Aplicativos Móveis].

Usando o recurso de sincronização de dados offline dos Aplicativos Móveis, os usuários podem interagir com um banco de dados local mesmo quando a rede estiver inacessível. Para usar esses recursos em seu aplicativo, inicialize o contexto de sincronização de `MSClient` e faça referência a um repositório local. Em seguida, faça referência à sua tabela por meio da interface **MSSyncTable**.

Em **QSTodoService.m** (Objective-C) ou em **ToDoTableViewController.swift** (Swift), observe que o tipo do membro **syncTable** é **MSSyncTable**. A sincronização offline usa esta interface de tabela de sincronização em vez de **MSTable**. Ao usar uma tabela de sincronização, todas as operações vão para o armazenamento local e são sincronizadas somente com o back-end remoto com operações de push e pull explícitas.

 Para obter uma referência a uma tabela de sincronização, use o método **syncTableWithName** no `MSClient`. Para remover a funcionalidade de sincronização offline, use **tableWithName**.

Antes de qualquer operação de tabela poder ser executada, o armazenamento local deve ser inicializado. Este é o código relevante:

* **Objective-C**. No método **QSTodoService.init**:

   ```objc
   MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
   self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
   ```    
* **Swift**. No método **ToDoTableViewController.viewDidLoad**:

   ```swift
   let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
   let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
   self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
   client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)
   ```
   Isso cria um repositório local usando a interface `MSCoreDataStore`, que é fornecida no SDK de Aplicativos Móveis. Como alternativa, é possível fornecer um repositório local diferente implementando o protocolo `MSSyncContextDataSource`. Além disso, usa-se o primeiro parâmetro de **MSSyncContext** para especificar um manipulador de conflito. Já que passamos `nil`, obteremos o manipulador de conflito padrão, que falha em qualquer conflito.

Agora vamos executar a operação de sincronização em si e obter dados do back-end remoto:

* **Objective-C**. Primeiro, `syncData` envia novas alterações por push e chama **pullData** para obter dados do back-end remoto. Por sua vez, o método **pullData** obtém novos dados que correspondem a uma consulta:

   ```objc
   -(void)syncData:(QSCompletionBlock)completion
   {
       // Push all changes in the sync context, and then pull new data.
       [self.client.syncContext pushWithCompletion:^(NSError *error) {
           [self logErrorIfNotNil:error];
           [self pullData:completion];
       }];
   }

   -(void)pullData:(QSCompletionBlock)completion
   {
       MSQuery *query = [self.syncTable query];

       // Pulls data from the remote server into the local table.
       // We're pulling all items and filtering in the view.
       // Query ID is used for incremental sync.
       [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
           [self logErrorIfNotNil:error];

           // Lets the caller know that we have finished.
           if (completion != nil) {
               dispatch_async(dispatch_get_main_queue(), completion);
           }
       }];
   }
   ```
* **Swift**:
   ```swift
   func onRefresh(sender: UIRefreshControl!) {
      UIApplication.sharedApplication().networkActivityIndicatorVisible = true

      self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
          (error) -> Void in

          UIApplication.sharedApplication().networkActivityIndicatorVisible = false

          if error != nil {
              // A real application would handle various errors like network conditions,
              // server conflicts, etc. via the MSSyncContextDelegate
              print("Error: \(error!.description)")

              // We will discard our changes and keep the server's copy for simplicity
              if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                  for opError in opErrors {
                      print("Attempted operation to item \(opError.itemId)")
                      if (opError.operation == .Insert || opError.operation == .Delete) {
                          print("Insert/Delete, failed discarding changes")
                          opError.cancelOperationAndDiscardItemWithCompletion(nil)
                      } else {
                          print("Update failed, reverting to server's copy")
                          opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                      }
                  }
              }
          }
          self.refreshControl?.endRefreshing()
      }
   }
   ```

Na versão do Objective-C, em `syncData`, primeiro chamamos **pushWithCompletion** no contexto da sincronização. Esse método é um membro de `MSSyncContext` (e não da tabela de sincronização em si) porque envia por push as alterações para todas as tabelas. Somente os registros que foram modificados localmente de alguma forma (por meio de operações CUD) são enviados ao servidor. Em seguida, o auxiliar **pullData** é chamado, o que chama **MSSyncTable.pullWithQuery** para recuperar os dados remotos e armazená-los no banco de dados local.

Na versão do Swift, porque a operação de envio não era estritamente necessária, não há nenhuma chamada para **pushWithCompletion**. Se houver alterações pendentes no contexto de sincronização para a tabela que está executando uma operação de push/pull, push é sempre emitido primeiro. No entanto, se você tiver mais de uma tabela de sincronização, é melhor chamar explicitamente por push para garantir que tudo esteja consistente em todas as tabelas relacionadas.

Em ambas as versões Objective-C e Swift, o método **pullWithQuery** permite que você especifique uma consulta para filtrar os registros que deseja recuperar. Neste exemplo, a consulta recupera todos os registros na tabela remota `TodoItem`.

O segundo parâmetro de **pullWithQuery** é uma ID de consulta usada para *sincronização incremental*. A sincronização incremental recupera somente os registros modificados desde a última sincronização, usando o carimbo de data/hora `UpdatedAt` do registro (chamado de `updatedAt` no repositório local). A ID da consulta deve ser uma cadeia de caracteres descritiva que é exclusiva para cada consulta lógica em seu aplicativo. Se desejar sair da sincronização incremental, passe `nil` como a ID da consulta. Observe que isso pode ser potencialmente ineficiente, já que recupera todos os registros de cada operação de recepção.

O aplicativo Objective-C é sincronizado ao modificar ou adicionar dados, quando um usuário executa a atualização e na inicialização.

O aplicativo Swift é sincronizado quando um usuário executa a atualização e na inicialização.

Como o aplicativo é sincronizado sempre que dados são modificados (Objective-C) ou sempre que o aplicativo é iniciado (Objective-C e Swift), o aplicativo pressupõe que o usuário está online. Em uma seção posterior, atualizaremos o aplicativo para que os usuários possam editar mesmo quando estiverem offline.

## <a name="review-core-data"></a>Examinar o modelo de dados principais
Ao usar o armazenamento offline de Dados Básicos, você precisa definir determinadas tabelas e campos em seu modelo de dados. O aplicativo de exemplo já inclui um modelo de dados com o formato correto. Nesta seção percorreremos essas tabelas e veremos como são usadas.

Abra **QSDataModel.xcdatamodeld**. Há quatro tabelas definidas - três que são usadas pelo SDK e uma usada para os itens pendentes:
  * MS_TableOperations: Rastreia os itens que precisam ser sincronizados com o servidor.
  * MS_TableOperationErrors: Rastreia os erros que ocorrem durante a sincronização offline.
  * MS_TableConfig: Rastreia o último horário atualizado da última operação de sincronização para todas as operações de pull.
  * TodoItem: Armazena os itens pendentes. As colunas do sistema **createdAt**, **updatedAt** e **version** são propriedades opcionais do sistema.

> [!NOTE]
> O SDK dos Aplicativos Móveis reserva nomes de coluna que começam com " **``** ". Não use esse prefixo em algo diferente das colunas do sistema. Caso contrário, os nomes de coluna serão modificados ao usar o back-end remoto.
>
>

Ao usar o recurso de sincronização offline, você define as três tabelas do sistema e a tabela de dados.

### <a name="system-tables"></a>Tabelas do sistema

**MS_TableOperations**  

![Atributos da tabela MS_TableOperations][defining-core-data-tableoperations-entity]

| Atributo | Type |
| --- | --- |
| id | Integer 64 |
| itemId | String |
| properties | Binary Data |
| table | String |
| tableKind | Integer 16 |


**MS_TableOperationErrors**

 ![Atributos da tabela MS_TableOperationErrors][defining-core-data-tableoperationerrors-entity]

| Atributo | Type |
| --- | --- |
| id |String |
| operationId |Integer 64 |
| properties |Binary Data |
| tableKind |Integer 16 |

 **MS_TableConfig**

 ![][defining-core-data-tableconfig-entity]

| Atributo | Type |
| --- | --- |
| id |String |
| key |String |
| keyType |Integer 64 |
| table |String |
| value |String |

### <a name="data-table"></a>Tabela de dados

**TodoItem**

| Atributo | Type | Observação |
| --- | --- | --- |
| id | String, marcadas como obrigatórias |Chave primária no repositório remoto |
| concluído | Boolean | Campo To-do item |
| text |string |Campo To-do item |
| createdAt | Data | (opcional) É mapeado para a propriedade do sistema **createdAt** |
| updatedAt | Data | (opcional) É mapeado para a propriedade do sistema **updatedAt** |
| version | String | (opcional) Usado para detectar conflitos, é mapeado para a versão |

## <a name="setup-sync"></a>Alterar o comportamento de sincronização do aplicativo
Nesta seção, você altera o aplicativo para que ele não sincronize na inicialização ou ao inserir e atualizar itens. Ele sincroniza somente quando o botão de atualização é executado.

**Objective-C**:

1. Em **QSTodoListViewController.m**, altere o método **viewDidLoad** para remover a chamada para `[self refresh]` no final do método. Agora os dados não são sincronizados com o servidor na inicialização do aplicativo. Em vez disso, são sincronizados com o conteúdo do repositório local.
2. Em **QSTodoService.m**, modifique a definição de `addItem` para que não ocorra sincronização após o item ser inserido. Remova o bloco `self syncData` e substitua-o pelo seguinte:

   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```
3. Modifique a definição de `completeItem` como mencionado anteriormente. Remova o bloco `self syncData` e substitua-o pelo seguinte:
   ```objc
   if (completion != nil) {
       dispatch_async(dispatch_get_main_queue(), completion);
   }
   ```

**Swift**:

Em `viewDidLoad` de **ToDoTableViewController.swift**, comente essas duas linhas para parar a sincronização na inicialização do aplicativo. No momento da redação deste artigo, o aplicativo Todo do Swift não atualiza o serviço quando alguém adiciona ou conclui um item, somente na inicialização do aplicativo.

   ```swift
  self.refreshControl?.beginRefreshing()
  self.onRefresh(self.refreshControl)
```

## <a name="test-app"></a>Testar o aplicativo
Nesta seção, você se conecta a uma URL inválida para simular um cenário offline. Ao adicionar itens de dados, eles serão mantidos no repositório local de Dados Básicos, mas não serão sincronizados com o back-end do aplicativo móvel.

1. Altere a URL do aplicativo móvel em **QSTodoService.m** para uma URL inválida e execute novamente o aplicativo:

   **Objective-C**. Em QSTodoService.m:
   ```objc
   self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   ```
   **Swift**. Em ToDoTableViewController.swift:
   ```swift
   let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
   ```
2. Adicione alguns itens pendentes. Feche o simulador (ou force o fechamento do aplicativo) e reinicie. Verifique se suas mudanças foram mantidas.

3. Exiba o conteúdo da tabela **TodoItem** remota:
   * Para um back-end do Node.js, vá para o [Portal do Azure](https://portal.azure.com/) e, no back-end de seu aplicativo móvel, clique em **Tabelas simples** > **TodoItem**.  
   * Para um back-end do .NET, use uma ferramenta SQL, como o SQL Server Management Studio, ou um cliente REST, como o Fiddler ou o Postman.  

4. Confirme que os novos itens *não* tenham sido sincronizados com o servidor.

5. Altere a URL novamente para a correta em **QSTodoService.m** e execute o aplicativo outra vez.

6. Faça o gesto de atualização puxando a lista de itens para baixo.  
Um controle giratório de progresso é exibido.

7. Exiba os dados de **TodoItem** novamente. Os itens de tarefas novos e modificados agora devem ser exibidos.

## <a name="summary"></a>Resumo
Para dar suporte a esse recurso de sincronização offline, usamos a interface `MSSyncTable` e inicializamos `MSClient.syncContext` com um repositório local. Nesse caso, o repositório local era um banco de dados baseado em Dados Básicos.

Ao usar um repositório local de Dados Básicos, você deve definir várias tabelas com as [propriedades corretas do sistema](#review-core-data).

As operações CRUD (criar, ler, atualizar e excluir) normais nos aplicativos móveis funcionam como se o aplicativo ainda estivesse conectado, mas todas as operações ocorrem no repositório local.

Ao sincronizar o repositório local com o servidor, usamos o método **MSSyncTable.pullWithQuery**.

## <a name="additional-resources"></a>Recursos adicionais
* [Sincronização de dados offline nos Aplicativos Móveis]
* [Cobertura em nuvem: Sincronização Offline nos Serviços Móveis do Azure] \(O vídeo é sobre Serviços Móveis, mas a sincronização offline de Aplicativos Móveis funciona de maneira semelhante.\)

<!-- URLs. -->


[Criar um aplicativo do iOS]: app-service-mobile-ios-get-started.md
[Sincronização de dados offline nos Aplicativos Móveis]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cobertura em nuvem: Sincronização offline nos Serviços Móveis do Azure]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
