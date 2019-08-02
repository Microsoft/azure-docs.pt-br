---
title: 'Tutorial: API de Tradução de Fala (C#)'
titleSuffix: Azure Cognitive Services
description: Use a API de Tradução de Fala para traduzir texto em tempo real.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: swmachan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a3853dd810182948e12b578c33b8cb91bef4b1cf
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445579"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Tutorial: Aplicativo de Tradução de Fala em C#

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Este tutorial é um tour de uma ferramenta de tradução de fala interativa que usa a API de Tradução de Fala, uma parte dos Serviços Cognitivos do Azure. Você saberá como:

> [!div class="checklist"]
> * Solicitar uma lista de idiomas com suporte pelo serviço
> * Capturar áudio e transmiti-lo para o serviço
> * Receber e exibir as traduções da fala como texto
> * Opcionalmente, executar uma versão falada (texto em fala) da tradução

Um arquivo de solução do Visual Studio para este aplicativo está [disponível no GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Pré-requisitos

Para esse tutorial, você precisa de qualquer edição do Visual Studio 2019, incluindo a Community Edition.

A solução do Visual Studio também cria um instalador para o aplicativo. É necessário o [WiX Toolset](http://wixtoolset.org/) e [extensão do Visual Studio WiX Toolset](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) para dar suporte a essa funcionalidade.

Você também precisa de uma chave de assinatura para o serviço de conversor de fala, que pode ser obtido a partir do painel do Microsoft Azure. Um tipo de preço gratuito está disponível e permite converter até 10 horas de fala por mês sem custo adicional. Esta camada é suficiente para este tutorial.

A [Biblioteca JSON.NET](https://www.newtonsoft.com/json) de terceiros (da Newtonsoft) também é necessária. Este assembly é instalado automaticamente pelo NuGet se ambas as caixas de seleção de restauração de pacotes estiverem habilitadas nas opções do Visual Studio.

## <a name="trying-the-translation-app"></a>Testando o aplicativo de tradução

Depois de abrir a solução Tradutor de Fala (`SpeechTranslator.sln`) no Visual STudio, pressione F5 para compilar e iniciar o aplicativo.  A janela principal do programa é exibida.

![[Janela principal da Tradução de Fala]](media/speech-translator-main-window.png)

Na primeira execução, escolha **Configurações de conta** no menu **Configurações** para abrir a janela mostrada aqui.

![[Janela principal da Tradução de Fala]](media/speech-translator-settings-window.png)

Cole a chave de assinatura da Tradução de Fala nesta janela e, em seguida, clique em **Salvar.** A chave é salva entre as execuções.

Volte na janela principal, escolha a entrada de áudio e dispositivos de saída que você deseja usar e os idiomas de origem e destino. Se você quiser ouvir o áudio da conversão, verifique se a opção **TTS** opção (texto em fala) está marcada. Se você quiser ver traduções parciais especulativas conforme fala, habilite a opção de **resultados parciais**.

Por fim, clique em **Iniciar** para começar a conversão. Diga algo que você deseja que seja traduzido e observe o texto reconhecido e a tradução que aparece na janela. Se você tiver habilitado a opção TTS, você também ouvirá a tradução.

## <a name="obtaining-supported-languages"></a>Obtendo idiomas com suporte

Atualmente, o serviço Tradução de Fala dá suporte a mais de cinco dúzias de idiomas para tradução de texto. Um número menor de idiomas é compatível para a tradução de fala. Esses idiomas requerem suporte para para transcrição (reconhecimento de fala) e para saída de texto para fala, síntese.

Em outras palavras, para a tradução de fala, o idioma de origem deve ser um suporte para a transcrição. O idioma de saída pode ser qualquer um dos idiomas com suporte para conversão de texto, supondo que você deseje um resultado de texto. Se desejar a saída de fala, você só pode se transformar em um idioma com suporte para texto em fala.

A Microsoft pode adicionar suporte para novos idiomas de tempos em tempos. Por esse motivo, não codifique qualquer conhecimento de idiomas com suporte em seu aplicativo. Em vez disso, a API de Tradução de Fala fornece um ponto de extremidade de idiomas que permite que você recupere os idiomas com suporte no tempo de execução. Você pode optar por receber uma ou mais listas de idiomas:

| | |
|-|-|
|`speech`|Idiomas com suporte para a transcrição de fala. Pode ser idiomas de origem para a tradução de fala.|
|`text`|Os idiomas com suporte para a transcrição de fala. Pode ser idiomas de destino para a tradução de fala quando a saída de texto for usada.|
|`tts`|As vozes suportadas para a síntese de fala, cada uma associado a um idioma específico. Pode ser idiomas de destino para a tradução de voz quando o texto em fala é usado. Um idioma específico pode ser suportado por mais de uma voz.|

O ponto de extremidade de idiomas não requer uma chave de assinatura e seu uso não conta em relação à cota. Seu URI é `https://dev.microsofttranslator.com/languages` e retorna os resultados no formato JSON.

O método `UpdateLanguageSettingsAsync()` em `MainWindow.xaml.cs`, conforme mostrado aqui, chama o ponto de extremidade de idiomas para obter a lista de idiomas com suporte.

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Esse método primeiro constrói uma solicitação HTTP para o ponto de extremidade de idiomas, solicitando todos as três listas de idiomas (`text`, `speech`, e `tts`).

O ponto de extremidade de Idiomas usa o cabeçalho de solicitação `Accept-Languages` para determinar o idioma no qual os nomes dos idiomas são representados. Por exemplo, o idioma conhecido para falantes de inglês como “Alemão” é chamado de “Deutsch” em alemão e “Alemán” em espanhol, e a lista de idiomas reflete essas diferenças. Idioma padrão do sistema é usado para esse cabeçalho.

Depois que a solicitação foi enviada e a resposta JSON foi recebida, a resposta é analisada em estruturas de dados internas. Essas estruturas são usadas para construir os os menus Do Idioma e Para o Idioma.

Como as vozes disponíveis dependem do idioma escolhido pelo usuário, não é possível configurar o menu de voz ainda. Em vez disso, as vozes disponíveis para cada idioma são armazenadas para uso posterior. O `ToLanguage_SelectionChanged` manipulador (no mesmo arquivo de origem) posterior atualiza o menu de voz chamando `UpdateVoiceComboBox()` quando o usuário escolhe um Para o Idioma.

Apenas por diversão, um idioma é selecionado aleatoriamente se o usuário não tiver executado o aplicativo antes. (as configurações de menu são armazenadas entre sessões).

## <a name="authenticating-requests"></a>Autenticar solicitações

Para autenticar o serviço Microsoft Translator Speech que precisa enviar sua chave de assinatura do Azure no cabeçalho, como o valor para `Ocp-Apim-Subscription-Key` na solicitação de conexão.

## <a name="translation-overview"></a>Visão Geral de Tradução

A API de Tradução (ponto de extremidade do WebSocket `wss://dev.microsofttranslator.com/speech/translate`) aceita áudio a ser convertido em monofônico, 16 kHz, 16 bits assinado formato WAVE. O serviço retorna uma ou mais respostas em JSON que contém tanto o texto reconhecido e convertido. Se o texto para fala foi solicitado, um arquivo de áudio é enviado.

O usuário escolhe a fonte de áudio usando o menu de microfone/arquivo de entrada. O áudio pode vir de um dispositivo de áudio (como um microfone) ou de um `.WAV` arquivo.

O método `StartListening_Click` é invocado quando o usuário clica no botão Iniciar. Este manipulador de eventos, por sua vez, chama `Connect()` para iniciar o processo de envio de áudio para o ponto de extremidade de API do serviço. Esse método`Connect()`   executa as seguintes tarefas:


> [!div class="checklist"]
> * Obter configurações de usuário na janela principal e validá-las
> * Inicializando a entrada de áudio e fluxos de saída
> * Chamando `ConnectAsync()` para lidar com o restante do trabalho

`ConnectAsync()`, por sua vez, lida com as seguintes tarefas:

> [!div class="checklist"]
> * Autenticação com a chave de assinatura do Azure no cabeçalho `Ocp-Apim-Subscription-Key`
> * Criando uma `SpeechClient` instância (encontrada no `SpeechClient.cs`) para se comunicar com o serviço
> * Inicializando `TextMessageDecoder` e `BinaryMessageDecoder` instâncias (consulte `SpeechResponseDecoder.cs`) para controlar as respostas
> * Enviar o áudio por meio da `SpeechClient` instância para o serviço de de Tradução de Fala
> * Receber e processar os resultados da tradução

As responsabilidades de `SpeechClient` são menos:

> [!div class="checklist"]
> * Estabelecer uma conexão WebSocket para o serviço de Tradução de Fala
> * Enviar dados de áudio e receber respostas através do soquete

## <a name="a-closer-look"></a>Uma análise mais detalhada

Deve estar mais claro agora como partes do aplicativo trabalham juntos para executar a solicitação de tradução. Vamos dar uma olhada em alguns códigos, focando as partes relevantes.

Esta é uma versão parcial do `Connect()` que mostra a configuração de fluxos de áudio:

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };

    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Uma parte significativa do `Connect()` envolve a criação de uma `SpeechClientOptions` instância (consulte `SpeechClientOptions.cs`) para manter as opções de conversão. As opções incluem as informações necessárias para se conectar ao serviço (como chave de autenticação e o nome do host) e os recursos usados para a tradução. Aqui, os campos mapeiam para os campos de cabeçalho e parâmetros HTTP expostos pela [API de Tradução de Fala](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` também cria e inicializa o dispositivo de entrada de áudio (variável `sampleProvider`) que serve como a fonte de fala a ser traduzida. Este dispositivo é um dispositivo de entrada de hardware, como um microfone, ou um arquivo que contém dados de áudio WAVE.

Aqui está o `ConnectAsync()` método que instancia a `speechClient` classe e busca funções anônimas para manipular texto e respostas binárias do serviço.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);

    TextMessageDecoder textDecoder;

    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);

    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected.
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Depois de autenticar, o método cria a `SpeechClient` instância. A `SpeechClient` classe (em `SpeechClient.cs`) invoca manipuladores de eventos em dados de texto e binários de recebimento. Manipuladores adicionais são chamados quando a conexão falha ou se desconecta.

Os dados binários são áudio (saída de texto em fala) enviado pelo serviço quando TTS está habilitado. Os dados de texto serão uma tradução parcial ou completa do texto falado. Portanto, depois de criar uma instância, o método conecta funções para lidar com essas mensagens: áudio pelo armazenamento para reprodução posterior e texto para exibição na janela.

## <a name="next-steps"></a>Próximas etapas

Este exemplo de código é um aplicativo de recursos que demonstra o uso da API de Tradução de Fala Assim, há um número razoável de partes móveis para entender. Você já percorreu os bits mais importantes. Para o restante, é instrutivo definir alguns pontos de interrupção no Visual Studio e percorrer o processo de tradução. Quando você entende o aplicativo de exemplo, você está pronto para usar o serviço de conversor de fala em seus próprios aplicativos.

> [!div class="nextstepaction"]
> [Referência da API de Tradução de Fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
