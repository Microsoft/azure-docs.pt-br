---
title: Usar a biblioteca .NET do executor em massa no Azure Cosmos DB para operações de importação e atualização em massa
description: Importe e atualize em massa os documentos de Azure Cosmos DB usando a biblioteca .NET do executor em massa.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 34aef5bd880e3ef080676fb9e90e62796d499e7b
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102429809"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>Use a biblioteca .NET do executor em massa para executar operações em massa no Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!NOTE]
> Essa biblioteca de executores em massa descrita neste artigo é mantida para aplicativos que usam a versão .NET SDK 2. x. Para novos aplicativos, você pode usar o **suporte em massa** que está diretamente disponível com o [SDK do .NET versão 3. x](tutorial-sql-api-dotnet-bulk-import.md) e não requer nenhuma biblioteca externa. 

> Se você estiver usando atualmente a biblioteca de executores em massa e planeja migrar para o suporte em massa no SDK mais recente, use as etapas no [Guia de migração](how-to-migrate-from-bulk-executor-library.md) para migrar seu aplicativo.

Este tutorial fornece instruções de como usar a biblioteca .NET do executor em massa para importar e atualizar documentos no contêiner do Azure Cosmos. Para saber mais sobre a biblioteca de executores em massa e como ela ajuda a aproveitar a produtividade e o armazenamento maciços, consulte o artigo [visão geral da biblioteca de executores em massa](bulk-executor-overview.md) . Neste tutorial, você verá um aplicativo .NET de exemplo que importa em massa documentos gerados aleatoriamente em um contêiner Cosmos do Azure. Após a importação, ele mostra como você pode atualizar em massa os dados importados, especificando os patches como operações a serem executadas em campos de documento específicos.

Atualmente, a biblioteca de executores em massa é suportada apenas pela API do SQL Azure Cosmos DB e por contas de API Gremlin. Este artigo descreve como usar a biblioteca .NET do executor em massa com contas da API do SQL. Para saber mais sobre como usar a biblioteca .NET do executor em massa com contas da API do Gremlin, consulte [executar operações em massa na API do Azure Cosmos DB Gremlin](bulk-executor-graph-dotnet.md).

## <a name="prerequisites"></a>Pré-requisitos

* Se você ainda não tiver o Visual Studio 2019 instalado, poderá baixar e usar o [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Certifique-se de habilitar o "desenvolvimento do Azure" durante a instalação do Visual Studio.

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

* Você pode [Experimentar o Azure Cosmos DB gratuitamente](https://azure.microsoft.com/try/cosmosdb/) sem uma assinatura do Azure, gratuitamente e sem compromisso. Ou, você pode usar o [emulador de Azure Cosmos DB](./local-emulator.md) com o `https://localhost:8081` ponto de extremidade. A Chave Primária é fornecida nas [Solicitações de autenticação](local-emulator.md#authenticate-requests).

* Crie uma conta de API SQL do Azure Cosmos DB usando as etapas descritas na seção [criar conta de banco de dados](create-sql-api-dotnet.md#create-account) do artigo de início rápido do .NET.

## <a name="clone-the-sample-application"></a>Clonar o aplicativo de exemplo

Agora, vamos mudar para trabalhar com código baixando um aplicativo .NET de exemplo do GitHub. Esse aplicativo executa operações em massa nos dados armazenados em sua conta do Azure Cosmos. Para clonar o aplicativo, abra um prompt de comando, navegue até o diretório onde você deseja copiá-lo e execute o seguinte comando:

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

O repositório clonado contém dois exemplos "BulkImportSample" e "BulkUpdateSample". Você pode abrir qualquer um dos aplicativos de exemplo, atualizar as cadeias de conexão no arquivo App.config com as cadeias de conexão da sua conta do Azure Cosmos DB, compilar a solução e executá-la.

O aplicativo "BulkImportSample" gera documentos aleatórios e os importa em massa para sua conta do Azure Cosmos. O aplicativo "BulkUpdateSample" atualiza em massa os documentos importados, especificando os patches como operações a serem executadas em campos de documento específicos. Nas próximas seções, você examinará o código em cada um desses aplicativos de exemplo.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Importar dados em massa para uma conta do Azure Cosmos

1. Navegue até a pasta "BulkImportSample" e abra o arquivo "BulkImportSample.sln".  

2. As cadeias de conexão do Azure Cosmos DB são recuperadas do arquivo de App.config, conforme mostrado no código a seguir:  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   O importador em massa cria um novo banco de dados e um contêiner com o nome do banco de dados, o nome do contêiner e os valores de taxa de transferência especificados no arquivo de App.config.

3. Em seguida, o objeto DocumentClient é inicializado com o modo de conexão Direct TCP:  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. O objeto BulkExecutor é inicializado com um valor de repetição alto para o tempo de espera e solicitações limitadas. E, em seguida, eles são definidos como 0 para passar o controle de congestionamento para BulkExecutor por toda a sua vida útil.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. O aplicativo chama a API BulkImportAsync. A biblioteca .NET fornece duas sobrecargas da API de importação em massa, uma que aceita uma lista de documentos JSON serializados e a outra que aceita uma lista de documentos POCO desserializados. Para saber mais sobre as definições de cada um desses métodos sobrecarregados, consulte a [documentação da API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync).

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **O método BulkImportAsync aceita os seguintes parâmetros:**
   
   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |enableUpsert    |   Um sinalizador para habilitar operações de Upsert nos documentos. Se um documento com a ID fornecida já existir, ele será atualizado. Por padrão, está definido como false.      |
   |disableAutomaticIdGeneration    |    Um sinalizador para desabilitar a geração automática de ID. Por padrão, está definido como true.     |
   |maxConcurrencyPerPartitionKeyRange    | O grau máximo de simultaneidade por intervalo de chave de partição, a configuração como null fará com que a biblioteca use um valor padrão de 20. |
   |maxInMemorySortingBatchSize     |  O número máximo de documentos extraídos do enumerador de documento, que é passado para a chamada à API em cada estágio. Para a fase de classificação na memória que ocorre antes da importação em massa, a definição desse parâmetro como NULL fará com que a biblioteca use o valor mínimo padrão (Documents. Count, 1 milhão).       |
   |cancellationToken    |    O token de cancelamento para sair normalmente da operação de importação em massa.     |

   **Definição de objeto de resposta de importação em massa** O resultado da chamada de API de importação em massa contém os seguintes atributos:

   |**Parâmetro**  |**Descrição**  |
   |---------|---------|
   |NumberOfDocumentsImported (long)   |  O número total de documentos que foram importados com êxito do total de documentos fornecidos para a chamada à API de importação em massa.       |
   |TotalRequestUnitsConsumed (double)   |   O total de unidades de solicitação (RU) consumidas pela chamada de API de importação em massa.      |
   |TotalTimeTaken (TimeSpan)    |   O tempo total gasto pela chamada à API de importação em massa para concluir a execução.      |
   |BadInputDocuments (List\<object>)   |     A lista de documentos com formato inválido que não foram importados com êxito na chamada de API de importação em massa. Corrija os documentos retornados e tente importar novamente. Os documentos com formato inválido incluem documentos cujo valor de ID não é uma cadeia de caracteres (null ou qualquer outro tipo de dados é considerado inválido).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Atualizar dados em massa em sua conta do Azure Cosmos

Você pode atualizar os documentos existentes usando a API BulkUpdateAsync. Neste exemplo, você definirá o `Name` campo como um novo valor e removerá o `Description` campo dos documentos existentes. Para obter o conjunto completo de operações de atualização com suporte, consulte a [documentação da API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

1. Navegue até a pasta "BulkUpdateSample" e abra o arquivo "BulkUpdateSample.sln".  

2. Defina os itens de atualização junto com as operações de atualização de campo correspondentes. Neste exemplo, você usará `SetUpdateOperation` para atualizar o `Name` campo e `UnsetUpdateOperation` remover o `Description` campo de todos os documentos. Você também pode executar outras operações, como incremento de um campo de documento por um valor específico, efetuar push de valores específicos para um campo de matriz ou remover um valor específico de um campo de matriz. Para saber mais sobre os diferentes métodos fornecidos pela API de atualização em massa, consulte a [documentação da API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate).

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. O aplicativo chama a API BulkUpdateAsync. Para saber mais sobre a definição do método BulkUpdateAsync, consulte a [documentação da API](/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync).  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **O método BulkUpdateAsync aceita os seguintes parâmetros:**

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   O grau máximo de simultaneidade por intervalo de chave de partição, definindo esse parâmetro como NULL fará com que a biblioteca use o valor padrão (20).   |
   |maxInMemorySortingBatchSize    |    O número máximo de itens de atualização extraídos do enumerador de itens de atualização passado para a chamada à API em cada estágio. Para a fase de classificação na memória que ocorre antes da atualização em massa, a definição desse parâmetro como NULL fará com que a biblioteca use o valor mínimo padrão (updateItems. Count, 1 milhão).     |
   | cancellationToken|O token de cancelamento para sair normalmente da operação de atualização em massa. |

   **Definição de objeto de resposta de atualização em massa** O resultado da chamada de API desatualização em massa contém os seguintes atributos:

   |**Parâmetro**  |**Descrição** |
   |---------|---------|
   |NumberOfDocumentsUpdated (long)    |   O número de documentos que foram atualizados com êxito do total de documentos fornecidos para a chamada à API de atualização em massa.      |
   |TotalRequestUnitsConsumed (double)   |    As unidades de solicitação totais (RUs) consumidas pela chamada à API de atualização em massa.    |
   |TotalTimeTaken (TimeSpan)   | O tempo total gasto pela chamada à API de atualização em massa para concluir a execução. |
    
## <a name="performance-tips"></a>Dicas de desempenho 

Considere os seguintes pontos para melhor desempenho ao usar a biblioteca de executores em massa:

* Para obter o melhor desempenho, execute o aplicativo de uma máquina virtual do Azure que esteja na mesma região que a região de gravação da sua conta do Azure Cosmos.  

* É recomendável que você crie uma instância de um único `BulkExecutor` objeto para todo o aplicativo em uma única máquina virtual que corresponda a um contêiner Cosmos específico do Azure.  

* Como uma única execução de API de operação em massa consome uma grande parte da CPU da máquina do cliente e da e/s de rede (isso acontece pela geração de várias tarefas internamente). Evite a geração de várias tarefas simultâneas no processo do aplicativo que executam chamadas à API de operação em massa. Se uma única chamada à API de operação em massa que está sendo executada em uma única máquina virtual não puder consumir a taxa de transferência do contêiner inteiro (se a taxa de transferência do contêiner > 1 milhão RU/s), é preferível criar máquinas virtuais separadas para executar simultaneamente as chamadas à API da operação em massa.  

* Verifique se o `InitializeAsync()` método é invocado depois de instanciar um objeto BulkExecutor para buscar o mapa de partição do contêiner de Cosmos de destino.  

* No App.Config do aplicativo, certifique-se de **gcServer** esteja habilitado para melhor desempenho
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* A biblioteca emite rastreamentos que podem ser coletados em um arquivo de log ou no console. Para habilitar ambos, adicione o código a seguir ao arquivo de App.Config do seu aplicativo.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os detalhes do pacote NuGet e as notas de versão, consulte os [detalhes do SDK do executor em massa](sql-api-sdk-bulk-executor-dot-net.md).
