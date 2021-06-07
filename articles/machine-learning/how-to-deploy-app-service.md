---
title: Implantar modelos de ml no serviço de Azure App (versão prévia)
titleSuffix: Azure Machine Learning
description: Saiba como usar Azure Machine Learning para implantar um modelo de ML treinado em um aplicativo Web usando o serviço Azure App.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, deploy, devx-track-azurecli
ms.openlocfilehash: 3b1b416f3fec9e40261a82c88260c041918c1424
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102521995"
---
# <a name="deploy-a-machine-learning-model-to-azure-app-service-preview"></a>Implantar um modelo de aprendizado de máquina no serviço Azure App (versão prévia)


Saiba como implantar um modelo de Azure Machine Learning como um aplicativo Web no serviço Azure App.

> [!IMPORTANT]
> Embora os serviços Azure Machine Learning e Azure App estejam geralmente disponíveis, a capacidade de implantar um modelo do serviço de Machine Learning para o serviço de aplicativo está em versão prévia.

Com Azure Machine Learning, você pode criar imagens do Docker de modelos de aprendizado de máquina treinados. Essa imagem contém um serviço Web que recebe dados, envia-os para o modelo e, em seguida, retorna a resposta. Azure App serviço pode ser usado para implantar a imagem e fornece os seguintes recursos:

* [Autenticação](../app-service/configure-authentication-provider-aad.md) avançada para segurança aprimorada. Os métodos de autenticação incluem Azure Active Directory e autenticação multifator.
* [Dimensionamento automático](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json) sem precisar reimplantar.
* [Suporte a TLS](../app-service/configure-ssl-certificate-in-code.md) para comunicações seguras entre clientes e o serviço.

Para obter mais informações sobre os recursos fornecidos pelo serviço Azure App, consulte [visão geral do serviço de aplicativo](../app-service/overview.md).

> [!IMPORTANT]
> Se você precisar da capacidade de registrar em log os dados de Pontuação usados com seu modelo implantado ou os resultados da pontuação, você deverá implantar no serviço kubernetes do Azure. Para obter mais informações, consulte [coletar dados em seus modelos de produção](how-to-enable-data-collection.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um Workspace do Azure Machine Learning. Para obter mais informações, consulte o artigo [criar um espaço de trabalho](how-to-manage-workspace.md) .
* O [CLI do Azure](/cli/azure/install-azure-cli).
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

    > [!IMPORTANT]
    > O SDK do Azure Machine Learning não fornece uma maneira para o serviço Web acessar seu datastore ou conjuntos de dados. Se você precisar que o modelo implantado acesse dados armazenados fora da implantação, como em uma conta de armazenamento do Azure, você deve desenvolver uma solução de código personalizado usando o SDK relevante. Por exemplo, o [SDK do armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python).
    >
    > Outra alternativa que pode funcionar para seu cenário é [previsões de lote](./tutorial-pipeline-batch-scoring-classification.md), que fornece acesso a repositórios de armazenamento durante a pontuação.

    Para obter mais informações sobre scripts de entrada, confira [Implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

* **Dependências**, como scripts auxiliares ou pacotes python/Conda necessários para executar o script ou modelo de entrada

Essas entidades são encapsuladas em uma __configuração de inferência__. A configuração de inferência faz referência ao script de entrada e a outras dependências.

> [!IMPORTANT]
> Ao criar uma configuração de inferência para uso com Azure App Service, você deve usar um objeto de [ambiente](/python/api/azureml-core/azureml.core.environment(class)) . Observe que, se você estiver definindo um ambiente personalizado, deverá adicionar o azureml-padrões com a versão >= 1.0.45 como uma dependência Pip. Esse pacote contém a funcionalidade necessária para hospedar o modelo como um serviço Web. O exemplo a seguir demonstra como criar um objeto de ambiente e usá-lo com uma configuração de inferência:
>
> ```python
> from azureml.core.environment import Environment
> from azureml.core.conda_dependencies import CondaDependencies
> from azureml.core.model import InferenceConfig
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
> Ao implantar no Azure App Service, você não precisa criar uma __configuração de implantação__.

## <a name="create-the-image"></a>Criar a imagem

Para criar a imagem do Docker que é implantada no serviço Azure App, use [Model. Package](/python/api/azureml-core/azureml.core.model.model). O trecho de código a seguir demonstra como criar uma nova imagem a partir do modelo e da configuração de inferência:

> [!NOTE]
> O trecho de código pressupõe que `model` contém um modelo registrado e que `inference_config` contém a configuração para o ambiente de inferência. Para obter mais informações, consulte [implantar modelos com Azure Machine Learning](how-to-deploy-and-where.md).

```python
from azureml.core import Model

package = Model.package(ws, [model], inference_config)
package.wait_for_creation(show_output=True)
# Display the package location/ACR path
print(package.location)
```

Quando `show_output=True` , a saída do processo de Build do Docker é mostrada. Quando o processo for concluído, a imagem terá sido criada no registro de contêiner do Azure para seu espaço de trabalho. Depois que a imagem tiver sido criada, o local no registro de contêiner do Azure será exibido. O local retornado está no formato `<acrinstance>.azurecr.io/package@sha256:<imagename>` . Por exemplo, `myml08024f78fd10.azurecr.io/package@sha256:20190827151241`.

> [!IMPORTANT]
> Salve as informações de local, pois elas são usadas durante a implantação da imagem.

## <a name="deploy-image-as-a-web-app"></a>Implantar imagem como um aplicativo Web

1. Use o comando a seguir para obter as credenciais de logon para o registro de contêiner do Azure que contém a imagem. Substituir `<acrinstance>` pelo valor retornado anteriormente de `package.location` :

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

    Neste exemplo, um tipo de preço __básico__ ( `--sku B1` ) é usado.

    > [!IMPORTANT]
    > As imagens criadas por Azure Machine Learning usam o Linux, portanto, você deve usar o `--is-linux` parâmetro.

1. Para criar o aplicativo Web, use o comando a seguir. Substituir `<app-name>` pelo nome que você deseja usar. Substitua `<acrinstance>` e `<imagename>` pelos valores de retornado `package.location` anteriormente:

    ```azurecli-interactive
    az webapp create --resource-group myresourcegroup --plan myplanname --name <app-name> --deployment-container-image-name <acrinstance>.azurecr.io/package@sha256:<imagename>
    ```

    Esse comando retorna informações semelhantes ao seguinte documento JSON:

    ```json
    {
    "adminSiteName": null,
    "appServicePlanName": "myplanname",
    "geoRegion": "West Europe",
    "hostingEnvironmentProfile": null,
    "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myplanname",
    "kind": "linux",
    "location": "West Europe",
    "maximumNumberOfWorkers": 1,
    "name": "myplanname",
    < JSON data removed for brevity. >
    "targetWorkerSizeId": 0,
    "type": "Microsoft.Web/serverfarms",
    "workerTierName": null
    }
    ```

    > [!IMPORTANT]
    > Neste ponto, o aplicativo Web foi criado. No entanto, como você não forneceu as credenciais para o registro de contêiner do Azure que contém a imagem, o aplicativo Web não está ativo. Na próxima etapa, você fornecerá as informações de autenticação para o registro de contêiner.

1. Para fornecer ao aplicativo Web as credenciais necessárias para acessar o registro de contêiner, use o comando a seguir. Substituir `<app-name>` pelo nome que você deseja usar. Substitua `<acrinstance>` e `<imagename>` pelos valores de retornado `package.location` anteriormente. Substitua `<username>` e `<password>` pelas informações de logon do ACR recuperadas anteriormente:

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group myresourcegroup --docker-custom-image-name <acrinstance>.azurecr.io/package@sha256:<imagename> --docker-registry-server-url https://<acrinstance>.azurecr.io --docker-registry-server-user <username> --docker-registry-server-password <password>
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
        "value": "DOCKER|myml08024f78fd10.azurecr.io/package@sha256:20190827195524"
    }
    ]
    ```

Neste ponto, o aplicativo Web começa a carregar a imagem.

> [!IMPORTANT]
> Pode levar vários minutos para que a imagem seja carregada. Para monitorar o progresso, use o seguinte comando:
>
> ```azurecli-interactive
> az webapp log tail --name <app-name> --resource-group myresourcegroup
> ```
>
> Depois que a imagem for carregada e o site estiver ativo, o log exibirá uma mensagem afirmando `Container <container name> for site <app-name> initialized successfully and is ready to serve requests` .

Depois que a imagem for implantada, você poderá encontrar o nome do host usando o seguinte comando:

```azurecli-interactive
az webapp show --name <app-name> --resource-group myresourcegroup
```

Esse comando retorna informações semelhantes ao nome de host a seguir: `<app-name>.azurewebsites.net` . Use esse valor como parte da __URL base__ para o serviço.

## <a name="use-the-web-app"></a>Usar o aplicativo Web

O serviço Web que passa solicitações para o modelo está localizado em `{baseurl}/score` . Por exemplo, `https://<app-name>.azurewebsites.net/score`. O código Python a seguir demonstra como enviar dados para a URL e exibir a resposta:

```python
import requests
import json

scoring_uri = "https://mywebapp.azurewebsites.net/score"

headers = {'Content-Type':'application/json'}

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10],
    [10,9,8,7,6,5,4,3,2,1]
]})

response = requests.post(scoring_uri, data=test_sample, headers=headers)
print(response.status_code)
print(response.elapsed)
print(response.json())
```

## <a name="next-steps"></a>Próximas etapas

* Saiba como configurar seu aplicativo Web na documentação do [serviço de aplicativo no Linux](/azure/app-service/containers/) .
* Saiba mais sobre o dimensionamento em introdução [ao dimensionamento automático no Azure](../azure-monitor/autoscale/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json).
* [Use um certificado TLS/SSL em seu serviço de Azure app](../app-service/configure-ssl-certificate-in-code.md).
* [Configure seu aplicativo do serviço de aplicativo para usar Azure Active Directory entrar](../app-service/configure-authentication-provider-aad.md).
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)