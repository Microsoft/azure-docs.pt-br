---
title: 'Início Rápido: SDK do Python'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a usar o SDK do Python para tarefas comuns, como analisar imagem, obter descrição, reconhecer texto e gerar miniatura.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: pafarley
ms.openlocfilehash: c03568ece97bdaad86f4564debf9f3b2fa14c6ed
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786638"
---
# <a name="azure-cognitive-services-computer-vision-sdk-for-python"></a>SDK da Pesquisa Visual Computacional dos Serviços Cognitivos do Azure para Python

O serviço de Pesquisa Visual Computacional fornece aos desenvolvedores o acesso a algoritmos avançados para processar imagens e retornar informações. Os algoritmos da Pesquisa Visual Computacional analisam o conteúdo de uma imagem de diferentes maneiras, dependendo das características visuais que interessam a você.

* [Analisar uma imagem](#analyze-an-image)
* [Obter a lista de domínios de assunto](#get-subject-domain-list)
* [Analisar uma imagem por domínio](#analyze-an-image-by-domain)
* [Obter uma descrição de texto de uma imagem](#get-text-description-of-an-image)
* [Obter o texto manuscrito de uma imagem](#get-text-from-image)
* [Gerar uma miniatura](#generate-thumbnail)

Para obter mais informações sobre esse serviço, confira [O que é a Pesquisa Visual Computacional?][computervision_docs].

Procurando mais documentação?

* [Documentação de referência do SDK](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision)
* [Documentação da Pesquisa Visual Computacional dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/computer-vision/)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.6 e superior][python]
* Libere o objeto cliente and associated endpoint. You need these values when you create the instance of the [ComputerVisionClient][ref_computervisionclient] da [chave de Pesquisa Visual Computacional][computervision_resource]. Use um dos métodos a seguir para obter esses valores.

### <a name="if-you-dont-have-an-azure-subscription"></a>Caso você não tenha uma assinatura do Azure

Crie uma chave gratuita válida por 7 dias com a experiência **[Experimentar][computervision_resource]** para o serviço de Pesquisa Visual Computacional. Quando a chave for criada, copie a chave e o nome do ponto de extremidade. Você precisará disso para [criar o cliente](#create-client).

Mantenha o seguinte depois que a chave for criada:

* Valor da chave: uma cadeia de caracteres de 32 caracteres com o formato `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`
* Ponto de extremidade de chave: a URL de ponto de extremidade base, https\://westcentralus.api.cognitive.microsoft.com

### <a name="if-you-have-an-azure-subscription"></a>Caso você tenha uma assinatura do Azure

O método mais fácil para criar um recurso em sua assinatura será usar o comando da [CLI do Azure][azure_cli] a seguir. Isso cria uma chave de serviço cognitivo que pode ser usada em muitos serviços cognitivos. Você precisa escolher o nome do grupo de recursos _existente_, por exemplo, "my-cogserv-group", e o nome do recurso da pesquisa visual computacional, como "my-computer-vision-resource".

```Bash
RES_REGION=westeurope
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

az cognitiveservices account create \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --location $RES_REGION \
    --kind CognitiveServices \
    --sku S0 \
    --yes
```

<!--
## Installation

Install the Azure Cognitive Services Computer Vision SDK with [pip][pip], optionally within a [virtual environment][venv].

### Configure a virtual environment (optional)

Although not required, you can keep your base system and Azure SDK environments isolated from one another if you use a [virtual environment][virtualenv]. Execute the following commands to configure and then enter a virtual environment with [venv][venv], such as `cogsrv-vision-env`:

```Bash
python3 -m venv cogsrv-vision-env
source cogsrv-vision-env/bin/activate
```
-->

### <a name="install-the-sdk"></a>Instalar o SDK

Instale o [pacote do][pypi_computervision] SDK da Pesquisa Visual Computacional dos Serviços Cognitivos do Azure para Python with [pip][pip]:

```Bash
pip install azure-cognitiveservices-vision-computervision
```

## <a name="authentication"></a>Authentication

Depois de criar o recurso da Pesquisa Visual Computacional, você precisará do **ponto de extremidade** e de uma das **chaves de conta** para criar uma instância do objeto de cliente.

Use esses valores ao criar a instância do objeto de cliente [ComputerVisionClient][ref_computervisionclient].

Por exemplo, use o terminal de Bash para definir as variáveis de ambiente:

```Bash
ACCOUNT_ENDPOINT=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>
```

### <a name="for-azure-subscription-users-get-credentials-for-key-and-endpoint"></a>Para usuários de assinatura do Azure, obtenha as credenciais para chave e ponto de extremidade

Se você não se lembrar do seu ponto de extremidade e da chave, poderá usar o seguinte método para encontrá-los. Se você precisar criar uma chave e um ponto de extremidade, poderá usar o método de [proprietários de assinatura do Azure](#if-you-have-an-azure-subscription) ou para [usuários sem uma assinatura do Azure](#if-you-dont-have-an-azure-subscription).

Use o snippet da [CLI do Azure][cloud_shell] abaixo para preencher duas variáveis de ambiente com o **ponto de extremidade** da conta de Pesquisa Visual Computacional e uma de suas **chaves** (também é possível localizar esses valores no [portal do Azure][azure_portal]). O trecho é formatado para o shell do Bash.

```Bash
RES_GROUP=<resourcegroup-name>
ACCT_NAME=<computervision-account-name>

export ACCOUNT_ENDPOINT=$(az cognitiveservices account show \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query endpoint \
    --output tsv)

export ACCOUNT_KEY=$(az cognitiveservices account keys list \
    --resource-group $RES_GROUP \
    --name $ACCT_NAME \
    --query key1 \
    --output tsv)
```


### <a name="create-client"></a>Criar cliente

Obtenha o ponto de extremidade e as chaves das variáveis de ambiente então crie o objeto de cliente [ComputerVisionClient][ref_computervisionclient].

```Python
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
from msrest.authentication import CognitiveServicesCredentials

# Get endpoint and key from environment variables
import os
endpoint = os.environ['ACCOUNT_ENDPOINT']
key = os.environ['ACCOUNT_KEY']

# Set credentials
credentials = CognitiveServicesCredentials(key)

# Create client
client = ComputerVisionClient(endpoint, credentials)
```

## <a name="examples"></a>Exemplos

É necessário um objeto de cliente [ComputerVisionClient][ref_computervisionclient] antes de usar uma das tarefas a seguir.

### <a name="analyze-an-image"></a>Analisar uma imagem

Você pode analisar uma imagem quanto a determinados recursos com a propriedade [`analyze_image`][ref_computervisionclient_analyze_image]. Use the [`visual_features`][ref_computervision_model_visualfeatures] para definir os tipos de análises a serem feitas na imagem. Os valores comuns são `VisualFeatureTypes.tags` e `VisualFeatureTypes.description`.

```Python
url = "https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Broadway_and_Times_Square_by_night.jpg/450px-Broadway_and_Times_Square_by_night.jpg"

image_analysis = client.analyze_image(url,visual_features=[VisualFeatureTypes.tags])

for tag in image_analysis.tags:
    print(tag)
```

### <a name="get-subject-domain-list"></a>Obter a lista de domínios de assunto

Examine os domínios de assunto usados para analisar a imagem com [`list_models`][ref_computervisionclient_list_models]. Esses nomes de domínio são usados durante a [análise de uma imagem por domínio](#analyze-an-image-by-domain). Um exemplo de um domínio é `landmarks`.

```Python
models = client.list_models()

for x in models.models_property:
    print(x)
```

### <a name="analyze-an-image-by-domain"></a>Analisar uma imagem por domínio

Analise uma imagem por domínio de assunto com [`analyze_image_by_domain`][ref_computervisionclient_analyze_image_by_domain]. Obtenha a [lista de domínios de assunto com suporte](#get-subject-domain-list) para usar o nome de domínio correto.

```Python
# type of prediction
domain = "landmarks"

# Public domain image of Eiffel tower
url = "https://images.pexels.com/photos/338515/pexels-photo-338515.jpeg"

# English language response
language = "en"

analysis = client.analyze_image_by_domain(domain, url, language)

for landmark in analysis.result["landmarks"]:
    print(landmark["name"])
    print(landmark["confidence"])
```

### <a name="get-text-description-of-an-image"></a>Obter uma descrição de texto de uma imagem

Obtenha uma descrição de texto baseada na linguagem de uma imagem com [`describe_image`][ref_computervisionclient_describe_image]. Solicite várias descrições com a propriedade `max_description` se estiver fazendo uma análise de texto para detectar palavras-chave associadas à imagem. Entre os exemplos de uma descrição de texto para a imagem a seguir estão `a train crossing a bridge over a body of water`, `a large bridge over a body of water` e `a train crossing a bridge over a large body of water`.

```Python
domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

analysis = client.describe_image(url, max_descriptions, language)

for caption in analysis.captions:
    print(caption.text)
    print(caption.confidence)
```

### <a name="get-text-from-image"></a>Obter o texto de uma imagem

Obtenha qualquer texto manuscrito ou impresso de uma imagem. Isso exige duas chamadas ao SDK: [`batch_read_file`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python) e [`get_read_operation_result`](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python). A chamada a `batch_read_file` é assíncrona. Nos resultados da chamada `get_read_operation_result`, você precisa verificar se a primeira chamada foi concluída com [`TextOperationStatusCodes`][ref_computervision_model_textoperationstatuscodes] antes de extrair os dados de texto. Os resultados incluem o texto, bem como as coordenadas da caixa delimitadora para o texto.

```Python
# import models
from azure.cognitiveservices.vision.computervision.models import TextOperationStatusCodes
import time

url = "https://azurecomcdn.azureedge.net/cvt-1979217d3d0d31c5c87cbd991bccfee2d184b55eeb4081200012bdaf6a65601a/images/shared/cognitive-services-demos/read-text/read-1-thumbnail.png"
raw = True
custom_headers = None
numberOfCharsInOperationId = 36

# Async SDK call
rawHttpResponse = client.batch_read_file(url, custom_headers,  raw)

# Get ID from returned headers
operationLocation = rawHttpResponse.headers["Operation-Location"]
idLocation = len(operationLocation) - numberOfCharsInOperationId
operationId = operationLocation[idLocation:]

# SDK call
while True:
    result = client.get_read_operation_result(operationId)
    if result.status not in ['NotStarted', 'Running']:
        break
    time.sleep(1)

# Get data
if result.status == TextOperationStatusCodes.succeeded:
    for textResult in result.recognition_results:
        for line in textResult.lines:
            print(line.text)
            print(line.bounding_box)
```

### <a name="generate-thumbnail"></a>Gerar uma miniatura

Gere uma miniatura (JPG) de uma imagem com [`generate_thumbnail`][ref_computervisionclient_generate_thumbnail]. A miniatura não precisa estar nas mesmas proporções da imagem original.

Instale o **Pillow** para usar este exemplo:

```bash
pip install Pillow
```

Depois de instalar o Pillow, use o pacote no exemplo de código a seguir para gerar a imagem em miniatura.

```Python
# Pillow package
from PIL import Image

# IO package to create local image
import io

width = 50
height = 50
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"

thumbnail = client.generate_thumbnail(width, height, url)

for x in thumbnail:
    image = Image.open(io.BytesIO(x))

image.save('thumbnail.jpg')
```

## <a name="troubleshooting"></a>solução de problemas

### <a name="general"></a>Geral

Quando você interage com o [ComputerVisionClient][ref_computervisionclient], a classe client object using the Python SDK, the [`ComputerVisionErrorException`][ref_computervision_computervisionerrorexception] é usada para retornar erros. Os erros retornados pelo serviço correspondem aos mesmos códigos de status HTTP retornados para as solicitações da API REST.

Por exemplo, se você tentar analisar uma imagem com uma chave inválida, um erro `401` será retornado. No snippet a seguir, o [erro][ref_httpfailure] é tratado normalmente capturando a exceção e exibindo informações adicionais sobre o erro.

```Python

domain = "landmarks"
url = "http://www.public-domain-photos.com/free-stock-photos-4/travel/san-francisco/golden-gate-bridge-in-san-francisco.jpg"
language = "en"
max_descriptions = 3

try:
    analysis = client.describe_image(url, max_descriptions, language)

    for caption in analysis.captions:
        print(caption.text)
        print(caption.confidence)
except HTTPFailure as e:
    if e.status_code == 401:
        print("Error unauthorized. Make sure your key and endpoint are correct.")
    else:
        raise
```

### <a name="handle-transient-errors-with-retries"></a>Tratar erros transitórios com novas tentativas

Ao trabalhar com o [ComputerVisionClient][ref_computervisionclient] client, you might encounter transient failures caused by [rate limits][computervision_request_units] imposto pelo serviço ou outros problemas temporários, como interrupções de rede. Para obter informações sobre como lidar com esses tipos de falhas, confira [Padrão de repetição][azure_pattern_retry] no guia Padrões de Design de Nuvem e o [padrão de Disjuntor][azure_pattern_circuit_breaker] relacionado.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Aplicando marcas de conteúdo a imagens](../concept-tagging-images.md)

<!-- LINKS -->
[pip]: https://pypi.org/project/pip/
[python]: https://www.python.org/downloads/

[azure_cli]: https://docs.microsoft.com/cli/azure/cognitiveservices/account?view=azure-cli-latest#az-cognitiveservices-account-create
[azure_pattern_circuit_breaker]: https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker
[azure_pattern_retry]: https://docs.microsoft.com/azure/architecture/patterns/retry
[azure_portal]: https://portal.azure.com
[azure_sub]: https://azure.microsoft.com/free/

[cloud_shell]: https://docs.microsoft.com/azure/cloud-shell/overview

[venv]: https://docs.python.org/3/library/venv.html
[virtualenv]: https://virtualenv.pypa.io

[source_code]: https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-vision-computervision

[pypi_computervision]:https://pypi.org/project/azure-cognitiveservices-vision-computervision/
[pypi_pillow]:https://pypi.org/project/Pillow/

[ref_computervision_sdk]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision?view=azure-python
[ref_computervision_computervisionerrorexception]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.computervisionerrorexception?view=azure-python
[ref_httpfailure]: https://docs.microsoft.com/python/api/msrest/msrest.exceptions.httpoperationerror?view=azure-python


[computervision_resource]: https://azure.microsoft.com/try/cognitive-services/?

[computervision_docs]: https://docs.microsoft.com/azure/cognitive-services/computer-vision/home

[ref_computervisionclient]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image]: https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_list_models]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_analyze_image_by_domain]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_describe_image]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_get_text_operation_result]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python

[ref_computervisionclient_generate_thumbnail]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-python


[ref_computervision_model_visualfeatures]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-python

[ref_computervision_model_textoperationstatuscodes]:https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-computervision/azure.cognitiveservices.vision.computervision.models.textoperationstatuscodes?view=azure-python

[computervision_request_units]:https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/
