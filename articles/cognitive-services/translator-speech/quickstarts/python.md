---
title: 'Início Rápido: API de Tradução de Fala com Python'
titlesuffix: Azure Cognitive Services
description: Obtenha informações e exemplos de código para ajudá-lo a começar a usar rapidamente a API de Tradução de Fala.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: quickstart
ms.date: 07/17/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 9426514853d58db1aa43eb42ee054b7bd931ae62
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356922"
---
# <a name="quickstart-translator-speech-api-with-python"></a>Início Rápido: API de Tradução de Fala com Python
<a name="HOLTop"></a>

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Este artigo mostra como usar a API de Tradução de Fala para traduzir palavras faladas em um arquivo .wav.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará do [Python 3.x](https://www.python.org/downloads/) para executar esse código.

Você precisará instalar o [pacote de cliente do websocket](https://pypi.python.org/pypi/websocket-client) para Python.

É necessário ter um arquivo .wav chamado "speak.wav" na mesma pasta do executável que você compila a partir do código abaixo. Este arquivo .wav deve estar no padrão PCM, 16 bits, 16kHz, formato mono.

É necessário ter uma [conta de API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) com **API de Tradução de Fala da Microsoft**. Você precisará de uma chave de assinatura paga no [painel do Azure](https://portal.azure.com/#create/Microsoft.CognitiveServices).

## <a name="translate-speech"></a>Traduzir fala

O código a seguir traduz fala de um idioma para outro.

1. Crie um novo projeto Python no seu IDE favorito.
2. Adicione o código fornecido abaixo.
3. Substitua o valor `key` por uma chave de acesso válida para a sua assinatura.
4. Execute o programa.

```python
# -*- coding: utf-8 -*-

# To install this package, run:
#   pip install websocket-client
import websocket

# **********************************************
# *** Update or verify the following values. ***
# **********************************************

# Replace the subscriptionKey string value with your valid subscription key.
key = 'ENTER KEY HERE'

host = 'wss://dev.microsofttranslator.com'
path = '/speech/translate'
params = '?api-version=1.0&from=en-US&to=it-IT&features=texttospeech&voice=it-IT-Elsa'
uri = host + path + params

input_file = 'speak.wav'
output_file = 'speak2.wav'

output = bytearray()


def on_open(client):
    print("Connected.")

# r = read. b = binary.
    with open(input_file, mode='rb') as file:
        data = file.read()

    print("Sending audio.")
    client.send(data, websocket.ABNF.OPCODE_BINARY)
# Make sure the audio file is followed by silence.
# This lets the service know that the audio input is finished.
    print("Sending silence.")
    client.send(bytearray(32000), websocket.ABNF.OPCODE_BINARY)


def on_data(client, message, message_type, is_last):
    global output
    if (websocket.ABNF.OPCODE_TEXT == message_type):
        print("Received text data.")
        print(message)
# For some reason, we receive the data as type websocket.ABNF.OPCODE_CONT.
    elif (websocket.ABNF.OPCODE_BINARY == message_type or websocket.ABNF.OPCODE_CONT == message_type):
        print("Received binary data.")
        print("Is last? " + str(is_last))
        output = output + message
        if (True == is_last):
            # w = write. b = binary.
            with open(output_file, mode='wb') as file:
                file.write(output)
                print("Wrote data to output file.")
            client.close()
    else:
        print("Received data of type: " + str(message_type))


def on_error(client, error):
    print("Connection error: " + str(error))


def on_close(client):
    print("Connection closed.")


client = websocket.WebSocketApp(
    uri,
    header=[
        'Ocp-Apim-Subscription-Key: ' + key
    ],
    on_open=on_open,
    on_data=on_data,
    on_error=on_error,
    on_close=on_close
)

print("Connecting...")
client.run_forever()
```

**Traduzir resposta de fala**

Um resultado com êxito é a criação de um arquivo chamado "speak2.wav". O arquivo contém a tradução das palavras faladas em "speak.wav".

[Voltar ao início](#HOLTop)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Tutorial de Tradução de Fala](../tutorial-translator-speech-csharp.md)

## <a name="see-also"></a>Consulte também

[Visão geral da Tradução de Fala](../overview.md)
[Referência de API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
