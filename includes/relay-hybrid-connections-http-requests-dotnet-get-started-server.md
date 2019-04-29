---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 5c7c2fe101315959d07ce4912905bbf59a7ee664
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749489"
---
### <a name="create-a-console-application"></a>Criar um aplicativo de console

N o Visual Studio, crie um novo projeto de **Aplicativo de Console (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Adicione o pacote NuGet de retransmissão

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Selecione a opção **Incluir pré-lançamento**. 
3. Selecione **Procurar** e, em seguida, procure **Microsoft.Azure.Relay**. Nos resultados da pesquisa, selecione **Retransmissão do Microsoft Azure**.
4. Para obter a versão, selecione **2.0.0-preview1-20180523**. 
5. Selecione **Instalar** para concluir a instalação. Feche a caixa de diálogo.

### <a name="write-code-to-receive-messages"></a>Gravar código para receber mensagens

1. Na parte superior do arquivo Program.cs, substitua as instruções `using` existentes pelas instruções `using` a seguir:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    using System.Net;
    ```
2. Adicione constantes à classe `Program` para os detalhes da conexão híbrida. Substitua os espaços reservados entre colchetes pelos valores obtidos quando você criou a conexão híbrida. Use o nome totalmente qualificado do namespace.
   
    ```csharp
    // replace {RelayNamespace} with the name of your namespace
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";

    // replace {HybridConnectionName} with the name of your hybrid connection
    private const string ConnectionName = "{HybridConnectionName}";

    // replace {SAKKeyName} with the name of your Shared Access Policies key, which is RootManageSharedAccessKey by default
    private const string KeyName = "{SASKeyName}";

    // replace {SASKey} with the primary key of the namespace you saved earlier
    private const string Key = "{SASKey}";
    ```

3. Adicione o método `RunAsync` à classe `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var cts = new CancellationTokenSource();
   
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Subscribe to the status events.
        listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
        listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
        listener.Online += (o, e) => { Console.WriteLine("Online"); };

        // Provide an HTTP request handler
        listener.RequestHandler = (context) =>
        {
            // Do something with context.Request.Url, HttpMethod, Headers, InputStream...
            context.Response.StatusCode = HttpStatusCode.OK;
            context.Response.StatusDescription = "OK, This is pretty neat";
            using (var sw = new StreamWriter(context.Response.OutputStream))
            {
                sw.WriteLine("hello!");
            }
            
            // The context MUST be closed here
            context.Response.Close();
        };
            
        // Opening the listener establishes the control channel to
        // the Azure Relay service. The control channel is continuously 
        // maintained, and is reestablished when connectivity is disrupted.
        await listener.OpenAsync();
        Console.WriteLine("Server listening");
    
        // Start a new thread that will continuously read the console.
        await Console.In.ReadLineAsync();
        
        // Close the listener after you exit the processing loop.
        await listener.CloseAsync();
    }
    ```
5. Adicione a linha de código a seguir ao método `Main` na classe `Program`:
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    O arquivo Program.cs concluído deve ter esta aparência:
   
    ```csharp
    namespace Server
    {
        using System;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Relay;
   
        public class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            public static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
           
                // Subscribe to the status events.
                listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
                listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
                listener.Online += (o, e) => { Console.WriteLine("Online"); };
        
                // Provide an HTTP request handler
                listener.RequestHandler = (context) =>
                {
                    // Do something with context.Request.Url, HttpMethod, Headers, InputStream...
                    context.Response.StatusCode = HttpStatusCode.OK;
                    context.Response.StatusDescription = "OK";
                    using (var sw = new StreamWriter(context.Response.OutputStream))
                    {
                        sw.WriteLine("hello!");
                    }
                    
                    // The context MUST be closed here
                    context.Response.Close();
                };
           
                // Opening the listener establishes the control channel to
                // the Azure Relay service. The control channel is continuously 
                // maintained, and is reestablished when connectivity is disrupted.
                await listener.OpenAsync();
                Console.WriteLine("Server listening");
           
                // Start a new thread that will continuously read the console.
                await Console.In.ReadLineAsync();
               
                // Close the listener after you exit the processing loop.
                await listener.CloseAsync();
            }
        }
    }
    ```

