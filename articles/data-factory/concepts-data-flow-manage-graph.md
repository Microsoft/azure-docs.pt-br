---
title: Gerenciando o grafo de fluxo de dados de mapeamento
description: Como gerenciar e editar com eficiência o grafo de fluxo de dados de mapeamento
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2020
ms.openlocfilehash: f55364fe0eabae4bb1698bd655cde8d3e01b127a
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590453"
---
# <a name="managing-the-mapping-data-flow-graph"></a>Gerenciando o grafo de fluxo de dados de mapeamento

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

O mapeamento de fluxos de dados é criado usando uma superfície de design conhecida como grafo de fluxo de dados. No grafo, a lógica de transformação é criada da esquerda para a direita e os fluxos de dados adicionais são adicionados de cima para baixo. Para adicionar uma nova transformação, selecione o sinal de adição no canto inferior direito de uma transformação existente.

![Tela](media/data-flow/canvas-2.png)

À medida que seus fluxos de dados ficam mais complexos, use os mecanismos a seguir para navegar e gerenciar com eficiência o grafo de fluxo de dados. 

## <a name="moving-transformations"></a>Movendo transformações

No mapeamento de fluxos de dados, um conjunto de lógica de transformação conectada é conhecido como um **fluxo**. O campo **fluxo de entrada** determina qual fluxo de dados está alimentando a transformação atual. Cada transformação tem um ou dois fluxos de entrada, dependendo de sua função e representa um fluxo de saída. O esquema de saída dos fluxos de entrada determina quais metadados de coluna podem ser referenciados pela transformação atual.

![Mover nó](media/data-flow/move-nodes.png "mover nó")

Ao contrário da tela de pipeline, as transformações de fluxo de dados não são editadas usando um modelo de arrastar e soltar. Para alterar o fluxo de entrada ou "mover" uma transformação, escolha um valor diferente na lista suspensa **fluxo de entrada** . Quando você fizer isso, todas as transformações de downstream serão movidas junto com a transformação editada. O grafo será atualizado automaticamente para mostrar o novo fluxo lógico. Se você alterar o fluxo de entrada para uma transformação que já tem a transformação downstream, um novo Branch ou fluxo de dados paralelos será criado. Saiba mais sobre [novas ramificações no fluxo de dados de mapeamento](data-flow-new-branch.md).

## <a name="hide-graph-and-show-graph"></a>Ocultar grafo e mostrar grafo

Ao editar sua transformação, você pode expandir o painel de configuração para ocupar a tela inteira, ocultando o grafo. Clique na divisa voltada para cima localizada no lado direito da tela.

![Ocultar grafo](media/data-flow/hide-graph.png "Ocultar grafo")

Quando o grafo está oculto, você pode mover entre transformações em um fluxo clicando em **Avançar** ou **anterior**. Clique na divisa voltada para baixo para mostrar o grafo.

![Mostrar grafo](media/data-flow/show-graph.png "Mostrar grafo")

## <a name="searching-for-transformations"></a>Procurando transformações

Para localizar rapidamente uma transformação em seu grafo, clique no ícone de **pesquisa** acima da configuração de zoom.

![Captura de tela mostra o botão Pesquisar.](media/data-flow/search-1.png "Grafo de pesquisa")

Você pode pesquisar por nome de transformação ou descrição para localizar uma transformação.

![Captura de tela mostra a caixa de texto Pesquisar.](media/data-flow/search-2.png "Grafo de pesquisa")

## <a name="hide-reference-nodes"></a>Ocultar nós de referência

Se o fluxo de dados tiver todas as transformações de junção, pesquisa, existência ou União, o fluxo de dados mostrará os nós de referência para todos os fluxos de entrada. Se desejar minimizar a quantidade de espaço vertical obtida, você poderá minimizar seus nós de referência. Para fazer isso, clique com o botão direito do mouse na tela e selecione **ocultar nós de referência**.

![Ocultar nós de referência](media/data-flow/hide-reference-nodes.png "Ocultar nós de referência")

## <a name="next-steps"></a>Próximas etapas

Depois de concluir a lógica de fluxo de dados, ative o [modo de depuração](concepts-data-flow-debug-mode.md) e teste-o em uma visualização de dados.
