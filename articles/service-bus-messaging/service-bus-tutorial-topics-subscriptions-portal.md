---
title: Atualizar estoque usando o portal do Azure e tópicos/assinaturas
description: Neste tutorial, você aprenderá como enviar e receber mensagens de um tópico e uma assinatura e como adicionar e usar regras de filtro usando .NET
author: spelluru
ms.author: spelluru
ms.date: 10/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d0a94f346f9d3cf7a05a1ca6e1b37d4d008f3e75
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179656"
---
# <a name="tutorial-update-inventory-using-azure-portal-and-topicssubscriptions"></a>Tutorial: Atualizar estoque usando o portal do Azure e tópicos/assinaturas

O Barramento de Serviço do Microsoft Azure é um serviço de mensagens na nuvem multilocatário que envia informações entre aplicativos e serviços. Operações assíncronas oferecem um sistema de mensagens agenciado e flexível, juntamente com recursos de mensagens PEPS (primeiro a entrar, primeiro a sair) e de publicação/assinatura. Este tutorial mostra como usar tópicos e assinaturas do Barramento de Serviço em um cenário de estoque de varejo com canais de publicação/assinatura usando o portal do Azure e .NET.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando o portal do Azure
> * Adicionar filtros de tópicos usando código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Um exemplo desse cenário é uma atualização de sortimento de estoque para várias lojas de varejo. Nesse cenário, cada loja ou conjunto de lojas recebe mensagens que são destinadas para atualizar os respectivos sortimentos. Este tutorial mostra como implementar esse cenário usando assinaturas e filtros. Primeiro, você cria um tópico com 3 assinaturas, adiciona algumas regras e filtros e envia e recebe mensagens do tópico e das assinaturas.

![topic](./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png)

Se você não tiver uma assinatura do Azure, poderá criar uma [conta gratuita][] antes de começar.

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial, verifique se você tem o seguinte instalado:

- [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](https://www.visualstudio.com/vs) ou posterior.
- [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="service-bus-topics-and-subscriptions"></a>Tópicos e assinaturas do Barramento de Serviço

Cada [assinatura a um tópico](service-bus-messaging-overview.md#topics) pode receber uma cópia de cada mensagem. Os tópicos tem total compatibilidade de protocolo e semântica com filas do Barramento de Serviço. Os tópicos do Barramento de Serviço dão suporte a uma ampla matriz de regras de seleção com condições de filtro, com ações opcionais que definem ou modificam as propriedades da mensagem. Cada vez que uma regra corresponde, é produzida uma mensagem. Para saber mais sobre regras, filtros e ações, consulte este [link](topic-filters.md).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]



## <a name="create-filter-rules-on-subscriptions"></a>Criar regras de filtro nas assinaturas

Depois que o namespace e tópicos/assinaturas forem provisionados e você tiver as credenciais necessárias, você está pronto para criar regras de filtro nas assinaturas e depois enviar e receber mensagens. É possível examinar o código [nesta pasta de exemplo do GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/TopicFilters).

## <a name="send-and-receive-messages"></a>Enviar e receber mensagens

Para executar o código, faça o seguinte:

1. Em um prompt de comando ou no prompt do PowerShell, clone o [repositório GitHub do Barramento de Serviço](https://github.com/Azure/azure-service-bus/), emitindo o comando a seguir:

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Navegue até a pasta de exemplo `azure-service-bus\samples\DotNet\Azure.Messaging.ServiceBus\BasicSendReceiveTutorialwithFilters`.

3. Obtenha a cadeia de conexão copiada para o Bloco de notas na seção Obter as credenciais de gerenciamento deste tutorial. Também será necessário o nome do tópico que você criou na seção anterior.

4. No prompt de comando, digite o comando a seguir:

   ```shell
   dotnet build
   ```

5. Navegue até a pasta `BasicSendReceiveTutorialwithFilters\bin\Debug\netcoreapp3.1`.

6. Digite o comando a seguir para executar o programa. Certifique-se de substituir `myConnectionString` pelo valor obtido anteriormente e `myTopicName` pelo nome do tópico que você criou:

   ```shell
   dotnet BasicSendReceiveTutorialwithFilters.dll -ConnectionString "myConnectionString" -TopicName "myTopicName"
   ``` 
7. Siga as instruções no console para selecionar a criação do filtro primeiro. Parte da criação do filtro é remover os filtros padrão. Ao utilizar o PowerShell ou a CLI não será necessário remover o filtro padrão mas, se fizer isso no código, deverá removê-los. Os comandos dos consoles 1 e 3 ajudam a gerenciar os filtros nas assinaturas criadas anteriormente:

   - Execute 1: para remover os filtros padrão.
   - Execute 2: para adicionar seus próprios filtros.
   - Execute 3: para, opcionalmente, remover seus próprios filtros. Observe que isso não irá recriar os filtros padrão.

     ![Mostrar a saída de 2](./media/service-bus-tutorial-topics-subscriptions-portal/create-rules.png)

8. Após a criação do filtro, será possível enviar mensagens. Pressione 4 e observe 10 mensagens sendo enviadas para o tópico:

    ![Enviar saída](./media/service-bus-tutorial-topics-subscriptions-portal/send-output.png)

9. Pressione 5 e observe as mensagens sendo recebidas. Se você não recebeu 10 mensagens de volta, pressione "m" para exibir o menu e pressione 5 novamente.

    ![Receber saída](./media/service-bus-tutorial-topics-subscriptions-portal/receive-output.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o namespace e o tópico. Para fazer isso, selecione esses recursos no portal e clique em **Excluir**.

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

Esta seção contém mais detalhes sobre o que o código de exemplo faz.

### <a name="get-connection-string-and-topic"></a>Obter cadeia de conexão e tópico

Primeiro, o código declara um conjunto de variáveis, que conduz a execução restante do programa.

```csharp
string ServiceBusConnectionString;
string TopicName;

static string[] Subscriptions = { "S1", "S2", "S3" };
static IDictionary<string, string[]> SubscriptionFilters = new Dictionary<string, string[]> {
    { "S1", new[] { "StoreId IN('Store1', 'Store2', 'Store3')", "StoreId = 'Store4'"} },
    { "S2", new[] { "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'" } },
    { "S3", new[] { "sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')" } }
};
// You can have only have one action per rule and this sample code supports only one action for the first filter, which is used to create the first rule. 
static IDictionary<string, string> SubscriptionAction = new Dictionary<string, string> {
    { "S1", "" },
    { "S2", "" },
    { "S3", "SET sys.Label = 'SalesEvent'"  }
};
static string[] Store = { "Store1", "Store2", "Store3", "Store4", "Store5", "Store6", "Store7", "Store8", "Store9", "Store10" };
static string SysField = "sys.To";
static string CustomField = "StoreId";
static int NrOfMessagesPerStore = 1; // Send at least 1.
```

A cadeia de conexão e o nome do tópico são passados por meio dos parâmetros da linha de comando, conforme mostrado e, em seguida, são lidos no método `Main()`:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string TopicName = "";

    for (int i = 0; i < args.Length; i++)
    {
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i + 1]}");
            ServiceBusConnectionString = args[i + 1]; // Alternatively enter your connection string here.
        }
        else if (args[i] == "-TopicName")
        {
            Console.WriteLine($"TopicName: {args[i + 1]}");
            TopicName = args[i + 1]; // Alternatively enter your queue name here.
        }
    }

    if (ServiceBusConnectionString != "" && TopicName != "")
    {
        Program P = StartProgram(ServiceBusConnectionString, TopicName);
        P.PresentMenu().GetAwaiter().GetResult();
    }
    else
    {
        Console.WriteLine("Specify -Connectionstring and -TopicName to execute the example.");
        Console.ReadKey();
    }
}
```

### <a name="remove-default-filters"></a>Remover filtros padrão

Quando você cria uma assinatura, o Barramento de Serviço cria um filtro padrão por assinatura. Este filtro permite receber todas as mensagens enviadas para o tópico. Se você quiser usar filtros personalizados, poderá remover o filtro padrão, conforme mostrado no código a seguir:

```csharp
private async Task RemoveDefaultFilters()
{
    Console.WriteLine($"Starting to remove default filters.");

    try
    {
        var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
        foreach (var subscription in Subscriptions)
        {
            await client.DeleteRuleAsync(TopicName, subscription, CreateRuleOptions.DefaultRuleName);
            Console.WriteLine($"Default filter for {subscription} has been removed.");
        }

        Console.WriteLine("All default Rules have been removed.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="create-filters"></a>Criar filtros

O código a seguir inclui os filtros personalizados definidos neste tutorial:

```csharp
private async Task CreateCustomFilters()
{
    try
    {
        for (int i = 0; i < Subscriptions.Length; i++)
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            string[] filters = SubscriptionFilters[Subscriptions[i]];
            if (filters[0] != "")
            {
                int count = 0;
                foreach (var myFilter in filters)
                {
                    count++;

                    string action = SubscriptionAction[Subscriptions[i]];
                    if (action != "")
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Action = new SqlRuleAction(action),
                            Name = $"MyRule{count}"
                        });
                    }
                    else
                    {
                        await client.CreateRuleAsync(TopicName, Subscriptions[i], new CreateRuleOptions
                        {
                            Filter = new SqlRuleFilter(myFilter),
                            Name = $"MyRule{count}"
                        });
                    }
                }
            }

            Console.WriteLine($"Filters and actions for {Subscriptions[i]} have been created.");
        }

        Console.WriteLine("All filters and actions have been created.\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }

    await PresentMenu();
}
```

### <a name="remove-your-custom-created-filters"></a>Remover os filtros personalizados criados

Se você quiser remover todos os filtros da assinatura, o código a seguir mostra como fazer isso:

```csharp
private async Task CleanUpCustomFilters()
{
    foreach (var subscription in Subscriptions)
    {
        try
        {
            var client = new ServiceBusAdministrationClient(ServiceBusConnectionString);
            IAsyncEnumerator<RuleProperties> rules = client.GetRulesAsync(TopicName, subscription).GetAsyncEnumerator();
            while (await rules.MoveNextAsync())
            {
                await client.DeleteRuleAsync(TopicName, subscription, rules.Current.Name);
                Console.WriteLine($"Rule {rules.Current.Name} has been removed.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
    Console.WriteLine("All default filters have been removed.\n");

    await PresentMenu();
}
```

### <a name="send-messages"></a>Enviar mensagens

Enviar mensagens para um tópico é semelhante ao envio de mensagens para uma fila. Este exemplo mostra como enviar mensagens usando uma lista de tarefas e um processamento assíncrono:

```csharp
public async Task SendMessages()
{
    try
    {
        await using var client = new ServiceBusClient(ServiceBusConnectionString);
        var taskList = new List<Task>();
        for (int i = 0; i < Store.Length; i++)
        {
            taskList.Add(SendItems(client, Store[i]));
        }

        await Task.WhenAll(taskList);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.ToString());
    }
    Console.WriteLine("\nAll messages sent.\n");
}

private async Task SendItems(ServiceBusClient client, string store)
{
    // create the sender
    ServiceBusSender tc = client.CreateSender(TopicName);

    for (int i = 0; i < NrOfMessagesPerStore; i++)
    {
        Random r = new Random();
        Item item = new Item(r.Next(5), r.Next(5), r.Next(5));

        // Note the extension class which is serializing an deserializing messages
        ServiceBusMessage message = item.AsMessage();
        message.To = store;
        message.ApplicationProperties.Add("StoreId", store);
        message.ApplicationProperties.Add("Price", item.GetPrice().ToString());
        message.ApplicationProperties.Add("Color", item.GetColor());
        message.ApplicationProperties.Add("Category", item.GetItemCategory());

        await tc.SendMessageAsync(message);
        Console.WriteLine($"Sent item to Store {store}. Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}"); ;
    }
}
```

### <a name="receive-messages"></a>Receber mensagens

As mensagens são recebidas novamente por meio de uma lista de tarefas e o código usa envio em lote. Você pode enviar e receber usando envio em lote, mas este exemplo mostra apenas como receber em lote. Na realidade, você não iria interromper o loop, mas, continuaria em loop e definiria um período de tempo maior, como um minuto. A chamada de recebimento para o agente é mantida aberta durante esse período e, se as mensagens chegarem, serão retornadas imediatamente e uma nova chamada de recebimento será emitida. Esse conceito é chamado de *sondagem longa*. Usar a bomba de recebimento que você pode ver no [início rápido](service-bus-quickstart-portal.md), e em vários outros exemplos no repositório, é uma opção mais típica.

```csharp
public async Task Receive()
{
    var taskList = new List<Task>();
    for (var i = 0; i < Subscriptions.Length; i++)
    {
        taskList.Add(this.ReceiveMessages(Subscriptions[i]));
    }

    await Task.WhenAll(taskList);
}

private async Task ReceiveMessages(string subscription)
{
    await using var client = new ServiceBusClient(ServiceBusConnectionString);
    ServiceBusReceiver receiver = client.CreateReceiver(TopicName, subscription);

    // In reality you would not break out of the loop like in this example but would keep looping. The receiver keeps the connection open
    // to the broker for the specified amount of seconds and the broker returns messages as soon as they arrive. The client then initiates
    // a new connection. So in reality you would not want to break out of the loop. 
    // Also note that the code shows how to batch receive, which you would do for performance reasons. For convenience you can also always
    // use the regular receive pump which we show in our Quick Start and in other github samples.
    while (true)
    {
        try
        {
            //IList<Message> messages = await receiver.ReceiveAsync(10, TimeSpan.FromSeconds(2));
            // Note the extension class which is serializing an deserializing messages and testing messages is null or 0.
            // If you think you did not receive all messages, just press M and receive again via the menu.
            IReadOnlyList<ServiceBusReceivedMessage> messages = await receiver.ReceiveMessagesAsync(maxMessages: 100);

            if (messages.Any())
            {
                foreach (ServiceBusReceivedMessage message in messages)
                {
                    lock (Console.Out)
                    {
                        Item item = message.As<Item>();
                        IReadOnlyDictionary<string, object> myApplicationProperties = message.ApplicationProperties;
                        Console.WriteLine($"StoreId={myApplicationProperties["StoreId"]}");
                        if (message.Subject != null)
                        {
                            Console.WriteLine($"Subject={message.Subject}");
                        }
                        Console.WriteLine(
                            $"Item data: Price={item.GetPrice()}, Color={item.GetColor()}, Category={item.GetItemCategory()}");
                    }

                    await receiver.CompleteMessageAsync(message);
                }
            }
            else
            {
                break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.ToString());
        }
    }
}
```

> [!NOTE]
> É possível gerenciar os recursos do Barramento de Serviço com o [Gerenciador de Barramento de Serviço](https://github.com/paolosalvatori/ServiceBusExplorer/). O Gerenciador de Barramento de Serviço permite que usuários se conectem a um namespace de serviço do Barramento de Serviço e administrem entidades de mensagens de uma maneira fácil. A ferramenta fornece recursos avançados, como a funcionalidade de importação/exportação ou a capacidade de testar tópicos, filas, assinaturas, serviços de retransmissão, hubs de notificação e hubs de eventos. 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você provisionou recursos usando o portal do Azure e, em seguida, enviou e recebeu mensagens de um tópico do Barramento de Serviço e as respectivas assinaturas. Você aprendeu a:

> [!div class="checklist"]
> * Criar um tópico de Barramento de Serviço e uma ou mais assinaturas para esse tópico usando o portal do Azure
> * Adicionar filtros de tópicos usando código .NET
> * Criar duas mensagens com conteúdo diferente
> * Enviar as mensagens e verificar se chegaram nas assinaturas esperadas
> * Receber mensagens das assinaturas

Para obter mais exemplos de envio e recebimento de mensagens, comece com os [Exemplos de Barramento de Serviço no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Avance para o próximo tutorial para saber mais sobre como usar os recursos de publicação/assinatura do Barramento de Serviço.

> [!div class="nextstepaction"]
> [Responder a eventos por meio da Grade de Eventos](service-bus-to-event-grid-integration-example.md)

[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[connection-string]: ./media/service-bus-tutorial-topics-subscriptions-portal/connection-string.png
[service-bus-flow]: ./media/service-bus-tutorial-topics-subscriptions-portal/about-service-bus-topic.png
