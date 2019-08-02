---
title: Executar cargas de trabalho do Azure Machine Learning com AutoML (machine learning automatizado) no Apache Spark no Azure HDInsight
description: Saiba como executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/14/2019
ms.openlocfilehash: ff6a071a2d157bf79ab27fcbf4f9753fdbcac118
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68354867"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-automl-on-apache-spark-in-azure-hdinsight"></a>Executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight

Azure Machine Learning simplifica e acelera a criação, o treinamento e a implantação de modelos de aprendizado de máquina. No AutoML (Machine Learning automatizado), você começa com dados de treinamento que têm um recurso de destino definido e, em seguida, itera através de combinações de algoritmos e seleções de recursos para selecionar automaticamente o melhor modelo para seus dados com base nas pontuações de treinamento. O HDInsight permite que os clientes provisionem clusters com centenas de nós. O AutoML em execução no Spark em um cluster HDInsight permite que os usuários usem a capacidade de computação entre esses nós para executar trabalhos de treinamento em um modo de expansão e executar vários trabalhos de treinamento em paralelo. Isso permite que os usuários executem experimentos do AutoML enquanto compartilham a computação com suas outras cargas de trabalho de Big Data.
 

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar o Azure Machine Learning em um cluster do HDInsight

Para obter tutoriais gerais sobre o aprendizado de máquina [automatizado, consulte o tutorial: Usar o aprendizado de máquina automatizado para compilar o modelo de regressão](../../machine-learning/service/tutorial-auto-train-models.md).
Todos os novos clusters HDInsight-Spark vêm pré-instalados com o SDK do AzureML-AutoML. Você pode começar a usar o AutoML no HDInsight com este [Notebook Jupyter de exemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/azure-hdi). Este Jupyter Notebook demonstra como usar um classificador de aprendizado de máquina automatizado para classificação simples de problema.

> [!Note]
> Pacotes do Azure Machine Learning são instalados no ambiente Python3 conda. O Jupyter Notebook instalado deve ser executado usando o kernel PySpark3.

Como alternativa, você pode usar blocos de anotações do Zeppelin para usar o AutoML também.

> [!Note]
> O Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) em que o PySpark3 não escolhe a versão correta do Python. Use a solução alternativa documentada.

## <a name="authentication-for-workspace"></a>Autenticação para o workspace

Criação do workspace e envio de teste exigem um token de autenticação. Esse token pode ser gerado usando um [aplicativo do Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Um [Usuário do Azure AD](https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python) também poderá ser usado para gerar o token de autenticação obrigatório se a autenticação multifator não estiver habilitada na conta.  

O snippet de código a seguir cria um token de autenticação usando um **aplicativo do Azure AD**.

```python
from azureml.core.authentication import ServicePrincipalAuthentication
auth_sp = ServicePrincipalAuthentication(
    tenant_id='<Azure Tenant ID>',
    service_principal_id='<Azure AD Application ID>',
    service_principal_password='<Azure AD Application Key>'
)
```
O snippet de código a seguir cria um token de autenticação usando um **usuário do Azure AD**.

```python
from azure.common.credentials import UserPassCredentials
credentials = UserPassCredentials('user@domain.com', 'my_smart_password')
```

## <a name="loading-dataset"></a>Carregando conjunto de dados

O aprendizado de máquina automatizado no Spark usa **Fluxos de dados**, que são operações imutáveis avaliadas lentamente nos dados.  Um Fluxo de Dados pode carregar um conjunto de dados de um blob com acesso de leitura público ou uma URL de blob com um token SAS.

```python
import azureml.dataprep as dprep

dataflow_public = dprep.read_csv(
    path='https://commonartifacts.blob.core.windows.net/automl/UCI_Adult_train.csv')

dataflow_with_token = dprep.read_csv(
    path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
```

Você também pode registrar o repositório de dados com o workspace usando um único registro.

## <a name="experiment-submission"></a>Envio de experimento

Na [configuração de Machine Learning automatizada](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlconfig), a `spark_context` propriedade deve ser definida para que o pacote seja executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definida como um número menor que os núcleos de executor para o aplicativo Spark.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a motivação por trás do aprendizado de máquina automatizado, consulte [modelos de versão em ritmo usando o aprendizado de máquina automatizado da Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Para obter mais detalhes sobre como usar os recursos de ML automatizados do Azure ML, consulte [novos recursos de Machine Learning automatizados no serviço Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
