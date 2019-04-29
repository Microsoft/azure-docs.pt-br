---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 08/16/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 8d73a22473ffff358c7424249c7581f6af740718
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749488"
---
### <a name="create-a-console-application"></a>Criar um aplicativo de console

Se você tiver desabilitado a opção "Exige Autorização do Cliente" ao criar a Retransmissão, poderá enviar solicitações para a URL de Conexões Híbridas com qualquer navegador. Para acessar os pontos de extremidade protegidos, você precisa criar e passar um token no cabeçalho `ServiceBusAuthorization`, que é mostrado aqui.

N o Visual Studio, crie um novo projeto de **Aplicativo de Console (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Adicione o pacote NuGet de retransmissão

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Selecione a opção **Incluir pré-lançamento**. 
3. Selecione **Procurar** e, em seguida, procure **Microsoft.Azure.Relay**. Nos resultados da pesquisa, selecione **Retransmissão do Microsoft Azure**.
4. Para obter a versão, selecione **2.0.0-preview1-20180523**. 
5. Selecione **Instalar** para concluir a instalação. Feche a caixa de diálogo.

### <a name="write-code-to-send-requests"></a>Gravar código para enviar solicitações

1. Na parte superior do arquivo Program.cs, substitua as instruções `using` existentes pelas instruções `using` a seguir:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using System.Net.Http;
    using Microsoft.Azure.Relay;
    ```
2. Adicione constantes à classe `Program` para os detalhes da conexão híbrida. Substitua os espaços reservados entre colchetes pelos valores obtidos quando você criou a conexão híbrida. Use o nome totalmente qualificado do namespace.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Adicione o seguinte método à classe `Program`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());        Console.ReadLine();
    }
    ```
4. Adicione a linha de código a seguir ao método `Main` na classe `Program`.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    O Program.cs deve ter esta aparência:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```

