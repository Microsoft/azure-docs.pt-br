---
title: Como executar um teste em lote-LUIS
titleSuffix: Azure Cognitive Services
description: Use os conjuntos de testes em lote do KUIS (Reconhecimento vocal) para localizar enunciados com intenções e entidades incorretas.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/29/2020
ms.openlocfilehash: b297330f3562babf9e83d36934827f7b92d5ea35
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787005"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Testes em lote com um conjunto de exemplos de enunciados

O teste em lotes valida sua versão treinada ativa para medir sua precisão de previsão. Um teste em lote ajuda a exibir a precisão de cada tentativa e entidade em sua versão ativa. Examine os resultados do teste em lotes para tomar as medidas apropriadas para melhorar a precisão, como adicionar mais exemplos de declarações a uma intenção se seu aplicativo freqüentemente falhar ao identificar a intenção correta ou rotular entidades dentro do expressão.

## <a name="group-data-for-batch-test"></a>Agrupar dados para o teste em lote

É importante que as declarações usadas para o teste em lote sejam novas no LUIS. Se você tiver um conjunto de dados de declarações, divida o declarações em três conjuntos: exemplo declarações adicionado a uma intenção, declarações recebido do ponto de extremidade publicado e declarações usado para testar o lote LUIS depois de ser treinado.

O arquivo JSON de lote que você usa deve incluir declarações com entidades de aprendizado de máquina de nível superior rotuladas, incluindo a posição inicial e final. Os enunciados não devem fazer parte dos exemplos já existentes no aplicativo. Eles devem ser enunciados dos quais você deseja prever positivamente a intenção e as entidades.

Você pode separar os testes por intenção e/ou entidade ou ter todos os testes (até 1.000 enunciados) no mesmo arquivo. 

### <a name="common-errors-importing-a-batch"></a>Erros comuns ao importar um lote

Se você encontrar erros ao carregar o arquivo em lotes para o LUIS, verifique os seguintes problemas comuns:

* Mais de 1.000 declarações em um arquivo em lotes
* Um objeto JSON de declaração que não tem uma propriedade de entidades. A propriedade pode ser uma matriz vazia.
* Palavra(s) rotulada(s) em várias entidades
* Rótulos de entidade iniciando ou terminando em um espaço.

## <a name="fixing-batch-errors"></a>Corrigindo erros em lote

Se houver erros no teste em lote, você poderá adicionar mais declarações a uma intenção e/ou rotular mais declarações com a entidade para ajudar o LUIS a diferenciar as intenções. Se você adicionou declarações, rotulou e ainda recebe erros de previsão no teste em lote, considere adicionar um recurso de [lista de frases](luis-concept-feature.md) com um vocabulário específico do domínio para ajudar o LUIS a aprender mais rápido.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Teste em lotes usando o portal do LUIS 

### <a name="import-and-train-an-example-app"></a>Importar e treinar um aplicativo de exemplo

Importe um aplicativo que recebe um pedido de pizza, como `1 pepperoni pizza on thin crust`.

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true).

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Selecione a seta ao lado de **novo aplicativo** e clique em **importar como JSON** para importar o JSON em um novo aplicativo. Nomeie o aplicativo `Pizza app` .


1. Selecione **Treinar** no canto superior direito da navegação para treinar o aplicativo.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Arquivo de teste em lotes

O JSON de exemplo inclui um enunciado com uma entidade rotulada para ilustrar a aparência de um arquivo de teste. Em seus testes, você deverá ter muitos enunciados com a intenção correta e a entidade de aprendizado de máquina rotulada.

1. Crie `pizza-with-machine-learned-entity-test.json` em um editor de texto ou [baixe-o](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true).

2. No arquivo em lotes formatado em JSON, adicione um enunciado com a **Intenção** que deseja prever no teste.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Executar o lote

1. Selecione **Testar** na barra de navegação superior.

2. Selecione **Painel de teste do lote** no painel direito.

    ![Link de teste de lote](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Selecione **Importar**. Na caixa de diálogo exibida, selecione **escolher arquivo** e localize um arquivo JSON com o formato JSON correto que não contenha *mais de 1.000* declarações para testar.

    Erros de importação são relatados em uma barra de notificação vermelha na parte superior do navegador. Quando uma importação tem erros, nenhum conjunto de dados é criado. Para obter mais informações, confira [Erros comuns](#common-errors-importing-a-batch).

4. Escolha o local do arquivo do `pizza-with-machine-learned-entity-test.json`.

5. Nomeie o conjunto de dados `pizza test` e selecione **Concluído**.

6. Selecione o botão **Executar**. Após a execução do teste em lote, selecione **Ver resultados**. 

    > [!TIP]
    > * A seleção de **Download** baixará o mesmo arquivo que você carregou.
    > * Se você vir o teste de lote com falha, pelo menos uma intenção de expressão não correspondeu à previsão.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Examinar as intenções dos resultados do lote

Para examinar os resultados de teste de lote, selecione **Ver resultados**. Os resultados de teste mostram graficamente como os enunciados de teste foram previstos em relação à versão ativa.

O gráfico do lote exibe quatro quadrantes dos resultados. À direita do gráfico, há um filtro. O filtro contém intenções e entidades. Ao selecionar uma [seção do gráfico](#review-batch-results-for-intents) ou um ponto dentro do gráfico, os enunciados associados são exibidos abaixo do gráfico.

Ao passar o mouse sobre o gráfico, um botão de rolagem do mouse pode ampliar ou reduzir a exibição no gráfico. Isso é útil quando há muitos pontos no gráfico agrupados em conjunto.

O gráfico está em quatro quadrantes, com duas das seções exibidas em vermelho.

1. Selecione a intenção **ModifyOrder** na lista de filtros. O enunciado é previsto como um **verdadeiro positivo**, o que significa que o enunciado correspondeu com êxito à previsão positiva listada no arquivo em lotes.

    > [!div class="mx-imgBorder"]
    > ![O enunciado correspondeu com êxito à previsão positiva](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    As marcas de seleção verdes na lista de filtros também indicam o sucesso do teste para cada intenção. Todas as outras intenções são listadas com uma pontuação positiva de 1/1, porque o enunciado foi testado em relação a cada intenção, como um teste negativo para as intenções não listadas no teste em lotes.

1. Selecione a intenção **Confirmação**. Essa intenção não está listada no teste em lotes e, portanto, esse é um teste negativo do enunciado listado no teste em lotes.

    > [!div class="mx-imgBorder"]
    > ![Enunciado previsto negativo com êxito para a intenção não listada no arquivo em lotes](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    O teste negativo foi bem-sucedido, conforme observado com o texto verde no filtro e na grade.

### <a name="review-batch-test-results-for-entities"></a>Examinar as entidades nos resultados de teste em lotes

A entidade ModifyOrder, como uma entidade de máquina com subentidades, será exibida se a entidade de nível superior corresponder e como as subentidades são previstas.

1. Selecione a entidade **ModifyOrder** na lista de filtros e, em seguida, selecione o círculo na grade.

1. A previsão de entidade é exibida abaixo do gráfico. A exibição inclui linhas sólidas para previsões que correspondem à expectativa e linhas pontilhadas para previsões que não correspondem à expectativa.

    > [!div class="mx-imgBorder"]
    > ![Entidade pai prevista com êxito no arquivo em lotes](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtrar resultados do gráfico

Para filtrar o gráfico por uma intenção ou entidade específica, selecione a intenção ou entidade no painel de filtragem direito. Os pontos de dados e sua distribuição são atualizados no grafo de acordo com sua seleção.

![Resultado do teste de lote visualizado](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Exemplos de resultados do gráfico

O gráfico no portal do LUIS, você pode executar as seguintes ações:
 
#### <a name="view-single-point-utterance-data"></a>Exibir dados de declaração de ponto único

No gráfico, passe o mouse sobre um ponto de dados para ver a pontuação de certeza de sua previsão. Selecione um ponto de dados para recuperar sua declaração correspondente na lista de declarações na parte inferior da página.

![Declaração selecionada](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Exibir dados de seção

No gráfico de quatro seções, selecione o nome da seção, como **Falso Positivo** na parte superior direita do gráfico. Embaixo do gráfico, todas as declarações nessa seção são exibidas embaixo do gráfico em uma lista.

![Declarações selecionadas por seção](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Nesta imagem anterior, a declaração `switch on` é rotulada com a intenção TurnAllOn, mas recebeu a previsão de Nenhuma intenção. Essa é uma indicação de que a intenção TurnAllOn precisa de mais declarações de exemplo para criar a previsão esperada.

As duas seções do gráfico em vermelho indicam declarações que não corresponderam à previsão esperada. Elas indicam declarações de que o LUIS precisa de mais treinamento.

As duas seções do gráfico em verde corresponderam à previsão esperada.

## <a name="batch-testing-using-the-rest-api"></a>Teste de lote usando a API REST 

O LUIS permite que você teste em lote usando o portal do LUIS e a API REST. Os pontos de extremidade para a API REST estão listados abaixo. Para obter informações sobre testes em lotes usando o portal do LUIS, consulte [tutorial: conjuntos de dados de teste em lotes](). Use as URLs completas abaixo, substituindo os valores de espaço reservado por sua própria chave de previsão LUIS e ponto de extremidade. 

Lembre-se de adicionar a chave LUIS ao `Ocp-Apim-Subscription-Key` no cabeçalho e definir `Content-Type` como `application/json` .

### <a name="start-a-batch-test"></a>Iniciar um teste em lote

Inicie um teste em lotes usando uma ID de versão do aplicativo ou um slot de publicação. Envie uma solicitação **post** para um dos seguintes formatos de ponto de extremidade. Inclua o arquivo em lotes no corpo da solicitação.

Slot de publicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

ID da versão do aplicativo
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Esses pontos de extremidade retornarão uma ID de operação que será usada para verificar o status e obter resultados. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Obter o status de um teste em lotes em andamento

Use a ID da operação do teste em lotes que você iniciou para obter seu status dos seguintes formatos de ponto de extremidade: 

Slot de publicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

ID da versão do aplicativo
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Obter os resultados de um teste em lotes

Use a ID da operação do teste em lotes que você iniciou para obter seus resultados dos seguintes formatos de ponto de extremidade: 

Slot de publicação
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

ID da versão do aplicativo
* `<YOUR-PREDICTION-ENDPOINT>/luis/prediction/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Arquivo em lotes de declarações

Envie um arquivo em lotes de declarações, conhecido como um *conjunto de dados*, para teste em lotes. O conjunto de dados é um arquivo formatado em JSON que contém um máximo de 1.000 rotulado como declarações. Você pode testar até 10 conjuntos de dados em um aplicativo. Se você precisar testar mais, exclua um conjunto de dados e, em seguida, adicione um novo. Todas as entidades personalizadas no modelo aparecem no filtro de entidades de teste de lote, mesmo se não houver nenhuma entidade correspondente nos dados do arquivo em lotes.

O arquivo em lote consiste em declarações. Cada expressão deve ter uma previsão de intenção esperada junto com as [entidades de aprendizado de máquina](luis-concept-entity-types.md#types-of-entities) que você espera que sejam detectadas.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Modelo de sintaxe em lote para intenções com entidades

Use o modelo a seguir para iniciar o arquivo em lotes:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

O arquivo em lotes usa as propriedades **startPos** e **endPos** para observar o início e o fim de uma entidade. Os valores são baseados em zero e não devem iniciar ou terminar em um espaço. Isso é diferente dos logs de consulta, que usam as propriedades startIndex e endIndex.

Se você não deseja testar entidades, inclua a propriedade `entities` e defina o valor como uma matriz vazia, `[]`.

### <a name="rest-api-batch-test-results"></a>Resultados do teste de lote da API REST

Há vários objetos retornados pela API:

* Informações sobre os modelos de intenções e entidades, como Precision, Recall e F-score.
* Informações sobre os modelos de entidades, como Precision, Recall e F-score) para cada entidade 
  * Usando o `verbose` sinalizador, você pode obter mais informações sobre a entidade, como `entityTextFScore` e `entityTypeFScore` .
* Declarações fornecido com os nomes de intenção previstos e rotulados
* Uma lista de entidades positivas falsas e uma lista de entidades negativas falsas.

## <a name="next-steps"></a>Próximas etapas

Se o teste indica que seu aplicativo LUIS não reconhece as intenções e entidades corretas, é possível trabalhar para melhorar o desempenho do seu aplicativo LUIS rotulando mais declarações ou adicionando recursos.

* [Rotular enunciados sugeridos com o LUIS](luis-how-to-review-endpoint-utterances.md)
* [Usar recursos para melhorar o desempenho do aplicativo de LUIS](luis-how-to-add-features.md)
