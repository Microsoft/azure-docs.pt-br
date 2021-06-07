---
title: Chamar a API da Análise de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo explica como você pode chamar os serviços cognitivas do Azure Análise de Texto a API REST e o postmaster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: 3c6fb1ca23bcc9c57e73bcaf960e0387611fcff3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599197"
---
# <a name="how-to-call-the-text-analytics-rest-api"></a>Como chamar a API REST de Análise de Texto

Neste artigo, usamos a API REST do Análise de Texto e o [postmaster](https://www.postman.com/downloads/) para demonstrar os principais conceitos. A API fornece vários pontos de extremidade síncronos e assíncronos para usar os recursos do serviço. 

## <a name="create-a-text-analytics-resource"></a>Criar um recurso de Análise de Texto

> [!NOTE]
> * Você precisará de um recurso de Análise de Texto usando um tipo de [preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/) padrão (S) se desejar usar `/analyze` os `/health` pontos de extremidade ou. O `/analyze` ponto de extremidade está incluído em seu [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

Antes de usar o API de Análise de Texto, você precisará criar um recurso do Azure com uma chave e um ponto de extremidade para seus aplicativos. 

1.  Primeiro, vá para a [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics) e crie um novo recurso de análise de texto, se você ainda não tiver um. Escolha um [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/).

2.  Selecione a região que você deseja usar para o ponto de extremidade.  Observe que os `/analyze` `/health` pontos de extremidade e estão disponíveis apenas nas seguintes regiões: oeste dos EUA 2, leste dos EUA 2, EUA Central, Europa Setentrional e Europa Ocidental.

3.  Crie o recurso Análise de Texto e vá para a "chaves e folha de ponto de extremidade" à esquerda da página. Copie a chave a ser usada posteriormente quando você chamar as APIs. Você o adicionará posteriormente como um valor para o `Ocp-Apim-Subscription-Key` cabeçalho.

## <a name="change-your-pricing-tier"></a>Alterar o tipo de preço 

Se você tiver um recurso de Análise de Texto existente usando o tipo de preço S0 a S4, deverá atualizá-lo para usar o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)Standard (S). Os tipos de preço S0 a S4 serão desativados. Para atualizar o preço do recurso:

1. Navegue até o recurso de Análise de Texto no [portal do Azure](https://portal.azure.com/).
2. Selecione **tipo de preço** no menu de navegação à esquerda. Ele estará abaixo do **Gerenciamento de recursos**. 
3. Escolha o tipo de preço Standard (S). Em seguida, clique em **Selecionar**.

Você também pode criar um novo recurso de Análise de Texto com o tipo de preço Standard (S) e migrar seus aplicativos para usar as credenciais para o novo recurso. 

## <a name="using-the-api-synchronously"></a>Usando a API de forma síncrona

Você pode chamar Análise de Texto de forma síncrona (para cenários de baixa latência). Você precisa chamar cada API (recurso) separadamente ao usar a API síncrona. Se você precisar chamar vários recursos, confira a seção abaixo sobre como chamar Análise de Texto de forma assíncrona. 

## <a name="using-the-api-asynchronously"></a>Usando a API de forma assíncrona

A partir do v 3.1-Preview. 3, o API de Análise de Texto fornece dois pontos de extremidade assíncronos: 

* O `/analyze` ponto de extremidade para análise de texto permite que você analise o mesmo conjunto de documentos de texto com vários recursos de análise de texto em uma chamada à API. Anteriormente, para usar vários recursos, você precisaria fazer chamadas de API separadas para cada operação. Considere esse recurso quando precisar analisar grandes conjuntos de documentos com mais de um recurso de Análise de Texto.

* O `/health` ponto de extremidade para análise de texto para integridade, que pode extrair e rotular informações médicas relevantes de documentos clínicos.  

Consulte a tabela abaixo para ver quais recursos podem ser usados de forma assíncrona. Observe que apenas alguns recursos podem ser chamados do ponto de `/analyze` extremidade. 

| Recurso | Síncrono | Assíncronos |
|--|--|--|
| Detecção de idioma | ✔ |  |
| Análise de sentimento | ✔ |  |
| Mineração de opinião | ✔ |  |
| Extração de frases-chave | ✔ | ✔* |
| Reconhecimento de entidade nomeada (incluindo PII e PHI) | ✔ | ✔* |
| Vinculação de entidade | ✔ | ✔* |
| Análise de Texto para integridade (contêiner) | ✔ |  |
| Análise de Texto para integridade (API) |  | ✔  |

`*` – Chamado de forma assíncrona através do `/analyze` ponto de extremidade.


[!INCLUDE [text-analytics-api-references](../includes/text-analytics-api-references.md)]

<a name="json-schema"></a>

## <a name="api-request-formats"></a>Formatos de solicitação de API

Você pode enviar chamadas síncronas e assíncronas para o API de Análise de Texto.

#### <a name="synchronous"></a>[Síncrono](#tab/synchronous)

### <a name="synchronous-requests"></a>Solicitações síncronas

O formato das solicitações de API é o mesmo para todas as operações síncronas. Os documentos são enviados em um objeto JSON como texto não estruturado bruto. Não há suporte para XML. O esquema JSON consiste nos elementos descritos abaixo.

| Elemento | Valores válidos | Necessário? | Uso |
|---------|--------------|-----------|-------|
|`id` |O tipo de dados é a cadeia de caracteres, mas na prática as IDs do documento tendem a ser números inteiros. | Obrigatório | O sistema usa as IDs que você fornece para estruturar o resultado. São gerados códigos de idioma, pontuações de sentimento e frases-chave para cada ID na solicitação.|
|`text` | Texto bruto não estruturado, até 5.120 caracteres. | Obrigatório | O texto pode ser expresso em qualquer idioma para a detecção de idioma. Para análise de sentimento, extração de frases-chave e identificação de entidades, o texto deve estar em um [idioma compatível](../language-support.md). |
|`language` | Código [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de dois caracteres para um [idioma compatível](../language-support.md) | Varia | Obrigatório para análise de sentimento, extração de frases-chave e vinculação de entidade; opcional para detecção de idioma. Nenhum erro ocorre se você excluí-lo, mas a análise é enfraquecida sem ele. O código de idioma deve corresponder ao `text` fornecido. |

Veja a seguir um exemplo de uma solicitação de API para os pontos de extremidade de Análise de Texto síncronos. 

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Sample text to be sent to the text analytics api."
    }
  ]
}
```

#### <a name="asynchronous"></a>[Assíncronos](#tab/asynchronous)

### <a name="asynchronous-requests-to-the-analyze-endpoint"></a>Solicitações assíncronas para o `/analyze` ponto de extremidade

> [!NOTE]
> A versão mais recente da biblioteca de cliente do Análise de Texto permite que você chame operações de análise assíncrona usando um objeto de cliente. Você pode encontrar exemplos no GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

O `/analyze` ponto de extremidade permite que você escolha qual dos recursos de análise de texto com suporte você deseja usar em uma única chamada à API. Atualmente, esse ponto de extremidade dá suporte a:

* Extração de Frases-Chave 
* Reconhecimento de entidade nomeada (incluindo PII e PHI)
* Vinculação de Identidade

| Elemento | Valores válidos | Necessário? | Uso |
|---------|--------------|-----------|-------|
|`displayName` | String | Opcional | Usado como o nome de exibição para o identificador exclusivo para o trabalho.|
|`analysisInput` | Inclui o `documents` campo abaixo | Obrigatório | Contém as informações para os documentos que você deseja enviar. |
|`documents` | Inclui os `id` `text` campos e abaixo | Obrigatório | Contém informações para cada documento que está sendo enviado e o texto bruto do documento. |
|`id` | String | Obrigatório | As IDs que você fornece são usadas para estruturar a saída. |
|`text` | Texto bruto não estruturado, até 125.000 caracteres. | Obrigatório | Deve estar no idioma inglês, que é o único idioma com suporte no momento. |
|`tasks` | Inclui os seguintes recursos de análise de texto `entityRecognitionTasks` : `entityLinkingTasks` , `keyPhraseExtractionTasks` ou `entityRecognitionPiiTasks` . | Obrigatório | Um ou mais dos Análise de Texto recursos que você deseja usar. Observe que `entityRecognitionPiiTasks` tem um `domain` parâmetro opcional que pode ser definido como `pii` ou `phi` e o `pii-categories` para detecção de tipos de entidade selecionados. Se o `domain` parâmetro não for especificado, o sistema padrão será `pii` . |
|`parameters` | Inclui os `model-version` `stringIndexType` campos e abaixo | Obrigatório | Esse campo está incluído nas tarefas de recurso acima que você escolher. Eles contêm informações sobre a versão do modelo que você deseja usar e o tipo de índice. |
|`model-version` | String | Obrigatório | Especifique qual versão do modelo está sendo chamada que você deseja usar.  |
|`stringIndexType` | String | Obrigatório | Especifique o decodificador de texto que corresponde ao seu ambiente de programação.  Tipos com suporte são `textElement_v8` (padrão), `unicodeCodePoint` , `utf16CodeUnit` . Consulte o [artigo deslocamentos de texto](../concepts/text-offsets.md#offsets-in-api-version-31-preview) para obter mais informações.  |
|`domain` | String | Opcional | Aplica-se apenas como um parâmetro à `entityRecognitionPiiTasks` tarefa e pode ser definido como `pii` ou `phi` . O padrão é `pii` se não especificado.  |

```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "entityRecognitionTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "entityLinkingTasks": [
            {
                "parameters": {
                    "model-version": "latest",
                    "stringIndexType": "TextElements_v8"
                }
            }
        ],
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
        "entityRecognitionPiiTasks": [{
            "parameters": {
                "model-version": "latest",
                "stringIndexType": "TextElements_v8",
                "domain": "phi",
                "pii-categories":"default"
            }
        }]
    }
}

```

### <a name="asynchronous-requests-to-the-health-endpoint"></a>Solicitações assíncronas para o `/health` ponto de extremidade

O formato de solicitações de API para o Análise de Texto para API de integridade hospedada é o mesmo para seu contêiner. Os documentos são enviados em um objeto JSON como texto não estruturado bruto. Não há suporte para XML. O esquema JSON consiste nos elementos descritos abaixo.  Preencha e envie o formulário de [solicitação de serviços cognitivas](https://aka.ms/csgate) para solicitar acesso ao análise de texto para visualização pública de integridade. Você não será cobrado por Análise de Texto para uso de integridade. 

| Elemento | Valores válidos | Necessário? | Uso |
|---------|--------------|-----------|-------|
|`id` |O tipo de dados é a cadeia de caracteres, mas na prática as IDs do documento tendem a ser números inteiros. | Obrigatório | O sistema usa as IDs que você fornece para estruturar o resultado. |
|`text` | Texto bruto não estruturado, até 5.120 caracteres. | Obrigatório | Observe que somente o texto em inglês tem suporte no momento. |
|`language` | Código [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) de dois caracteres para um [idioma compatível](../language-support.md) | Obrigatório | Somente tem `en` suporte no momento. |

Veja a seguir um exemplo de uma solicitação de API para o Análise de Texto para pontos de extremidade de integridade. 

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

---

>[!TIP]
> Consulte o artigo [dados e limites de taxa](../concepts/data-limits.md) para obter informações sobre as taxas e os limites de tamanho para enviar dados para o API de análise de texto.


## <a name="set-up-a-request"></a>Configurar uma solicitação 

No postmaster (ou outra ferramenta de teste de API Web), adicione o ponto de extremidade para o recurso que você deseja usar. Use a tabela a seguir para localizar o formato de ponto de extremidade apropriado e substituir `<your-text-analytics-resource>` pelo ponto de extremidade do recurso. Por exemplo:

`https://my-resource.cognitiveservices.azure.com/text/analytics/v3.0/languages`

#### <a name="synchronous"></a>[Síncrono](#tab/synchronous)

### <a name="endpoints-for-sending-synchronous-requests"></a>Pontos de extremidade para enviar solicitações síncronas

| Recurso | Tipo de solicitação | Pontos de extremidade do recurso |
|--|--|--|
| Detecção de idioma | POST | `<your-text-analytics-resource>/text/analytics/v3.0/languages` |
| Análise de sentimento | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment` |
| Mineração de opiniões | POST | `<your-text-analytics-resource>/text/analytics/v3.0/sentiment?opinionMining=true` |
| Extração de frases-chave | POST | `<your-text-analytics-resource>/text/analytics/v3.0/keyPhrases` |
| Reconhecimento de entidade nomeada-geral | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/general` |
| Reconhecimento de entidade nomeada-PII | POST | `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii` |
| Reconhecimento de entidade nomeada-PHI | POST |  `<your-text-analytics-resource>/text/analytics/v3.0/entities/recognition/pii?domain=phi` |

#### <a name="asynchronous"></a>[Assíncronos](#tab/asynchronous)

### <a name="endpoints-for-sending-asynchronous-requests-to-the-analyze-endpoint"></a>Pontos de extremidade para envio de solicitações assíncronas para o `/analyze` Endpoint

| Recurso | Tipo de solicitação | Pontos de extremidade do recurso |
|--|--|--|
| Enviar trabalho de análise | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze` |
| Obter resultados e status da análise | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>` |

### <a name="endpoints-for-sending-asynchronous-requests-to-the-health-endpoint"></a>Pontos de extremidade para envio de solicitações assíncronas para o `/health` Endpoint

| Recurso | Tipo de solicitação | Pontos de extremidade do recurso |
|--|--|--|
| Enviar Análise de Texto para trabalho de integridade  | POST | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs` |
| Obter resultados e status do trabalho | GET | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |
| Cancelar trabalho | Delete (excluir) | `https://<your-text-analytics-resource>/text/analytics/v3.1-preview.4/entities/health/jobs/<Operation-Location>` |

--- 

Depois de ter seu ponto de extremidade, no postmaster (ou outra ferramenta de teste da API Web):

1. Escolha o tipo de solicitação para o recurso que você deseja usar.
2. Cole no ponto de extremidade da operação apropriada que você deseja da tabela acima.
3. Defina os três cabeçalhos de solicitação:

   + `Ocp-Apim-Subscription-Key`: sua chave de acesso, obtida do portal do Azure
   + `Content-Type`: aplicativo/json
   + `Accept`: aplicativo/json

    Se você estiver usando o postmaster, sua solicitação deverá ser semelhante à captura de tela a seguir, supondo um `/keyPhrases` ponto de extremidade.
    
    ![Captura de tela da solicitação com o ponto de extremidade e cabeçalhos](../media/postman-request-keyphrase-1.png)
    
4. Escolha **RAW** para o formato do **corpo**
    
    ![Captura de tela da solicitação com as configurações de corpo](../media/postman-request-body-raw.png)

5. Cole alguns documentos JSON em um formato válido. Use os exemplos na seção **formato de solicitação de API** acima e para obter mais informações e exemplos, consulte os tópicos abaixo:

      + [Detecção de idioma](text-analytics-how-to-language-detection.md)
      + [Extração de frases-chave](text-analytics-how-to-keyword-extraction.md)
      + [Análise de sentimento](text-analytics-how-to-sentiment-analysis.md)
      + [Reconhecimento de entidade](text-analytics-how-to-entity-linking.md)

## <a name="send-the-request"></a>Enviar a solicitação

Envie a solicitação de API. Se você fez a chamada para um ponto de extremidade síncrono, a resposta será exibida imediatamente, como um único documento JSON, com um item para cada ID de documento fornecida na solicitação.

Se você fez a chamada para os pontos assíncronos `/analyze` ou de `/health` extremidade, verifique se recebeu um código de resposta 202. Você precisará obter a resposta para exibir os resultados:

1. Na resposta da API, localize o `Operation-Location` no cabeçalho, que identifica o trabalho que você enviou para a API. 
2. Crie uma solicitação GET para o ponto de extremidade usado. consulte a [tabela acima](#set-up-a-request) para o formato do ponto de extremidade e examine a [documentação de referência da API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/AnalyzeStatus). Por exemplo:

    `https://my-resource.cognitiveservices.azure.com/text/analytics/v3.1-preview.4/analyze/jobs/<Operation-Location>`

3. Adicione o `Operation-Location` à solicitação.

4. A resposta será um único documento JSON, com um item para cada ID de documento fornecida na solicitação.

Observe que, para as operações ou assíncronas `/analyze` `/health` , os resultados da solicitação get na etapa 2 acima estão disponíveis por 24 horas a partir do momento em que o trabalho foi criado.  Esse tempo é indicado pelo `expirationDateTime` valor na resposta Get.  Após esse período de tempo, os resultados são limpos e não estão mais disponíveis para recuperação.    

## <a name="example-api-responses"></a>Exemplo de respostas de API
 
# <a name="synchronous"></a>[Síncrono](#tab/synchronous)

### <a name="example-responses-for-synchronous-operation"></a>Respostas de exemplo para a operação síncrona

As respostas do ponto de extremidade síncrono irão variar dependendo do ponto de extremidade usado. Consulte os artigos a seguir para obter respostas de exemplo.

+ [Detecção de idioma](text-analytics-how-to-language-detection.md#step-3-view-the-results)
+ [Extração de frases-chave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Análise de sentimento](text-analytics-how-to-sentiment-analysis.md#view-the-results)
+ [Reconhecimento de entidade](text-analytics-how-to-entity-linking.md#view-results)

# <a name="asynchronous"></a>[Assíncronos](#tab/asynchronous)

### <a name="example-responses-for-asynchronous-operations"></a>Respostas de exemplo para operações assíncronas

Se for bem-sucedido, a solicitação GET para o `/analyze` ponto de extremidade retornará um objeto que contém as tarefas atribuídas. Por exemplo, `keyPhraseExtractionTasks`. Essas tarefas contêm o objeto de resposta do recurso de Análise de Texto apropriado. Consulte os artigos a seguir para obter mais informações.

+ [Extração de frases-chave](text-analytics-how-to-keyword-extraction.md#step-3-view-results)
+ [Reconhecimento de entidade](text-analytics-how-to-entity-linking.md#view-results)
+ [Análise de Texto para integridade](text-analytics-for-health.md#hosted-asynchronous-web-api-response)

--- 

## <a name="see-also"></a>Confira também

* [Visão geral da Análise de Texto](../overview.md)
* [Perguntas frequentes (FAQ)](../text-analytics-resource-faq.md)</br>
* [Página de produto de Análise de Texto do Azure Machine Learning](//go.microsoft.com/fwlink/?LinkID=759712)
* [Como usar a biblioteca de clientes da Análise de Texto](../quickstarts/client-libraries-rest-api.md)
* [Novidades](../whats-new.md)
