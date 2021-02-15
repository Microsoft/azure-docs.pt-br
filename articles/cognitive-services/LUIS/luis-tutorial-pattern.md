---
title: 'Tutorial: Padrões – LUIS'
description: Use padrões para aumentar a previsão de intenção e entidade enquanto fornece menos enunciados de exemplo neste tutorial. O padrão é fornecido como um enunciado de modelo de exemplo, que inclui sintaxe para identificar entidades e texto ignorável.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 9814304aed4d7a5f307fb2179491b0fa9635fd68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324647"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Tutorial: Adicionar formatos de enunciado de modelo de padrão comuns para aprimorar previsões

Neste tutorial, use padrões para aumentar a previsão de intenção e entidade, o que permite fornecer menos enunciados de exemplo. O padrão é um enunciado de modelo atribuído a uma intenção, que contém a sintaxe para identificar as entidades e o texto ignorável.

**Neste tutorial, você aprenderá a:**

> [!div class="checklist"]
> * Criar um padrão
> * Verificar aprimoramentos na previsão de padrões
> * Marcar texto como ignorável e aninhar dentro do padrão
> * Use o painel de teste para verificar o sucesso do padrão

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Enunciados em intenção e padrão

Há dois tipos de enunciados armazenados no aplicativo LUIS:

* Enunciados de exemplo na Intenção
* Enunciados de modelo no Padrão

Adicionar enunciados de modelo como um padrão permite fornecer menos enunciados de exemplo para uma intenção.

Um padrão é aplicado como uma combinação da correspondência de texto e do machine learning.  O enunciado de modelo no padrão, junto com os enunciados de exemplo na intenção, fornecem ao LUIS uma melhor compreensão de quais enunciados se encaixam na intenção.

## <a name="import-example-app-and-clone-to-new-version"></a>Importar aplicativo de exemplo e clonar para a nova versão

Use as seguintes etapas:

1.  Baixe e salve o [arquivo JSON do aplicativo](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Importe o JSON para um novo aplicativo usando o [portal do LUIS](https://www.luis.ai). Na página **Meus Aplicativos**, selecione **+ Novo aplicativo para conversa** e, em seguida, **Importar como JSON**. Escolha o arquivo que você baixou na etapa anterior e nomeie o aplicativo `Patterns tutorial`.

## <a name="create-new-intents-and-their-utterances"></a>Crie novas intenções e seus enunciados

As duas intenções localizam o gerente ou os subordinados diretos do gerente, com base no texto do enunciado. A dificuldade é que as duas intenções _significam_ coisas diferentes, mas a maioria das palavras é a mesma. Somente a ordem das palavras é diferente. Para que a intenção seja prevista corretamente, ela precisará ter muitos exemplos.

1. Selecione **Build** no menu de navegação.

1. Na página **Intenções**, selecione **+ Criar** para criar uma intenção.

1. Insira `OrgChart-Manager` na caixa de diálogo pop-up, depois selecione **Concluído**.

    ![Criar nova janela pop-up de mensagem](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Adicione enunciados de exemplo para a intenção. Esses enunciados não são _exatamente_ iguais, mas têm um padrão que pode ser extraído.

    |Exemplo de enunciados|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. Selecione **Intenções** no painel de navegação esquerdo.

1. Selecione **+ Criar** para criar uma intenção. Insira `OrgChart-Reports` na caixa de diálogo pop-up, depois selecione **Concluído**.

1. Adicione enunciados de exemplo para a intenção.

    |Exemplo de enunciados|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Cuidado com a quantidade de enunciado de exemplo

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Treinar o aplicativo antes do teste ou da publicação

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publicar o aplicativo para consulta do ponto de extremidade

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Obter a intenção e as entidades do ponto de extremidade

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Vá para o final da URL na barra de endereços e substitua _YOUR_QUERY_HERE_ por: `Who is the boss of Jill Jones?`.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

A principal intenção correta foi prevista, `OrgChart-Manager`, mas a pontuação não está acima de 70% e não está acima o suficiente da próxima intenção mais alta. Use padrões para tornar a pontuação da intenção correta significativamente maior em porcentagem e mais distante da próxima pontuação mais alta.

Uma nova e segunda janela do navegador é aberta. Você a usará mais adiante no tutorial.

## <a name="template-utterances"></a>Enunciados de modelo
Devido à natureza do domínio de assunto de Recursos Humanos, há algumas maneiras comuns de perguntar sobre as relações de funcionários em organizações. Por exemplo:

|Declarações|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Esses enunciados estão muito próximos para determinar a exclusividade contextual de cada um sem fornecer _muitos_ exemplos de enunciados. Adicionando um padrão a uma intenção, o LUIS aprende padrões de enunciado comuns para uma intenção sem a necessidade de fornecer muitos outros exemplos de enunciado.

Exemplos de enunciado de modelo para essa intenção incluem:

|Exemplo dos enunciados de modelo|significado da sintaxe|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|`{EmployeeListEntity}` intercambiável<br>ignorar `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|`{EmployeeListEntity}` intercambiável<br>ignorar `[?]`|

A sintaxe `{EmployeeListEntity}` marca o local da entidade dentro da declaração de modelo, bem como qual entidade é. A sintaxe opcional, `[?]`, marca palavras ou [pontuações](luis-reference-application-settings.md#punctuation-normalization) opcionais. O LUIS corresponde ao enunciado, ignorando o texto opcional dentro dos parênteses.

Embora a sintaxe se pareça com uma expressão regular, ela não é uma expressão regular. Apenas a sintaxe com aspas inglesas, `{}`, e colchetes, `[]`, tem suporte. Eles podem ser aninhados em até dois níveis.

Para que um padrão seja correspondido a um enunciado, _primeiro_, as entidades do enunciado precisam corresponder às entidades do enunciado de modelo. Isso significa que as entidades precisam ter exemplos suficientes em enunciados de exemplo com um alto grau de previsão antes que os padrões com entidades sejam bem-sucedidos. No entanto, o modelo não ajuda a prever entidades, apenas intenções.

**Embora os padrões permitam que você forneça menos enunciados de exemplo, se as entidades não forem detectadas, o padrão não corresponderá.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Adicione os padrões para a intenção do Organograma-Manager

1. Selecione **Compilar** no menu superior.

1. Na navegação esquerda, em **Melhorar o desempenho do aplicativo**, selecione **Padrões** na navegação esquerda.

1. Selecione a intenção **OrgChart-Manager** e, em seguida, insira os seguintes enunciados de modelo:

    |Enunciados de modelo|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Esses enunciados de modelo incluem a entidade **EmployeeListEntity** com a notação de chave.

1. Selecione a intenção **OrgChart-Reports** e, em seguida, insira os seguintes enunciados de modelo, enquanto ainda estiver na página de Padrões:

    |Enunciados de modelo|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Ponto de extremidade de consulta quando os padrões são usados

Agora que os padrões foram adicionados ao aplicativo, treine, publique e consulte o aplicativo no ponto de extremidade de runtime de previsão.

1. Selecione **Treinar**. Após a conclusão do treinamento, selecione **Publicar** e selecione o slot **Produção** e selecione **Concluído**.

1. Após a conclusão da publicação, alterne as guias do navegador de volta para a guia da URL do ponto de extremidade.

1. Acesse o final da URL na barra de endereços e confirme se a consulta ainda é `Who is the boss of Jill Jones?` e envie a URL para uma nova previsão.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
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

A previsão da intenção agora é significativamente mais confiável, e a pontuação da próxima intenção mais alta é muito menor. Essas duas intenções não mudarão durante o treinamento.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Trabalhanr com texto opcional e as entidades predefinidas

As declarações de modelo padrão anteriores neste tutorial tinham alguns exemplos de uso da letra s, caso seja um texto opcional `'s`e o uso de ponto de interrogação, `?`. Suponha que você precisa permitir para datas atuais e futuras no texto da expressão.

Exemplo de enunciados:

|Intencional|Exemplo de enunciado com texto opcional e as entidades predefinidas|
|:--|:--|
|Organograma-Manager|`Who was Jill Jones manager on March 3?`|
|Organograma-Manager|`Who is Jill Jones manager now?`|
|Organograma-Manager|`Who will be Jill Jones manager in a month?`|
|Organograma-Manager|`Who will be Jill Jones manager on March 3?`|

Cada um desses exemplos usa um indicativo de verbo, `was`, `is`, `will be`, bem como uma data `March 3`, `now`, e `in a month`, que LUIS precisa prever corretamente. Observe que os dois últimos exemplos na tabela usam quase o mesmo texto, exceto para `in` e `on`.

Declarações de modelo de exemplo que permitem essas informações opcionais:

|Intencional|Exemplo de enunciado com texto opcional e as entidades predefinidas|
|:--|:--|
|Organograma-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|Organograma-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


O uso da sintaxe de colchetes, opcional `[]`, facilita esse texto opcional adicionar a declaração de modelo e podem ser aninhados em até um segundo nível, `[[]]`e incluem entidades ou texto.


**Pergunta: Por que todas as letras `w`, a primeira letra de cada expressão de modelo, são minúsculas? Eles não devem ser opcionalmente letras maiusculas ou minúsculas?** A expressão enviado para o ponto de extremidade da consulta pelo aplicativo cliente, é convertida em minúsculas. A expressão de modelo pode ser em maiusculas ou minúsculas e a expressão de ponto de extremidade também pode ser qualquer uma. A comparação sempre é feita após a conversão em letras minúsculas.

**Pergunta: Por que um número predefinido não faz parte do modelo de expressão se 3 de março está previsto tanto como número `3` quanto como data `March 3`?** A expressão de modelo contextualmente está usando uma data, ou, literalmente, como em `March 3` ou abstraída como `in a month`. Uma data pode conter um número, mas um número necessariamente não pode ser visto como uma data. Sempre use a entidade que melhor representa o tipo que você deseja que seja retornado nos resultados da previsão de JSON.

**Pergunta: E quanto a expressões mal formuladas como `Who will {EmployeeListEntity}['s] manager be on March 3?`.** Tempos verbais gramaticalmente diferentes, como este, em que o `will` e `be` são separados precisarão ser uma nova declaração de modelo. A expressão de modelo existente não coincida com isso. Embora a intenção da declaração não foi alterada, o posicionamento da palavra na declaração foi alterada. Essa alteração afeta a previsão no LUIS. Você pode [grupo e ou](#use-the-or-operator-and-groups) verbais combinar essas declarações.

> [!CAUTION]
> **Lembre-se: entidades encontram-se em primeiro lugar, em seguida, o padrão é correspondido.**

### <a name="add-new-pattern-template-utterances"></a>Adicionar novas declarações de modelo padrão

1. Enquanto estiver na seção **Padrões** de **Build**, adicione declarações do modelo de padrão novo. Selecione **OrgChart Manager** no menu suspenso intencional e insira cada uma das declarações de modelo a seguir:

    |Intencional|Exemplo de enunciado com texto opcional e as entidades predefinidas|
    |--|--|
    |Organograma-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |Organograma-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |Organograma-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Selecione **Treinar** na barra de navegação para treinar o aplicativo.

3. Após a conclusão do treinamento, selecione **Teste** na parte superior do painel para abrir o painel de teste.

4. Insira várias declarações de teste para verificar se o padrão é correspondido e a pontuação de intenção é muito alta.

    Depois de inserir a primeira expressão, selecione **Inspecionar** sob o resultado para que você possa ver todos os resultados de previsão. Cada enunciado deve ter a intenção **OrgChart-Manager** e extrair os valores para as entidades `EmployeeListEntity` e `datetimeV2`.

    |Enunciado|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Todas essas declarações encontradas as entidades dentro, portanto eles coincidir com o mesmo padrão e têm uma pontuação alta de previsão. Você adicionou alguns padrões que corresponderão a muitas variações de enunciados. Você não precisa adicionar nenhum enunciado de exemplo na intenção para que o enunciado de modelo funcione no padrão.

Esse uso de padrões forneceu:
* Pontuações de previsão mais altas
* Com os mesmos enunciados de exemplo na intenção
* Com apenas alguns enunciados de modelo bem construídos no padrão

### <a name="use-the-or-operator-and-groups"></a>Use o operador OR e grupos

Várias das declarações de modelo anterior são muito parecidas. Use a sintaxe **group** `()` e **OR** `|` para reduzir os enunciados de modelo.

Os dois padrões a seguir podem ser combinados em um só padrão por meio do grupo `()` e da sintaxe OR `|`.

|Intencional|Exemplo de enunciado com texto opcional e as entidades predefinidas|
|--|--|
|Organograma-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|Organograma-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

A nova declaração de modelo será:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Isso usa um **grupo** em torno dos tempos verbais exigidos e o `in` opcional e `on` com um **ou** pipe entre eles.

> [!NOTE]
> Ao usar o símbolo _OR_, `|` (barra vertical), separe o símbolo de barra vertical com um espaço antes e depois dele no modelo de exemplo.

1. Na página **Padrões**, selecione o filtro **Organograma-Manager**. Restrinja a lista pesquisando por `manager`.

1. Mantenha uma versão da expressão de modelo (para editar na próxima etapa) e excluir as outras variações.

1. Alterar a definição do modelo para:

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Selecione **Treinar** na barra de navegação para treinar o aplicativo.

3. Após a conclusão do treinamento, selecione **Teste** na parte superior do painel para abrir o painel de teste.

    Use o painel de teste para testar versões de declaração:

    |Declarações a serem inseridas no painel de teste|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Usando mais sintaxe de padrão, você pode reduzir o número de enunciados de modelo que precisa manter no aplicativo, obtendo, ainda, uma alta pontuação de previsão.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Use as âncoras de início e término da declaração

A sintaxe padrão oferece uma sintaxe de âncora da declaração de início e término de um ponto de inserção, `^`. As âncoras de declaração de início e término podem ser usadas juntas para direcionar na declaração muito específica e possivelmente literal ou usadas separadamente para propósitos de destino.

## <a name="using-patternany-entity"></a>Usando a entidade Pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Adicione o enunciado de exemplo com Pattern.any

1. Selecione **Construir** no painel de navegação superior e, em seguida, selecione **Intenções** na barra de navegação esquerda.

1. Selecione **FindForm** na lista de intenções.

1. Adicione alguns enunciados de exemplo. O texto que deve ser previsto como um Pattern.any está em **negrito**. O nome do formulário é difícil de ser determinado com base nas outras palavras no enunciado. O Pattern.any ajudará com isso marcando os limites da entidade.

    |Exemplo de enunciado|Nome do formulário|
    |--|--|
    |Onde está o formulário **O que fazer quando ocorrer um incêndio no laboratório** e quem precisa para assiná-lo depois que eu lê-lo?|O que fazer quando um incêndio começa no laboratório
    |Onde está **Solicitar a realocação do funcionário novo para a empresa** no servidor?|Realocação de solicitação do novo funcionário para a empresa|
    |Quem criou "**Solicitações de saúde e bem-estar no campus principal**" e qual é a versão mais atual?|Solicitações de saúde e bem-estar no campus principal|
    |Estou procurando o formulário nomeado "**Solicitação de mudança do escritório incluindo ativos físicos**". |Solicitação de mudança de escritório incluindo ativos físicos|

    Sem uma entidade Pattern.any, seria difícil para o LUIS reconhecer onde o título do formulário termina por causa das muitas variações dos nomes de formulário.

### <a name="create-a-patternany-entity"></a>Criar uma entidade Pattern.any
A entidade Pattern.any extrai entidades de comprimento variável. Funciona apenas em um padrão porque o padrão marca o início e o fim da entidade com sintaxe.

1. Selecione **Entidades** no painel de navegação esquerdo.

1. Selecione **+ Criar**, insira o nome `FormName` e selecione **Pattern.any** como o tipo. Selecione **Criar**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Adicionar um padrão que usa o Pattern.any

1. Selecione **Padrões** no painel de navegação esquerdo.

1. Selecione a intenção **FindForm**.

1. Insira os seguintes enunciados de modelo que usam a nova entidade:

    |Enunciados de modelo|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Treine o aplicativo.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testar o novo padrão quanto à extração de dados de forma livre
1. Selecione **Testar** na barra superior para abrir o painel de teste.

1. Insira o enunciado a seguir:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. Selecione **Inspecionar** embaixo do resultado para ver os resultados de teste para a entidade e para a intenção.

    A entidade `FormName` é encontrada primeiro e o padrão é encontrado depois, determinando a intenção. Se tiver um resultado do teste em que as entidades não forem detectadas e, portanto, o padrão não for encontrado, será necessário adicionar mais declarações de exemplo sobre a intenção (não o padrão).

1. Feche o painel de teste selecionando o botão **Testar** na navegação superior.

### <a name="using-an-explicit-list"></a>Uso de uma lista explícita

Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir esse problema.

## <a name="what-did-this-tutorial-accomplish"></a>O que este tutorial realizou?

Este tutorial adicionou padrões para ajudar o LUIS a prever a intenção com uma pontuação significativamente mais alta sem a necessidade de adicionar mais enunciados de exemplo. Marcar entidades e texto ignorável permitiu ao LUIS aplicar o padrão a uma variedade maior de enunciados.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas


> [!div class="nextstepaction"]
> [Saiba como usar funções com um padrão](luis-tutorial-pattern.md)
