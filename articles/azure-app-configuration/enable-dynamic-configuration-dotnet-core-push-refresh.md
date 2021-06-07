---
title: 'Tutorial: Usar configuração dinâmica usando a atualização por push em um aplicativo .NET Core'
titleSuffix: Azure App Configuration
description: Neste tutorial, você aprenderá a atualizar dinamicamente os dados de configuração de aplicativos .NET Core usando a atualização por push
services: azure-app-configuration
documentationcenter: ''
author: GrantMeStrength
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/25/2020
ms.author: jken
ms.openlocfilehash: f330e69d37ac36e2a374e81fbffc6235c86feedd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625712"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Tutorial: Usar configuração dinâmica usando a atualização por push em um aplicativo .NET Core

A biblioteca de clientes do .NET Core de Configuração de Aplicativos é compatível com a atualização de configuração sob demanda sem fazer o aplicativo reiniciar. Um aplicativo pode ser configurado para detectar alterações na Configuração de Aplicativos usando uma das abordagens a seguir ou ambas.

1. Modelo de sondagem: este é o comportamento padrão que usa a sondagem para detectar alterações na configuração. Após o valor em cache de uma configuração expirar, a próxima chamada para `TryRefreshAsync` ou `RefreshAsync` enviará uma solicitação ao servidor para verificar se a configuração foi alterada e efetuará pull da configuração atualizada, se necessário.

1. Modo de push: usa [eventos da Configuração de Aplicativos](./concept-app-configuration-event.md) para detectar alterações na configuração. Quando a Configuração de Aplicativos é configurada para enviar eventos de alteração do valor da chave para a Grade de Eventos do Azure, o aplicativo pode usar esses eventos para otimizar o número total de solicitações necessárias para manter a configuração atualizada. Os aplicativos podem optar por assiná-los diretamente na Grade de Eventos ou por meio de um dos [manipuladores de eventos com suporte](../event-grid/event-handlers.md), como um webhook, uma função do Azure ou um tópico do Barramento de Serviço.

Os aplicativos podem optar por assinar esses eventos diretamente da Grade de Eventos ou por meio de um webhook ou encaminhando eventos para o Barramento de Serviço do Azure. O SDK do Barramento de Serviço do Azure fornece uma API para registrar um manipulador de mensagens que simplifica esse processo para aplicativos que não têm um ponto de extremidade HTTP ou que não desejam sondar a Grade de Eventos em busca de alterações continuamente.

Este tutorial mostra como você pode implementar atualizações de configuração dinâmica no código usando a atualização por push. Ele se baseia no aplicativo introduzido nos inícios rápidos. Antes de continuar, primeiro conclua [Criar um aplicativo .NET Core com a Configuração de Aplicativos](./quickstart-dotnet-core-app.md).

Você pode usar qualquer editor de código para executar as etapas deste tutorial. [Visual Studio Code](https://code.visualstudio.com/) é uma opção excelente que está disponível nas plataformas Windows, macOS e Linux.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar uma assinatura para enviar eventos de alteração de configuração da Configuração de Aplicativos para um tópico do Barramento de Serviço
> * Configure seu aplicativo .NET Core para atualizar a configuração em resposta a alterações na Configuração de Aplicativos.
> * Consuma a configuração mais recente em seu aplicativo.

## <a name="prerequisites"></a>Pré-requisitos

Para realizar este tutorial, instale o [SDK do .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Configurar o tópico e a assinatura do Barramento de Serviço do Azure

Este tutorial usa a integração do Barramento de Serviço para a Grade de Eventos para simplificar a detecção de alterações de configuração para aplicativos que não desejam sondar a Configuração de Aplicativos em busca de alterações continuamente. O SDK do Barramento de Serviço do Azure fornece uma API para registrar um manipulador de mensagens que pode ser usado para atualizar a configuração quando alterações são detectadas na Configuração de Aplicativos. Siga as etapas no [Início Rápido: Usar o portal do Azure para criar um tópico e uma assinatura do Barramento de Serviço](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) para criar um namespace, um tópico e uma assinatura do Barramento de Serviço.

Depois que os recursos forem criados, adicione as variáveis de ambiente a seguir. Elas serão usadas para registrar um manipulador de eventos para alterações de configuração no código do aplicativo.

| Chave | Valor |
|---|---|
| ServiceBusConnectionString | Cadeia de conexão para o namespace do Barramento de Serviço |
| ServiceBusTopic | Nome do tópico do Barramento de Serviço |
| ServiceBusSubscription | Nome da assinatura do Barramento de Serviço |

## <a name="set-up-event-subscription"></a>Configurar uma assinatura do Evento

1. Abra o recurso da Configuração de Aplicativos no portal do Azure e clique em `+ Event Subscription` no painel `Events`.

    ![Eventos da Configuração de Aplicativos](./media/events-pane.png)

1. Insira um nome para o `Event Subscription` e o `System Topic`.

    ![Criar assinatura de evento](./media/create-event-subscription.png)

1. Selecione o `Endpoint Type` como `Service Bus Topic`, escolha o tópico do Barramento de Serviço e clique em `Confirm Selection`.

    ![Ponto de extremidade do Barramento de Serviço da assinatura de evento](./media/event-subscription-servicebus-endpoint.png)

1. Clique em `Create` para criar a assinatura de evento.

1. Clique em `Event Subscriptions` no painel `Events` para validar se a assinatura foi criada com êxito.

    ![Assinaturas de evento da Configuração de Aplicativos](./media/event-subscription-view.png)

> [!NOTE]
> Ao assinar alterações de configuração, um ou mais filtros podem ser usados para reduzir o número de eventos enviados ao aplicativo. Eles podem ser configurados como [Filtros de assinatura da Grade de Eventos](../event-grid/event-filtering.md) ou [Filtros de assinatura do Barramento de Serviço](../service-bus-messaging/topic-filters.md). Por exemplo, um filtro de assinatura pode ser usado para assinar somente eventos de alterações em uma chave que começa com uma cadeia de caracteres específica.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Registrar manipulador de eventos para recarregar dados da Configuração de Aplicativos

Abra *Program.cs* e atualize o arquivo com o código a seguir.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

O método [SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) é usado para definir o valor armazenado em cache para valores de chave registrados para atualização como sujo. Isso garante que a próxima chamada para `RefreshAsync` ou `TryRefreshAsync` revalide os valores armazenados em cache com a Configuração de Aplicativos e os atualize, se necessário.

Um atraso aleatório é adicionado antes que o valor em cache seja marcado como sujo para reduzir a limitação em potencial no caso de várias instâncias serem atualizadas ao mesmo tempo. O atraso máximo padrão antes do valor em cache ser marcado como sujo é de 30 segundos, mas pode ser substituído passando um parâmetro `TimeSpan` opcional para o método `SetDirty`.

> [!NOTE]
> Para reduzir o número de solicitações para a Configuração de Aplicativos ao usar a atualização por push, é importante chamar `SetCacheExpiration(TimeSpan cacheExpiration)` com um valor apropriado do parâmetro `cacheExpiration`. Ele controla o tempo de expiração de cache para a atualização por pull e pode ser usado como uma rede de segurança caso haja um problema com a assinatura do Evento ou com a assinatura do Barramento de Serviço. O valor recomendado é `TimeSpan.FromDays(30)`.

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Defina uma variável de ambiente chamada **AppConfigurationConnectionString** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. Se você usar o prompt de comando do Windows, execute o comando a seguir e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Execute o seguinte comando para compilar o aplicativo de console:

    ```console
     dotnet build
    ```

1. Depois que a construção for concluída com êxito, execute o seguinte comando para executar o aplicativo localmente:

    ```console
     dotnet run
    ```

    ![Execução da atualização por push antes da atualização](./media/dotnet-core-app-pushrefresh-initial.png)

1. Entre no [portal do Azure](https://portal.azure.com). Escolha **Todos os recursos** e escolha a instância do repositório de Configuração de Aplicativos que você criou no início rápido.

1. Selecione **Gerenciador de Configurações** e atualize os valores das seguintes chaves:

    | Chave | Valor |
    |---|---|
    | TestApp:Settings:Message | Dados da Configuração de Aplicativos do Azure – Atualizados |

1. Aguarde 30 segundos para permitir que o evento seja processado e a configuração seja atualizada.

    ![Execução da atualização por push após a atualização](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou seu aplicativo .NET Core para atualizar dinamicamente as configurações da Configuração de Aplicativos. Para saber como usar uma identidade gerenciada pelo Azure para simplificar o acesso à Configuração de Aplicativos, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)