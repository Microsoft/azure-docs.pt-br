---
title: Criar modelos de ML com o designer
titleSuffix: Azure Machine Learning
description: Saiba mais sobre a terminologia, os conceitos e o fluxo de trabalho que compõem o designer para Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: peterlu
author: peterclu
ms.date: 06/28/2020
ms.custom: designer
ms.openlocfilehash: e6738bf944c5a80d0cb54432ade7555ebdcfbd51
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503533"
---
# <a name="what-is-azure-machine-learning-designer"></a>O que é o designer do Azure Machine Learning? 


O designer do Azure Machine Learning permite conectar visualmente [conjuntos de dados](#datasets) e [módulos](#module) em uma tela interativa para criar modelos de aprendizado de máquina. Para saber como começar a usar o designer, confira o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)

![Exemplo do Azure Machine Learning Designer](./media/concept-designer/designer-drag-and-drop.gif)

O designer usa o [espaço de trabalho](concept-workspace.md) do Azure Machine Learning para organizar recursos compartilhados, como:

+ [Pipelines](#pipeline)
+ [Conjunto de dados](#datasets)
+ [Recursos de computação](#compute)
+ [Modelos registrados](concept-azure-machine-learning-architecture.md#models)
+ [Pipelines publicados](#publish)
+ [Pontos de extremidade em tempo real](#deploy)

## <a name="model-training-and-deployment"></a>Treinamento e implantação de modelo

O designer fornece uma tela visual para compilar, testar e implantar modelos de aprendizado de máquina. Com o designer, é possível:

+ Arrastar e soltar [conjuntos de dados](#datasets) e [módulos](#module) na tela.
+ Conecte os módulos para criar um [rascunho de pipeline](#pipeline-draft).
+ Enviar uma [execução de pipeline](#pipeline-run) usando os recursos de computação no workspace do Azure Machine Learning.
+ Converter os **pipelines de treinamento** em **pipelines de inferência**.
+ [Publique](#publish) seus pipelines em um **ponto de extremidade de pipeline** REST para enviar um novo pipeline que é executado com parâmetros e conjuntos de valores diferentes.
    + Publicar um **pipeline de treinamento** para reutilizar um único pipeline para treinar vários modelos, enquanto altera os parâmetros e conjuntos de dados.
    + Publicar um **pipeline de inferência de lote** para fazer previsões sobre novos dados usando um modelo treinado anteriormente.
+ [Implante](#deploy) um **pipeline de inferência em tempo real** para um ponto de extremidade em tempo real para fazer previsões sobre novos dados em tempo real.

![Diagrama de fluxo de trabalho para treinamento, inferência de lote e inferência em tempo real no designer](./media/concept-designer/designer-workflow-diagram.png)

## <a name="pipeline"></a>Pipeline

Um [pipeline](concept-azure-machine-learning-architecture.md#ml-pipelines) consiste em conjuntos de valores e módulos analíticos, que você se conecta. Os pipelines têm muitas utilidades: você pode criar um pipeline que treina um único modelo ou um que treina vários modelos. Você pode criar um pipeline que faça previsões em tempo real ou em lote, ou então crie um pipeline que limpa apenas os dados. Os pipelines permitem reutilizar o trabalho e organizar os projetos.

### <a name="pipeline-draft"></a>Rascunho de pipeline

À medida que você edita um pipeline no designer, seu progresso é salvo como um **rascunho de pipeline**. Você pode editar um rascunho de pipeline a qualquer momento adicionando ou removendo módulos, configurando destinos de computação, criando parâmetros e assim por diante.

Um pipeline válido tem estas características:

* Os conjuntos de dados só podem ser conectados a módulos.
* Os módulos só podem ser conectados a conjuntos de dados ou outros módulos.
* Todas as portas de entrada dos módulos devem ter uma conexão com o fluxo de dados.
* Todos os parâmetros necessários para cada módulo devem estar configurados.

Quando você estiver pronto para executar o rascunho de pipeline, envie uma execução de pipeline.

### <a name="pipeline-run"></a>Execução do pipeline

Cada vez que você executa um pipeline, a configuração do pipeline e seus resultados são armazenados no espaço de trabalho como uma **execução de pipeline**. Você pode voltar a qualquer execução de pipeline para inspecioná-la para fins de solução de problemas ou auditoria. **Clone** uma execução de pipeline para criar um novo rascunho de pipeline para edição.

As execuções de pipeline são agrupadas em [experimentos](concept-azure-machine-learning-architecture.md#experiments) para organizar o histórico de execuções. Você pode definir o experimento para cada execução de pipeline. 

## <a name="datasets"></a>Conjunto de dados

Um conjunto de dados do aprendizado de máquina facilita o acesso aos dados e o trabalho com eles. Vários conjuntos de exemplos de conjunto de valores são incluídos no designer para você experimentar. Você pode [registrar](how-to-create-register-datasets.md) mais conjuntos de dados conforme necessário.

## <a name="module"></a>Módulo

Um módulo é um algoritmo que você pode executar em seus dados. O designer tem vários módulos que vão desde funções de entrada de dados até processos de treinamento, pontuação e validação.

Um módulo pode ter um conjunto de parâmetros que você pode usar para configurar os algoritmos internos do módulo. Ao selecionar um módulo nas telas, os parâmetros do módulo são exibidos no painel Propriedades à direita das telas. Você pode modificar os parâmetros nesse painel para ajustar seu modelo. Você pode definir os recursos de computação para módulos individuais no designer. 

:::image type="content" source="./media/concept-designer/properties.png" alt-text="Propriedades do módulo":::


Para obter ajuda para navegar pela biblioteca de algoritmos disponíveis do aprendizado de máquina, confira [Visão geral sobre referência de algoritmos e módulos](algorithm-module-reference/module-reference.md). Para obter ajuda para escolher um algoritmo, confira a [Página de dicas úteis do algoritmo do Azure Machine Learning](algorithm-cheat-sheet.md).

## <a name="compute-resources"></a><a name="compute"></a> Recursos de computação

Use os recursos de computação do espaço de trabalho para executar o pipeline e hospedar os modelos implantados como pontos de extremidade em tempo real ou pontos de extremidade do pipeline (para inferência de lote). Os destinos de computação com suporte são:

| Destino de computação | Treinamento | Implantação |
| ---- |:----:|:----:|
| Computação do Azure Machine Learning | ✓ | |
| Instância de computação do Azure Machine Learning | ✓ | |
| Serviço de Kubernetes do Azure | | ✓ |

Os destinos de computação são anexados ao [workspace do Azure Machine Learning](concept-workspace.md). Você gerencia os destinos de computação no espaço de trabalho do [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="deploy"></a>Implantar

Para realizar a inferência em tempo real, você deve implantar um pipeline como **ponto de extremidade em tempo real**. O ponto de extremidade em tempo real cria uma interface entre um aplicativo externo e o modelo de pontuação. Uma chamada para um ponto de extremidade em tempo real retorna resultados de previsão para o aplicativo em tempo real. Para fazer uma chamada para um ponto de extremidade em tempo real, você passa a chave de API criada quando você implantou o ponto de extremidade. O ponto de extremidade baseia-se em REST, uma opção popular de arquitetura para projetos de programação da Web.

Os pontos de extremidade em tempo real devem ser implantados em um cluster do Serviço de Kubernetes do Azure.

Para saber como implantar o modelo, confira [Tutorial: Implantar um modelo de machine learning com o designer](tutorial-designer-automobile-price-deploy.md).

## <a name="publish"></a>Publicar

Você também pode publicar um pipeline para um **ponto de extremidade do pipeline**. Semelhante a um ponto de extremidade em tempo real, um ponto de extremidade do pipeline permite enviar novas execuções de pipeline de aplicativos externos usando chamadas REST. No entanto, não é possível enviar ou receber dados em tempo real usando um ponto de extremidade do pipeline.

Os pipelines publicados são flexíveis, podem ser usados ​​para treinar ou retreinar modelos, [realizar inferências de lote](how-to-run-batch-predictions-designer.md), processar novos dados e muito mais. Você pode publicar vários pipelines em um único ponto de extremidade do pipeline e especificar qual versão de pipeline deve ser executada.

Um pipeline publicado é executado nos recursos de computação que você define no rascunho de pipeline para cada módulo.

O designer cria o mesmo objeto [PublishedPipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline) que o SDK.

## <a name="next-steps"></a>Próximas etapas

* Conheça os conceitos fundamentais da análise preditiva e do machine learning com o [Tutorial: Prever preço de automóvel com o designer](tutorial-designer-automobile-price-train-score.md)
* Saiba como modificar as [amostras existentes do designer](samples-designer.md) para adaptá-las às suas necessidades.