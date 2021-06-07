---
title: Pare o servidor de aplicativos normalmente.
description: Este artigo fornece informações sobre o desligamento normal do servidor de aplicativo Signalr
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: d9dd7ce9cf321628598a7bb866c5d1b1a6fb0e1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98201664"
---
# <a name="server-graceful-shutdown"></a>Desligamento normal do servidor
Microsoft Azure serviço Signalr fornece dois modos para desligar um servidor normalmente. 

A principal vantagem de usar esse recurso é impedir que o cliente tenha quedas de conexão inesperadas. 

Em vez disso, você poderia esperar que suas conexões de cliente se fechem em relação à sua lógica de negócios ou até mesmo migrar a conexão do cliente para outro servidor sem perder dados. 

## <a name="how-it-works"></a>Como ele funciona

Em geral, haverá quatro estágios em um processo de desligamento normal:

1. **Definir o servidor offline**

    Isso significa que nenhuma conexão de cliente será roteada para esse servidor.

2. **Disparar `OnShutdown` ganchos**

    Você pode registrar ganchos de desligamento para cada Hub que você possui em seu servidor.
    Eles serão chamados em relação à ordem registrada logo após recebermos uma resposta **FINACK** do nosso serviço Signalr do Azure, o que significa que esse servidor foi definido offline no serviço de Signaler do Azure.

    Você pode difundir mensagens ou fazer alguns trabalhos de limpeza neste estágio, depois que todos os ganchos de desligamento tiverem sido executados, continuaremos para o próximo estágio.

3. **Aguarde até que todas as conexões de cliente sejam concluídas**, dependendo do modo escolhido, pode ser:

    **Modo definido como WaitForClientsToClose**

    O serviço de Signaler do Azure manterá os clientes existentes.

    Talvez você precise criar uma maneira, como transmitir uma mensagem de fechamento para todos os clientes e, em seguida, permitir que os clientes decidam quando fechar/se conectar novamente.

    Leia [callsample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample) para uso de exemplo, que transmitimos uma mensagem ' Exit ' para disparar o fechamento do cliente no gancho de desligamento.

    **Modo definido como MigrateClients**

    O serviço de sinalizador do Azure tentará redirecionar a conexão do cliente neste servidor para outro servidor válido. 
    
    Nesse cenário, o `OnConnectedAsync` `OnDisconnectedAsync` será disparado no novo servidor e no servidor antigo, respectivamente com um `IConnectionMigrationFeature` conjunto no `HttpContext` , que pode ser usado para identificar se a conexão do cliente estava sendo migrada-em nossa migração. Isso pode ser útil especialmente para cenários com estado.

    A conexão do cliente será migrada imediatamente após a entrega da mensagem atual, o que significa que a próxima mensagem será roteada para o novo servidor.

4. **Parar conexões do servidor**

    Depois que todas as conexões de cliente forem fechadas/migradas ou tempo limite (30s por padrão) excedido,

    O SDK do servidor signalr continuará o processo de desligamento para esse estágio e fechará todas as conexões do servidor.

    As conexões de cliente ainda serão descartadas se não forem fechadas/migradas. Por exemplo, não foi concluído nenhum servidor de destino adequado/mensagem de cliente para servidor atual.

## <a name="sample-codes"></a>Códigos de exemplo.

Adicione as seguintes opções quando `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>Configure `OnConnected` e `OnDisconnected` ao definir o modo de desligamento normal como `MigrateClients` .

Apresentamos um "IConnectionMigrationFeature" para indicar se uma conexão estava sendo migrada-in/out.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```