---
title: 'Início Rápido: converter texto em fala, Python – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Neste início rápido, você aprenderá a converter texto em Fala usando Python e a API REST de conversão de texto em Fala. O texto de exemplo incluído neste guia é estruturado como Linguagem de marcação de síntese de Fala (SSML). Isso permite que você escolha a voz e o idioma da resposta de Fala.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 60a7fe79de62e3b81e25c17c23ba39a08e8a061c
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604995"
---
# <a name="quickstart-convert-text-to-speech-using-python"></a>Início Rápido: converter texto em fala usando o Python

Neste início rápido, você aprenderá a converter texto em fala usando Python e a API REST de conversão de texto em fala. O corpo da solicitação neste guia é estruturado como [Linguagem de marcação de síntese de Fala (SSML)](speech-synthesis-markup.md), que permite escolher a voz e o idioma da resposta.

Este início rápido exige uma [Conta de Serviços Cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com um recurso dos Serviços de Fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](get-started.md) para obter uma chave de assinatura.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Python 2.7.x ou 3.x
* [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/download) ou seu editor de texto favorito
* Uma chave de assinatura do Azure para os Serviços de Fala

## <a name="create-a-project-and-import-required-modules"></a>Criar um projeto e importar os módulos necessários

Crie um novo projeto Python usando seu IDE ou editor favorito. Em seguida, copie esse trecho de código para seu projeto em um arquivo denominado `tts.py`.

```python
import os
import requests
import time
from xml.etree import ElementTree
```

> [!NOTE]
> Se você nunca usou esses módulos, você precisará instalá-los antes de executar o programa. Para instalar esses pacotes, execute: `pip install requests`.

Esses módulos são usados para gravar a resposta de fala em um arquivo com um registro de data e hora, construir a solicitação de HTTP e chamar a API de conversão de texto em fala.

## <a name="set-the-subscription-key-and-create-a-prompt-for-tts"></a>Defina a chave de assinatura e crie um prompt para o TTS

Nas próximas seções, você criará métodos para manipular a autorização, chamar a API de conversão de texto em fala e validar a resposta. Vamos começar adicionando algum código que garante que esse exemplo funcionará com o Python 2.7.xe 3.x.

```python
try:
    input = raw_input
except NameError:
    pass
```

Em seguida, vamos criar uma classe. É onde colocaremos nossos métodos para troca de tokens e chamaremos a API text-to-speech.

```python
class TextToSpeech(object):
    def __init__(self, subscription_key):
        self.subscription_key = subscription_key
        self.tts = input("What would you like to convert to speech: ")
        self.timestr = time.strftime("%Y%m%d-%H%M")
        self.access_token = None
```

A `subscription_key` é sua chave exclusiva do portal do Azure. `tts` solicita que o usuário insira o texto que será convertido em fala. Essa entrada é uma cadeia de caracteres literal, para que os caracteres não devem ser escapados. Por fim, `timestr` obtém a hora atual, que usaremos para nomear o arquivo.

## <a name="get-an-access-token"></a>Obter um token de acesso

A API REST de conversão de texto em fala requer um token de acesso para autenticação. Para obter um token de acesso, é necessária uma troca. Este exemplo troca sua chave de assinatura dos Serviços de Fala por um token de acesso usando o ponto de extremidade `issueToken`.

Este exemplo pressupõe que a sua assinatura dos Serviços de Fala esteja na região Oeste dos EUA. Se você estiver usando uma região diferente, atualize o valor para `fetch_token_url`. Para uma lista completa, consulte [Regiões](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis).

Copie este código para a classe `TextToSpeech`:

```python
def get_token(self):
    fetch_token_url = "https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken"
    headers = {
        'Ocp-Apim-Subscription-Key': self.subscription_key
    }
    response = requests.post(fetch_token_url, headers=headers)
    self.access_token = str(response.text)
```

> [!NOTE]
> Para obter mais informações sobre autenticação, consulte [Autenticar com um token de acesso](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token).

## <a name="make-a-request-and-save-the-response"></a>Faça uma solicitação e salve a resposta

Aqui você vai criar a solicitação e salvar a resposta de fala. Primeiro, você precisa definir `base_url` e `path`. Este exemplo supõe que você esteja usando o endpoint do West US. Se o seu recurso estiver registrado em uma região diferente, atualize o `base_url`. Para obter mais informações, veja [regiões dos Serviços de Fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#text-to-speech).

Em seguida, você precisa adicionar cabeçalhos obrigatórios para a solicitação. Certifique-se de atualizar `User-Agent` com o nome do seu recurso (localizado no portal do Azure) e defina `X-Microsoft-OutputFormat` como sua saída de áudio preferida. Para obter uma lista completa dos formatos de saída, consulte [Saídas de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis).

Em seguida, construa o corpo da solicitação usando a Linguagem de marcação de síntese de Fala (SSML). Esta amostra define a estrutura e usa a entrada `tts` criada anteriormente.

>[!NOTE]
> Este exemplo usa a fonte de voz `ZiraRUS`. Para obter uma lista completa das vozes / idiomas fornecidos pela Microsoft, consulte [Suporte ao idioma](language-support.md).
> Se você estiver interessado em criar uma voz exclusiva e reconhecível para sua marca, consulte [Criando fontes de voz personalizadas](how-to-customize-voice-font.md).

Finalmente, você fará uma solicitação ao serviço. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta de fala será gravada em um arquivo com registro de data e hora.

Copie este código para a classe `TextToSpeech`:

```python
def save_audio(self):
    base_url = 'https://westus.tts.speech.microsoft.com/'
    path = 'cognitiveservices/v1'
    constructed_url = base_url + path
    headers = {
        'Authorization': 'Bearer ' + self.access_token,
        'Content-Type': 'application/ssml+xml',
        'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
        'User-Agent': 'YOUR_RESOURCE_NAME'
    }
    xml_body = ElementTree.Element('speak', version='1.0')
    xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
    voice = ElementTree.SubElement(xml_body, 'voice')
    voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
    voice.set(
        'name', 'Microsoft Server Speech Text to Speech Voice (en-US, Guy24KRUS)')
    voice.text = self.tts
    body = ElementTree.tostring(xml_body)

    response = requests.post(constructed_url, headers=headers, data=body)
    if response.status_code == 200:
        with open('sample-' + self.timestr + '.wav', 'wb') as audio:
            audio.write(response.content)
            print("\nStatus code: " + str(response.status_code) +
                  "\nYour TTS is ready for playback.\n")
    else:
        print("\nStatus code: " + str(response.status_code) +
              "\nSomething went wrong. Check your subscription key and headers.\n")
```

## <a name="put-it-all-together"></a>Colocar tudo isso junto

Você está quase lá. Se a solicitação for bem-sucedida e um código de status 200 for retornado, a resposta de fala será gravada em um arquivo com registro de data e hora.

```python
if __name__ == "__main__":
    subscription_key = "YOUR_KEY_HERE"
    app = TextToSpeech(subscription_key)
    app.get_token()
    app.save_audio()
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

É isso aí, você está pronto para executar seu aplicativo de amostra de texto para fala. Na linha de comando (ou sessão de terminal), navegue até o diretório do seu projeto e execute:

```console
python tts.py
```

Quando solicitado, digite o que você deseja converter de conversão de texto em fala. Se bem sucedido, o arquivo de fala está localizado na pasta do seu projeto. Reproduzi-lo usando o player de mídia favorito.

## <a name="clean-up-resources"></a>Limpar recursos

Remova todas as informações confidenciais do código-fonte do seu aplicativo de exemplo, como as chaves de assinatura.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Explorar exemplos do Python no GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/Python)

## <a name="see-also"></a>Consulte também

* [Referência de API de texto em fala](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [Criar fontes de voz personalizada](how-to-customize-voice-font.md)
* [Grave amostras de voz para criar uma voz personalizada](record-custom-voice-samples.md)
