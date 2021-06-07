---
title: Movendo dados em pipelines de ML
titleSuffix: Azure Machine Learning
description: Saiba como Azure Machine Learning pipelines ingerirem dados e como gerenciar e mover dados entre as etapas do pipeline.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, devx-track-python, data4ml
ms.openlocfilehash: 0c4f1fbc0ed27dbf3b7347e0e4c12f1926ac1377
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566244"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Movendo dados para e entre as etapas de pipeline de ML (Python)

Este artigo fornece código para importação, transformação e movimentação de dados entre etapas em um pipeline de Azure Machine Learning. Para obter uma visão geral de como os dados funcionam no Azure Machine Learning, consulte [acessar dados nos serviços de armazenamento do Azure](how-to-access-data.md). Para obter os benefícios e a estrutura de pipelines de Azure Machine Learning, confira [o que são pipelines de Azure Machine Learning?](concept-ml-pipelines.md).

Esta artigo mostrará como:

- Usar `Dataset` objetos para dados preexistentes
- Acessar dados em suas etapas
- Dividir `Dataset` dados em subconjuntos, como subconjuntos de treinamento e validação
- Criar `OutputFileDatasetConfig` objetos para transferir dados para a próxima etapa do pipeline
- Usar `OutputFileDatasetConfig` objetos como entrada para etapas de pipeline
- Criar novos `Dataset` objetos a partir de `OutputFileDatasetConfig` sua wisƒh para persistir

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de:

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/intro) ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning.
  
  [Crie um workspace do Azure Machine Learning](how-to-manage-workspace.md) ou use um existente por meio do SDK do Python. Importe as classes `Workspace` e `Datastore` e carregue as informações de assinatura do arquivo `config.json` usando a função `from_config()`. Essa função procura o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alguns dados já existentes. Este artigo mostra resumidamente o uso de um [contêiner de blob do Azure](../storage/blobs/storage-blobs-overview.md).

- Opcional: um pipeline de Machine Learning existente, como o descrito em [criar e executar pipelines de Machine Learning com Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Usar `Dataset` objetos para dados preexistentes 

A maneira preferida de ingerir dados em um pipeline é usar um objeto [DataSet](/python/api/azureml-core/azureml.core.dataset%28class%29) . `Dataset` os objetos representam dados persistentes disponíveis em um espaço de trabalho.

Há várias maneiras de criar e registrar `Dataset` objetos. Os conjuntos de dados tabulares são para a disponibilidade delimitada em um ou mais arquivos. Os conjuntos de dados de arquivo são para dado binário (como imagens) ou para dados que você analisará. As maneiras programáticas mais simples de criar `Dataset` objetos são usar BLOBs existentes no armazenamento do espaço de trabalho ou URLs públicas:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

datastore_path = [
    DataPath(datastore, 'animals/dog/1.jpg'),
    DataPath(datastore, 'animals/dog/2.jpg'),
    DataPath(datastore, 'animals/cat/*.jpg')
]
cats_dogs_dataset = Dataset.File.from_files(path=datastore_path)
```

Para obter mais opções sobre como criar conjuntos de dados com opções diferentes e de fontes diferentes, registrá-los e analisá-los na interface do usuário do Azure Machine Learning, entender como o tamanho dos dados interage com a capacidade de computação e o controle de versão deles, consulte [criar conjuntos de Azure Machine Learning](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Passe conjuntos de valores para seu script

Para passar o caminho do conjunto de scripts para o script, use o `Dataset` método do objeto `as_named_input()` . Você pode passar o objeto resultante `DatasetConsumptionConfig` para o script como um argumento ou, usando o `inputs` argumento para o script de pipeline, você pode recuperar o conjunto de resultados usando `Run.get_context().input_datasets[]` .

Depois de criar uma entrada nomeada, você pode escolher seu modo de acesso: `as_mount()` ou `as_download()` . Se o seu script processar todos os arquivos em seu conjunto de recursos e o disco em seu recurso de computação for grande o suficiente para o conjunto de espaço, o modo de acesso de download será a melhor opção. O modo de acesso de download evitará a sobrecarga de transmitir os dados em tempo de execução. Se o script acessar um subconjunto do conjunto de um ou for muito grande para sua computação, use o modo de acesso de montagem. Para obter mais informações, leia [montar versus baixar](./how-to-train-with-datasets.md#mount-vs-download)

Para passar um conjunto de um DataSet para a etapa de pipeline:

1. Use `TabularDataset.as_named_input()` ou `FileDataset.as_named_input()` (não está ' no final) para criar um `DatasetConsumptionConfig` objeto
1. Use `as_mount()` ou `as_download()` para definir o modo de acesso
1. Passe os conjuntos de itens para suas etapas de pipeline usando o `arguments` argumento ou o `inputs`

O trecho a seguir mostra o padrão comum de combinar essas etapas dentro do `PythonScriptStep` Construtor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

> [!NOTE]
> Você precisa substituir os valores de todos esses argumentos (ou seja,,, `"train_data"` `"train.py"` `cluster` e `iris_dataset` ) pelos seus próprios dados. O trecho acima mostra apenas a forma da chamada e não faz parte de um exemplo da Microsoft. 

Você também pode usar métodos como `random_split()` e `take_sample()` para criar várias entradas ou reduzir a quantidade de dados passados para a etapa do pipeline:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Acessar conjuntos de os em seu script

As entradas nomeadas para o script de etapa de pipeline estão disponíveis como um dicionário dentro do `Run` objeto. Recupere o `Run` objeto ativo usando `Run.get_context()` e, em seguida, recupere o dicionário de entradas nomeadas usando `input_datasets` . Se você passou o `DatasetConsumptionConfig` objeto usando o `arguments` argumento em vez do `inputs` argumento, acesse os dados usando o `ArgParser` código. As duas técnicas são demonstradas no trecho a seguir.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

O valor transmitido será o caminho para os arquivos do conjunto de valores.

Também é possível acessar um registrado `Dataset` diretamente. Como os conjuntos de dado registrados são persistentes e compartilhados em um espaço de trabalho, você pode recuperá-los diretamente:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

> [!NOTE]
> Os trechos de código anteriores mostram a forma das chamadas e não fazem parte de um exemplo da Microsoft. Você deve substituir os vários argumentos por valores de seu próprio projeto.

## <a name="use-outputfiledatasetconfig-for-intermediate-data"></a>Usar `OutputFileDatasetConfig` para dados intermediários

Embora `Dataset` os objetos representem apenas dados persistentes, os [`OutputFileDatasetConfig`](/python/api/azureml-core/azureml.data.outputfiledatasetconfig) objetos podem ser usados para saída de dados temporários de etapas de pipeline **e** dados de saída persistentes. `OutputFileDatasetConfig` dá suporte à gravação de dados no armazenamento de BLOBs, FileShare, adlsgen1 ou adlsgen2. Ele dá suporte ao modo de montagem e ao modo de carregamento. No modo de montagem, os arquivos gravados no diretório montado são armazenados permanentemente quando o arquivo é fechado. No modo de carregamento, os arquivos gravados no diretório de saída são carregados no final do trabalho. Se o trabalho falhar ou for cancelado, o diretório de saída não será carregado.

 `OutputFileDatasetConfig` o comportamento padrão do objeto é gravar no repositório de armazenamento padrão do espaço de trabalho. Passe seus `OutputFileDatasetConfig` objetos para o `PythonScriptStep` com o `arguments` parâmetro.

```python
from azureml.data import OutputFileDatasetConfig
dataprep_output = OutputFileDatasetConfig()
input_dataset = Dataset.get_by_name(workspace, 'raw_data')

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=[input_dataset.as_named_input('raw_data').as_mount(), dataprep_output]
    )
```

Você pode optar por carregar o conteúdo do seu `OutputFileDatasetConfig` objeto no final de uma execução. Nesse caso, use a `as_upload()` função junto com seu `OutputFileDatasetConfig` objeto e especifique se deseja substituir os arquivos existentes no destino. 

```python
#get blob datastore already registered with the workspace
blob_store= ws.datastores['my_blob_store']
OutputFileDatasetConfig(name="clean_data", destination=(blob_store, 'outputdataset')).as_upload(overwrite=False)
```

> [!NOTE]
> As gravações simultâneas em um `OutputFileDatasetConfig` falharão. Não tente usar um único `OutputFileDatasetConfig` simultaneamente. Não compartilhe um único `OutputFileDatasetConfig` em uma situação de multiprocessamento, como ao usar o treinamento distribuído. 

### <a name="use-outputfiledatasetconfig-as-outputs-of-a-training-step"></a>Usar `OutputFileDatasetConfig` como saídas de uma etapa de treinamento

No `PythonScriptStep` do pipeline, você pode recuperar os caminhos de saída disponíveis usando os argumentos do programa. Se esta etapa for a primeira e iniciar os dados de saída, você deve criar o diretório no caminho especificado. Em seguida, você pode gravar quaisquer arquivos que queira que estejam contidos no `OutputFileDatasetConfig` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

### <a name="read-outputfiledatasetconfig-as-inputs-to-non-initial-steps"></a>Ler `OutputFileDatasetConfig` como entradas para etapas não iniciais

Depois que a etapa inicial do pipeline grava alguns dados no `OutputFileDatasetConfig` caminho e se torna uma saída dessa etapa inicial, ela pode ser usada como uma entrada para uma etapa posterior. 

No seguinte código: 

* `step1_output_data` indica que a saída do PythonScriptStep, `step1` é gravada no repositório de armazenamento do ADLS Gen 2, `my_adlsgen2` no modo carregar acesso. Saiba mais sobre como [configurar permissões de função](how-to-access-data.md#azure-data-lake-storage-generation-2) a fim de gravar dados de volta nos armazenamentos de ADLS Gen 2. 

* Após `step1` a conclusão e a saída ser gravada no destino indicado por `step1_output_data` , etapa 2 estará pronto para ser usado `step1_output_data` como uma entrada. 

```python
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']
step1_output_data = OutputFileDatasetConfig(name="processed_data", destination=(datastore, "mypath/{run-id}/{output-name}")).as_upload()

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data.as_input()]

)

pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

## <a name="register-outputfiledatasetconfig-objects-for-reuse"></a>Registrar `OutputFileDatasetConfig` objetos para reutilização

Se você quiser disponibilizar seu `OutputFileDatasetConfig` período por mais tempo do que o seu experimento, registre-o em seu espaço de trabalho para compartilhar e reutilizar entre experimentos.

```python
step1_output_ds = step1_output_data.register_on_complete(name='processed_data', 
                                                         description = 'files from step1`)
```

## <a name="delete-outputfiledatasetconfig-contents-when-no-longer-needed"></a>Excluir `OutputFileDatasetConfig` conteúdo quando não for mais necessário

O Azure não exclui automaticamente os dados intermediários gravados com o `OutputFileDatasetConfig` . Para evitar encargos de armazenamento para grandes quantidades de dados desnecessários, você deve:

* Exclua programaticamente dados intermediários no final de uma execução de pipeline, quando ele não for mais necessário
* Use o armazenamento de BLOBs com uma política de armazenamento de curto prazo para dados intermediários (consulte [otimizar custos automatizando as camadas de acesso do armazenamento de BLOBs do Azure](../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)) 
* Examine regularmente e exclua dados não mais necessários

Para obter mais informações, consulte [planejar e gerenciar custos para Azure Machine Learning](concept-plan-manage-cost.md).

## <a name="next-steps"></a>Próximas etapas

* [Criar um conjunto de dados do Azure Machine Learning](how-to-create-register-datasets.md)
* [Crie e execute pipelines de machine learning com o SDK do Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)