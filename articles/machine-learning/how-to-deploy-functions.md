---
title: Implantar modelos de ml em aplicativos Azure Functions (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Machine Learning para empacotar e implantar um modelo como um serviço Web em um aplicativo Azure Functions.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: vaidyas
author: vaidya-s
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.topic: conceptual
ms.custom: how-to, racking-python, devx-track-azurecli
ms.openlocfilehash: 901e4d458cc2d77d4e7f13c1782b86c8532ca499
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327161"
---
# <a name="deploy-a-machine-learning-model-to-azure-functions-preview"></a>Implantar um modelo de aprendizado de máquina para Azure Functions (versão prévia)


Saiba como implantar um modelo de Azure Machine Learning como um aplicativo de funções no Azure Functions.

> [!IMPORTANT]
> Embora os Azure Machine Learning e Azure Functions estejam geralmente disponíveis, a capacidade de empacotar um modelo do serviço de Machine Learning para o Functions está em versão prévia.

Com Azure Machine Learning, você pode criar imagens do Docker de modelos de aprendizado de máquina treinados. O Azure Machine Learning agora tem a funcionalidade de visualização para criar esses modelos de aprendizado de máquina em aplicativos de funções, que podem ser [implantados em Azure Functions](../azure-functions/functions-deployment-technologies.md#docker-container).

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .
* O [CLI do Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* Um modelo de aprendizado de máquina treinado registrado em seu espaço de trabalho. Se você não tiver um modelo, use o [tutorial de classificação de imagem: treinar modelo](tutorial-train-models-with-aml.md) para treinar e registrar um.

    > [!IMPORTANT]
    > Os trechos de código neste artigo pressupõem que você definiu as seguintes variáveis:
    >
    > * `ws` -Seu espaço de trabalho do Azure Machine Learning.
    > * `model` -O modelo registrado que será implantado.
    > * `inference_config` -A configuração de inferência para o modelo.
    >
    > Para obter mais informações sobre como definir essas variáveis, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

## <a name="prepare-for-deployment"></a>Preparar para a implantação

Antes de implantar o, você deve definir o que é necessário para executar o modelo como um serviço Web. A lista a seguir descreve os principais itens necessários para uma implantação:

* Um __script de entrada__. Esse script aceita solicitações, pontua a solicitação usando o modelo e retorna os resultados.

    > [!IMPORTANT]
    > O script de entrada é específico para seu modelo; Ele deve entender o formato dos dados de solicitação de entrada, o formato dos dados esperados pelo seu modelo e o formato dos dados retornados aos clientes.
    >
    > Se os dados da solicitação estiverem em um formato que não pode ser usado pelo seu modelo, o script poderá transformá-lo em um formato aceitável. Ele também pode transformar a resposta antes de retornar a ela para o cliente.
    >
    > Por padrão, ao empacotar para funções, a entrada é tratada como texto. Se você estiver interessado em consumir os bytes brutos da entrada (por exemplo, para gatilhos de BLOB), deverá usar [AMLRequest para aceitar dados brutos](./how-to-deploy-advanced-entry-script.md#binary-data).

Para obter mais informações sobre o script de entrada, consulte [definir código de Pontuação](./how-to-deploy-and-where.md#define-an-entry-script)

* **Dependências**, como scripts auxiliares ou pacotes python/Conda necessários para executar o script ou modelo de entrada

Essas entidades são encapsuladas em uma __configuração de inferência__. A configuração de inferência faz referência ao script de entrada e a outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para uso com Azure Functions, você deve usar um objeto de [ambiente](/python/api/azureml-core/azureml.core.environment%28class%29?preserve-view=true&view=azure-ml-py) . Observe que, se você estiver definindo um ambiente personalizado, deverá adicionar o azureml-padrões com a versão >= 1.0.45 como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. O exemplo a seguir demonstra como criar um objeto de ambiente e usá-lo com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
>
> # Create an environment and add conda dependencies to it
> myenv = Environment(name="myenv")
> # Enable Docker based environment
> myenv.docker.enabled = True
> # Build conda dependencies
> myenv.python.conda_dependencies = CondaDependencies.create(conda_packages=['scikit-learn'],
>                                                            pip_packages=['azureml-defaults'])
> inference_config = InferenceConfig(entry_script="score.py", environment=myenv)
> ```

Para obter mais informações sobre ambientes, consulte [criar e gerenciar ambientes para treinamento e implantação](how-to-use-environments.md).

Para obter mais informações sobre a configuração de inferência, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

> [!IMPORTANT]
> Ao implantar o para o functions, você não precisa criar uma __configuração de implantação__.

## <a name="install-the-sdk-preview-package-for-functions-support"></a>Instalar o pacote de visualização do SDK para o suporte do Functions

Para criar pacotes para Azure Functions, você deve instalar o pacote de visualização do SDK.

```bash
pip install azureml-contrib-functions
```

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem do Docker que é implantada no Azure Functions, use [azureml. contrib. Functions. Package](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py) ou a função de pacote específica para o gatilho que você está interessado em usar. O trecho de código a seguir demonstra como criar um novo pacote com um gatilho de blob do modelo e a configuração de inferência:

> [!NOTE]
> O trecho de código pressupõe que `model` contém um modelo registrado e que `inference_config` contém a configuração para o ambiente de inferência. Para obter mais informações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.contrib.functions import package
from azureml.contrib.functions import BLOB_TRIGGER
blob = package(ws, [model], inference_config, functions_enabled=True, trigger=BLOB_TRIGGER, input_path="input/{blobname}.json", output_path="output/{blobname}_out.json")
blob.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(blob.location)
```

Quando `show_output=True` , a saída do processo de Build do Docker é mostrada. Quando o processo for concluído, a imagem terá sido criada no registro de contêiner do Azure para seu espaço de trabalho. Depois que a imagem tiver sido criada, o local no registro de contêiner do Azure será exibido. O local retornado está no formato `<acrinstance>.azurecr.io/package@sha256:<imagename>` .

> [!NOTE]
> O empacotamento para funções atualmente dá suporte a gatilhos HTTP, gatilhos de BLOB e gatilhos do barramento de serviço Para obter mais informações sobre gatilhos, consulte [associações de Azure Functions](../azure-functions/functions-bindings-storage-blob-trigger.md#blob-name-patterns).

> [!IMPORTANT]
> Salve as informações de local, pois elas são usadas durante a implantação da imagem.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo Web

1. Use o comando a seguir para obter as credenciais de logon para o registro de contêiner do Azure que contém a imagem. Substituir `<myacr>` pelo valor retornado anteriormente de `blob.location` : 

    ```azurecli-interactive
    az acr credential show --name <myacr>
    ```

    A saída desse comando é semelhante ao seguinte documento JSON:

    ```json
    {
    "passwords": [
        {
        "name": "password",
        "value": "Iv0lRZQ9762LUJrFiffo3P4sWgk4q+nW"
        },
        {
        "name": "password2",
        "value": "=pKCxHatX96jeoYBWZLsPR6opszr==mg"
        }
    ],
    "username": "myml08024f78fd10"
    }
    ```

    Salve o valor de __nome de usuário__ e uma das __senhas__.

1. Se você ainda não tiver um grupo de recursos ou um plano do serviço de aplicativo para implantar o serviço, os comandos a seguir demonstrarão como criar ambos:

    ```azurecli-interactive
    az group create --name myresourcegroup --location "West Europe"
    az appservice plan create --name myplanname --resource-group myresourcegroup --sku B1 --is-linux
    ```

    Neste exemplo, um tipo de preço _básico do Linux_ ( `--sku B1` ) é usado.

    > [!IMPORTANT]
    > As imagens criadas por Azure Machine Learning usam o Linux, portanto, você deve usar o `--is-linux` parâmetro.

1. Crie a conta de armazenamento a ser usada para o armazenamento de trabalhos da Web e obtenha sua cadeia de conexão. Substituir `<webjobStorage>` pelo nome que você deseja usar.

    ```azurecli-interactive
    az storage account create --name <webjobStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <webJobStorage> --query connectionString --output tsv
    ```

1. Para criar o aplicativo de funções, use o comando a seguir. Substituir `<app-name>` pelo nome que você deseja usar. Substitua `<acrinstance>` e `<imagename>` pelos valores de retornado `package.location` anteriormente. Substitua `<webjobStorage>` pelo nome da conta de armazenamento da etapa anterior:

    ```azurecli-interactive
    az functionapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package:<imagename> --storage-account <webjobStorage>
    ```

    > [!IMPORTANT]
    > Neste ponto, o aplicativo de funções foi criado. No entanto, como você não forneceu a cadeia de conexão para o gatilho de BLOB ou credenciais para o registro de contêiner do Azure que contém a imagem, o aplicativo de funções não está ativo. Nas próximas etapas, você fornecerá a cadeia de conexão e as informações de autenticação para o registro de contêiner. 

1. Crie a conta de armazenamento a ser usada para o armazenamento do gatilho de BLOB e obtenha sua cadeia de conexão. Substituir `<triggerStorage>` pelo nome que você deseja usar.

    ```azurecli-interactive
    az storage account create --name <triggerStorage> --location westeurope --resource-group myresourcegroup --sku Standard_LRS
    ```
    ```azurecli-interactive
    az storage account show-connection-string --resource-group myresourcegroup --name <triggerStorage> --query connectionString --output tsv
    ```
    Registre essa cadeia de conexão para fornecer ao aplicativo de funções. Usaremos isso mais tarde quando solicitarmos `<triggerConnectionString>`

1. Crie os contêineres para a entrada e a saída na conta de armazenamento. Substituir `<triggerConnectionString>` pela cadeia de conexão retornada anteriormente:

    ```azurecli-interactive
    az storage container create -n input --connection-string <triggerConnectionString>
    ```
    ```azurecli-interactive
    az storage container create -n output --connection-string <triggerConnectionString>
    ```

1. Para associar a cadeia de conexão do gatilho ao aplicativo de funções, use o comando a seguir. Substituir `<app-name>` pelo nome do aplicativo de funções. Substituir `<triggerConnectionString>` pela cadeia de conexão retornada anteriormente:

    ```azurecli-interactive
    az functionapp config appsettings set --name <app-name> --resource-group myresourcegroup --settings "TriggerConnectionString=<triggerConnectionString>"
    ```
1. Você precisará recuperar a marca associada ao contêiner criado usando o comando a seguir. Substituir `<username>` pelo nome de usuário retornado anteriormente do registro de contêiner:

    ```azurecli-interactive
    az acr repository show-tags --repository package --name <username> --output tsv
    ```
    Salve o valor retornado, ele será usado como o `imagetag` na próxima etapa.

1. Para fornecer ao aplicativo de funções as credenciais necessárias para acessar o registro de contêiner, use o comando a seguir. Substituir `<app-name>` pelo nome do aplicativo de funções. Substitua `<acrinstance>` e `<imagetag>` pelos valores da chamada AZ CLI na etapa anterior. Substitua `<username>` e `<password>` pelas informações de logon do ACR recuperadas anteriormente:

    ```azurecli-interactive
    az functionapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package:<imagetag> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
    ```

    Esse comando retorna informações semelhantes ao seguinte documento JSON:

    ```json
    [
    {
        "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
        "slotSetting": false,
        "value": "false"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_URL",
        "slotSetting": false,
        "value": "https://myml08024f78fd10.azurecr.io"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_USERNAME",
        "slotSetting": false,
        "value": "myml08024f78fd10"
    },
    {
        "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
        "slotSetting": false,
        "value": null
    },
    {
        "name": "DOCKER_CUSTOM_IMAGE_NAME",
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package:20190827195524"
    }
    ]
    ```

Neste ponto, o aplicativo de funções começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar vários minutos para que a imagem seja carregada. Você pode monitorar o progresso usando o portal do Azure.

## <a name="test-the-deployment"></a>Teste a implantação

Depois que a imagem for carregada e o aplicativo estiver disponível, use as seguintes etapas para disparar o aplicativo:

1. Crie um arquivo de texto que contenha os dados esperados pelo arquivo score.py. O exemplo a seguir funcionaria com um score.py que espera uma matriz de 10 números:

    ```json
    {"data": [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10], [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
    ```

    > [!IMPORTANT]
    > O formato dos dados depende do que o seu score.py e o modelo esperam.

2. Use o comando a seguir para carregar esse arquivo para o contêiner de entrada no blob de armazenamento de gatilho criado anteriormente. Substituir `<file>` pelo nome do arquivo que contém os dados. Substitua `<triggerConnectionString>` pela cadeia de conexão retornada anteriormente. Neste exemplo, `input` é o nome do contêiner de entrada criado anteriormente. Se você usou um nome diferente, substitua este valor:

    ```azurecli-interactive
    az storage blob upload --container-name input --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    A saída desse comando é semelhante ao JSON a seguir:

    ```json
    {
    "etag": "\"0x8D7C21528E08844\"",
    "lastModified": "2020-03-06T21:27:23+00:00"
    }
    ```

3. Para exibir a saída produzida pela função, use o comando a seguir para listar os arquivos de saída gerados. Substitua `<triggerConnectionString>` pela cadeia de conexão retornada anteriormente. Neste exemplo, `output` é o nome do contêiner de saída criado anteriormente. Se você usou um nome diferente, substitua este valor:

    ```azurecli-interactive
    az storage blob list --container-name output --connection-string <triggerConnectionString> --query '[].name' --output tsv
    ```

    A saída desse comando é semelhante a `sample_input_out.json` .

4. Para baixar o arquivo e inspecionar o conteúdo, use o comando a seguir. Substituir pelo `<file>` nome de arquivo retornado pelo comando anterior. Substituir `<triggerConnectionString>` pela cadeia de conexão retornada anteriormente: 

    ```azurecli-interactive
    az storage blob download --container-name output --file <file> --name <file> --connection-string <triggerConnectionString>
    ```

    Quando o comando for concluído, abra o arquivo. Ele contém os dados retornados pelo modelo.

Para obter mais informações sobre como usar gatilhos de BLOB, consulte o artigo [criar uma função disparada pelo armazenamento de BLOBs do Azure](../azure-functions/functions-create-storage-blob-triggered-function.md) .

## <a name="next-steps"></a>Próximas etapas

* Saiba como configurar seu aplicativo de funções na documentação do [Functions](../azure-functions/functions-create-function-linux-custom-image.md) .
* Saiba mais sobre o armazenamento de BLOBs que dispara [associações de armazenamento de BLOBs do Azure](../azure-functions/functions-bindings-storage-blob.md).
* [Implante seu modelo no serviço Azure app](how-to-deploy-app-service.md).
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Referência da API](/python/api/azureml-contrib-functions/azureml.contrib.functions?preserve-view=true&view=azure-ml-py)
