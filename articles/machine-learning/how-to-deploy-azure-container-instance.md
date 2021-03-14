---
title: Como implantar modelos em instâncias de contêiner do Azure
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando instâncias de contêiner do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, deploy
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: 5bf3c92f07cc33b35a070a3479e0063a63c9e43a
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522012"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Implantar um modelo nas Instâncias de Contêiner do Azure

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web em ACI (instâncias de contêiner do Azure). Use as instâncias de contêiner do Azure se uma das seguintes condições for verdadeira:

- Você precisa implantar rapidamente e validar o modelo. Você não precisa criar contêineres ACI antes do tempo. Eles são criados como parte do processo de implantação.
- Você está testando um modelo ainda em desenvolvimento. 

Para obter informações sobre a disponibilidade de cota e região para ACI, consulte o artigo [cotas e disponibilidade de região para instâncias de contêiner do Azure](../container-instances/container-instances-quotas.md) .

> [!IMPORTANT]
> É altamente recomendável depurar localmente antes de implantar no serviço Web, para obter mais informações, consulte [depurar localmente](./how-to-troubleshoot-deployment-local.md)
>
> Você também pode ver o Azure Machine Learning - [Implantar no notebook local](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se você não tiver um modelo registrado, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](/python/api/overview/azure/ml/intro)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Os trechos de código __Python__ neste artigo pressupõem que as seguintes variáveis sejam definidas:

    * `ws` -Defina para seu espaço de trabalho.
    * `model` -Defina para o modelo registrado.
    * `inference_config` – Defina para a configuração de inferência para o modelo.

    Para obter mais informações sobre como definir essas variáveis, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- Os trechos de código da __CLI__ neste artigo pressupõem que você criou um `inferenceconfig.json` documento. Para obter mais informações sobre como criar este documento, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="limitations"></a>Limitações

* Ao usar instâncias de contêiner do Azure em uma rede virtual, a rede virtual deve estar no mesmo grupo de recursos que seu espaço de trabalho do Azure Machine Learning.
* Ao usar as instâncias de contêiner do Azure dentro da rede virtual, o ACR (registro de contêiner do Azure) para seu espaço de trabalho também não pode estar na rede virtual.

Para obter mais informações, consulte [como proteger o inferência com redes virtuais](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

## <a name="deploy-to-aci"></a>Implantar no ACI

Para implantar um modelo em instâncias de contêiner do Azure, crie uma __configuração de implantação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Você também precisa de uma __configuração de inferência__, que descreve o ambiente necessário para hospedar o modelo e o serviço Web. Para obter mais informações sobre como criar a configuração de inferência, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

> [!NOTE]
> * ACI é adequado apenas para modelos pequenos com menos de 1 GB de tamanho. 
> * É recomendável usar o AKS de nó único para modelos maiores de desenvolvimento/teste.
> * O número de modelos a serem implantados é limitado a modelos de 1.000 por implantação (por contêiner). 

### <a name="using-the-sdk"></a>Usar o SDK

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [Modelo. implantar](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](/python/api/azureml-core/azureml.core.webservice%28class%29#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando a CLI

Para implantar usando a CLI, use o comando a seguir. Substituir `mymodel:1` pelo nome e versão do modelo registrado. Substitua `myservice` pelo nome para fornecer este serviço:

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

Para obter mais informações, consulte a referência de [implantação do modelo AZ ml](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) . 

## <a name="using-vs-code"></a>Usar o VS Code

Consulte [implantar seus modelos com vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> Você não precisa criar um contêiner ACI para testar com antecedência. Os contêineres ACI são criados conforme necessário.

> [!IMPORTANT]
> Acrescentamos ID de espaço de trabalho com hash a todos os recursos de ACI subjacentes que são criados, todos os nomes de ACI do mesmo espaço de trabalho terão o mesmo sufixo. O nome do serviço Azure Machine Learning ainda seria o mesmo cliente fornecido "service_name" e todas as APIs do SDK do Azure Machine Learning voltadas para o usuário não precisam de nenhuma alteração. Não oferecemos nenhuma garantia sobre os nomes dos recursos subjacentes que estão sendo criados.

## <a name="next-steps"></a>Próximas etapas

* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Atualizar o serviço Web](how-to-deploy-update-web-service.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)