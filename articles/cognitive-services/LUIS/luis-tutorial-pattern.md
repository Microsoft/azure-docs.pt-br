---
title: Padrões
titleSuffix: Azure Cognitive Services
description: Use padrões para aumentar a previsão de intenção e entidade enquanto fornece menos enunciados de exemplo. O padrão é fornecido por meio de um exemplo de enunciado de modelo, que inclui sintaxe para identificar entidades e texto ignorável.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: diberry
ms.openlocfilehash: e559d4a3366c45bd054cbf3a235805e048de3493
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276041"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats"></a>Tutorial: Adicionar formatos comuns de enunciado de modelo padrão

Neste tutorial, use padrões para aumentar a previsão de intenção e entidade enquanto fornece menos enunciados de exemplo. O padrão é fornecido por meio de um exemplo de enunciado de modelo, que inclui sintaxe para identificar entidades e texto ignorável. Um padrão é uma combinação da correspondência entre expressões e o aprendizado de máquina.  O exemplo de enunciado de modelo, junto com os enunciados de intenções, fornecem ao LUIS uma melhor compreensão de quais enunciados se encaixam na intenção. 

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Importar o aplicativo de exemplo 
> * Criar a intenção
> * Treinar
> * Publicar
> * Obter intenções e entidades do ponto de extremidade
> * Criar um padrão
> * Verificar aprimoramentos na previsão de padrões
> * Marcar texto como ignorável e aninhar dentro do padrão
> * Use o painel de teste para verificar o sucesso do padrão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importar o aplicativo de exemplo

Continue com o aplicativo criado no último tutorial, denominado **HumanResources**. 

Use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-batchtest-HumanResources.json).

2. Importe o JSON em um novo aplicativo.

3. Na seção **Gerenciar**, na guia **Versões**, clone a versão e nomeie-a como `patterns`. A clonagem é uma ótima maneira de testar vários recursos de LUIS sem afetar a versão original. Como o nome da versão é usado como parte da rota de URL, o nome não pode conter nenhum caractere que não seja válido em uma URL.

## <a name="create-new-intents-and-their-utterances"></a>Crie novas intenções e seus enunciados

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Na página **Inteções**, selecione **Criar nova intenção**. 

3. Insira `OrgChart-Manager` na caixa de diálogo pop-up, depois selecione **Concluído**.

    ![Criar nova janela pop-up de mensagem](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

4. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |A quem João W. Silva é subordinado?|
    |A quem João W. Silva se reporta?|
    |Quem é o gerente de João W. Silva?|
    |A quem Jill Jones se reporta diretamente?|
    |Quem é o supervisor de Jill Jones?|

    [![Captura de tela de LUIS adicionando novas declarações de intenção](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png "Captura de tela de LUIS adicionando novas declarações a intenção")](media/luis-tutorial-pattern/hr-orgchart-manager-intent.png#lightbox)

    Não se preocupe se a entidade keyPhrase estiver rotulada nos enunciados da intenção, em vez da entidade do funcionário. Ambos são previstos corretamente no painel Teste e no ponto de extremidade. 

5. Selecione **Intenções** no painel de navegação esquerdo.

6. Selecione **Criar nova intenção**. 

7. Insira `OrgChart-Reports` na caixa de diálogo pop-up, depois selecione **Concluído**.

8. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |Quem são os subordinados de João W. Silva?|
    |Quem reporta-se a João W. Silva?|
    |Quem João W. Silva gerencia?|
    |Quem são os subordinados diretos de João W. Silva?|
    |Quem é o supervisor de João W. Silva?|

## <a name="caution-about-example-utterance-quantity"></a>Cuidado com a quantidade de enunciado de exemplo

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

## <a name="train"></a>Treinar

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Publicar

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Vá até o final da URL no endereço e insira `Who is the boss of Jill Jones?`. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.353984952
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.353984952
            },
            {
                "intent": "OrgChart-Reports",
                "score": 0.214128986
            },
            {
                "intent": "EmployeeFeedback",
                "score": 0.08434003
            },
            {
                "intent": "MoveEmployee",
                "score": 0.019131
            },
            {
                "intent": "GetJobInformation",
                "score": 0.004819009
            },
            {
                "intent": "Utilities.Confirm",
                "score": 0.0043958663
            },
            {
                "intent": "Utilities.StartOver",
                "score": 0.00312064588
            },
            {
                "intent": "Utilities.Cancel",
                "score": 0.002265454
            },
            {
                "intent": "Utilities.Help",
                "score": 0.00133465114
            },
            {
                "intent": "None",
                "score": 0.0011388344
            },
            {
                "intent": "Utilities.Stop",
                "score": 0.00111166481
            },
            {
                "intent": "FindForm",
                "score": 0.0008900076
            },
            {
                "intent": "ApplyForJob",
                "score": 0.0007836131
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                }
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

Essa consulta foi realizada com êxito? Para este ciclo de treinamento, teve êxito. As pontuações das duas principais intenções estão próximas. Como o treinamento de LUIS não é exatamente o mesmo a cada vez, há um pouco de variação, essas duas pontuações podem se inverter no próximo ciclo de treinamento. O resultado é que a intenção errada pode ser retornada. 

Use padrões para tornar a pontuação da intenção correta significativamente maior em porcentagem e mais distante da próxima pontuação mais alta. 

Uma nova e segunda janela do navegador é aberta. Você usará esse valor posteriormente no tutorial. 

## <a name="template-utterances"></a>Enunciados de modelo
Devido à natureza do domínio de Recursos Humanos, há algumas maneiras comuns de perguntar sobre relações de funcionário em organizações. Por exemplo:

|Declarações|
|--|
|A quem o senhor João W. Silva deve se reportar?|
|Quem responde a João W. Silva?|

Essas declarações estão perto demais para determinar a exclusividade contextual de cada uma sem fornecer muitos exemplos de declaração. Adicionando um padrão para uma intenção, o LUIS aprende padrões de declaração comuns para uma intenção sem fornecer muitos exemplos de declaração. 

Exemplos de enunciado de modelo para essa intenção incluem:

|Exemplo dos enunciados de modelo|significado da sintaxe|
|--|--|
|A quem {Funcionário} se reporta [?]|intercambiável {Funcionário}, ignorar [?]}|
|Quem reporta-se a {Funcionário} [?]|intercambiável {Funcionário}, ignorar [?]}|

A sintaxe `{Employee}` marca o local da entidade dentro da declaração de modelo, bem como qual entidade é. A sintaxe opcional, `[?]`, marca palavras ou pontuações que são opcionais. O LUIS corresponde ao enunciado, ignorando o texto opcional dentro dos parênteses.

Embora a sintaxe se pareça com expressões regulares, não se trata de expressões regulares. Apenas a sintaxe com aspas inglesas, `{}`, e colchetes, `[]`, tem suporte. Eles podem ser aninhados em até dois níveis.

Para que um padrão seja correspondido a um enunciado, as entidades dentro do enunciado têm que corresponder primeiro às entidades no enunciado de modelo. No entanto, o modelo não ajuda a prever entidades, apenas intenções. 

**Embora os padrões permitam que você forneça menos enunciados de exemplo, se as entidades não forem detectadas, o padrão não corresponderá.**

## <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Adicione os padrões para a intenção do Organograma-Manager

1. Selecione **Compilar** no menu superior.

2. Na navegação esquerda, em **Melhorar o desempenho do aplicativo**, selecione **Padrões** na navegação esquerda.

3. Selecione a intenção **OrgChart-Manager** e, em seguida, insira os seguintes enunciados de modelo:

    |Enunciados de modelo|
    |:--|
    |A quem {Funcionário} é subordinado [?]|
    |A quem {Funcionário} se reporta [?]|
    |Quem é o gerente [do] {Funcionário} [?]|
    |A quem {Funcionário} reporta-se diretamente [?]|
    |Quem é o supervisor de {Funcionário} [?]|
    |Quem é o chefe de {Funcionário} [?]|

    Entidades com funções usam uma sintaxe que inclui o nome da função e são abordadas em um [tutorial separado para funções](luis-tutorial-pattern-roles.md). 

    Se você digitar o enunciado de modelo, o LUIS ajudará a preencher a entidade quando você inserir o colchete esquerdo, `{`.

    [![Captura de tela da entrada de enunciados de modelo para intenção](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png)](./media/luis-tutorial-pattern/hr-pattern-missing-entity.png#lightbox)

4. Selecione a intenção **OrgChart-Reports** e, em seguida, insira os seguintes enunciados de modelo, enquanto ainda estiver na página de Padrões:

    |Enunciados de modelo|
    |:--|
    |Quem são os subordinados [de] {Funcionário} [?]|
    |Quem reporta-se a {Funcionário} [?]|
    |Quem {Funcionário} gerencia [?]|
    |Quem são subordinados diretos do {Funcionário} [?]|
    |Quem {Funcionário} supervisiona [?]|
    |Quem é o chefe de {Funcionário} [?]|

## <a name="query-endpoint-when-patterns-are-used"></a>Ponto de extremidade de consulta quando os padrões são usados

Agora que os padrões foram adicionados ao aplicativo, treine, publique e consulte o aplicativo no ponto de extremidade de tempo de execução de previsão.

1. Treine e publique o aplicativo novamente.

1. Alterne as guias do navegador para a guia de URL do ponto de extremidade.

1. Vá até o final da URL no endereço e insira `Who is the boss of Jill Jones?` como o enunciado. O último parâmetro de querystring é `q`, o enunciado **consulta**. 

    ```json
    {
        "query": "who is the boss of jill jones?",
        "topScoringIntent": {
            "intent": "OrgChart-Manager",
            "score": 0.9999989
        },
        "intents": [
            {
                "intent": "OrgChart-Manager",
                "score": 0.9999989
            },
            {
                "intent": "OrgChart-Reports",
                "score": 7.616303E-05
            },
            {
                "intent": "EmployeeFeedback",
                "score": 7.84204349E-06
            },
            {
                "intent": "GetJobInformation",
                "score": 1.20674213E-06
            },
            {
                "intent": "MoveEmployee",
                "score": 7.91245157E-07
            },
            {
                "intent": "None",
                "score": 3.875E-09
            },
            {
                "intent": "Utilities.StartOver",
                "score": 1.49E-09
            },
            {
                "intent": "Utilities.Confirm",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Help",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Stop",
                "score": 1.34545453E-09
            },
            {
                "intent": "Utilities.Cancel",
                "score": 1.225E-09
            },
            {
                "intent": "FindForm",
                "score": 1.123077E-09
            },
            {
                "intent": "ApplyForJob",
                "score": 5.625E-10
            }
        ],
        "entities": [
            {
                "entity": "jill jones",
                "type": "Employee",
                "startIndex": 19,
                "endIndex": 28,
                "resolution": {
                    "values": [
                        "Employee-45612"
                    ]
                },
                "role": ""
            },
            {
                "entity": "boss of jill jones",
                "type": "builtin.keyPhrase",
                "startIndex": 11,
                "endIndex": 28
            }
        ]
    }
    ```

A previsão de intenção agora é significativamente mais confiante.

## <a name="working-with-optional-text-and-prebuilt-entities"></a>Trabalhanr com texto opcional e as entidades predefinidas

As declarações de modelo padrão anteriores neste tutorial tinham alguns exemplos de uso da letra s, caso seja um texto opcional `'s`e o uso de ponto de interrogação, `?`. Suponha que você precisa permitir para datas atuais e futuras no texto da expressão.

Exemplo de enunciados:

|Intenção|Exemplo de enunciado com texto opcional e as entidades predefinidas|
|:--|:--|
|Organograma-Manager|`Who was Jill Jones manager on March 3?`|
|Organograma-Manager|`Who is Jill Jones manager now?`|
|Organograma-Manager|`Who will be Jill Jones manager in a month?`|
|Organograma-Manager|`Who will be Jill Jones manager on March 3?`|

Cada um desses exemplos usa um indicativo de verbo, `was`, `is`, `will be`, bem como uma data `March 3`, `now`, e `in a month`, que LUIS precisa prever corretamente. Observe que os dois últimos exemplos usam quase o mesmo texto, exceto para `in` e `on`.

Declarações de modelo de exemplo que permitem essas informações opcionais: 

|Intenção|Exemplo de enunciado com texto opcional e as entidades predefinidas|
|:--|:--|
|Organograma-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?`]|
|Organograma-Manager|`who is {Employee}['s] manager [[on]{datetimeV2}?]`|


O uso da sintaxe de colchetes, opcional `[]`, facilita esse texto opcional adicionar a declaração de modelo e podem ser aninhados em até um segundo nível, `[[]]`e incluem entidades ou texto.


**Pergunta: Por que todas as letras `w`, a primeira letra de cada expressão de modelo, são minúsculas? Eles não devem ser opcionalmente letras maiusculas ou minúsculas?** A expressão enviado para o ponto de extremidade da consulta pelo aplicativo cliente, é convertida em minúsculas. A expressão de modelo pode ser em maiusculas ou minúsculas e a expressão de ponto de extremidade também pode ser qualquer uma. A comparação sempre é feita após a conversão em letras minúsculas.

**Pergunta: Por que um número predefinido não faz parte do modelo de expressão se 3 de março está previsto tanto como número `3` quanto como data `March 3`?** A expressão de modelo contextualmente está usando uma data, ou, literalmente, como em `March 3` ou abstraída como `in a month`. Uma data pode conter um número, mas um número necessariamente não pode ser visto como uma data. Sempre use a entidade que melhor representa o tipo que você deseja que seja retornado nos resultados da previsão de JSON.  

**Pergunta: E quanto a expressões mal formuladas como `Who will {Employee}['s] manager be on March 3?`.** Tempos verbais gramaticalmente diferentes, como este, em que o `will` e `be` são separados precisarão ser uma nova declaração de modelo. A expressão de modelo existente não coincida com isso. Embora a intenção da declaração não foi alterada, o posicionamento da palavra na declaração foi alterada. Essa alteração afeta a previsão no LUIS. Você pode [grupo e ou](#use-the-or-operator-and-groups) verbais combinar essas declarações. 

**Lembre-se: entidades encontram-se em primeiro lugar, em seguida, o padrão é correspondido.**

## <a name="edit-the-existing-pattern-template-utterance"></a>Editar a expressão de modelo padrão existente

1. No site do LUIS, selecione **construir** no menu superior, em seguida, selecione **padrões** no menu à esquerda. 

1. Pesquise a declaração de modelo existente `Who is {Employee}['s] manager[?]`e selecione as reticências (***...*** ) para a direita, em seguida, selecione **Editar** no menu pop-up. 

1. Alterar a definição do modelo para: `who is {Employee}['s] manager [[on]{datetimeV2}?]`

## <a name="add-new-pattern-template-utterances"></a>Adicionar novas declarações de modelo padrão

1. Enquanto estiver na seção **Padrões** de **Build**, adicione declarações do modelo de padrão novo. Selecione **OrgChart Manager** no menu suspenso intencional e insira cada uma das declarações de modelo a seguir:

    |Intenção|Exemplo de enunciado com texto opcional e as entidades predefinidas|
    |--|--|
    |Organograma-Manager|`who was {Employee}['s] manager [[on]{datetimeV2}?]`|
    |Organograma-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
    |Organograma-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

2. Treine o aplicativo.

3. Selecione **Teste** na parte superior do painel para abrir o painel de teste. 

4. Insira várias declarações de teste para verificar se o padrão é correspondido e a pontuação de intenção é muito alta. 

    Depois de inserir a primeira expressão, selecione **Inspecionar** sob o resultado para que você possa ver todos os resultados de previsão. Cada expressão deve ter a intenção do **Organograma-Manager** e deve extrair os valores para as entidades de funcionário e datetimeV2.

    |Enunciado|
    |--|
    |Quem será o gerente de João W. Silva|
    |quem será o gerente de joão|
    |Quem será o gerente de João?|
    |Quem será o gerente de João W. Silva em 3 de março|
    |Quem será o gerente de João W. Silva no próximo mês|
    |Quem será o gerente de João W. Silva no próximo mês?|

Todas essas declarações encontradas as entidades dentro, portanto eles coincidir com o mesmo padrão e têm uma pontuação alta de previsão.

## <a name="use-the-or-operator-and-groups"></a>Use o operador OR e grupos

Várias das declarações de modelo anterior são muito parecidas. Use o **grupo** `()` e a sintaxe **OU** `|` para reduzir as declarações de modelo. 

Os seguintes 2 padrões podem combinar em um único padrão usando o grupo `()` e a sintaxe OU `|`.

|Intenção|Exemplo de enunciado com texto opcional e as entidades predefinidas|
|--|--|
|Organograma-Manager|`who will be {Employee}['s] manager [[in]{datetimeV2}?]`|
|Organograma-Manager|`who will be {Employee}['s] manager [[on]{datetimeV2}?]`|

A nova declaração de modelo será: 

`who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`. 

Isso usa um **grupo** em torno dos tempos verbais exigidos e o `in` opcional e `on` com um **ou** pipe entre eles. 

1. Na página **Padrões**, selecione o filtro **Organograma-Manager**. Restrinja a lista pesquisando por `manager`. 

    ![Pesquisar nos padrões de intenção do Organograma-Manager para o termo 'gerenciador'](./media/luis-tutorial-pattern/search-patterns.png)

1. Mantenha uma versão da expressão de modelo (para editar na próxima etapa) e excluir as outras variações. 

1. Alterar a definição do modelo para: 

    `who ( was | is | will be ) {Employee}['s] manager [([in]|[on]){datetimeV2}?]`.

1. Treine o aplicativo.

1. Use o painel de teste para testar versões de declaração:

    |Declarações a serem inseridas no painel de teste|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|    
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|


## <a name="use-the-utterance-beginning-and-ending-anchors"></a>Use as âncoras de início e término da declaração

A sintaxe padrão oferece uma sintaxe de âncora da declaração de início e término de um ponto de inserção, `^`. As âncoras de declaração de início e término podem ser usadas juntas para direcionar na declaração muito específica e possivelmente literal ou usadas separadamente para propósitos de destino. 

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial adiciona duas novas intenções a enunciados que eram difíceis prever com alta precisão, sem necessidade de muitos enunciados de exemplo. Adicionar padrões a eles permitiu que o LUIS previsse melhor a intenção com uma pontuação significativamente maior. Marcar entidades e texto ignorável permitiu ao LUIS aplicar o padrão a uma variedade maior de enunciados.

> [!div class="nextstepaction"]
> [Saiba como usar funções com um padrão](luis-tutorial-pattern-roles.md)
