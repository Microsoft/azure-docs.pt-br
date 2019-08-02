---
title: 'Início Rápido: Detectar e enquadrar faces em uma imagem com o SDK de Python'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você criará um script do Python que usa a API de Detecção Facial para detectar e enquadrar rostos em uma imagem remota.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/15/2019
ms.author: sbowles
ms.openlocfilehash: 2f2245b4f6e4b38e0b071678ac0f3bddeb72f7ec
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277531"
---
# <a name="quickstart-create-a-python-script-to-detect-and-frame-faces-in-an-image"></a>Início Rápido: Criar um aplicativo em Python para detectar e enquadrar faces em uma imagem

Neste início rápido, você criará um script do Python que usa a API de Detecção Facial do Azure, por meio do SDK do Python, para detectar rostos humanos em uma imagem remota. O aplicativo exibe uma imagem selecionada e desenha um quadro ao redor de cada face detectada.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

- Uma chave de assinatura da API de Detecção Facial. É possível obter uma chave de assinatura de avaliação gratuita em [Experimente os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Ou siga as instruções em [Criar uma conta dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para assinar o serviço API de Detecção Facial e obter sua chave.
- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="get-the-face-sdk"></a>Obter o SDK de Detecção Facial

Instale o SDK do Python de Detecção Facial abrindo o prompt de comando e executando o seguinte comando:

```shell
pip install cognitive_face
```

## <a name="detect-faces-in-an-image"></a>Detectar faces em uma imagem

Crie um novo script do Python chamado _FaceQuickstart.py_ e adicione o código a seguir. Esse código manipula a funcionalidade principal da detecção facial. Será necessário substituir `<Subscription Key>` pelo valor da sua chave. Talvez também seja necessário alterar o valor de `BASE_URL` para usar o identificador de região correto para sua chave (confira os [documentos da API de Detecção Facial](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obter uma lista com todos os pontos de extremidade de região). As chaves de assinatura de avaliação gratuita são geradas na região **westus**. Opcionalmente, defina `img_url` como a URL de qualquer imagem que você queira usar.

O script detectará faces chamando o método **cognitive_face.face.detect**, que encapsula a API REST [Detectar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e retorna uma lista de faces.

```python
import cognitive_face as CF

# Replace with a valid subscription key (keeping the quotes in place).
KEY = '<Subscription Key>'
CF.Key.set(KEY)

# Replace with your regional Base URL
BASE_URL = 'https://westus.api.cognitive.microsoft.com/face/v1.0/'
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)
```

### <a name="try-the-app"></a>Testar o aplicativo

Execute o aplicativo com o comando `python FaceQuickstart.py`. Você deve obter uma resposta de texto na janela do console semelhante à seguinte:

```console
[{'faceId': '26d8face-9714-4f3e-bfa1-f19a7a7aa240', 'faceRectangle': {'top': 124, 'left': 459, 'width': 227, 'height': 227}}]
```

A saída representa uma lista de rostos detectados. Cada item na lista é uma instância **dict** em que `faceId` é uma ID exclusiva para a face detectada e `faceRectangle` descreve a posição da face detectada. 

> [!NOTE]
> IDs de Detecção Facial expiram após 24 horas. Você precisará armazenar explicitamente os dados faciais se quiser mantê-los por muito tempo.

## <a name="draw-face-rectangles"></a>Desenhar retângulos faciais

Usando as coordenadas que você recebeu do comando anterior, desenhe retângulos na imagem para representar visualmente cada face. Você precisará instalar o Pillow (`pip install pillow`) para usar o Módulo de Imagem do Pillow. Na início de *FaceQuickstart.py*, adicione o seguinte código:

```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
```

Em seguida, na parte inferior do script, adicione o seguinte código. Esse código cria uma função simples para analisar as coordenadas do retângulo e usa o Pillow para desenhar retângulos na imagem original. Depois, exibe a imagem em seu visualizador de imagem padrão.

```python
# Convert width height to a point in a rectangle


def getRectangle(faceDictionary):
    rect = faceDictionary['faceRectangle']
    left = rect['left']
    top = rect['top']
    bottom = left + rect['height']
    right = top + rect['width']
    return ((left, top), (bottom, right))


# Download the image from the url
response = requests.get(img_url)
img = Image.open(BytesIO(response.content))

# For each face returned use the face rectangle and draw a red box.
draw = ImageDraw.Draw(img)
for face in faces:
    draw.rectangle(getRectangle(face), outline='red')

# Display the image in the users default image browser.
img.show()
```

## <a name="run-the-app"></a>Execute o aplicativo

Talvez você receba uma solicitação para selecionar um visualizador de imagem padrão primeiro. Depois, você verá algo semelhante ao seguinte. Você também deverá ver os dados do retângulo impressos na janela do console.

![Uma moça com um retângulo vermelho desenhado ao redor da face](../images/face-rectangle-result.png)

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu o processo básico para usar o SDK de Python da API de Detecção Facial e criou um script para detectar e enquadrar faces em uma imagem. Agora, explore o uso do SDK de Python em um exemplo mais complexo. Acesse o exemplo Cognitive Face Python no GitHub, clone-o na pasta de seu projeto e siga as instruções no arquivo _README.md_.

> [!div class="nextstepaction"]
> [Exemplo Cognitive Face Python](https://github.com/Microsoft/Cognitive-Face-Python)
