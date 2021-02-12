---
title: Modelo de alto desempenho que atende ao Triton (versão prévia)
titleSuffix: Azure Machine Learning
description: Aprenda a implantar seu modelo com o servidor de inferência NVIDIA Triton em Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 09/23/2020
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: c5db04a673c1cdc0c0f24e128f340f4ae55fea81
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825515"
---
# <a name="high-performance-serving-with-triton-inference-server-preview"></a>Serviço de alto desempenho com o servidor de inferência de Triton (visualização) 

Saiba como usar o [servidor de inferência NVIDIA Triton](https://developer.nvidia.com/nvidia-triton-inference-server) para melhorar o desempenho do serviço Web usado para a inferência de modelo.

Uma das maneiras de implantar um modelo para a inferência é como um serviço Web. Por exemplo, uma implantação para o serviço kubernetes do Azure ou instâncias de contêiner do Azure. Por padrão, o Azure Machine Learning usa uma estrutura da Web de *uso geral* de thread único para implantações de serviço Web.

Triton é uma estrutura *otimizada para inferência*. Ele fornece melhor utilização de GPUs e inferência mais econômica. No lado do servidor, ele agrupa solicitações de entrada e envia esses lotes para inferência. O envio em lote utiliza melhor os recursos de GPU e é uma parte fundamental do desempenho do Triton.

> [!IMPORTANT]
> O uso de Triton para implantação do Azure Machine Learning está atualmente em versão __prévia__. A funcionalidade de visualização pode não ser coberta pelo atendimento ao cliente. Para obter mais informações, consulte os [termos de uso complementares para visualizações de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!TIP]
> Os trechos de código neste documento são para fins ilustrativos e podem não mostrar uma solução completa. Para o código de exemplo funcional, consulte os [exemplos de ponta a ponta de Triton em Azure Machine Learning](https://github.com/Azure/azureml-examples/tree/main/tutorials).

## <a name="prerequisites"></a>Pré-requisitos

* Uma **assinatura do Azure**. Se você não tiver uma, experimente a [versão paga ou gratuita do Azure Machine Learning](https://aka.ms/AMLFree).
* Familiaridade com [como e onde implantar um modelo](how-to-deploy-and-where.md) com Azure Machine Learning.
* O [SDK do Azure Machine Learning para Python](/python/api/overview/azure/ml/?view=azure-ml-py) **ou** a extensão de [CLI do Azure](/cli/azure/?view=azure-cli-latest) e [Machine Learning](reference-azure-machine-learning-cli.md).
* Uma instalação funcional do Docker para teste local. Para obter informações sobre como instalar e validar o Docker, consulte [orientação e configuração](https://docs.docker.com/get-started/) na documentação do Docker.

## <a name="architectural-overview"></a>Visão geral da arquitetura

Antes de tentar usar o Triton para seu próprio modelo, é importante entender como ele funciona com Azure Machine Learning e como ele se compara a uma implantação padrão.

**Implantação padrão sem Triton**

* Vários trabalhos do [Gunicorn](https://gunicorn.org/) são iniciados para lidar simultaneamente com as solicitações de entrada.
* Esses trabalhadores tratam o pré-processamento, a chamada do modelo e o pós-processamento. 
* As solicitações de inferência usam o __URI de Pontuação__. Por exemplo, `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/normal-deploy.png" alt-text="Diagrama de arquitetura de implantação normal, não Triton":::

### <a name="setting-the-number-of-workers"></a>Definindo o número de trabalhadores

Para definir o número de trabalhadores em sua implantação, defina a variável de ambiente `WORKER_COUNT` . Dado que você tem um objeto de [ambiente](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py) chamado `env` , você pode fazer o seguinte:

```{py}
env.environment_variables["WORKER_COUNT"] = "1"
```

Isso instruirá o Azure ML a acelerar o número de trabalhadores que você especificar.


**Implantação de configuração de inferência com Triton**

* Vários trabalhos do [Gunicorn](https://gunicorn.org/) são iniciados para lidar simultaneamente com as solicitações de entrada.
* As solicitações são encaminhadas para o **servidor Triton**. 
* O Triton processa solicitações em lotes para maximizar a utilização da GPU.
* O cliente usa o __URI de Pontuação__ para fazer solicitações. Por exemplo, `https://myserevice.azureml.net/score`.

:::image type="content" source="./media/how-to-deploy-with-triton/inferenceconfig-deploy.png" alt-text="Implantação do Inferenceconfig com Triton":::

O fluxo de trabalho para usar o Triton para a implantação do modelo é:

1. Verifique se Triton pode servir para seu modelo.
1. Verifique se você pode enviar solicitações para o modelo implantado por Triton.
1. Incorpore seu código específico do Triton à sua implantação AML.

## <a name="verify-that-triton-can-serve-your-model"></a>Verifique se o Triton pode servir para seu modelo

Primeiro, siga as etapas abaixo para verificar se o servidor de inferência Triton pode servir para seu modelo.

### <a name="optional-define-a-model-config-file"></a>Adicional Definir um arquivo de configuração de modelo

O arquivo de configuração de modelo informa ao Triton quantas entradas esperadas e de quais dimensões essas entradas serão. Para obter mais informações sobre como criar o arquivo de configuração, consulte [configuração do modelo](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html) na documentação do NVIDIA.

> [!TIP]
> Usamos a `--strict-model-config=false` opção ao iniciar o servidor de inferência Triton, o que significa que você não precisa fornecer um `config.pbtxt` arquivo para modelos ONNX ou TensorFlow.
> 
> Para obter mais informações sobre essa opção, consulte [configuração de modelo gerado](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_configuration.html#generated-model-configuration) na documentação do NVIDIA.

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
> Essa estrutura de diretório é um repositório de modelo Triton e é necessário para que seus modelos funcionem com Triton. Para obter mais informações, consulte [repositórios do modelo Triton](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/model_repository.html) na documentação do NVIDIA.

### <a name="test-with-triton-and-docker"></a>Testar com o Triton e o Docker

Para testar seu modelo para certificar-se de que ele é executado com Triton, você pode usar o Docker. Os comandos a seguir efetuam pull do contêiner Triton para o computador local e, em seguida, iniciam o servidor Triton:

1. Para efetuar pull da imagem do servidor Triton para o computador local, use o seguinte comando:

    ```bash
    docker pull nvcr.io/nvidia/tritonserver:20.09-py3
    ```

1. Para iniciar o servidor Triton, use o comando a seguir. Substitua `<path-to-models/triton>` pelo caminho para o repositório do modelo de Triton que contém seus modelos:

    ```bash
    docker run --rm -ti -v<path-to-models/triton>:/models nvcr.io/nvidia/tritonserver:20.09-py3 tritonserver --model-repository=/models --strict-model-config=false
    ```

    > [!IMPORTANT]
    > Se estiver usando o Windows, você poderá ser solicitado a permitir conexões de rede para esse processo na primeira vez que executar o comando. Nesse caso, selecione para habilitar o acesso.

    Depois de iniciada, informações semelhantes ao texto a seguir são registradas na linha de comando:

    ```bash
    I0923 19:21:30.582866 1 http_server.cc:2705] Started HTTPService at 0.0.0.0:8000
    I0923 19:21:30.626081 1 http_server.cc:2724] Started Metrics Service at 0.0.0.0:8002
    ```

    A primeira linha indica o endereço do serviço Web. Nesse caso, `0.0.0.0:8000` , que é o mesmo que `localhost:8000` .

1. Use um utilitário como a rotação para acessar o ponto de extremidade de integridade.

    ```bash
    curl -L -v -i localhost:8000/v2/health/ready
    ```

    Esse comando retorna informações semelhantes às seguintes. Observe o `200 OK` ; esse status significa que o servidor Web está em execução.

    ```bash
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

Além de uma verificação de integridade básica, você pode criar um cliente para enviar dados ao Triton para inferência. Para obter mais informações sobre como criar um cliente, consulte os [exemplos de cliente](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/client_example.html) na documentação do NVIDIA. Também há [exemplos de Python no GitHub Triton](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples).

Para obter mais informações sobre como executar o Triton usando o Docker, consulte [executando o Triton em um sistema com uma GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-with-a-gpu) e [executando o Triton em um sistema sem uma GPU](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/run.html#running-triton-on-a-system-without-a-gpu).

### <a name="register-your-model"></a>Registrar seu modelo

Agora que você verificou que seu modelo funciona com o Triton, registre-o com Azure Machine Learning. O registro de modelo armazena seus arquivos de modelo no espaço de trabalho Azure Machine Learning e são usados ao implantar com o SDK do Python e CLI do Azure.

Os exemplos a seguir demonstram como registrar os modelos:

# <a name="python"></a>[Python](#tab/python)

```python
from azureml.core.model import Model

model = Model.register(
    model_path=os.path.join("..", "triton"),
    model_name="bidaf_onnx",
    tags={'area': "Natural language processing", 'type': "Question answering"},
    description="Question answering model from ONNX model zoo",
    workspace=ws
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az ml model register --model-path='triton' \
--name='bidaf_onnx' \
--workspace-name='<my_workspace>'
```
---

<a id="processing"></a>

## <a name="verify-you-can-call-into-your-model"></a>Verifique se você pode chamar seu modelo

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

## <a name="redeploy-with-an-inference-configuration"></a>Reimplantar com uma configuração de inferência

Uma configuração de inferência permite que você use um script de entrada, bem como o Azure Machine Learning processo de implantação usando o SDK do Python ou o CLI do Azure.

> [!IMPORTANT]
> Você deve especificar o `AzureML-Triton` [ambiente organizado](./resource-curated-environments.md).
>
> O exemplo de código Python é clonado `AzureML-Triton` em outro ambiente chamado `My-Triton` . O código de CLI do Azure também usa esse ambiente. Para obter mais informações sobre a clonagem de um ambiente, consulte a referência de [Environment. Clone ()](/python/api/azureml-core/azureml.core.environment.environment?preserve-view=true&view=azure-ml-py#clone-new-name-) .

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

> [!TIP]
> Para obter mais informações sobre como criar uma configuração de inferência, consulte o [esquema de configuração de inferência](./reference-azure-machine-learning-cli.md#inference-configuration-schema).

```azurecli
az ml model deploy -n triton-densenet-onnx \
-m densenet_onnx:1 \
--ic inference-config.json \
-e My-Triton --dc deploymentconfig.json \
--overwrite --compute-target=aks-gpu
```

---

Após a conclusão da implantação, o URI de pontuação é exibido. Para essa implantação local, será `http://localhost:6789/score` . Se você implantar na nuvem, poderá usar o comando [AZ ml Service show](/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext_azure_cli_ml_az_ml_service_show) CLI para obter o URI de pontuação.

Para obter informações sobre como criar um cliente que envia solicitações de inferência para o URI de pontuação, consulte [consumir um modelo implantado como um serviço Web](how-to-consume-web-service.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você planeja continuar a usar o espaço de trabalho Azure Machine Learning, mas deseja livrar-se do serviço implantado, use uma das seguintes opções:

# <a name="python"></a>[Python](#tab/python)

```python
local_service.delete()
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az ml service delete -n triton-densenet-onnx
```

---

## <a name="next-steps"></a>Próximas etapas

* [Consulte amostras de ponta a ponta de Triton no Azure Machine Learning](https://aka.ms/aml-triton-sample)
* Confira os [exemplos de cliente Triton](https://github.com/triton-inference-server/server/tree/master/src/clients/python/examples)
* Ler a [documentação do servidor de inferência de Triton](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/index.html)
* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)