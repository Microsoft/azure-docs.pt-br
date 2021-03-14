---
title: Modelo de alto desempenho que atende ao Triton (versão prévia)
titleSuffix: Azure Machine Learning
description: Aprenda a implantar seu modelo com o servidor de inferência NVIDIA Triton em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 02/16/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: 2966b685e1904102467bf16994ea781556544047
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102519190"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Serviço de alto desempenho com o servidor de inferência de Triton (visualização) 

Saiba como usar o [servidor de inferência NVIDIA Triton](https://aka.ms/nvidia-triton-docs) para melhorar o desempenho do serviço Web usado para a inferência de modelo.

Uma das maneiras de implantar um modelo para a inferência é como um serviço Web. Por exemplo, uma implantação para o serviço kubernetes do Azure ou instâncias de contêiner do Azure. Por padrão, o Azure Machine Learning usa uma estrutura da Web de *uso geral* de thread único para implantações de serviço Web.

Triton é uma estrutura *otimizada para inferência*. Ele fornece melhor utilização de GPUs e inferência mais econômica. No lado do servidor, ele agrupa solicitações de entrada e envia esses lotes para inferência. O envio em lote utiliza melhor os recursos de GPU e é uma parte fundamental do desempenho do Triton.

> [!IMPORTANT]
> O uso de Triton para implantação do Azure Machine Learning está atualmente em versão __prévia__. A funcionalidade de visualização pode não ser coberta pelo atendimento ao cliente. Para obter mais informações, consulte os [termos de uso complementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Os trechos de código neste documento são para fins ilustrativos e podem não mostrar uma solução completa. Para o código de exemplo funcional, consulte os [exemplos de ponta a ponta de Triton em Azure Machine Learning](https://aka.ms/triton-aml-sample).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).
* Familiaridade com [como e onde implantar um modelo](how-to-deploy-and-where.md) com Azure Machine Learning.
* O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/) **ou** a extensão de [CLI do Azure](/cli/azure/) e [Machine Learning](reference-azure-machine-learning-cli.md).
* Uma instalação funcional do Docker para teste local. Para obter informações sobre como instalar e validar o Docker, consulte [orientação e configuração](https://docs.docker.com/get-started/) na documentação do Docker.

## <a name="architectural-overview"></a>Visão geral da arquitetura

Antes de tentar usar o Triton para seu próprio modelo, é importante entender como ele funciona com Azure Machine Learning e como ele se compara a uma implantação padrão.

**Implantação padrão sem Triton**

* Vários trabalhos do [Gunicorn](https://gunicorn.org/) são iniciados para lidar simultaneamente com as solicitações de entrada.
* Esses trabalhadores tratam o pré-processamento, a chamada do modelo e o pós-processamento. 
* Os clientes usam o __URI de Pontuação do am do Azure__. Por exemplo, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagrama de arquitetura de implantação normal, não Triton":::

**Implantando com o Triton diretamente**

* As solicitações vão diretamente para o servidor Triton.
* O Triton processa solicitações em lotes para maximizar a utilização da GPU.
* O cliente usa o __URI Triton__ para fazer solicitações. Por exemplo, `https://myservice.azureml.net/v2/models/${MODEL_NAME}/versions/${MODEL_VERSION}/infer`.

:::image type="content" source="./media/how-to-deploy-with-triton/triton-deploy.png" alt-text="Inferenceconfig implantação somente com Triton e sem middleware Python":::

**Implantação de configuração de inferência com Triton**

* Vários trabalhos do [Gunicorn](https://gunicorn.org/) são iniciados para lidar simultaneamente com as solicitações de entrada.
* As solicitações são encaminhadas para o **servidor Triton**. 
* O Triton processa solicitações em lotes para maximizar a utilização da GPU.
* O cliente usa o __URI de Pontuação do am do Azure__ para fazer solicitações. Por exemplo, `https://myservice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inference-config-deploy.png" alt-text="Implantação com middleware Triton e Python":::

O fluxo de trabalho para usar o Triton para a implantação do modelo é:

1. Sirva seu modelo com o Triton diretamente.
1. Verifique se você pode enviar solicitações para o modelo implantado por Triton.
1. Adicional Criar uma camada de middleware do Python para pré e pós-processamento do lado do servidor

## <a name="deploying-triton-without-python-pre--and-post-processing"></a>Implantando Triton sem Python pré e pós-processamento

Primeiro, siga as etapas abaixo para verificar se o servidor de inferência Triton pode servir para seu modelo.

### <a name="optional-define-a-model-config-file"></a>Adicional Definir um arquivo de configuração de modelo

O arquivo de configuração de modelo informa ao Triton quantas entradas esperadas e de quais dimensões essas entradas serão. Para obter mais informações sobre como criar o arquivo de configuração, consulte [configuração do modelo](https://aka.ms/nvidia-triton-docs) na documentação do NVIDIA.

> [!TIP]
> Usamos a `--strict-model-config=false` opção ao iniciar o servidor de inferência Triton, o que significa que você não precisa fornecer um `config.pbtxt` arquivo para modelos ONNX ou TensorFlow.
> 
> Para obter mais informações sobre essa opção, consulte [configuração de modelo gerado](https://aka.ms/nvidia-triton-docs) na documentação do NVIDIA.

### <a name="use-the-correct-directory-structure"></a>Usar a estrutura de diretório correta

Ao registrar um modelo com o Azure Machine Learning, você pode registrar arquivos individuais ou uma estrutura de diretório. Para usar o Triton, o registro do modelo deve ser para uma estrutura de diretório que contém um diretório chamado `triton` . A estrutura geral desse diretório é:

```bash
models
    - triton
        - model_1
            - model_version
                - model_file
                - config_file
        - model_2
            ...
```

> [!IMPORTANT]
> Essa estrutura de diretório é um repositório de modelo Triton e é necessário para que seus modelos funcionem com Triton. Para obter mais informações, consulte [repositórios do modelo Triton](https://aka.ms/nvidia-triton-docs) na documentação do NVIDIA.

### <a name="register-your-triton-model"></a>Registrar seu modelo Triton

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli-interactive
az ml model register -n my_triton_model -p models --model-framework=Multi
```

Para obter mais informações sobre `az ml model register` o, consulte a [documentação de referência](/cli/azure/ext/azure-cli-ml/ml/model).

# <a name="python"></a>[Python](#tab/python)


```python

from azureml.core.model import Model

model_path = "models"

model = Model.register(
    model_path=model_path,
    model_name="bidaf-9-tutorial",
    tags={"area": "Natural language processing", "type": "Question-answering"},
    description="Question answering from ONNX model zoo",
    workspace=ws,
    model_framework=Model.Framework.MULTI,  # This line tells us you are registering a Triton model
)

```
Para obter mais informações, consulte a documentação da [classe Model](/python/api/azureml-core/azureml.core.model.model).

---

### <a name="deploy-your-model"></a>Implantar o seu modelo

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

Se você tiver um cluster do serviço kubernetes do Azure habilitado para GPU chamado "AKS-GPU" criado por meio de Azure Machine Learning, poderá usar o comando a seguir para implantar seu modelo.

```azurecli
az ml model deploy -n triton-webservice -m triton_model:1 --dc deploymentconfig.json --compute-target aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import InferenceConfig
from random import randint

service_name = "triton-webservice"

config = AksWebservice.deploy_configuration(
    compute_target_name="aks-gpu",
    gpu_cores=1,
    cpu_cores=1,
    memory_gb=4,
    auth_enabled=True,
)

service = Model.deploy(
    workspace=ws,
    name=service_name,
    models=[model],
    deployment_config=config,
    overwrite=True,
)
```
---

Consulte [esta documentação para obter mais detalhes sobre a implantação de modelos](how-to-deploy-and-where.md).

### <a name="call-into-your-deployed-model"></a>Chamar seu modelo implantado

Primeiro, obtenha o URI de Pontuação e os tokens de portador.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)


```azurecli
az ml service show --name=triton-webservice
```
# <a name="python"></a>[Python](#tab/python)

```python
import requests

print(service.scoring_uri)
print(service.get_keys())

```

---

Em seguida, verifique se o serviço está em execução fazendo o: 

```{bash}
!curl -v $scoring_uri/v2/health/ready -H 'Authorization: Bearer '"$service_key"''
```

Esse comando retorna informações semelhantes às seguintes. Observe o `200 OK` ; esse status significa que o servidor Web está em execução.

```{bash}
*   Trying 127.0.0.1:8000...
* Connected to localhost (127.0.0.1) port 8000 (#0)
> GET /v2/health/ready HTTP/1.1
> Host: localhost:8000
> User-Agent: curl/7.71.1
> Accept: */*
>
* Mark bundle as not supporting multiuse
< HTTP/1.1 200 OK
HTTP/1.1 200 OK
```

Depois de executar uma verificação de integridade, você pode criar um cliente para enviar dados ao Triton para inferência. Para obter mais informações sobre como criar um cliente, consulte os [exemplos de cliente](https://aka.ms/nvidia-client-examples) na documentação do NVIDIA. Também há [exemplos de Python no GitHub Triton](https://aka.ms/nvidia-triton-docs).

Neste ponto, se você não quiser adicionar pré e pós-processamento do Python ao serviço Web implantado, você pode estar pronto. Se você quiser adicionar essa lógica de pré e pós-processamento, continue lendo.

## <a name="optional-re-deploy-with-a-python-entry-script-for-pre--and-post-processing"></a>Adicional Reimplantar com um script de entrada Python para pré e pós-processamento

Depois de verificar se o Triton é capaz de servir seu modelo, você pode adicionar código de pré e pós-processamento definindo um script de _entrada_. Esse arquivo é nomeado `score.py` . Para obter mais informações sobre scripts de entrada, consulte [definir um script de entrada](how-to-deploy-and-where.md#define-an-entry-script).

As duas etapas principais são inicializar um cliente HTTP Triton em seu `init()` método e chamar esse cliente em sua `run()` função.

### <a name="initialize-the-triton-client"></a>Inicializar o cliente Triton

Inclua um código como o exemplo a seguir em seu `score.py` arquivo. Triton em Azure Machine Learning espera ser abordada no localhost, porta 8000. Nesse caso, o localhost está dentro da imagem do Docker para essa implantação, não para uma porta em seu computador local:

> [!TIP]
> O `tritonhttpclient` pacote Pip está incluído no `AzureML-Triton` ambiente organizado, portanto, não é necessário especificá-lo como uma dependência Pip.

```python
import tritonhttpclient

def init():
    global triton_client
    triton_client = tritonhttpclient.InferenceServerClient(url="localhost:8000")
```

### <a name="modify-your-scoring-script-to-call-into-triton"></a>Modificar o script de Pontuação para chamar em Triton

O exemplo a seguir demonstra como solicitar dinamicamente os metadados para o modelo:

> [!TIP]
> Você pode solicitar dinamicamente os metadados de modelos que foram carregados com Triton usando o `.get_model_metadata` método do cliente Triton. Consulte o [bloco de anotações de exemplo](https://aka.ms/triton-aml-sample) para obter um exemplo de seu uso.

```python
input = tritonhttpclient.InferInput(input_name, data.shape, datatype)
input.set_data_from_numpy(data, binary_data=binary_data)

output = tritonhttpclient.InferRequestedOutput(
         output_name, binary_data=binary_data, class_count=class_count)

# Run inference
res = triton_client.infer(model_name,
                          [input]
                          request_id='0',
                          outputs=[output])

```

<a id="redeploy"></a>

### <a name="redeploy-with-an-inference-configuration"></a>Reimplantar com uma configuração de inferência

Uma configuração de inferência permite que você use um script de entrada, bem como o Azure Machine Learning processo de implantação usando o SDK do Python ou o CLI do Azure.

> [!IMPORTANT]
> Você deve especificar o `AzureML-Triton` [ambiente organizado](./resource-curated-environments.md).
>
> O exemplo de código Python é clonado `AzureML-Triton` em outro ambiente chamado `My-Triton` . O código de CLI do Azure também usa esse ambiente. Para obter mais informações sobre a clonagem de um ambiente, consulte a referência de [Environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment#clone-new-name-) .

# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

> [!TIP]
> Para obter mais informações sobre como criar uma configuração de inferência, consulte o [esquema de configuração de inferência](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.webservice import LocalWebservice
from azureml.core import Environment
from azureml.core.model import InferenceConfig


local_service_name = "triton-bidaf-onnx"
env = Environment.get(ws, "AzureML-Triton").clone("My-Triton")

for pip_package in ["nltk"]:
    env.python.conda_dependencies.add_pip_package(pip_package)

inference_config = InferenceConfig(
    entry_script="score_bidaf.py",  # This entry script is where we dispatch a call to the Triton server
    source_directory=os.path.join("..", "scripts"),
    environment=env
)

local_config = LocalWebservice.deploy_configuration(
    port=6789
)

local_service = Model.deploy(
    workspace=ws,
    name=local_service_name,
    models=[model],
    inference_config=inference_config,
    deployment_config=local_config,
    overwrite=True)

local_service.wait_for_deployment(show_output = True)
print(local_service.state)
# Print the URI you can use to call the local deployment
print(local_service.scoring_uri)
```

---

Após a conclusão da implantação, o URI de pontuação é exibido. Para essa implantação local, será `http://localhost:6789/score` . Se você implantar na nuvem, poderá usar o comando [AZ ml Service show](/cli/azure/ext/azure-cli-ml/ml/service#ext_azure_cli_ml_az_ml_service_show) CLI para obter o URI de pontuação.

Para obter informações sobre como criar um cliente que envia solicitações de inferência para o URI de pontuação, consulte [consumir um modelo implantado como um serviço Web](how-to-consume-web-service.md).

### <a name="setting-the-number-of-workers"></a>Definindo o número de trabalhadores

Para definir o número de trabalhadores em sua implantação, defina a variável de ambiente `WORKER_COUNT` . Dado que você tem um objeto de [ambiente](/python/api/azureml-core/azureml.core.environment.environment) chamado `env` , você pode fazer o seguinte:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Isso instruirá o Azure ML a acelerar o número de trabalhadores que você especificar.


## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja continuar a usar o espaço de trabalho Azure Machine Learning, mas deseja livrar-se do serviço implantado, use uma das seguintes opções:


# <a name="azure-cli"></a>[CLI do Azure](#tab/azcli)

```azurecli
az ml service delete -n triton-densenet-onnx
```
# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```


---

## <a name="next-steps"></a>Próximas etapas

* [Consulte amostras de ponta a ponta de Triton no Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Confira os [exemplos de cliente Triton](https://aka.ms/nvidia-client-examples)
* Ler a [documentação do servidor de inferência de Triton](https://aka.ms/nvidia-triton-docs)
* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)