---
title: Início rápido da biblioteca de cliente Python do Detector de Anomalias
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/25/2020
ms.author: mbullwin
ms.openlocfilehash: 216c45bf097718f6a696e64c8bd9c8718fc0185e
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445018"
---
Introdução à biblioteca de clientes do Detector de Anomalias para Python. Siga estas etapas para instalar o pacote e começar a usar os algoritmos fornecidos pelo serviço. O serviço Detector de Anomalias permite localizar anormalidades em seus dados de série temporal usando automaticamente os modelos de melhor ajuste, independentemente do setor, cenário ou volume de dados.

Use a biblioteca de cliente do Detector de Anomalias para Python para:

* Detectar anomalias em todo o seu conjunto de dados de série temporal, como uma solicitação em lote
* Detectar o status de anomalias do ponto de dados mais recente em sua série temporal
* Detectar pontos de alteração de tendência no conjunto de dados.

[Documentação de referência da biblioteca](https://go.microsoft.com/fwlink/?linkid=2090370) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Pacote (PyPi)](https://pypi.org/project/azure-ai-anomalydetector/) | [Localize o código de exemplo no GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/anomalydetector/azure-ai-anomalydetector/samples)

## <a name="prerequisites"></a>Pré-requisitos

* [Python 3.x](https://www.python.org/)
* A [biblioteca de análise de dados do Pandas](https://pandas.pydata.org/)
* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services)
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title="Crie um recurso de Detector de Anomalias"  target="_blank">Crie um recurso de detector de anomalias </a> no portal do Azure para obter a chave e o ponto de extremidade. Aguarde até que ele seja implantado e clique no botão **Ir para o recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso que você criar para conectar seu aplicativo à API do Detector de Anomalias. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.


## <a name="setting-up"></a>Configurando

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

### <a name="create-a-new-python-application"></a>Criar um novo aplicativo Python

 Crie um arquivo do Python e importe as bibliotecas a seguir.

```python
import os
from azure.ai.anomalydetector import AnomalyDetectorClient
from azure.ai.anomalydetector.models import DetectRequest, TimeSeriesPoint, TimeGranularity, \
    AnomalyDetectorError
from azure.core.credentials import AzureKeyCredential
import pandas as pd
```

Crie variáveis para sua chave como uma variável de ambiente, o caminho para um arquivo de dados de série temporal e a localização do Azure de sua assinatura. Por exemplo, `westus2`.

```python
SUBSCRIPTION_KEY = os.environ["ANOMALY_DETECTOR_KEY"]
ANOMALY_DETECTOR_ENDPOINT = os.environ["ANOMALY_DETECTOR_ENDPOINT"]
TIME_SERIES_DATA_PATH = os.path.join("./sample_data", "request-data.csv")
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Depois de instalar o Python, você pode instalar a biblioteca de clientes com:

```console
pip install --upgrade azure-ai-anomalydetector
```

## <a name="object-model"></a>Modelo de objeto

O cliente do Detector de Anomalias é um objeto [AnomalyDetectorClient](https://github.com/Azure/azure-sdk-for-python/blob/0b8622dc249969c2f01c5d7146bd0bb36bb106dd/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector/azure/cognitiveservices/anomalydetector/_anomaly_detector_client.py) que se autentica no Azure usando sua chave. O cliente pode fazer a detecção de anomalias em um conjunto de dados inteiro usando [detect_entire_series](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L26) ou no último ponto de dados usando [detect_last_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/operations/_anomaly_detector_client_operations.py#L87). A função [detect_change_point](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/aio/operations_async/_anomaly_detector_client_operations_async.py#L142) detecta pontos que marcam as alterações em uma tendência.

Os dados de série temporal são enviados como uma série de objetos [TimeSeriesPoints](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L370). O objeto `DetectRequest` contém propriedades para descrever os dados (`TimeGranularity`, por exemplo) e os parâmetros para a detecção de anomalias.

A resposta do Detector de Anomalias é um objeto [LastDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse), [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) ou [ChangePointDetectResponse](https://github.com/Azure/azure-sdk-for-python/blob/bf9d44f2a50aea46a59c4cb83ccfccaff5e2b218/sdk/anomalydetector/azure-ai-anomalydetector/azure/ai/anomalydetector/models/_models_py3.py#L107), dependendo do método usado.

## <a name="code-examples"></a>Exemplos de código

Esses snippets de códigos mostram como fazer o seguinte com a biblioteca de clientes do Detector de Anomalias para Python:

* [Autenticar o cliente](#authenticate-the-client)
* [Carregar um conjunto de dados de série temporal de um arquivo](#load-time-series-data-from-a-file)
* [Detectar anomalias no conjunto de dados inteiro](#detect-anomalies-in-the-entire-data-set)
* [Detectar o status de anomalias do último ponto de dados](#detect-the-anomaly-status-of-the-latest-data-point)
* [Detectar os pontos de alteração no conjunto de dados](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione sua variável de localização do Azure ao ponto de extremidade e autentique o cliente com a sua chave.

```python
client = AnomalyDetectorClient(AzureKeyCredential(SUBSCRIPTION_KEY), ANOMALY_DETECTOR_ENDPOINT)
```

## <a name="load-time-series-data-from-a-file"></a>Carregar dados de série temporal de um arquivo

Baixe os dados de exemplo deste guia de início rápido no [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. Em seu navegador, clique com o botão direito do mouse em **Raw**.
2. Clique em **Salvar link como**.
3. Salve o arquivo no diretório do aplicativo como um arquivo .csv.

Esses dados de série temporal são formatados como um arquivo .csv e enviados à API do Detector de Anomalias.

Carregue seu arquivo de dados com o método `read_csv()` da biblioteca do Pandas e crie uma variável de lista vazia para armazenar sua série de dados. Itere pelo arquivo e acrescente os dados como um objeto `TimeSeriesPoint`. Esse objeto conterá o carimbo de data/hora e o valor numérico das linhas de seu arquivo de dados .csv.

```python
series = []
data_file = pd.read_csv(TIME_SERIES_DATA_PATH, header=None, encoding='utf-8', parse_dates=[0])
for index, row in data_file.iterrows():
    series.append(TimeSeriesPoint(timestamp=row[0], value=row[1]))
```

Crie um objeto `DetectRequest` com sua série temporal e a `TimeGranularity` (ou periodicidade) de seus pontos de dados. Por exemplo, `TimeGranularity.daily`.

```python
request = DetectRequest(series=series, granularity=TimeGranularity.daily)
```

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detectar anomalias no conjunto de dados inteiro

Chame a API para detectar anomalias em todos os dados da série temporal usando o método `detect_entire_series` do cliente. Armazene o objeto [EntireDetectResponse](/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse) retornado. Itere pela lista de `is_anomaly` da resposta e imprima o índice de qualquer valor `true`. Esses valores correspondem ao índice de pontos de dados anômalos, caso algum seja encontrado.

```python
print('Detecting anomalies in the entire time series.')

try:
    response = client.detect_entire_series(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_anomaly):
    print('An anomaly was detected at index:')
    for i, value in enumerate(response.is_anomaly):
        if value:
            print(i)
else:
    print('No anomalies were detected in the time series.')
```

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar o status de anomalias do último ponto de dados

Chame a API do Detector de Anomalias para determinar se o seu ponto de dados mais recente é uma anomalia usando o método `detect_last_point` do cliente e armazene o objeto `LastDetectResponse` retornado. O valor `is_anomaly` da resposta é um valor booliano que especifica o status de anomalias daquele ponto.  

```python
print('Detecting the anomaly status of the latest data point.')

try:
    response = client.detect_last_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if response.is_anomaly:
    print('The latest point is detected as anomaly.')
else:
    print('The latest point is not detected as anomaly.')
```

## <a name="detect-change-points-in-the-data-set"></a>Detectar pontos de alteração no conjunto de dados

Chame a API para detectar pontos de alteração nos dados de série temporal usando o método `detect_change_point` do cliente. Armazene o objeto `ChangePointDetectResponse` retornado. Itere pela lista de `is_change_point` da resposta e imprima o índice de qualquer valor `true`. Esses valores correspondem aos índices de pontos de alteração de tendência, caso algum tenha sido encontrado.

```python
print('Detecting change points in the entire time series.')

try:
    response = client.detect_change_point(request)
except AnomalyDetectorError as e:
    print('Error code: {}'.format(e.error.code), 'Error message: {}'.format(e.error.message))
except Exception as e:
    print(e)

if any(response.is_change_point):
    print('An change point was detected at index:')
    for i, value in enumerate(response.is_change_point):
        if value:
            print(i)
else:
    print('No change point were detected in the time series.')
```

## <a name="run-the-application"></a>Executar o aplicativo

Execute o aplicativo com o comando `python` e o nome do arquivo.

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
