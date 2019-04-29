---
title: Visão geral das APIs do .NET Standard de Retransmissão do Azure | Microsoft Docs
description: Visão geral da API do .NET Standard de Retransmissão do Azure
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: b1da9ac1-811b-4df7-a22c-ccd013405c40
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: spelluru
ms.openlocfilehash: 78ad3ab49db162af060b4273deea717cd3472668
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749012"
---
# <a name="azure-relay-hybrid-connections-net-standard-api-overview"></a>Visão geral da API .NET Standard de Conexões Híbridas de Retransmissão do Azure

Este artigo resume algumas das principais [APIs de cliente](/dotnet/api/microsoft.azure.relay) .NET Standard de Conexões Híbridas de Retransmissão do Azure.
  
## <a name="relay-connection-string-builder-class"></a>Classe Construtor de Cadeia de Conexão de Retransmissão

A classe [RelayConnectionStringBuilder][RelayConnectionStringBuilder] formata cadeias de conexão que são específicas para Conexões Híbridas de Retransmissão. Você pode usá-la para verificar o formato de uma cadeia de conexão ou para criar uma cadeia de conexão do zero. Veja o código a seguir para obter um exemplo:

```csharp
var endpoint = "[Relay namespace]";
var entityPath = "[Name of the Hybrid Connection]";
var sharedAccessKeyName = "[SAS key name]";
var sharedAccessKey = "[SAS key value]";

var connectionStringBuilder = new RelayConnectionStringBuilder()
{
    Endpoint = endpoint,
    EntityPath = entityPath,
    SharedAccessKeyName = sasKeyName,
    SharedAccessKey = sasKeyValue
};
```

Você também pode passar uma cadeia de conexão diretamente para o método `RelayConnectionStringBuilder`. Esta operação permite que você verifique se a cadeia de conexão está em um formato válido. Se qualquer um dos parâmetros for inválido, o construtor gerará um `ArgumentException`.

```csharp
var myConnectionString = "[RelayConnectionString]";
// Declare the connectionStringBuilder so that it can be used outside of the loop if needed
RelayConnectionStringBuilder connectionStringBuilder;
try
{
    // Create the connectionStringBuilder using the supplied connection string
    connectionStringBuilder = new RelayConnectionStringBuilder(myConnectionString);
}
catch (ArgumentException ae)
{
    // Perform some error handling
}
```

## <a name="hybrid-connection-stream"></a>Fluxo de conexão híbrida

Se você estiver trabalhando com um [HybridConnectionClient][HCClient] ou um [HybridConnectionListener][HCListener], a classe [HybridConnectionStream][HCStream] será o objeto principal usado para enviar e receber dados de um ponto de extremidade de retransmissão do Azure.

### <a name="getting-a-hybrid-connection-stream"></a>Obter um fluxo de conexão híbrida

#### <a name="listener"></a>Ouvinte

Usando um objeto [HybridConnectionListener][HCListener], você pode obter um `HybridConnectionStream` da seguinte maneira:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var listener = new HybridConnectionListener(csb.ToString());
// Open a connection to the Relay endpoint
await listener.OpenAsync();
// Get a `HybridConnectionStream`
var hybridConnectionStream = await listener.AcceptConnectionAsync();
```

#### <a name="client"></a>Cliente

Usando um objeto [HybridConnectionClient][HCClient], você pode obter um objeto `HybridConnectionStream` da seguinte maneira:

```csharp
// Use the RelayConnectionStringBuilder to get a valid connection string
var client = new HybridConnectionClient(csb.ToString());
// Open a connection to the Relay endpoint and get a `HybridConnectionStream`
var hybridConnectionStream = await client.CreateConnectionAsync();
```

### <a name="receiving-data"></a>Recebendo dados

A classe [HybridConnectionStream][HCStream] permite comunicação bidirecional. Na maioria dos casos de uso, você recebe continuamente do fluxo. Se estiver lendo o texto do fluxo, recomendamos usar um objeto [StreamReader](https://msdn.microsoft.com/library/system.io.streamreader(v=vs.110).aspx), que permite uma análise mais fácil dos dados. Por exemplo, você pode ler os dados como texto em vez de como `byte[]`.

O código a seguir lê linhas de texto individuais do fluxo até que um cancelamento seja solicitado:

```csharp
// Create a CancellationToken, so that we can cancel the while loop
var cancellationToken = new CancellationToken();
// Create a StreamReader from the 'hybridConnectionStream`
var streamReader = new StreamReader(hybridConnectionStream);

while (!cancellationToken.IsCancellationRequested)
{
    // Read a line of input until a newline is encountered
    var line = await streamReader.ReadLineAsync();
    if (string.IsNullOrEmpty(line))
    {
        // If there's no input data, we will signal that 
        // we will no longer send data on this connection
        // and then break out of the processing loop.
        await hybridConnectionStream.ShutdownAsync(cancellationToken);
        break;
    }
}
```

### <a name="sending-data"></a>Enviar dados

Quando você tiver uma conexão estabelecida, você poderá enviar uma mensagem para o ponto de extremidade de Retransmissão. Já que o objeto de conexão herda o [Fluxo](https://msdn.microsoft.com/library/system.io.stream(v=vs.110).aspx), envie os dados como um `byte[]`. O exemplo a seguir mostra como fazer isso:

```csharp
var data = Encoding.UTF8.GetBytes("hello");
await clientConnection.WriteAsync(data, 0, data.Length);
```

No entanto, se você deseja enviar texto diretamente, sem a necessidade de codificar a cadeia de caracteres a cada vez, você pode encapsular o objeto `hybridConnectionStream` com um objeto [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx).

```csharp
// The StreamWriter object only needs to be created once
var textWriter = new StreamWriter(hybridConnectionStream);
await textWriter.WriteLineAsync("hello");
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Retransmissão do Azure, visite estes links:

* [Referência de Microsoft.Azure.Relay](/dotnet/api/microsoft.azure.relay)
* [O que é Retransmissão do Azure?](relay-what-is-it.md)
* [APIs de Retransmissão Disponíveis](relay-api-overview.md)

[RelayConnectionStringBuilder]: /dotnet/api/microsoft.azure.relay.relayconnectionstringbuilder
[HCStream]: /dotnet/api/microsoft.azure.relay.hybridconnectionstream
[HCClient]: /dotnet/api/microsoft.azure.relay.hybridconnectionclient
[HCListener]: /dotnet/api/microsoft.azure.relay.hybridconnectionlistener