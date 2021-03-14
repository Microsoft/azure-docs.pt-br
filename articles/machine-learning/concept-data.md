---
title: Proteger o acesso a dados na nuvem
titleSuffix: Azure Machine Learning
description: Saiba como se conectar com segurança ao seu armazenamento de dados no Azure com Azure Machine Learning armazenamentos e DataSets.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 08/31/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: 601be8409db22162a410d481e6609d378718a7b4
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503582"
---
# <a name="secure-data-access-in-azure-machine-learning"></a>Proteger o acesso a dados no Azure Machine Learning

Azure Machine Learning facilita a conexão com seus dados na nuvem. Ele fornece uma camada de abstração sobre o serviço de armazenamento subjacente, para que você possa acessar e trabalhar com segurança com seus dados sem precisar escrever código específico ao seu tipo de armazenamento. O Azure Machine Learning também fornece os seguintes recursos de dados:

*    Interoperabilidade com pandas e Spark dataframes
*    Controle de versão e acompanhamento de linhagem de dados
*    Rotulagem de dados 
*    Monitoramento de dessincronização de dados
    
## <a name="data-workflow"></a>Fluxo de trabalho de dados

Quando você estiver pronto para usar os dados em sua solução de armazenamento baseada em nuvem, recomendamos o seguinte fluxo de trabalho de entrega de dados. Este fluxo de trabalho pressupõe que você tenha uma [conta de armazenamento do Azure](../storage/common/storage-account-create.md?tabs=azure-portal) e dados em um serviço de armazenamento baseado em nuvem no Azure. 

1. Crie um [repositório](#datastores) de dados Azure Machine Learning para armazenar informações de conexão para o armazenamento do Azure.

2. Nesse datastore, crie um [conjunto de Azure Machine Learning](#datasets) para apontar para um ou mais arquivos em seu armazenamento subjacente. 

3. Para usar esse conjunto de informações em seu experimento do Machine Learning, você pode
    1. Monte-o no destino de computação do experimento para o treinamento do modelo.

        **OR** 

    1. Consuma-o diretamente em soluções de Azure Machine Learning como, execuções de experimento automatizadas do Machine Learning (ML automatizado), pipelines de aprendizado de máquina ou [Designer de Azure Machine Learning](concept-designer.md).

4. Crie [monitores de conjunto](#drift) de dados para seu conjunto de dados de saída de modelo para detectar descompasso de dado 

5. Se a descompasso de dados for detectada, atualize o DataSet de entrada e retreine seu modelo de acordo.

O diagrama a seguir fornece uma demonstração visual desse fluxo de trabalho recomendado.

![O diagrama mostra o serviço de armazenamento do Azure que flui em um repositório de armazenamento, que flui para um conjunto de uma. O conjunto de dados flui para o treinamento do modelo, que flui em descompassos, o que flui para o conjunto.](./media/concept-data/data-concept-diagram.svg)

<a name="datastores"></a>
## <a name="connect-to-storage-with-datastores"></a>Conectar-se ao armazenamento com armazenamentos de

Azure Machine Learning armazenamentos de dados mantêm com segurança as informações de conexão para o armazenamento de seu dado no Azure, de modo que você não precisará codificar em seus scripts. [Registre e crie um armazenamento](how-to-access-data.md) de dados para se conectar facilmente à sua conta de armazenamento e acesse os dados em seu serviço de armazenamento subjacente. 

Serviços de armazenamento baseados em nuvem com suporte no Azure que podem ser registrados como repositórios de armazenamento:

+ Contêiner de blob do Azure
+ Compartilhamento de arquivos do Azure
+ Azure Data Lake
+ Azure Data Lake Gen2
+ Banco de Dados SQL do Azure
+ Banco de Dados do Azure para PostgreSQL
+ Sistema de arquivos do Databricks
+ Banco de Dados do Azure para MySQL

>[!TIP]
> A funcionalidade geralmente disponível para a criação de repositórios de armazenamento requer autenticação baseada em credencial para acessar serviços de armazenamento, como uma entidade de serviço ou um token SAS (assinatura de acesso compartilhado). Essas credenciais podem ser acessadas por usuários que têm acesso de *leitor* ao espaço de trabalho. <br><br>Se essa for uma preocupação,  [crie um armazenamento de dados que usa o acesso a serviços de armazenamento com base em identidades (versão prévia)](how-to-identity-based-data-access.md). Esse recurso é um recurso de visualização [experimental](/python/api/overview/azure/ml/#stable-vs-experimental) e pode mudar a qualquer momento.

<a name="datasets"></a>
## <a name="reference-data-in-storage-with-datasets"></a>Dados de referência no armazenamento com DataSets

Os conjuntos de dados Azure Machine Learning não são cópias de seu dado. Com a criação de um conjunto de dados, você cria uma referência a eles em seu serviço de armazenamento, juntamente com uma cópia de seus metadados. 

Como os conjuntos de dados são avaliados lentamente, e eles permanecem em seu local existente, você

* Não incorrer nenhum custo de armazenamento extra.
* Não arrisque a alteração acidental de suas fontes de dados originais.
* Melhorar as velocidades de desempenho de fluxo de trabalho ML.

Para interagir com os seus dados no armazenamento, [crie um conjunto](how-to-create-register-datasets.md) de dados para empacotar seus dados em um objeto consumível para tarefas de aprendizado de máquina. Registre o conjunto de dados em seu espaço de trabalho para compartilhá-lo e reutilizá-lo em experimentos diferentes sem complexidades de ingestão de dados.

Os conjuntos de valores podem ser criados a partir de arquivos locais, de URLs públicas, de conjuntos de de [Azure abertos](https://azure.microsoft.com/services/open-datasets/)ou de serviços de armazenamento do Azure por meio de armazenamentos. 

Há dois tipos de conjuntos de valores: 

+ Um [Filedataset](/python/api/azureml-core/azureml.data.file_dataset.filedataset) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Se seus dados já estiverem limpos e prontos para uso em experimentos de treinamento, você poderá [baixar ou montar arquivos](how-to-train-with-datasets.md#mount-files-to-remote-compute-targets) referenciados por filedatasets em seu destino de computação.

+ Um [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) representa dados em um formato tabular analisando o arquivo ou a lista de arquivos fornecida. Você pode carregar um TabularDataset em um dataframe do pandas ou Spark para manipulação e limpeza adicionais. Para obter uma lista completa dos formatos de dados dos quais você pode criar TabularDatasets, consulte a [classe TabularDatasetFactory](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory).

Os recursos adicionais de conjuntos de documentos podem ser encontrados na seguinte documentação:

+ Linhagem [e faixa de conjunto de](how-to-version-track-datasets.md) DataSet.
+ [Monitore seu conjunto](how-to-monitor-datasets.md) de dados para ajudá-lo com a detecção de descompasso.    

## <a name="work-with-your-data"></a>Trabalhe com seus dados

Com conjuntos de informações, você pode realizar várias tarefas de aprendizado de máquina por meio de integração direta com recursos de Azure Machine Learning. 

+ Crie um [projeto de rotulagem de dados](#label).
+ Treinar modelos de aprendizado de máquina:
     + [experimentos de ML automatizados](how-to-use-automated-ml-for-ml-models.md)
     + o [Designer](tutorial-designer-automobile-price-train-score.md#import-data)
     + [notebooks](how-to-train-with-datasets.md)
     + [Pipelines de Azure Machine Learning](./how-to-create-machine-learning-pipelines.md)
+ Acesse conjuntos de informações para pontuação com [inferência de lote](./tutorial-pipeline-batch-scoring-classification.md) em [pipelines de Machine Learning](./how-to-create-machine-learning-pipelines.md).
+ Configure um monitor de conjunto de dados para a detecção de [descompasso de dados](#drift) .

<a name="label"></a>

## <a name="label-data-with-data-labeling-projects"></a>Rotular dados com projetos de rótulos de dados

Rotular grandes quantidades de dados costuma ser uma dor de cabeça nos projetos de aprendizado de máquina. Aqueles com um componente da pesquisa Visual computacional, como classificação de imagem ou detecção de objetos, geralmente exigem milhares de imagens e rótulos correspondentes.

O Azure Machine Learning oferece uma localização central para criar, gerenciar e monitorar projetos de rotulagem. Os projetos de rotulagem ajudam a coordenar os dados, os rótulos e os membros da equipe, permitindo que você gerencie com mais eficiência as tarefas de rotulagem. Atualmente, as tarefas compatíveis são a classificação de imagem (seja ela de vários rótulos ou de várias classes) e a identificação de objetos usando caixas delimitadas.

Crie um [projeto de rotulagem de dados](how-to-create-labeling-projects.md)e a saída de um DataSet para uso em experimentos de aprendizado de máquina.

<a name="drift"></a>

## <a name="monitor-model-performance-with-data-drift"></a>Monitorar o desempenho do modelo com descompasso de dados

No contexto do Machine Learning, a descompasso de dados é a alteração nos dados de entrada do modelo que leva à degradação do desempenho do modelo. É um dos principais motivos pelos quais a precisão do modelo diminui ao longo do tempo, o que monitora a descompasso de dados ajuda a detectar problemas de desempenho do modelo.

Consulte o artigo [criar um conjunto de](how-to-monitor-datasets.md) dados para saber mais sobre como detectar e alertar a descompasso de dados em novos dados em um DataSet.

## <a name="next-steps"></a>Próximas etapas 

+ Crie um conjunto de um DataSet no Azure Machine Learning Studio ou com o SDK do Python [usando estas etapas.](how-to-create-register-datasets.md)
+ Experimente exemplos de treinamento de conjunto de [anotações com nossos notebooks de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/work-with-data/).