---
title: Tutorial de entidade composta-LUIS
titleSuffix: Azure Cognitive Services
description: Adicione uma entidade composta para agrupar dados extraídos de vários tipos em uma única entidade contida. Agrupando os dados, o aplicativo cliente poderá extrair com facilidade dados relacionados em diferentes tipos de dados.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 610a7ee677771777c9bd88e5747ff766f284ae3e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68637923"
---
# <a name="tutorial-group-and-extract-related-data"></a>Tutorial: Agrupar e extrair dados relacionados
Neste tutorial, adicione uma entidade composta para agrupar dados extraídos de vários tipos em uma única entidade contida. Agrupando os dados, o aplicativo cliente poderá extrair com facilidade dados relacionados em diferentes tipos de dados.

A finalidade da entidade composta é agrupar entidades relacionadas em uma entidade de categoria pai. As informações existem como entidades separadas antes da criação de uma composição. 

A entidade composta é uma boa opção para esse tipo de dados, porque os dados:

* Estão relacionados uns aos outros. 
* Usam uma variedade de tipos de entidade.
* Precisam ser agrupadas e processados pelo aplicativo cliente como uma unidade de informações.

**Neste tutorial, você aprenderá a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importar o aplicativo de exemplo
> * Criar a intenção
> * Adicionar entidade composta 
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar o aplicativo de exemplo

1.  Faça o download e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) da lista de entidades do tutorial.

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `composite`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="composite-entity"></a>Entidade composta

Neste aplicativo, o nome do departamento é definido na lista de entidades **Departamento** e inclui sinônimos. 

A intenção **TransferEmployeeToDepartment** tem declarações de enunciados para solicitar um funcionário ser movido para outro departamento. 

As declarações de enunciados para essa intenção incluem:

|Exemplo de enunciados|
|--|
|mover John W. Smith para o departamento de contabilidade|
|transferir Jill Jones para R&D|
 
A solicitação de movimentação deve incluir o nome do departamento e o nome do funcionário. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Adicionar as entidades predefinidas PersonName para ajudar com a extração de tipo de dados comuns

O LUIS fornece várias entidades predefinidas para extração de dados comuns. 

1. Selecione **Construir** no painel de navegação superior e, em seguida, selecione **Entidades** na barra de navegação esquerda.

1. Selecione o botão **Gerenciar entidade pré-criada**.

1. Selecione **[PersonName](luis-reference-prebuilt-person.md)** na lista de entidades predefinidas, e selecione **Concluído**.

    ![Captura de tela do número selecionado no diálogo de entidades predefinidas](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Essa entidade ajuda a adicionar o reconhecimento de nome para o aplicativo do seu cliente.

## <a name="create-composite-entity-from-example-utterances"></a>Criar entidades compostas por exemplos de enunciados

1. Selecione **Intenções** no painel de navegação esquerdo.

1. Selecione **TransferEmployeeToDepartment** da lista de intenções.

1. No expressão `place John Jackson in engineering`, selecione a entidade PersonName, `John Jackson`, em seguida, selecione encapsule **na entidade composta** na lista de menus pop-up para o seguinte expressão. 

    ![Captura de tela da seleção de encapsular composição na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Em seguida, selecione imediatamente a última entidade, `engineering` no enunciado. Uma barra verde é desenhada sob as palavras selecionadas, indicando uma entidade composta. No menu pop-up, insira o nome composto `TransferEmployeeInfo` e, em seguida, selecione enter. 

    ![Captura de tela da inserção do nome composto na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. Em **Que tipo de entidade você quer criar?** , quase todos os campos necessários estão na lista: `personName` e `Department`. Selecione **Concluído**. Observe que a entidade predefinida, personName, foi adicionada à entidade composta. Se você pudesse fazer uma entidade predefinida ser exibida entre os tokens de início e fim de uma entidade composta, ela deveria conter essas entidades predefinidas. Se as entidades predefinidas não forem incluídas, a entidade composta não será prevista corretamente, mas, sim, cada elemento individual.

    ![Captura de tela da inserção do nome composto na caixa de diálogo suspensa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Declarações de exemplo de rótulo com entidade composta

1. Em cada enunciado de exemplo, selecione a entidade mais à esquerda que deveria estar na composição. Em seguida, selecione **Encapsular em entidade composta**.

1. Selecione a última palavra na entidade composta e, em seguida, selecione **TransferEmployeeInfo** no menu pop-up. 

1. Verifique se todos os enunciados na intenção estão rotulados com a entidade composta. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Faça o treinamento do aplicativo para que as alterações na intenção possam ser testadas 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Publicar o aplicativo para que o modelo em que foi feito o treinamento possa ser consultado por meio do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Obter a previsão de intenção e de entidade do ponto de extremidade 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Move Jill Jones to DevOps`. O último parâmetro querystring é `q`, a consulta da declaração. 

    Esse teste é para verificar se a composição é extraída corretamente, e um teste poderá incluir um enunciado de exemplo existente ou um novo enunciado. Um bom teste é incluir todas as entidades filho na entidade composta.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Esse enunciado retorna uma matriz de entidades compostas. Cada entidade recebe um tipo e valor. Para conseguir maior precisão para cada entidade filho, use a combinação de tipo e valor do item da matriz composta para localizar o item correspondente na matriz de entidades.  

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informações relacionadas

* [Tutorial de entidade de lista](luis-quickstart-intents-only.md)
* Informações conceituais de [entidade de composição](luis-concept-entity-types.md)
* [Como treinar](luis-how-to-train.md)
* [Como publicar](luis-how-to-publish-app.md)
* [Como testar no portal do LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Próximas etapas

Este tutorial criou uma entidade composta para encapsular as entidades existentes. Isso permite que o aplicativo cliente encontre um grupo de dados relacionados em diferentes tipos de dados para continuar a conversa. Um aplicativo cliente para este aplicativo de Recursos Humanos poderia perguntar qual dia e hora a transferência precisa começar e terminar. Ele também poderia pedir outra logística da transferência, como um telefone físico. 

> [!div class="nextstepaction"] 
> [Saiba mais sobre como adicionar uma entidade simples com uma lista de frases](luis-quickstart-primary-and-secondary-data.md)  
