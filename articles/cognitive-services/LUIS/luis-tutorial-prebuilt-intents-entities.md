---
title: Intenções e entidades predefinidas
titleSuffix: Azure Cognitive Services
description: Neste tutorial, adicione intenções e entidades predefinidas a um aplicativo para obter rapidamente a previsão de intenção e a extração de dados. Não é necessário rotular nenhum enunciado com as entidades pré-criadas. A entidade é detectada automaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2018
ms.author: diberry
ms.openlocfilehash: 189514c51451dedfaab7b07f28d79f674b32f83a
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277542"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Tutorial: Identificar intenções e entidades comuns

Neste tutorial, adicione intenções e entidades pré-criadas a um aplicativo do tutorial de recursos humanos para obter rapidamente a previsão de intenção e a extração de dados. Não é necessário marcar nenhum enunciado com as entidades predefinidas, porque a entidade é detectada automaticamente.

Os modelos predefinidos (domínios, intenções e entidades) ajudam a criar o modelo rapidamente.

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar novo aplicativo
> * Adicionar intenções predefinidas 
> * Adicionar entidades predefinidas 
> * Treinar 
> * Publicar 
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Criar um novo aplicativo

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Adicionar intenções predefinidas para ajudar com as intenções de usuário comuns

O LUIS oferece várias intenções predefinidas para ajudar com intenções do comuns do usuário.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selecione **Adicionar intenção de domínio predefinida**. 

1. Pesquise `Utilities`. 

    [![Captura de tela da caixa de diálogo de intenções predefinidas com Utilitários na caixa de pesquisa](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Selecione as seguintes intenções e selecione **Concluído**: 

   * Utilities.Cancel
   * Utilities.Confirm
   * Utilities.Help
   * Utilities.StartOver
   * Utilities.Stop

     Essas intenções são úteis para determinar em que ponto da conversa o usuário está e o que ele está solicitando que seja feito. Essas intenções incluem o uso de entidades. Essas entidades são adicionadas automaticamente ao aplicativo LUIS: Number, ordinal e Utilities.DirectionalReference. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Adicionar as entidades predefinidas para ajudar com a extração de tipo de dados comuns

O LUIS fornece várias entidades predefinidas para extração de dados comuns. 

1. Selecione **Entidades** no menu de navegação à esquerda.

1. Selecione o botão **Adicionar entidade predefinida**.

1. Selecione as entidades a seguir na lista de entidades predefinidas e selecione **Pronto**:

   * **[PersonName](luis-reference-prebuilt-person.md)** 
   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

     Essas entidades ajudarão você a adicionar o reconhecimento de nome e local ao aplicativo cliente.

## <a name="add-example-utterances-to-the-none-intent"></a>Adicionar enunciados de exemplo à intenção None 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Treinar o aplicativo para que as alterações à intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo em que foi feito o treinamento possa ser consultado por meio do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de entidade e de intenção do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Acesse o fim da URL na barra de endereço do navegador e insira `I want to cancel my trip to Seattle to see Bob Smith`. O último parâmetro da cadeia de consulta é `q`, a declaração **query**. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    O resultado previu a intenção de Utilities.Cancel com 80% de confiança e extraiu os dados de nome de cidade e de pessoa. 


## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

Saiba mais sobre modelos predefinidos:

* [Domínios predefinidos](luis-reference-prebuilt-domains.md): esses são domínios comuns que reduzem a criação de aplicativo LUIS geral
* Intenções predefinidas: essas são as intenções individuais dos domínios comuns. Você pode adicionar intenções individualmente, em vez de adicionar o domínio inteiro.
* [Entidades predefinidas](luis-prebuilt-entities.md): esses são tipos de dados comuns úteis para a maioria dos aplicativos do LUIS.

Saiba mais sobre como trabalhar com seu aplicativo do LUIS:

* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Próximas etapas

Adicionando entidades e intenções pré-criadas, o aplicativo cliente pode determinar as intenções comuns do usuário e extrair os tipos de dados comuns.  

> [!div class="nextstepaction"]
> [Saiba como adicionar uma entidade de expressão regular](luis-quickstart-intents-regex-entity.md)

