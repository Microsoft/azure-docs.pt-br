---
title: Adicionar entidades-LUIS
titleSuffix: Azure Cognitive Services
description: Crie entidades para extrair dados de chave de declarações de usuário em aplicativos de Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 1b42ab9155f5b9719ef6477934722021e3fbac99
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638242"
---
# <a name="create-entities-without-utterances"></a>Criar entidades sem declarações

A entidade representa uma palavra ou frase dentro da declaração que você deseja que seja extraída. Uma entidade representa uma classe que inclui uma coleção de objetos semelhantes (locais, coisas, pessoas, eventos ou conceitos). As entidades descrevem informações relevantes para a intenção e, às vezes, elas são essenciais para seu aplicativo realizar sua tarefa. Você pode criar entidades ao adicionar um expressão a uma intenção ou à parte (antes ou depois) de adicionar um expressão a uma intenção.

Você pode adicionar, editar ou excluir entidades em seu aplicativo LUIS por meio da lista **Entities** na página **Entities**. O LUIS oferece dois tipos principais de entidades: [entidades pré-construídas](luis-reference-prebuilt-entities.md) e suas próprias [entidades customizadas](luis-concept-entity-types.md#types-of-entities).

Depois que uma entidade aprendida por máquina é criada, você precisa marcar essa entidade em todos os expressão de exemplo de todas as intenções em que ela se encontra.

<a name="add-prebuilt-entity"></a>

## <a name="add-a-prebuilt-entity-to-your-app"></a>Adicionar uma entidade predefinida ao seu aplicativo

Entidades pré-compiladas comuns adicionadas a um aplicativo são *número* e *datetimeV2*. 

1. No seu aplicativo, na seção **Criar**, selecione **Entidades** no painel à esquerda.
 
1. Na página **Entidades**, selecione **Incluir entidades pré-construídas**.

1. Na **adicionar as entidades predefinidas** caixa de diálogo, selecione o **número** e **datetimeV2** as entidades predefinidas. Em seguida, selecione **Concluído**.

    ![Captura de tela da caixa de diálogo Adicionar entidade pré-compilada](./media/add-entities/list-of-prebuilt-entities.png)

<a name="add-simple-entities"></a>

## <a name="add-simple-entities-for-single-concepts"></a>Adicionar entidades simples para conceitos únicos

Uma entidade simples descreve um único conceito. Use o procedimento a seguir para criar uma entidade que extrai nomes de departamentos da empresa, como *Recursos humanos* ou *Operações*.   

1. No seu aplicativo, selecione a seção **Criar**, depois selecione **Entidades** no painel esquerdo e, em seguida, selecione **Criar nova entidade**.

1. Na caixa de diálogo pop-up, digite `Location` no **nome da entidade** caixa, selecione **simples** do **tipo de entidade** lista e, em seguida, selecione **feito**.

    Uma vez que esta entidade é criada, vá para todas as intenções que tenham declarações de exemplo que contenham a entidade. Selecione o texto no enunciado do exemplo e marque o texto como a entidade. 

    Uma [lista de frases](luis-concept-feature.md) é comumente usada para aumentar o sinal de uma entidade simples.

<a name="add-regular-expression-entities"></a>

## <a name="add-regular-expression-entities-for-highly-structured-concepts"></a>Adicionar entidades de expressão regular para conceitos altamente estruturados

Uma entidade de expressão regular é usada para extrair dados do enunciado com base em uma expressão regular fornecida por você. 

1. No aplicativo, selecione **Entidades** na navegação esquerda e, em seguida, selecione **Criar nova entidade**.

1. Na caixa de diálogo pop-up, digite `Human resources form name` na caixa **Nome da entidade**, selecione **Expressão regular** na lista **Tipo de entidade**, insira a expressão regular `hrf-[0-9]{6}` e, em seguida, selecione **Concluído**. 

    Essa expressão regular corresponde a caracteres `hrf-`literais, em seguida, 6 dígitos para representar um número de formulário para um formulário de recursos humanos.

<a name="add-composite-entities"></a>

## <a name="add-composite-entities-to-group-into-a-parent-child-relationship"></a>Adicionar entidades compostas ao grupo em uma relação pai-filho

Você pode definir relacionamentos entre entidades de tipos diferentes, criando uma entidade composta. No exemplo a seguir, a entidade contém uma expressão regular e uma entidade pré-construída de nome.  

No enunciado `Send hrf-123456 to John Smith`, o texto `hrf-123456` é correspondido a uma expressão regular de [recursos humanos](#add-regular-expression-entities) e `John Smith` é extraído com a entidade pré-construída personName. Cada entidade é parte de uma entidade de pai maior. 

1. No seu aplicativo, selecione **Entities** na navegação à esquerda da seção **Build** e selecione **Add prbuilt entity**.

1. Adicionar a entidade predefinida **PersonName**. Para mais instruções, consulte [Adicionar entidades pré-compiladas](#add-prebuilt-entity). 

1. Selecione **Entidades** na navegação esquerda e, em seguida, selecione **Criar nova entidade**.

1. Na caixa de diálogo pop-up, insira `SendHrForm` na caixa **Nome da entidade** e, em seguida, selecione **Composto** na lista **Tipo de entidade**.

1. Selecione **Adicionar Filho** para adicionar um novo filho.

1. Em **Filho Nº 1**, selecione a entidade **número** da lista.

1. Em **Child # 2**, selecione a entidade **Nome do formulário de recursos humanos** da lista. 

1. Selecione **Concluído**.

<a name="add-pattern-any-entities"></a>

## <a name="add-patternany-entities-to-capture-free-form-entities"></a>Adicionar padrão. qualquer entidade para capturar entidades de forma livre

[Pattern.any](luis-concept-entity-types.md) entidades são válidas apenas em [padrões](luis-how-to-model-intent-pattern.md), não intenções. Esse tipo de entidade ajuda o LUIS a encontrar o fim de entidades de comprimento variável e escolha de palavras. Como essa entidade é usada em um padrão, o LUIS sabe onde o final da entidade está no modelo de emissão.

Se um aplicativo tiver uma intenção `FindHumanResourcesForm`, o título do formulário extraído poderá interferir na previsão de intenção. Para esclarecer quais palavras estão no título do formulário, use um Pattern.any dentro de um padrão. A previsão do LUIS começa com o enunciado. Primeiro, o enunciado é verificado e correspondido para entidades, quando as entidades são localizadas, então, o padrão é verificado e correspondido. 

No enunciado `Where is Request relocation from employee new to the company on the server?`, o título do formulário é complicado porque não é contextualmente óbvio onde o título termina e onde o resto do enunciado começa. Os títulos podem ser qualquer ordem de palavras, incluindo uma única palavra, frases complexas com pontuação e ordenação de palavras sem sentido. Um padrão permite que você crie uma entidade, em que a entidade completa e exata possa ser extraída. Quando o título é encontrado, a intenção `FindHumanResourcesForm` é prevista porque essa é a intenção do padrão.

1. Na seção **Criar**, selecione **Entidades** no painel esquerdo e, em seguida, selecione **Criar nova entidade**.

1. Na caixa de diálogo **Add Entity**, insira `HumanResourcesFormTitle` na caixa **Entity name** e selecione **Pattern.any** como o **tipo Entity**.

    Para usar a entidade pattern.any, adicione um padrão na página **Padrões**, na seção **Melhorar desempenho do aplicativo**, com a sintaxe correta de chave, como `Where is **{HumanResourcesFormTitle}** on the server?`.

    Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](luis-concept-patterns.md#explicit-lists) para corrigir esse problema. 

<a name="add-a-role-to-pattern-based-entity"></a>

## <a name="add-a-role-to-distinguish-different-contexts"></a>Adicionar uma função para distinguir contextos diferentes

Uma função é um subtipo nomeado com base no contexto. Ele está disponível em todas as entidades, incluindo entidades predefinidas e não aprendidas por computador. 

A sintaxe de uma função é **`{Entityname:Rolename}`** onde o nome da entidade é seguido por dois-pontos e, em seguida, o nome da função. Por exemplo, `Move {personName} from {Location:Origin} to {Location:Destination}`.

1. Na seção **Build**, selecione **Entities** no painel esquerdo.

1. Selecione **Criar nova entidade**. Insira o nome de `Location`. Selecione o tipo **simples** e selecione **feito**. 

1. Selecione **entidades** no painel esquerdo e, em seguida, selecione o novo **local** de entidade criado na etapa anterior.

1. Na caixa de texto **Nome da função**, insira o nome da função `Origin` e insira. Adicione um segundo nome de função `Destination`. 

    ![Captura de tela da adição da função Origem à entidade Local](./media/add-entities/roles-enter-role-name-text.png)

<a name="add-list-entities"></a>

## <a name="add-list-entities-for-exact-matches"></a>Adicionar entidades de lista para correspondências exatas

As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas. 

Para um aplicativo de Recursos Humanos, você pode ter uma lista de todos os departamentos junto com quaisquer sinônimos para os departamentos. Você não precisa conhecer todos os valores ao criar a entidade. Você pode adicionar mais depois de revisar os enunciados reais do usuário com sinônimos.

1. Na seção **Criar**, selecione **Entidades** no painel esquerdo e, em seguida, selecione **Criar nova entidade**.

1. Na caixa de diálogo **Adicionar Entidade**, digite `Department` na caixa **Nome da entidade** e selecione **Lista** como o **Tipo de entidade**. Selecione **Concluído**.
  
1. A página da entidade de lista permite adicionar nomes normalizados. Na caixa de texto **Valores**, insira um nome de departamento para a lista, como `HumanResources`, e pressione Enter no teclado. 

1. À direita do valor normalizado, insira sinônimos, pressionando Enter no teclado após cada item.

1. Se você quiser mais itens normalizados para a lista, selecione **Recomendar**para ver as opções do [dicionário semântico](luis-glossary.md#semantic-dictionary).

    ![Captura de tela de seleção do recurso Recomendar para ver as opções](./media/add-entities/hr-list-2.png)


1. Selecione um item na lista recomendada para adicioná-lo como um valor normalizado ou selecione **Adicionar todos** para adicionar todos os itens. 
    Você pode importar valores para uma entidade de lista existente usando o seguinte formato JSON:

    ```JSON
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

<a name="change-entity-type"></a>

## <a name="do-not-change-entity-type"></a>Não alterar tipo de entidade

O LUIS não permite alterar o tipo da entidade porque não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome ligeiramente diferente. Depois que a entidade é criada, em cada enunciado, remova o antigo nome da entidade rotulada e adicione o novo nome da entidade. Depois que todos os enunciados tiverem sido remarcados, exclua a entidade antiga. 

<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-pattern-from-an-example-utterance"></a>Criar um padrão a partir de um exemplo de expressão

Veja [Adicionar padrão de enunciado existente na página de intenção ou de entidade](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="train-your-app-after-changing-model-with-entities"></a>Treine o aplicativo após alterar o modelo com entidades

Após adicionar, editar ou remover entidades, [treine](luis-how-to-train.md) e [publique](luis-how-to-publish-app.md) o aplicativo para que as alterações afetem as consultas de ponto de extremidade. 

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre entidades pré-construídas, consulte o projeto [Reconhecedores-Texto](https://github.com/Microsoft/Recognizers-Text). 

Para obter informações sobre como a entidade aparece na resposta da consulta do terminal JSON, consulte [Data Extraction](luis-concept-data-extraction.md)

Agora que você adicionou intenções, enunciados e entidades, você tem um aplicativo LUIS básico. Saiba como [treinar](luis-how-to-train.md), [testar](luis-interactive-test.md) e [publicar](luis-how-to-publish-app.md) o aplicativo.
 
