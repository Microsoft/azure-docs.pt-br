---
title: 'Tutorial: Examinando enunciados de ponto de extremidade – LUIS'
description: Neste tutorial, aprimore as previsões de aplicativo verificando ou corrigindo os enunciados recebidos pelo ponto de extremidade HTTP do LUIS dos quais o LUIS não tem certeza. Alguns enunciados podem ser verificados quanto à intenção e outros quanto à entidade.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/02/2020
ms.openlocfilehash: b8f8fa2cd3c9c22187bb95c55d9de2abb2e8caec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324630"
---
# <a name="tutorial-fix-unsure-predictions-by-reviewing-endpoint-utterances"></a>Tutorial: Corrigir previsões incertas examinando os enunciados de ponto de extremidade
Neste tutorial, melhore as previsões de aplicativo verificando ou corrigindo os enunciados recebidos pelo ponto de extremidade HTTP do LUIS sobre os quais o LUIS não tem certeza. Você deve examinar os enunciados de ponto de extremidade como uma parte regular da sua manutenção agendada do LUIS.

Esse processo de revisão permite que o LUIS aprenda seu domínio de aplicativo. O LUIS seleciona os enunciados que aparecem na lista de análise. Esta lista é:

* Específica para o aplicativo.
* Destina-se a melhorar a precisão da previsão do aplicativo.
* Deve ser revisada regularmente.

Ao revisar os enunciados de ponto de extremidade, você verifica ou corrige a intenção prevista do enunciado.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Examinar declarações de ponto de extremidade
> * Treinar e publicar o aplicativo
> * Consulte ponto de extremidade do aplicativo para ver a resposta JSON do LUIS

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="download-json-file-for-app"></a>Baixar arquivo JSON para aplicativo

Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

## <a name="import-json-file-for-app"></a>Importar arquivo JSON para aplicativo


1. No [portal do LUIS](https://www.luis.ai), na página **Meus aplicativos**, selecione **+ Novo aplicativo para conversa**, em seguida, **Importar como JSON**. Localize o arquivo JSON salvo na etapa anterior. Você não precisa alterar o nome do aplicativo. Selecione **Concluído**

1. Selecione **Compilar** e, em seguida, **Intenções** para ver as intenções, que são os principais blocos de construção de um aplicativo LUIS.

    :::image type="content" source="media/luis-tutorial-review-endpoint-utterances/initial-intents-in-app.png" alt-text="Altere da página Versões para a página Intenções.":::

## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treinar o aplicativo para aplicar as alterações de entidade ao aplicativo

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicar o aplicativo para acessá-lo por meio do ponto de extremidade HTTP

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="add-utterances-at-the-endpoint"></a>Adicionar enunciados no ponto de extremidade

Neste aplicativo, você tem intenções e entidades, mas não tem nenhum uso de ponto de extremidade. Esse uso de ponto de extremidade é necessário para melhorar o aplicativo com a análise do enunciado do ponto de extremidade.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL na barra de endereços e substitua _YOUR_QUERY_HERE_ pelos enunciados da tabela a seguir. Para cada enunciado, envie o enunciado e obtenha o resultado. Em seguida, substitua o enunciado no final pelo próximo enunciado.

    |Enunciado do ponto de extremidade|Intenção alinhada|
    |--|--|
    |`I'm looking for a job with Natural Language Processing`|`GetJobInformation`|
    |`I want to cancel on March 3`|`Utilities.Cancel`|
    |`When were HRF-123456 and hrf-234567 published in the last year?`|`FindForm`|
    |`shift 123-45-6789 from Z-1242 to T-54672`|`MoveEmployee`|
    |`Please relocation jill-jones@mycompany.com from x-2345 to g-23456`|`MoveEmployee`|
    |`Here is my c.v. for the programmer job`|`ApplyForJob`|
    |`This is the lead welder paperwork.`|`ApplyForJob`|
    |`does form hrf-123456 cover the new dental benefits and medical plan`|`FindForm`|
    |`Jill Jones work with the media team on the public portal was amazing`|`EmployeeFeedback`|

    Há um único pool de enunciados para analisar, independentemente de qual versão está ativamente editando ou qual versão do aplicativo foi publicada no ponto de extremidade.

## <a name="review-endpoint-utterances"></a>Examinar declarações de ponto de extremidade

Examine os enunciados do ponto de extremidade para uma intenção corretamente alinhada. Embora haja um único pool de declarações para examinar em todas as versões, o processo de alinhamento correto da intenção adiciona o enunciado de exemplo somente ao _modelo ativo_ atual.

1. Na seção **Build** do portal, selecione **Examinar enunciados do ponto de extremidade** no painel de navegação esquerdo. A lista é filtrada para a intenção **ApplyForJob**.

    :::image type="content" source="./media/luis-tutorial-review-endpoint-utterances/review-endpoint-utterances-with-entity-view.png" alt-text="Altere da página Versões para a página Intenções.":::

    Este enunciado, `I'm looking for a job with Natural Language Processing`, não está na intenção correta, _GetJobInformation_. Ele foi previsto incorretamente como _ApplyForJob_ devido à similaridade de nomes e verbos do de trabalho nas duas intenções.

1.  Para alinhar esse enunciado, selecione a **Intenção Alinhada** correta de `GetJobInformation`. Adicione o enunciado alterado ao aplicativo selecionando a marca de seleção.

    Examine os enunciados restantes nesta intenção, corrigindo a intenção alinhada conforme necessário. Use a tabela de enunciado inicial neste tutorial para exibir a intenção alinhada.

    A lista **Examinar enunciados de ponto de extremidade** não deve mais ter esses enunciados corrigidos. Se mais enunciados aparecerem, continue trabalhando na lista, corrigindo as intenções alinhadas até que a lista esteja vazia.

    Qualquer correção da rotulação de entidade é feita depois que a intenção é alinhada, na página Detalhes da intenção.

1. Treine e publique o aplicativo novamente.

## <a name="get-intent-prediction-from-endpoint"></a>Obter a previsão de intenção do ponto de extremidade

Para verificar se os enunciados de exemplo alinhados corretamente melhoraram a previsão do aplicativo, tente um enunciado próximo ao enunciado corrigido.

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL na barra de endereços e substitua _YOUR_QUERY_HERE_ por `Are there any natural language processing jobs in my department right now?`.

   ```json
    {
        "query": "Are there any natural language processing jobs in my department right now?",
        "prediction": {
            "topIntent": "GetJobInformation",
            "intents": {
                "GetJobInformation": {
                    "score": 0.901367366
                },
                "ApplyForJob": {
                    "score": 0.0307973567
                },
                "EmployeeFeedback": {
                    "score": 0.0296942145
                },
                "MoveEmployee": {
                    "score": 0.00739785144
                },
                "FindForm": {
                    "score": 0.00449316856
                },
                "Utilities.Stop": {
                    "score": 0.00417657848
                },
                "Utilities.StartOver": {
                    "score": 0.00407167152
                },
                "Utilities.Help": {
                    "score": 0.003662492
                },
                "None": {
                    "score": 0.00335733569
                },
                "Utilities.Cancel": {
                    "score": 0.002225436
                },
                "Utilities.Confirm": {
                    "score": 0.00107437756
                }
            },
            "entities": {
                "keyPhrase": [
                    "natural language processing jobs",
                    "department"
                ],
                "datetimeV2": [
                    {
                        "type": "datetime",
                        "values": [
                            {
                                "timex": "PRESENT_REF",
                                "resolution": [
                                    {
                                        "value": "2020-07-02 21:45:50"
                                    }
                                ]
                            }
                        ]
                    }
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "natural language processing jobs",
                            "startIndex": 14,
                            "length": 32,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "builtin.keyPhrase",
                            "text": "department",
                            "startIndex": 53,
                            "length": 10,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "datetimeV2": [
                        {
                            "type": "builtin.datetimeV2.datetime",
                            "text": "right now",
                            "startIndex": 64,
                            "length": 9,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
   ```

   Agora que os enunciados de incerteza estão alinhados corretamente, a intenção correta foi prevista com uma **pontuação alta**.

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>A revisão pode ser substituída pela adição de mais enunciados?
Você deve estar imaginando por que não adicionar mais exemplos de enunciado. Qual é o objetivo de examinar os enunciados de ponto de extremidade? Em um aplicativo do mundo real LUIS, os enunciados de ponto de extremidade são de usuários com um arranjo e escolha de palavras que você ainda não usou. Se você tivesse usado o mesmo arranjo e escolha de palavras, a previsão original teria tido um percentual mais alto.

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Por que a intenção mais bem pontuada está na lista de enunciados?
Alguns enunciados de ponto de extremidade terão uma pontuação de previsão alta na lista de análise. Ainda assim é necessário examinar esses enunciados. Eles estão na lista porque a intenção com a segunda pontuação mais alta tinha uma pontuação muito próxima da intenção com a maior pontuação. Você deseja cerca de 15% de diferença entre as duas primeiras intenções.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você analisou enunciados enviados no ponto de extremidade sobre os quais o LUIS não tinha certeza. Depois que esses enunciados forem verificados e movidos para as intenções corretas como enunciados de exemplo, o LUIS melhorará a precisão da previsão.

> [!div class="nextstepaction"]
> [Saiba como usar padrões](luis-tutorial-pattern.md)
