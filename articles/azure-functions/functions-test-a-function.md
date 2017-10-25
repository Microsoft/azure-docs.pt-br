---
title: Testando o Azure Functions | Microsoft Docs
description: Teste o Azure Functions usando Postman, cURL e Node.js.
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, testes"
ms.assetid: c00f3082-30d2-46b3-96ea-34faf2f15f77
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/02/2017
ms.author: wesmc
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 41796a8cdde0756e5157ba276463a56b07679d04
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar seu código no Azure Functions

Este tópico demonstra as várias maneiras de testar as funções, incluindo o uso das seguintes abordagens gerais:

+ Ferramentas baseadas em HTTP, como cURL, Postman e até mesmo um navegador da Web para gatilhos baseados na Web
+ Gerenciador de Armazenamento do Azure para testar gatilhos baseados no Armazenamento do Azure
+ Guia Teste no portal do Azure Functions
+ Função disparada por timer
+ Teste de aplicativo ou de estrutura

Todos esses métodos de testes usam uma função de gatilho HTTP que aceita entrada por meio de um parâmetro de cadeia de caracteres de consulta ou o corpo da solicitação. Você cria esta função na primeira seção.

## <a name="create-a-function-for-testing"></a>Criar uma função para teste
Durante a maior parte deste tutorial, usamos uma versão ligeiramente modificada do modelo da função JavaScript HttpTrigger disponível quando você criar uma função. Se você precisar de ajuda para criar uma função, examinar isso [tutorial](functions-create-first-azure-function.md). Escolha o modelo **HttpTrigger- JavaScript** ao criar a função de teste no [portal do Azure].

O modelo da função padrão é basicamente uma função “hello world” que retorna o nome do corpo da solicitação do parâmetro de cadeia de caracteres da consulta, `name=<your name>`.  Atualizamos o código para permitir também que você forneça o nome e um endereço como conteúdo JSON no corpo da solicitação. Em seguida, a função retorna para o cliente quando disponível.   

Atualize a função com o código a seguir, que usaremos para teste:

```javascript
module.exports = function (context, req) {
    context.log("HTTP trigger function processed a request. RequestUri=%s", req.originalUrl);
    context.log("Request Headers = " + JSON.stringify(req.headers));
    var res;

    if (req.query.name || (req.body && req.body.name)) {
        if (typeof req.query.name != "undefined") {
            context.log("Name was provided as a query string param...");
            res = ProcessNewUserInformation(context, req.query.name);
        }
        else {
            context.log("Processing user info from request body...");
            res = ProcessNewUserInformation(context, req.body.name, req.body.address);
        }
    }
    else {
        res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done(null, res);
};
function ProcessNewUserInformation(context, name, address) {
    context.log("Processing user information...");
    context.log("name = " + name);
    var echoString = "Hello " + name;
    var res;

    if (typeof address != "undefined") {
        echoString += "\n" + "The address you provided is " + address;
        context.log("address = " + address);
    }
    res = {
        // status: 200, /* Defaults to 200 */
        body: echoString
    };
    return res;
}
```

## <a name="test-a-function-with-tools"></a>Testar uma função com ferramentas
Fora do portal do Azure, há várias ferramentas que podem ser usadas para disparar as funções para teste. Elas incluem as ferramentas de teste de HTTP (baseado na interface do usuário e de linha de comando), ferramentas de acesso ao Armazenamento do Azure e até mesmo um navegador da web simples.

### <a name="test-with-a-browser"></a>Testar com um navegador
O navegador da web é uma maneira simples de funções de gatilho via HTTP. Você pode usar um navegador para solicitações GET que não exigem uma carga de corpo e usam apenas parâmetros de cadeia de caracteres de consulta.

Para testar a função que definimos anteriormente, copie a **Url da Função** do portal. Ele tem o seguinte formato:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Acrescente a `name` parâmetro para a cadeia de caracteres de consulta. Usar um nome real para o `<Enter a name here>` espaço reservado.

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>&name=<Enter a name here>

Cole a URL no seu navegador e você obterá uma resposta semelhante à seguinte.

![Guia do navegador de captura de tela de cromo com resposta de teste](./media/functions-test-a-function/browser-test.png)

Este exemplo é o navegador Chrome, que encapsula a cadeia de caracteres retornada em XML. Outros navegadores exibem apenas o valor de cadeia de caracteres.

Na janela **Logs** do portal, é registrada uma saída semelhante à seguinte ao executar a função:

    2016-03-23T07:34:59  Welcome, you are now connected to log-streaming service.
    2016-03-23T07:35:09.195 Function started (Id=61a8c5a9-5e44-4da0-909d-91d293f20445)
    2016-03-23T07:35:10.338 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==&name=Glenn from a browser
    2016-03-23T07:35:10.338 Request Headers = {"cache-control":"max-age=0","connection":"Keep-Alive","accept":"text/html","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T07:35:10.338 Name was provided as a query string param.
    2016-03-23T07:35:10.338 Processing User Information...
    2016-03-23T07:35:10.369 Function completed (Success, Id=61a8c5a9-5e44-4da0-909d-91d293f20445)

### <a name="test-with-postman"></a>Testar com o Postman
A ferramenta recomendada para a maioria das funções de teste é carteiro, que integra o navegador Chrome. Para instalar o Postman, consulte [Obter o Postman](https://www.getpostman.com/). O Postman fornece controle sobre muito mais atributos de uma solicitação HTTP.

> [!TIP]
> Use o HTTP que estiver mais familiarizado com a ferramenta de teste. Aqui estão algumas alternativas ao Postman:  
>
> * [Fiddler](http://www.telerik.com/fiddler)  
> * [Paw](https://luckymarmot.com/paw)  
>
>

Para testar a função com um corpo de solicitação no Postman:

1. Inicie o Postman com o botão **Aplicativos** no canto superior esquerdo de uma janela do navegador Chrome.
2. Copie a **Url da Função** e cole-a no Postman. Ele inclui o parâmetro de cadeia de caracteres de consulta do código de acesso.
3. Altere o método HTTP para **POST**.
4. Clique em **Corpo** > **bruto** e adicione um corpo da solicitação JSON da seguinte maneira:

    ```json
    {
        "name" : "Wes testing with Postman",
        "address" : "Seattle, WA 98101"
    }
    ```
5. Clique em **Enviar**.

A imagem a seguir mostra como testar o exemplo de função de eco simples neste tutorial.

![Interface de usuário de captura de tela de Postman](./media/functions-test-a-function/postman-test.png)

Na janela **Logs** do portal, é registrada uma saída semelhante à seguinte ao executar a função:

    2016-03-23T08:04:51  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:04:57.107 Function started (Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)
    2016-03-23T08:04:57.763 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/WesmcHttpTriggerNodeJS1?code=XXXXXXXXXX==
    2016-03-23T08:04:57.763 Request Headers = {"cache-control":"no-cache","connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:04:57.763 Processing user info from request body...
    2016-03-23T08:04:57.763 Processing User Information...
    2016-03-23T08:04:57.763 name = Wes testing with Postman
    2016-03-23T08:04:57.763 address = Seattle, W.A. 98101
    2016-03-23T08:04:57.795 Function completed (Success, Id=dc5db8b1-6f1c-4117-b5c4-f6b602d538f7)

### <a name="test-with-curl-from-the-command-line"></a>Teste com rotação da linha de comando
Muitas vezes, quando você estiver testando software, não será necessário procurar além da linha de comando para ajudar a depurar seu aplicativo. Isso não é diferente com funções de teste. Observe que a rotação está disponível por padrão nos sistemas baseados em Linux. No Windows, você deve primeiro baixar e instalar o [cURL ferramenta](https://curl.haxx.se/).

Para testar a função que definimos acima, copie a **URL da Função** do portal. Ele tem o seguinte formato:

    https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Esta é a URL para o disparo de sua função. Esse teste, use o comando cURL na linha de comando para fazer um GET (`-G` ou `--get`) solicitação em relação a função:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code>

Esse exemplo específico exige um parâmetro de cadeia de caracteres de consulta que pode ser passado como Dados (`-d`) no comando cURL:

    curl -G https://<Your Function App>.azurewebsites.net/api/<Your Function Name>?code=<your access code> -d name=<Enter a name here>

Execute o comando e você verá a seguinte saída da função na linha de comando:

![Saída de tela do Prompt de comando](./media/functions-test-a-function/curl-test.png)

Na janela **Logs** do portal, é registrada uma saída semelhante à seguinte ao executar a função:

    2016-04-05T21:55:09  Welcome, you are now connected to log-streaming service.
    2016-04-05T21:55:30.738 Function started (Id=ae6955da-29db-401a-b706-482fcd1b8f7a)
    2016-04-05T21:55:30.738 Node.js HTTP trigger function processed a request. RequestUri=https://functionsExample.azurewebsites.net/api/HttpTriggerNodeJS1?code=XXXXXXX&name=Azure Functions
    2016-04-05T21:55:30.738 Function completed (Success, Id=ae6955da-29db-401a-b706-482fcd1b8f7a)

### <a name="test-a-blob-trigger-by-using-storage-explorer"></a>Testar um gatilho de blob usando o Gerenciador de Armazenamento
Você pode testar uma função de gatilho de blob usando o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).

1. No [portal do Azure] para seu aplicativo de funções, crie uma função de gatilho de blob do C#, do F# ou do JavaScript. Defina o caminho a ser monitorado para o nome do seu contêiner de blobs. Por exemplo:

        files
2. Clique no botão **+** para selecionar ou criar a conta de armazenamento que você deseja usar. Em seguida, clique em **Criar**.
3. Crie um arquivo de texto com o texto a seguir e salve-o:

        A text file for blob trigger function testing.
4. Execute o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/) e conecte-se ao contêiner de blob na conta de armazenamento que está sendo monitorada.
5. Clique em **Carregar** para carregar o arquivo de texto.

    ![Captura de tela do Gerenciador de Armazenamento](./media/functions-test-a-function/azure-storage-explorer-test.png)

O código de função do gatilho de blob padrão relata o processamento do blob nos logs:

    2016-03-24T11:30:10  Welcome, you are now connected to log-streaming service.
    2016-03-24T11:30:34.472 Function started (Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)
    2016-03-24T11:30:34.472 C# Blob trigger function processed: A text file for blob trigger function testing.
    2016-03-24T11:30:34.472 Function completed (Success, Id=739ebc07-ff9e-4ec4-a444-e479cec2e460)

## <a name="test-a-function-within-functions"></a>Testar uma função dentro de funções
O portal do Azure Functions foi projetado para permitir que você teste as funções de timer e HTTP acionadas. Você também pode criar funções para disparar outras funções que você está testando.

### <a name="test-with-the-functions-portal-run-button"></a>Testar com o botão Executar do portal do Functions
O portal fornece um **executar** botão que você pode usar para fazer algumas limitada a testes. Você pode fornecer um corpo de solicitação usando o botão, porém não é possível fornecer parâmetros de cadeia de caracteres de consulta ou atualizar os cabeçalhos de solicitação.

Teste a função de gatilho HTTP criada anteriormente adicionando uma cadeia de caracteres JSON similar à seguinte no campo **Corpo da solicitação**. Em seguida, clique no botão **Executar**.

```json
{
    "name" : "Wes testing Run button",
    "address" : "USA"
}
```

Na janela **Logs** do portal, é registrada uma saída semelhante à seguinte ao executar a função:

    2016-03-23T08:03:12  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:03:17.357 Function started (Id=753a01b0-45a8-4125-a030-3ad543a89409)
    2016-03-23T08:03:18.697 HTTP trigger function processed a request. RequestUri=https://functions841def78.azurewebsites.net/api/wesmchttptriggernodejs1
    2016-03-23T08:03:18.697 Request Headers = {"connection":"Keep-Alive","accept":"*/*","accept-encoding":"gzip","accept-language":"en-US"}
    2016-03-23T08:03:18.697 Processing user info from request body...
    2016-03-23T08:03:18.697 Processing User Information...
    2016-03-23T08:03:18.697 name = Wes testing Run button
    2016-03-23T08:03:18.697 address = USA
    2016-03-23T08:03:18.744 Function completed (Success, Id=753a01b0-45a8-4125-a030-3ad543a89409)


### <a name="test-with-a-timer-trigger"></a>Testar com um gatilho de temporizador
Para algumas funções, realmente não é possível testar adequadamente com as ferramentas já mencionadas. Por exemplo, considere uma função de gatilho de fila executada quando uma mensagem é inserida no [Armazenamento de Filas do Azure](../storage/queues/storage-dotnet-how-to-use-queues.md). Você sempre pode escrever código para remover uma mensagem da fila e um exemplo disso em um projeto de console fornecido posteriormente neste artigo. No entanto, há outra abordagem que você pode usar para testar com funções diretamente.  

Você pode usar um gatilho de temporizador configurado com uma associação de saída da fila. Esse código de gatilho de temporizador pode então escrever as mensagens de teste para a fila. Esta seção explica um exemplo.

Para obter informações mais detalhadas sobre como usar associações com os Azure Functions, consulte a [Referência do desenvolvedor dos Azure Functions](functions-reference.md).

#### <a name="create-a-queue-trigger-for-testing"></a>Criar um gatilho de fila para teste
Para demonstrar essa abordagem, primeiro criaremos uma função de gatilho de fila que desejamos testar para uma fila denominada `queue-newusers`. Essa função processa as informações de nome e endereço soltas no armazenamento de Filas para um novo usuário.

> [!NOTE]
> Se você usar um nome de fila diferente, verifique se o nome usado está de acordo com as regras de [Nomeação de Filas e Metadados](https://msdn.microsoft.com/library/dd179349.aspx) . Caso contrário, você obterá um erro.
>
>

1. No [portal do Azure] para seu aplicativo de Funções, clique em **Nova Função** > **QueueTrigger - C#**.
2. Inserir o nome da fila a ser monitorada pela função de fila:

        queue-newusers
3. Clique no botão **+** para selecionar ou criar a conta de armazenamento que você deseja usar. Em seguida, clique em **Criar**.
4. Deixe essa janela do navegador do portal aberta para que você possa monitorar as entradas de log para o código do modelo da função de fila padrão.

#### <a name="create-a-timer-trigger-to-drop-a-message-in-the-queue"></a>Criar um gatilho de temporizador para remover uma mensagem na fila
1. Abra o [portal do Azure] em uma nova janela do navegador e navegue para seu aplicativo de funções.
2. Clique em **Nova Função** > **TimerTrigger - C#**. Insira uma expressão cron para definir com que frequência o código do temporizador testará sua função de fila. Em seguida, clique em **Criar**. Se você quiser que o teste seja executado cada 30 segundos, use a seguinte [expressão CRON](https://wikipedia.org/wiki/Cron#CRON_expression):

        */30 * * * * *
3. Clique na guia **Integrar** para o novo gatilho de temporizador.
4. Em **saída**, clique em **+ nova saída**. Em seguida, clique em **fila** e **selecione**.
5. Observe o nome usado para o **objeto de mensagem da fila**. Você pode usar isso no código da função timer.

        myQueue
6. Insira o nome da fila na qual a mensagem é enviada:

        queue-newusers
7. Clique no botão **+** para selecionar a conta de armazenamento que você usou anteriormente com o gatilho de fila. Em seguida, clique em **Salvar**.
8. Clique na guia **Desenvolver** do gatilho de temporizador.
9. Você pode usar o código a seguir para a função de temporizador C#, desde que tenha suado o mesmo nome de objeto de mensagem de fila mostrado anteriormente. Em seguida, clique em **Salvar**.

    ```cs
    using System;

    public static void Run(TimerInfo myTimer, out String myQueue, TraceWriter log)
    {
        String newUser =
        "{\"name\":\"User testing from C# timer function\",\"address\":\"XYZ\"}";

        log.Verbose($"C# Timer trigger function executed at: {DateTime.Now}");   
        log.Verbose($"{newUser}");   

        myQueue = newUser;
    }
    ```

Neste ponto, a função de temporizador do C# será executada a cada 30 segundos caso você tenha usado a expressão cron de exemplo. Os logs da função de temporizador relatam cada execução:

    2016-03-24T10:27:02  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.004 Function started (Id=04061790-974f-4043-b851-48bd4ac424d1)
    2016-03-24T10:27:30.004 C# Timer trigger function executed at: 3/24/2016 10:27:30 AM
    2016-03-24T10:27:30.004 {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.004 Function completed (Success, Id=04061790-974f-4043-b851-48bd4ac424d1)

Na janela do navegador para a função de fila, você poderá ver cada mensagem que está sendo processada:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"User testing from C# timer function","address":"XYZ"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)

## <a name="test-a-function-with-code"></a>Testar uma função com código
Talvez seja necessário criar um aplicativo externo ou uma estrutura para testar suas funções.

### <a name="test-an-http-trigger-function-with-code-nodejs"></a>Testar uma função de gatilho HTTP com código: Node.js
Você pode usar um aplicativo Node.js para executar uma solicitação HTTP para testar sua função.
Lembre-se de definir:

* O `host` nas opções de solicitação para o host do aplicativo de funções.
* O nome da função no `path`.
* Seu código de acesso (`<your code>`) em `path`.

Exemplo de código:

```javascript
var http = require("http");

var nameQueryString = "name=Wes%20Query%20String%20Test%20From%20Node.js";

var nameBodyJSON = {
    name : "Wes testing with Node.JS code",
    address : "Dallas, T.X. 75201"
};

var bodyString = JSON.stringify(nameBodyJSON);

var options = {
  host: "functions841def78.azurewebsites.net",
  //path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9&" + nameQueryString,
  path: "/api/HttpTriggerNodeJS2?code=sc1wt62opn7k9buhrm8jpds4ikxvvj42m5ojdt0p91lz5jnhfr2c74ipoujyq26wab3wk5gkfbt9",
  method: "POST",
  headers : {
      "Content-Type":"application/json",
      "Content-Length": Buffer.byteLength(bodyString)
    }    
};

callback = function(response) {
  var str = ""
  response.on("data", function (chunk) {
    str += chunk;
  });

  response.on("end", function () {
    console.log(str);
  });
}

var req = http.request(options, callback);
console.log("*** Sending name and address in body ***");
console.log(bodyString);
req.end(bodyString);
```


Saída:

    C:\Users\Wesley\testing\Node.js>node testHttpTriggerExample.js
    *** Sending name and address in body ***
    {"name" : "Wes testing with Node.JS code","address" : "Dallas, T.X. 75201"}
    Hello Wes testing with Node.JS code
    The address you provided is Dallas, T.X. 75201

Na janela **Logs** do portal, é registrada uma saída semelhante à seguinte ao executar a função:

    2016-03-23T08:08:55  Welcome, you are now connected to log-streaming service.
    2016-03-23T08:08:59.736 Function started (Id=607b891c-08a1-427f-910c-af64ae4f7f9c)
    2016-03-23T08:09:01.153 HTTP trigger function processed a request. RequestUri=http://functionsExample.azurewebsites.net/api/WesmcHttpTriggerNodeJS1/?code=XXXXXXXXXX==
    2016-03-23T08:09:01.153 Request Headers = {"connection":"Keep-Alive","host":"functionsExample.azurewebsites.net"}
    2016-03-23T08:09:01.153 Name not provided as query string param. Checking body...
    2016-03-23T08:09:01.153 Request Body Type = object
    2016-03-23T08:09:01.153 Request Body = [object Object]
    2016-03-23T08:09:01.153 Processing User Information...
    2016-03-23T08:09:01.215 Function completed (Success, Id=607b891c-08a1-427f-910c-af64ae4f7f9c)


### <a name="test-a-queue-trigger-function-with-code-c"></a>Testar uma função de gatilho de fila com o código: C# #
Mencionamos anteriormente que você pode testar um gatilho de fila usando código para remover uma mensagem na fila. O exemplo de código a seguir é baseado no código C# apresentado no tutorial [Introdução ao Armazenamento de Filas do Azure](../storage/queues/storage-dotnet-how-to-use-queues.md). Também há código para outras linguagens disponível nesse link.

Para testar esse código em um aplicativo de console, que você deve:

* [Configurar a cadeia de conexão de armazenamento no arquivo app.config](../storage/queues/storage-dotnet-how-to-use-queues.md).
* Passe um `name` e `address` como parâmetros para o aplicativo. Por exemplo: `C:\myQueueConsoleApp\test.exe "Wes testing queues" "in a console app"`. (Este código aceita o nome e o endereço de um novo usuário como argumentos de linha de comando durante o tempo de execução).

Código C# de exemplo:

```cs
static void Main(string[] args)
{
    string name = null;
    string address = null;
    string queueName = "queue-newusers";
    string JSON = null;

    if (args.Length > 0)
    {
        name = args[0];
    }
    if (args.Length > 1)
    {
        address = args[1];
    }

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["StorageConnectionString"]);

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference(queueName);

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    if (name != null)
    {
        if (address != null)
            JSON = String.Format("{{\"name\":\"{0}\",\"address\":\"{1}\"}}", name, address);
        else
            JSON = String.Format("{{\"name\":\"{0}\"}}", name);
    }

    Console.WriteLine("Adding message to " + queueName + "...");
    Console.WriteLine(JSON);

    CloudQueueMessage message = new CloudQueueMessage(JSON);
    queue.AddMessage(message);
}
```

Na janela do navegador para a função de fila, você poderá ver cada mensagem que está sendo processada:

    2016-03-24T10:27:06  Welcome, you are now connected to log-streaming service.
    2016-03-24T10:27:30.607 Function started (Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)
    2016-03-24T10:27:30.607 C# Queue trigger function processed: {"name":"Wes testing queues","address":"in a console app"}
    2016-03-24T10:27:30.607 Function completed (Success, Id=e304450c-ff48-44dc-ba2e-1df7209a9d22)


<!-- URLs. -->

[portal do Azure]: https://portal.azure.com
