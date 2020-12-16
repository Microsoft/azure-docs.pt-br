---
title: 'Início Rápido: Extrair informações de texto e layout usando o Python e o Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Neste Início Rápido, você usará a API REST de Layout do Reconhecimento de Formulários com o Python para ler dados de texto e de tabela de seus formulários.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: e9368b5c6c90fe7ce65fc066e0b19523e07ece51
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2020
ms.locfileid: "97504331"
---
# <a name="quickstart-extract-text-and-layout-information-using-the-form-recognizer-rest-api-with-python"></a>Início Rápido: Extrair informações de texto e de layout usando a API REST do Reconhecimento de Formulários com Python

Neste início rápido, você usará a API REST de Reconhecimento de Formulários do Azure com o Python para extrair informações de layout e de texto e dados de tabela de documentos de formulário.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/cognitive-services/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, é necessário ter:
- [Python](https://www.python.org/downloads/) instalado (se quiser executar o exemplo localmente).
- Um documento de formulário. Você pode baixar uma imagem do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) (baixar e extrair *sample_data.zip*) para este início rápido.

> [!NOTE]
> Este início rápido usa um documento armazenado localmente. Para saber como usar arquivos remotos acessados pela URL, confira a [documentação de referência](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync).


## <a name="create-a-form-recognizer-resource"></a>Criar um recurso do Reconhecimento de Formulários

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="analyze-the-form-layout"></a>Analisar o layout do formulário

Para começar a analisar o layout, você chama a API **[Analisar Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)** usando o script Python abaixo. Antes de executar o script, faça estas alterações:

1. Substitua `<Endpoint>` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `<path to your form>` pelo caminho para o documento de formulário local.
1. Substitua `<subscription key>` pela chave de assinatura que você copiou na etapa anterior.

    # <a name="v20"></a>[v2.0](#tab/v2-0) 
    ```python
    ########### Python Form Recognizer Async Layout #############
    
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.0/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        # Change Content-Type as appropriate
        'Content-Type': 'application/pdf',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```   
    # <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)  
    ```python
    ########### Python Form Recognizer Async Layout #############
    
    import json
    import time
    from requests import get, post
    
    # Endpoint URL
    endpoint = r"<Endpoint>"
    apim_key = "<Subscription Key>"
    post_url = endpoint + "/formrecognizer/v2.1-preview.2/Layout/analyze"
    source = r"<path to your form>"
    
    headers = {
        # Request headers
        # Change Content-Type as appropriate
        'Content-Type': 'application/pdf',
        'Ocp-Apim-Subscription-Key': apim_key,
    }
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % resp.text)
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit()
    ```  
    
    
      ---


1. Salve o código como um arquivo com uma extensão .py. Por exemplo, *form-recognizer-layout.py*.
1. Abra una janela de prompt de comando.
1. No prompt, use o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-layout.py`.

Você receberá uma resposta `202 (Success)` que inclui um cabeçalho **Operation-Location**, que será impresso pelo script no console. Esse cabeçalho contém uma ID da operação que você pode usar para consultar o status da operação assíncrona e obter os resultados. No seguinte valor de exemplo, a cadeia de caracteres após `operations/` é a ID da operação.

# <a name="v20"></a>[v2.0](#tab/v2-0)   
```console
https://cognitiveservice/formrecognizer/v2.0/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
``` 
# <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)  
```console
https://cognitiveservice/formrecognizer/v2.1-preview.2/layout/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```  

---
    



## <a name="get-the-layout-results"></a>Obter os resultados do layout

Depois de ter chamado a API **Analisar Layout**, você chama a API **[Obter Resultados da Análise de Layout](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeLayoutResult)** para obter o status da operação e os dados extraídos. Adicione o código a seguir à parte inferior do script Python. Este código usa o valor da ID da operação em uma nova chamada à API. Esse script chama a API em intervalos regulares, até que os resultados estejam disponíveis. É recomendável fazer um intervalo de um segundo ou mais.

```python
n_tries = 10
n_try = 0
wait_sec = 6
while n_try < n_tries:
    try:
        resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": apim_key})
        resp_json = json.loads(resp.text)
        if resp.status_code != 200:
            print("GET Layout results failed:\n%s" % resp_json)
            quit()
        status = resp_json["status"]
        if status == "succeeded":
            print("Layout Analysis succeeded:\n%s" % resp_json)
            quit()
        if status == "failed":
            print("Layout Analysis failed:\n%s" % resp_json)
            quit()
        # Analysis still running. Wait and retry.
        time.sleep(wait_sec)
        n_try += 1     
    except Exception as e:
        msg = "GET analyze results failed:\n%s" % str(e)
        print(msg)
        quit()
```

1. Salve o script.
1. Mais uma vez, use o comando `python` para executar o exemplo. Por exemplo, `python form-recognizer-layout.py`.

### <a name="examine-the-response"></a>Examinar a resposta

O script imprimirá as respostas no console até que a operação **Analisar Layout** seja concluída. Em seguida, ele imprimirá os dados extraídos no formato JSON. O nó `"readResults"` contém cada linha de texto com seu respectivo posicionamento de caixa delimitadora na página. O nó `"selectionMarks"` (na versão prévia da v2.1) mostra cada marca de seleção (caixa de seleção, marca de opção) e indica se o status é "marcado" ou "não marcado". O campo `"pageResults"` mostra cada trecho de texto nas tabelas, cada um com sua respectiva coordenada de linha-coluna.

Confira a imagem da fatura a seguir e a respectiva saída em JSON correspondente. A saída foi reduzida para maior simplicidade.

:::image type="content" source="../media/contoso-invoice.png" alt-text="Documento de declaração de projeto da Contoso com uma tabela.":::

# <a name="v20"></a>[v2.0](#tab/v2-0)    
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:36:52Z",
    "lastUpdatedDateTime": "2020-08-20T20:36:58Z",
    "analyzeResult": {
        "version": "2.0.0",
        "readResults": [
            {
                "page": 1,
                "language": "en",
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/14/words/0",
                                    "#/readResults/0/lines/14/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
```

# <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)   
```json
{
    "status": "succeeded",
    "createdDateTime": "2020-08-20T20:40:50Z",
    "lastUpdatedDateTime": "2020-08-20T20:40:55Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [
            {
                "page": 1,
                "angle": 0,
                "width": 8.5,
                "height": 11,
                "unit": "inch",
                "lines": [
                    {
                        "boundingBox": [
                            0.5826,
                            0.4411,
                            2.3387,
                            0.4411,
                            2.3387,
                            0.7969,
                            0.5826,
                            0.7969
                        ],
                        "text": "Contoso, Ltd.",
                        "words": [
                            {
                                "boundingBox": [
                                    0.5826,
                                    0.4411,
                                    1.744,
                                    0.4411,
                                    1.744,
                                    0.7969,
                                    0.5826,
                                    0.7969
                                ],
                                "text": "Contoso,",
                                "confidence": 1
                            },
                            {
                                "boundingBox": [
                                    1.8448,
                                    0.4446,
                                    2.3387,
                                    0.4446,
                                    2.3387,
                                    0.7631,
                                    1.8448,
                                    0.7631
                                ],
                                "text": "Ltd.",
                                "confidence": 1
                            }
                        ]
                    },
                    ...
                        ]
                    }
                ],
                "selectionMarks": [
                    {
                        "boundingBox": [
                            3.9737,
                            3.7475,
                            4.1693,
                            3.7475,
                            4.1693,
                            3.9428,
                            3.9737,
                            3.9428
                        ],
                        "confidence": 0.989,
                        "state": "selected"
                    },
                    ...
                ]
            }
        ],
        "pageResults": [
            {
                "page": 1,
                "tables": [
                    {
                        "rows": 5,
                        "columns": 5,
                        "cells": [
                            {
                                "rowIndex": 0,
                                "columnIndex": 0,
                                "text": "Training Date",
                                "boundingBox": [
                                    0.5133,
                                    4.2167,
                                    1.7567,
                                    4.2167,
                                    1.7567,
                                    4.4492,
                                    0.5133,
                                    4.4492
                                ],
                                "elements": [
                                    "#/readResults/0/lines/12/words/0",
                                    "#/readResults/0/lines/12/words/1"
                                ]
                            },
                            ...
                        ]
                    },
                    ...
                ]
            }
        ]
    }
}
``` 

---



## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a API REST de Reconhecimento de Formulários com Python para extrair o layout de texto de uma fatura. Em seguida, confira a documentação de referência para explorar a API de Reconhecimento de Formulários de forma mais aprofundada.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeLayoutAsync)
