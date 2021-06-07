---
title: 'Início rápido: API REST do Reconhecimento de Formulários'
description: Use a API REST do Reconhecimento de Formulários para criar um aplicativo de processamento de formulários que extraia pares chave/valor e dados de tabela dos seus documentos personalizados.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: e0769f77bffaab0e04b492b19a5ea131cec64f06
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105519"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!NOTE]
> Este guia usa cURL para executar chamadas à API REST. Há também o [código de exemplo no GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/FormRecognizer/rest) que ilustra como chamar as APIs REST com o Python.

## <a name="prerequisites"></a>Pré-requisitos

* [cURL](https://curl.haxx.se/windows/) instalado.
* [PowerShell versão 6.0+](/powershell/scripting/install/installing-powershell-core-on-windows) ou um aplicativo de linha de comando semelhante.
* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Um blob do Armazenamento do Azure contendo um conjunto de dados de treinamento. Confira [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este guia de início rápido, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2090451) (baixe e extraia o *sample_data.zip*).
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Criar um recurso do Reconhecimento de Formulários"  target="_blank">crie um Reconhecimento de Formulários </a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, clique em **Ir para o recurso**.
  * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API do Reconhecimento de Formulários. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
  * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Uma URL para uma imagem de um recibo. Use uma [imagem de exemplo](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-allinone.jpg) para este início rápido.
* Uma URL para uma imagem de um cartão de visita. Use uma [imagem de exemplo](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/business_cards/business-card-english.jpg) para este início rápido.
* Uma URL para uma imagem de uma fatura. Você pode usar um [documento de exemplo](https://raw.githubusercontent.com/Azure/azure-sdk-for-python/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/forms/Invoice_1.pdf) para este início rápido.

## <a name="analyze-layout"></a>Analisar layout

Use o Reconhecimento de Formulários para analisar e extrair tabelas, marcas de seleção, texto e estrutura em documentos, sem a necessidade de treinar um modelo. Para obter mais informações sobre a extração de layout, confira o [Guia conceitual de layout](../../concept-layout.md). Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `\"{your-document-url}` por uma das URLs de exemplo.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.0/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

---

Você receberá uma resposta `202 (Success)` com um cabeçalho **Operation-Location**. O valor desse cabeçalho contém uma ID da operação que você pode usar para consultar o status da operação assíncrona e obter os resultados. No valor de exemplo a seguir, a cadeia de caracteres após `analyzeResults/` é a ID da operação.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Obter resultados do layout

Depois de ter chamado a API **[Analisar Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeLayoutAsync)** , você chama a API **[Obter Resultados da Análise de Layout](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeLayoutResult)** para obter o status da operação e os dados extraídos. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `{resultId}` pela ID da operação da etapa anterior.
<!-- markdownlint-disable MD024 -->

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-results"></a>Examinar os resultados

Você receberá a resposta `200 (success)` com conteúdo JSON.

Confira a imagem da fatura a seguir e a respectiva saída em JSON correspondente.
* O nó `"readResults"` contém cada linha de texto com seu respectivo posicionamento de caixa delimitadora na página. 
* O nó `"selectionMarks"` (na versão prévia da v2.1) mostra cada marca de seleção (caixa de seleção, marca de opção) e indica se o status é "marcado" ou "não marcado". 
* A seção `"pageResults"` inclui as tabelas extraídas. Para cada tabela, o índice de texto, de linha e de coluna, abrangência de linha e coluna, caixa delimitadora e mais são extraídos.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Documento de declaração de projeto da Contoso com uma tabela.":::

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

Essa saída foi reduzida para maior simplicidade. Confira a [saída de exemplo completo no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

### <a name="v20"></a>[v2.0](#tab/v2-0)

Essa saída foi reduzida para maior simplicidade. Confira a [saída de exemplo completo no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json).

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

---

## <a name="analyze-invoices"></a>Analisar faturas

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

Para começar a analisar uma fatura, use o comando cURL abaixo. Para obter mais informações sobre a análise de faturas, confira o [Guia conceitual de faturas](../../concept-invoices.md). Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{your invoice URL}` pelo endereço da URL de um documento de fatura.
1. Substitua `{subscription key}` por sua chave de assinatura.

```bash
curl -v -i POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

Você receberá uma resposta `202 (Success)` com um cabeçalho **Operation-Location**. O valor desse cabeçalho contém uma ID da operação que você pode usar para consultar o status da operação assíncrona e obter os resultados.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Obter resultados da fatura

Depois de chamar a API **[Analisar Fatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9843c2794cbb1a96291)** , chame a API **[Obter Resultado da Análise de Fatura](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/5ed8c9acb78c40a2533aee83)** para obter o status da operação e os dados extraídos. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
1. Substitua `{resultId}` pela ID da operação da etapa anterior.
1. Substitua `{subscription key}` por sua chave de assinatura.

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examinar a resposta

Você receberá a resposta `200 (Success)` como saída JSON. 
* O campo `"readResults"` contém todas as linhas de texto que foram extraídas da fatura.
* O `"pageResults"` inclui as tabelas e as marcas de seleção extraídas da fatura.
* O campo `"documentResults"` contém informações de chave/valor para as partes mais relevantes da fatura.

Confira o documento de fatura e a saída em JSON correspondente a seguir. 

* [Exemplo de fatura](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf)

O conteúdo JSON foi reduzido para facilitar a leitura. Confira a [saída de exemplo completo no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-11-06T23:32:11Z",
    "lastUpdatedDateTime": "2020-11-06T23:32:20Z",
    "analyzeResult": {
        "version": "2.1.0",
        "readResults": [{
            "page": 1,
            "angle": 0,
            "width": 8.5,
            "height": 11,
            "unit": "inch"
        }],
        "pageResults": [{
            "page": 1,
            "tables": [{
                "rows": 3,
                "columns": 4,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "QUANTITY",
                    "boundingBox": [0.4953,
                    5.7306,
                    1.8097,
                    5.7306,
                    1.7942,
                    6.0122,
                    0.4953,
                    6.0122]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "DESCRIPTION",
                    "boundingBox": [1.8097,
                    5.7306,
                    5.7529,
                    5.7306,
                    5.7452,
                    6.0122,
                    1.7942,
                    6.0122]
                },
                ...
                ],
                "boundingBox": [0.4794,
                5.7132,
                8.0158,
                5.714,
                8.0118,
                6.5627,
                0.4757,
                6.5619]
            },
            {
                "rows": 2,
                "columns": 6,
                "cells": [{
                    "rowIndex": 0,
                    "columnIndex": 0,
                    "text": "SALESPERSON",
                    "boundingBox": [0.4979,
                    4.963,
                    1.8051,
                    4.963,
                    1.7975,
                    5.2398,
                    0.5056,
                    5.2398]
                },
                {
                    "rowIndex": 0,
                    "columnIndex": 1,
                    "text": "P.O. NUMBER",
                    "boundingBox": [1.8051,
                    4.963,
                    3.3047,
                    4.963,
                    3.3124,
                    5.2398,
                    1.7975,
                    5.2398]
                },
                ...
                ],
                "boundingBox": [0.4976,
                4.961,
                7.9959,
                4.9606,
                7.9959,
                5.5204,
                0.4972,
                5.5209]
            }]
        }],
        "documentResults": [{
            "docType": "prebuilt:invoice",
            "pageRange": [1,
            1],
            "fields": {
                "AmountDue": {
                    "type": "number",
                    "valueNumber": 610,
                    "text": "$610.00",
                    "boundingBox": [7.3809,
                    7.8153,
                    7.9167,
                    7.8153,
                    7.9167,
                    7.9591,
                    7.3809,
                    7.9591],
                    "page": 1,
                    "confidence": 0.875
                },
                "BillingAddress": {
                    "type": "string",
                    "valueString": "123 Bill St, Redmond WA, 98052",
                    "text": "123 Bill St, Redmond WA, 98052",
                    "boundingBox": [0.594,
                    4.3724,
                    2.0125,
                    4.3724,
                    2.0125,
                    4.7125,
                    0.594,
                    4.7125],
                    "page": 1,
                    "confidence": 0.997
                },
                "BillingAddressRecipient": {
                    "type": "string",
                    "valueString": "Microsoft Finance",
                    "text": "Microsoft Finance",
                    "boundingBox": [0.594,
                    4.1684,
                    1.7907,
                    4.1684,
                    1.7907,
                    4.2837,
                    0.594,
                    4.2837],
                    "page": 1,
                    "confidence": 0.998
                },
                ...                
            }
        }]
    }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

> [!IMPORTANT]
> Este recurso não está disponível na versão selecionada da API.

---

## <a name="train-a-custom-model"></a>Treinar um modelo personalizado

Para treinar um modelo personalizado, você precisará de um conjunto de dados de treinamento em um Azure Storage Blob. É necessário ter no mínimo cinco formulários preenchidos (documentos PDF e/ou imagens) do mesmo tipo/estrutura. Veja [Criar um conjunto de dados de treinamento para um modelo personalizado](../../build-training-data-set.md) para obter dicas e opções para compilar os dados de treinamento.

O treinamento sem rotular dados é a operação padrão e é mais simples. Como alternativa, você pode rotular manualmente alguns ou todos os seus dados de treinamento com antecedência. Esse é um processo mais complexo, mas resulta em um modelo melhor treinado.

> [!NOTE]
> Você também pode treinar modelos com uma interface gráfica do usuário, como a [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md).


### <a name="train-a-model-without-labels"></a>Treinar um modelo sem rótulos

Para treinar um modelo do Reconhecimento de Formulários usando os documentos no contêiner de blobs do Azure, chame a API **[Treinar Modelo Personalizado](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** executando o comando cURL a seguir. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `{SAS URL}` pela URL da Assinatura de Acesso Compartilhado (SAS) do contêiner de armazenamento de Blobs do Azure. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recuperação da URL SAS":::

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}'}"
```

---

Você receberá uma resposta `201 (Success)` com um cabeçalho **Location**. O valor desse cabeçalho é a ID do novo modelo que está sendo treinado.

### <a name="train-a-model-with-labels"></a>Treinar um modelo com rótulos

Para o treinamento com rótulos, você precisará ter arquivos de informações de rótulo especiais (`\<filename\>.pdf.labels.json`) no contêiner do Armazenamento de Blobs junto com os documentos de treinamento. A [ferramenta de rotulagem de exemplo Reconhecimento de Formulários](../../quickstarts/label-tool.md) fornece uma interface do usuário para ajudar você a criar esses arquivos de rótulo. Depois de tê-los, você pode chamar a API **[Treinar o Modelo Personalizado](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/TrainCustomModelAsync)** , com o parâmetro `"useLabelFile"` definido como `true` no corpo JSON.

Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `{SAS URL}` pela URL da Assinatura de Acesso Compartilhado (SAS) do contêiner de armazenamento de Blobs do Azure. [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Recuperação da URL SAS":::

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{SAS URL}', 'useLabelFile':true }"
```

---

Você receberá uma resposta `201 (Success)` com um cabeçalho **Location**. O valor desse cabeçalho é a ID do novo modelo que está sendo treinado.

### <a name="get-training-results"></a>Obter os resultados do treinamento

Depois de iniciar a operação de treinamento, você usa uma nova operação, **[Obter Modelo Personalizado](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** , para verificar o status de treinamento. Passe a ID do modelo para esta chamada à API para verificar o status de treinamento:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a chave de assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` por sua chave de assinatura
1. Substitua `{model ID}` pela ID do modelo que você recebeu na seção anterior

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Você receberá a resposta `200 (Success)` com um corpo JSON no formato a seguir. Observe o campo `"status"`. Ele terá o valor `"ready"` quando o treinamento for concluído. Se o modelo não tiver terminado o treinamento, você precisará consultar o serviço novamente, executando o comando mais uma vez. Recomendamos dar um intervalo de um segundo ou mais entre chamadas.

O campo `"modelId"` contém a ID do modelo que você está treinando. Você precisará dela para a próxima etapa.

```json
{
  "modelInfo":{
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{
    "trainingDocuments":[
      {
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      },
      {
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[

        ],
        "status":"succeeded"
      }
    ],
    "errors":[

    ]
  },
  "keys":{
    "0":[
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-with-a-custom-model"></a>Analisar formulários com um modelo personalizado

Em seguida, você usará seu modelo treinado recentemente para analisar um documento e extrair pares chave-valor e tabelas dele. Chame a API **[Analisar Formulário](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)** executando o comando cURL a seguir. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve da chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
1. Substitua `{model ID}` pela ID do modelo recebida na seção anterior.
1. Substitua `{SAS URL}` por uma URL SAS em seu arquivo no armazenamento do Azure. Siga as etapas na seção Treinamento, mas, em vez de obter uma URL SAS para todo o contêiner de blobs, obtenha uma para o arquivo específico que você deseja analisar.
1. Substitua `{subscription key}` por sua chave de assinatura.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyze?includeTextDetails=true" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" -d "{ 'source': '{SAS URL}' } "
```

---

Você receberá uma resposta `202 (Success)` com um cabeçalho **Operation-Location**. O valor desse cabeçalho inclui uma ID de resultados que pode ser usada para rastrear os resultados da operação de análise. Salve essa ID de resultados para a próxima etapa.

### <a name="get-the-analyze-results"></a>Obter os resultados da análise

Chame a API Get **[Analisar Resultado da Forma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeFormResult)** para consultar os resultados da operação de análise.

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve da chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
1. Substitua `{result ID}` pela ID recebida na seção anterior.
1. Substitua `{subscription key}` por sua chave de assinatura.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{model ID}/analyzeResults/{result ID}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Você receberá a resposta `200 (Success)` com um corpo JSON no formato a seguir. A saída foi abreviada para maior simplicidade. Observe o campo `"status"` próximo à parte inferior. Ele terá o valor `"succeeded"` quando a operação de análise for concluída. Se a operação de análise não tiver sido concluída, você precisará consultar o serviço novamente, executando o comando mais uma vez. Recomendamos dar um intervalo de um segundo ou mais entre chamadas.

Em modelos personalizados treinados sem rótulos, as associações e tabelas do par chave/valor estão no nó `"pageResults"` da saída JSON. Em modelos personalizados treinados com rótulos, as associações e tabelas do par chave/valor estão no nó `"documentResults"`. Se você também especificou a extração de texto sem formatação por meio do parâmetro de URL *includeTextDetails*, o nó `"readResults"` mostrará o conteúdo e as posições de todo o texto no documento.

Este exemplo de saída JSON foi abreviado para manter a simplicidade. Confira a [saída de exemplo completo no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/analyze-result-invoice-6.pdf.json).

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T01:13:28Z",
  "lastUpdatedDateTime": "2020-08-21T01:13:42Z",
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
            "text": "Project Statement",
            "boundingBox": [
              5.0444,
              0.3613,
              8.0917,
              0.3613,
              8.0917,
              0.6718,
              5.0444,
              0.6718
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0444,
                  0.3587,
                  6.2264,
                  0.3587,
                  6.2264,
                  0.708,
                  5.0444,
                  0.708
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3361,
                  0.3635,
                  8.0917,
                  0.3635,
                  8.0917,
                  0.6396,
                  6.3361,
                  0.6396
                ]
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
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9833,
                1.0615,
                7.3333,
                1.0615,
                7.3333,
                1.1649,
                6.9833,
                1.1649
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "value": {
              "text": "9/10/2020",
              "boundingBox": [
                7.3833,
                1.0802,
                7.925,
                1.0802,
                7.925,
                1.174,
                7.3833,
                1.174
              ],
              "elements": [
                "#/readResults/0/lines/3/words/0"
              ]
            },
            "confidence": 1
          },
          ...
        ], 
        "tables": [
          {
            "rows": 5,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6944,
                  4.2779,
                  1.5625,
                  4.2779,
                  1.5625,
                  4.4005,
                  0.6944,
                  4.4005
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ], 
    "documentResults": [],
    "errors": []
  }
}
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```JSON
{
  "status": "succeeded",
  "createdDateTime": "2020-08-21T00:46:25Z",
  "lastUpdatedDateTime": "2020-08-21T00:46:32Z",
  "analyzeResult": {
    "version": "2.0.0",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 8.5,
        "height": 11,
        "unit": "inch",
        "lines": [
          {
            "text": "Project Statement",
            "boundingBox": [
              5.0153,
              0.275,
              8.0944,
              0.275,
              8.0944,
              0.7125,
              5.0153,
              0.7125
            ],
            "words": [
              {
                "text": "Project",
                "boundingBox": [
                  5.0153,
                  0.275,
                  6.2278,
                  0.275,
                  6.2278,
                  0.7125,
                  5.0153,
                  0.7125
                ]
              },
              {
                "text": "Statement",
                "boundingBox": [
                  6.3292,
                  0.275,
                  8.0944,
                  0.275,
                  8.0944,
                  0.7125,
                  6.3292,
                  0.7125
                ]
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
        "keyValuePairs": [
          {
            "key": {
              "text": "Date:",
              "boundingBox": [
                6.9722,
                1.0264,
                7.3417,
                1.0264,
                7.3417,
                1.1931,
                6.9722,
                1.1931
              ],
              "elements": [
                "#/readResults/0/lines/2/words/0"
              ]
            },
            "confidence": 1
          },
         ...
        ],
        "tables": [
          {
            "rows": 4,
            "columns": 5,
            "cells": [
              {
                "text": "Training Date",
                "rowIndex": 0,
                "columnIndex": 0,
                "boundingBox": [
                  0.6931,
                  4.2444,
                  1.5681,
                  4.2444,
                  1.5681,
                  4.4125,
                  0.6931,
                  4.4125
                ],
                "confidence": 1,
                "rowSpan": 1,
                "columnSpan": 1,
                "elements": [
                  "#/readResults/0/lines/15/words/0",
                  "#/readResults/0/lines/15/words/1"
                ],
                "isHeader": true,
                "isFooter": false
              },
              ...
            ]
          }
        ], 
        "clusterId": 0
      }
    ],
    "documentResults": [],
    "errors": []
  }
}
```  

---

### <a name="improve-results"></a>Aprimorar os resultados

[!INCLUDE [improve results](../improve-results-unlabeled.md)]

## <a name="analyze-receipts"></a>Analisar recibos

Esta seção demonstra como analisar e extrair campos comuns de recibos dos EUA usando um modelo de recibo pré-treinado. Para obter mais informações sobre a análise de recibos, confira o [Guia conceitual de recibos](../../concept-receipts.md). Para começar a analisar um recibo, chame a API **[Analisar Recibo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeReceiptAsync)** usando o comando cURL abaixo. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{your receipt URL}` pelo endereço da URL de uma imagem de recibo.
1. Substitua `{subscription key>` pela chave de assinatura que você copiou na etapa anterior.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

---

Você receberá uma resposta `202 (Success)` com um cabeçalho **Operation-Location**. O valor desse cabeçalho contém uma ID da operação que você pode usar para consultar o status da operação assíncrona e obter os resultados. No valor de exemplo a seguir, a cadeia de caracteres após `operations/` é a ID da operação.

```console
https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/operations/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-the-receipt-results"></a>Obter os resultados do recibo

Depois de ter chamado a API **Analisar Recibo**, chame a API **[Obter Resultados de Analisar Recibo](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeReceiptResult)** para receber o status da operação e os dados extraídos. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
1. Substitua `{operationId}` pela ID da operação da etapa anterior.
1. Substitua `{subscription key}` por sua chave de assinatura.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -X GET "https://{Endpoint}/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

### <a name="examine-the-response"></a>Examinar a resposta

Você receberá a resposta `200 (Success)` como saída JSON. O primeiro campo, `"status"`, indica o status da operação. Se a operação não foi concluída, o valor de `"status"` será `"running"` ou `"notStarted"`, e você deverá chamar a API novamente, manualmente ou por meio de um script. Recomendamos dar um intervalo de um segundo ou mais entre chamadas.

O nó `"readResults"` contém todo o texto reconhecido (se você define o parâmetro opcional *includeTextDetails* como `true`). O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. O nó `"documentResults"` contém os valores específicos de recibo que o modelo descobriu. É aí que você encontrará pares úteis de chave/valor como imposto, total, endereço de comerciante e assim por diante.

Confira a imagem do recibo a seguir e a saída em JSON correspondente.

![Um recibo da loja Contoso](../../media/contoso-allinone.jpg)

Essa saída foi reduzida para facilitar a leitura. Confira a [saída de exemplo completo no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/receipt-result.json).

```json
{
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{
    "version":"2.1.0",
    "readResults":[
      {
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[
          {
            "text":"Contoso",
            "boundingBox":[
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[
              {
                "text":"Contoso",
                "boundingBox":[
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[
      {
        "docType":"prebuilt:receipt",
        "pageRange":[
          1,
          1
        ],
        "fields":{
          "ReceiptType":{
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{
            "type":"array",
            "valueArray":[
              {
                "type":"object",
                "valueObject":{
                  "Quantity":{
                    "type":"number",
                    "text":"1",
                    "boundingBox":[
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```

## <a name="analyze-business-cards"></a>Analisar cartões de visita

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)  

Esta seção demonstra como analisar e extrair campos comuns de cartões de visita em inglês usando um modelo pré-treinado. Para obter mais informações sobre a análise de cartões de visita, confira o [Guia conceitual de cartões de visita](../../concept-business-cards.md). Para começar a analisar um cartão de visita, chame a API **[Analisar Cartão de Visita](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeBusinessCardAsync)** usando o comando cURL abaixo. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{your business card URL}` pelo endereço da URL de uma imagem de recibo.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.

```bash
curl -i -X POST "https://{Endpoint}/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{ 'source': '{your receipt URL}'}"
```

Você receberá uma resposta `202 (Success)` com um cabeçalho **Operation-Location**. O valor desse cabeçalho contém uma ID da operação que você pode usar para consultar o status da operação assíncrona e obter os resultados.

```console
https://cognitiveservice/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-business-card-results"></a>Obter resultados do cartão de visita

Depois de chamar a API **Analisar Cartão de Visita**, chame a API **[Obter Resultado da Análise do Cartão de Visita](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetAnalyzeBusinessCardResult)** para obter o status da operação e os dados extraídos. Antes de executar o comando, faça essas alterações:

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a chave de assinatura do Reconhecimento de Formulários. Encontre-o na guia **Visão geral** de recursos do Reconhecimento de Formulários.
1. Substitua `{resultId}` pela ID da operação da etapa anterior.
1. Substitua `{subscription key}` por sua chave de assinatura.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/formrecognizer/v2.1-preview.3/prebuilt/businessCard/analyzeResults/{resultId}"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Examinar a resposta

Você receberá a resposta `200 (Success)` como saída JSON. 

O nó `"readResults"` contém todo o texto reconhecido. O texto é organizado por página, depois por linha e, em seguida, por palavras individuais. O nó `"documentResults"` contém os valores específicos do cartão de visita descobertos pelo modelo. É nele que você encontrará informações de contato úteis, como nome da empresa, nome, sobrenome, número de telefone etc.

![Um cartão de visita da empresa Contoso](../../media/business-card-english.jpg)

Este exemplo de saída JSON foi reduzido para facilitar a leitura. Confira a [saída de exemplo completo no GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/business-card-result.json).

```json
{
    "status": "succeeded",
    "createdDateTime": "2020-06-04T08:19:29Z",
    "lastUpdatedDateTime": "2020-06-04T08:19:35Z",
    "analyzeResult": {
        "version": "2.1.1",
        "readResults": [
            {
                "page": 1,
                "angle": -17.0956,
                "width": 4032,
                "height": 3024,
                "unit": "pixel"
            }
        ],
        "documentResults": [
            {
                "docType": "prebuilt:businesscard",
                "pageRange": [
                    1,
                    1
                ],
                "fields": {
                    "ContactNames": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "object",
                                "valueObject": {
                                    "FirstName": {
                                        "type": "string",
                                        "valueString": "Avery",
                                        "text": "Avery",
                                        "boundingBox": [
                                            703,
                                            1096,
                                            1134,
                                            989,
                                            1165,
                                            1109,
                                            733,
                                            1206
                                        ],
                                        "page": 1
                                },
                                "text": "Dr. Avery Smith",
                                "boundingBox": [
                                    419.3,
                                    1154.6,
                                    1589.6,
                                    877.9,
                                    1618.9,
                                    1001.7,
                                    448.6,
                                    1278.4
                                ],
                                "confidence": 0.993
                            }
                        ]
                    },
                    "Emails": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "avery.smith@contoso.com",
                                "text": "avery.smith@contoso.com",
                                "boundingBox": [
                                    2107,
                                    934,
                                    2917,
                                    696,
                                    2935,
                                    764,
                                    2126,
                                    995
                                ],
                                "page": 1,
                                "confidence": 0.99
                            }
                        ]
                    },
                    "Websites": {
                        "type": "array",
                        "valueArray": [
                            {
                                "type": "string",
                                "valueString": "https://www.contoso.com/",
                                "text": "https://www.contoso.com/",
                                "boundingBox": [
                                    2121,
                                    1002,
                                    2992,
                                    755,
                                    3014,
                                    826,
                                    2143,
                                    1077
                                ],
                                "page": 1,
                                "confidence": 0.995
                            }
                        ]
                    }
                }
            }
        ]
    }
}
```

O script imprimirá as respostas no console até que a operação **Analisar Cartão de Visita** seja concluída.

### <a name="v20"></a>[v2.0](#tab/v2-0)  

> [!IMPORTANT]
> Este recurso não está disponível na versão selecionada da API.

---

## <a name="manage-custom-models"></a>Gerenciar modelos personalizados

### <a name="get-a-list-of-custom-models"></a>Obter uma lista de modelos personalizados

Use a API **[Lista dos Modelos Personalizados](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModels)** no comando a seguir para retornar uma lista de todos os modelos personalizados que pertencem à sua assinatura.

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models?op=full"
-H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Você receberá uma resposta de êxito `200`, com os dados JSON como exibidos a seguir. O elemento `"modelList"` contém todos os seus modelos criados e suas informações.

```json
{
  "summary": {
    "count": 0,
    "limit": 0,
    "lastUpdatedDateTime": "string"
  },
  "modelList": [
    {
      "modelId": "string",
      "status": "creating",
      "createdDateTime": "string",
      "lastUpdatedDateTime": "string"
    }
  ],
  "nextLink": "string"
}
```

### <a name="get-a-specific-model"></a>Obter um modelo específico

Para recuperar informações detalhadas sobre um modelo personalizado específico, use a API **[Obter Modelo Personalizado](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/GetCustomModel)** no comando a seguir.

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `{modelId}` pela ID do modelo personalizado que você deseja pesquisar.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X GET "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Você receberá uma resposta de êxito `200`, com os dados JSON como exibidos a seguir.

```json
{
  "modelInfo": {
    "modelId": "string",
    "status": "creating",
    "createdDateTime": "string",
    "lastUpdatedDateTime": "string"
  },
  "keys": {
    "clusters": {}
  },
  "trainResult": {
    "trainingDocuments": [
      {
        "documentName": "string",
        "pages": 0,
        "errors": [
          "string"
        ],
        "status": "succeeded"
      }
    ],
    "fields": [
      {
        "fieldName": "string",
        "accuracy": 0.0
      }
    ],
    "averageModelAccuracy": 0.0,
    "errors": [
      {
        "message": "string"
      }
    ]
  }
}
```

### <a name="delete-a-model-from-the-resource-account"></a>Excluir um modelo da conta do recurso

Exclua também um modelo da sua conta referenciando a ID. Esse comando chama a API **[Excluir o Modelo Personalizado](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/DeleteCustomModel)** para excluir o modelo usado na seção anterior.

1. Substitua `{Endpoint}` pelo ponto de extremidade que você obteve com a assinatura do Reconhecimento de Formulários.
1. Substitua `{subscription key}` pela chave de assinatura que você copiou na etapa anterior.
1. Substitua `{modelId}` pela ID do modelo personalizado que você deseja pesquisar.

### <a name="v21-preview"></a>[Versão prévia v2.1](#tab/v2-1)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.1-preview.3/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="v20"></a>[v2.0](#tab/v2-0)

```bash
curl -v -X DELETE "https://{Endpoint}/formrecognizer/v2.0/custom/models/{modelId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

---

Você receberá uma resposta `204` de êxito, indicando que seu modelo está marcado para exclusão. Os artefatos de modelo serão removidos dentro de 48 horas.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você usou a API REST do Reconhecimento de Formulários para treinar modelos e analisar formulários de diferentes maneiras. Em seguida, confira a documentação de referência para explorar a API de Reconhecimento de Formulários de forma mais aprofundada.

> [!div class="nextstepaction"]
> [Documentação de referência da API REST](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-3/operations/AnalyzeWithCustomForm)

* [O que é o Reconhecimento de Formulários?](../../overview.md)