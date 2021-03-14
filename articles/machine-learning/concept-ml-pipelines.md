---
title: O que são pipelines de aprendizado de máquina?
titleSuffix: Azure Machine Learning
description: Saiba como os pipelines do Machine Learning ajudam a criar, otimizar e gerenciar fluxos de trabalho de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.custom: devx-track-python
ms.openlocfilehash: 584e421b6beac0e4ecfab5b3e3cb735b8465e1b4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503514"
---
# <a name="what-are-azure-machine-learning-pipelines"></a>O que são pipelines Azure Machine Learning?

Neste artigo, você aprenderá como um pipeline do Machine Learning ajuda a criar, otimizar e gerenciar seu fluxo de trabalho de aprendizado de máquina. 

<a name="compare"></a>
## <a name="which-azure-pipeline-technology-should-i-use"></a>Qual tecnologia de pipeline do Azure devo usar?

A nuvem do Azure fornece vários tipos de pipeline, cada um com uma finalidade diferente. A tabela a seguir lista os pipelines diferentes e para que eles são usados:

| Cenário | Persona primário | Oferta do Azure | Oferta de OSS | Pipe canônico | Pontos fortes | 
| -------- | --------------- | -------------- | ------------ | -------------- | --------- | 
| Orquestração de modelo (Machine Learning) | Cientista de dados | Pipelines do Azure Machine Learning | Pipelines do Kubeflow | Modelo de > de dados | Distribuição, cache, primeiro código, reutilização | 
| Orquestração de dados (preparação de dados) | Engenheiro de dados | [Pipelines do Azure Data Factory](../data-factory/concepts-pipelines-activities.md) | Fluxo de ar do Apache | Dados > dados | Movimentação com rigidez de tipos, atividades centradas em dados |
| Código & orquestração de aplicativo (CI/CD) | Desenvolvedor de aplicativos/Ops | [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) | Jenkins | Código + modelo – aplicativo/serviço > | Mais suporte a atividades abertas e flexíveis, filas de aprovação, fases com a retenção | 

## <a name="what-can-machine-learning-pipelines-do"></a>O que os pipelines de Machine Learning podem fazer?

Um pipeline de Azure Machine Learning é um fluxo de trabalho executável independente de uma tarefa de aprendizado de máquina completa. As subtarefas são encapsuladas como uma série de etapas no pipeline. Um pipeline Azure Machine Learning pode ser tão simples quanto um que chama um script Python, portanto, _pode_ fazer praticamente qualquer coisa. Os pipelines _devem_ se concentrar em tarefas de aprendizado de máquina, como:

+ Preparação de dados, incluindo importação, validação e limpeza, mudanças irreversíveis e transformação, normalização e preparo
+ Configuração de treinamento, incluindo argumentos de parametrização, filePaths e configurações de log/relatório
+ Treinamento e validação de forma eficiente e repetida. A eficiência pode vir de especificar subconjuntos de dados específicos, diferentes recursos de computação de hardware, processamento distribuído e monitoramento de progresso
+ Implantação, incluindo controle de versão, dimensionamento, provisionamento e controle de acesso

As etapas independentes permitem que vários cientistas de dados funcionem no mesmo pipeline ao mesmo tempo sem sobrecarregar recursos de computação. Etapas separadas também facilitam o uso de diferentes tipos/tamanhos de computação para cada etapa.

Depois que o pipeline é criado, muitas vezes, há mais ajustes finos no loop de treinamento do pipeline. Quando você executa novamente um pipeline, a execução salta para as etapas que precisam ser executadas novamente, como um script de treinamento atualizado. As etapas que não precisam ser executadas novamente são ignoradas. 

Com pipelines, você pode optar por usar hardware diferente para tarefas diferentes. O Azure coordena os vários [destinos de computação](concept-azure-machine-learning-architecture.md) que você usa, de modo que seus dados intermediários fluem diretamente para destinos de computação downstream.

Você pode [acompanhar as métricas de seus experimentos de pipeline](./how-to-track-experiments.md) diretamente no portal do Azure ou na [página de aterrissagem do espaço de trabalho (versão prévia)](https://ml.azure.com). Depois que um pipeline tiver sido publicado, você poderá configurar um ponto de extremidade REST, que permite executar novamente o pipeline de qualquer plataforma ou pilha.

Em suma, todas as tarefas complexas do ciclo de vida do Machine Learning podem ser ajudado com pipelines. Outras tecnologias de pipeline do Azure têm seus próprios pontos fortes. [Azure data Factory pipelines](../data-factory/concepts-pipelines-activities.md) os excels em trabalhando com dados e [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/) é a ferramenta certa para integração e implantação contínuas. Mas se o seu foco for aprendizado de máquina, Azure Machine Learning pipelines provavelmente serão a melhor opção para suas necessidades de fluxo de trabalho. 

### <a name="analyzing-dependencies"></a>Analisando dependências

Muitos ecossistemas de programação têm ferramentas que orquestram dependências de recursos, biblioteca ou compilação. Em geral, essas ferramentas usam carimbos de data/hora de arquivo para calcular dependências. Quando um arquivo é alterado, apenas ele e seus dependentes são atualizados (baixados, recompilados ou empacotados). Azure Machine Learning pipelines estendem esse conceito. Como as ferramentas de Build tradicionais, os pipelines calculam as dependências entre as etapas e só executam os recálculos necessários. 

No entanto, a análise de dependência em pipelines de Azure Machine Learning é mais sofisticada do que carimbos de data/hora simples. Cada etapa pode ser executada em um ambiente de hardware e software diferente. A preparação de dados pode ser um processo demorado, mas não precisa ser executada em hardware com GPUs poderosas, determinadas etapas podem exigir software específico do sistema operacional, talvez você queira usar o treinamento distribuído e assim por diante. 

Azure Machine Learning orquestra automaticamente todas as dependências entre as etapas do pipeline. Essa orquestração pode incluir a rotação de imagens do Docker para cima e para baixo, anexando e desanexando recursos de computação e movendo dados entre as etapas de maneira consistente e automática.

### <a name="coordinating-the-steps-involved"></a>Coordenando as etapas envolvidas

Quando você cria e executa um `Pipeline` objeto, ocorrem as seguintes etapas de alto nível:

+ Para cada etapa, o serviço calcula os requisitos para:
    + Recursos de computação de hardware
    + Recursos do sistema operacional (imagem (ns) do Docker)
    + Recursos de software (dependências de Conda/virtualenv)
    + Entradas de dados 
+ O serviço determina as dependências entre etapas, resultando em um grafo de execução dinâmica
+ Quando cada nó no grafo de execução é executado:
    + O serviço configura o ambiente de hardware e software necessário (talvez reutilizando os recursos existentes)
    + A etapa é executada, fornecendo registro em log e informações de monitoramento para seu `Experiment` objeto recipiente
    + Quando a etapa for concluída, suas saídas serão preparadas como entradas para a próxima etapa e/ou gravadas no armazenamento
    + Os recursos que não são mais necessários são finalizados e desanexados

![Etapas de pipeline](./media/concept-ml-pipelines/run_an_experiment_as_a_pipeline.png)

## <a name="building-pipelines-with-the-python-sdk"></a>Criando pipelines com o SDK do Python

No [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/install), um pipeline é um objeto Python definido no `azureml.pipeline.core` módulo. Um objeto de [pipeline](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29) contém uma sequência ordenada de um ou mais objetos [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.builder.pipelinestep) . A `PipelineStep` classe é abstrata e as etapas reais serão de subclasses, como [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep), [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.pythonscriptstep)ou [DataTransferStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep). A classe [ModuleStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep) mantém uma sequência reutilizável de etapas que podem ser compartilhadas entre pipelines. Um `Pipeline` é executado como parte de um `Experiment` .

Um pipeline do Azure Machine Learning está associado a um espaço de trabalho Azure Machine Learning e uma etapa de pipeline está associada a um destino de computação disponível nesse espaço de trabalho. Para obter mais informações, consulte [criar e gerenciar espaços de trabalho do Azure Machine Learning no portal do Azure](./how-to-manage-workspace.md) ou [quais são os destinos de computação no Azure Machine Learning?](./concept-compute-target.md).

### <a name="a-simple-python-pipeline"></a>Um pipeline Python simples

Este trecho de código mostra os objetos e as chamadas necessárias para criar e executar um `Pipeline` :

```python
ws = Workspace.from_config() 
blob_store = Datastore(ws, "workspaceblobstore")
compute_target = ws.compute_targets["STANDARD_NC6"]
experiment = Experiment(ws, 'MyExperiment') 

input_data = Dataset.File.from_files(
    DataPath(datastore, '20newsgroups/20news.pkl'))
prepped_data_path = OutputFileDatasetConfig(name="output_path")

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    source_directory="prep_src",
    compute_target=compute_target,
    arguments=["--prepped_data_path", prepped_data_path],
    inputs=[input_dataset.as_named_input('raw_data').as_mount() ]
    )

prepped_data = prepped_data_path.read_delimited_files()

train_step = PythonScriptStep(
    name="train",
    script_name="train.py",
    compute_target=compute_target,
    arguments=["--prepped_data", prepped_data],
    source_directory="train_src"
)
steps = [ dataprep_step, train_step ]

pipeline = Pipeline(workspace=ws, steps=steps)

pipeline_run = experiment.submit(pipeline)
pipeline_run.wait_for_completion()
```

O trecho de código começa com objetos Azure Machine Learning comuns, a `Workspace` , a `Datastore` , um [ComputeTarget](/python/api/azureml-core/azureml.core.computetarget)e um `Experiment` . Em seguida, o código cria os objetos a serem guardados `input_data` e `prepped_data_path` . O `input_data` é uma instância de [filedataset](/python/api/azureml-core/azureml.data.filedataset) e `prepped_data_path` é uma instância de  [OutputFileDatasetConfig](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig). Para `OutputFileDatasetConfig` o comportamento padrão é copiar a saída para o `workspaceblobstore` repositório de armazenamento no caminho `/dataset/{run-id}/{output-name}` , em que `run-id` é a ID da execução e `output-name` é um valor gerado automaticamente se não for especificado pelo desenvolvedor.

O código de preparação de dados (não mostrado), grava arquivos delimitados no `prepped_data_path` . Essas saídas da etapa de preparação de dados são passadas `prepped_data` para a etapa de treinamento. 

A matriz `steps` contém os dois `PythonScriptStep` s `dataprep_step` e `train_step` . Azure Machine Learning analisará a dependência de dados de `prepped_data` e executar `dataprep_step` antes `train_step` . 

Em seguida, o código instancia o `Pipeline` objeto em si, passando o espaço de trabalho e a matriz de etapas. A chamada para `experiment.submit(pipeline)` começa a execução do pipeline do Azure ml. A chamada para `wait_for_completion()` blocos até que o pipeline seja concluído. 

Para saber mais sobre como conectar seu pipeline a seus dados, confira os artigos [acesso a dados em Azure Machine Learning](concept-data.md) e [movendo dados para e entre as etapas de pipeline do ml (Python)](how-to-move-data-in-out-of-pipelines.md). 

## <a name="building-pipelines-with-the-designer"></a>Criando pipelines com o designer

Os desenvolvedores que preferem uma superfície de Design Visual podem usar o designer de Azure Machine Learning para criar pipelines. Você pode acessar essa ferramenta por meio da seleção de **Designer** na home page do seu espaço de trabalho.  O designer permite que você arraste e solte as etapas na superfície de design. 

Quando você cria pipelines visualmente, as entradas e saídas de uma etapa são exibidas visivelmente. Você pode arrastar e soltar conexões de dados, permitindo que você entenda rapidamente e modifique o fluxo de armazenamento de seu pipeline.

![Exemplo do Azure Machine Learning Designer](./media/concept-designer/designer-drag-and-drop.gif)

## <a name="key-advantages"></a>Principais vantagens

As principais vantagens de usar pipelines para seus fluxos de trabalho de aprendizado de máquina são:

|Principal vantagem|Descrição|
|:-------:|-----------|
|**Execuções&nbsp;autônomas**|Agende as etapas para execução em paralelo ou em sequência de maneira confiável e autônoma. A preparação e a modelagem de dados podem durar dias ou semanas, e os pipelines permitem que você se concentre em outras tarefas enquanto o processo está em execução. |
|**Computação heterogênea**|Use vários pipelines que são coordenados de forma confiável entre os recursos de computação heterogêneos e escalonáveis e os locais de armazenamento. Faça uso eficiente dos recursos de computação disponíveis executando etapas de pipeline individuais em diferentes destinos de computação, como o HDInsight, VMs de ciência de dados de GPU e databricks.|
|**Reutilização**|Crie modelos de pipeline para cenários específicos, como retreinamento e pontuação de lote. Disparar pipelines publicados de sistemas externos por meio de chamadas REST simples.|
|**Acompanhamento e controle de versão**|Em vez de acompanhar manualmente os caminhos de dados e resultados durante a iteração, use o SDK de pipelines para fornecer explicitamente o nome e a versão de fontes de dados, entradas e saídas. Você também pode gerenciar os scripts e os dados separadamente para aumentar a produtividade.|
| **Modularidade** | Separar áreas de preocupações e isolar alterações permite que o software evolua a uma taxa mais rápida com maior qualidade. | 
|**Colaboração**|Os pipelines permitem que os cientistas de dados colaborem em todas as áreas do processo de design do Machine Learning, ao mesmo tempo em que podem trabalhar simultaneamente em etapas de pipeline.|

## <a name="next-steps"></a>Próximas etapas

Azure Machine Learning pipelines são um recurso poderoso que começa a fornecer valor nos primeiros estágios de desenvolvimento. O valor aumenta conforme a equipe e o projeto crescem. Este artigo explicou como pipelines são especificados com o SDK Azure Machine Learning Python e orquestrados no Azure. Você viu um código-fonte simples e foi introduzido em algumas das `PipelineStep` classes disponíveis. Você deve ter uma noção de quando usar Azure Machine Learning pipelines e como o Azure os executa. 

+ Saiba como [criar seu primeiro pipeline](./how-to-create-machine-learning-pipelines.md).

+ Saiba como [executar previsões em lote em dados grandes](tutorial-pipeline-batch-scoring-classification.md ).

+ Consulte os documentos de referência do SDK para [pipeline Core](/python/api/azureml-pipeline-core/) e [as etapas de pipeline](/python/api/azureml-pipeline-steps/).

+ Experimente o exemplo Jupyter notebooks mostrando [Azure Machine Learning pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/machine-learning-pipelines). Saiba como [executar blocos de anotações para explorar esse serviço](samples-notebooks.md).