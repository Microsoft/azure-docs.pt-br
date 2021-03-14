---
title: incluir arquivo
description: incluir arquivo
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 08/25/2020
ms.openlocfilehash: f98c34a53de60adc7866c1b57918232b966a0783
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444376"
---
[Documentação de referência](/dotnet/api/Microsoft.Azure.CognitiveServices.Personalizer) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Personalizer) | [Pacote (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Personalizer/) | [Exemplos](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer)

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* A versão atual do [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Depois de obter a assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Criar um recurso do Personalizador"  target="_blank">, crie um recurso do Personalizador </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar o seu aplicativo à API do Personalizador. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.

## <a name="setting-up"></a>Ao configurar o

[!INCLUDE [Change model frequency](change-model-frequency.md)]

### <a name="create-a-new-c-application"></a>Criar um aplicativo em C#

Crie um novo aplicativo .NET Core em seu IDE ou editor preferido.

Em uma janela de console (como cmd, PowerShell ou Bash), use o comando `new` para criar um novo aplicativo do console com o nome `personalizer-quickstart`. Esse comando cria um projeto simples em C# do tipo "Olá, Mundo" com um arquivo de origem único: `Program.cs`.

```console
dotnet new console -n personalizer-quickstart
```

Altere o diretório para a pasta do aplicativo recém-criado. É possível criar o aplicativo com:

```console
dotnet build
```

A saída de compilação não deve conter nenhum aviso ou erro.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Dentro do diretório do aplicativo, instale a biblioteca de clientes do Personalizador para .NET com o seguinte comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Personalizer --version 0.8.0-preview
```

> [!TIP]
> Se você estiver usando o IDE do Visual Studio, a biblioteca de clientes estará disponível como um pacote baixável do NuGet.

No diretório do projeto, abra o arquivo `Program.cs` no IDE ou no editor de sua preferência. Adicione o seguinte usando as orientações:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;
```

## <a name="object-model"></a>Modelo de objeto

O cliente do Personalizador é um objeto [PersonalizerClient](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclient) que se autentica no Azure usando Microsoft.Rest.ServiceClientCredentials, que contém sua chave.

Para solicitar o melhor item do conteúdo, crie uma [RankRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rankrequest) e, em seguida, transmita-a para o método [client.Rank](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.rank). O método Rank retornará uma RankResponse.

Para enviar uma pontuação de recompensa ao Personalizador, crie uma [RewardRequest](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.models.rewardrequest) e, em seguida, transmita-a para método [client.Reward](/dotnet/api/microsoft.azure.cognitiveservices.personalizer.personalizerclientextensions.reward).

Determinar a pontuação de recompensa neste início rápido é trivial. Em um sistema de produção, a determinação do que afeta a [pontuação de recompensa](../concept-rewards.md) e em que medida pode ser um processo complexo; você pode decidir alterá-lo ao longo do tempo. Essa decisão de design deve ser uma das principais decisões na arquitetura do Personalizador.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de código mostram como realizar as seguintes tarefas com a biblioteca de clientes do Personalizador para .NET:

* [Criar um cliente do Personalizador](#authenticate-the-client)
* [API de Classificação](#request-the-best-action)
* [API de Recompensa](#send-a-reward)


## <a name="authenticate-the-client"></a>Autenticar o cliente

Nesta seção, você executará duas etapas:
* Especificar a sua chave e o ponto de extremidade
* Criar um cliente do Personalizador

Comece adicionando as linhas a seguir à sua classe do Programa. Certifique-se de adicionar a sua chave e o ponto de extremidade do recurso do Personalizador.

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```csharp
private static readonly string ApiKey = "REPLACE-WITH-YOUR-PERSONALIZER-KEY";
private static readonly string ServiceEndpoint = "https://REPLACE-WITH-YOUR-PERSONALIZER-RESOURCE-NAME.cognitiveservices.azure.com";
```

Em seguida, adicione um método ao seu programa para criar um cliente do Personalizador.

```csharp
static PersonalizerClient InitializePersonalizerClient(string url)
{
    PersonalizerClient client = new PersonalizerClient(
        new ApiKeyServiceClientCredentials(ApiKey)) { Endpoint = url };

    return client;
}
```

## <a name="get-food-items-as-rankable-actions"></a>Obter itens de alimentos como ações classificáveis

As ações representam as opções de conteúdo entre as quais você deseja que o Personalizador selecione o melhor item de conteúdo. Adicione os métodos a seguir à classe Program para representar o conjunto de ações e os recursos. 

```csharp
static IList<RankableAction> GetActions()
{
    IList<RankableAction> actions = new List<RankableAction>
    {
        new RankableAction
        {
            Id = "pasta",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
        },

        new RankableAction
        {
            Id = "ice cream",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
        },

        new RankableAction
        {
            Id = "juice",
            Features =
            new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
        },

        new RankableAction
        {
            Id = "salad",
            Features =
            new List<object>() { new { taste = "salty", spiceLevel = "low" }, new { nutritionLevel = 8 } }
        }
    };

    return actions;
}
```

## <a name="get-user-preferences-for-context"></a>Obter preferências do usuário para o contexto

Adicione os seguintes métodos à classe Program para obter a entrada de um usuário da linha de comando para a hora do dia e a preferência de alimentos atual. Eles serão usados como recursos de contexto.

```csharp
static string GetUsersTimeOfDay()
{
    string[] timeOfDayFeatures = new string[] { "morning", "afternoon", "evening", "night" };

    Console.WriteLine("\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night");
    if (!int.TryParse(GetKey(), out int timeIndex) || timeIndex < 1 || timeIndex > timeOfDayFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + timeOfDayFeatures[0] + ".");
        timeIndex = 1;
    }

    return timeOfDayFeatures[timeIndex - 1];
}
```

```csharp
static string GetUsersTastePreference()
{
    string[] tasteFeatures = new string[] { "salty", "sweet" };

    Console.WriteLine("\nWhat type of food would you prefer (enter number)? 1. salty 2. sweet");
    if (!int.TryParse(GetKey(), out int tasteIndex) || tasteIndex < 1 || tasteIndex > tasteFeatures.Length)
    {
        Console.WriteLine("\nEntered value is invalid. Setting feature value to " + tasteFeatures[0] + ".");
        tasteIndex = 1;
    }

    return tasteFeatures[tasteIndex - 1];
}
```

Os dois métodos usam o método `GetKey` para ler a seleção do usuário na linha de comando.

```csharp
private static string GetKey()
{
    return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
}
```

## <a name="create-the-learning-loop"></a>Criar o loop de aprendizado

O loop de aprendizado do Personalizador é um ciclo de chamadas de [Classificação](#request-the-best-action) e [Recompensa](#send-a-reward). Neste início rápido, cada chamada de Classificação, para personalizar o conteúdo, é seguida por uma chamada de Recompensa para informar ao Personalizador o nível de desempenho do serviço.

O código a seguir executa um loop em um ciclo que inclui perguntas sobre as preferências do usuário na linha de comando, envio dessas informações ao Personalizador para seleção da melhor ação, apresentação da seleção ao cliente para escolha de uma opção na lista e, em seguida, envio de uma pontuação de recompensa ao Personalizador, sinalizando o nível de desempenho do serviço na seleção.

```csharp
static void Main(string[] args)
{
    int iteration = 1;
    bool runLoop = true;

    IList<RankableAction> actions = GetActions();

    PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

    do
    {
        Console.WriteLine("\nIteration: " + iteration++);

        string timeOfDayFeature = GetUsersTimeOfDay();
        string tasteFeature = GetUsersTastePreference();

        IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature }
        };

        IList<string> excludeActions = new List<string> { "juice" };

        string eventId = Guid.NewGuid().ToString();

        var request = new RankRequest(actions, currentContext, excludeActions, eventId);
        RankResponse response = client.Rank(request);

        Console.WriteLine("\nPersonalizer service thinks you would like to have: " + response.RewardActionId + ". Is this correct? (y/n)");

        float reward = 0.0f;
        string answer = GetKey();

        if (answer == "Y")
        {
            reward = 1;
            Console.WriteLine("\nGreat! Enjoy your food.");
        }
        else if (answer == "N")
        {
            reward = 0;
            Console.WriteLine("\nYou didn't like the recommended food choice.");
        }
        else
        {
            Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
        }

        Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
        foreach (var rankedResponse in response.Ranking)
        {
            Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
        }

        client.Reward(response.EventId, new RewardRequest(reward));

        Console.WriteLine("\nPress q to break, any other key to continue:");
        runLoop = !(GetKey() == "Q");

    } while (runLoop);
}
```

Adicione os seguintes métodos, que [obtêm as opções de conteúdo](#get-food-items-as-rankable-actions), antes de executar o arquivo de código:

* `GetActions`
* `GetUsersTimeOfDay`
* `GetUsersTastePreference`
* `GetKey`

## <a name="request-the-best-action"></a>Solicitar a melhor ação

Para concluir a solicitação de Classificação, o programa pergunta as preferências do usuário para criar um `currentContext` das opções de conteúdo. O processo pode criar o conteúdo a ser excluído das ações, mostrado como `excludeActions`. A solicitação de Classificação precisa das ações e dos recursos delas, dos recursos do currentContext, das excludeActions e de uma ID exclusiva do evento para receber a resposta.

Este início rápido tem recursos de contexto simples da hora do dia e da preferência de alimentos do usuário. Em sistemas de produção, determinar e [avaliar](../concept-feature-evaluation.md) [ações e recursos](../concepts-features.md) pode ser uma questão não trivial.

```csharp
string timeOfDayFeature = GetUsersTimeOfDay();
string tasteFeature = GetUsersTastePreference();

IList<object> currentContext = new List<object>() {
    new { time = timeOfDayFeature },
    new { taste = tasteFeature }
};

IList<string> excludeActions = new List<string> { "juice" };

string eventId = Guid.NewGuid().ToString();

var request = new RankRequest(actions, currentContext, excludeActions, eventId);
RankResponse response = client.Rank(request);
```

## <a name="send-a-reward"></a>Enviar uma recompensa

Para obter a pontuação de recompensa a ser enviada na solicitação de Recompensa, o programa obtém a seleção do usuário na linha de comando, atribui um valor numérico a cada seleção e, em seguida, envia uma ID exclusiva do evento e a pontuação de recompensa como o valor numérico para a API de Recompensa.

Este início rápido atribui um número simples como uma pontuação de recompensa, um zero ou um 1. Em sistemas de produção, determinar quando e o que enviar para a chamada de [Recompensa](../concept-rewards.md) pode ser uma questão não trivial, dependendo das suas necessidades específicas.

```csharp
float reward = 0.0f;
string answer = GetKey();

if (answer == "Y")
{
    reward = 1;
    Console.WriteLine("\nGreat! Enjoy your food.");
}
else if (answer == "N")
{
    reward = 0;
    Console.WriteLine("\nYou didn't like the recommended food choice.");
}
else
{
    Console.WriteLine("\nEntered choice is invalid. Service assumes that you didn't like the recommended food choice.");
}

Console.WriteLine("\nPersonalizer service ranked the actions with the probabilities as below:");
foreach (var rankedResponse in response.Ranking)
{
    Console.WriteLine(rankedResponse.Id + " " + rankedResponse.Probability);
}

// Send the reward for the action based on user response.
client.Reward(response.EventId, new RewardRequest(reward));
```

## <a name="run-the-program"></a>Execute o programa

Execute o aplicativo em seu próprio diretório com o comando `run` de dotnet.

```console
dotnet run
```

![O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.](../media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

O [código-fonte desse início rápido](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Personalizer) está disponível.
