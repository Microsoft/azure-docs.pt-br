---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/20/2020
ms.author: trbye
ms.openlocfilehash: 9365b4bbf6e6fb37156b6e9f24b923591945ea52
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377029"
---
## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é preciso:

> [!div class="checklist"]
> * [Criar um recurso de Fala do Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Criar um aplicativo LUIS para obter uma chave de ponto de extremidade](../../../../quickstarts/create-luis.md)
> * [Configurar seu ambiente de desenvolvimento e criar um projeto vazio](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="support-and-updates"></a>Suporte e atualizações

As atualizações do pacote Python do SDK de Fala são distribuídas por meio do PyPI e anunciadas no [Notas sobre a versão](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Se uma nova versão estiver disponível, você poderá atualizar para ela com o comando `pip install --upgrade azure-cognitiveservices-speech`.
Verifique qual versão está instalada no momento inspecionando a variável `azure.cognitiveservices.speech.__version__`.

Se você tiver um problema ou se estiver faltando um recurso, confira [Support and help options](~/articles/cognitive-services/Speech-Service/support.md) (Opções de Ajuda e Suporte).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Criar um aplicativo Python que usa o SDK de Fala

### <a name="run-the-sample"></a>Execute o exemplo

É possível copiar o [código de exemplo](#sample-code) neste início rápido para um arquivo de origem `quickstart.py` e executá-lo no IDE ou no console:

```Bash
python quickstart.py
```

ou é possível baixar este tutorial de início rápido como um [Jupyter](https://jupyter.org) Notebook do [repositório de exemplos do SDK de Fala](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) e executá-lo como um notebook.

### <a name="sample-code"></a>Código de exemplo

> [!NOTE]
> O SDK de Fala usará como padrão o reconhecimento do uso de en-us como o idioma; confira [Especificar o idioma de origem para conversão de fala em texto](../../../../how-to-specify-source-language.md) para obter informações sobre como escolher o idioma de origem.

```python
import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and region identifier from here: https://aka.ms/speech/sdkregion
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "whatstheweatherlike.wav"
audio_input = speechsdk.audio.AudioConfig(filename=audio_filename)

# Creates a recognizer with the given settings
speech_recognizer = speechsdk.SpeechRecognizer(speech_config=speech_config, audio_config=audio_input)

print("Recognizing first result...")

# Starts speech recognition, and returns after a single utterance is recognized. The end of a
# single utterance is determined by listening for silence at the end or until a maximum of 15
# seconds of audio is processed.  The task returns the recognition text as result. 
# Note: Since recognize_once() returns only a single utterance, it is suitable only for single
# shot recognition like command or query. 
# For long-running multi-utterance recognition, use start_continuous_recognition() instead.
result = speech_recognizer.recognize_once()

# Checks result.
if result.reason == speechsdk.ResultReason.RecognizedSpeech:
    print("Recognized: {}".format(result.text))
elif result.reason == speechsdk.ResultReason.NoMatch:
    print("No speech could be recognized: {}".format(result.no_match_details))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech Recognition canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        print("Error details: {}".format(cancellation_details.error_details))
```

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Instalar e usar o SDK de Fala com o Visual Studio Code

1. Baixe e instale uma versão de 64 bits do [Python](https://www.python.org/downloads/), 3.5 a 3.8, em seu computador.
1. Baixe e instale o [Visual Studio Code](https://code.visualstudio.com/Download).
1. Abra o Visual Studio Code e instale a extensão do Python. Selecione **Arquivo** > **Preferências** > **Extensões** no menu. Pesquise **Python**.

   ![Instalar a extensão do Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Crie uma pasta na qual armazenar o projeto. Um exemplo é fazer isso usando o Windows Explorer.
1. No Visual Studio Code, selecione o ícone **Arquivo**. Em seguida, abra a pasta criada.

   ![Abrir uma pasta](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Crie um novo arquivo de origem Python, `speechsdk.py`, clicando no ícone de novo arquivo.

   ![Criar um arquivo](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copie, cole e salve o [código do Python](#sample-code) para o arquivo recém-criado.
1. Insira as informações de assinatura do Serviço de Fala.
1. Se já tiver sido selecionado, um interpretador do Python será exibido no lado esquerdo da barra de status na parte inferior da janela.
   Caso contrário, exiba uma lista de interpretadores do Python disponíveis. Abra a paleta de comandos <kbd>Ctrl+Shift+P</kbd> e insira **Python: selecionar interpretador**. Escolha um adequado.
1. É possível instalar o pacote Python do SDK de Fala de dentro do Visual Studio Code. Faça isso se ele ainda não foi instalado para o interpretador do Python selecionado.
   Para instalar o pacote do SDK de Fala, abra um terminal. Exiba a paleta de comandos novamente <kbd>Ctrl+Shift+P</kbd> e insira **Terminal: Criar um Novo Terminal Integrado**.
   No terminal aberto, insira o comando `python -m pip install azure-cognitiveservices-speech` ou o comando apropriado para o seu sistema.
1. Para executar o código de exemplo, clique com o botão direito do mouse em algum lugar dentro do editor. Selecione **Executar arquivo Python no Terminal**.
   Os próximos 15 segundos de entrada de fala do arquivo de áudio serão reconhecidos e registrados na janela do console.

   ```console
   Recognizing first result...
   We recognized: What's the weather like?
   ```

Se houver problemas em seguir estas instruções, consulte o [tutorial do Python do Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial) mais amplo.

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]