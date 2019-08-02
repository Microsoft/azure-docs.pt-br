---
title: Introdução ao armazenamento de filas e aos serviços conectados do Visual Studio (projetos de Trabalho Web) | Microsoft Docs
description: Como começar a usar o armazenamento de fila do Azure em um projeto WebJob depois de se conectar a uma conta de armazenamento usando os serviços conectados do Visual Studio.
services: storage
author: ghogen
manager: douge
ms.assetid: 5c3ef267-2a67-44e9-ab4a-1edd7015034f
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: article
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: 44206f1826fc25407d9dec3f832b70881091e187
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248971"
---
# <a name="getting-started-with-azure-queue-storage-and-visual-studio-connected-services-webjob-projects"></a>Introdução ao Armazenamento de Fila do Azure e aos Serviços Conectados do Visual Studio (Projetos WebJob)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral
Este artigo descreve como começar a usar o armazenamento de filas do Azure em um projeto de Trabalho Web do Azure Visual Studio depois de ter criado ou referenciado uma conta de armazenamento do Azure usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio. Quando você adiciona uma conta de armazenamento a um projeto de WebJob usando a caixa de diálogo **Adicionar Serviços Conectados** do Visual Studio, os pacotes NuGet do Armazenamento do Azure apropriados são instalados, as referências apropriadas .NET são adicionadas ao projeto e cadeias de conexão para a conta de armazenamento são atualizadas no arquivo App.config.  

Este artigo fornece exemplos de código em C# que mostram como usar o SDK do Azure WebJobs versão 1.x com o serviço de armazenamento de Fila do Azure.

O armazenamento de filas do Azure é um serviço para armazenamento de um grande número de mensagens que podem ser acessadas de qualquer lugar do mundo por meio de chamadas autenticadas usando HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter milhões de mensagens, até o limite de capacidade total de uma conta de armazenamento. Consulte a [Introdução ao Armazenamento de Filas do Azure usando o .NET](../storage/queues/storage-dotnet-how-to-use-queues.md) para obter mais informações. Para saber mais sobre ASP.NET, confira [ASP.NET](https://www.asp.net).

## <a name="how-to-trigger-a-function-when-a-queue-message-is-received"></a>Como disparar uma função quando é recebida uma mensagem da fila
Para gravar uma função que o SDK de Trabalhos Web chama quando uma mensagem da fila é recebida, use o atributo **QueueTrigger** . O construtor de atributo tem um parâmetro de cadeia que especifica o nome da fila para sondagem. Para saber como definir o nome da fila dinamicamente, confira [Como definir as Opções de Configuração](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens da fila da cadeia
No exemplo a seguir, a fila contém uma mensagem de cadeia de caracteres, portanto **QueueTrigger** é aplicado a um parâmetro de cadeia de caracteres chamado **logMessage**, que inclui o conteúdo da mensagem da fila. A função [grava uma mensagem de log no painel](#how-to-write-logs).

```csharp
public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    logger.WriteLine(logMessage);
}
```

Além da **cadeia de caracteres**, o parâmetro pode ser uma matriz de bytes, um objeto **CloudQueueMessage** ou um POCO definido por você.

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Mensagens de fila POCO [(Objeto Plain Old CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
No exemplo a seguir, a mensagem da fila contém o JSON para um objeto **BlobInformation**, que inclui uma propriedade **BlobName**. O SDK automaticamente desserializa o objeto.

```csharp
public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
{
    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
}
```

O SDK usa o [pacote NuGet Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar as mensagens. Se criar mensagens de fila em um programa que não usa o SDK de Trabalhos Web, você poderá escrever código como o exemplo a seguir para criar uma mensagem de fila POCO que o SDK possa analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

### <a name="async-functions"></a>Funções assíncronas
A seguinte função assíncrona [grava um log no painel](#how-to-write-logs).

```csharp
public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
{
    await logger.WriteLineAsync(logMessage);
}
```

As funções assíncronas podem levar um [token de cancelamento](https://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), conforme mostrado no exemplo a seguir, que copia um blob. (Para obter uma explicação do espaço reservado **queueTrigger** , consulte a seção [Blobs](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) ).

```csharp
public async static Task ProcessQueueMessageAsyncCancellationToken(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
    CancellationToken token)
{
    await blobInput.CopyToAsync(blobOutput, 4096, token);
}
```

## <a name="types-the-queuetrigger-attribute-works-with"></a>Tipos com os quais o atributo QueueTrigger funciona
Você pode usar **QueueTrigger** com os seguintes tipos:

* **string**
* Um tipo POCO serializado como JSON
* **byte[]**
* **CloudQueueMessage**

## <a name="polling-algorithm"></a>Algoritmo de sondagem
O SDK implementa um algoritmo exponencial aleatório de retirada para reduzir o efeito de sondagem de fila ociosa nos custos das transações de armazenamento.  Quando uma mensagem for encontrada, o SDK aguarda dois segundos e, em seguida, verifica outra mensagem; quando nenhuma mensagem for encontrada, ele aguarda cerca de quatro segundos antes de tentar novamente. Após subsequentes tentativas falhas para obter uma mensagem da fila, o tempo de espera continua a aumentar até atingir o tempo de espera máximo, cujo padrão é um minuto. [O tempo de espera máximo é configurável](#how-to-set-configuration-options).

## <a name="multiple-instances"></a>Várias instâncias
Se o seu aplicativo Web for executado em várias instâncias, um Trabalho Web contínuo será executado em todos os computadores, e cada computador aguardará os gatilhos e tentará executar as funções. Em alguns cenários, isso pode fazer com que algumas funções processem os mesmos dados duas vezes. Assim, as funções devem ser idempotentes (escritas de forma que chamá-las repetidamente com os mesmos dados de entrada não produza resultados duplicados).  

## <a name="parallel-execution"></a>Execução paralela
Se você tiver várias funções escutando em filas diferentes, o SDK as chamará em paralelo quando as mensagens forem recebidas simultaneamente.

O mesmo é verdadeiro quando várias mensagens são recebidas para uma única fila. Por padrão, o SDK obtém um lote de 16 mensagens de fila por vez e executa a função que as processa em paralelo. [O tamanho do lote é configurável](#how-to-set-configuration-options). Quando o número que está sendo processado chega até a metade do tamanho do lote, o SDK obtém outro lote e começa a processar as mensagens. Portanto, o número máximo de mensagens simultâneas que estão sendo processadas por função é uma vez e meia o tamanho do lote. Esse limite se aplica separadamente a cada função que tem um atributo **QueueTrigger** . Se você não quiser uma execução paralela para mensagens recebidas em uma fila, defina o tamanho do lote como 1.

## <a name="get-queue-or-queue-message-metadata"></a>Obter fila ou metadados de mensagem da fila
Você pode obter as propriedades da mensagem a seguir adicionando parâmetros à assinatura do método:

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **string** queueTrigger (contém o texto da mensagem)
* **string** id
* **string** popReceipt
* **int** dequeueCount

Se você quiser trabalhar diretamente com a API de armazenamento do Azure, também é possível adicionar um parâmetro **CloudStorageAccount** .

O exemplo a seguir grava todos os metadados em um log de aplicativo de informações. No exemplo, tanto logMessage quanto queueTrigger contém o conteúdo da mensagem da fila.

```csharp
public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
    DateTimeOffset expirationTime,
    DateTimeOffset insertionTime,
    DateTimeOffset nextVisibleTime,
    string id,
    string popReceipt,
    int dequeueCount,
    string queueTrigger,
    CloudStorageAccount cloudStorageAccount,
    TextWriter logger)
{
    logger.WriteLine(
        "logMessage={0}\n" +
        "expirationTime={1}\ninsertionTime={2}\n" +
        "nextVisibleTime={3}\n" +
        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
        "queue endpoint={7} queueTrigger={8}",
        logMessage, expirationTime,
        insertionTime,
        nextVisibleTime, id,
        popReceipt, dequeueCount,
        cloudStorageAccount.QueueEndpoint,
        queueTrigger);
}
```

Este é um log de exemplo gravado pelo código de exemplo:

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## <a name="graceful-shutdown"></a>Desligamento normal
Uma função que é executada em um Trabalho Web contínuo pode aceitar um parâmetro **CancellationToken** que permite ao sistema operacional notificar a função quando o Trabalho Web está prestes a ser encerrado. Você pode usar essa notificação para certificar-se de que a função não finalize inesperadamente de uma maneira que os dados fiquem em um estado inconsistente.

O exemplo a seguir mostra como verificar se há eminência de encerramento do trabalho Web em uma função.

```csharp
public static void GracefulShutdownDemo(
            [QueueTrigger("inputqueue")] string inputText,
            TextWriter logger,
            CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(1000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

**Observação:** O painel pode não mostrar corretamente o status e a saída das funções que foram desligadas.

Para obter mais informações, consulte [Desligamento normal dos trabalhos Web](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a name="how-to-create-a-queue-message-while-processing-a-queue-message"></a>Como criar uma mensagem da fila durante o processamento de uma mensagem da fila
Para gravar uma função que cria uma nova mensagem da fila, use o atributo **Queue** . Como **QueueTrigger**, você passa o nome da fila como uma cadeia de caracteres ou pode [definir o nome da fila dinamicamente](#how-to-set-configuration-options).

### <a name="string-queue-messages"></a>Mensagens da fila da cadeia
O exemplo de código não síncrono a seguir cria uma nova mensagem de fila na fila denominada "outputqueue" com o mesmo conteúdo que a mensagem da fila recebida na fila denominada "inputqueue". (Para funções assíncronas, use **\<IAsyncCollector T >** conforme mostrado mais adiante nesta seção.)

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] out string outputQueueMessage )
{
    outputQueueMessage = queueMessage;
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Mensagens de fila POCO [(Objeto Plain Old CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Para criar uma mensagem da fila que contenha um POCO em vez de uma cadeia de caracteres, passe o tipo POCO como um parâmetro de saída para o construtor de atributo **Queue** .

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
{
    blobInfoOutput = blobInfoInput;
}
```

O SDK serializa automaticamente o objeto em JSON. Uma mensagem da fila sempre é criada, mesmo que o objeto seja nulo.

### <a name="create-multiple-messages-or-in-async-functions"></a>Criar várias mensagens ou em funções assíncronas
Para criar várias mensagens, crie o tipo de parâmetro para a fila de saída **\<ICollector t >** ou **\<IAsyncCollector t >** , conforme mostrado no exemplo a seguir.

```csharp
public static void CreateQueueMessages(
    [QueueTrigger("inputqueue")] string queueMessage,
    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
    TextWriter logger)
{
    logger.WriteLine("Creating 2 messages in outputqueue");
    outputQueueMessage.Add(queueMessage + "1");
    outputQueueMessage.Add(queueMessage + "2");
}
```

Cada mensagem da fila é criada imediatamente quando o método **Add** é chamado.

### <a name="types-that-the-queue-attribute-works-with"></a>Tipos com os quais o atributo Queue funciona
Você pode usar o atributo **Queue** nos seguintes tipos de parâmetros:

* **out string** (criará a mensagem da fila se o valor do parâmetro não for nulo quando a função terminar)
* **out byte[]** (funciona como **string**)
* **out CloudQueueMessage** (funciona como **string**)
* **out POCO** (um tipo serializável, cria uma mensagem com um objeto nulo se o parâmetro for nulo quando a função terminar)
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** (para a criação de mensagens manualmente usando a API de Armazenamento do Azure diretamente)

### <a name="use-webjobs-sdk-attributes-in-the-body-of-a-function"></a>Usar atributos do SDK de Trabalhos Web no corpo de uma função
Se precisar realizar algum trabalho em sua função antes de usar um atributo do SDK de Trabalhos Web como **Queue** , **Blob** ou **Table** , você poderá usar a interface **IBinder** .

O exemplo a seguir usa uma mensagem da fila de entrada e cria uma nova mensagem com o mesmo conteúdo em uma fila de saída. O nome da fila de saída é definido pelo código no corpo da função.

```csharp
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
}
```

A interface **IBinder** também pode ser usada com os atributos **Table** e **Blob**.

## <a name="how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message"></a>Como ler e gravar blobs e tabelas ao processar uma mensagem da fila
Os atributos **Blob** e **Table** permitem que você leia e grave os blobs e as tabelas. Os exemplos nesta seção se aplicam a blobs. Para obter exemplos de código que mostram como disparar processos quando blobs são criados ou atualizados, consulte [Como usar o armazenamento de blobs do Azure com o SDK do WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).
<!-- , and for code samples that read and write tables, see [How to use Azure table storage with the WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-tables-how-to.md). -->

### <a name="string-queue-messages-triggering-blob-operations"></a>Mensagens da fila de cadeia de caracteres que disparam operações de blob
Para uma mensagem da fila que contém uma cadeia de caracteres, **queueTrigger** é um espaço reservado que você pode utilizar no parâmetro **blobPath** do atributo **Blob** que inclui o conteúdo da mensagem.

O exemplo a seguir utiliza objetos **Stream** para ler e gravar os blobs. A mensagem da fila é o nome de um blob localizado no contêiner de textblobs. Uma cópia de blob com "-new" acrescentado ao nome é criada no mesmo contêiner.

```csharp
public static void ProcessQueueMessage(
    [QueueTrigger("blobcopyqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O construtor do atributo **Blob** aceita um parâmetro **blobPath** que especifica o nome do blob e o contêiner. Para obter mais informações sobre esse espaço reservado, consulte [Como usar o Armazenamento de Blobs do Azure com o SDK de WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki).

Quando o atributo decora um objeto **Stream**, outro parâmetro de construtor especifica o modo **FileAccess** como leitura, gravação ou leitura/gravação.

O exemplo a seguir utiliza um objeto **CloudBlockBlob** para excluir um blob. A mensagem da fila é o nome do blob.

```csharp
public static void DeleteBlob(
    [QueueTrigger("deleteblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
{
    blobToDelete.Delete();
}
```

### <a name="poco-plain-old-clr-objecthttpsenwikipediaorgwikiplainoldclrobject-queue-messages"></a>Mensagens de fila POCO [(Objeto Plain Old CLR](https://en.wikipedia.org/wiki/Plain_Old_CLR_Object))
Para um POCO armazenado como JSON na mensagem da fila, é possível utilizar espaços reservados que nomeiam propriedades do objeto no parâmetro **blobPath** do atributo **Queue**. Também é possível utilizar os nomes de propriedade de metadados de fila como espaços reservados. Consulte [Obter a fila ou os metadados de mensagem da fila](#get-queue-or-queue-message-metadata).

O exemplo a seguir copia um blob para um novo blob com uma extensão diferente. A mensagem da fila é um objeto **BlobInformation** que inclui as propriedades **BlobName** e **BlobNameWithoutExtension**. Os nomes de propriedade são usados como espaços reservados no caminho de blob para os atributos **Blob** .

```csharp
public static void CopyBlobPOCO(
    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}
```

O SDK usa o [pacote NuGet Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) para serializar e desserializar as mensagens. Se criar mensagens de fila em um programa que não usa o SDK de Trabalhos Web, você poderá escrever código como o exemplo a seguir para criar uma mensagem de fila POCO que o SDK possa analisar.

```csharp
BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
logQueue.AddMessage(queueMessage);
```

Se você precisar fazer algum trabalho em sua função antes de associar um blob a um objeto, você poderá usar o atributo no corpo da função, conforme mostrado em [Usar o SDK do WebJobs no corpo de uma função](#use-webjobs-sdk-attributes-in-the-body-of-a-function).

### <a name="types-you-can-use-the-blob-attribute-with"></a>Tipos com os quais você pode usar o atributo Blob
O atributo **Blob** pode ser usado com os seguintes tipos:

* **Stream** (leitura ou gravação, especificadas usando o parâmetro de construtor FileAccess)
* **TextReader**
* **TextWriter**
* **string** (leitura)
* **out string** (gravação; criará um blob somente se o parâmetro de cadeia de caracteres não for nulo quando a função retornar)
* POCO (leitura)
* out POCO (gravação; sempre cria um blob; criará como objeto nulo se o parâmetro POCO for nulo quando a função retornar)
* **CloudBlobStream** (gravação)
* **ICloudBlob** (leitura ou gravação)
* **CloudBlockBlob** (leitura ou gravação)
* **CloudPageBlob** (leitura ou gravação)

## <a name="how-to-handle-poison-messages"></a>Como tratar mensagens suspeitas
As mensagens cujo conteúdo faz com que uma função falhe são chamadas de *mensagens suspeitas*. Quando a função falhar, a mensagem da fila não é excluída e eventualmente é captada novamente, fazendo com que o ciclo seja repetido. O SDK pode interromper automaticamente o ciclo após algumas iterações, ou você fazê-lo manualmente.

### <a name="automatic-poison-message-handling"></a>Manipulação automática de mensagens suspeitas
O SDK chamará uma função até 5 vezes para processar uma mensagem da fila. Se a quinta tentativa falhar, a mensagem é movida para uma fila de mensagens suspeitas. Veja como configurar o número máximo de tentativas em [Como definir opções de configuração](#how-to-set-configuration-options).

A fila de mensagens suspeita é denominada *{originalqueuename}* -suspeita. Você pode gravar uma função para processar as mensagens da fila de mensagens suspeitas registrando-as ou enviando uma notificação de que a atenção manual é necessária.

No exemplo a seguir a função **CopyBlob** falhará quando uma mensagem da fila tiver o nome de um blob que não existe. Quando isso acontece, a mensagem será movida da fila copyblobqueue para a fila copyblobqueue-poison. O **ProcessPoisonMessage**, em seguida, registra a mensagem suspeita.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
{
    blobInput.CopyTo(blobOutput, 4096);
}

public static void ProcessPoisonMessage(
    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
{
    logger.WriteLine("Failed to copy blob, name=" + blobName);
}
```

A ilustração a seguir mostra a saída do console dessas funções quando uma mensagem suspeita é processada.

![Saída do console para tratamento de mensagens suspeitas](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### <a name="manual-poison-message-handling"></a>Manipulação manual de mensagens suspeitas
É possível obter o número de vezes que uma mensagem foi selecionada para processamento, bastando adicionar um parâmetro **int** chamado **dequeueCount** à sua função. Você pode, então, verificar a contagem de remoção da fila no código de função e realizar seu próprio tratamento de mensagem suspeita quando o número exceder um limite, conforme mostrado no exemplo a seguir.

```csharp
public static void CopyBlob(
    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
    TextWriter logger)
{
    if (dequeueCount > 3)
    {
        logger.WriteLine("Failed to copy blob, name=" + blobName);
    }
    else
    {
        blobInput.CopyTo(blobOutput, 4096);
    }
}
```

## <a name="how-to-set-configuration-options"></a>Como definir as Opções de Configuração
É possível utilizar o tipo **JobHostConfiguration** para definir as seguintes opções de configuração:

* Definir as cadeias de conexão do SDK no código.
* Definir as configurações de **QueueTrigger** como contagem máxima de remoção da fila.
* Obtenha nomes de fila por meio da configuração.

### <a name="set-sdk-connection-strings-in-code"></a>Defina as cadeias de conexão do SDK no código
A definição das cadeias de conexão do SDK no código lhe permite usar seus próprios nomes de cadeia de conexão em arquivos de configuração ou variáveis de ambiente, conforme mostrado no exemplo a seguir.

```csharp
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    var _dashboardConn = ConfigurationManager
        .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    var _serviceBusConn = ConfigurationManager
        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    config.DashboardConnectionString = _dashboardConn;
    config.ServiceBusConnectionString = _serviceBusConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="configure-queuetrigger--settings"></a>Definir configurações de QueueTrigger
Você pode definir as seguintes configurações que se aplicam ao processamento de mensagem de fila:

* O número máximo de mensagens da fila que são recebidas simultaneamente a serem executadas em paralelo (o padrão é 16).
* O número máximo de tentativas antes de uma mensagem da fila ser enviada para uma fila de suspeita (o padrão é 5).
* O tempo máximo de espera antes de sondar novamente quando uma fila está vazia (o padrão é 1 minuto).

O exemplo a seguir mostra como definir essas configurações:

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="set-values-for-webjobs-sdk-constructor-parameters-in-code"></a>Definir valores para parâmetros do construtor do SDK WebJobs no código
Às vezes você deseja especificar um nome de fila, um nome de blob ou contêiner ou um nome de tabela no código em vez de embuti-lo no código. Por exemplo, você talvez queira especificar o nome da fila para **QueueTrigger** em um arquivo de configuração ou variável de ambiente.

Você pode fazer isso passando um objeto **NameResolver** para o tipo **JobHostConfiguration**. Você inclui espaços reservados especiais entre sinais de percentual (%) em parâmetros do construtor de atributos do SDK de Trabalhos Web e o código **NameResolver** especifica os valores reais a serem usados no lugar desses espaços reservados.

Por exemplo, suponha que você deseje usar uma fila denominada logqueuetest no ambiente de teste e uma denominada logqueueprod na produção. Ao invés de um nome de fila embutido em código, você deseja especificar o nome de uma entrada na coleção **appSettings** que tem o nome da fila real. Se a chave **appSettings** for logqueue, sua função pode parecer com o exemplo a seguir.

```csharp
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

A classe **NameResolver** pode, em seguida, obter o nome da fila de **appSettings**, conforme mostrado no exemplo a seguir:

```csharp
public class QueueNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

Você passa a classe **NameResolver** para o objeto **JobHost**, conforme mostrado no exemplo a seguir.

```csharp
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new QueueNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

**Observação:** Os nomes de fila, tabela e blob são resolvidos sempre que uma função é chamada, mas os nomes dos contêineres de blobs são resolvidos somente quando o aplicativo é iniciado. Você não pode alterar o nome do contêiner de blob enquanto o trabalho está em execução.

## <a name="how-to-trigger-a-function-manually"></a>Como disparar uma função manualmente
Para disparar uma função manualmente, use o método **Call** ou **CallAsync** no objeto **JobHost** e no atributo **NoAutomaticTrigger** na função, conforme mostrado no exemplo a seguir.

```csharp
public class Program
{
    static void Main(string[] args)
    {
        JobHost host = new JobHost();
        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
    }

    [NoAutomaticTrigger]
    public static void CreateQueueMessage(
        TextWriter logger,
        string value,
        [Queue("outputqueue")] out string message)
    {
        message = value;
        logger.WriteLine("Creating queue message: ", message);
    }
}
```

## <a name="how-to-write-logs"></a>Como gravar logs
O painel mostra logs em dois lugares: na página do Trabalho Web e na página de determinada invocação do Trabalho Web.

![Logs na página do Trabalho Web](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![Logs na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

A saída de métodos de console que você chama em uma função ou no método **Main()** aparece na página Painel para o trabalho Web, não na página de uma invocação de método específica. A saída do objeto TextWriter obtido de um parâmetro na assinatura do método é exibida na página Painel para uma invocação de método.

A saída do console não pode ser vinculada a uma invocação de método específica porque o console é single-threaded, embora muitas funções de trabalho possam estar sendo executadas ao mesmo tempo. É por isso que o SDK fornece a cada invocação de função seu próprio objeto de gravador de log exclusivo.

Para gravar [logs de rastreamento do aplicativo](../app-service/troubleshoot-dotnet-visual-studio.md#logsoverview), use **Console.Out** (cria logs marcados como INFO) e **Console.Error** (cria logs marcados como ERROR). Uma alternativa é usar [Rastreamento ou TraceSource](https://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), que fornece os níveis Detalhado, de Aviso e Crítico, além de Informações e Erro. Os logs de rastreamento de aplicativo aparecem nos arquivos de log de aplicativo Web, nas tabelas do Azure ou nos blobs do Azure, dependendo de como você configura seu aplicativo web do Azure. Assim como ocorre com toda a saída do console, os 100 logs de aplicativos mais recentes também são exibidos na página Painel do Trabalho Web, não na página de uma invocação de função.

A saída do console aparecerá no painel somente se o programa estiver em execução em um Trabalho Web do Azure, não se o programa estiver em execução localmente ou em outro ambiente.

É possível desabilitar o log definindo a cadeia de conexão do painel de controle como null. Para obter mais informações, consulte [Como definir as Opções de Configuração](#how-to-set-configuration-options).

O exemplo a seguir mostra várias maneiras de gravar logs:

```csharp
public static void WriteLog(
    [QueueTrigger("logqueue")] string logMessage,
    TextWriter logger)
{
    Console.WriteLine("Console.Write - " + logMessage);
    Console.Out.WriteLine("Console.Out - " + logMessage);
    Console.Error.WriteLine("Console.Error - " + logMessage);
    logger.WriteLine("TextWriter - " + logMessage);
}
```

No painel do SDK de Trabalhos Web, a saída do objeto **TextWriter** aparece quando você acessa a página de invocação de uma função específica e seleciona **Ativar/Desativar Saída**:

![Link de invocação](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![Logs na página de invocação de função](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

No painel do SDK do Web Jobs, as 100 linhas da saída do Console mais recentes são mostradas quando você vai até a página do Web Jobs (não da invocação de função) e selecione **Alternar Saída**.

![Ativar/Desativar Saída](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

Em um Trabalho Web contínuo, os logs de aplicativo são mostrados em /data/jobs/continuous/ *{nomedowebjob}* /job_log.txt no sistema de arquivos do aplicativo Web.

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Em um blob do Azure, os logs do aplicativo são semelhantes a este: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Em uma tabela do Azure, os logs de **Console.Out** e **Console.Error** têm esta aparência:

![Log de informações na tabela](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![Log de erros na tabela](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

## <a name="next-steps"></a>Próximas etapas
Este guia forneceu exemplos de código que mostram como lidar com cenários comuns para trabalhar com filas do Azure. Para obter mais informações sobre como usar o Azure WebJobs e o SDK do WebJobs, consulte [Recursos de documentação do Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

