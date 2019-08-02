---
title: API REST (V4) - Python – QnA Maker
titleSuffix: Azure Cognitive Services
description: Obtenha informações e amostras de código em Python para começar a usar rapidamente a API de Tradução de Texto da Microsoft nos Serviços Cognitivos da Microsoft no Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 5add68902b23d41c9e3031839508418a81086e6b
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342183"
---
# <a name="how-to-use-the-qna-maker-rest-api-with-python"></a>Como usar a API REST de QnA Maker com Python
<a name="HOLTop"></a>

Este artigo mostra como usar a [API de QnA Maker da Microsoft](../Overview/overview.md)  com Python para fazer o seguinte.

- [Criar uma nova base de dados de conhecimento.](#Create)
- [Atualizar uma base de dados de conhecimento existente.](#Update)
- [Obter o status de uma solicitação para criar ou atualizar uma base de dados de conhecimento.](#Status)
- [Publicar em uma base de dados de conhecimento existente.](#Publish)
- [Substituir o conteúdo de uma base de dados de conhecimento existente.](#Replace)
- [Baixar o conteúdo de uma base de dados de conhecimento.](#GetQnA)
- [Obter respostas a uma pergunta usando uma base de dados de conhecimento.](#GetAnswers)
- [Obter informações sobre uma base de dados de conhecimento.](#GetKB)
- [Obter informações sobre todas as bases de dados de conhecimento pertencentes ao usuário especificado.](#GetKBsByUser)
- [Excluir uma base de dados de conhecimento.](#Delete)
- [Obter as chaves do ponto de extremidade atual.](#GetKeys)
- [Gerar novamente as chaves do ponto de extremidade atual.](#PutKeys)
- [Obter o conjunto atual de alterações de palavras que não consideram maiúsculas e minúsculas.](#GetAlterations)
- [Substituir o conjunto atual de alterações de palavras que não consideram maiúsculas e minúsculas.](#PutAlterations)

[!INCLUDE [Code is available in Azure-Samples GitHub repo](../../../../includes/cognitive-services-qnamaker-python-repo-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Python 3.x](https://www.python.org/downloads/) para executar esse código.

É necessário ter uma [conta de API de Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com a **API de QnA Maker da Microsoft**. Você precisará de uma chave de assinatura paga do seu [painel do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

<a name="Create"></a>

## <a name="create-knowledge-base"></a>Criar base de dados de conhecimento

O código a seguir cria uma nova base de dados de conhecimento usando o método [Criar](https://go.microsoft.com/fwlink/?linkid=2092179).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/create'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def create_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()
# /knowledgebases/create returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read()


def check_status(path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read()


req = {
    "name": "QnA Maker FAQ",
    "qnaList": [
      {
          "id": 0,
          "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
          "source": "Custom Editorial",
          "questions": [
              "How do I programmatically update my Knowledge Base?"
          ],
          "metadata": [
              {
                  "name": "category",
                  "value": "api"
              }
          ]
      }
    ],
    "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
    ],
    "files": []
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
operation, result = create_kb(path, content)
print(pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status(path)
    print(pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep(int(wait))
    else:
        done = True
```

**Criar a resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Running",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:30Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:52:30Z",
  "lastActionTimestamp": "2018-04-13T01:52:46Z",
  "resourceLocation": "/knowledgebases/b0288f33-27b9-4258-a304-8b9f63427dad",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "e88b5b23-e9ab-47fe-87dd-3affc2fb10f3"
}
```

[Voltar ao início](#HOLTop)

<a name="Update"></a>

## <a name="update-knowledge-base"></a>Atualizar a base de dados de conhecimento

O código a seguir atualiza uma base de dados de conhecimento existente usando o método [Atualizar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def update_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PATCH", path, content, headers)
    response = conn.getresponse()
# PATCH /knowledgebases returns an HTTP header named Location that contains a URL
# to check the status of the operation to create the knowledgebase.
    return response.getheader('Location'), response.read()


def check_status(path):
    print('Calling ' + host + path + '.')
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, None, headers)
    response = conn.getresponse()
# If the operation is not finished, /operations returns an HTTP header named Retry-After
# that contains the number of seconds to wait before we query the operation again.
    return response.getheader('Retry-After'), response.read()


req = {
    'add': {
        'qnaList': [
            {
                'id': 1,
                'answer': 'You can change the default message if you use the QnAMakerDialog. See this for details: https://docs.botframework.com/azure-bot-service/templates/qnamaker/#navtitle',
                'source': 'Custom Editorial',
                'questions': [
                    'How can I change the default message from QnA Maker?'
                ],
                'metadata': []
            }
        ],
        'urls': [
            'https://docs.microsoft.com/azure/cognitive-services/Emotion/FAQ'
        ]
    },
    'update': {
        'name': 'New KB Name'
    },
    'delete': {
        'ids': [
            0
        ]
    }
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
operation, result = update_kb(path, content)
print(pretty_print(result))

done = False
while False == done:
    path = service + operation
    wait, status = check_status(path)
    print(pretty_print(status))

# Convert the JSON response into an object and get the value of the operationState field.
    state = json.loads(status)['operationState']
# If the operation isn't finished, wait and query again.
    if state == 'Running' or state == 'NotStarted':
        print('Waiting ' + wait + ' seconds...')
        time.sleep(int(wait))
    else:
        done = True
```

**Atualizar resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:48Z",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
...
{
  "operationState": "Succeeded",
  "createdTimestamp": "2018-04-13T01:49:48Z",
  "lastActionTimestamp": "2018-04-13T01:49:50Z",
  "resourceLocation": "/knowledgebases/140a46f3-b248-4f1b-9349-614bfd6e5563",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "operationId": "5156f64e-e31d-4638-ad7c-a2bdd7f41658"
}
Press any key to continue.
```

[Voltar ao início](#HOLTop)

<a name="Status"></a>

## <a name="get-request-status"></a>Obter status da solicitação

É possível chamar o método [Operação](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/operations/getdetails) para verificar o status de uma solicitação para criar ou atualizar uma base de dados de conhecimento. Para ver como esse método é utilizado, confira o código de exemplo do método [Create](#Create) ou [Update](#Update).

[Voltar ao início](#HOLTop)

<a name="Publish"></a>

## <a name="publish-knowledge-base"></a>Publicar base de dados de conhecimento

O código a seguir publica uma base de dados de conhecimento existente usando o método [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def publish_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + kb
result = publish_kb(path, '')
print(pretty_print(result))
```

**Publicar resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "result": "Success."
}
```

[Voltar ao início](#HOLTop)

<a name="Replace"></a>

## <a name="replace-knowledge-base"></a>Substituir base de dados de conhecimento

O código a seguir substitui o conteúdo da base de dados de conhecimento especificada usando o método [Substituir](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def replace_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PUT", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


req = {
    'qnaList': [
      {
          'id': 0,
          'answer': 'You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update',
          'source': 'Custom Editorial',
          'questions': [
              'How do I programmatically update my Knowledge Base?'
          ],
          'metadata': [
              {
                  'name': 'category',
                  'value': 'api'
              }
          ]
      }
    ]
}

path = service + method + kb
# Convert the request to a string.
content = json.dumps(req)
result = replace_kb(path, content)
print(pretty_print(result))
```

**Substituir resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
    "result": "Success."
}
```

[Voltar ao início](#HOLTop)

<a name="GetQnA"></a>

## <a name="download-the-contents-of-a-knowledge-base"></a>Baixar o conteúdo de uma base de dados de conhecimento

O código a seguir baixa o conteúdo da base de dados de conhecimento especificada usando o método [Baixar base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

# Replace this with "test" or "prod".
env = 'test'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/{0}/{1}/qna/'.format(kb, env)


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_qna(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_qna(path)
print(pretty_print(result))
```

**Baixar resposta da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "qnaDocuments": [
    {
      "id": 1,
      "answer": "You can use our REST APIs to manage your Knowledge Base. See here for details: https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update",
      "source": "Custom Editorial",
      "questions": [
        "How do I programmatically update my Knowledge Base?"
      ],
      "metadata": [
        {
          "name": "category",
          "value": "api"
        }
      ]
    },
    {
      "id": 2,
      "answer": "QnA Maker provides an FAQ data source that you can query from your bot or application. Although developers will find this useful, content owners will especially benefit from this tool. QnA Maker is a completely no-code way of managing the content that powers your bot or application.",
      "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
      "questions": [
        "Who is the target audience for the QnA Maker tool?"
      ],
      "metadata": []
    },
...
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="GetAnswers"></a>

## <a name="get-answers-to-a-question-using-a-knowledge-base"></a>Obter respostas a uma pergunta usando uma base de dados de conhecimento

O código a seguir obtém respostas para uma pergunta usando a base de dados de conhecimento especificada por meio do método **Gerar respostas**.

1. Crie um projeto Python em seu IDE favorito.
1. Adicione o código fornecido abaixo.
1. Substitua o valor `host` pelo nome do site da sua assinatura do QnA Maker. Para obter mais informações, consulte [Criar um serviço do QnA Maker](../How-To/set-up-qnamaker-service-azure.md).
1. Substitua o valor `endpoint_key` por uma chave de ponto de extremidade válida para a sua assinatura. Observe que não será igual à sua chave de assinatura. É possível obter as chaves de ponto de extremidade usando o método [Obter chaves do ponto de extremidade](#GetKeys).
1. Substitua o valor `kb` pela ID da base de dados de conhecimento que você quer consultar. Observe que essa base de dados de conhecimento já deve ter sido publicada usando o método [Publicar](#Publish).
1. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# NOTE: Replace this with a valid host name.
host = "ENTER HOST HERE"

# NOTE: Replace this with a valid endpoint key.
# This is not your subscription key.
# To get your endpoint keys, call the GET /endpointkeys method.
endpoint_key = "ENTER KEY HERE"

# NOTE: Replace this with a valid knowledge base ID.
# Make sure you have published the knowledge base with the
# POST /knowledgebases/{knowledge base ID} method.
kb = "ENTER KB ID HERE"

method = "/qnamaker/knowledgebases/" + kb + "/generateAnswer"

question = {
    'question': 'Is the QnA Maker Service free?',
    'top': 3
}


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_answers(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Authorization': 'EndpointKey ' + endpoint_key,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("POST", path, content, headers)
    response = conn.getresponse()
    return response.read()


# Convert the request to a string.
content = json.dumps(question)
result = get_answers(method, content)
print(pretty_print(result))
```

**Resposta de obter respostas**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "answers": [
    {
      "questions": [
        "Can I use BitLocker with the Volume Shadow Copy Service?"
      ],
      "answer": "Yes. However, shadow copies made prior to enabling BitLocker will be automatically deleted when BitLocker is enabled on software-encrypted drives. If you are using a hardware encrypted drive, the shadow copies are retained.",
      "score": 17.3,
      "id": 62,
      "source": "https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-frequently-asked-questions",
      "metadata": []
    },
...
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="GetKB"></a>

## <a name="get-information-about-a-knowledge-base"></a>Obter informações sobre uma base de dados de conhecimento

O código a seguir obtém informações sobre a base de dados de conhecimento especificada usando o método [Obter detalhes da base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/getdetails).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_kb(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method + kb
result = get_kb(path)
print(pretty_print(result))
```

**Obter resposta dos detalhes da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
  "hostName": "https://qna-docs.azurewebsites.net",
  "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
  "lastChangedTimestamp": "2018-04-12T22:58:01Z",
  "name": "QnA Maker FAQ",
  "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
  "urls": [
    "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
    "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
  ],
  "sources": [
    "Custom Editorial"
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="GetKBsByUser"></a>

## <a name="get-all-knowledge-bases-for-a-user"></a>Obter todas as bases de dados de conhecimento para um usuário

O código a seguir obtém informações sobre todas as bases de dados de conhecimento para um usuário especificado usando o método [Obter bases de dados de conhecimento para o usuário](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/listall).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_kbs(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_kbs(path)
print(pretty_print(result))
```

**Obter bases de dados de conhecimento para resposta do usuário**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "knowledgebases": [
    {
      "id": "081c32a7-bd05-4982-9d74-07ac736df0ac",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T11:51:58Z",
      "lastChangedTimestamp": "2018-04-12T11:51:58Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [],
      "sources": []
    },
    {
      "id": "140a46f3-b248-4f1b-9349-614bfd6e5563",
      "hostName": "https://qna-docs.azurewebsites.net",
      "lastAccessedTimestamp": "2018-04-12T22:58:01Z",
      "lastChangedTimestamp": "2018-04-12T22:58:01Z",
      "name": "QnA Maker FAQ",
      "userId": "2280ef5917bb4ebfa1aae41fb1cebb4a",
      "urls": [
        "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
        "https://docs.microsoft.com/bot-framework/resources-bot-framework-faq"
      ],
      "sources": [
        "Custom Editorial"
      ]
    },
...
  ]
}
Press any key to continue.
```

[Voltar ao início](#HOLTop)

<a name="Delete"></a>

## <a name="delete-a-knowledge-base"></a>Excluir uma base de dados de conhecimento

O código a seguir exclui a base de dados de conhecimento especificada usando o método [Excluir base de dados de conhecimento](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/delete).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with a valid knowledge base ID.
kb = 'ENTER ID HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/knowledgebases/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def delete_kb(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("DELETE", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + kb
result = delete_kb(path, '')
print(pretty_print(result))
```

**Resposta de excluir da base de dados de conhecimento**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "result": "Success."
}
```

[Voltar ao início](#HOLTop)

<a name="GetKeys"></a>

## <a name="get-endpoint-keys"></a>Obter chaves de ponto de extremidade

O código a seguir obtém as chaves do ponto de extremidade atuais usando o método [Obter chaves de ponto de extremidade](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/getkeys).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_keys(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_keys(path)
print(pretty_print(result))
```

**Resposta de obter chaves de ponto de extremidade**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Voltar ao início](#HOLTop)

<a name="PutKeys"></a>

## <a name="refresh-endpoint-keys"></a>Atualizar chaves do ponto de extremidade

O código a seguir gera novamente as chaves do ponto de extremidade atuais usando o método [Atualizar chaves do ponto de extremidade](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/endpointkeys/refreshkeys).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

# Replace this with "PrimaryKey" or "SecondaryKey."
key_type = "PrimaryKey"

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/endpointkeys/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def refresh_keys(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PATCH", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


path = service + method + key_type
result = refresh_keys(path, '')
print(pretty_print(result))
```

**Resposta de atualizar chaves do ponto de extremidade**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "primaryEndpointKey": "ac110bdc-34b7-4b1c-b9cd-b05f9a6001f3",
  "secondaryEndpointKey": "1b4ed14e-614f-444a-9f3d-9347f45a9206"
}
```

[Voltar ao início](#HOLTop)

<a name="GetAlterations"></a>

## <a name="get-word-alterations"></a>Obter alterações de palavras

O código a seguir obtém as alterações de palavras atuais usando o método [Baixar alterações](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/get).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def get_alterations(path):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("GET", path, '', headers)
    response = conn.getresponse()
    return response.read()


path = service + method
result = get_alterations(path)
print(pretty_print(result))
```

**Resposta de obter alterações de palavras**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "wordAlterations": [
    {
      "alterations": [
        "botframework",
        "bot frame work"
      ]
    }
  ]
}
```

[Voltar ao início](#HOLTop)

<a name="PutAlterations"></a>

## <a name="replace-word-alterations"></a>Substituir alterações de palavras

O código a seguir substitui as alterações de palavras atuais usando o método [Substituir alterações](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/alterations/replace).

1. Crie um projeto Python em seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

import http.client
import urllib.parse
import json
import time

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace this with a valid subscription key.
subscriptionKey = 'ENTER KEY HERE'

host = 'westus.api.cognitive.microsoft.com'
service = '/qnamaker/v4.0'
method = '/alterations/'


def pretty_print(content):
    # Note: We convert content to and from an object so we can pretty-print it.
    return json.dumps(json.loads(content), indent=4)


def put_alterations(path, content):
    print('Calling ' + host + path + '.')
    headers = {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Content-Type': 'application/json',
        'Content-Length': len(content)
    }
    conn = http.client.HTTPSConnection(host)
    conn.request("PUT", path, content, headers)
    response = conn.getresponse()

    if response.status == 204:
        return json.dumps({'result': 'Success.'})
    else:
        return response.read()


req = {
    'wordAlterations': [
      {
          'alterations': [
              'botframework',
              'bot frame work'
          ]
      }
    ]
}

path = service + method
# Convert the request to a string.
content = json.dumps(req)
result = put_alterations(path, content)
print(pretty_print(result))
```

**Resposta de substituir alterações de palavras**

Uma resposta com êxito é retornada em JSON, conforme mostrado no seguinte exemplo: 

```json
{
  "result": "Success."
}
```

[Voltar ao início](#HOLTop)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência da API REST do QnA Maker (V4)](https://go.microsoft.com/fwlink/?linkid=2092179)

## <a name="see-also"></a>Consulte também 

[Visão geral do QnA Maker](../Overview/overview.md)
