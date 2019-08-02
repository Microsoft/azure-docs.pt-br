---
title: Introdução ao armazenamento de filas do Azure usando .NET – armazenamento do Azure
description: As filas do Azure fornecem uma mensagem assíncrona e confiável entre os componentes do aplicativo. A mensagem na nuvem permite que os componentes do aplicativo se dimensionem de modo independente.
services: storage
author: mhopkins-msft
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mhopkins
ms.reviewer: cbrooks
ms.subservice: queues
ms.openlocfilehash: 59995715ab42b4682befa7d1512b14427740dea2
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446851"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Introdução ao armazenamento de Fila do Azure usando o .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Visão geral

O armazenamento de filas do Azure fornece mensagens na nuvem entre os componentes do aplicativo. Na criação de aplicativos para escala, os componentes do aplicativo geralmente são desassociados, para que possam ser redimensionados independentemente. O armazenamento de filas fornece mensagens assíncronas para a comunicação entre os componentes do aplicativo, estando eles em execução na nuvem, na área de trabalho, em um servidor local ou em um dispositivo móvel. O armazenamento de Fila também dá suporte ao gerenciamento de tarefas assíncronas e à criação de fluxos de trabalho do processo.

### <a name="about-this-tutorial"></a>Sobre este tutorial

Este tutorial mostra como gravar código .NET para alguns cenários comuns usando o armazenamento de Fila do Azure. Os cenários abordados incluem a criação e a exclusão de filas e a adição, a leitura e a exclusão de mensagens da fila.

**Tempo estimado para conclusão:** 45 minutos

### <a name="prerequisites"></a>Pré-requisitos

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage client biblioteca comum para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Biblioteca de cliente de fila de armazenamento do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Gerenciador de Configurações do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* [Uma conta do Armazenamento do Azure](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Em seguida, configure seu ambiente de desenvolvimento no Visual Studio para poder ficar pronto para experimentar os exemplos de código neste guia.

### <a name="create-a-windows-console-application-project"></a>Criar um projeto de aplicativo de console do Windows.

No Visual Studio, crie um novo aplicativo de console do Windows. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. As etapas são semelhantes em outras versões do Visual Studio.

1. Selecione **Arquivo** > **Novo** > **Projeto**
2. Selecione **plataforma** > **Windows**
3. Selecione **Aplicativo do Console (.NET Framework)**
4. Selecione **Avançar**
5. No **nome do projeto** , insira um nome para seu aplicativo
6. Escolha **Criar**

Todos os exemplos de código neste tutorial podem ser adicionados para o **Main ()** método do seu aplicativo de console **Program.cs** arquivo.

Você pode usar as bibliotecas de cliente de armazenamento do Azure em qualquer tipo de aplicativo .NET, incluindo um aplicativo de web ou serviço de nuvem do Azure e aplicativos móveis e desktops. Neste guia, usamos um aplicativo de console para simplificar.

### <a name="use-nuget-to-install-the-required-packages"></a>Use o NuGet para instalar os pacotes necessários

Você precisará referenciar os seguintes três pacotes em seu projeto para concluir este tutorial:

* [Microsoft Azure Storage Client biblioteca comum para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): este pacote fornece acesso programático aos recursos de dados em sua conta de armazenamento.
* [Biblioteca de fila de armazenamento do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Essa biblioteca de cliente permite trabalhar com o serviço de fila de armazenamento do Microsoft Azure para armazenar mensagens que podem ser acessadas por um cliente.
* [Biblioteca do Gerenciador de Configuração do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/): este pacote fornece uma classe para analisar uma cadeia de conexão em um arquivo de configuração, independentemente do local em que o aplicativo está sendo executado.

Você pode usar o NuGet para obter esses pacotes. Siga estas etapas:

1. Clique com o botão direito do mouse no seu projeto no **Gerenciador de Soluções** e escolha **Gerenciar Pacotes NuGet**.
2. Selecione **Procurar**
3. Pesquise online por "Microsoft.Azure.Storage.Queue" e selecione **instalar** para instalar a biblioteca de cliente de armazenamento e suas dependências. A biblioteca de Storage. Common, que é uma dependência de biblioteca de fila também será instalado.
4. Pesquise online por "Microsoft.Azure.ConfigurationManager" e selecione **instalar** para instalar o Gerenciador de configuração do Azure.

> [!NOTE]
> Os pacotes de bibliotecas de cliente de armazenamento também estão incluídos na [SDK do Azure para .NET](https://azure.microsoft.com/downloads/). No entanto, é recomendável que você também instale as bibliotecas de cliente de armazenamento do NuGet para garantir que você sempre tenha as versões mais recentes.
>
> As dependências do ODataLib nas bibliotecas de cliente do armazenamento para .NET são resolvidas por pacotes do ODataLib disponíveis no NuGet, não do WCF Data Services. As bibliotecas do ODataLib podem ser baixadas diretamente ou referenciadas por seu projeto de código por meio do NuGet. Os pacotes ODataLib específicos usados pelas bibliotecas de cliente de armazenamento são [OData](https://nuget.org/packages/Microsoft.Data.OData/), [Edm](https://nuget.org/packages/Microsoft.Data.Edm/), e [espacial](https://nuget.org/packages/System.Spatial/). Embora essas bibliotecas são usadas pelas classes de armazenamento de tabelas do Azure, elas são dependências necessárias para a programação com as bibliotecas de cliente de armazenamento.

### <a name="determine-your-target-environment"></a>Determinar o ambiente de destino

Você tem duas opções de ambiente para executar os exemplos neste guia:

* Você pode executar o código em uma conta de Armazenamento do Azure na nuvem.
* Você pode executar o código em um emulador de armazenamento do Azure. O emulador de armazenamento é um ambiente local que emula uma conta de Armazenamento do Azure na nuvem. O emulador é uma opção gratuita para testar e depurar seu código enquanto o aplicativo está em desenvolvimento. O emulador usa uma conta e chave bem conhecidas. Para saber mais, confira [Usar o emulador de armazenamento do Azure para desenvolvimento e teste](../common/storage-use-emulator.md).

Se você estiver selecionando uma conta de armazenamento na nuvem, copie a chave de acesso primário para sua conta de armazenamento a partir do portal do Azure. Para obter mais informações, confira [Chaves de acesso](../common/storage-account-manage.md#access-keys).

> [!NOTE]
> Você pode utilizar o emulador de armazenamento como destino para evitar quaisquer custos associados ao Armazenamento do Azure. Todavia, se você escolher utilizar como destino uma conta de armazenamento do Azure na nuvem, os custos para realização das operações neste tutorial serão insignificantes.

### <a name="configure-your-storage-connection-string"></a>Configurar a cadeia de conexão de armazenamento

As bibliotecas de cliente de armazenamento do Azure para o suporte do .NET usando uma cadeia de caracteres de conexão de armazenamento para configurar pontos de extremidade e credenciais para acessar os serviços de armazenamento. A melhor maneira de manter a cadeia de conexão de armazenamento é em um arquivo de configuração.

Para obter mais detalhes sobre as sequências de conexão, veja [Configurar uma cadeia de conexão para o Armazenamento do Azure](../common/storage-configure-connection-string.md).

> [!NOTE]
> Sua chave de conta de armazenamento é semelhante para a senha raiz da sua conta de armazenamento. Sempre tenha cuidado para proteger a chave de sua conta de armazenamento. Evite distribuí-la a outros usuários, embuti-la no código ou salvá-lo em um arquivo de texto sem formatação que esteja acessível a outras pessoas. Regenere a chave usando o portal do Azure se você achar que ela pode ter sido comprometida.

Para configurar sua cadeia de caracteres de conexão, abra o **App. config** arquivo no Gerenciador de soluções no Visual Studio. Adicionar o conteúdo do **\<appSettings\>** elemento mostrado abaixo. Substitua *nome da conta* com o nome da sua conta de armazenamento, e *chave de conta* com sua chave de acesso da conta:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Por exemplo, a configuração é semelhante a:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Para direcionar o emulador de armazenamento, você pode usar um atalho que mapeia para o nome de conta e chave bem conhecidos. Nesse caso, a configuração da cadeia de conexão é:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Adicionar diretivas using

Adicione as seguintes diretivas `using` à parte superior do arquivo `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Copiar suas credenciais no Portal do Azure

O código de exemplo precisa autorizar o acesso à sua conta de armazenamento. Para autorizar, forneça suas credenciais de conta de armazenamento ao aplicativo na forma de uma cadeia de conexão. Para verificar as credenciais da conta de armazenamento:

1. Navegue até o [Portal do Azure](https://portal.azure.com).
2. Localize sua cadeia de conexão.
3. Na seção **Configurações** da visão geral da conta de armazenamento, selecione **Chaves de acesso**. Suas chaves de acesso da conta são exibidas, bem como a cadeia de conexão completa para cada chave.
4. Encontre o valor da **Cadeia de conexão** em **key1** e clique no botão **Copiar** para copiar a cadeia de conexão. Você adicionará o valor de cadeia de conexão para uma variável de ambiente na próxima etapa.

    ![Captura de tela mostrando como copiar uma cadeia de conexão do portal do Azure](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Analisar a cadeia de conexão

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Criar o cliente do serviço Fila

A classe [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) permite que você recupere as filas armazenadas no Armazenamento de filas. Veja uma maneira de criar o cliente de serviço:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Agora você está pronto para escrever um código que lê e grava dados no Armazenamento de Filas.

## <a name="create-a-queue"></a>Criar uma fila

Este exemplo mostra como criar uma fila se ela ainda não existe:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Inserir uma mensagem em uma fila

Para inserir uma mensagem em uma fila existente, primeiro crie uma nova [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Em seguida, chame o método [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet) . Um **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de **bytes**. Este é o código que cria uma fila (se ela não existir) e insere a mensagem 'Hello, World':

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Espiar a próxima mensagem

Você pode inspecionar a mensagem na frente de uma fila sem removê-la da fila chamando o método [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet) .

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

## <a name="change-the-contents-of-a-queued-message"></a>Alterar o conteúdo de uma mensagem na fila

Você pode alterar o conteúdo de uma mensagem in-loco na fila. Se a mensagem representar uma tarefa de trabalho, você poderá usar esse recurso para atualizar o status da tarefa de trabalho. O código a seguir atualiza a mensagem da fila com novo conteúdo e define o tempo limite de visibilidade para estender mais 60 segundos. Isso salva o estado do trabalho associado à mensagem e dá ao cliente mais um minuto para continuar trabalhando na mensagem. Você pode usar essa técnica para acompanhar fluxos de trabalho de várias etapas em mensagens em fila, sem a necessidade de começar desde o início, caso uma etapa de processamento falhar devido a uma falha de hardware ou de software. Normalmente, você mantém uma contagem de repetições e, se a mensagem for repetida mais de *n* vezes, você a exclui. Isso protege contra uma mensagem que dispara um erro do aplicativo sempre que for processada.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

## <a name="de-queue-the-next-message"></a>Remover a próxima mensagem da fila

Seu código remove uma mensagem de um fila em duas etapas. Ao chamar [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet), você recebe a próxima mensagem em uma fila. Uma mensagem retornada de **GetMessage** torna-se invisível para todas as outras mensagens de leitura de código da fila. Por padrão, essa mensagem permanece invisível por 30 segundos. Para terminar de remover a mensagem da fila, você também deve chamar [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Este processo de duas etapas de remover uma mensagem garante que quando o código não processa uma mensagem devido à falhas de hardware ou de software, outra instância do seu código pode receber a mesma mensagem e tentar novamente. Seu código chama **DeleteMessage** logo depois que a mensagem é processada.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Usar padrão Async-Await com APIs comuns de armazenamento da fila

Este exemplo mostra como usar o padrão Async-Await com APIs de armazenamento da fila comuns. O exemplo chama a versão assíncrona de cada um dos métodos determinados, conforme indicado pelo sufixo *Async* de cada método. Quando um método assíncrono é usado, o padrão async-await suspende a execução local até que a chamada seja concluída. Esse comportamento permite que o thread atual faça outro trabalho que ajude a evitar gargalos de desempenho e melhora a capacidade de resposta geral do aplicativo. Para obter mais detalhes sobre como usar o padrão Async-Await no .NET, confira [Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Aproveitar opções adicionais para remover mensagens da fila

Há duas maneiras de personalizar a recuperação da mensagem de uma fila. Primeiro, você pode obter um lote de mensagens (até 32). Segundo, você pode definir um tempo limite de invisibilidade mais longo ou mais curto, permitindo mais ou menos tempo para seu código processar totalmente cada mensagem. O exemplo de código a seguir usa o método [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) para receber 20 mensagens em uma chamada. Em seguida, ele processa cada mensagem usando um loop **foreach** . Ele também define o tempo limite de invisibilidade de cinco minutos para cada mensagem. Observe que os 5 minutos começam para todas as mensagens ao mesmo tempo; portanto, depois de 5 minutos desde a chamada para **GetMessages**, todas as mensagens que não tenham sido excluídas se tornarão visíveis novamente.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

## <a name="get-the-queue-length"></a>Obter o tamanho da fila

Você pode obter uma estimativa do número de mensagens em uma fila. O método [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) solicita que o serviço Fila recupere os atributos da fila, incluindo a contagem de mensagens. A propriedade [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) retorna o último valor recuperado pelo método **FetchAttributes**, sem chamar o serviço Fila.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Excluir uma fila

Para excluir uma fila e todas as mensagens que ela contém, chame o método [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) no objeto fila.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu os conceitos básicos do armazenamento de Fila, siga estes links para saber mais sobre tarefas de armazenamento mais complexas.

* Consulte a documentação de referência do serviço Fila para obter detalhes completos sobre as APIs disponíveis:
  * [Referência à Biblioteca de Cliente de Armazenamento para .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [Referência da API REST](https://msdn.microsoft.com/library/azure/dd179355)
* Saiba como simplificar o código que você escreve para trabalhar com o Armazenamento do Azure usando o [SDK do Azure WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Consulte outros guias de recursos para obter informações sobre opções adicionais para armazenar dados no Azure.
  * [Introdução ao armazenamento de Tabelas do Azure usando o .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) para armazenar dados estruturados.
  * [Introdução ao armazenamento de Blobs do Azure usando o .NET](../blobs/storage-dotnet-how-to-use-blobs.md) para armazenar dados não estruturados.
  * [Conectar-se ao Banco de Dados SQL usando .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md) para armazenar dados relacionais.

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
