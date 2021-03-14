---
title: Solucionando problemas de implantação de modelo local
titleSuffix: Azure Machine Learning
description: Experimente uma implantação de modelo local como uma primeira etapa na solução de problemas de erros de implantação de modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: gvashishtha
ms.author: gopalv
ms.reviewer: luquinta
ms.date: 11/25/2020
ms.topic: troubleshooting
ms.custom: devx-track-python, deploy, contperf-fy21q2
ms.openlocfilehash: 69ac47296cb4624de6cdf05ddb3e72973751f631
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519615"
---
# <a name="troubleshooting-with-a-local-model-deployment"></a>Solução de problemas com uma implantação de modelo local

Experimente uma implantação de modelo local como uma primeira etapa para solucionar problemas de implantação em ACI (instâncias de contêiner do Azure) ou AKS (serviço kubernetes do Azure).  O uso de um serviço Web local torna mais fácil identificar e corrigir erros Azure Machine Learning comuns de implantação do serviço Web Docker.

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).
* Opção A (**recomendada**) – depurar localmente em Azure Machine Learning instância de computação
   * Um Workspace do Azure Machine Learning com [instância de computação](how-to-deploy-local-container-notebook-vm.md) em execução
* Opção B-depurar localmente em sua computação
   * O [SDK do Azure Machine Learning](/python/api/overview/azure/ml/install).
   * O [CLI do Azure](/cli/azure/install-azure-cli).
   * A [Extensão da CLI do Azure Machine Learning](reference-azure-machine-learning-cli.md).
   * Ter uma instalação do Docker em funcionamento no sistema local. 
   * Para verificar a instalação do Docker, use o comando `docker run hello-world` em um terminal ou prompt de comando. Para obter informações sobre a instalação do Docker ou solução de erros do Docker, confira a [Documentação do Docker](https://docs.docker.com/).

## <a name="debug-locally"></a>Depurar Localmente

Você pode encontrar um exemplo de [bloco de anotações de implantação local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/deploy-to-local/register-model-deploy-local.ipynb) no repositório  [MachineLearningNotebooks](https://github.com/Azure/MachineLearningNotebooks) para explorar uma amostra executável.

> [!WARNING]
> Não há suporte para implantações de serviço Web local para cenários de produção.

Para implantar localmente, modifique o código para usar `LocalWebservice.deploy_configuration()` para criar uma configuração de implantação. Em seguida, use `Model.deploy()` para implantar o serviço. O exemplo a seguir implanta um modelo (contido na variável de modelo) como serviço Web local:

```python
from azureml.core.environment import Environment
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import LocalWebservice


# Create inference configuration based on the environment definition and the entry script
myenv = Environment.from_conda_specification(name="env", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
# Create a local deployment, using port 8890 for the web service endpoint
deployment_config = LocalWebservice.deploy_configuration(port=8890)
# Deploy the service
service = Model.deploy(
    ws, "mymodel", [model], inference_config, deployment_config)
# Wait for the deployment to complete
service.wait_for_deployment(True)
# Display the port that the web service is available on
print(service.port)
```

Se você estiver definindo seu próprio Conda de especificação YAML, liste azureml-defaults versão >= 1.0.45 como uma dependência Pip. Esse pacote é necessário para hospedar o modelo como um serviço Web.

A essa altura, você pode trabalhar com o serviço normalmente. O código a seguir demonstra o envio de dados para o serviço:

```python
import json

test_sample = json.dumps({'data': [
    [1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
    [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]
]})

test_sample = bytes(test_sample, encoding='utf8')

prediction = service.run(input_data=test_sample)
print(prediction)
```

Para obter mais informações sobre como personalizar o ambiente do Python, confira [Criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md). 

### <a name="update-the-service"></a>Atualizar o serviço

Durante os testes locais, talvez seja necessário atualizar o arquivo `score.py` para adicionar o log ou tentar resolver os problemas detectados. Para recarregar as alterações no arquivo `score.py`, use `reload()`. Por exemplo, o código a seguir recarrega o script para o serviço e depois envia dados a ele. Os dados são pontuados usando o arquivo `score.py` atualizado:

> [!IMPORTANT]
> O método `reload` só está disponível para implantações locais. Para obter informações sobre como atualizar uma implantação para outro destino de computação, consulte [como atualizar seu WebService](how-to-deploy-update-web-service.md).

```python
service.reload()
print(service.run(input_data=test_sample))
```

> [!NOTE]
> O script é recarregado no local especificado pelo objeto `InferenceConfig` usado pelo serviço.

Para alterar o modelo, as dependências Conda ou a configuração de implantação, use [update()](/python/api/azureml-core/azureml.core.webservice%28class%29#update--args-). O exemplo a seguir atualiza o modelo usado pelo serviço:

```python
service.update([different_model], inference_config, deployment_config)
```

### <a name="delete-the-service"></a>Excluir o serviço

Para excluir o serviço, use [delete()](/python/api/azureml-core/azureml.core.webservice%28class%29#delete--).

### <a name="inspect-the-docker-log"></a><a id="dockerlog"></a> Inspecionar o log do Docker

Você pode imprimir mensagens de log do mecanismo do Docker detalhadas do objeto de serviços. Você pode exibir o log para implantações de ACI, AKS e local. O exemplo a seguir demonstra como fazer imprimir os logs.

```python
# if you already have the service object handy
print(service.get_logs())

# if you only know the name of the service (note there might be multiple services with the same name but different version number)
print(ws.webservices['mysvc'].get_logs())
```

Se você vir a linha que `Booting worker with pid: <pid>` está ocorrendo várias vezes nos logs, isso significa que não há memória suficiente para iniciar o trabalho.
Você pode resolver o erro aumentando o valor de `memory_gb` em `deployment_config`

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a implantação:

* [Como solucionar problemas de implantações remotas](how-to-troubleshoot-deployment.md)
* [Como e onde implantar](how-to-deploy-and-where.md)
* [Tutorial: Treinar e implantar modelos](tutorial-train-models-with-aml.md)
* [Como executar e depurar experimentos localmente](./how-to-debug-visual-studio-code.md)