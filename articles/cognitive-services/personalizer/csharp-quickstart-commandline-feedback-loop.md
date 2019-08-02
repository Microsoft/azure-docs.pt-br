---
title: 'Início Rápido: Criar um loop de comentários – Personalizador'
titleSuffix: Azure Cognitive Services
description: Personalize o conteúdo neste Início Rápido do C# com o serviço Personalizador.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: diberry
ms.openlocfilehash: 54aa23071fef09058a1702218d6b7fc920363518
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662806"
---
# <a name="quickstart-personalize-content-using-c"></a>Início Rápido: Personalizar o conteúdo usando o C# 

Exiba o conteúdo personalizado neste início rápido do C# com o serviço Personalizador.

Esta amostra descreve como usar a biblioteca de clientes do Personalizador para o C# para executar as seguintes ações: 

 * Classificar uma lista de ações para personalização.
 * Relatar a recompensa para alocar a ação com melhor classificação de acordo com a seleção do usuário para o evento especificado.

A introdução ao Personalizador envolve as seguintes etapas:

1. Referenciar o SDK 
1. Escrever um código para classificar as ações que você deseja mostrar aos usuários.
1. Escrever um código para enviar as recompensas para treinar o loop.

## <a name="prerequisites"></a>Pré-requisitos

* Você precisa de um [serviço do Personalizador](how-to-settings.md) para obter sua chave de assinatura e a URL do ponto de extremidade. 
* [Visual Studio 2015 ou 2017](https://visualstudio.microsoft.com/downloads/).
* O pacote NuGet do SDK [Microsoft.Azure.CognitiveServices.Personalizer](https://go.microsoft.com/fwlink/?linkid=2092272). As instruções de instalação são fornecidas abaixo.

## <a name="change-the-model-update-frequency"></a>Alterar a frequência de atualização do modelo

No recurso do Personalizador no portal do Azure, altere a **Frequência de atualização do modelo** para 10 segundos. Isso treinará o serviço rapidamente, permitindo que você veja como a ação superior é alterada a cada iteração.

Quando o Loop do Personalizador é instanciado pela primeira vez, não há modelo, pois não houve nenhuma chamada à API de Recompensa com base na qual treinar. Chamadas de classificação retornarão probabilidades iguais para cada item. Seu aplicativo ainda deve classificar sempre o conteúdo usando a saída de RewardActionId.

![Alterar a frequência de atualização do modelo](./media/settings/configure-model-update-frequency-settings.png)

## <a name="creating-a-new-console-app-and-referencing-the-personalizer-sdk"></a>Como criar um aplicativo de console e referenciar o SDK do Personalizador 

<!--
Get the latest code as a Visual Studio solution from [GitHub] (add link).
-->

1. Crie um novo aplicativo de console do Visual C# no Visual Studio.
1. Instale o pacote NuGet da biblioteca de clientes do Personalizador. No menu, selecione **Ferramentas**, selecione **Gerenciador de Pacotes NuGet** e, em seguida, **Gerenciar Pacotes NuGet para a Solução**.
1. Marque **Incluir pré-lançamento**.
1. Selecione a guia **Procurar** e, na caixa **Pesquisa**, digite `Microsoft.Azure.CognitiveServices.Personalizer`.
1. Selecione **Microsoft.Azure.CognitiveServices.Personalizer** quando ele for exibido.
1. Marque a caixa de seleção ao lado do nome do projeto e selecione **Instalar**.

## <a name="add-the-code-and-put-in-your-personalizer-and-azure-keys"></a>Adicionar o código e colocá-lo nas chaves do Personalizador e do Azure

1. Substitua Program.cs pelo código a seguir. 
1. Substitua o valor `serviceKey` por sua chave de assinatura válida do Personalizador.
1. Substitua `serviceEndpoint` pelo ponto de extremidade de serviço. Um exemplo é `https://westus2.api.cognitive.microsoft.com/`.
1. Execute o programa.

## <a name="add-code-to-rank-the-actions-you-want-to-show-to-your-users"></a>Adicionar um código para classificar as ações que você deseja mostrar aos usuários

O código C# a seguir é uma listagem completa para passar informações de usuário, recursos e informações sobre o conteúdo, as _ações_, para o Personalizador usando o SDK. O Personalizador retorna a ação com a melhor classificação a ser mostrada ao usuário.  

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
using Microsoft.Azure.CognitiveServices.Personalizer.Models;
using System;
using System.Collections.Generic;
using System.Linq;

namespace PersonalizerExample
{
    class Program
    {
        // The key specific to your personalizer service instance; e.g. "0123456789abcdef0123456789ABCDEF"
        private const string ApiKey = "";

        // The endpoint specific to your personalizer service instance; e.g. https://westus2.api.cognitive.microsoft.com/
        private const string ServiceEndpoint = "";

        static void Main(string[] args)
        {
            int iteration = 1;
            bool runLoop = true;

            // Get the actions list to choose from personalizer with their features.
            IList<RankableAction> actions = GetActions();

            // Initialize Personalizer client.
            PersonalizerClient client = InitializePersonalizerClient(ServiceEndpoint);

            do
            {
                Console.WriteLine("\nIteration: " + iteration++);

                // Get context information from the user.
                string timeOfDayFeature = GetUsersTimeOfDay();
                string tasteFeature = GetUsersTastePreference();

                // Create current context from user specified data.
                IList<object> currentContext = new List<object>() {
                    new { time = timeOfDayFeature },
                    new { taste = tasteFeature }
                };

                // Exclude an action for personalizer ranking. This action will be held at its current position.
                IList<string> excludeActions = new List<string> { "juice" };

                // Generate an ID to associate with the request.
                string eventId = Guid.NewGuid().ToString();

                // Rank the actions
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

                // Send the reward for the action based on user response.
                client.Reward(response.EventId, new RewardRequest(reward));

                Console.WriteLine("\nPress q to break, any other key to continue:");
                runLoop = !(GetKey() == "Q");

            } while (runLoop);
        }

        /// <summary>
        /// Initializes the personalizer client.
        /// </summary>
        /// <param name="url">Azure endpoint</param>
        /// <returns>Personalizer client instance</returns>
        static PersonalizerClient InitializePersonalizerClient(string url)
        {
            PersonalizerClient client = new PersonalizerClient(
                new ApiKeyServiceClientCredentials(ApiKey)) {Endpoint = url};

            return client;
        }

        /// <summary>
        /// Get users time of the day context.
        /// </summary>
        /// <returns>Time of day feature selected by the user.</returns>
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

        /// <summary>
        /// Gets user food preference.
        /// </summary>
        /// <returns>Food taste feature selected by the user.</returns>
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

        /// <summary>
        /// Creates personalizer actions feature list.
        /// </summary>
        /// <returns>List of actions for personalizer.</returns>
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

        private static string GetKey()
        {
            return Console.ReadKey().Key.ToString().Last().ToString().ToUpper();
        }
    }
}
```

## <a name="run-the-program"></a>Execute o programa

Compile e execute o programa. O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.

![O programa de Início Rápido faz algumas perguntas para coletar as preferências do usuário, conhecidas como recursos e, em seguida, fornece a ação principal.](media/csharp-quickstart-commandline-feedback-loop/quickstart-program-feedback-loop-example.png)

## <a name="clean-up-resources"></a>Limpar recursos
Quando você terminar com o início rápido, remova todos os arquivos criados nesse início rápido. 

## <a name="next-steps"></a>Próximas etapas

[Como funciona o Personalizador](how-personalizer-works.md)


