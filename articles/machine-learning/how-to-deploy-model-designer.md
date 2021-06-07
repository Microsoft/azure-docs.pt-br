---
title: Use o Studio para implantar modelos treinados no designer
titleSuffix: Azure Machine Learning
description: Use o Azure Machine Learning Studio para implantar modelos de aprendizado de máquina sem escrever uma única linha de código.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio, designer
ms.openlocfilehash: 35acfc51ae76fdacef11f03b1fbd91ad58650ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97722616"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Use o Studio para implantar modelos treinados no designer

Neste artigo, você aprenderá a implantar um modelo de designer como um ponto de extremidade em tempo real no Azure Machine Learning Studio.

Depois de registrado ou baixado, você pode usar modelos treinados de designer, assim como qualquer outro modelo. Modelos exportados podem ser implantados em casos de uso, como Internet das coisas (IoT) e implantações locais.

A implantação no estúdio consiste nas seguintes etapas:

1. Registre o modelo treinado.
1. Baixe o script de entrada e o arquivo de dependências Conda para o modelo.
1. Adicional Configure o script de entrada.
1. Implante o modelo em um destino de computação.

Você também pode implantar modelos diretamente no designer para ignorar as etapas de registro de modelo e download de arquivo. Isso pode ser útil para a implantação rápida. Para obter mais informações, consulte [implantar um modelo com o designer](tutorial-designer-automobile-price-deploy.md).

Os modelos treinados no designer também podem ser implantados por meio do SDK ou da CLI (interface de linha de comando). Para obter mais informações, consulte [implantar seu modelo existente com Azure Machine Learning](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Um espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md)

* Um pipeline de treinamento concluído que contém um dos seguintes módulos:
    - [Módulo Treinar Modelo](./algorithm-module-reference/train-model.md)
    - [Módulo treinar modelo de detecção de anomalias](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Módulo treinar modelo de clustering](./algorithm-module-reference/train-clustering-model.md)
    - [Módulo de modelo Train Pytorch](./algorithm-module-reference/train-pytorch-model.md)
    - [Treinar módulo Recomendador SVD](./algorithm-module-reference/train-svd-recommender.md)
    - [Treinar módulo de modelo do Vowpal Wabbit](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Módulo de modelo profundo de treinamento amplo &](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Registre o modelo

Após a conclusão do pipeline de treinamento, registre o modelo treinado em seu espaço de trabalho Azure Machine Learning para acessar o modelo em outros projetos.

1. Selecione o [módulo modelo de treinamento](./algorithm-module-reference/train-model.md).
1. Selecione a guia **saídas + logs** no painel direito.
1. Selecione o ícone **registrar modelo** ![ captura de tela do ícone de engrenagem ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Captura de tela do painel direito do módulo modelo de treinamento](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Insira um nome para seu modelo e, em seguida, selecione **salvar**.

Depois de registrar seu modelo, você pode encontrá-lo na página de ativos de **modelos** no estúdio.
    
![Captura de tela do modelo registrado na página de ativos de modelos](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Baixar o arquivo de script de entrada e o arquivo de dependências Conda

Você precisa dos seguintes arquivos para implantar um modelo no Azure Machine Learning Studio:

- **Arquivo de script de entrada** – carrega o modelo treinado, processa dados de entrada de solicitações, realiza inferências em tempo real e retorna o resultado. O designer gera automaticamente um `score.py` arquivo de script de entrada quando o módulo **modelo de treinamento** é concluído.

- **Arquivo de dependências Conda** -especifica em quais pacotes de Pip e Conda o WebService depende. O designer cria automaticamente um `conda_env.yaml` arquivo quando o módulo **modelo de treinamento** é concluído.

Você pode baixar esses dois arquivos no painel direito do módulo **modelo de treinamento** :

1. Selecione o módulo **Treinar Modelo**.
1. Na guia **saídas + logs** , selecione a pasta `trained_model_outputs` .
1. Baixe o `conda_env.yaml` arquivo e o `score.py` arquivo.

    ![Captura de tela de baixar arquivos para implantação no painel direito](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

Como alternativa, você pode baixar os arquivos da página de ativos de **modelos** depois de registrar seu modelo:

1. Navegue até a página de ativos de **modelos** .
1. Selecione o modelo que você deseja implantar.
1. Selecione a guia **artefatos** .
1. Selecione a pasta `trained_model_outputs` .
1. Baixe o `conda_env.yaml` arquivo e o `score.py` arquivo.  

    ![Captura de tela de baixar arquivos para implantação na página de detalhes do modelo](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> O `score.py` arquivo fornece quase a mesma funcionalidade que os módulos do **modelo de Pontuação** . No entanto, alguns módulos como [Score SVD recomendador](./algorithm-module-reference/score-svd-recommender.md), [Pontuação de largura e de fundo profundo](./algorithm-module-reference/score-wide-and-deep-recommender.md)e [Pontuação Vowpal modelo de Wabbit](./algorithm-module-reference/score-vowpal-wabbit-model.md) têm parâmetros para diferentes modos de pontuação. Você também pode alterar esses parâmetros no script de entrada.
>
>Para obter mais informações sobre como definir parâmetros no `score.py` arquivo, consulte a seção [Configurar o script de entrada](#configure-the-entry-script).

## <a name="deploy-the-model"></a>Implantar o modelo

Depois de baixar os arquivos necessários, você estará pronto para implantar o modelo.

1. Na página de ativos **modelos** , selecione o modelo registrado.
1. Selecione o botão **Implantar**.
1. No menu configuração, insira as seguintes informações:

    - Insira um nome para o ponto de extremidade.
    - Selecione para implantar o modelo no [serviço kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md) ou [instância de contêiner do Azure](how-to-deploy-azure-container-instance.md).
    - Carregue o `score.py` para o **arquivo de script de entrada**.
    - Carregue o `conda_env.yml` para o **arquivo de dependências Conda**. 

    >[!TIP]
    > Na configuração **avançada** , você pode definir a capacidade de CPU/memória e outros parâmetros para implantação. Essas configurações são importantes para determinados modelos, como modelos PyTorch, que consomem uma quantidade considerável de memery (cerca de 4 GB).

1. Selecione **implantar** para implantar seu modelo como um ponto de extremidade em tempo real.

    ![Captura de tela de implantar modelo na página de ativos do modelo](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Consumir o ponto de extremidade em tempo real

Após a implantação ser realizada com sucesso, você pode encontrar o ponto de extremidade em tempo real na página de ativos de **pontos de extremidade** . Uma vez lá, você encontrará um ponto de extremidade REST, que os clientes podem usar para enviar solicitações para o ponto de extremidade em tempo real. 

> [!NOTE]
> O designer também gera um arquivo JSON de dados de exemplo para teste, você pode baixar `_samples.json` na pasta **trained_model_outputs** .

Use o exemplo de código a seguir para consumir um ponto de extremidade em tempo real.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Consumir pontos de extremidade em tempo real relacionados à pesquisa Visual computacional

Ao consumir pontos de extremidade em tempo real relacionados à pesquisa Visual computacional, você precisa converter imagens em bytes, já que o serviço Web aceita apenas a cadeia de caracteres como entrada. Veja a seguir o código de exemplo:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Configurar o script de entrada

Alguns módulos no designer como [Score SVD recomendador](./algorithm-module-reference/score-svd-recommender.md), [Pontuação de largura e de fundo profundo](./algorithm-module-reference/score-wide-and-deep-recommender.md)e [Pontuação Vowpal modelo de Wabbit](./algorithm-module-reference/score-vowpal-wabbit-model.md) têm parâmetros para diferentes modos de pontuação. 

Nesta seção, você aprenderá a atualizar esses parâmetros no arquivo de script de entrada também.

O exemplo a seguir atualiza o comportamento padrão para um modelo de **recomendador profundo de & de nível** treinado. Por padrão, o `score.py` arquivo informa ao serviço Web para prever as classificações entre usuários e itens. 

Você pode modificar o arquivo de script de entrada para fazer recomendações de item e retornar itens recomendados alterando o `recommender_prediction_kind` parâmetro.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

Para **grandes &** modelos de recomendação e **Vowpal Wabbit** , você pode configurar o parâmetro de modo de Pontuação usando os seguintes métodos:

- Os nomes de parâmetro são as combinações de letras minúsculas e sublinhados de nomes de parâmetro para [pontuar o modelo Vowpal Wabbit](./algorithm-module-reference/score-vowpal-wabbit-model.md) e a [Pontuação de largura e de fundo profundo](./algorithm-module-reference/score-wide-and-deep-recommender.md);
- Os valores de parâmetro de tipo de modo são cadeias de caracteres dos nomes de opção correspondentes. Use o **tipo de previsão de recomendação** nos códigos acima como exemplo, o valor pode ser `'Rating Prediction'` ou `'Item Recommendation'` . Outros valores não são permitidos.

Para o modelo treinado de **recomendador SVD** , os nomes de parâmetros e valores talvez menos óbvios e você pode pesquisar as tabelas abaixo para decidir como definir parâmetros.

| Nome do parâmetro em [Pontuação SVD recomendador](./algorithm-module-reference/score-svd-recommender.md)                           | Nome do parâmetro no arquivo de script de entrada |
| ------------------------------------------------------------ | --------------------------------------- |
| Tipo de previsão do recomendador                                  | prediction_kind                         |
| Seleção de itens recomendados                                   | recommended_item_selection              |
| Tamanho mínimo do pool de recomendação para um único usuário    | min_recommendation_pool_size            |
| Número máximo de itens a serem recomendados a um usuário               | max_recommended_item_count              |
| Se as classificações previstas dos itens devem ser retornadas juntamente com os rótulos | return_ratings                          |

O código a seguir mostra como definir parâmetros para um recomendador SVD, que usa todos os seis parâmetros para recomendar itens classificados com classificações previstas anexadas.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Próximas etapas

* [Treinar um modelo no designer](tutorial-designer-automobile-price-train-score.md)
* [Implantar modelos com Azure Machine Learning SDK](how-to-deploy-and-where.md)
* [Solucionar problemas de uma implantação com falha](how-to-troubleshoot-deployment.md)
* [Implantar no Serviço de Kubernetes do Azure](how-to-deploy-azure-kubernetes-service.md)
* [Criar aplicativos cliente para consumir serviços Web](how-to-consume-web-service.md)
* [Atualizar serviço Web](how-to-deploy-update-web-service.md)
