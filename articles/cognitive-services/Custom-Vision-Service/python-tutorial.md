---
title: 'Início Rápido: Criar um projeto de classificação de imagem com o SDK de Visão Personalizada para Python'
titleSuffix: Azure Cognitive Services
description: Crie um projeto, adicione marcações, faça upload de imagens, treine seu projeto e faça uma previsão usando o SDK do Python.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: areddish
ms.openlocfilehash: 25ee212bbcabb1697de1611cc67c5bfdf615c617
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560994"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-python-sdk"></a>Início Rápido: Criar um projeto de classificação de imagem com o SDK do Python de Visão Personalizada

Este artigo fornece informações e código de exemplo para ajudar você a começar a usar o SDK da Visão Personalizada com Python para criar um modelo de classificação de imagem. Depois de criá-lo, você pode adicionar marcas, carregar imagens, treinar o projeto, obter a URL do ponto de extremidade de previsão do projeto publicado e usar o ponto de extremidade para testar uma imagem de forma programática. Use este exemplo como um modelo para criar seu próprio aplicativo Python. Se você quiser passar pelo processo de criar e usar um modelo de classificação _sem_ código, confira as [diretrizes baseadas em navegador](getting-started-build-a-classifier.md).

## <a name="prerequisites"></a>Pré-requisitos

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Ferramenta [pip](https://pip.pypa.io/en/stable/installing/)

## <a name="install-the-custom-vision-sdk"></a>Instalar o SDK da Visão Personalizada

Para instalar o SDK do Serviço de Visão Personalizada para Python, execute o seguinte comando no PowerShell:

```powershell
pip install azure-cognitiveservices-vision-customvision
```

[!INCLUDE [get-keys](includes/get-keys.md)]

[!INCLUDE [python-get-images](includes/python-get-images.md)]


## <a name="add-the-code"></a>Adicionar o código

Crie um novo arquivo chamado *sample.py* no diretório de seu projeto de preferência.

### <a name="create-the-custom-vision-service-project"></a>Criar o projeto do Serviço de Visão Personalizada

Adicione o código a seguir ao seu script para criar um novo projeto do Serviço de Visão Personalizada. Insira as chaves de assinatura nas definições pertinentes.

```Python
from azure.cognitiveservices.vision.customvision.training import CustomVisionTrainingClient
from azure.cognitiveservices.vision.customvision.training.models import ImageFileCreateEntry

ENDPOINT = "https://southcentralus.api.cognitive.microsoft.com"

# Replace with a valid key
training_key = "<your training key>"
prediction_key = "<your prediction key>"
prediction_resource_id = "<your prediction resource id>"

publish_iteration_name = "classifyModel"

trainer = CustomVisionTrainingClient(training_key, endpoint=ENDPOINT)

# Create a new project
print ("Creating project...")
project = trainer.create_project("My New Project")
```

### <a name="create-tags-in-the-project"></a>Criar marcas no projeto

Para criar marcas de classificação para o projeto, adicione o seguinte código ao final de *sample.py*:

```Python
# Make two tags in the new project
hemlock_tag = trainer.create_tag(project.id, "Hemlock")
cherry_tag = trainer.create_tag(project.id, "Japanese Cherry")
```

### <a name="upload-and-tag-images"></a>Carregar e marcar imagens

Para adicionar imagens de exemplo ao projeto, insira o código a seguir após a criação da marca. Esse código carrega cada imagem com sua marca correspondente. Você precisará inserir o caminho da URL de imagem base de acordo com o local em que baixou o projeto Amostras de SDK de Python dos Serviços Cognitivos.

> [!NOTE]
> Você precisará alterar o caminho para as imagens com base em onde você baixou o projeto Amostras de SDK de Python dos Serviços Cognitivos anteriormente.

```Python
base_image_url = "<path to project>"

print("Adding images...")

image_list = []

for image_num in range(1, 11):
    file_name = "hemlock_{}.jpg".format(image_num)
    with open(base_image_url + "images/Hemlock/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[hemlock_tag.id]))

for image_num in range(1, 11):
    file_name = "japanese_cherry_{}.jpg".format(image_num)
    with open(base_image_url + "images/Japanese Cherry/" + file_name, "rb") as image_contents:
        image_list.append(ImageFileCreateEntry(name=file_name, contents=image_contents.read(), tag_ids=[cherry_tag.id]))

upload_result = trainer.create_images_from_files(project.id, images=image_list)
if not upload_result.is_batch_successful:
    print("Image batch upload failed.")
    for image in upload_result.images:
        print("Image status: ", image.status)
    exit(-1)
```

### <a name="train-the-classifier-and-publish"></a>Treinar o classificador e publicar

Este código cria a primeira iteração no projeto e, em seguida, a publica no ponto de extremidade de previsão. O nome dado à iteração publicada pode ser usado para enviar solicitações de previsão. Uma iteração não fica disponível no ponto de extremidade de previsão até ser publicada.

```Python
import time

print ("Training...")
iteration = trainer.train_project(project.id)
while (iteration.status != "Completed"):
    iteration = trainer.get_iteration(project.id, iteration.id)
    print ("Training status: " + iteration.status)
    time.sleep(1)

# The iteration is now trained. Publish it to the project endpoint
trainer.publish_iteration(project.id, iteration.id, publish_iteration_name, prediction_resource_id)
print ("Done!")
```

### <a name="get-and-use-the-published-iteration-on-the-prediction-endpoint"></a>Obter e usar a iteração publicada no ponto de extremidade de previsão

Para enviar uma imagem para o ponto de extremidade de previsão e recuperar a previsão, adicione o seguinte código ao final do arquivo:

```python
from azure.cognitiveservices.vision.customvision.prediction import CustomVisionPredictionClient

# Now there is a trained endpoint that can be used to make a prediction
predictor = CustomVisionPredictionClient(prediction_key, endpoint=ENDPOINT)

with open(base_image_url + "images/Test/test_image.jpg", "rb") as image_contents:
    results = predictor.classify_image(
        project.id, publish_iteration_name, image_contents.read())

    # Display the results.
    for prediction in results.predictions:
        print("\t" + prediction.tag_name +
              ": {0:.2f}%".format(prediction.probability * 100))
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute *sample.py*.

```powershell
python sample.py
```

A saída do aplicativo deve ser semelhante ao seguinte texto:

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

Em seguida, você pode verificar se a imagem de teste (encontrada em **<url_imagem_base>/Images/Test/** ) foi marcada apropriadamente. Você pode também voltar para o [site da Visão Personalizada](https://customvision.ai) e ver o estado atual do projeto recém-criado.

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>Próximas etapas

Agora você viu como cada etapa do processo de classificação de imagem pode ser executada em código. Este exemplo executa uma iteração de treinamento única, mas muitas vezes você precisará treinar e testar o modelo várias vezes para torná-lo mais preciso.

> [!div class="nextstepaction"]
> [Testar e readaptar um modelo](test-your-model.md)
