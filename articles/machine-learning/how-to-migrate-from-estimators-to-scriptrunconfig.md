---
title: Migrar dos Avaliadores para o ScriptRunConfig
titleSuffix: Azure Machine Learning
description: Guia de migração para migrar de estimadores para ScriptRunConfig para configurar trabalhos de treinamento.
services: machine-learning
author: mx-iao
ms.author: minxia
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.date: 12/14/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: d603a12f851dac5b7cefc5bad728d42967bb27dc
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878588"
---
# <a name="migrating-from-estimators-to-scriptrunconfig"></a>Migrando de estimadores para ScriptRunConfig

Até agora, houve vários métodos para configurar um trabalho de treinamento no Azure Machine Learning por meio do SDK, incluindo estimadores, ScriptRunConfig e RunConfiguration de nível inferior.   Para resolver essa ambiguidade e inconsistência, estamos simplificando o processo de configuração do trabalho no Azure ML.  Agora você deve usar ScriptRunConfig como a opção recomendada para configurar trabalhos de treinamento. 

Os estimadores foram preteridos com a versão 1.19.0 do SDK do Python. Normalmente, você também deve evitar instanciar explicitamente um objeto RunConfiguration e, em vez disso, configurar seu trabalho usando a classe ScriptRunConfig.

Este artigo aborda considerações comuns ao migrar de estimadores para ScriptRunConfig.

> [!IMPORTANT]
> Para migrar para o ScriptRunConfig de estimados, verifique se você está usando >= 1.15.0 do SDK do Python.

## <a name="scriptrunconfig-documentation-and-samples"></a>Documentação e exemplos do ScriptRunConfig
Azure Machine Learning documentação e exemplos foram atualizados para usar o [ScriptRunConfig](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?preserve-view=true&view=azure-ml-py) para a configuração e o envio do trabalho.

Para obter informações sobre como usar o ScriptRunConfig, consulte a seguinte documentação:
* [Configurar e enviar execuções de treinamento](how-to-set-up-training-targets.md)
* [Configurando execuções de treinamento do PyTorch](how-to-train-pytorch.md)
* [Configurando execuções de treinamento do TensorFlow](how-to-train-tensorflow.md)
* [Configurando as execuções de treinamento scikit-Learn](how-to-train-scikit-learn.md)

Além disso, consulte os exemplos a seguir & tutoriais:
* [Azure/MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)
* [Azure/azureml-examples](https://github.com/Azure/azureml-examples)

## <a name="defining-the-training-environment"></a>Definindo o ambiente de treinamento
Embora os vários estimadores de estrutura tenham ambientes pré-configurados que são apoiados por imagens do Docker, os Dockerfiles para essas imagens são privados.  Portanto, você não tem muita transparência no que esses ambientes contêm. Além disso, os estimadores tomam configurações relacionadas ao ambiente como parâmetros individuais (como `pip_packages` , `custom_docker_image` ) em seus respectivos construtores.

Ao usar ScriptRunConfig, todas as configurações relacionadas ao ambiente são encapsuladas no `Environment` objeto que é passado para o `environment` parâmetro do Construtor ScriptRunConfig. Para configurar um trabalho de treinamento, forneça um ambiente que tenha todas as dependências necessárias para seu script de treinamento. Se nenhum ambiente for fornecido, o Azure ML usará uma das imagens base do Azure ML, especificamente a definida por `azureml.core.environment.DEFAULT_CPU_IMAGE` , como o ambiente padrão. Há duas maneiras de fornecer um ambiente:

* [Usar um ambiente organizado](how-to-use-environments.md#use-a-curated-environment) -os ambientes estruturados são ambientes predefinidos disponíveis em seu espaço de trabalho por padrão. Há um ambiente organizado correspondente para cada uma das imagens pré-configuradas da estrutura/versão do Docker que apoiaram cada estimador de estrutura.
* [Definir seu próprio ambiente personalizado](how-to-use-environments.md)

Aqui está um exemplo de como usar o ambiente PyTorch 1,6 organizado para treinamento:

```python
from azureml.core import Workspace, ScriptRunConfig, Environment

curated_env_name = 'AzureML-PyTorch-1.6-GPU'
pytorch_env = Environment.get(workspace=ws, name=curated_env_name)

compute_target = ws.compute_targets['my-cluster']
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env)
```

Se você quiser especificar **variáveis de ambiente** que serão definidas no processo em que o script de treinamento é executado, use o objeto de ambiente:
```
myenv.environment_variables = {"MESSAGE":"Hello from Azure Machine Learning"}
```

Para obter informações sobre como configurar e gerenciar ambientes do Azure ML, consulte:
* [Como usar ambientes](how-to-use-environments.md)
* [Ambientes selecionados](resource-curated-environments.md)
* [Treinar com uma imagem personalizada do Docker](how-to-train-with-custom-image.md)

## <a name="using-data-for-training"></a>Usando dados para treinamento
### <a name="datasets"></a>Conjunto de dados
Se você estiver usando um conjunto de um Azure ML para treinamento, passe o conjunto de um como um argumento para o seu script usando o `arguments` parâmetro. Ao fazer isso, você obterá o caminho de dados (ponto de montagem ou caminho de download) em seu script de treinamento por meio de argumentos.

O exemplo a seguir configura um trabalho de treinamento em que filedataset, `mnist_ds` , será montado na computação remota.
```python
src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', mnist_ds.as_mount()], # or mnist_ds.as_download() to download
                      compute_target=compute_target,
                      environment=pytorch_env)
```

### <a name="datareference-old"></a>Referência de data(antiga)
Embora seja recomendável usar conjuntos de trabalho do Azure ML por meio da antiga referência de datareferente, se você ainda estiver usando datareferenciations por qualquer motivo, deverá configurar seu trabalho da seguinte maneira:
```python
# if you want to pass a DataReference object, such as the below:
datastore = ws.get_default_datastore()
data_ref = datastore.path('./foo').as_mount()

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      arguments=['--data-folder', str(data_ref)], # cast the DataReference object to str
                      compute_target=compute_target,
                      environment=pytorch_env)
src.run_config.data_references = {data_ref.data_reference_name: data_ref.to_config()} # set a dict of the DataReference(s) you want to the `data_references` attribute of the ScriptRunConfig's underlying RunConfiguration object.
```

Para obter mais informações sobre como usar dados para treinamento, consulte:
* [Treinar com conjuntos de valores no Azure ML](./how-to-train-with-datasets.md)

## <a name="distributed-training"></a>Treinamento distribuído
Se você precisar configurar um trabalho distribuído para treinamento, faça isso especificando o `distributed_job_config` parâmetro no Construtor ScriptRunConfig. Passe um [MpiConfiguration](/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?preserve-view=true&view=azure-ml-py), [PyTorchConfiguration](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration?preserve-view=true&view=azure-ml-py)ou [TensorflowConfiguration](/python/api/azureml-core/azureml.core.runconfig.tensorflowconfiguration?preserve-view=true&view=azure-ml-py) para trabalhos distribuídos dos respectivos tipos.

O exemplo a seguir configura um trabalho de treinamento do PyTorch para usar o treinamento distribuído com MPI/Horovod:
```python
from azureml.core.runconfig import MpiConfiguration

src = ScriptRunConfig(source_directory='.',
                      script='train.py',
                      compute_target=compute_target,
                      environment=pytorch_env,
                      distributed_job_config=MpiConfiguration(node_count=2, process_count_per_node=2))
```

Para obter mais informações, consulte:
* [Treinamento distribuído com PyTorch](how-to-train-pytorch.md#distributed-training)
* [Treinamento distribuído com o TensorFlow](how-to-train-tensorflow.md#distributed-training)

## <a name="miscellaneous"></a>Diversos
Se você precisar acessar o objeto RunConfiguration subjacente para um ScriptRunConfig por qualquer motivo, você pode fazer isso da seguinte maneira:
```
src.run_config
```

## <a name="next-steps"></a>Próximas etapas

* [Configurar e enviar execuções de treinamento](how-to-set-up-training-targets.md)