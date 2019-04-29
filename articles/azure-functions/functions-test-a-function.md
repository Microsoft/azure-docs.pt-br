---
title: Testando os Azure Functions
description: Criar testes automatizados para uma Função do C# no Visual Studio e uma Função JavaScript no VS Code
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: azure functions, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor, testes
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: e0009e1c6380e02e2e0e24bf86e6dab435b6c022
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021096"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar seu código no Azure Functions

Este artigo demonstra como criar testes automatizados para o Azure Functions. 

É recomendável testar todo o código; no entanto, talvez você obtenha resultados melhores ao encapsular uma lógica da função e criar testes fora dela. Abstrair a lógica limita as linhas de código da função e permite que ela seja a única responsável por chamar outras classes ou módulos. No entanto, este artigo demonstra como criar testes automatizados em um HTTP e uma função disparada por temporizador.

O conteúdo a seguir é dividido em duas seções diferentes direcionadas a linguagens e ambientes distintos. Você pode aprender a criar testes:

- [C# no Visual Studio com xUnit](#c-in-visual-studio)
- [JavaScript no VS Code com Jest](#javascript-in-vs-code)

O repositório de exemplo está disponível no [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# no Visual Studio
O exemplo a seguir descreve como criar um aplicativo de funções C# no Visual Studio e executar testes com [xUnit](https://xunit.github.io).

![Testar o Azure Functions com C# no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Configuração

Para configurar o ambiente, crie uma função e teste o aplicativo. As etapas a seguir ajudam você a criar os aplicativos e as funções necessários para os testes:

1. [Crie um aplicativo de funções](./functions-create-first-azure-function.md) e nomeie-o como *Functions*
2. [Crie uma função HTTP do modelo](./functions-create-first-azure-function.md) e nomeie-a *HttpTrigger*.
3. [Crie uma função temporizadora do modelo](./functions-create-scheduled-function.md) e nomeie-a *TimerTrigger*.
4. [Crie um aplicativo de teste xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) no Visual Studio, clicando em **Arquivo > Novo > Projeto > Visual C# > .NET Core > Projeto de teste xUnit** e nomeie-o *Functions.Test*. 
5. Use o Nuget para adicionar uma referência do aplicativo teste [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referencie o aplicativo *Functions* no](https://docs.microsoft.com/visualstudio/ide/managing-references-in-a-project?view=vs-2017) aplicativo *Functions.Test*.

### <a name="create-test-classes"></a>Criar classes de teste

Agora que os aplicativos foram criados, será possível criar as classes usadas para executar os testes automatizados.

Cada função usa uma instância do [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) para manipular o log de mensagens. Alguns testes não registram mensagens ou não se preocupam com como log é implementado. Outros testes precisam avaliar as mensagens registradas para determinar se foram aprovadas.

A classe `ListLogger` deve implementar a interface `ILogger` e manter uma lista interna de mensagens para avaliação durante um teste.

**Clique com botão direito** sobre o *Functions.Test* aplicativo e selecione **Adicionar > classe**, nomeie- **NullScope.cs** e insira o código a seguir:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Em seguida, **com o botão direito** sobre o *Functions.Test* aplicativo e selecione **Adicionar > classe**, nomeie- **ListLogger.cs** e insira o o código a seguir:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel, 
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

A classe `ListLogger` implementará os seguintes membros, conforme contratado pela interface `ILogger`:

- **BeginScope**: Define o escopo de adicionar contexto do log. Nesse caso, o teste apenas aponta para a instância estática sobre o `NullScope` classe para permitir que o teste de função.

- **IsEnabled**: Um valor padrão de `false` é fornecido.

- **Log**: Esse método usa a função `formatter` fornecida para formatar a mensagem e, em seguida, adiciona o texto resultante à coleção `Logs`.

A coleção `Logs` é uma instância de `List<string>` e é inicializada no construtor.

Em seguida, **clique com o botão direito do mouse** no aplicativo *Functions.Test* e selecione **Adicionar > Classe**. Nomeie como **LoggerTypes.cs** e insira este código:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```
Esta enumeração especifica o tipo de agente usado pelos testes. 

Em seguida, **clique com o botão direito do mouse** no aplicativo *Functions.Test* e selecione **Adicionar > Classe**. Nomeie como **TestFactory.cs** e insira este código:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static DefaultHttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var request = new DefaultHttpRequest(new DefaultHttpContext())
            {
                Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue))
            };
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```
A classe `TestFactory` implementa os seguintes membros:

- **Data**: Essa propriedade retorna uma coleção [IEnumerable](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable) de dados de exemplo. Os pares chave-valor representam valores que são passados para uma cadeia de caracteres de consulta.

- **CreateDictionary**: Esse método aceita um par chave-valor como argumentos e retorna um novo `Dictionary` usado para criar `QueryCollection` para representar valores de cadeia de caracteres de consulta.

- **CreateHttpRequest**: Esse método cria uma solicitação HTTP inicializada com os parâmetros de cadeia de caracteres de consulta fornecidos.

- **CreateLogger**: Com base no tipo de agente, esse método retornará uma classe de agente usada para teste. O `ListLogger` controla as mensagens registradas disponíveis para avaliação em testes.

Em seguida, **clique com o botão direito do mouse** no aplicativo *Functions.Test* e selecione **Adicionar > Classe**. Nomeie como **FunctionsTests.cs** e insira este código:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await HttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            TimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```
Os membros implementados nesta classe são:

- **Http_trigger_should_return_known_string**: Esse teste cria uma solicitação com os valores de `name=Bill` da cadeia de caracteres de consulta para uma função HTTP e verifica se a resposta esperada será retornada.

- **Http_trigger_should_return_string_from_member_data**: Esse teste usa atributos de xUnit para fornecer dados de exemplo para a função HTTP.

- **Timer_should_log_message**: Esse teste cria uma instância de `ListLogger` e passa-a para uma função de temporizador. Depois que a função é executada, o log é verificado para garantir que a mensagem esperada está presente.

Se você quiser acessar as configurações de aplicativo em seus testes, você pode usar [GetEnvironmentVariable](./functions-dotnet-class-library.md#environment-variables).

### <a name="run-tests"></a>Executar testes

Para executar os testes, navegue até a **Gerenciador de Testes** e clique em **Executar todos**.

![Testar o Azure Functions com C# no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Depurar testes

Para depurar os testes, defina um ponto de interrupção em um teste, navegue até o **Gerenciador de Testes** e clique em **Executar > Depurar última execução**.

## <a name="javascript-in-vs-code"></a>JavaScript no VS Code

O exemplo a seguir descreve como criar um aplicativo de funções JavaScript no VS Code e executar testes com [Jest](https://jestjs.io). Este procedimento usa a [extensão de funções do VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar no Azure Functions.

![Testar o Azure Functions com JavaScript no VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Configuração

Para configurar o ambiente, inicialize um novo aplicativo Node.js em uma pasta vazia executando `npm init`.

```bash
npm init -y
```
Em seguida, instale o Jest executando o seguinte comando:

```bash
npm i jest
```
Agora, atualize _package.json_ para substituir o comando de teste existente com o seguinte comando:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Criar módulos de teste
Com o projeto inicializado, você poderá criar os módulos usados para executar os testes automatizados. Comece criando uma pasta chamada *testing* para manter os módulos de suporte.

Na pasta *testing*, adicione um novo arquivo, nomeie-o como **defaultContext.js** e adicione o seguinte código:

```javascript
module.exports = {
    log: jest.fn()
};
```
Esse módulo simula a função *log* para representar o contexto de execução padrão.

Em seguida, adicione um novo arquivo, nomeie- **defaultTimer.js** e adicione o seguinte código:

```javascript
module.exports = {
    IsPastDue: false
};
```
Esse módulo implementa a propriedade `IsPastDue` como uma instância de temporizador falsa.

Em seguida, use a extensão de funções do VS Code para [criar uma função HTTP do JavaScript](https://code.visualstudio.com/tutorials/functions-extension/getting-started) e nomeie-a *HttpTrigger*. Após a criação da função, adicione um novo arquivo à mesma pasta de nome **index.test.js** e adicione o seguinte código:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```
A função HTTP do modelo retornará uma cadeia de caracteres "Hello" concatenada com o nome fornecido na cadeia de caracteres de consulta. Esse teste cria uma instância falsa de uma solicitação e passa-a para a função HTTP. O teste verifica se o método *log* é chamado uma vez e se o texto retornado é igual a "Hello Bill".

Em seguida, use a extensão de funções do VS Code para criar uma função de temporizador do JavaScript e nomeie-a *TimerTrigger*. Após a criação da função, adicione um novo arquivo à mesma pasta de nome **index.test.js** e adicione o seguinte código:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```
A função de temporizador do modelo registra uma mensagem no final do corpo da função. Esse teste assegura que a função *log* será chamada uma vez.

### <a name="run-tests"></a>Executar testes
Para executar os testes, pressione **CTRL + ~** para abrir a janela de comando e execute `npm test`:

```bash
npm test
```

![Testar o Azure Functions com JavaScript no VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Depurar testes

Para depurar seus testes, adicione a seguinte configuração ao arquivo *launch.json*:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "program": "${workspaceRoot}\\node_modules\\jest\\bin\\jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Em seguida, defina um ponto de interrupção no teste e pressione **F5**.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a gravar testes automatizados das funções, continue com estes recursos:
- [Executar manualmente uma função não disparada por HTTP](./functions-manually-run-non-http.md)
- [Tratamento de erros do Azure Functions](./functions-bindings-error-pages.md)
- [Depuração local do gatilho da Grade de Eventos do Azure Functions](./functions-debug-event-grid-trigger-local.md)
