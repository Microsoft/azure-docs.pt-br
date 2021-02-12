---
title: Monitoramento de fluxos de dados de mapeamento
description: Como monitorar visualmente o mapeamento de fluxos de dados no Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/22/2020
ms.openlocfilehash: 9ca5ea5cdebe297af5081ae6e219935c56ba942e
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96004850"
---
# <a name="monitor-data-flows"></a>Monitorar Fluxo de Dados

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Após concluir a criação e a depuração do fluxo de dados, recomendamos programá-lo para executar em um agendamento dentro do contexto de um pipeline. É possível agendar o pipeline a partir do Azure Data Factory usando Gatilhos. Ou, você pode usar a opção Disparar Agora do Construtor de Pipeline do Azure Data Factory para executar uma execução única e testar o fluxo de dados no contexto de pipeline.

Ao executar o pipeline, você poderá monitorar o pipeline e todas as atividades que ele contém, incluindo a atividade do Fluxo de Dados. Clique no ícone de monitoramento no painel esquerdo da interface do usuário do Azure Data Factory. Uma tela semelhante à seguinte será exibida. Os ícones realçados permitem que você analise detalhadamente as atividades no pipeline, incluindo a atividade de Fluxo de Dados.

![Captura de tela mostra ícones a serem selecionados para pipelines para obter mais informações.](media/data-flow/mon001.png "Monitoramento de fluxo de dados")

Você verá as estatísticas nesse nível, incluindo os tempos de execução e o status. A ID de execução no nível de atividade é diferente da ID de execução no nível de pipeline. A ID de Execução no nível anterior é para o pipeline. Selecionar o ícone de óculos permite examinar os detalhes da sua execução de fluxo de dados.

![Captura de tela mostra o ícone de óculos para ver detalhes da execução do fluxo de dados.](media/data-flow/monitoring-details.png "Monitoramento de fluxo de dados")

Quando estiver na exibição de monitoramento de nós de gráfico, você verá uma versão simplificada do grafo que não pode ser editada do fluxo de dados.

![Captura de tela mostra a versão somente exibição do grafo.](media/data-flow/mon003.png "Monitoramento de fluxo de dados")

Veja um vídeo de visão geral do desempenho de monitoramento dos seus fluxos de dados na tela de monitoramento do ADF:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u4mH]

## <a name="view-data-flow-execution-plans"></a>Exibir Planos de Execução do Fluxo de Dados

Quando o Fluxo de Dados é executado no Spark, o Azure Data Factory determina os caminhos de código ideais com base na totalidade do fluxo de dados. Além disso, os caminhos de execução podem ocorrer em diferentes nós de expansão e partições de dados. Portanto, o grafo de monitoramento representa o design do fluxo, levando em conta o caminho de execução de suas transformações. Ao selecionar nós individuais, você poderá ver “agrupamentos” que representam o código que foi executado junto no cluster. As contagens e intervalos exibidos representam esses grupos, ao contrário das etapas individuais no design.

![Captura de tela mostra a página de um fluxo de dados.](media/data-flow/mon004.png "Monitoramento de fluxo de dados")

* Ao selecionar o espaço aberto na janela de monitoramento, as estatísticas no painel inferior exibem as contagens de linha e tempo para cada coletor e as transformações que conduziram aos dados do coletor para a linhagem de transformação.

* Ao selecionar transformações individuais, você receberá comentários adicionais no painel direito que mostrará as estatísticas de partição, contagens de colunas, assimetria (como os dados estão distribuídos uniformemente em partições) e curtose (se os dados têm muitos picos).

* Ao selecionar o coletor na exibição do nó, a linhagem da coluna será exibida. Há três métodos diferentes em que as colunas são acumuladas ao longo do fluxo de dados para chegar ao Coletor. Eles são:

  * Computado: Você usa a coluna para processamento condicional ou dentro de uma expressão no fluxo de dados, mas não a coloca no coletor
  * Derivado: A coluna é uma nova coluna que você gerou no fluxo, ou seja, ela não estava presente na origem
  * Mapeado: A coluna é originada da origem e você está mapeando-a para um campo do coletor
  * Status do fluxo de dados: O status atual da execução
  * Tempo de inicialização do cluster: Tempo necessário para adquirir o ambiente de computação do Spark JIT para sua execução de fluxo de dados
  * Número de transformações: Quantas etapas de transformação estão sendo executadas no seu fluxo
  
![Captura de tela mostra a opção Atualizar.](media/data-flow/monitornew.png "Novo monitoramento do Fluxo de Dados")

## <a name="total-sink-processing-time-vs-transformation-processing-time"></a>Tempo total de processamento do coletor versus tempo de processamento da transformação

Cada estágio de transformação inclui um tempo total para que esse estágio seja concluído com cada tempo de execução de partição totalizado em conjunto. Ao clicar no coletor, você verá "tempo de processamento do coletor". Esse tempo inclui o total do tempo de transformação *mais* o tempo de e/s necessário para gravar seus dados no armazenamento de destino. A diferença entre o tempo de processamento do coletor e o total da transformação é o tempo de e/s para gravar os dados.

Você também pode ver o tempo detalhado para cada etapa de transformação de partição se abrir a saída JSON de sua atividade de fluxo de dados no modo de exibição de monitoramento do pipeline do ADF. O JSON contém um tempo de milissegundo para cada partição, enquanto o modo de exibição de monitoramento de UX é um tempo agregado de partições adicionadas juntas:

```
 {
     "stage": 4,
     "partitionTimes": [
          14353,
          14914,
          14246,
          14912,
          ...
         ]
}
```

### <a name="post-processing-time"></a>Tempo de pós-processamento

Quando você seleciona um ícone de transformação do coletor em seu mapa, o painel deslizante à direita mostrará um ponto de dados adicional chamado "tempo de processamento posterior" na parte inferior. Esse é o tempo gasto na execução do trabalho no cluster Spark *depois* que os dados são carregados, transformados e gravados. Esse tempo pode incluir o fechamento de pools de conexão, desligamento de driver, exclusão de arquivos, arquivos de União, etc. Ao executar ações em seu fluxo, como "mover arquivos" e "saída para um único arquivo", você provavelmente verá um aumento no valor de tempo de processamento posterior.
  
## <a name="error-rows"></a>Linhas de erro

Habilitar o tratamento de linha de erro no coletor de fluxo de dados será refletido na saída de monitoramento. Quando você define o coletor como "relatar êxito no erro", a saída de monitoramento mostrará o número de linhas de êxito e com falha quando você clicar no nó monitoramento do coletor.

![Captura de tela mostra linhas de erro.](media/data-flow/error-row-2.png "Êxito no monitoramento da linha de erro")

Quando você seleciona "relatar falha no erro", a mesma saída será mostrada apenas no texto de saída de monitoramento de atividade. Isso ocorre porque a atividade de fluxo de dados retornará falha para execução e a exibição de monitoramento detalhada não estará disponível.

![Captura de tela mostra linhas de erro na atividade.](media/data-flow/error-rows-4.png "Falha no monitoramento da linha de erro")

## <a name="monitor-icons"></a>Ícones de monitoramento

Esse ícone significa que os dados de transformação já foram armazenados em cache no cluster, portanto, o caminho de execução e os intervalos levaram isso em consideração:

![Captura de tela mostra o ícone de disco.](media/data-flow/mon005.png "Monitoramento de fluxo de dados")

Você também verá ícones de círculo verde na transformação. Eles representam uma contagem do número de coletores para os quais os dados estão fluindo.
