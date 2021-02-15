---
title: Atualizar para Leitura v3.0 da API da Pesquisa Visual Computacional
titleSuffix: Azure Cognitive Services
description: Saiba como atualizar a API de Leitura da Pesquisa Visual Computacional v2.0/v2.1 para a v3.0.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 08/11/2020
ms.author: pafarley
ROBOTS: NOINDEX
ms.openlocfilehash: cfc9745fc4684a7b0d8f7da7e63149a6fe50f6d2
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331831"
---
# <a name="upgrade-from-read-v2x-to-read-v3x"></a>Atualizar de Leitura v2.x para Leitura v3.x

Este guia mostra como atualizar o código da API de nuvem ou o contêiner existente da Leitura v2.x para a Leitura v3.0 e para o v3.1 versão prévia.

## <a name="determine-your-api-path"></a>Determinar o caminho da API
Use a tabela a seguir para determinar a **cadeia de caracteres de versão** no caminho da API com base na versão da Leitura 3. x para a qual você está migrando.

|Tipo de produto| Versão | Cadeia de caracteres de versão no caminho da API 3.x |
|:-----|:----|:----|
|Serviço | Leitura 3.0 ou 3.1 | **v3.0** ou **v3.1** , respectivamente |
|Serviço | Versão prévia da Leitura 3.2 | **v3.2-preview.1** |
|Contêiner | Versão prévia da Leitura 3.0 ou da Leitura 3.1 | **v3.0** ou **v3.1-preview.2** , respectivamente |


Em seguida, use as seções a seguir para restringir suas operações e substituir a **cadeia de caracteres de versão** no caminho da API pelo valor da tabela. Por exemplo, para as versões de nuvem e contêiner da **Versão prévia da Leitura v3.2** , atualize o caminho da API para **https://{endpoint}/vision/v3.2-preview.1/read/analyze[?language]** .

## <a name="servicecontainer"></a>Serviço/contêiner

### `Batch Read File`

|Read 2.x |Leitura 3.x  |
|----------|-----------|
|https://{endpoint}/vision/ **v2.0/read/core/asyncBatchAnalyze**     |https://{endpoint}/vision/< **cadeia de caracteres de versão** >/read/analyze[?language]|
    
Um novo parâmetro _language_ opcional está disponível. Se não souber o idioma de seu documento, ou se ele puder ser multilíngue, não o inclua. 

### `Get Read Results`

|Read 2.x |Leitura 3.x  |
|----------|-----------|
|https://{endpoint}/vision/ **v2.0/read/operations** /{operationId}     |https://{endpoint}/vision/< **cadeia de caracteres de versão** >/read/analyzeResults/{operationId}|

### <a name="get-read-operation-result-status-flag"></a>Sinalizador de status `Get Read Operation Result`

Quando é bem-sucedida, a chamada para `Get Read Operation Result` retorna um campo de cadeia de caracteres de status no corpo JSON.
 
|Read 2.x |Leitura 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|
    
### <a name="api-response-json"></a>Resposta da API (JSON) 

Observe as seguintes alterações no JSON:
* No v2.x, `Get Read Operation Result` retornará o JSON de reconhecimento de OCR quando o status for `Succeeded"`. No v3.0, esse campo é `succeeded`.
* Para obter a raiz da matriz da página, altere a hierarquia JSON de `recognitionResults` para `analyzeResult`/`readResults`. A hierarquia JSON de linhas e de palavras por página permanece inalterada, portanto, nenhuma alteração de código é necessária.
* O ângulo da página `clockwiseOrientation` foi renomeado para `angle` e o intervalo foi alterado de 0 a 360 graus para -180 a 180 graus. Dependendo de seu código, você pode ou não precisar fazer alterações, pois a maioria das funções matemáticas pode lidar com ambos os intervalos.

A API v3.0 também introduz os seguintes aprimoramentos, cujo uso é opcional:
* `createdDateTime` e `lastUpdatedDateTime` foram adicionados para que você possa acompanhar a duração do processamento. Confira a documentação para obter mais detalhes. 
* `version` informa a versão da API usada para gerar os resultados
* Um `confidence` por palavra foi adicionado. Esse valor é calibrado de maneira que um valor de 0.95 indique que há uma chance de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar qual texto deve ser enviado para revisão humana. 
    
    
Na 2.X, o formato de saída é o seguinte: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResults": [
                    {
                    "page": 1,
                    "language": "en",
                    "clockwiseOrientation": 349.59,
                    "width": 2661,
                    "height": 1901,
                    "unit": "pixel",
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
}
```
    
Na v3.0, ele foi ajustado:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "language": "en",
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="service-only"></a>Somente serviço

### `Recognize Text`
`Recognize Text` é uma operação de *versão prévia* que está sendo *preterida em todas as versões da API da Pesquisa Visual Computacional* . É necessário migrar da `Recognize Text` para a `Read` (v3.0) ou `Batch Read File` (v2.0, v2.1). A v3.0 de `Read` inclui modelos mais novos e melhores para reconhecimento de texto, além de recursos adicionais e, portanto, é recomendada. Para fazer upgrade de `Recognize Text` para `Read`:

|Reconhecimento de Texto 2.x |Leitura 3.x  |
|----------|-----------|
|https://{endpoint}/vision/ **v2.0/recognizeText[?mode]**|https://{endpoint}/vision/< **cadeia de caracteres de versão** >/read/analyze[?language]|
    
O parâmetro _mode_ não tem suporte no `Read`. Tanto texto manuscrito quanto impresso terão suporte automaticamente.
    
Um novo parâmetro _language_ opcional está disponível na v3.0. Se não souber o idioma de seu documento, ou se ele puder ser multilíngue, não o inclua. 

### `Get Recognize Text Operation Result`

|Reconhecimento de Texto 2.x |Leitura 3.x  |
|----------|-----------|
|https://{endpoint}/vision/ **v2.0/textOperations/** {operationId}|https://{endpoint}/vision/< **cadeia de caracteres de versão** >/read/analyzeResults/{operationId}|

### <a name="get-recognize-text-operation-result-status-flags"></a>Sinalizadores de status `Get Recognize Text Operation Result`
Quando é bem-sucedida, a chamada para `Get Recognize Text Operation Result` retorna um campo de cadeia de caracteres de status no corpo JSON. 
 
|Reconhecimento de Texto 2.x |Leitura 3.x  |
|----------|-----------|
|`"NotStarted"` |    `"notStarted"`|
|`"Running"` | `"running"`|
|`"Failed"` | `"failed"`|
|`"Succeeded"` | `"succeeded"`|

### <a name="api-response-json"></a>Resposta da API (JSON)

Observe as seguintes alterações no JSON:    
* No v2.x, `Get Read Operation Result` retornará o JSON de reconhecimento de OCR quando o status for `Succeeded`. No v3.x, esse campo é `succeeded`.
* Para obter a raiz da matriz da página, altere a hierarquia JSON de `recognitionResult` para `analyzeResult`/`readResults`. A hierarquia JSON de linhas e de palavras por página permanece inalterada, portanto, nenhuma alteração de código é necessária.

A API v3.0 também introduz os aprimoramentos a seguir, cujo uso é opcional. Confira a referência da API para obter mais detalhes:
* `createdDateTime` e `lastUpdatedDateTime` foram adicionados para que você possa acompanhar a duração do processamento. Confira a documentação para obter mais detalhes. 
* `version` informa a versão da API usada para gerar os resultados
* Um `confidence` por palavra foi adicionado. Esse valor é calibrado de maneira que um valor de 0.95 indique que há uma chance de 95% de o reconhecimento estar correto. A pontuação de confiança pode ser usada para selecionar qual texto deve ser enviado para revisão humana. 
* `angle` orientação geral do texto no sentido horário, medida em graus entre (-180, 180).
* `width` e `"height"` fornecem as dimensões do documento e `"unit"` fornece a unidade dessas dimensões (pixels ou polegadas, dependendo do tipo de documento).
* `page` há suporte para documentos multipágina
* `language` o idioma de entrada do documento (do parâmetro opcional _language_ .)


Na 2.X, o formato de saída é o seguinte: 
    
```json
    {
        {
                "status": "Succeeded",
                "recognitionResult": [
                    {
                    "lines": [
                        {
                        "boundingBox": [
                            67,
                            646,
                            2582,
                            713,
                            2580,
                            876,
                            67,
                            821
                        ],
                        "text": "The quick brown fox jumps",
                        "words": [
                            {
                            "boundingBox": [
                                143,
                                650,
                                435,
                                661,
                                436,
                                823,
                                144,
                                824
                            ],
                            "text": "The",
                            },
            // The rest of result is omitted for brevity 
            
    }
```
    
No v3.x, ele foi ajustado:
    
```json
    {
        {
            "status": "succeeded",
            "createdDateTime": "2020-05-28T05:13:21Z",
            "lastUpdatedDateTime": "2020-05-28T05:13:22Z",
            "analyzeResult": {
            "version": "3.0.0",
            "readResults": [
                {
                "page": 1,
                "angle": 0.8551,
                "width": 2661,
                "height": 1901,
                "unit": "pixel",
                "lines": [
                    {
                    "boundingBox": [
                        67,
                        646,
                        2582,
                        713,
                        2580,
                        876,
                        67,
                        821
                    ],
                    "text": "The quick brown fox jumps",
                    "words": [
                        {
                        "boundingBox": [
                            143,
                            650,
                            435,
                            661,
                            436,
                            823,
                            144,
                            824
                        ],
                        "text": "The",
                        "confidence": 0.958
                        },
        // The rest of result is omitted for brevity 
        
    }
```

## <a name="container-only"></a>Somente contêiner

### `Synchronous Read`

|Leitura 2.0 |Leitura 3.x  |
|----------|-----------|
|https://{endpoint}/vision/ **v2.0/read/core/Analyze**     |https://{endpoint}/vision/< **cadeia de caracteres de versão** >/read/syncAnalyze[?language]|
