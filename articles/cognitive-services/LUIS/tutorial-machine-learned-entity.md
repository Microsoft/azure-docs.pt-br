---
title: 'Tutorial: extrair dados estruturados com entidade de aprendizado de máquina – LUIS'
description: Extrair dados estruturados de um enunciado usando a entidade de aprendizado de máquina. Para aumentar a precisão de extração, adicione subentidades com recursos.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/08/2020
ms.openlocfilehash: aa2783abe86b90a907510dec8d7eb40162820ad5
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025252"
---
# <a name="tutorial-extract-structured-data-from-user-utterance-with-machine-learning-entities-in-language-understanding-luis"></a>Tutorial: Extrair dados estruturados do enunciado do usuário usando entidades de aprendizado de máquina no LUIS (Reconhecimento vocal)

Neste tutorial, extraia dados estruturados de um enunciado usando a entidade de aprendizado de máquina.

A entidade de aprendizado de máquina dá suporte ao [conceito de decomposição de modelo](luis-concept-model.md#v3-authoring-model-decomposition) por meio do fornecimento de [recursos](luis-concept-feature.md) às entidades de subentidade.

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Entidade de aprendizado de máquina
> * Adicionar subentidade e recurso
> * Treinar, testar e publicar aplicativo
> * Obter uma previsão de entidade do ponto de extremidade

[!INCLUDE [LUIS Free account](includes/quickstart-tutorial-use-free-starter-key.md)]


## <a name="why-use-a-machine-learning-entity"></a>Por que usar uma entidade de aprendizado de máquina?

Este tutorial adiciona uma entidade de aprendizado de máquina para extrair dados do enunciado de um usuário.

A entidade define os dados a serem extraídos do enunciado. Isso inclui fornecer aos dados um nome, um tipo (se possível), qualquer resolução dos dados, em caso de ambiguidade, bem como o texto exato que compõe os dados.

Para definir os dados, você precisa:
* Criar a entidade
* Rotule o texto, nos exemplos de enunciado, que representa a entidade. Esses exemplos rotulados ensinam ao LUIS qual é a entidade e em que local ela pode ser encontrada em um enunciado.

## <a name="entity-decomposability-is-important"></a>A decomponibilidade da entidade é importante

A decomponibilidade da entidade é importante para a previsão da intenção e a extração de dados com a entidade.

Comece com uma entidade de aprendizado de máquina, que é a entidade inicial e de nível superior para extração de dados. Em seguida, decomponha a entidade em subentidades.

Embora você possa não saber o nível de detalhes que deseja que a entidade tenha ao começar seu aplicativo, uma melhor prática é começar com uma entidade de aprendizado de máquina e, em seguida, decompô-la com subentidades à medida que o aplicativo amadurecer.

Neste tutorial, você criará uma entidade de machine learning para representar um pedido em um aplicativo de pizza. A entidade extrairá o texto relacionado ao pedido, o tamanho de extração e a quantidade.

Um enunciado para `Please deliver one large cheese pizza to me` deve extrair `one large cheese pizza` como o pedido. Depois, extrair `1` para quantidade e `large` para tamanho.

## <a name="download-json-file-for-app"></a>Baixar arquivo JSON para aplicativo

Baixe e salve o [arquivo JSON do aplicativo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-language-understanding/master/documentation-samples/tutorials/machine-learned-entity/pizza-intents-only.json).

## <a name="import-json-file-for-app"></a>Importar arquivo JSON para aplicativo

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="create-machine-learned-entity"></a>Criar entidade de aprendizado de máquina

Para extrair os detalhes sobre um pedido de pizza, crie uma entidade `Order` de aprendizado de máquina de nível superior.

1. Na página **Intenções**, selecione a intenção **OrderPizza**.

1. Na lista de enunciados de exemplo, selecione o enunciado a seguir.

    |Enunciado de exemplo de pedido|
    |--|
    |`pickup a cheddar cheese pizza large with extra anchovies`|

    Clique e arraste o cursor sobre o texto do primeiro exemplo de intenção. no menu exibido, insira o nome da entidade como `Order`. Em seguida, selecione `Order Create new entity` na lista.

    ![Rotular o início e o fim do texto do pedido concluído](media/tutorial-machine-learned-entity/mark-complete-order.png)

    > [!NOTE]
    > Uma entidade nem sempre será o enunciado inteiro. Nesse caso específico, `pickup` indica como o pedido deve ser recebido. Do ponto de vista conceitual, `pickup` deve fazer parte da entidade rotulada para o pedido.

1. Na caixa **Escolher um tipo de entidade**, selecione **Adicionar Estrutura** e, em seguida, selecione **Avançar**. A estrutura é necessária para adicionar subentidades, como tamanho e quantidade.

    ![A captura de tela mostra a janela Escolher um tipo de entidade com a opção Adicionar estrutura marcada.](media/tutorial-machine-learned-entity/add-structure-to-entity.png)

1. Na caixa **Adicionar subentidades (opcional)** , selecione **+** na linha `Order`, em seguida, adicione `Size` e `Quantity` como subentidades e selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![A captura de tela mostra a janela Adicionar subentidades (opcional) com subentidades realçadas.](media/tutorial-machine-learned-entity/add-subentities-when-creating-machine-learned-entity.png)

## <a name="edit-subentities-to-improve-extraction"></a>Editar subentidades para aprimorar a extração

As etapas anteriores criam a entidade e a subentidade. Para aprimorar a extração, adicione recursos às subentidades.

### <a name="improve-size-extraction-with-phrase-list"></a>Aprimorar a extração de tamanho com a lista de frases

1. Selecione **Entidades** no menu esquerdo e, em seguida, selecione a entidade **Ordem**.

1. Na guia **Esquema e recursos**, selecione a subentidade **Tamanho** e, em seguida, selecione **+ Adicionar recurso**.

1. Selecione **Criar lista de frases** no menu suspenso.

1. Na caixa **Criar nova lista de frases**, insira o nome `SizePhraselist` e, em seguida, insira os valores `small`, `medium` e `large`. Quando a caixa **Sugestões** for preenchida, selecione `extra large` e `xl`. Selecione **Criar** para criar a lista de frases.

    Essa lista de frases ajuda a subentidade `Size` a localizar palavras relacionadas ao tamanho, fornecendo a ela palavras de exemplo. Essa lista de frases não precisa incluir todas as palavras de tamanho, mas deve incluir palavras que precisam indicar o tamanho.

### <a name="add-sizelist-entity"></a>Adicionar entidade SizeList

Adicionar uma lista de tamanhos conhecidos que o aplicativo cliente reconhece também ajudará na extração.

1. Selecione **Entidades** no menu esquerdo e, em seguida, selecione **+ Criar**.

1. Defina o nome da entidade como `SizeListentity` e defina o Tipo como **Lista** para que seja fácil identificá-la quando comparada à `SizePhraselist` criada na seção anterior.

1. Adicione os tamanhos que o aplicativo cliente espera: `Small`, `Medium`, `Large` e `XLarge`, em seguida, adicione sinônimos para cada um. Os sinônimos devem ser os termos que um usuário insere no chatbot. A entidade é extraída com uma entidade de lista quando corresponde exatamente ao valor normalizado ou aos sinônimos.

    |Valor normalizado|Sinônimos|
    |--|--|
    |Pequena|sm, sml, tiny, smallest|
    |Médio|md, mdm, regular, average, middle|
    |grande|lg, lrg, big|
    |XLarge|xl, biggest, giant|


    > [!div class="mx-imgBorder"]
    > ![A captura de tela mostra a janela SizeList e lista os itens com XLarge selecionado.](media/tutorial-machine-learned-entity/size-list-entity-with-synonyms.png)

### <a name="add-feature-of-sizelist-entity"></a>Adicionar recurso da entidade SizeList

1. Selecione **Entidades** do menu esquerdo para retornar à lista de entidades.

1. Selecione **Ordem** na lista de entidades.

1. Na guia **Esquema e recursos**, selecione a entidade **Tamanho** e, em seguida, selecione **+ Adicionar recurso**.

1. Selecione **@ SizeListentity** na lista suspensa.

<a name="add-prebuilt-number-entity-to-app"></a>

### <a name="add-prebuilt-number-entity"></a>Adicionar uma entidade de número predefinida

Adicionar uma entidade de número predefinida também ajudará na extração.

1. Selecione **Entidades** no menu à esquerda e, em seguida, selecione **Adicionar entidade predefinida**.

1. Selecione **Número** na lista e selecione **Concluído**.

1. Selecione **Entidades** do menu esquerdo para retornar à lista de entidades.

### <a name="add-feature-of-prebuilt-number-entity"></a>Adicionar recurso de entidade de número predefinida

1. Selecione **Ordem** na lista de entidades.

1. Na guia **Esquema e recursos**, selecione a entidade **Quantidade** e, em seguida, selecione **+ Adicionar recurso**.

1. Selecione **@ número** na lista suspensa.

<a name="create-subcomponent-entity-with-constraint-to-help-extract-data"></a>

## <a name="configure-required-features"></a>Configurar os recursos necessários

Na página de detalhes da entidade **Ordem**, selecione o asterisco, `*`, para o recurso **@ SizeList** e o recurso **@ number**. O asterisco aparece no mesmo rótulo que o nome do recurso.

> [!div class="mx-imgBorder"]
> ![A captura de tela mostra o recurso @SizeList com o asterisco e o aviso Obrigatório.](media/tutorial-machine-learned-entity/set-required-feature-on-subentity.png)

<a name="label-text-as-entities-in-example-utterances"></a>
<a name="label-example-utterance-to-teach-luis-about-the-entity"></a>

## <a name="label-example-utterances"></a>Rotular enunciados de exemplo

A entidade de aprendizado de máquina é criada e as subentidades têm recursos. Para concluir ao aprimoramento da extração, os enunciados de exemplo precisam ser rotulados com as subentidades.

1. Selecione **Intenções** no painel de navegação esquerdo e, em seguida, selecione a intenção **OrderPizza**.

1. Para abrir a **Paleta de Entidades**, selecione o símbolo **@** na barra de ferramentas contextual.

1. Selecione cada linha de entidade na paleta e, em seguida, use o cursor de paleta para selecionar a entidade em cada exemplo de enunciado. Quando você terminar, a lista de entidades deverá ser parecida com a imagem a seguir.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela parcial da configuração do recurso necessário](media/tutorial-machine-learned-entity/labeled-example-utterances-for-machine-learned-entity.png)

## <a name="train-the-app"></a>Treinar o aplicativo

Para treinar o aplicativo, selecione **Treinar**. O treinamento aplica as alterações, como as novas entidades e os enunciados rotulados, ao modelo ativo.

## <a name="add-a-new-example-utterance"></a>Adicionar um novo exemplo de enunciado

1. Após o treinamento, adicione um novo enunciado de exemplo à intenção `OrderPizza` para entender o nível de entendimento do LUIS da entidade de aprendizado de máquina.

    |Enunciado de exemplo de pedido|
    |--|
    |`I need a large pepperoni pizza`|

    A entidade superior geral, `Order`, é rotulada, e a subentidade `Size` também é rotulada com linhas pontilhadas.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela parcial do novo enunciado de exemplo previsto com a entidade](media/tutorial-machine-learned-entity/new-example-utterance-predicted-with-entity.png)

    A linha pontilhada indica a previsão com base no aplicativo treinado atual.

1. Para alterar a previsão para uma entidade rotulada, selecione a marca de seleção na mesma linha.

    > [!div class="mx-imgBorder"]
    > ![A captura de tela mostra um exemplo de enunciado com a marca de seleção realçada.](media/tutorial-machine-learned-entity/confirm-entity-prediction-for-new-example-utterance-added.png)

    Neste ponto, a entidade de aprendizado de máquina está funcionando porque pode encontrar a entidade em um novo enunciado de exemplo. Ao adicionar o enunciado de exemplo, se a entidade não for prevista corretamente, rotule a entidade e as subentidades. Se a entidade for prevista corretamente, você precisará confirmar as previsões.


## <a name="train-the-app-to-apply-the-entity-changes-to-the-app"></a>Treinar o aplicativo para aplicar as alterações de entidade ao aplicativo

Selecione **Treinar** para treinar o aplicativo com os novos enunciados.

Neste ponto, o pedido tem alguns detalhes que podem ser extraídos (tamanho, quantidade e texto do pedido total). Há um refinamento ainda maior da entidade `Order`, como coberturas da pizza, tipo de borda e acompanhamentos. Cada um deles deve ser criado como subentidades da entidade `Order`.

## <a name="test-the-app-to-validate-the-changes"></a>Testar o aplicativo para validar as alterações

Teste o aplicativo usando o painel **Teste** interativo. Esse processo permite que você insira um novo enunciado e exiba os resultados da previsão para ver se o aplicativo ativo e treinado está funcionando corretamente. A previsão da intenção deve ser razoavelmente confiante (acima de 60%) e a extração de entidade deve selecionar, pelo menos, a entidade `Order`. Os detalhes da entidade de pedido podem estar ausentes porque esses poucos enunciados não são suficientes para tratar todos os casos.

1. Selecione **Testar** na navegação superior.
1. Insira o enunciado `2 small cheese pizzas for pickup` e selecione Enter. O modelo ativo previu a intenção correta com mais de 60% de confiança.


1. Selecione **Inspecionar** para ver as previsões de entidade.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela parcial da exibição das previsões de entidade no painel de teste interativo.](media/tutorial-machine-learned-entity/interactive-test-panel-with-first-utterance-and-entity-predictions.png)

<a name="publish-with-sentiment-analysis"></a>

## <a name="publish-the-app-to-access-it-from-the-http-endpoint"></a>Publicar o aplicativo para acessá-lo por meio do ponto de extremidade HTTP

Para receber uma previsão do LUIS em um chat bot ou em outro aplicativo cliente, publique o aplicativo no ponto de extremidade.

1. Selecione **Publicar** no painel de navegação superior direito.

    ![Captura de tela da publicação do LUIS para o botão do ponto de extremidade no menu à direita superior](./media/howto-publish/publish-button.png)

1. Selecione o slot de **Produção**, selecione **Alterar configurações**, escolha **Análise de Sentimento** e, em seguida, selecione **Concluído**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da publicação do LUIS para o ponto de extremidade](./media/tutorial-machine-learned-entity/publish-with-sentiment-analysis.png)

1. Selecione o link **Acessar as URLs do ponto de extremidade** na notificação para ir à página **Recursos do Azure**. As URLs do ponto de extremidade são listadas como a **Consulta de Exemplo**.

## <a name="get-intent-and-entity-prediction-from-http-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade HTTP

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL na barra de endereços e substitua _YOUR_QUERY_HERE_ pela mesma consulta que você inseriu no painel de teste interativo.

    `2 small cheese pizzas for pickup`

    O último parâmetro de querystring é `query`, o enunciado **consulta**.

    ```json
    {
        "query": "2 small cheese pizzas for pickup",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.7812769
                },
                "None": {
                    "score": 0.0314020254
                },
                "Confirm": {
                    "score": 0.009299271
                },
                "Greeting": {
                    "score": 0.007551549
                }
            },
            "entities": {
                "Order": [
                    {
                        "Size": [
                            "small"
                        ],
                        "Quantity": [
                            2
                        ]
                    }
                ]
            }
        },
        "sentimentAnalysis":{
            "label":"neutral",
            "score":0.98
       }
    }
    ```


[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial – intenções](./tutorial-intents-only.md)
* [Conceito – informações conceituais sobre entidades](luis-concept-entity-types.md)
* [Conceito – informações conceituais sobre recursos](luis-concept-feature.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, o aplicativo usa uma entidade de aprendizado de máquina para descobrir a intenção do enunciado de um usuário e extrair detalhes desse enunciado. O uso da entidade de aprendizado de máquina permite decompor os detalhes da entidade.

> [!div class="nextstepaction"]
> [Adicionar uma entidade de keyphrase predefinida](./luis-reference-prebuilt-keyphrase.md)