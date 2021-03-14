---
title: API de áudio longo-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como a API de áudio longa é projetada para a síntese assíncrona de texto em fala de longa forma.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: 65c0d80394317c2b2bfbf621d3cc2ad0c2e3448a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618399"
---
# <a name="long-audio-api"></a>API de áudio longo

A API de áudio de longa duração é projetada para síntese assíncrona de texto em fala de longa forma (por exemplo: livros de áudio, artigos de notícias e documentos). Essa API não retorna áudio sintetizado em tempo real, em vez disso, a expectativa é que você pesquise as respostas e consuma as saídas conforme elas são disponibilizadas pelo serviço. Ao contrário da API de texto para fala usada pelo SDK de fala, a API de áudio de longa duração pode criar áudio sintetizado por mais de 10 minutos, tornando-o ideal para Publicadores e plataformas de conteúdo de áudio para criar conteúdo de áudio longo, como livros de áudio em um lote.

Benefícios adicionais da API de áudio longa:

* A fala sintetizada retornada pelo serviço usa as melhores vozes neurais.
* Não é necessário implantar um ponto de extremidade de voz, pois ele sintetiza vozes em nenhum modo de lote em tempo real.

> [!NOTE]
> A API de áudio longa agora dá suporte a [vozes neurais públicas](./language-support.md#neural-voices) e a [vozes neurais personalizadas](./how-to-custom-voice.md#custom-neural-voices).

## <a name="workflow"></a>Fluxo de trabalho

Normalmente, ao usar a API de áudio longo, você enviará um arquivo de texto ou arquivos a serem sintetizados, sondando o status e, se o status for bem-sucedido, você poderá baixar a saída de áudio.

Este diagrama fornece uma visão geral de alto nível do fluxo de trabalho.

![Diagrama de fluxo de trabalho da API de áudio longo](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Preparar conteúdo para síntese

Ao preparar seu arquivo de texto, verifique se:

* É texto sem formatação (. txt) ou o texto SSML (. txt)
* É codificado como [UTF-8 com marca de ordem de byte (bom)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom)
* É um arquivo único, não um zip
* Contém mais de 400 caracteres para texto sem formatação ou 400 [caracteres cobráveis](./text-to-speech.md#pricing-note) para texto de SSML e menos de 10.000 parágrafos
  * Para texto sem formatação, cada parágrafo é separado ao pressionar o exemplo **Inserir/retornar** -exibir [entrada de texto sem formatação](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt)
  * Para o texto SSML, cada parte da SSML é considerada um parágrafo. As partes de SSML devem ser separadas por parágrafos diferentes – exibir [exemplo de entrada de texto de SSML](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt)

## <a name="sample-code"></a>Código de exemplo
O restante desta página se concentrará no Python, mas o código de exemplo para a API de áudio de longa disponibilidade está disponível no GitHub para as seguintes linguagens de programação:

* [Código de exemplo: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Código de exemplo: C #](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Código de exemplo: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="python-example"></a>Exemplo de Python

Esta seção contém exemplos do Python que mostram o uso básico da API de áudio de longa duração. Crie um novo projeto Python usando seu IDE ou editor favorito. Em seguida, copie esse trecho de código em um arquivo chamado `long_audio_synthesis_client.py` .

```python
import json
import ntpath
import requests
```

Essas bibliotecas são usadas para construir a solicitação HTTP e chamar a API REST de síntese de conversão de áudio de texto em fala.

### <a name="get-a-list-of-supported-voices"></a>Obter uma lista de vozes com suporte

Para obter uma lista de vozes com suporte, envie uma solicitação GET para `https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/voices` .


Esse código permite obter uma lista completa de vozes para uma região/um ponto de extremidade específico que você pode usar.
```python
def get_voices():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/voices'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print(response.text)

get_voices()
```

Substitua os seguintes valores:

* Substitua `<your_key>` por sua chave de assinatura do Serviço de Fala. Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substituir `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus` ). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).

Você verá uma saída parecida com esta:

```console
{
  "values": [
    {
      "locale": "en-US",
      "voiceName": "en-US-AriaNeural",
      "description": "",
      "gender": "Female",
      "createdDateTime": "2020-05-21T05:57:39.123Z",
      "properties": {
        "publicAvailable": true
      }
    },
    {
      "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141"
      "locale": "en-US",
      "voiceName": "my custom neural voice",
      "description": "",
      "gender": "Male",
      "createdDateTime": "2020-05-21T05:25:40.243Z",
      "properties": {
        "publicAvailable": false
      }
    }
  ]
}
```

Se **Properties. publicAvailable** for **true**, a voz será uma voz neural pública. Caso contrário, é uma voz neural personalizada.

### <a name="convert-text-to-speech"></a>Converter texto em fala

Prepare um arquivo de texto de entrada, em texto sem formatação ou de SSML, e adicione o seguinte código a `long_audio_synthesis_client.py` :

> [!NOTE]
> `concatenateResult` é um parâmetro opcional. Se esse parâmetro não estiver definido, as saídas de áudio serão geradas por parágrafo. Você também pode concatenar os áudios em 1 saída definindo o parâmetro. 
> `outputFormat` também é opcional. Por padrão, a saída de áudio é definida como riff-16kHz-16 bits-mono-PCM. Para obter mais informações sobre formatos de saída de áudio com suporte, consulte [formatos de saída de áudio](#audio-output-formats).

```python
def submit_synthesis():
    region = '<region>'
    key = '<your_key>'
    input_file_path = '<input_file_path>'
    locale = '<locale>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis'.format(region)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    voice_identities = [
        {
            'voicename': '<voice_name>'
        }
    ]

    payload = {
        'displayname': 'long audio synthesis sample',
        'description': 'sample description',
        'locale': locale,
        'voices': json.dumps(voice_identities),
        'outputformat': 'riff-16khz-16bit-mono-pcm',
        'concatenateresult': True,
    }

    filename = ntpath.basename(input_file_path)
    files = {
        'script': (filename, open(input_file_path, 'rb'), 'text/plain')
    }

    response = requests.post(url, payload, headers=header, files=files)
    print('response.status_code: %d' % response.status_code)
    print(response.headers['Location'])

submit_synthesis()
```

Substitua os seguintes valores:

* Substitua `<your_key>` por sua chave de assinatura do Serviço de Fala. Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substituir `<region>` pela região em que o recurso de fala foi criado (por exemplo: `eastus` ou `westus` ). Essas informações estão disponíveis na guia **visão geral** do recurso na [portal do Azure](https://aka.ms/azureportal).
* Substitua `<input_file_path>` pelo caminho para o arquivo de texto que você preparou para conversão de texto em fala.
* Substituir `<locale>` pela localidade de saída desejada. Para obter mais informações, consulte [suporte a idiomas](language-support.md#neural-voices).

Use uma das vozes retornadas pela chamada anterior ao ponto de `/voices` extremidade.

* Se você estiver usando uma voz neural pública, substitua `<voice_name>` pela voz de saída desejada.
* Para usar uma voz neural personalizada, substitua `voice_identities` a variável pelo seguinte e substitua `<voice_id>` pelo `id` da sua voz neural personalizada.
```Python
voice_identities = [
    {
        'id': '<voice_id>'
    }
]
```

Você verá uma saída parecida com esta:

```console
response.status_code: 202
https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/<guid>
```

> [!NOTE]
> Se você tiver mais de 1 arquivo de entrada, será necessário enviar várias solicitações. Há algumas limitações que precisam estar cientes.
> * O cliente tem permissão para enviar até **5** solicitações ao servidor por segundo para cada conta de assinatura do Azure. Se ele exceder a limitação, o cliente receberá um código de erro 429 (muitas solicitações). Reduza o valor da solicitação por segundo.
> * O servidor tem permissão para executar e enfileirar até **120** solicitações para cada conta de assinatura do Azure. Se ele exceder a limitação, o servidor retornará um código de erro 429 (número excessivo de solicitações). Aguarde e evite enviar uma nova solicitação até que algumas solicitações sejam concluídas.

A URL na saída pode ser usada para obter o status da solicitação.

### <a name="get-information-of-a-submitted-request"></a>Obter informações de uma solicitação enviada

Para obter o status de uma solicitação de síntese enviada, basta enviar uma solicitação GET para a URL retornada pela etapa anterior.
```Python

def get_synthesis():
    url = '<url>'
    key = '<your_key>'
    header = {
        'Ocp-Apim-Subscription-Key': key
    }
    response = requests.get(url, headers=header)
    print(response.text)

get_synthesis()
```
A saída será assim:
```console
response.status_code: 200
{
  "models": [
    {
      "voiceName": "en-US-AriaNeural"
    }
  ],
  "properties": {
    "outputFormat": "riff-16khz-16bit-mono-pcm",
    "concatenateResult": false,
    "totalDuration": "PT5M57.252S",
    "billableCharacterCount": 3048
  },
  "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
  "lastActionDateTime": "2021-01-14T11:12:27.240Z",
  "status": "Succeeded",
  "createdDateTime": "2021-01-14T11:11:02.557Z",
  "locale": "en-US",
  "displayName": "long audio synthesis sample",
  "description": "sample description"
}
```

Em `status` propriedade, você pode ler o status dessa solicitação. A solicitação será iniciada com `NotStarted` status, então altere para `Running` e, finalmente, se torna `Succeeded` ou `Failed` . Você pode usar um loop para sondar essa API até que o status se torne `Succeeded` .

### <a name="download-audio-result"></a>Baixar resultado de áudio

Depois que uma solicitação de síntese for realizada com sucesso, você poderá baixar o resultado de áudio chamando GET `/files` API.

```python
def get_files():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/files'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_files()
```
Substituir `<request_id>` pela ID da solicitação que você deseja baixar o resultado. Ele pode ser encontrado na resposta da etapa anterior.

A saída será assim:
```console
response.status_code: 200
{
  "values": [
    {
      "name": "2779f2aa-4e21-4d13-8afb-6b3104d6661a.txt",
      "kind": "LongAudioSynthesisScript",
      "properties": {
        "size": 4200
      },
      "createdDateTime": "2021-01-14T11:11:02.410Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/input.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "name": "voicesynthesis_waves.zip",
      "kind": "LongAudioSynthesisResult",
      "properties": {
        "size": 9290000
      },
      "createdDateTime": "2021-01-14T11:12:27.226Z",
      "links": {
        "contentUrl": "https://customvoice-usw.blob.core.windows.net/artifacts/voicesynthesis_waves.zip?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ]
}
```
A saída contém informações de 2 arquivos. Aquela com `"kind": "LongAudioSynthesisScript"` é o script de entrada enviado. O outro com `"kind": "LongAudioSynthesisResult"` o resultado dessa solicitação.
O resultado é zip que contém os arquivos de saída de áudio gerados, juntamente com uma cópia do texto de entrada.

Ambos os arquivos podem ser baixados da URL em sua `links.contentUrl` propriedade.

### <a name="get-all-synthesis-requests"></a>Obter todas as solicitações de síntese

Você pode obter uma lista de todas as solicitações enviadas com o seguinte código:

```python
def get_synthesis():
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/'.format(region)    
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.get(url, headers=header)
    print('response.status_code: %d' % response.status_code)
    print(response.text)

get_synthesis()
```

A saída será semelhante a:
```console
response.status_code: 200
{
  "values": [
    {
      "models": [
        {
          "id": "8fafd8cd-5f95-4a27-a0ce-59260f873141",
          "voiceName": "my custom neural voice"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT1S",
        "billableCharacterCount": 5
      },
      "id": "f9f0bb74-dfa5-423d-95e7-58a5e1479315",
      "lastActionDateTime": "2021-01-05T07:25:42.433Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-05T07:25:13.600Z",
      "locale": "en-US",
      "displayName": "Long Audio Synthesis",
      "description": "Long audio synthesis sample"
    },
    {
      "models": [
        {
          "voiceName": "en-US-AriaNeural"
        }
      ],
      "properties": {
        "outputFormat": "riff-16khz-16bit-mono-pcm",
        "concatenateResult": false,
        "totalDuration": "PT5M57.252S",
        "billableCharacterCount": 3048
      },
      "id": "eb3d7a81-ee3e-4e9a-b725-713383e71677",
      "lastActionDateTime": "2021-01-14T11:12:27.240Z",
      "status": "Succeeded",
      "createdDateTime": "2021-01-14T11:11:02.557Z",
      "locale": "en-US",
      "displayName": "long audio synthesis sample",
      "description": "sample description"
    }
  ]
}
```

`values` a propriedade contém uma lista de solicitações de síntese. A lista é paginada, com um tamanho máximo de página de 100. Se houver mais de 100 solicitações, uma `"@nextLink"` propriedade será fornecida para obter a próxima página da lista paginada.

```console
  "@nextLink": "https://<endpoint>/api/texttospeech/v3.0/longaudiosynthesis/?top=100&skip=100"
```

Você também pode personalizar o tamanho da página e ignorar o número fornecendo `skip` e `top` no parâmetro de URL.

### <a name="remove-previous-requests"></a>Remover solicitações anteriores

O serviço manterá até **20.000** solicitações para cada conta de assinatura do Azure. Se o valor da solicitação exceder essa limitação, remova as solicitações anteriores antes de fazer as novas. Se você não remover as solicitações existentes, receberá uma notificação de erro.

O código a seguir mostra como remover uma solicitação de síntese específica.
```python
def delete_synthesis():
    id = '<request_id>'
    region = '<region>'
    key = '<your_key>'
    url = 'https://{}.customvoice.api.speech.microsoft.com/api/texttospeech/v3.0/longaudiosynthesis/{}/'.format(region, id)
    header = {
        'Ocp-Apim-Subscription-Key': key
    }

    response = requests.delete(url, headers=header)
    print('response.status_code: %d' % response.status_code)
```

Se a solicitação for removida com êxito, o código de status de resposta será HTTP 204 (sem conteúdo).

```console
response.status_code: 204
```

> [!NOTE]
> Solicitações com um status de `NotStarted` ou `Running` não podem ser removidas ou excluídas.

O concluído `long_audio_synthesis_client.py` está disponível no [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py).

## <a name="http-status-codes"></a>Códigos de status HTTP

A tabela a seguir detalha os códigos de resposta HTTP e as mensagens da API REST.

| API | Código de status HTTP | Descrição | Solução |
|-----|------------------|-------------|----------|
| Criar | 400 | A síntese de voz não está habilitada nesta região. | Altere a chave de assinatura de fala com uma região com suporte. |
|        | 400 | Somente a assinatura de fala **padrão** para essa região é válida. | Altere a chave de assinatura de fala para o tipo de preço "padrão". |
|        | 400 | Excede o limite de solicitação de 20.000 para a conta do Azure. Remova algumas solicitações antes de enviar novas. | O servidor manterá até 20.000 solicitações para cada conta do Azure. Exclua algumas solicitações antes de enviar novas. |
|        | 400 | Este modelo não pode ser usado na síntese de voz: {ModelId}. | Certifique-se de que o estado de {ModelId} esteja correto. |
|        | 400 | A região da solicitação não corresponde à região do modelo: {ModelId}. | Certifique-se de que a região da {ModelId} corresponda à região da solicitação. |
|        | 400 | A síntese de voz só dá suporte ao arquivo de texto na codificação UTF-8 com o marcador de ordem de byte. | Verifique se os arquivos de entrada estão em codificação UTF-8 com o marcador de ordem de byte. |
|        | 400 | Somente entradas SSML válidas são permitidas na solicitação de síntese de voz. | Verifique se as expressões SSML de entrada estão corretas. |
|        | 400 | O nome de voz {voicename} não foi encontrado no arquivo de entrada. | O nome da voz SSML de entrada não está alinhado com a ID do modelo. |
|        | 400 | O número de parágrafos no arquivo de entrada deve ser menor que 10.000. | Verifique se o número de parágrafos no arquivo é menor que 10.000. |
|        | 400 | O arquivo de entrada deve ter mais de 400 caracteres. | Verifique se o arquivo de entrada excede 400 caracteres. |
|        | 404 | O modelo declarado na definição de síntese de voz não pode ser encontrado: {ModelId}. | Verifique se {ModelId} está correto. |
|        | 429 | Excede o limite de síntese de voz ativo. Aguarde até que algumas solicitações sejam concluídas. | O servidor tem permissão para executar e enfileirar até 120 solicitações para cada conta do Azure. Aguarde e evite enviar novas solicitações até que algumas solicitações sejam concluídas. |
| Todos       | 429 | Há muitas solicitações. | O cliente tem permissão para enviar até 5 solicitações ao servidor por segundo para cada conta do Azure. Reduza o valor da solicitação por segundo. |
| Excluir    | 400 | A tarefa de síntese de voz ainda está em uso. | Você só pode excluir solicitações **concluídas** ou **com falha**. |
| GetByID   | 404 | A entidade especificada não pode ser encontrada. | Verifique se a ID de síntese está correta. |

## <a name="regions-and-endpoints"></a>Regiões e endpoints

A API de áudio longa está disponível em várias regiões com pontos de extremidade exclusivos.

| Region | Ponto de extremidade |
|--------|----------|
| Leste dos EUA | `https://eastus.customvoice.api.speech.microsoft.com` |
| Centro da Índia | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Sudeste Asiático | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Sul do Reino Unido | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa Ocidental | `https://westeurope.customvoice.api.speech.microsoft.com` |

## <a name="audio-output-formats"></a>Formatos de saída de áudio

Damos suporte a formatos de saída de áudio flexíveis. Você pode gerar saídas de áudio por parágrafo ou concatenar as saídas de áudio em uma única saída definindo o parâmetro ' concatenateResult '. Os seguintes formatos de saída de áudio são suportados pela API de áudio longa:

> [!NOTE]
> O formato de áudio padrão é riff-16kHz-16 bits-mono-PCM.

* riff-8kHz-16 bits-mono-PCM
* riff-16kHz-16 bits-mono-PCM
* riff-24kHz-16 bits-mono-PCM
* riff-48kHz-16 bits-mono-PCM
* Audio-16kHz-32kbitrate-mono-mp3
* Audio-16kHz-64kbitrate-mono-mp3
* Audio-16kHz-128kbitrate-mono-mp3
* Audio-24kHz-48kbitrate-mono-mp3
* Audio-24kHz-96kbitrate-mono-mp3
* Audio-24kHz-160kbitrate-mono-mp3
