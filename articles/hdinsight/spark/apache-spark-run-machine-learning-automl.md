---
title: Executar cargas de trabalho de Azure Machine Learning no Apache Spark no HDInsight
description: Saiba como executar cargas de trabalho do Azure Machine Learning com AutoML (aprendizado de máquina automatizado) no Apache Spark no Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.date: 12/13/2019
ms.openlocfilehash: 4087341a9a96ae56c00972f886ce3cc8891750a5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929741"
---
# <a name="run-azure-machine-learning-workloads-with-automated-machine-learning-on-apache-spark-in-hdinsight"></a>Executar cargas de trabalho de Azure Machine Learning com o Machine Learning automatizado no Apache Spark no HDInsight

Azure Machine Learning simplifica e acelera a criação, o treinamento e a implantação de modelos de aprendizado de máquina. No AutoML (Machine Learning automatizado), você começa com dados de treinamento que têm um recurso de destino definido e, em seguida, itera através de combinações de algoritmos e seleções de recursos para selecionar automaticamente o melhor modelo para seus dados com base nas pontuações de treinamento. O HDInsight permite que os clientes provisionem clusters com centenas de nós. O AutoML em execução no Spark em um cluster HDInsight permite que os usuários usem a capacidade de computação entre esses nós para executar trabalhos de treinamento em um modo de expansão e executar vários trabalhos de treinamento em paralelo. Isso permite que os usuários executem experimentos do AutoML enquanto compartilham a computação com suas outras cargas de trabalho de Big Data.

## <a name="install-azure-machine-learning-on-an-hdinsight-cluster"></a>Instalar o Azure Machine Learning em um cluster do HDInsight

Para obter tutoriais gerais sobre o aprendizado de máquina automatizado, consulte [tutorial: usar o Machine Learning automatizado para criar seu modelo de regressão](../../machine-learning/tutorial-auto-train-models.md).
Todos os novos clusters de HDInsight-Spark vêm pré-instalados com o SDK do AzureML-AutoML.

> [!Note]
> Pacotes do Azure Machine Learning são instalados no ambiente Python3 conda. O Jupyter Notebook instalado deve ser executado usando o kernel PySpark3.

Você também pode usar blocos de anotações do Zeppelin para usar o AutoML.

> [!Note]
> O Zeppelin tem um [problema conhecido](https://community.hortonworks.com/content/supportkb/207822/the-livypyspark3-interpreter-uses-python-2-instead.html) em que o PySpark3 não escolhe a versão correta do Python. Use a solução alternativa documentada.

## <a name="authentication-for-workspace"></a>Autenticação para o workspace

Criação do workspace e envio de teste exigem um token de autenticação. Esse token pode ser gerado usando um [aplicativo do Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Um [Usuário do Azure AD](/azure/python/python-sdk-azure-authenticate) também poderá ser usado para gerar o token de autenticação obrigatório se a autenticação multifator não estiver habilitada na conta.  

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

Na [configuração de Machine Learning automatizada](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig), a propriedade `spark_context` deve ser definida para que o pacote seja executado no modo distribuído. A propriedade `concurrent_iterations`, que é o número máximo de iterações executadas em paralelo, deve ser definida como um número menor que os núcleos de executor para o aplicativo Spark.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a motivação por trás do aprendizado de máquina automatizado, consulte [modelos de versão em ritmo usando o aprendizado de máquina automatizado da Microsoft!](https://azure.microsoft.com/blog/release-models-at-pace-using-microsoft-s-automl/)
* Para obter mais informações sobre como usar recursos de ML automatizados do Azure ML, consulte [novos recursos de Machine Learning automatizados no Azure Machine Learning](https://azure.microsoft.com/blog/new-automated-machine-learning-capabilities-in-azure-machine-learning-service/)
* [Projeto AutoML da Microsoft Research](https://www.microsoft.com/research/project/automl/)
