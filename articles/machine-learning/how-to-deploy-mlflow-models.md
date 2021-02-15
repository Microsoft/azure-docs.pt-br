---
title: Implantar modelos MLflow como serviços Web
titleSuffix: Azure Machine Learning
description: Configure o MLflow com Azure Machine Learning para implantar seus modelos de ML como um serviço Web do Azure.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 12/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: aaa7dbf2ae7c8acb3b3beeb3e9098c5058af26a7
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918056"
---
# <a name="deploy-mlflow-models-as-azure-web-services-preview"></a>Implantar modelos MLflow como serviços Web do Azure (versão prévia)

Neste artigo, saiba como implantar o modelo [MLflow](https://www.mlflow.org) como um serviço Web do Azure, para que você possa aproveitar e aplicar os recursos de detecção de descompasso de dados e gerenciamento de modelos de Azure Machine Learning para seus modelos de produção.

O Azure Machine Learning oferece configurações de implantação para:
* ACI (instância de contêiner do Azure) que é uma opção adequada para uma implantação rápida de desenvolvimento/teste.
* AKS (serviço kubernetes do Azure), recomendado para implantações de produção escalonáveis.
> [!TIP]
> As informações contidas neste documento são basicamente para cientistas de dados e desenvolvedores que desejam implantar seu modelo MLflow em um ponto de extremidade de serviço Web Azure Machine Learning. Se você for um administrador interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).
## <a name="mlflow-with-azure-machine-learning-deployment"></a>MLflow com implantação Azure Machine Learning

O MLflow é uma biblioteca de open-source para gerenciar o ciclo de vida dos experimentos de aprendizado de máquina. Sua integração com o Azure Machine Learning permite que você estenda esse gerenciamento além do treinamento do modelo para a fase de implantação do seu modelo de produção.

O diagrama a seguir demonstra que, com a API de implantação do MLflow e Azure Machine Learning, você pode implantar modelos criados com estruturas populares, como PyTorch, Tensorflow, scikit-learn, etc., como os serviços Web do Azure e gerenciá-los em seu espaço de trabalho. 

![ implantar modelos mlflow com o Azure Machine Learning](./media/how-to-deploy-mlflow-models/mlflow-diagram-deploy.png)


>[!NOTE]
> Como uma biblioteca de software livre, o MLflow muda com frequência. Dessa forma, a funcionalidade disponibilizada por meio da integração de Azure Machine Learning e MLflow deve ser considerada uma versão prévia e não tem suporte total da Microsoft.

## <a name="prerequisites"></a>Pré-requisitos

* Um modelo de aprendizado de máquina. Se você não tiver um modelo treinado, encontre o exemplo de notebook que melhor se adapta ao seu cenário de computação neste [repositório](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) e siga suas instruções. 
* [Configure o URI de acompanhamento de MLflow para se conectar Azure Machine Learning](how-to-use-mlflow.md#track-local-runs).
* Instale o pacote `azureml-mlflow`. 
    * Esse pacote automaticamente traz a `azureml-core` [Azure Machine Learning SDK do Python](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py), que fornece a conectividade para MLflow acessar seu espaço de trabalho.
* Veja quais [permissões de acesso você precisa para executar suas operações MLflow com seu espaço de trabalho](how-to-assign-roles.md#mlflow-operations). 

## <a name="deploy-to-azure-container-instance-aci"></a>Implantar na instância de contêiner do Azure (ACI)

Para implantar o modelo MLflow em um serviço Web Azure Machine Learning, seu modelo deve ser configurado com o [URI de acompanhamento do MLflow para se conectar ao Azure Machine Learning](how-to-use-mlflow.md). 

Configure sua configuração de implantação com o método [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Em seguida, registre e implante o modelo em uma etapa com o método de [implantação](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) do MLflow para Azure Machine Learning. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

## <a name="deploy-to-azure-kubernetes-service-aks"></a>Implantar no AKS (Serviço de Kubernetes do Azure)

Para implantar o modelo MLflow em um serviço Web Azure Machine Learning, seu modelo deve ser configurado com o [URI de acompanhamento do MLflow para se conectar ao Azure Machine Learning](how-to-use-mlflow.md). 

Para implantar no AKS, primeiro crie um cluster AKS. Crie um cluster AKS usando o método [ComputeTarget. Create ()](/python/api/azureml-core/azureml.core.computetarget?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Pode levar de 20-25 minutos para criar um novo cluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Configure sua configuração de implantação com o método [deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aciwebservice?preserve-view=true&view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Em seguida, registre e implante o modelo em uma etapa com o método de [implantação](https://www.mlflow.org/docs/latest/python_api/mlflow.azureml.html#mlflow.azureml.deploy) do MLflow para Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

A implantação do serviço pode levar vários minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não planeja usar seu serviço Web implantado, use `service.delete()` para excluí-lo do bloco de anotações.  Para obter mais informações, consulte a documentação de [WebService. Delete ()](/python/api/azureml-core/azureml.core.webservice%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truedelete--).

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

O [MLflow com blocos de anotações Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/using-mlflow) demonstram e se expandem sobre os conceitos apresentados neste artigo.

> [!NOTE]
> Um repositório controlado pela comunidade de exemplos que usam o mlflow pode ser encontrado em https://github.com/Azure/azureml-examples .

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar os modelos](concept-model-management-and-deployment.md).
* Monitore os modelos de produção para [descompasso de dados](./how-to-enable-data-collection.md).
* [Acompanhe as execuções de Azure Databricks com MLflow](how-to-use-mlflow-azure-databricks.md).

