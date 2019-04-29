---
title: Monitoramento Visual de Fluxo de Dados do Mapeamento do Azure Data Factory
description: Como monitorar visualmente os Fluxo de Dados do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 90aa6261aebb9d1f7da89c101854bad8061dd6ff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61268952"
---
# <a name="monitor-data-flows"></a>Monitorar Fluxo de Dados

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Após concluir a criação e depuração do fluxo de dados, é altamente recomendável programar o fluxo de dados para executar em um agendamento dentro do contexto de um pipeline. É possível agendar o pipeline a partir do Azure Data Factory usando Gatilhos. Ou, você pode usar a opção Disparar Agora do Construtor de Pipeline do Azure Data Factory para executar uma execução única e testar o fluxo de dados no contexto de pipeline.

Ao executar o pipeline, você poderá monitorar o pipeline e todas as atividades contidas no pipeline, incluindo a atividade de Fluxo de Dados. Clique no ícone de monitoramento no painel esquerdo da interface do usuário do Azure Data Factory. Uma tela semelhante à seguinte será exibida. Os ícones realçados permitirão que você analise detalhadamente as atividades no pipeline, incluindo a atividade de Fluxo de Dados.

<img src="media/data-flow/mon001.png" width="800">

Você verá as estatísticas nesse nível, incluindo os tempos de execução e o status. A ID de Execução no nível de atividade é diferente da ID de Execução no nível de pipeline. A ID de Execução no nível anterior é para o pipeline. Ao clicar no ícone de óculos, os detalhes sobre a execução do fluxo de dados serão exibidos detalhadamente.

<img src="media/data-flow/mon002.png" width="800">

Quando você estiver na exibição de monitoramento de nós de gráfico, uma versão somente exibição simplificada do grafo do fluxo de dados será exibida.

<img src="media/data-flow/mon003.png" width="800">

## <a name="view-data-flow-execution-plans"></a>Exibir Planos de Execução do Fluxo de Dados

Quando o Fluxo de Dados é executado em Databricks, o Azure Data Factory determina os caminhos de código ideais com base na totalidade do fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de expansão e partições de dados. Portanto, o grafo de monitoramento representa o design do fluxo, levando em conta o caminho de execução de suas transformações. Ao clicar em nós individuais, você verá "agrupamentos" que representam o código que foi executado em conjunto no cluster. As contagens e intervalos exibidos representam esses grupos, ao contrário das etapas individuais no design.

<img src="media/data-flow/mon004.png" width="800"> 

* Ao clicar no espaço aberto na janela de monitoramento, as estatísticas no painel inferior exibem as contagens de linha e tempo para cada Coletor e as transformações que conduziram aos dados do coletor para a linhagem de transformação.

* Ao selecionar transformações individuais, você receberá comentários adicionais no painel direito que mostrará as estatísticas de partição, contagens de colunas, assimetria (como os dados estão distribuídos uniformemente em partições) e curtose (o quão "pontiagudo" estão os dados).

* Ao clicar no Coletor na exibição do nó, a linhagem da coluna é exibida. Há três métodos diferentes em que as colunas são acumuladas ao longo do fluxo de dados para chegar ao Coletor. Eles são:

  * Computado: Você usa a coluna para processamento condicional ou dentro de uma expressão no fluxo de dados, mas não a coloca no Coletor
  * Derivado: A coluna é uma nova coluna que você gerou no fluxo, ou seja, ela não estava presente na Origem
  * Mapeado: A coluna é originada da origem e você está mapeando-a para um campo do coletor
  
## <a name="monitor-icons"></a>Ícones de monitoramento

Esse ícone significa que os dados de transformação já foram armazenados em cache no cluster, portanto, o caminho de execução e os intervalos levaram isso em consideração:

<img src="media/data-flow/mon005.png" width="800"> 

Você também verá ícones de círculo verde na transformação. Eles representam uma contagem do número de coletores para os quais os dados estão fluindo.
