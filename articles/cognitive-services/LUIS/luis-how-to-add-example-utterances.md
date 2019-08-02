---
title: Adicionar exemplo declarações-LUIS
titleSuffix: Azure Cognitive Services
description: Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar o Entendimento de Linguagem (LUIS), você precisa adicionar expressões de exemplo a uma intenção.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 04f0944173df59989745ee9167984b493f202b14
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638225"
---
# <a name="add-an-entity-to-example-utterances"></a>Adicionar uma entidade a enunciados de exemplo 

Enunciados de exemplo são exemplos de texto de comandos ou perguntas do usuário. Para ensinar o LUIS (Serviço Inteligente de Reconhecimento Vocal), você precisa adicionar [enunciados de exemplo](luis-concept-utterance.md) a uma [intenção](luis-concept-intent.md).

Normalmente, você adiciona um exemplo expressão a uma intenção primeiro e, em seguida, cria entidades e o rótulo declarações na página de **tentativas** . Se você preferir criar entidades primeiro, veja [Adicionar entidades](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Marcar entidades em enunciados de exemplo

Quando você seleciona um determinado texto na declaração de exemplo a marcar para uma entidade, um menu pop-up no local é exibido. Use esse menu para criar ou selecionar uma entidade. 

Certos tipos de entidade, como as entidades predefinidas e entidades de expressão regular, não podem ser marcados na declaração de exemplo porque são marcados automaticamente. 

## <a name="add-a-simple-entity"></a>Adicionar uma entidade simples

No procedimento a seguir, você cria e marca uma entidade personalizada dentro do seguinte expressão na página de **tentativas** :

```text
Are there any SQL server jobs?
```

1. Selecione `SQL server` no enunciado para rotulá-lo como uma entidade simples. Na caixa suspensa da entidade que é exibida, você pode selecionar uma entidade existente ou adicionar uma nova entidade. Para adicionar uma nova entidade, digite seu nome `Job` na caixa de texto e, em seguida, selecione **criar nova entidade**.

    ![Captura de tela de inserção do nome de entidade](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Ao selecionar palavras para marcá-las como entidades:
    > * Para uma única palavra, apenas selecione-a. 
    > * Para um conjunto de duas ou mais palavras, selecione a primeira palavra e, em seguida, a palavra final.

1. Na caixa de pop-up **Que tipo de entidade você deseja criar?** , verifique o nome da entidade e selecione o tipo de entidade **Simples**, então selecione **Concluído**.

    Uma [lista de frases](luis-concept-feature.md) normalmente é usada para melhorar o sinal de uma entidade simples.

## <a name="add-a-list-entity"></a>Adicionar uma entidade de lista

As entidades de lista representam um conjunto de correspondências exatas de texto de palavras relacionadas em seu sistema. 

Para obter lista de departamentos da empresa, você pode ter valores normalizados: `Accounting` e `Human Resources`. Cada nome normalizado tem sinônimos. Para um departamento, esses sinônimos podem incluir quaisquer acrônimos, números ou gírias de departamento. Você não precisa conhecer todos os valores ao criar a entidade. Você pode adicionar mais depois de revisar os enunciados reais do usuário com sinônimos.

1. Em um exemplo de expressão na página de **tentativas** , selecione a palavra ou frase desejada na nova lista. Quando a lista suspensa entidade for exibida, insira o nome para a nova entidade na caixa de texto superior e, em seguida, selecione **criar nova entidade**.   

1. Na caixa pop-up **Que tipo de entidade você deseja criar?** , nomeie a entidade e selecione **Lista** como o tipo. Adicione sinônimos deste item de lista e, em seguida, selecione **Concluído**. 

    ![Captura de tela de inserção de sinônimos de entidade de lista](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Você pode adicionar mais itens de lista ou mais sinônimos de item ao rotular outros enunciados ou editar a entidade em **Entidades** no painel de navegação esquerdo. [Editar](luis-how-to-add-entities.md#add-list-entities) as entidades dá as opções de inserir itens adicionais com sinônimos correspondentes ou importar uma lista. 

## <a name="add-a-composite-entity"></a>Adicionar uma entidade composta

As entidades compostas são criadas a partir de **entidades** existentes para formar uma entidade pai. 

Supondo que expressão `Does John Smith work in Seattle?`,, um expressão composto possa retornar informações de entidade do nome `John Smith`do funcionário e o `Seattle` local em uma entidade composta. As entidades filho já devem existir no aplicativo e ser marcadas no exemplo expressão antes de criar a entidade composta.

1. Para encapsular as entidades filho em uma entidade composta, selecione a **primeira** entidade rotulada (mais à esquerda) no expressão para a entidade composta. Uma lista suspensa é exibida para mostrar as opções para essa seleção.

1. Selecione **encapsular em entidade composta** na lista suspensa. 

1. Selecione a última palavra da entidade composta (aquela mais à direita). Observe que uma linha verde segue a entidade composta. Este é o indicador visual de uma entidade composta e deve estar sob todas as palavras na entidade composta da entidade filho mais à esquerda para a entidade filho mais à direita.

1. Insira o nome da entidade composta na lista suspensa.

    Ao encapsular as entidades corretamente, uma linha verde fica sob toda a frase.

1. Valide os detalhes de entidade composta na caixa pop-up **Que tipo de entidade você deseja criar?** e, em seguida, selecione **Concluído**.

    ![Captura de tela do pop-up de detalhes da entidade](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. A entidade composta é exibida com destaques em azul para entidades individuais e um sublinhado verde para a entidade composta inteira. 

    ![Captura de tela da página Detalhes de intenções, com entidade composta](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Adicionar função da entidade ao expressão

Uma função é um subtipo nomeado de uma entidade, determinado pelo contexto do expressão. Você pode marcar uma entidade em um expressão como a entidade ou selecionar uma função dentro dessa entidade. Qualquer entidade pode ter funções, incluindo entidades personalizadas que são aprendidas por máquina (entidades simples e entidades compostas), não são aprendidas por máquina (entidades predefinidas, entidades de expressão regular, entidades de lista). 

Saiba [como marcar um expressão com funções de entidade](tutorial-entity-roles.md) de um tutorial prático. 

## <a name="entity-status-predictions"></a>Previsões do status da entidade

Quando você insere um novo enunciado no portal LUIS, o enunciado pode ter erros de previsão de entidade. O erro de previsão é uma diferença entre como uma entidade é rotulada em comparação com a forma como o LUIS previu a entidade. 

Essa diferença é representada visualmente no portal LUIS com um sublinhado vermelho no enunciado. O sublinhado vermelho pode aparecer em colchetes de entidades ou fora dos colchetes. 

![Captura de tela da discrepância de previsão do status da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Selecione as palavras que estão sublinhadas em vermelho na frase. 

A caixa de entidade exibe o **status de Entidade** com um ponto de exclamação vermelho, se houver uma discrepância de previsão. Para ver o status Entidade com informações sobre a diferença entre entidades rotuladas e previstas, selecione **Status da entidade** e, em seguida, selecione o item à direita.

![Captura de tela da seleção de status da entidade](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

A linha vermelha pode aparecer em qualquer um dos seguintes horários:

   * Quando um enunciado é inserido, mas antes que a entidade seja rotulada
   * Quando o rótulo da entidade é aplicado
   * Quando o rótulo da entidade é removido
   * Quando mais de um rótulo de entidade é previsto para esse texto 

As soluções a seguir ajudam a resolver a discrepância de previsão da entidade:

|Entidade|Indicador Visual|Previsão|Solução|
|--|--|--|--|
|A enunciação entrou, entidade ainda não está rotulada.|sublinhado em vermelho|Previsão está correta.|Rotule a entidade com o valor previsto.|
|Texto sem rótulo|sublinhado em vermelho|Previsão incorreta|Os enunciados atuais que usam essa entidade incorreta precisam ser revisados em todas as intenções. As declarações atuais têm equivocado LUIS que este texto é a entidade prevista.
|Texto corretamente rotulado|destaque da entidade azul, sublinhado em vermelho|Previsão incorreta|Forneça mais enunciados com a entidade corretamente rotulada em uma variedade de lugares e usos. Os enunciados atuais não são suficientes para ensinar ao LUIS que esta é a entidade ou entidades semelhantes aparecem no mesmo contexto. Entidade semelhante deve ser combinada em uma única entidade, portanto, LUIS não é confuso. Outra solução é adicionar uma lista de frases para aumentar o significado das palavras. |
|Texto etiquetado incorretamente|destaque da entidade azul, sublinhado em vermelho|Previsão correta| Forneça mais enunciados com a entidade corretamente rotulada em uma variedade de lugares e usos. 

## <a name="other-actions"></a>Outras ações

Você pode executar ações em enunciados de exemplo como um grupo selecionado ou como um item individual. Grupos de enunciados de exemplo selecionados alteram o menu contextual acima da lista. Itens únicos podem usar o menu contextual acima da lista e o botão de reticências contextual individual no final de cada linha de enunciado. 

### <a name="remove-entity-labels-from-utterances"></a>Remover rótulos de entidade de enunciados

Você pode remover rótulos de entidade aprendidas por computador de um expressão na página de tentativas. Se a entidade não for de aprendizado de máquina, ela não poderá ser removida de um enunciado. Se você precisar remover uma entidade que não seja de aprendizado de máquina do enunciado, precisará excluir a entidade de todo o aplicativo. 

Para remover um rótulo de entidade de aprendizado de máquina de um enunciado, selecione a entidade no enunciado. Em seguida, selecione **Remover Rótulo** na caixa suspensa da entidade que é exibida.

### <a name="add-a-prebuilt-entity-label"></a>Adicionar um rótulo de entidade predefinida

Quando adicionar as entidades predefinidas ao seu aplicativo de LUIS, você não precisará marcar enunciados com essas entidades. Para saber mais sobre entidades predefinidas e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app).

### <a name="add-a-regular-expression-entity-label"></a>Adicionar um rótulo de entidade de expressão regular

Se você adicionar as entidades de expressão regular ao seu aplicativo de LUIS, não precisará marcar enunciados com essas entidades. Para saber mais sobre entidades de expressão regular e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts).


### <a name="create-a-pattern-from-an-utterance"></a>Criar um padrão de um enunciado

Veja [Adicionar padrão de enunciado existente na página de intenção ou de entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).


### <a name="add-a-patternany-entity"></a>Adicionar um padrão. qualquer entidade

Se você adicionar entidades pattern.any ao seu aplicativo de LUIS, não será possível rotular enunciados com essas entidades. Elas só são válidas em padrões. Para saber mais sobre entidades pattern.any e como adicioná-las, veja [Adicionar entidades](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Treinar seu aplicativo após alterar o modelo com enunciados

Depois de adicionar, editar ou remover enunciados, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) seu aplicativo para que as suas alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

Depois de rotular declarações em suas **intenções**, agora você pode criar uma [entidade composta](luis-how-to-add-entities.md).
