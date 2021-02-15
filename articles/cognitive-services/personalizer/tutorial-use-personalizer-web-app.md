---
title: Usar aplicativo Web – Personalizador
description: Personalize um aplicativo Web .NET em C# com um loop Personalizador para fornecer o conteúdo correto a um usuário com base em ações (com recursos) e recursos de contexto.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c004887e3883ae711974b544510dff16a98d4ef9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363911"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Tutorial: Adicionar Personalizador a um aplicativo Web .NET

Personalize um aplicativo Web .NET em C# com um loop Personalizador para fornecer o conteúdo correto a um usuário com base em ações (com recursos) e recursos de contexto.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurar a chave e o ponto de extremidade do Personalizador
> * Coletar recursos
> * Chamar APIs de Classificação e Recompensa
> * Exibir a melhor ação, designada como _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>Selecionar o melhor conteúdo para um aplicativo Web

Um aplicativo Web deve usar o Personalizador quando há uma lista de _ações_ (algum tipo de conteúdo) na página da Web que precisa ser personalizada para exibir o melhor item (rewardActionId). Exemplos de listas de ações incluem artigos de notícias, locais de posicionamento de botão e opções de palavras para nomes de produtos.

Você envia a lista de ações, juntamente com recursos de contexto, para o loop do Personalizador. O Personalizador seleciona a melhor ação e, em seguida, seu aplicativo Web exibe essa ação.

Neste tutorial, as ações são tipos de alimentos:

* macarrão
* sorvete
* suco
* salada
* pipoca
* café
* sopa

Para ajudar o Personalizador a aprender com as suas ações, envie as _ações com recursos_ e os _recursos de contexto_ com cada solicitação à API de Classificação.

Um **recurso** do modelo consiste em informações sobre a ação ou o contexto que pode ser agregado (agrupado) entre membros da sua base de usuários do aplicativo Web. Um recurso _não é_ específico individualmente (como uma ID de usuário) ou altamente específico (como uma hora exata do dia).

### <a name="actions-with-features"></a>Ações com recursos

Cada ação (item de conteúdo) tem recursos para ajudar a distinguir o item de alimento.

Os recursos não são configurados como parte da configuração de loop no portal do Azure. Em vez disso, eles são enviados como um objeto JSON em cada chamada à API de Classificação. Isso dá flexibilidade para que as ações e seus recursos aumentem, alterem e reduzam ao longo do tempo, permitindo que o Personalizador acompanhe as tendências.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
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
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Recursos de contexto

Os recursos de contexto ajudam o Personalizador a entender o contexto das ações. O contexto para este aplicativo de exemplo inclui:

* hora do dia: manhã, tarde, fim de tarde, noite
* preferência de gosto do usuário: salgado, doce, amargo, ácido ou saboroso
* contexto do navegador: agente do usuário, localização geográfica, referenciador

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Como o aplicativo Web usa o Personalizador?

O aplicativo Web usa o Personalizador para selecionar a melhor ação da lista de opções de alimentos. Ele faz isso enviando as seguintes informações em cada chamada à API de Classificação:
* **ações** com seus recursos, como `taste` e `spiceLevel`
* recursos de **contexto** , como `time` do dia, a preferência de `taste` do usuário e as informações do agente do usuário do navegador e os recursos de contexto
* **ações a serem excluídas** como suco
* **eventId** , que é diferente para cada chamada à API de Classificação.

## <a name="personalizer-model-features-in-a-web-app"></a>Recursos de modelo do Personalizador em um aplicativo Web

O Personalizador precisa de recursos para as ações (conteúdo) e o contexto atual (usuário e ambiente). Os recursos são usados para alinhar ações ao contexto atual no modelo. O modelo é uma representação do conhecimento passado do Personalizador sobre ações, contexto e recursos que permitem que ele tome decisões instruídas.

O modelo, incluindo os recursos, é atualizado em um agendamento com base na configuração **Frequência de atualização de modelo** no portal do Azure.

> [!CAUTION]
> Os recursos neste aplicativo destinam-se a ilustrar os recursos e os valores de recursos, mas não necessariamente os melhores recursos a serem usados em um aplicativo Web.

### <a name="plan-for-features-and-their-values"></a>Planejar recursos e seus valores

Os recursos devem ser selecionados com o mesmo planejamento e design que você aplicaria a qualquer esquema ou modelo em sua arquitetura técnica. Os valores de recurso podem ser definidos com lógica de negócios ou com sistemas de terceiros. Os valores de recurso não devem ser altamente específicos de modo que não se apliquem a um grupo ou uma classe de recursos.

### <a name="generalize-feature-values"></a>Generalizar valores de recurso

#### <a name="generalize-into-categories"></a>Generalizar em categorias

Esse aplicativo usa `time` como um recurso, mas agrupa o tempo em categorias como `morning`, `afternoon`, `evening` e `night`. Esse é um exemplo de como usar as informações de tempo, mas não de uma forma altamente específica, como `10:05:01 UTC+2`.

#### <a name="generalize-into-parts"></a>Generalizar em partes

Esse aplicativo usa os recursos de solicitação HTTP do navegador. Isso começa com uma cadeia de caracteres muito específica com todos os dados, por exemplo:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

A biblioteca de classes **HttpRequestFeatures** generaliza essa cadeia de caracteres em um objeto **userAgentInfo** com valores individuais. Os valores muito específicos são definidos como uma cadeia de caracteres vazia. Quando os recursos de contexto para a solicitação são enviados, ele tem o seguinte formato JSON:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Usando o aplicativo Web de exemplo

O aplicativo Web baseado em navegador de exemplo (todo o código é fornecido) precisa dos seguintes aplicativos instalados para executar o aplicativo.

Instale o seguinte software:

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1) – o servidor de back-end de exemplo usa o .NET Core
* [Node.js](https://nodejs.org/) – o cliente/front-end depende deste aplicativo
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) ou [CLI do .NET Core](/dotnet/core/tools/) – use o ambiente de desenvolvedor do Visual Studio 2019 ou a CLI do .NET Core para compilar e executar o aplicativo

### <a name="set-up-the-sample"></a>Configurar o exemplo
1. Clone o repositório de exemplos do Personalizador do Azure.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Navegue até _samples/HttpRequestFeatures_ para abrir a solução `HttpRequestFeaturesExample.sln`.

    Se solicitado, permita que o Visual Studio atualize o pacote .NET para o Personalizador.

### <a name="set-up-azure-personalizer-service"></a>Configurar o serviço Personalizador do Azure

1. [Crie um recurso do Personalizador](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) no portal do Azure.

1. Na portal do Azure, localize o `Endpoint` e `Key1` ou `Key2` (qualquer um funcionará) na guia **Chaves e Pontos de Extremidade**. Essas são o `PersonalizerServiceEndpoint` e a `PersonalizerApiKey`.
1. Preencha o `PersonalizerServiceEndpoint` no **appsettings.json**.
1. Configure a `PersonalizerApiKey` como um [segredo do aplicativo](/aspnet/core/security/app-secrets) de uma das seguintes maneiras:

    * Se você estiver usando a CLI do .NET Core, use o comando `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"`.
    * Se você estiver usando o Visual Studio, clique com o botão direito do mouse no projeto e selecione a opção de menu **Gerenciar Segredos do Usuário** para configurar as chaves do Personalizador. Ao fazer isso, o Visual Studio abrirá um arquivo `secrets.json` no qual você poderá adicionar as chaves da seguinte maneira:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>Execute o exemplo

Compile e execute o HttpRequestFeaturesExample com um dos seguintes métodos:

* Visual Studio 2019: Pressione **F5**
* CLI do .NET Core: `dotnet build` e, em seguida, `dotnet run`

Usando um navegador da Web, você pode enviar uma solicitação de Classificação e uma solicitação de Recompensa e ver as respostas, bem como os recursos de solicitação HTTP extraídos do seu ambiente.

> [!div class="mx-imgBorder"]
> ![A captura de tela mostra um exemplo do Recurso de Solicitação HTTP em um navegador da Web.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>Demonstrar o loop do Personalizador

1. Selecione o botão **Gerar nova Solicitação de Classificação** para criar um objeto JSON para a chamada à API de Classificação. Isso cria as ações (com recursos) e os recursos de contexto e exibe os valores para que você possa ver como o JSON ficou.

    Para seu aplicativo futuro, a geração de ações e recursos pode ocorrer no cliente, no servidor, em uma combinação dos dois ou com chamadas a outros serviços.

1. Selecione **Enviar Solicitação de Classificação** para enviar o objeto JSON para o servidor. O servidor chama a API de Classificação do Personalizador. O servidor recebe a resposta e retorna a ação de classificação mais alta para o cliente exibir.

1. Defina o valor de recompensa e selecione o botão **Enviar Solicitação de Recompensa**. Se você não alterar o valor de recompensa, o aplicativo cliente sempre enviará o valor igual a `1` para o Personalizador.

    > [!div class="mx-imgBorder"]
    > ![A captura de tela mostra a seção Solicitação de Recompensa.](./media/tutorial-web-app/reward-score-api-call.png)

    Para seu aplicativo futuro, a geração da pontuação de recompensa pode ocorrer após a coleta de informações do comportamento do usuário no cliente, juntamente com a lógica de negócios no servidor.

## <a name="understand-the-sample-web-app"></a>Entender o aplicativo Web de exemplo

O aplicativo Web de exemplo tem um servidor **.NET de C#** que gerencia a coleção de recursos e envia e recebe chamadas HTTP para o ponto de extremidade do Personalizador.

O aplicativo Web de exemplo usa um **aplicativo cliente front-end eliminatório** para capturar recursos e processar ações da interface do usuário, como cliques em botões e envio de dados para o servidor .NET.

As seções a seguir explicam as partes do servidor e do cliente que um desenvolvedor precisa entender para usar o Personalizador.

## <a name="rank-api-client-application-sends-context-to-server"></a>API de Classificação: O aplicativo cliente envia contexto para o servidor

O aplicativo cliente coleta o _agente do usuário_ do navegador do usuário.

> [!div class="mx-imgBorder"]
> ![Compile e execute o projeto HTTPRequestFeaturesExample. Uma janela do navegador é aberta para exibir o aplicativo de página única.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>API de Classificação: O aplicativo para servidores chama o Personalizador

Este é um aplicativo Web .NET típico com um aplicativo cliente; grande parte do código padrão é fornecido para você. O código que não é específico do Personalizador é removido dos trechos de código a seguir para que você possa se concentrar no código específico do Personalizador.

### <a name="create-personalizer-client"></a>Criar cliente do Personalizador

No **Startup.cs** do servidor, o ponto de extremidade e a chave do Personalizador são usados para criar o cliente do Personalizador. O aplicativo cliente não precisa se comunicar com o Personalizador neste aplicativo, mas precisa do servidor para fazer essas chamadas de SDK.

O código de inicialização do .NET do servidor Web é:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Selecionar a melhor ação

No **PersonalizerController.cs** do servidor, a API do servidor **GenerateRank** resume a preparação para chamar a API de Classificação

* Crie um `eventId` para a chamada da Classificação
* Obtenha a lista de ações
* Obtenha a lista de recursos do usuário e crie recursos de contexto
* Opcionalmente, defina as ações excluídas
* Chame a API de Classificação e retorne os resultados para o cliente

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

O JSON enviado ao Personalizador, contendo as ações (com recursos) e os recursos de contexto atuais, é semelhante a:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Retornar a rewardActionId do Personalizador ao cliente

A API de Classificação retorna a melhor ação selecionada **rewardActionId** ao servidor.

Exiba a ação retornada na **rewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>O cliente exibe a ação rewardActionId

Neste tutorial, o valor da `rewardActionId` é exibido.

No seu aplicativo futuro, isso poderá ser um texto exato, um botão ou uma seção da página da Web realçada. A lista é retornada para análises posteriores de pontuações, não para uma ordenação do conteúdo. Somente o conteúdo da `rewardActionId` deve ser exibido.

## <a name="reward-api-collect-information-for-reward"></a>API de Recompensa: coletar informações para recompensa

A [pontuação de recompensa](concept-rewards.md) deve ser cuidadosamente planejada, assim como os recursos são planejados. A pontuação de recompensa normalmente é um valor de 0 a 1. O valor _pode_ ser calculado parcialmente no aplicativo cliente, com base nos comportamentos do usuário e parcialmente no servidor, com base nas metas e na lógica de negócios.

Se o servidor não chamar a API de Recompensa dentro do **Tempo de espera de recompensa** configurado no portal do Azure para o recurso do Personalizador, a **Recompensa padrão** (também configurada no portal do Azure) será usada para esse evento.

Neste aplicativo de exemplo, você pode selecionar um valor para ver como a recompensa afeta as seleções.

## <a name="additional-ways-to-learn-from-this-sample"></a>Outras maneiras de aprender com este exemplo

O exemplo usa vários eventos baseados em tempo configurados no portal do Azure para o recurso do Personalizador. Brinque com esses valores e retorne a este aplicativo Web de exemplo para ver como as alterações afetam as chamadas à Classificação e Recompensa:

* Tempo de espera de recompensa
* Frequência de atualização do modelo

As configurações adicionais para brincar incluem:
* Recompensa padrão
* Percentual de exploração


## <a name="clean-up-resources"></a>Limpar os recursos

Quando você concluir este tutorial, limpe os seguintes recursos:

* Exclua o diretório do projeto de exemplo.
* Exclua o recurso do Personalizador; considere um recurso do Personalizador como dedicado às ações e ao contexto; somente reutilize o recurso se você ainda estiver usando os alimentos como o domínio do tema das ações.


## <a name="next-steps"></a>Próximas etapas
* [Como funciona o Personalizador](how-personalizer-works.md)
* [Recursos](concepts-features.md): aprenda conceitos sobre recursos usando ações e contexto
* [Recompensas](concept-rewards.md): saiba mais sobre como calcular recompensas