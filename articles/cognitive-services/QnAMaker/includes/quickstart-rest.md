---
title: 'Início Rápido: Usar cURL e REST para gerenciar a base de dados de conhecimento – QnA Maker'
description: Este início rápido mostra como criar, publicar e consultar sua base de dados de conhecimento usando APIs REST.
ms.date: 1/22/2021
ms.topic: quickstart
ms.openlocfilehash: d4c832c4a96ee1419b9e2f0accc1b86e9394ee02
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2021
ms.locfileid: "98701273"
---
[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

* A versão atual do [cURL](https://curl.haxx.se/). Várias opções de linha de comando são usadas nos inícios rápidos, indicadas na [documentação do cURL](https://curl.haxx.se/docs/manpage.html).
* Você deve ter um [Recurso do QnA Maker](../How-To/set-up-qnamaker-service-azure.md) para usar a chave e o nome do recurso. Você inseriu o **Nome** do recurso durante a criação dele e a chave foi criada para você. O nome do recurso é usado como o subdomínio para seu ponto de extremidade. Para recuperar a chave e o nome do recurso, selecione **Início Rápido** para o recurso no portal do Azure. O nome do recurso é o primeiro subdomínio da URL do ponto de extremidade:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> Os exemplos de BASH a seguir usam o caractere de continuação de linha `\`. Se o console ou o terminal usar um caractere de continuação de linha diferente, use esse caractere.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

* A versão atual do [cURL](https://curl.haxx.se/). Várias opções de linha de comando são usadas nos inícios rápidos, indicadas na [documentação do cURL](https://curl.haxx.se/docs/manpage.html).
* Você deve ter um [Recurso do QnA Maker](../How-To/set-up-qnamaker-service-azure.md) para usar a chave e o nome do recurso. Você inseriu o **Nome** do recurso durante a criação dele e a chave foi criada para você. O nome do recurso é usado como o subdomínio para seu ponto de extremidade. Para recuperar a chave e o nome do recurso, selecione **Início Rápido** para o recurso no portal do Azure. O nome do recurso é o primeiro subdomínio da URL do ponto de extremidade:

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1`

> [!CAUTION]
> Os exemplos de BASH a seguir usam o caractere de continuação de linha `\`. Se o console ou o terminal usar um caractere de continuação de linha diferente, use esse caractere.

---

## <a name="create-a-knowledge-base"></a>Como criar uma base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Para criar uma base de dados de conhecimento com as APIs REST e o cURL, você precisa ter as seguintes informações:

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|
|JSON que descreve a base de dados de conhecimento|Parâmetro `-d`|[Exemplos](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) de JSON|
|Tamanho do JSON em bytes|Parâmetro `-h` para o cabeçalho `Content-Size`||

O comando cURL é executado em um shell BASH. Edite esse comando com os valores de nome de recurso, chave de recurso e JSON e o tamanho do JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A resposta de cURL do QnA Maker inclui o `operationId`, que é necessário para [obter o status da operação](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)


Para criar uma base de dados de conhecimento com as APIs REST e o cURL, você precisa ter as seguintes informações:

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|
|JSON que descreve a base de dados de conhecimento|Parâmetro `-d`|[Exemplos](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) de JSON|
|Tamanho do JSON em bytes|Parâmetro `-h` para o cabeçalho `Content-Size`||

O comando cURL é executado em um shell BASH. Edite esse comando com os valores de nome de recurso, chave de recurso e JSON e o tamanho do JSON.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A resposta de cURL do QnA Maker inclui o `operationId`, que é necessário para [obter o status da operação](#get-status-of-operation).

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```
---

## <a name="get-status-of-operation"></a>Obter o status da operação

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Quando você cria uma base de dados de conhecimento, como a operação é assíncrona, a resposta inclui informações para determinar o status.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|ID da operação|Roteamento de URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da operação.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A resposta de cURL inclui o status. Se o estado da operação for bem-sucedido, o `resourceLocation` incluirá a ID da base de dados de conhecimento.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)


Quando você cria uma base de dados de conhecimento, como a operação é assíncrona, a resposta inclui informações para determinar o status.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|ID da operação|Roteamento de URL|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da operação.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A resposta de cURL inclui o status. Se o estado da operação for bem-sucedido, o `resourceLocation` incluirá a ID da base de dados de conhecimento.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

---

## <a name="publish-knowledge-base"></a>Publicar base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Antes de consultar a base de dados de conhecimento, você precisa:
* Publicar base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime

Esta tarefa publica a base de dados de conhecimento. Obter a chave de ponto de extremidade do runtime é uma [tarefa separada](#get-published-knowledge-bases-runtime-endpoint-key).

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|
|ID da base de dados de conhecimento|Roteamento de URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da base de dados de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

O status da resposta é 204 sem resultados. Use o parâmetro de linha de comando `-v` para ver a saída detalhada do comando cURL. Isso incluirá o status HTTP.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Antes de consultar a base de dados de conhecimento, você precisa:
* Publicar base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime

Esta tarefa publica a base de dados de conhecimento. Obter a chave de ponto de extremidade do runtime é uma [tarefa separada](#get-published-knowledge-bases-runtime-endpoint-key).

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|
|ID da base de dados de conhecimento|Roteamento de URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da base de dados de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

O status da resposta é 204 sem resultados. Use o parâmetro de linha de comando `-v` para ver a saída detalhada do comando cURL. Isso incluirá o status HTTP.

---

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Obter a chave de ponto de extremidade do runtime da base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Antes de consultar a base de dados de conhecimento, você precisa:
* Publicar base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime

Esta tarefa obtém a chave de ponto de extremidade do runtime. Publicar a base de dados de conhecimento é uma [tarefa separada](#publish-knowledge-base).

A chave de ponto de extremidade do runtime é a mesma chave para todas as bases de dados de conhecimento que usam o recurso do QnA Maker.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso e a chave de recurso.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A resposta de cURL inclui as chaves de ponto de extremidade do runtime. Use apenas uma das chaves ao consultar para obter uma resposta da base de dados de conhecimento.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```
# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Antes de consultar a base de dados de conhecimento, você precisa:
* Publicar base de dados de conhecimento
* Obter a chave de ponto de extremidade do runtime

Esta tarefa obtém a chave de ponto de extremidade do runtime. Publicar a base de dados de conhecimento é uma [tarefa separada](#publish-knowledge-base).

A chave de ponto de extremidade do runtime é a mesma chave para todas as bases de dados de conhecimento que usam o recurso do QnA Maker.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso e a chave de recurso.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A resposta de cURL inclui as chaves de ponto de extremidade do runtime. Use apenas uma das chaves ao consultar para obter uma resposta da base de dados de conhecimento.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

---

## <a name="query-for-answer-from-published-knowledge-base"></a>Consultar resposta da base de dados de conhecimento publicada

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Obter uma resposta do conhecimento é realizado por meio de um runtime separado do gerenciamento da base de dados de conhecimento. Como é um runtime separado, você precisa autenticar-se com uma chave de runtime.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de runtime do QnA Maker|Parâmetro `-h` para o cabeçalho `Authorization`|A chave faz parte de uma cadeia de caracteres que inclui a palavra `Endpointkey `. Autenticar-se no serviço QnA Maker|
|ID da base de dados de conhecimento|Roteamento de URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON que descreve a consulta|Parâmetro `-d`|[Parâmetros do corpo da solicitação](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) e [exemplos](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) de JSON|
|Tamanho do JSON em bytes|Parâmetro `-h` para o cabeçalho `Content-Size`||

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da base de dados de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Uma resposta bem-sucedida inclui a resposta principal, juntamente com outras informações de que um aplicativo cliente, como um bot de chat, precisa para exibir uma resposta para o usuário.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Obter uma resposta do conhecimento é realizado por meio de um runtime separado do gerenciamento da base de dados de conhecimento. Como é um runtime separado, você precisa autenticar-se com uma chave de runtime.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|
|ID da base de dados de conhecimento|Roteamento de URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON que descreve a consulta|Parâmetro `-d`|[Parâmetros do corpo da solicitação](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) e [exemplos](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples) de JSON|
|Tamanho do JSON em bytes|Parâmetro `-h` para o cabeçalho `Content-Size`||

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da base de dados de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

Uma resposta bem-sucedida inclui a resposta principal, juntamente com outras informações de que um aplicativo cliente, como um bot de chat, precisa para exibir uma resposta para o usuário.


---

## <a name="delete-knowledge-base"></a>Excluir base de dados de conhecimento

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versão estável)](#tab/v1)

Quando você concluir a base de dados de conhecimento, exclua-a.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|
|ID da base de dados de conhecimento|Roteamento de URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da base de dados de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

O status da resposta é 204 sem resultados. Use o parâmetro de linha de comando `-v` para ver a saída detalhada do comando cURL. Isso incluirá o status HTTP.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerenciado (versão prévia)](#tab/v2)

Quando você concluir a base de dados de conhecimento, exclua-a.

|Informações|Configuração cURL|Finalidade|
|--|--|--|
|Nome de recursos do QnA Maker|URL|usado para construir a URL|
|Chave de recursos do QnA Maker|Parâmetro `-h` para o cabeçalho `Ocp-Apim-Subscription-Key`|Autenticar-se no serviço QnA Maker|
|ID da base de dados de conhecimento|Roteamento de URL|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

O comando cURL é executado em um shell BASH. Edite esse comando com o próprio nome de recurso, a chave de recurso e a ID da base de dados de conhecimento.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.1/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

O status da resposta é 204 sem resultados. Use o parâmetro de linha de comando `-v` para ver a saída detalhada do comando cURL. Isso incluirá o status HTTP.

---

## <a name="additional-resources"></a>Recursos adicionais

* Documentação de referência de [criação](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase)
* Documentação de referência do [Runtime](/rest/api/cognitiveservices/qnamaker4.0/runtime)
* [Scripts de BASH de exemplo que usam cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)