---
title: "Referência da API de coleta de dados de modelo do Azure Machine Learning | Microsoft Docs"
description: "Referência da API de coleta de dados de modelo do Azure Machine Learning."
services: machine-learning
author: aashishb
ms.author: aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/12/2017
ms.openlocfilehash: 7a0fda8a44d13bcaba84b4124d9b693c05874154
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-model-data-collection-api-reference"></a>Referência da API de coleta de dados de modelo do Azure Machine Learning

A coleta de dados de modelo permite arquivar entradas e previsões do modelo de uma serviço Web do Machine Learning. Consulte o [model data collection how-to guide](how-to-use-model-data-collection.md) (guia de instruções de coleta de dados de modelo) para entender como instalar o `azureml.datacollector` em seu computador Windows e Linux.

Neste guia de referência de API, usamos uma abordagem passo a passo de como coletar entradas e previsões de modelo de um serviço Web do Machine Learning.

## <a name="enable-model-data-collection-in-azure-ml-workbench-environment"></a>Habilitar a coleta de dados de modelo no ambiente do Azure ML Workbench

 Procure o arquivo conda\_dependencies.yml no projeto na pasta aml_config e inclua o módulo azureml.datacollector na seção pip do arquivo conda\_dependencies.yml, conforme é mostrado abaixo. Observe que isso é apenas uma subseção do arquivo conda\_dependencies.yml completo:

    dependencies:
      - python=3.5.2
      - pip:
        - azureml.datacollector==0.1.0a13

>[!NOTE] 
>No momento, você pode usar o módulo de coletor de dados no Azure ML Workbench executando no modo do Docker. O modo local pode não funcionar para todos os ambientes.




## <a name="enable-model-data-collection-in-the-scoring-file"></a>Habilitar a coleta de dados de modelo no arquivo de pontuação

No arquivo de pontuação que está sendo usado para operacionalização, importe o módulo do coletor de dados e a classe ModelDataCollector:

    from azureml.datacollector import ModelDataCollector


## <a name="model-data-collector-instantiation"></a>Criação de instância de coletor de dados de modelo
Crie uma nova instância de um ModelDataCollector:

dc = ModelDataCollector(model_name, identifier='default', feature_names=None, model_management_account_id='unknown', webservice_name='unknown', model_id='unknown', model_version='unknown')

Consulte os detalhes da classe e dos parâmetros:

### <a name="class"></a>Classe
| Nome | Descrição |
|--------------------|--------------------|
| ModelDataCollector | Uma classe no namespace azureml.datacollector. Uma instância desta classe será usada para coletar dados de modelo. Um único arquivo de pontuação pode conter vários ModelDataCollectors. Cada instância deve ser usada para coletar dados em um local distinto no arquivo de pontuação para que o esquema de dados coletados permaneça consistente (ou seja, entradas e previsão)|


### <a name="parameters"></a>parâmetros

| Nome | Tipo | Descrição |
|-------------|------------|-------------------------|
| model_name | string | o nome do modelo do qual os dados estão sendo coletados |
| identificador | string | o local no código que identifica esses dados, ou seja 'RawInput' ou 'Prediction' |
| feature_names | lista de cadeias de caracteres | uma lista de nomes de recursos que tornam-se o cabeçalho de CSV fornecido |
| model_management_account_id | string | o identificador da conta de gerenciamento de modelos em que este modelo está armazenado. Esse campo é preenchido automaticamente quando os modelos são operacionalizados por meio do AML |
| webservice_name | string | o nome do que o serviço Web no qual esse modelo está implantado no momento. Esse campo é preenchido automaticamente quando os modelos são operacionalizados por meio do AML |
| model_id | string | O identificador exclusivo deste modelo no contexto de uma conta de gerenciamento de modelos. Esse campo é preenchido automaticamente quando os modelos são operacionalizados por meio do AML |
| model_version | string | o número de versão desse modelo no contexto de uma conta de gerenciamento de modelos. Esse campo é preenchido automaticamente quando os modelos são operacionalizados por meio do AML |



 

## <a name="collecting-the-model-data"></a>Coletando dados de modelo

Você pode coletar os dados de modelo usando uma instância do ModelDataCollector criada acima.

    dc.collect(input_data, user_correlation_id="")

Consulte os detalhes do método e dos parâmetros:

### <a name="method"></a>Método
| Nome | Descrição |
|--------------------|--------------------|
| collect | Usado para coletar os dados para uma entrada ou previsão de modelo|


### <a name="parameters"></a>parâmetros

| Nome | Tipo | Descrição |
|-------------|------------|-------------------------|
| input_data | vários tipos | os dados a serem coletados (atualmente aceita a lista de tipos, numpy.array, pandas.DataFrame, pyspark.sql.DataFrame). Para os tipos dataframe, se houver um cabeçalho com nomes de recurso, essas informações estarão incluídas no destino dos dados (sem a necessidade de passar explicitamente os nomes de recurso no construtor ModelDataCollector) |
| user_correlation_id | string | uma ID de correlação opcional, que pode ser fornecida pelo usuário para correlacionar essa previsão |

