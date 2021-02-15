---
title: Usar Personalizador no chatbot – Personalizador
description: Personalize um chatbot .NET em C# com um loop Personalizador para fornecer o conteúdo correto a um usuário com base em ações (com recursos) e recursos de contexto.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 07/17/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7c4920eaa7a5619be37d38afd763e7be416d3124
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565714"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Tutorial: Usar Personalizador no chatbot .NET

Use um chatbot .NET em C# com um loop Personalizador para fornecer o conteúdo correto a um usuário. Esse chatbot sugere um café ou um chá específico para um usuário. O usuário pode aceitar ou rejeitar essa sugestão. Isso fornece informações ao Personalizador para ajudar a tornar a próxima sugestão mais apropriada.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Configurar recursos do Azure
> * Configurar e executar o bot
> * Interagir com o bot usando o Bot Framework Emulator
> * Entender onde e como o bot usa o Personalizador


## <a name="how-does-the-chat-bot-work"></a>Como o chatbot funciona?

Um chatbot é normalmente uma conversa com um usuário. Esse chatbot específico usa o Personalizador para selecionar a melhor ação (café ou chá) para oferecer ao usuário. O Personalizador usa o aprendizado de reforço para fazer essa seleção.

O chatbot precisa gerenciar os turnos da conversa. Ele usa o [Bot Framework](https://github.com/microsoft/botframework-sdk) para gerenciar a arquitetura e a conversa de bots e usa o Serviço Cognitivo [LUIS](../LUIS/index.yml) (Reconhecimento vocal) para entender a intenção da linguagem natural do usuário.

O chatbot é um site com uma rota específica disponível para responder a solicitações, `http://localhost:3978/api/messages`. Você pode usar o emulador de bot para interagir de forma visual com o chatbot em execução enquanto estiver desenvolvendo um bot localmente.

### <a name="user-interactions-with-the-bot"></a>Interações do usuário com o bot

Este é um chatbot simples que permite inserir consultas de texto.

|O usuário digita o texto|O bot responde com texto|Descrição da ação que o bot realiza para determinar o texto da resposta|
|--|--|--|
|Nenhum texto inserido – o bot começa a conversa.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|O bot começa a conversa com texto de instrução e informa o contexto: `Tuesday`, `Snowy`.|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Determina a intenção da consulta usando LUIS, depois exibe as opções do menu com os itens de café e chá. Os recursos das ações são |
|`What do you suggest`|`How about Latte?`|Determina a intenção da consulta usando LUIS, chama a **API de Classificação** e exibe a melhor opção como uma pergunta `How about {response.RewardActionId}?`. Também exibe a chamada JSON e a resposta para fins de ilustração.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Determina a intenção da consulta usando LUIS, depois chama a **API de Recompensa** com a recompensa `1` e exibe a chamada JSON e a resposta para fins de ilustração.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Determina a intenção da consulta usando LUIS, depois chama a **API de Recompensa** com a recompensa `0` e exibe a chamada JSON e a resposta para fins de ilustração.|
|`Reset`|Retorna o texto de instrução.|Determina a intenção da consulta usando LUIS, exibe o texto de instrução e redefine o contexto.|


### <a name="personalizer-in-this-bot"></a>Personalizador nesse bot

Esse chatbot usa o Personalizador para selecionar a ação principal (café ou chá específico), com base em uma lista de _ações_ (algum tipo de conteúdo) e recursos de contexto.

O bot envia a lista de ações, com os recursos de contexto, para o loop Personalizador. O Personalizador retorna a melhor ação individual para o bot, que a exibe.

Neste tutorial, as **ações** são tipos de café e chá:

|Café|Chá|
|--|--|
|Cappuccino<br>Expresso<br>Latte<br>Mocha|Chá verde<br>Rooibos|

**API de Classificação:** para ajudar o Personalizador a aprender sobre suas ações, o bot envia o seguinte com cada solicitação de API de Classificação:

* Ações _com recursos_
* Recursos de contexto

Um **recurso** do modelo consiste em informações sobre a ação ou o contexto que podem ser agregadas (agrupadas) entre membros da sua base de usuários do chatbot. Um recurso _não é_ específico individualmente (como uma ID de usuário) ou altamente específico (como uma hora exata do dia).

Os recursos são usados para alinhar ações ao contexto atual no modelo. O modelo é uma representação do conhecimento passado do Personalizador sobre ações, contexto e recursos que permitem que ele tome decisões instruídas.

O modelo, incluindo os recursos, é atualizado em um agendamento com base na configuração **Frequência de atualização de modelo** no portal do Azure.

Os recursos devem ser selecionados com o mesmo planejamento e design que você aplicaria a qualquer esquema ou modelo em sua arquitetura técnica. Os valores de recurso podem ser definidos com lógica de negócios ou com sistemas de terceiros.

> [!CAUTION]
> Os recursos neste aplicativo são para demonstração e podem não ser necessariamente os melhores a serem usados em um aplicativo Web para seu caso de uso.

#### <a name="action-features"></a>Recursos de ações

Cada ação (item de conteúdo) tem recursos para ajudar a distinguir o item de café ou chá.

Os recursos não são configurados como parte da configuração de loop no portal do Azure. Em vez disso, eles são enviados como um objeto JSON em cada chamada à API de Classificação. Isso dá flexibilidade para que as ações e seus recursos aumentem, alterem e reduzam ao longo do tempo, permitindo que o Personalizador acompanhe as tendências.

Os recursos para café e chá incluem:

* Local de origem do grão de café, como Quênia e Brasil
* O café ou chá é orgânico?
* Café com torra clara ou escura

Enquanto o café tem três recursos na lista anterior, o chá tem apenas um. Transmita para o Personalizador somente os recursos que fazem sentido para a ação. Não transmita um valor vazio para um recurso se ele não se aplicar à ação.

#### <a name="context-features"></a>Recursos de contexto

Recursos de contexto ajudam a Personalizador a entender o contexto do ambiente, como o dispositivo de exibição, o usuário, o local e outros recursos relevantes para o caso de uso.

O contexto para esse chatbot inclui:

* Tipo de clima (com neve, chuvoso, ensolarado)
* Dia da semana

A seleção de recursos é aleatória nesse chatbot. Em um bot real, use dados reais como recursos de contexto.

### <a name="design-considerations-for-this-bot"></a>Considerações de design para esse bot

Há algumas precauções a serem consideradas sobre essa conversa:
* **Interação com o bot**: a conversa é muito simples porque demonstra a classificação e a recompensa em um caso de uso simples. Ela não demonstra a funcionalidade completa do SDK do Bot Framework ou do Emulador.
* **Personalizador**: os recursos são selecionados aleatoriamente para simular o uso. Não torne os recursos aleatórios em um cenário de produção do Personalizador.
* **LUIS (Reconhecimento vocal)** : os poucos exemplos de enunciados do modelo do LUIS servem apenas para essa amostra. Não use poucos exemplos de enunciados em seu aplicativo LUIS de produção.


## <a name="install-required-software"></a>Instalar o software necessário
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). O repositório de amostras para download inclui instruções caso você prefira usar a CLI do .NET Core.
- O [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) é um aplicativo de área de trabalho que permite aos desenvolvedores de bot testar e depurar seus bots no localhost ou executá-los remotamente por meio de um túnel.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Baixar o exemplo de código do chatbot

O chatbot está disponível no repositório de amostras do Personalizador. Clone ou [baixe](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) o repositório, depois abra a amostra no diretório `/samples/ChatbotExample` com o Visual Studio 2019.

Para clonar o repositório, use o comando Git a seguir em um shell Bash (terminal).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Criar e configurar o Personalizador e os recursos do LUIS

### <a name="create-azure-resources"></a>Criar recursos do Azure

Para usar esse chatbot, você precisa criar recursos do Azure para o Personalizador e o LUIS (Reconhecimento vocal).

* [Crie recursos do LUIS](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal). Selecione **ambos** na etapa de criação porque você precisa de recursos de criação e de previsão.
* [Crie o recurso do Personalizador](how-to-create-resource.md) e copie a chave e o ponto de extremidade do portal do Azure. Você precisará definir esses valores no arquivo `appsettings.json` do projeto do .NET.

### <a name="create-luis-app"></a>Criar um aplicativo LUIS

Se você for novo no LUIS, precisará [entrar](https://www.luis.ai) e migrar imediatamente sua conta. Não é necessário criar recursos; em vez disso, selecione os recursos que você criou na seção anterior deste tutorial.

1. Para criar um aplicativo LUIS, no [Portal do LUIS](https://www.luis.ai), selecione sua assinatura e o recurso de criação.
1. Na mesma página, selecione **+ Novo aplicativo para conversa** e **Importar como JSON**.
1. Na caixa de diálogo pop-up, selecione **Escolher arquivo**, depois selecione o arquivo `/samples/ChatbotExample/CognitiveModels/coffeebot.json`. Insira o nome `Personalizer Coffee bot`.
1. Selecione o botão **Treinar** na barra de navegação superior direita do portal do LUIS.
1. Selecione o botão **Publicar** para publicar o aplicativo no **Slot de produção** no runtime de previsão.
1. Selecione **Gerenciar** e **Configurações**. Copie o valor da **ID do aplicativo**. Você precisará definir esse valor no arquivo `appsettings.json` do projeto do .NET.
1. Ainda na seção **Gerenciar**, selecione **Recursos do Azure**. Os recursos associados no aplicativo serão exibidos.
1. Selecione **Adicionar recurso de previsão**. Na caixa de diálogo pop-up, selecione sua assinatura e o recurso de previsão criado em uma seção anterior deste tutorial, depois selecione **Concluído**.
1. Copie os valores da **Chave primária** e **URL do ponto de extremidade**. Você precisará definir esses valores no arquivo `appsettings.json` do projeto do .NET.

### <a name="configure-bot-with-appsettingsjson-file"></a>Configurar o bot com o arquivo appsettings.json

1. Abra o arquivo `ChatbotSamples.sln` da solução de chatbot no Visual Studio 2019.
1. Abra `appsettings.json` no diretório raiz do projeto.
1. Defina as cinco configurações copiadas na seção anterior deste tutorial.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>Compilar e executar o bot

Depois de configurar o `appsettings.json`, você estará pronto para criar e executar o chatbot. Quando você faz isso, um navegador é aberto no site em execução, `http://localhost:3978`.

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Captura de tela do navegador exibindo o site do chatbot.":::

Mantenha o site em execução porque o tutorial explica o que o bot está fazendo, para que você possa interagir com ele.


## <a name="set-up-the-bot-emulator"></a>Configurar o emulador de bot

1. Abra o Bot Framework Emulator e selecione **Abrir Bot**.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Captura da tela de inicialização do emulador de bot.":::


1. Configure o bot com a seguinte **URL de bot** e selecione **Conectar**:

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Captura de tela do emulador de bot aberto nas configurações de bot.":::

    O emulador se conecta ao chatbot e exibe o texto de instrução, com informações de log e de depuração úteis para o desenvolvimento local.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Captura de tela do emulador de bot na conversa inicial.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>Usar o bot no emulador

1. Peça para ver o menu inserindo `I would like to see the menu`. O chatbot exibe os itens.
1. Deixe o bot sugerir um item inserindo `Please suggest a drink for me.`. O emulador exibe a solicitação de Classificação e a resposta na janela de chat para que você possa ver o JSON completo. E o bot faz uma sugestão, algo como `How about Latte?`
1. Ao responder que você gostaria disso, `I like it.`, você aceita a seleção de classificação mais alta do Personalizador. O emulador exibe a solicitação de Recompensa com a pontuação 1 e a resposta na janela de chat, para que você possa ver o JSON completo. E o bot responde com `That’s great! I’ll keep learning your preferences over time.` e `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Se você responder `no` à seleção, a pontuação de recompensa como o valor 0 será enviada ao Personalizador.


## <a name="understand-the-net-code-using-personalizer"></a>Entender o código .NET usando o Personalizador

A solução .NET é um chatbot simples de estrutura de bot. O código relacionado ao Personalizador está nas seguintes pastas:
* `/samples/ChatbotExample/Bots`
    * O arquivo `PersonalizerChatbot.cs` para a interação entre o bot e o Personalizador
* `/samples/ChatbotExample/ReinforcementLearning` – gerencia as ações e os recursos do modelo do Personalizador
* `/samples/ChatbotExample/Model` – arquivos para as ações e os recursos do Personalizador e para as intenções do LUIS

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs – Trabalhar com o Personalizador

A classe `PersonalizerChatbot` é derivada de `Microsoft.Bot.Builder.ActivityHandler`. Ela tem três propriedades e métodos para gerenciar o fluxo da conversa.

> [!CAUTION]
> Não copie o código deste tutorial. Use o exemplo de código disponível no [Repositório de amostras do Personalizador](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

Os métodos com o prefixo `Send` gerenciam a conversa com o bot e o LUIS. Os métodos `ChooseRankAsync` e `RewardAsync` interagem com o Personalizador.

#### <a name="calling-rank-api-and-display-results"></a>Chamar a API de Classificação e exibir os resultados

O método `ChooseRankAsync` compila os dados JSON a serem enviados para a API de Classificação do Personalizador ao coletar as ações com recursos e os recursos de contexto.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Chamar a API de Recompensa e exibir os resultados

O método `RewardAsync` compila os dados JSON para enviar à API de Recompensa do Personalizador ao determinar a pontuação. A pontuação é determinada pela intenção do LUIS identificada no texto do usuário e enviada com o método `OnTurnAsync`.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Considerações de design para um bot

Essa amostra destina-se a demonstrar uma solução simples de ponta a ponta do Personalizador em um bot. Pode ser que seu caso de uso seja mais complexo.

Se você pretende usar o Personalizador em um bot de produção, planeje o seguinte:
* O acesso em tempo real ao Personalizador _sempre_ que você precisar de uma seleção classificada. A API de Classificação não pode ser armazenada em lote ou em cache.  A chamada de recompensa pode ser atrasada ou descarregada em um processo separado e, se você não retornar uma recompensa no período cronometrado, um valor de recompensa padrão será definido para o evento.
* Cálculo da recompensa com base no caso de uso: esse exemplo mostrou duas recompensas de zero e um, sem intervalo e sem nenhum valor negativo para uma pontuação. Seu sistema pode precisar de uma pontuação mais granular.
* Canais de bot: essa amostra usa um único canal, mas, se você tiver a intenção de usar mais de um canal ou variações de bots em um único canal, poderá ser necessário considerar isso como parte dos recursos de contexto do modelo do Personalizador.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você concluir este tutorial, limpe os seguintes recursos:

* Exclua o diretório do projeto de exemplo.
* Exclua o recurso do Personalizador e do LUIS no portal do Azure.

## <a name="next-steps"></a>Próximas etapas
* [Como funciona o Personalizador](how-personalizer-works.md)
* [Recursos](concepts-features.md): aprenda conceitos sobre recursos usando ações e contexto
* [Recompensas](concept-rewards.md): saiba mais sobre como calcular recompensas
