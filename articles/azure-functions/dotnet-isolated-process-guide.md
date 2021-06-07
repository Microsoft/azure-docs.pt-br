---
title: Guia de processo isolado do .NET para .NET 5,0 no Azure Functions
description: Saiba como usar um processo isolado do .NET para executar suas funções em C# no .NET 5,0 fora do processo no Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 4da685c247427e78297df1753779ee9b5c7866b8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023190"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Guia para executar funções no .NET 5,0 no Azure

Este artigo é uma introdução ao uso do C# para desenvolver funções de processo isoladas do .NET, que ficam fora do processo no Azure Functions. A execução fora do processo permite desacoplar seu código de função do tempo de execução de Azure Functions. Ele também fornece uma maneira de criar e executar funções direcionadas para a versão atual do .NET 5,0. 

| Introdução | Conceitos| Exemplos |
|--|--|--| 
| <ul><li>[Usar o Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Usando ferramentas de linha de comando](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Usando o Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Opções de hospedagem](functions-scale.md)</li><li>[Monitoring](functions-monitoring.md)</li> | <ul><li>[Exemplos de referência](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Se você não precisar dar suporte ao .NET 5,0 ou executar suas funções fora do processo, convém [desenvolver funções de biblioteca de classes C#](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Por que o processo isolado do .NET?

Anteriormente Azure Functions há suporte apenas para um modo rigidamente integrado para funções do .NET, que são executadas [como uma biblioteca de classes](functions-dotnet-class-library.md) no mesmo processo que o host. Esse modo fornece uma integração profunda entre o processo de host e as funções. Por exemplo, as funções de biblioteca de classes do .NET podem compartilhar APIs e tipos de associação. No entanto, essa integração também requer um acoplamento mais rígido entre o processo de host e a função .NET. Por exemplo, as funções do .NET em execução no processo são necessárias para executar na mesma versão do .NET que o tempo de execução do functions. Para permitir que você execute fora dessas restrições, agora você pode optar por executar em um processo isolado. Esse isolamento de processo também permite que você desenvolva funções que usam versões atuais do .NET (como o .NET 5,0), sem suporte nativo pelo tempo de execução do functions.

Como essas funções são executadas em um processo separado, há algumas [diferenças de funcionalidade e recurso](#differences-with-net-class-library-functions) entre aplicativos de funções isoladas do .net e aplicativos de função da biblioteca de classes do .net.

### <a name="benefits-of-running-out-of-process"></a>Benefícios da execução fora do processo

Ao executar fora do processo, suas funções do .NET podem aproveitar os seguintes benefícios: 

+ Menos conflitos: como as funções são executadas em um processo separado, os assemblies usados em seu aplicativo não entrarão em conflito com versões diferentes dos mesmos assemblies usados pelo processo de host.  
+ Controle total do processo: você controla a inicialização do aplicativo e pode controlar as configurações usadas e o middleware iniciado.
+ Injeção de dependência: como você tem controle total do processo, você pode usar os comportamentos atuais do .NET para injeção de dependência e incorporar middleware em seu aplicativo de funções. 

## <a name="supported-versions"></a>Versões com suporte

A única versão do .NET que atualmente tem suporte para executar fora do processo é o .NET 5,0.

## <a name="net-isolated-project"></a>Projeto isolado do .NET

Um projeto de função isolado do .NET é basicamente um projeto de aplicativo de console .NET que tem como alvo o .NET 5,0. Estes são os arquivos básicos necessários em qualquer projeto isolado do .NET:

+ [host.jsno](functions-host-json.md) arquivo.
+ [local.settings.jsno](functions-run-local.md#local-settings-file) arquivo.
+ Arquivo de projeto C# (. csproj) que define o projeto e as dependências.
+ Arquivo Program. cs que é o ponto de entrada para o aplicativo.

## <a name="package-references"></a>Referências de pacote

Ao executar fora do processo, seu projeto do .NET usa um conjunto exclusivo de pacotes, que implementam a funcionalidade principal e as extensões de associação. 

### <a name="core-packages"></a>Pacotes principais 

Os pacotes a seguir são necessários para executar as funções do .NET em um processo isolado:

+ [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft. Azure. Functions. Worker. Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Pacotes de extensão

Como as funções que são executadas em um processo isolado do .NET usam tipos de associação diferentes, elas exigem um conjunto exclusivo de pacotes de extensão de associação. 

Você encontrará esses pacotes de extensão em [Microsoft. Azure. Functions. Worker. Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Inicialização e configuração 

Ao usar funções isoladas do .NET, você tem acesso à inicialização do seu aplicativo de funções, que geralmente está em Program. cs. Você é responsável por criar e iniciar sua própria instância de host. Assim, você também tem acesso direto ao pipeline de configuração para seu aplicativo. Ao executar fora do processo, você pode adicionar configurações com mais facilidade, injetar dependências e executar seu próprio middleware. 

O código a seguir mostra um exemplo de um pipeline [HostBuilder] :

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Um [HostBuilder] é usado para criar e retornar uma instância de [IHost] totalmente inicializada, que você executa de forma assíncrona para iniciar seu aplicativo de funções. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Configuração

O método [ConfigureFunctionsWorkerDefaults] é usado para adicionar as configurações necessárias para que o aplicativo de funções seja executado fora do processo, o que inclui a seguinte funcionalidade:

+ Conjunto padrão de conversores.
+ Defina o [JsonSerializerOptions] padrão para ignorar maiúsculas e minúsculas nos nomes de propriedade.
+ Integre com o log de Azure Functions.
+ Middleware de associação de saída e recursos.
+ Middleware de execução de função.
+ Suporte a gRPC padrão. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Ter acesso ao pipeline do host Builder significa que você também pode definir qualquer configuração específica do aplicativo durante a inicialização. Você pode chamar o método [ConfigureAppConfiguration] em [HostBuilder] uma ou mais vezes para adicionar as configurações exigidas por seu aplicativo de funções. Para saber mais sobre a configuração do aplicativo, consulte [configuração no ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

Essas configurações se aplicam ao seu aplicativo de funções em execução em um processo separado. Para fazer alterações no host do Functions ou no gatilho e na configuração de associação, você ainda precisará usar o [host.jsno arquivo](functions-host-json.md).   

### <a name="dependency-injection"></a>Injeção de dependência

A injeção de dependência é simplificada, em comparação com as bibliotecas de classes do .NET. Em vez de precisar criar uma classe de inicialização para registrar serviços, basta chamar [configuraservices] no construtor de hosts e usar os métodos de extensão em [IServiceCollection] para injetar serviços específicos. 

O exemplo a seguir injeta uma dependência de serviço singleton:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Para saber mais, confira [injeção de dependência em ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Middleware

O .NET isolado também dá suporte ao registro de middleware, novamente usando um modelo semelhante ao que existe em ASP.NET. Esse modelo oferece a capacidade de injetar a lógica no pipeline de invocação e as funções before e After são executadas.

O método de extensão [ConfigureFunctionsWorkerDefaults] tem uma sobrecarga que permite registrar seu próprio middleware, como você pode ver no exemplo a seguir.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Para obter um exemplo mais completo de como usar o middleware personalizado em seu aplicativo de funções, consulte o [exemplo de referência de middleware personalizado](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Contexto de execução

O .NET isolado passa um objeto [FunctionContext] para seus métodos de função. Esse objeto permite que você obtenha uma instância de [ILogger] para gravar nos logs chamando o método [GetLogger] e fornecendo uma `categoryName` cadeia de caracteres. Para saber mais, consulte [Logging](#logging). 

## <a name="bindings"></a>Associações 

As associações são definidas usando atributos em métodos, parâmetros e tipos de retorno. Um método de função é um método com um atributo `Function` e um atributo de gatilho aplicado a um parâmetro de entrada, conforme mostrado no exemplo a seguir:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

O atributo de gatilho especifica o tipo de gatilho e associa dados de entrada a um parâmetro de método. A função de exemplo anterior é disparada por uma mensagem da fila e a mensagem da fila é passada para o método no `myQueueItem` parâmetro.

O atributo `Function` marca o método como um ponto de entrada da função. O nome deve ser exclusivo em um projeto, começar com uma letra e conter apenas letras, números, `_` e `-` até 127 caracteres de comprimento. Modelos de projeto geralmente criam um método chamado `Run`, mas o nome do método pode ser qualquer nome de método C# válido.

Como os projetos isolados do .NET são executados em um processo de trabalho separado, as associações não podem tirar proveito das classes de ligação avançadas, como, `ICollector<T>` `IAsyncCollector<T>` e `CloudBlockBlob` . Também não há suporte direto para tipos herdados de SDKs de serviço subjacentes, como [DocumentClient] e [BrokeredMessage]. Em vez disso, as associações dependem de cadeias de caracteres, matrizes e tipos serializáveis, como POCOs (objetos de classe antiga). 

Para gatilhos HTTP, você deve usar [HttpRequestData] e [HttpResponseData] para acessar os dados de solicitação e resposta. Isso ocorre porque você não tem acesso aos objetos de solicitação e resposta HTTP originais ao executar fora do processo.

Para obter um conjunto completo de exemplos de referência para usar gatilhos e associações ao executar fora do processo, consulte o [exemplo de referência de extensões de associação](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Associações de entrada

Uma função pode ter zero ou mais associações de entrada que podem passar dados para uma função. Como gatilhos, as associações de entrada são definidas pela aplicação de um atributo de associação a um parâmetro de entrada. Quando a função é executada, o tempo de execução tenta obter os dados especificados na associação. Os dados que estão sendo solicitados geralmente dependem das informações fornecidas pelo gatilho usando parâmetros de associação.  

### <a name="output-bindings"></a>Associações de saída

Para gravar em uma associação de saída, você deve aplicar um atributo de associação de saída ao método de função, que definiu como gravar no serviço associado. O valor retornado pelo método é gravado na associação de saída. Por exemplo, o exemplo a seguir grava um valor de cadeia de caracteres em uma fila de mensagens chamada `functiontesting2` usando uma associação de saída:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Várias associações de saída

Os dados gravados em uma associação de saída são sempre o valor de retorno da função. Se você precisar gravar em mais de uma associação de saída, deverá criar um tipo de retorno personalizado. Esse tipo de retorno deve ter o atributo de associação de saída aplicado a uma ou mais propriedades da classe. O exemplo a seguir de um gatilho HTTP é gravado na resposta HTTP e em uma associação de saída de fila:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

A resposta de um gatilho HTTP é sempre considerada uma saída, portanto, um atributo de valor de retorno não é necessário.

### <a name="http-trigger"></a>Gatilho HTTP

Os gatilhos HTTP convertem a mensagem de solicitação HTTP de entrada em um objeto [HttpRequestData] que é passado para a função. Esse objeto fornece dados da solicitação, incluindo `Headers` , `Cookies` , `Identities` , `URL` e uma mensagem opcional `Body` . Esse objeto é uma representação do objeto de solicitação HTTP e não da solicitação em si. 

Da mesma forma, a função retorna um objeto [HttpResponseData] , que fornece dados usados para criar a resposta http, incluindo a mensagem `StatusCode` , `Headers` e, opcionalmente, uma mensagem `Body` .  

O código a seguir é um gatilho HTTP 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Log

No .NET isolado, você pode gravar em logs usando uma instância [ILogger] Obtida de um objeto [FunctionContext] passado para sua função. Chame o método [GetLogger] , passando um valor de cadeia de caracteres que é o nome da categoria na qual os logs são gravados. A categoria geralmente é o nome da função específica da qual os logs são gravados. Para saber mais sobre categorias, consulte o [artigo monitoramento](functions-monitoring.md#log-levels-and-categories). 

O exemplo a seguir mostra como obter um [ILogger] e gravar logs dentro de uma função:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Use vários métodos de [ILogger] para gravar vários níveis de log, como `LogWarning` ou `LogError` . Para saber mais sobre os níveis de log, consulte o [artigo monitoramento](functions-monitoring.md#log-levels-and-categories).

Um [ILogger] também é fornecido ao usar a [injeção de dependência](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Diferenças com funções de biblioteca de classes do .NET

Esta seção descreve o estado atual das diferenças funcionais e comportamentais em execução no .NET 5,0 fora do processo em comparação com as funções da biblioteca de classes do .NET em execução no processo:

| Recurso/comportamento |  Em processo (.NET Core 3,1) | Fora do processo (.NET 5,0) |
| ---- | ---- | ---- |
| Versões do .NET | LTS (.NET Core 3,1) | Atual (.NET 5,0) |
| Pacotes principais | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. Functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. Functions. Worker. Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Pacotes de extensão de associação | [Microsoft. Azure. webjobs. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Em [Microsoft. Azure. Functions. Worker. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Log | [ILogger] passado para a função | [ILogger] obtido de [FunctionContext] |
| Tokens de cancelamento | [Com suporte](functions-dotnet-class-library.md#cancellation-tokens) | Sem suporte |
| Associações de saída | Parâmetros de saída | Valores retornados |
| Tipos de associação de saída |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage]e outros tipos específicos do cliente | Tipos simples, tipos serializáveis JSON e matrizes. |
| Várias associações de saída | Com suporte | [Com suporte](#multiple-output-bindings) |
| Gatilho HTTP | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Funções duráveis | [Com suporte](durable/durable-functions-overview.md) | Sem suporte | 
| Associações imperativas | [Com suporte](functions-dotnet-class-library.md#binding-at-runtime) | Sem suporte |
| function.jsno artefato | Gerado | Não gerado |
| Configuração | [host. JSON](functions-host-json.md) | Inicialização [dehost.js](functions-host-json.md) e personalizada |
| Injeção de dependência | [Com suporte](functions-dotnet-dependency-injection.md)  | [Com suporte](#dependency-injection) |
| Middleware | Sem suporte | Com suporte |
| Horários de início frio | Típico | Mais tempo, devido à inicialização just-in-time. Execute no Linux em vez do Windows para reduzir possíveis atrasos. |
| ReadyToRun | [Com suporte](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Problemas conhecidos

Para obter informações sobre soluções alternativas para saber problemas na execução de funções de processo isolado do .NET, consulte [esta página de problemas conhecidos](https://aka.ms/AAbh18e). Para relatar problemas, [crie um problema neste repositório GitHub](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose).  

## <a name="next-steps"></a>Próximas etapas

+ [Saiba mais sobre gatilhos e associações](functions-triggers-bindings.md)
+ [Saiba mais sobre práticas recomendadas do Azure Functions](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
