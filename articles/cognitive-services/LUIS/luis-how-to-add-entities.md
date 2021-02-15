---
title: Adicionar entidades-LUIS
description: Crie entidades para extrair dados de chave de declarações de usuário em aplicativos de Reconhecimento vocal (LUIS). Os dados de entidade extraídos são usados pelo aplicativo cliente para fullfil solicitações de clientes.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: c5c6836c2d68036bf2b9c5abe191943537349b8d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540958"
---
# <a name="add-entities-to-extract-data"></a>Adicionar entidades para extrair dados

Crie entidades para extrair dados de chave de declarações de usuário em aplicativos de Reconhecimento vocal (LUIS). Os dados de entidade extraídos são usados pelo aplicativo cliente para fullfil solicitações de clientes.

A entidade representa uma palavra ou frase dentro da declaração que você deseja que seja extraída. As entidades descrevem informações relevantes para a intenção e, às vezes, elas são essenciais para seu aplicativo realizar sua tarefa. Você pode criar entidades ao adicionar um exemplo de expressão a uma intenção ou além de (antes ou depois) de adicionar um expressão de exemplo a uma intenção.

## <a name="plan-entities-then-create-and-label"></a>Planejar entidades e, em seguida, criar e rotular

as entidades de aprendizado de máquina podem ser criadas no exemplo declarações ou criadas na página **entidades** .

Em geral, uma prática recomendada é gastar tempo planejando as entidades antes de criar uma entidade de aprendizado de máquina no Portal. Em seguida, crie a entidade Machine-Learning do exemplo expressão com o máximo de detalhes nas subentidades e nos recursos que você conhece no momento. O [tutorial de entidade decombinável](tutorial-machine-learned-entity.md) demonstra como usar esse método.

Como parte do planejamento das entidades, você pode saber que precisa de entidades de correspondência de texto (como entidades predefinidas, entidades de expressão regular ou entidades de lista). Você pode criá-los na página **entidades** antes que eles sejam rotulados no exemplo declarações.

Ao rotular, você pode rotular entidades individuais e, em seguida, criar uma entidade pai de aprendizado de máquina. Ou você pode começar com uma entidade pai de aprendizado de máquina e decompor em entidades filhas.

> [!TIP]
>Rotular todas as palavras que podem indicar uma entidade, mesmo se as palavras não forem usadas quando extraídas no aplicativo cliente.

## <a name="when-to-create-an-entity"></a>Quando criar uma entidade

Depois de planejar suas entidades, você deve criar suas entidades e subentidades de aprendizado de máquina. Isso pode exigir a adição de entidades predefinidas ou entidades de correspondência de texto para fornecer recursos para suas entidades de aprendizado de máquina. Tudo isso deve ser feito antes de rotular.

Depois de começar a rotular o exemplo declarações, você poderá criar entidades aprendidas no computador ou estender as entidades da lista.

Use a tabela a seguir para entender onde criar ou adicionar cada tipo de entidade ao aplicativo.

|Tipo de entidade|Onde criar a entidade no portal do LUIS|
|--|--|
|entidade de aprendizado de máquina|Detalhes de entidades ou intenções|
|Entidade de lista|Detalhes de entidades ou intenções|
|Entidade de expressão regular|Entidades|
|Entidade pattern.any|Entidades|
|Entidade predefinida|Entidades|
|Entidade de domínio predefinida|Entidades|

Você pode criar todas as entidades na página **entidades** ou pode criar duas entidades como parte da rotulagem da entidade no exemplo expressão na página de **detalhes da intenção** . Você só pode _rotular_ uma entidade em um exemplo de expressão da página de **detalhes da intenção** .



## <a name="how-to-create-a-new-custom-entity"></a>Como criar uma nova entidade personalizada

Esse processo funciona para entidades aprendidas por computador, entidades de lista e entidades de expressão regular.

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Selecione a página **entidades** .
1. Selecione **+ criar**e, em seguida, selecione o tipo de entidade.
1. Continue Configurando a entidade e, em seguida, selecione **criar** quando terminar.

## <a name="create-a-machine-learned-entity"></a>Criar uma entidade aprendida pelo computador

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione **+ criar**.
1. Na caixa de diálogo **criar um tipo de entidade** , digite o nome da entidade e selecione **computador aprendido**, selecione. Para adicionar subentidades, selecione **Adicionar estrutura**. Selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da criação de uma entidade aprendida por máquina.](media/add-entities/machine-learned-entity-with-structure.png)

1. Em **Adicionar subentidades**, adicione uma subentidade selecionando a **+** na linha entidade pai.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição de subentidades.](media/add-entities/machine-learned-entity-with-subentities.png)

1. Selecione **criar** para concluir o processo de criação.

## <a name="add-a-feature-to-a-machine-learned-entity"></a>Adicionar um recurso a uma entidade aprendida pelo computador

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione a entidade aprendida do computador.
1. Adicione um recurso selecionando **+ Adicionar recurso** na linha entidade ou subentidade.
1. Selecione entre as listas de entidades e frases existentes.
1. Se a entidade só deve ser extraída se o recurso for encontrado, selecione o asterisco `*` para esse recurso.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição de recurso à entidade.](media/add-entities/machine-learned-entity-schema-with-features.png)

## <a name="create-a-regular-expression-entity"></a>Criar uma entidade de expressão regular

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione **+ criar**.

1. Na caixa de diálogo **criar um tipo de entidade** , digite o nome da entidade e selecione **Regex**, insira a expressão regular no campo **Regex** e selecione **criar**.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da criação de uma entidade de expressão regular.](media/add-entities/add-regular-expression-entity.png)


<a name="add-list-entities"></a>

## <a name="create-a-list-entity"></a>Criar uma entidade de listas

As entidades da lista representam um conjunto fixo e fechado de palavras relacionadas. Embora você, como autor, possa alterar a lista, o LUIS não aumentará nem reduzirá a lista. Você também pode importar para uma entidade de lista existente usando um [formato Entity. JSON de lista](reference-entity-list.md#example-json-to-import-into-list-entity).

A lista a seguir demonstra o nome canônico e os sinônimos.

|Nome do item da lista de cores|Sinônimos de cor|
|--|--|
|Vermelho|Crimson, sangue, Apple, mecanismo de incêndio|
|Azul|céu, Cobalt|
|Verde|Kelly, verde-limão|

Use o procedimento para criar uma entidade de lista. Depois que a entidade de lista for criada, você não precisará rotular declarações de exemplo em uma intenção. Itens de lista e sinônimos são correspondidos usando texto exato.
1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Na seção **Compilar** , selecione **entidades** no painel esquerdo e, em seguida, selecione **+ criar**.

1. Na caixa de diálogo **criar um tipo de entidade** , digite o nome da entidade, como `Colors` e selecione **lista**.
1. Na caixa de diálogo **criar uma entidade de lista** , em **Adicionar nova sublista...**, insira o nome do item de lista, como `Green` e, em seguida, adicione sinônimos.

    > [!div class="mx-imgBorder"]
    > ![Crie uma lista de cores como uma entidade de lista na página de detalhes da entidade.](media/how-to-add-entities/create-list-entity-of-colors.png)

1. Quando terminar de adicionar itens de lista e sinônimos, selecione **criar**.

    Quando você terminar com um grupo de alterações no aplicativo, lembre-se de **treinar** o aplicativo. Não treine o aplicativo após uma única alteração.

    > [!NOTE]
    > Este procedimento demonstra como criar e rotular uma entidade de lista a partir de um exemplo de expressão na página de **detalhes da intenção** . Você também pode criar a mesma entidade na página **entidades** .

## <a name="add-a-role-for-an-entity"></a>Adicionar uma função para uma entidade

Uma função é um subtipo nomeado de uma entidade, com base no contexto.

### <a name="add-a-role-to-distinguish-different-contexts"></a>Adicionar uma função para distinguir contextos diferentes

No expressão a seguir, há dois locais, e cada um é especificado semanticamente pelas palavras, como `to` e `from` :

`Pick up the package from Seattle and deliver to New York City.`

Neste procedimento, adicione `origin` funções e `destination` a uma entidade geographyV2 predefinida.
1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Na seção **Build**, selecione **Entities** no painel esquerdo.

1. Selecione **+ Adicionar entidade predefinida**. Selecione **geographyV2** e, em seguida, selecione **concluído**. Isso adiciona uma entidade predefinida ao aplicativo.

    Se você achar que seu padrão, quando ele incluir um Pattern.any, extrai as entidades incorretamente, use uma [lista explícita](reference-pattern-syntax.md#explicit-lists) para corrigir esse problema.

1. Selecione a entidade geographyV2 pré-criados recém-adicionada na lista de entidades da página **entidades** .
1. Para adicionar uma nova função, selecione **+** Avançar para **não adicionar funções**.
1. Na caixa de texto **função de tipo...** , insira o nome da função `Origin` e insira. Adicione um segundo nome de função `Destination` e, em seguida, Enter.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela da adição da função Origem à entidade Local](media/how-to-add-entities//add-role-to-prebuilt-geographyv2-entity.png)

    A função é adicionada à entidade predefinida, mas não é adicionada a nenhum declarações usando essa entidade.

### <a name="label-text-with-a-role-in-an-example-utterance"></a>Texto do rótulo com uma função em um exemplo de expressão

> [!TIP]
> As funções podem ser substituídas por rótulos com subentidades de entidades de aprendizado de máquina.

1. Entre no [portal do LUIS](https://www.luis.ai) selecione sua **Assinatura** e **Recurso de criação** para ver os aplicativos atribuídos a esse recurso.
1. Abra seu aplicativo selecionando seu nome na página **meus aplicativos** .
1. Vá para a página de detalhes da intenção, que tem exemplo declarações que usam a função.
1. Para rotular com a função, selecione o rótulo da entidade (linha sólida em texto) no exemplo expressão e selecione **Exibir no painel de entidade** na lista suspensa.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra a exibição no item de menu do painel de entidade selecionado.](media/add-entities/view-in-entity-pane.png)

    A paleta de entidades é aberta à direita.

1. Selecione a entidade e, em seguida, vá para a parte inferior da paleta e selecione a função.

    > [!div class="mx-imgBorder"]
    > ![Captura de tela mostra onde selecionar a função.](media/add-entities/select-role-in-entity-palette.png)

<a name="add-pattern-any-entities"></a>
<a name="add-a-patternany-entity"></a>
<a name="create-a-pattern-from-an-utterance"></a>

## <a name="create-a-patternany-entity"></a>Criar um padrão. qualquer entidade

O **padrão. qualquer** entidade está disponível apenas com [padrões](luis-how-to-model-intent-pattern.md).


## <a name="do-not-change-entity-type"></a>Não alterar tipo de entidade

O LUIS não permite alterar o tipo da entidade porque não sabe o que adicionar ou remover para construir essa entidade. Para alterar o tipo, é melhor criar uma nova entidade do tipo correto com um nome ligeiramente diferente. Depois que a entidade é criada, em cada enunciado, remova o antigo nome da entidade rotulada e adicione o novo nome da entidade. Depois que todos os enunciados tiverem sido remarcados, exclua a entidade antiga.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Usar modelos predefinidos](howto-add-prebuilt-models.md)

Saiba mais sobre:
* Como [treinar](luis-how-to-train.md)
* Como [testar](luis-interactive-test.md)
* Como [publicar](luis-how-to-publish-app.md)
* Padrões
    * [Conceitos](luis-concept-patterns.md)
    * [Sintaxe](reference-pattern-syntax.md)
* [Repositório GitHub de entidades predefinidas](https://github.com/Microsoft/Recognizers-Text)
* [Conceitos de extração de dados](luis-concept-data-extraction.md)



