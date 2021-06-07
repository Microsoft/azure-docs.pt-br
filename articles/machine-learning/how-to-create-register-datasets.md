---
title: Criar conjuntos de dados no Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Saiba como criar conjuntos de dados de Azure Machine Learning para acessar seus dados para execuções de experimento de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, data4ml
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.openlocfilehash: 81779d942b31f940d579de623ecb39c35d3a8b14
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105642149"
---
# <a name="create-azure-machine-learning-datasets"></a>Criar conjuntos de dados no Azure Machine Learning

Neste artigo, você aprenderá a criar conjuntos de dados Azure Machine Learnings para acessar os dados para seus experimentos locais ou remotos com o SDK do Azure Machine Learning Python. Para entender onde os conjuntos de dados se encaixam no fluxo de trabalho de acesso de data de Azure Machine Learning geral, confira o artigo [dados de acesso seguro](concept-data.md#data-workflow) .

Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dados com uma cópia de seus metadados. Como os dados permanecem em seu local existente, você não incorre nenhum custo de armazenamento extra e não arriscará a integridade de suas fontes de dados. Além disso, os conjuntos de itens são avaliados lentamente, o que auxilia nas velocidades de desempenho do fluxo de trabalho. Você pode criar conjuntos de valores de repositórios de armazenamento, URLs públicas e conjuntos de de [Azure abertos](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md).

Para uma experiência de baixo código, [crie Azure Machine Learning conjuntos de valores com o Azure Machine Learning Studio.](how-to-connect-data-ui.md#create-datasets)

Com os conjuntos de Azure Machine Learning, você pode:

* Mantenha uma única cópia de dados em seu armazenamento, referenciada por conjuntos.

* Acesse dados diretamente durante o treinamento do modelo sem se preocupar com cadeias de conexão ou caminhos de dados. [Saiba mais sobre como treinar com conjuntos de dados](how-to-train-with-datasets.md).

* Compartilhe dados e colabore com outros usuários.

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma assinatura do Azure. Se você não tiver uma conta gratuita, crie uma antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

* O [SDK do Azure Machine Learning para Python instalado](/python/api/overview/azure/ml/install), que inclui o pacote de conjuntos de linhas do azureml.

    * Crie uma [instância de computação Azure Machine Learning](how-to-create-manage-compute-instance.md), que é um ambiente de desenvolvimento totalmente configurado e gerenciado que inclui blocos de anotações integrados e o SDK já instalado.

    **OR**

    * Trabalhe em seu próprio notebook Jupyter e instale o SDK com [estas instruções](/python/api/overview/azure/ml/install).

> [!NOTE]
> Algumas classes de conjunto de objetos têm dependências no pacote [azureml-dataprep](https://pypi.org/project/azureml-dataprep/) , que é compatível apenas com Python de 64 bits. Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux (7, 8), Ubuntu (14, 4, 16, 4, 18, 4), Fedora (27, 28), Debian (8, 9) e CentOS (7). Se você estiver usando distribuições sem suporte, siga [este guia](/dotnet/core/install/linux) para instalar o .net Core 2,1 para continuar. 

## <a name="compute-size-guidance"></a>Diretrizes de tamanho de computação

Ao criar um conjunto de dados, revise sua capacidade de processamento de computação e o tamanho dos seus dados na memória. O tamanho dos dados no armazenamento não é igual ao tamanho dos dados em um dataframe. Por exemplo, os dados em arquivos CSV podem expandir até 10 vezes em um dataframe, de modo que um arquivo CSV de 1 GB possa se tornar 10 GB em um dataframe. 

Se os dados estiverem compactados, eles poderão ser expandidos. 20 GB de dados relativamente esparsos armazenados no formato parquet compactado podem ser expandidos para aproximadamente 800 GB de memória. Como os arquivos parquet armazenam dados em um formato de coluna, se você precisar apenas de metade das colunas, precisará carregar apenas ~ 400 GB de memória.

[Saiba mais sobre como otimizar o processamento de dados em Azure Machine Learning](concept-optimize-data-processing.md).

## <a name="dataset-types"></a>Tipos de conjunto de dados

Há dois tipos de conjuntos de conjunto de DataSet, com base em como os usuários os consomem em treinamento; DataSets e TabularDatasets. Ambos os tipos podem ser usados em Azure Machine Learning fluxos de trabalho de treinamento envolvendo, estimadores, AutoML, hyperDrive e pipelines. 

### <a name="filedataset"></a>DataSet

Um [Filedataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Se seus dados já estiverem limpos e prontos para uso em experimentos de treinamento, você poderá [baixar ou montar](how-to-train-with-datasets.md#mount-vs-download) os arquivos em sua computação como um objeto filedataset. 

É recomendável o filedatasets para seus fluxos de trabalho de aprendizado de máquina, já que os arquivos de origem podem estar em qualquer formato, o que permite uma ampla gama de cenários de aprendizado de máquina, incluindo aprendizado profundo.

Crie um filedataset com o [SDK do Python](#create-a-filedataset) ou o [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets) .
### <a name="tabulardataset"></a>TabularDataset

Um [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) representa dados em um formato tabular analisando o arquivo ou a lista de arquivos fornecida. Isso fornece a capacidade de materializar os dados em um data frame do pandas ou Spark para que você possa trabalhar com bibliotecas de treinamento e preparação de dados familiares sem precisar sair do bloco de anotações. Você pode criar um `TabularDataset` objeto a partir de arquivos. csv,. TSV,. parquet,. jsonl e dos [resultados da consulta SQL](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-).

Com o TabularDatasets, você pode especificar um carimbo de data/hora de uma coluna nos dados ou de onde quer que os dados de padrão de caminho sejam armazenados para habilitar uma característica de série temporal. Essa especificação permite uma filtragem fácil e eficiente por tempo. Para obter um exemplo, consulte [tabela de demonstração de API relacionada à série temporal com dados meteorológicos NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

Crie um TabularDataset com [o SDK do Python ou o](#create-a-tabulardataset) [Azure Machine Learning Studio](how-to-connect-data-ui.md#create-datasets).

>[!NOTE]
> Fluxos de trabalho de [ml automatizados](concept-automated-ml.md) gerados por meio do Azure Machine Learning Studio atualmente só dão suporte a TabularDatasets. 

## <a name="access-datasets-in-a-virtual-network"></a>Acessar conjuntos de os em uma rede virtual

Se o seu espaço de trabalho estiver em uma rede virtual, você deverá configurar o conjunto de um para ignorar a validação. Para obter mais informações sobre como usar armazenamentos de dados e conjuntos de dados em uma rede virtual, consulte [proteger um espaço de trabalho e os recursos associados](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

<a name="datasets-sdk"></a>

## <a name="create-datasets-from-datastores"></a>Criar conjuntos de os de armazenamentos

Para que os dados sejam acessíveis por Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em [Azure Machine Learning repositórios](how-to-access-data.md) de dados ou URLs da Web. 

> [!TIP] 
> Você pode criar conjuntos de dados diretamente a partir de URLs de armazenamento com acesso a data baseado em identidade. Saiba mais em [conectar-se ao armazenamento com acesso a dados com base em identidade (versão prévia)](how-to-identity-based-data-access.md)<br><br>
Esse recurso é um recurso de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e pode mudar a qualquer momento. 

 
Para criar conjuntos de itens de um repositório de armazenamento com o SDK do Python:

1. Verifique se você tem `contributor` ou tem `owner` acesso ao serviço de armazenamento subjacente de seu armazenamento de Azure Machine Learning registrado. [Verifique as permissões da conta de armazenamento no portal do Azure](../role-based-access-control/check-access.md).

1. Crie o conjunto de um referenciando caminhos no repositório de armazenamento. Você pode criar um conjunto de um DataSet a partir de vários caminhos em vários repositórios de armazenamento. Não há nenhum limite rígido para o número de arquivos ou o tamanho de dados do qual você pode criar um DataSet. 

> [!NOTE]
> Para cada caminho de dados, algumas solicitações serão enviadas ao serviço de armazenamento para verificar se ele aponta para um arquivo ou uma pasta. Essa sobrecarga pode levar à degradação do desempenho ou falha. Um conjunto de dados que faz referência a uma pasta com 1000 arquivos dentro é considerado fazendo referência a um caminho de dado. É recomendável criar um conjunto de consulta que referencie menos de 100 caminhos em repositórios de armazenamento para um desempenho ideal.

### <a name="create-a-filedataset"></a>Criar um FileDataset

Use o [`from_files()`](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#from-files-path--validate-true-) método na `FileDatasetFactory` classe para carregar arquivos em qualquer formato e para criar um filedataset não registrado. 

Se o armazenamento estiver protegido por uma rede virtual ou firewall, defina o parâmetro `validate=False` em seu `from_files()` método. Isso ignora a etapa de validação inicial e garante que você possa criar seu conjunto de seus arquivos seguros. Saiba mais sobre como [usar repositórios de armazenamento e conjuntos de informações em uma rede virtual](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```
Para reutilizar e compartilhar conjuntos de testes no seu espaço de trabalho, [Registre seu conjunto de registros](#register-datasets). 

> [!TIP] 
> Carregue arquivos de um diretório local e crie um filedataset em um único método com o método de visualização pública, [upload_directory ()](/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory#upload-directory-src-dir--target--pattern-none--overwrite-false--show-progress-true-). Esse método é um recurso de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e pode ser alterado a qualquer momento. 
> 
>  Esse método carrega dados para o armazenamento subjacente e, como resultado, incorre em custos de armazenamento. 

### <a name="create-a-tabulardataset"></a>Criar um TabularDataset

Use o [`from_delimited_files()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) método na `TabularDatasetFactory` classe para ler os arquivos no formato. csv ou. tsv e para criar um TabularDataset não registrado. Se você estiver lendo de vários arquivos, os resultados serão agregados em uma representação tabular. 

Se o armazenamento estiver protegido por uma rede virtual ou firewall, defina o parâmetro `validate=False` em seu `from_delimited_files()` método. Isso ignora a etapa de validação inicial e garante que você possa criar seu conjunto de seus arquivos seguros. Saiba mais sobre como usar [repositórios de armazenamento e conjuntos de informações em uma rede virtual](how-to-secure-workspace-vnet.md#secure-datastores-and-datasets).

O código a seguir obtém o espaço de trabalho existente e o repositório de armazenamento desejado pelo nome. E, em seguida, passa o repositório de armazenamento e os locais de arquivo para o `path` parâmetro para criar um novo TabularDataset, `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```
### <a name="set-data-schema"></a>Definir esquema de dados

Por padrão, quando você cria um TabularDataset, os tipos de dados de coluna são inferidos automaticamente. Se os tipos deduzidos não corresponderem às suas expectativas, você poderá atualizar o esquema do conjunto de seus conjuntos de colunas, especificando os tipos de coluna com o código a seguir. O parâmetro `infer_column_type` só é aplicável a conjuntos de valores criados a partir de arquivos delimitados. [Saiba mais sobre os tipos de dados com suporte](/python/api/azureml-core/azureml.data.dataset_factory.datatype).


```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Index|PassengerId|Survived|Pclass|Nome|Sexo|Idade|SibSp|Parch|Tíquete|Tarifa|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris|masculino|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, Sra. John Bradley (Florence Briggs th...|feminino|38,0|1|0|PC 17599|71,2833|C85|C
2|3|True|3|Heikkinen, erro. Laina|feminino|26,0|0|0|STON/O2. 3101282|7,9250||S

Para reutilizar e compartilhar conjuntos de testes entre experimentos em seu espaço de trabalho, [Registre seu conjunto de registros](#register-datasets).

## <a name="wrangle-data"></a>Dados do arrebanhar
Depois de criar e [registrar](#register-datasets) seu conjunto de dados, você pode carregá-lo no bloco de anotações para data Wrangling e [exploração](#explore-data) antes do treinamento do modelo. 

Se você não precisar fazer qualquer Wrangling de dados ou exploração, consulte como consumir conjuntos de dados em seus scripts de treinamento para enviar experimentos de ML em [treinamento com conjuntos](how-to-train-with-datasets.md)de dados.

### <a name="filter-datasets-preview"></a>Filtrar conjuntos de valores (visualização)
Os recursos de filtragem dependem do tipo de conjunto de um que você tem. 
> [!IMPORTANT]
> A filtragem de conjuntos de itens com o método de visualização pública [`filter()`](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) é um recurso de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e pode ser alterada a qualquer momento. 
> 
**Para TabularDatasets**, você pode manter ou remover colunas com os métodos [keep_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#keep-columns-columns--validate-false-) e [drop_columns ()](/python/api/azureml-core/azureml.data.tabulardataset#drop-columns-columns-) .

Para filtrar as linhas por um valor de coluna específico em um TabularDataset, use o método [Filter ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) (visualização). 

Os exemplos a seguir retornam um conjunto de um DataSet não registrado com base nas expressões especificadas.

```python
# TabularDataset that only contains records where the age column value is greater than 15
tabular_dataset = tabular_dataset.filter(tabular_dataset['age'] > 15)

# TabularDataset that contains records where the name column value contains 'Bri' and the age column value is greater than 15
tabular_dataset = tabular_dataset.filter((tabular_dataset['name'].contains('Bri')) & (tabular_dataset['age'] > 15))
```

**Em DataSets**, cada linha corresponde a um caminho de um arquivo, portanto, a filtragem por valor de coluna não é útil. Mas, você pode [Filtrar ()](/python/api/azureml-core/azureml.data.filedataset#filter-expression-) as linhas por metadados como, CreationTime, tamanho, etc.

Os exemplos a seguir retornam um conjunto de um DataSet não registrado com base nas expressões especificadas.

```python
# FileDataset that only contains files where Size is less than 100000
file_dataset = file_dataset.filter(file_dataset.file_metadata['Size'] < 100000)

# FileDataset that only contains files that were either created prior to Jan 1, 2020 or where 
file_dataset = file_dataset.filter((file_dataset.file_metadata['CreatedTime'] < datetime(2020,1,1)) | (file_dataset.file_metadata['CanSeek'] == False))
```

Os conjuntos de dados **rotulados** criados a partir dos [projetos de rotulação de dado](how-to-create-labeling-projects.md) são um caso especial. Esses conjuntos de valores são um tipo de TabularDataset composto por arquivos de imagem. Para esses tipos de conjuntos de valores, você pode [Filtrar ()](/python/api/azureml-core/azureml.data.tabulardataset#filter-expression-) imagens por metadados e por valores de coluna `label` como `image_details` e.

```python
# Dataset that only contains records where the label column value is dog
labeled_dataset = labeled_dataset.filter(labeled_dataset['label'] == 'dog')

# Dataset that only contains records where the label and isCrowd columns are True and where the file size is larger than 100000
labeled_dataset = labeled_dataset.filter((labeled_dataset['label']['isCrowd'] == True) & (labeled_dataset.file_metadata['Size'] > 100000))
```

## <a name="explore-data"></a>Explorar dados

Depois de concluir a Wrangling de seus dados, você pode [registrar](#register-datasets) seu conjunto e carregá-lo em seu notebook para exploração de dados antes do treinamento do modelo.

Para conjuntos de dados, você pode **montar** ou **baixar** seu conjunto de dados e aplicar as bibliotecas do Python que normalmente usaria para a exploração do dado. [Saiba mais sobre montagem vs download](how-to-train-with-datasets.md#mount-vs-download).

```python
# download the dataset 
dataset.download(target_path='.', overwrite=False) 

# mount dataset to the temp directory at `mounted_path`

import tempfile
mounted_path = tempfile.mkdtemp()
mount_context = dataset.mount(mounted_path)

mount_context.start()
```

Para TabularDatasets, use o [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) método para exibir seus dados em um dataframe. 

```python
# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Index|PassengerId|Survived|Pclass|Nome|Sexo|Idade|SibSp|Parch|Tíquete|Tarifa|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris|masculino|22,0|1|0|A/5 21171|7,2500||S
1|2|True|1|Cumings, Sra. John Bradley (Florence Briggs th...|feminino|38,0|1|0|PC 17599|71,2833|C85|C
2|3|True|3|Heikkinen, erro. Laina|feminino|26,0|0|0|STON/O2. 3101282|7,9250||S

## <a name="create-a-dataset-from-pandas-dataframe"></a>Criar um conjunto de uma série de dataframe do pandas

Para criar um TabularDataset a partir de um dataframe do pandas na memória, grave os dados em um arquivo local, como um CSV, e crie seu conjunto de dado a partir desse arquivo. O código a seguir demonstra esse fluxo de trabalho.

```python
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required

from azureml.core import Workspace, Dataset
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)

# upload the local file to a datastore on the cloud

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')

# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(path = [(datastore, ('data/prepared.csv'))])
```

> [!TIP]
> Crie e registre um TabularDataset de um dataframe do Spark ou pandas em memória com um único método com métodos de visualização pública [`register_spark_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) e [`register_pandas_dataframe()`](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#methods) . Esses métodos de registro são recursos de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e podem ser alterados a qualquer momento. 
> 
>  Esses métodos carregam dados para o armazenamento subjacente e, como resultado, incorrem em custos de armazenamento. 

## <a name="register-datasets"></a>Registrar conjuntos de os

Para concluir o processo de criação, registre seus conjuntos de registros com um espaço de trabalho. Use o [`register()`](/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset#&preserve-view=trueregister-workspace--name--description-none--tags-none--create-new-version-false-) método para registrar conjuntos de registros com seu espaço de trabalho a fim de compartilhá-los com outras pessoas e reutilizá-los entre experimentos em seu espaço de trabalho:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-using-azure-resource-manager"></a>Criar conjuntos de valores usando Azure Resource Manager

Há muitos modelos [https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-dataset-create-*](https://github.com/Azure/azure-quickstart-templates/tree/master/) que podem ser usados para criar conjuntos de os.

Para obter informações sobre como usar esses modelos, consulte [usar um modelo de Azure Resource Manager para criar um espaço de trabalho para Azure Machine Learning](how-to-create-workspace-template.md).


## <a name="create-datasets-from-azure-open-datasets"></a>Criar conjuntos de itens de conjuntos de os abertos do Azure

Os [Conjuntos de dados abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos coletados que você pode usar para adicionar recursos específicos do cenário para soluções de aprendizado de máquina para obter modelos mais precisos. Os conjuntos de dados incluem dados de domínio público de clima, censo, feriados, segurança pública e localização que ajudam você a treinar os modelos de machine learning e aprimorar as soluções de previsão. Os conjuntos de itens abertos estão na nuvem no Microsoft Azure e são incluídos no SDK e no Studio.

Saiba como criar [conjuntos de Azure Machine Learning de conjuntos de informações a partir do Azure Open DataSets](../open-datasets/how-to-create-azure-machine-learning-dataset-from-open-dataset.md). 

## <a name="train-with-datasets"></a>Treinar com conjuntos de dados

Use seus conjuntos de informações em seus experimentos de aprendizado de máquina para modelos de ML de treinamento. [Saiba mais sobre como treinar com conjuntos de dados](how-to-train-with-datasets.md).

## <a name="version-datasets"></a>Conjuntos de itens de versão

Você pode registrar um novo conjunto de registros com o mesmo nome criando uma nova versão. Uma versão de conjunto de dados é uma maneira de marcar o estado de seus dados de forma que você possa aplicar uma versão específica do conjunto para experimentação ou reprodução futura. Saiba mais sobre [as versões do conjunto](how-to-version-track-datasets.md)de informações.
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="next-steps"></a>Próximas etapas

* Saiba [como treinar com conjuntos de](how-to-train-with-datasets.md)informações.
* Use o Machine Learning automatizado para [treinar com o TabularDatasets](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).
* Para obter mais exemplos de treinamento de conjunto de informações, consulte os [blocos de anotações de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).
