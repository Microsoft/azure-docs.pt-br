---
title: 'Início Rápido: Gere uma miniatura – REST, Python'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você gerará uma miniatura de uma imagem usando a API da Pesquisa Visual Computacional com Python.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5203467eadadf1180eabf7b5a51b113ae273ee7c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603546"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-python"></a>Início Rápido: Gerar uma miniatura usando a API REST da Pesquisa Visual Computacional e o Python

Neste Início Rápido, você gerará uma miniatura de uma imagem usando a API REST da Pesquisa Visual Computacional. Com o método [Obter Miniatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb), você pode especificar a altura e a largura desejadas e a Pesquisa Visual Computacional usa o corte inteligente para identificar a área de interesse com inteligência e gerar as coordenadas de corte com base nessa região.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/try/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Você precisa ter uma chave de assinatura para a Pesquisa Visual Computacional. É possível obter uma chave de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar a Pesquisa Visual Computacional e obter sua chave.
- Um editor de códigos como o [Visual Studio Code](https://code.visualstudio.com/download)

## <a name="create-and-run-the-sample"></a>Criar e executar o exemplo

Para criar e executar a amostra, copie o código a seguir no editor de códigos. 

```python
import requests
# If you are using a Jupyter notebook, uncomment the following line.
# %matplotlib inline
import matplotlib.pyplot as plt
from PIL import Image
from io import BytesIO

# Replace <Subscription Key> with your valid subscription key.
subscription_key = "<Subscription Key>"
assert subscription_key

# You must use the same region in your REST call as you used to get your
# subscription keys. For example, if you got your subscription keys from
# westus, replace "westcentralus" in the URI below with "westus".
#
# Free trial subscription keys are generated in the "westcentralus" region.
# If you use a free trial subscription key, you shouldn't need to change
# this region.
vision_base_url = "https://westcentralus.api.cognitive.microsoft.com/vision/v2.0/"

thumbnail_url = vision_base_url + "generateThumbnail"

# Set image_url to the URL of an image that you want to analyze.
image_url = "https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg"

headers = {'Ocp-Apim-Subscription-Key': subscription_key}
params = {'width': '50', 'height': '50', 'smartCropping': 'true'}
data = {'url': image_url}
response = requests.post(thumbnail_url, headers=headers,
                         params=params, json=data)
response.raise_for_status()

thumbnail = Image.open(BytesIO(response.content))

# Display the thumbnail.
plt.imshow(thumbnail)
plt.axis("off")

# Verify the thumbnail size.
print("Thumbnail is {0}-by-{1}".format(*thumbnail.size))
```

Em seguida, faça o seguinte:
1. Substitua o valor de `subscription_key` pela sua chave de assinatura.
1. Substitua o valor de `vision_base_url` pela URL do ponto de extremidade para o recurso de Pesquisa Visual Computacional da região do Azure em que você adquiriu suas chaves de assinatura, se necessário.
1. Outra opção é substituir o valor de `image_url` pela URL de uma imagem diferente para a qual você deseja gerar uma miniatura.
1. Salve o código como um arquivo com uma extensão `.py`. Por exemplo, `get-thumbnail.py`.
1. Abra una janela de prompt de comando.
1. No prompt, use o comando `python` para executar o exemplo. Por exemplo, `python get-thumbnail.py`.

## <a name="examine-the-response"></a>Examinar a resposta

Uma resposta bem-sucedida é retornada como dados binários, que representam os dados de imagem da miniatura. A amostra deverá exibir esta imagem. Se a solicitação falhar, a resposta será exibida na janela do prompt de comando e deverá conter um código de erro.

## <a name="run-in-jupyter-optional"></a>Execução no Jupyter (opcional)

Você pode executar este Início Rápido como um passo a passo usando um Jupyter Notebook no [MyBinder](https://mybinder.org). Para inicializar o Associador, selecione o botão a seguir:

[![Associador](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=VisionAPI.ipynb)

## <a name="next-steps"></a>Próximas etapas

Em seguida, obtenha informações mais detalhadas sobre o recurso de geração de miniatura.

> [!div class="nextstepaction"]
> [Gerando miniaturas](../concept-generating-thumbnails.md)
