---
title: Grupos de pastas de trabalho Azure Monitor
description: Como usar grupos em pastas de trabalho do Azure Monitor.
services: azure-monitor
author: lgayhardt
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: lagayhar
ms.openlocfilehash: b36dec79e5d60d97c11b9f8c74790527e1bd19fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731350"
---
# <a name="how-to-use-groups-in-workbooks"></a>Como usar grupos em pastas de trabalho

Um item de grupo em uma pasta de trabalho permite agrupar logicamente um conjunto de etapas em uma pasta de trabalho.

Os grupos nas pastas de trabalho são úteis para várias coisas:

- Layout
  - Em cenários em que você deseja que os itens sejam organizados verticalmente, você pode criar um grupo de itens que todos serão empilhados e definir o estilo do grupo para ser uma largura percentual em vez de definir a largura percentual em todos os itens individuais.
- Visibilidade
  - Em cenários em que você deseja que muitos itens sejam ocultados/mostrados juntos, você pode definir a visibilidade de todo o grupo de itens, em vez de definir as configurações de visibilidade em cada item individual. Isso pode ser útil em modelos que usam guias, pois você pode usar um grupo como o conteúdo da guia, e o grupo inteiro pode ser ocultado/mostrado com base em um parâmetro definido pela guia selecionada.
- Desempenho
  - Nos casos em que você tem um modelo muito grande com muitas seções ou guias, você pode converter cada seção em seu próprio submodelo e usar grupos para carregar todos os submodelos dentro do modelo de nível superior. O conteúdo dos submodelos não será carregado ou executado até que um usuário torne esses grupos visíveis. Saiba mais sobre [como dividir um modelo grande em vários modelos](#how-to-split-a-large-template-into-many-templates).

## <a name="using-groups"></a>Usando grupos

Para adicionar um grupo à pasta de trabalho, selecione *Adicionar* e *Adicionar grupo*.

![Selecione Adicionar e agrupar.](./media/workbooks-groups/add-group.png)

Veja abaixo uma captura de tela de um grupo no modo de leitura com dois itens dentro: um item de texto e um item de consulta.  

![Grupo no modo de leitura.](./media/workbooks-groups/groups-view.png)

Ao editar a pasta de trabalho, você pode ver que esses dois itens estão, na verdade, dentro de um item de Grupo:

![Grupo no modo de edição. ](./media/workbooks-groups/groups-edit.png)

Na captura de tela acima, o grupo está no modo de edição, mostrando que ele contém dois itens (dentro da área tracejada). Cada etapa pode estar no modo de edição ou leitura, independente um do outro. Por exemplo, a etapa de texto está no modo de edição enquanto a etapa de consulta está em modo de leitura.

## <a name="scoping"></a>Scoping

Atualmente, um grupo é tratado como um novo escopo na pasta de trabalho. Todos os parâmetros criados no grupo são visíveis apenas dentro do grupo. Isso também é válido para mesclagem, você só pode ver os dados dentro de seu grupo ou no nível pai.

## <a name="group-types"></a>Tipos de grupo

O item de grupo da pasta de trabalho permite que você adicione um grupo de itens a uma pasta de trabalho. Como o autor de uma pasta de trabalho, você especifica o tipo de grupo que será. Há dois tipos de grupos:

- Editável
  - O grupo na pasta de trabalho permite que você adicione, remova ou edite o conteúdo dos itens no grupo. Isso é mais comumente usado para fins de layout e visibilidade.
- Do modelo
  - O grupo na pasta de trabalho é carregado a partir do conteúdo de outro modelo por sua ID. O conteúdo desse modelo é carregado e mesclado na pasta de trabalho em tempo de execução. No modo de edição, não é possível modificar nenhum conteúdo do grupo, pois eles simplesmente serão carregados novamente a partir do modelo na próxima vez que o item for carregado.  

## <a name="load-types"></a>Tipos de carga

Há algumas maneiras diferentes que o conteúdo de um grupo pode ser carregado. Como o autor de uma pasta de trabalho, você obtém a especificação de quando e como o conteúdo de um grupo será carregado.

### <a name="lazy-the-default"></a>Lazy (o padrão)

O grupo será carregado somente quando o item estiver visível. Isso permite que um grupo seja usado por itens de guia. Se a guia nunca for selecionada, o grupo nunca ficará visível, portanto, o conteúdo não será carregado.

Para grupos criados por meio de um modelo, o conteúdo do modelo não é recuperado e os itens no grupo não são criados até que o grupo se torne visível. O usuário verá os esavanços de andamento para o grupo inteiro enquanto o conteúdo é recuperado.

### <a name="explicit"></a>Explícita

Nesse modo, um botão é exibido onde o grupo seria, e nenhum conteúdo é recuperado ou criado até que o usuário clique explicitamente no botão para carregar o conteúdo. Isso é útil em cenários em que o conteúdo pode ser caro para ser computado ou raramente usado. O autor pode especificar o texto a ser exibido no botão.

Veja abaixo uma captura de tela de configurações de carregamento explícitas mostrando um botão "carregar mais" configurado.

![Configurações de carregamento explícitas](./media/workbooks-groups/groups-explicitly-loaded.png)

O grupo antes de ser carregado na pasta de trabalho:

![Grupo explícito antes de ser carregado na pasta de trabalho](./media/workbooks-groups/groups-explicitly-loaded-before.png)

O grupo após ser carregado na pasta de trabalho:

![Grupo explícito após ser carregado em uma pasta de trabalho](./media/workbooks-groups/groups-explicitly-loaded-after.png)

### <a name="always"></a>Sempre

Nesse modo, o conteúdo do grupo é sempre carregado e criado assim que a pasta de trabalho é carregada. Isso é usado com mais frequência ao usar um grupo somente para fins de layout, em que o conteúdo sempre estará visível.

## <a name="using-templates-inside-a-group"></a>Usando modelos dentro de um grupo

Quando um grupo é configurado para carregar de um modelo, esse conteúdo será carregado por padrão, ele só será carregado quando o grupo estiver visível.

Quando um modelo é carregado em um grupo, a pasta de trabalho tenta mesclar todos os parâmetros declarados no modelo que está sendo carregado com parâmetros já existentes no grupo. Todos os parâmetros que já existem na pasta de trabalho com nomes idênticos serão mesclados do modelo que está sendo carregado. Se todos os parâmetros em uma etapa de parâmetro forem mesclados, a etapa de parâmetros inteira desaparecerá.

### <a name="example-1-all-parameters-have-identical-names"></a>Exemplo 1: todos os parâmetros têm nomes idênticos

Considere um modelo que tenha dois parâmetros na parte superior.

- `TimeRange` -um parâmetro de intervalo de tempo.
- `Filter` -um parâmetro de texto.

![Editando parâmetros item: "parâmetros de nível superior"](./media/workbooks-groups/groups-top-level-params.png)

Em seguida, um item de grupo carrega um segundo modelo que tem seus próprios parâmetros e uma etapa de texto, em que os parâmetros são nomeados da mesma:

![Editando um item de parâmetro para o segundo modelo](./media/workbooks-groups/groups-merged-away.png)

Quando o segundo modelo é carregado no grupo, os parâmetros duplicados serão mesclados. Como todos os parâmetros são mesclados, a etapa de parâmetros internos também é mesclada, resultando no grupo que contém apenas a etapa de texto.

### <a name="example-2-one-parameter-has-an-identical-name"></a>Exemplo 2: um parâmetro tem um nome idêntico

Considere o modelo de um grupo que tem dois parâmetros na parte superior.

- `TimeRange` -um parâmetro de intervalo de tempo.
- `FilterB` -um parâmetro de texto, observe que não é `Filter` como o modelo superior tinha.

![Editando um item de grupo com o resultado de parâmetros mesclados fora](./media/workbooks-groups/groups-wont-merge-away.png)

Quando o modelo item's do grupo for carregado, o `TimeRange` parâmetro será mesclado fora do grupo. Em seguida, a pasta de trabalho terá os parâmetros iniciais Step com `TimeRange` e `Filter` , e a etapa do parâmetro do grupo incluirá apenas `FilterB`

![resultado de parâmetros que não serão mesclados](./media/workbooks-groups/groups-wont-merge-away-result.png)

Se o modelo carregado tivesse sido contido `TimeRange` e `Filter` (em vez de `FilterB` ), a pasta de trabalho resultante teria uma etapa de parâmetros e um grupo com apenas a etapa de texto restante.

## <a name="how-to-split-a-large-template-into-many-templates"></a>Como dividir um modelo grande em muitos modelos

Para melhorar o desempenho, é benéfico dividir um modelo grande em vários modelos menores que carregam algum conteúdo lento ou sob demanda pelo usuário. Isso torna a carga inicial mais rápida, pois o modelo de nível superior pode ser muito menor.

Ao dividir um modelo em partes, você precisará efetivamente dividir o modelo em muitos modelos (submodelos) que todos trabalham individualmente. Portanto, se o modelo de nível superior tiver um `TimeRange` parâmetro que outras etapas usam, o submodelo também precisará ter uma etapa de parâmetros que define um parâmetro com o nome exato. Isso permite que os submodelos trabalhem de forma independente e permite que eles sejam carregados dentro de modelos maiores em grupos.

Para transformar um modelo maior em vários submodelos:

1.  Crie um novo grupo vazio próximo à parte superior da pasta de trabalho, após os parâmetros compartilhados. Esse novo grupo, eventualmente, se tornará um submodelo.
2. Crie uma cópia da etapa de parâmetros compartilhados e, em seguida, use *mover para o grupo* para mover a cópia para o grupo criado na etapa 1. Essa etapa de parâmetros permitirá que o submodelo funcione independentemente do modelo externo e será mesclado quando for carregado dentro do modelo externo.
    > [!NOTE]
    > O submodelo não precisa tecnicamente ter esses parâmetros que serão mesclados se você nunca pretender que os submodelos fiquem visíveis por si mesmos. No entanto, isso o tornará muito difícil de editar ou depurar se você precisar fazer isso mais tarde.

3. Mova cada item na pasta de trabalho que você deseja que esteja no submodelo para o grupo criado na etapa 1.
4. Se as etapas individuais movidas na etapa 3 tiverem visibilidades condicional, isso se tornará a visibilidade do grupo externo (como usado em guias). Remova-os dos itens dentro do grupo e adicione essa configuração de visibilidade ao próprio grupo. Salve aqui para evitar perder as alterações e/ou exportar e salvar uma cópia do conteúdo JSON.
5. Se desejar que esse grupo seja carregado a partir de um modelo, você poderá usar o botão *Editar* da barra de ferramentas no grupo. Isso abrirá apenas o conteúdo desse grupo como uma pasta de trabalho em uma nova janela. Você pode salvá-lo conforme apropriado e fechar este modo de exibição da pasta de trabalho (não feche o navegador, apenas essa exibição para voltar à pasta de trabalho anterior que você estava editando).
6. Em seguida, você pode alterar a etapa do grupo para carregar do modelo e definir o campo ID do modelo para a pasta de trabalho/modelo que você criou na etapa 5. Para trabalhar com IDs de pastas de trabalho, a origem precisa ser uma ID de recurso de pasta de trabalho compartilhada. Pressione *carregar* e o conteúdo desse grupo agora será carregado desse submodelo, em vez de salvo nessa pasta de trabalho externa.

## <a name="next-steps"></a>Próximas etapas
- [Visão geral das pastas de trabalho](./workbooks-overview.md)
- [Usando JSONPath com pastas de trabalho](workbooks-jsonpath.md)