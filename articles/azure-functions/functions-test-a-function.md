---
title: Testando os Azure Functions
description: Criar testes automatizados para uma Função do C# no Visual Studio e uma Função JavaScript no VS Code
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: 27bc8171509e16f25c508dd71515d8cb25a11b0b
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639872"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Estratégias para testar seu código no Azure Functions

Este artigo demonstra como criar testes automatizados para o Azure Functions.

É recomendável testar todo o código; no entanto, talvez você obtenha resultados melhores ao encapsular uma lógica da função e criar testes fora dela. Abstrair a lógica limita as linhas de código da função e permite que ela seja a única responsável por chamar outras classes ou módulos. Este artigo, no entanto, demonstra como criar testes automatizados em relação a um HTTP e a funções disparadas por temporizador.

O conteúdo a seguir é dividido em duas seções diferentes direcionadas a linguagens e ambientes distintos. Você pode aprender a criar testes:

- [C# no Visual Studio com xUnit](#c-in-visual-studio)
- [JavaScript no VS Code com Jest](#javascript-in-vs-code)

O repositório de exemplo está disponível no [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>C# no Visual Studio

O exemplo a seguir descreve como criar um aplicativo de funções C# no Visual Studio e executar testes com [xUnit](https://github.com/xunit/xunit).

![Testar o Azure Functions com C# no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Instalação

Para configurar o ambiente, crie uma função e teste o aplicativo. As etapas a seguir ajudam você a criar os aplicativos e as funções necessários para os testes:

1. [Crie um aplicativo de funções](./functions-get-started.md) e nomeie-o como **Functions**
2. [Crie uma função http a partir do modelo](./functions-get-started.md) e nomeie-a **MyHttpTrigger**.
3. [Crie uma função de temporizador a partir do modelo](./functions-create-scheduled-function.md) e nomeie-a **MyTimerTrigger**.
4. [Crie um aplicativo de teste do xUnit](https://xunit.net/docs/getting-started/netcore/cmdline) na solução e nomeie-o como **functions. Tests**.
5. Use o NuGet para adicionar uma referência do aplicativo de teste a [Microsoft. AspNetCore. Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Referencie o aplicativo de *funções*](/visualstudio/ide/managing-references-in-a-project) do aplicativo *functions. Tests* .

### <a name="create-test-classes"></a>Criar classes de teste

Agora que os projetos foram criados, você pode criar as classes usadas para executar os testes automatizados.

Cada função usa uma instância do [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) para manipular o log de mensagens. Alguns testes não registram mensagens ou não se preocupam com como log é implementado. Outros testes precisam avaliar as mensagens registradas para determinar se foram aprovadas.

Você criará uma nova classe chamada `ListLogger` que contém uma lista interna de mensagens a serem avaliadas durante um teste. Para implementar a `ILogger` interface necessária, a classe precisa de um escopo. A classe a seguir simula um escopo para os casos de teste passar para a `ListLogger` classe.

Crie uma nova classe no projeto *functions. Tests* chamada **NullScope. cs** e insira o seguinte código:

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

Em seguida, crie uma nova classe no projeto *functions. Tests* chamada **ListLogger. cs** e insira o seguinte código:

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

- **BeginScope**: os escopos adicionam contexto ao seu registro em log. Nesse caso, o teste apenas aponta para a instância estática na `NullScope` classe para permitir que o teste funcione.

- **IsEnabled**: é fornecido um valor padrão de `false` .

- **Log**: esse método usa a `formatter` função fornecida para formatar a mensagem e, em seguida, adiciona o texto resultante à `Logs` coleção.

A coleção `Logs` é uma instância de `List<string>` e é inicializada no construtor.

Em seguida, crie um novo arquivo no projeto *functions. Tests* chamado **LoggerTypes. cs** e insira o código a seguir:

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

Agora, crie uma nova classe no projeto *functions. Tests* chamada **TestFactory. cs** e insira o seguinte código:

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

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
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

- **Dados**: essa propriedade retorna uma coleção [IEnumerable](/dotnet/api/system.collections.ienumerable) de dados de exemplo. Os pares chave-valor representam valores que são passados para uma cadeia de caracteres de consulta.

- **CreateDictionary**: esse método aceita um par de chave/valor como argumentos e retorna um novo `Dictionary` usado para criar `QueryCollection` para representar valores de cadeia de caracteres de consulta.

- **Createhttprequest**: esse método cria uma solicitação HTTP inicializada com os parâmetros de cadeia de caracteres de consulta fornecidos.

- **Createlogger**: com base no tipo de agente, esse método retorna uma classe de agente usada para teste. O `ListLogger` controla as mensagens registradas disponíveis para avaliação em testes.

Por fim, crie uma nova classe no projeto *functions. Tests* chamada **FunctionsTests. cs** e insira o seguinte código:

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
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

Os membros implementados nesta classe são:

- **Http_trigger_should_return_known_string**: esse teste cria uma solicitação com os valores de cadeia de caracteres de consulta de `name=Bill` para uma função http e verifica se a resposta esperada é retornada.

- **Http_trigger_should_return_string_from_member_data**: esse teste usa atributos xUnit para fornecer dados de exemplo para a função http.

- **Timer_should_log_message**: esse teste cria uma instância do `ListLogger` e a passa para uma função de temporizador. Depois que a função é executada, o log é verificado para garantir que a mensagem esperada está presente.

Se você quiser acessar as configurações do aplicativo em seus testes, poderá [injetar](./functions-dotnet-dependency-injection.md) uma `IConfiguration` instância com valores de variável de ambiente fictícios em sua função.

### <a name="run-tests"></a>Executar testes

Para executar os testes, navegue até a **Gerenciador de Testes** e clique em **Executar todos**.

![Testar o Azure Functions com C# no Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Depurar testes

Para depurar os testes, defina um ponto de interrupção em um teste, navegue até o **Gerenciador de Testes** e clique em **Executar > Depurar última execução**.

## <a name="javascript-in-vs-code"></a>JavaScript no VS Code

O exemplo a seguir descreve como criar um aplicativo de funções JavaScript no VS Code e executar testes com [Jest](https://jestjs.io). Este procedimento usa a [extensão de funções do VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) para criar no Azure Functions.

![Testar o Azure Functions com JavaScript no VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Instalação

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

Esse módulo implementa a propriedade `IsPastDue` como uma instância de temporizador falsa. Configurações de temporizador como expressões NCRONTAB não são necessárias aqui, pois o equipamento de teste está simplesmente chamando a função diretamente para testar o resultado.

Em seguida, use a extensão de funções do VS Code para [criar uma função HTTP do JavaScript](/azure/developer/javascript/tutorial-vscode-serverless-node-01) e nomeie-a *HttpTrigger*. Após a criação da função, adicione um novo arquivo à mesma pasta de nome **index.test.js** e adicione o seguinte código:

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
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
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