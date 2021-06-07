---
title: Memória de modelo de perfil e uso de CPU
titleSuffix: Azure Machine Learning
description: Saiba como criar o perfil de seu modelo antes da implantação. A criação de perfil determina a memória e o uso da CPU do seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "93320401"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Criar o perfil de seu modelo para determinar a utilização de recursos

Este artigo mostra como criar o perfil de um Machine Learning como modelo para determinar a quantidade de CPU e memória que você precisará alocar para o modelo ao implantá-lo como um serviço Web.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo pressupõe que você tenha treinado e registrado um modelo com Azure Machine Learning. Consulte o [tutorial de exemplo aqui](how-to-train-scikit-learn.md) para obter um exemplo de treinamento e registro de um modelo scikit-learn com Azure Machine Learning.

## <a name="limitations"></a>Limitações

* A criação de perfil não funcionará quando o ACR (registro de contêiner do Azure) para seu espaço de trabalho estiver por trás de uma rede virtual.

## <a name="run-the-profiler"></a>Executar o criador de perfil

Depois de registrar seu modelo e preparar os outros componentes necessários para sua implantação, você pode determinar a CPU e a memória que o serviço implantado precisará. A criação de perfil testa o serviço que executa o modelo e retorna informações como uso da CPU, uso de memória e latência de resposta. Ele também fornece uma recomendação para a CPU e a memória com base no uso de recursos.

Para criar o perfil de seu modelo, será necessário:
* Um modelo registrado.
* Uma configuração de inferência com base em sua definição de ambiente de inferência e script de entrada.
* Um conjunto de dados de tabela de coluna única, em que cada linha contém uma cadeia de caracteres que representa um exemplo de dado de solicitação

> [!IMPORTANT]
> Neste ponto, damos suporte apenas à criação de perfil de serviços que esperam que seus dados de solicitação sejam uma cadeia de caracteres, por exemplo: JSON serializado, texto, imagem serializada de cadeia de caracteres, etc. O conteúdo de cada linha do conjunto de linhas (cadeia de caracteres) será colocado no corpo da solicitação HTTP e enviado ao serviço que encapsula o modelo para pontuação.

> [!IMPORTANT]
> Damos suporte apenas à criação de perfil de até duas CPUs na região ChinaEast2 e USGovArizona.

Abaixo está um exemplo de como você pode construir um conjunto de dados de entrada para criar o perfil de um serviço que espera que os seus serviços de solicitação de entrada contenham o JSON serializado. Nesse caso, criamos um conjunto de dados com base em instâncias 100 do mesmo conteúdo de dado de solicitação. Em cenários do mundo real, sugerimos que você use conjuntos de dados maiores contendo várias entradas, especialmente se o uso/comportamento do recurso de modelo for dependente de entrada.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Depois que você tiver o conjunto de dados que contém o exemplo de data de solicitação pronto, crie uma configuração de inferência. A configuração de inferência é baseada no score.py e na definição de ambiente. O exemplo a seguir demonstra como criar a configuração de inferência e executar a criação de perfil:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


O comando a seguir demonstra como criar um perfil de um modelo usando a CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Para manter as informações retornadas pela criação de perfil, use marcas ou propriedades para o modelo. O uso de marcas ou Propriedades armazena os dados com o modelo no registro de modelo. Os exemplos a seguir demonstram como adicionar uma nova marca contendo as `requestedCpu` `requestedMemoryInGb` informações e:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
* [Criar alertas de eventos e gatilhos para implantações de modelo](how-to-use-event-grid.md)

