---
title: 'Tutorial: Prever intenções – LUIS'
description: Crie um aplicativo personalizado que prevê a intenção de um usuário com base no enunciado (texto) neste tutorial.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 04ba7dc79deef2dbad12c3f65a324201d3e9f598
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612755"
---
# <a name="tutorial-build-a-luis-app-to-determine-user-intentions"></a>Tutorial: Criar um aplicativo LUIS para determinar as intenções do usuário

Neste tutorial, você criará um aplicativo personalizado que prevê a intenção de um usuário com base no enunciado (texto).

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um novo aplicativo
> * Criar intenções
> * Adicionar enunciados de exemplo
> * Treinar o aplicativo
> * Publicar aplicativo
> * Obter a previsão de intenção do ponto de extremidade

## <a name="user-intentions-as-intents"></a>Intenções do usuário

A finalidade do aplicativo é determinar a intenção do texto em linguagem natural da conversa:

`I'd like to order a veggie pizza with a salad on the side.`

Essas intenções são categorizadas em **Intenções**.

|Intencional|Finalidade|
|--|--|
|`ModifyOrder`|Determinar o pedido de pizza do usuário.|
|`Greeting`|Iniciar conversa de bot.|
|`ConfirmOrder`|Confirmar o pedido de pizza.|
|`None`|Determine se o usuário está perguntando algo que o aplicativo LUIS não deve responder. Essa intenção é fornecida como parte da criação do aplicativo e não pode ser excluída. |

## <a name="create-a-new-app"></a>Criar um novo aplicativo

[!INCLUDE [Follow these steps to create a new LUIS app](includes/create-pizza-app.md)]

## <a name="create-a-new-intent"></a>Criar uma nova intenção

Uma intenção é usada para classificar enunciados do usuário com base na intenção dele, determinada com base no texto do idioma natural.

Para classificar um enunciado, a intenção precisa de exemplos de enunciados classificados com a intenção.

1. Na seção **Criar**, na página **Intenções**, selecione **+ Criar** para criar uma intenção. Insira o nome da nova intenção, `OrderPizza`, depois selecione **Concluído**.

    A intenção `OrderPizza` é prevista quando um usuário quer pedir uma pizza.

1. Adicione vários enunciados de exemplo para esta intenção que se espera que o usuário solicite:

    |Enunciados de exemplo de `OrderPizza`|
    |--|
    |`can i get a pepperoni pizza and a can of coke please`|
    |`can i get a small pizza with onions peppers and olives`|
    |`delivery for a small pepperoni pizza`|
    |`pickup a cheddar cheese pizza large with extra anchovies`|
    |`i need 2 large cheese pizzas 6 large pepperoni pizzas and 1 large supreme pizza`|
    |`Order a pizza for me`|

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição de enunciados de exemplo no portal do LUIS na página Intenção](media/tutorial-intents-only/add-example-utterances-for-pizza-order.png)

    Ao fornecer _enunciados de exemplo_, você está treinando o LUIS sobre quais tipos de enunciados devem ser previstos para essa intenção. Esses são exemplos positivos. Os enunciados em todas as outras intenções são tratados como exemplos negativos para essa intenção.

    [!INCLUDE [Do not use too few utterances](includes/do-not-use-too-few-utterances.md)]

## <a name="create-remaining-intents"></a>Criar as intenções restantes

1. Crie a intenção `Greeting` e adicione os enunciados de exemplo a seguir. Essa é a intenção de determinar se um usuário está começando uma conversa para um novo pedido de pizza.

    |Enunciados de exemplo de `Greeting`|
    |--|
    |`Hi`|
    |`Hello`|
    |`Hey`|
    |`Start`|
    |`Begin`|

1. Crie a intenção `Confirm` e adicione os enunciados de exemplo a seguir. Essa é a intenção de determinar se um usuário finalizou o pedido e aceita os detalhes do pedido.

    |Enunciados de exemplo de `Confirm`|
    |--|
    |`Go ahead`|
    |`ok`|
    |`Yes`|
    |`Sure`|


## <a name="none-intent-example-utterances"></a>Exemplos de enunciado da intenção Nenhum

[!INCLUDE [Follow these steps to add the None intent to the app](includes/add-example-utterances-none-intent.md)]

## <a name="train-the-app"></a>Treinar o aplicativo

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app"></a>Publicar o aplicativo

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-prediction"></a>Obter a previsão de intenção

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá até o final da URL na barra de endereços e insira:

    `get a medium vegetarian pizza for delivery`

  
    O parâmetro de cadeia de consulta é `query`, o enunciado **consulta** é passado no URI. Esse enunciado não é o mesmo de nenhum dos enunciados de exemplo. Esse deve ser um bom teste para verificar se o LUIS aprende e prevê a intenção de `OrderPizza` como a principal intenção de pontuação.

    ```JSON
    {
        "query": "get a medium vegetarian pizza for delivery",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.6488959
                },
                "None": {
                    "score": 0.139966831
                },
                "Confirm": {
                    "score": 0.00736504374
                },
                "Greeting": {
                    "score": 0.003970454
                }
            },
            "entities": {}
        }
    }
    ```

    A matriz de entidades está vazia porque esse aplicativo no momento não tem nenhuma entidade (unidade de dados dentro do enunciado a ser extraído).

    O resultado em JSON identifica a intenção com a maior pontuação como a propriedade **`prediction.topIntent`** . Todas as pontuações estão entre 1 e 0, com a melhor pontuação mais próxima a 1.

1. Altere o parâmetro de **consulta** da URL para ter como destino a intenção **Saudação**:

    `Howdy`

    Isso não é exatamente o mesmo que um enunciado de exemplo, portanto, é um bom teste para ver se o LUIS pode aprender o que deve ser previsto com essa intenção.

    ```json
    {
        "query": "howdy",
        "prediction": {
            "topIntent": "Greeting",
            "intents": {
                "Greeting": {
                    "score": 0.446016937
                },
                "Confirm": {
                    "score": 0.2390079
                },
                "None": {
                    "score": 0.09119555
                },
                "OrderPizza": {
                    "score": 0.00109590159
                }
            },
            "entities": {}
        }
    }
    ```

    Essa previsão tem uma pontuação de confiança de 44%. Para aumentar a pontuação de confiança, adicione entre 15 e 30 enunciados de exemplo.

## <a name="client-application-next-steps"></a>Próximas etapas do aplicativo cliente

Este tutorial criou o aplicativo do LUIS, criou intenções, adicionou enunciados de exemplo a cada intenção, adicionou enunciados de exemplo à intenção Nenhuma, além de treiná-la, publicá-la e testá-la no ponto de extremidade. Essas são as etapas básicas da criação de um modelo LUIS.

Depois que o LUIS retornar a resposta JSON, ele terá concluído esta solicitação. O LUIS não fornece respostas para os enunciados do usuário; só identifica qual tipo de informação está sendo solicitado em idioma natural. O acompanhamento de conversação é fornecido pelo aplicativo cliente como um Bot do Azure.


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tipos de entidades](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)
* [Bot do Azure](/azure/bot-service/)


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Adicionar uma entidade divisível a este aplicativo](tutorial-machine-learned-entity.md)