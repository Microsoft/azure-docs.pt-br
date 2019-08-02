---
title: 'Início Rápido: Assistente virtual personalizado com prioridade no uso de voz (Versão prévia), Java (Android) – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Aprenda a criar um aplicativo de assistente virtual com prioridade no uso de voz em Java no Android usando o SDK de Fala
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: c62402faa1995e1e992c8251ed87160a8f33d3a7
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67602737"
---
# <a name="quickstart-create-a-voice-first-virtual-assistant-in-java-on-android-by-using-the-speech-sdk"></a>Início Rápido: Criar um assistente virtual com prioridade no uso de voz em Java no Android usando o SDK de Fala

Há um início rápido disponível também para [conversão de fala em texto](quickstart-java-android.md).

Neste artigo, você criará um assistente virtual com prioridade no uso de voz em Java para Android usando o [SDK de Fala](speech-sdk.md). Esse aplicativo se conectará a um bot que você já criou e configurou com o [canal de Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech). Ele enviará uma solicitação de voz ao bot e apresentará uma atividade de resposta habilitada para voz.

Esse aplicativo é compilado com o pacote do Maven do SDK de Fala e com o Android Studio 3.3. O SDK de Fala é atualmente compatível com dispositivos Android que têm processadores ARM de 32/64 bits e Intel x86/x64 compatíveis.

> [!NOTE]
> Para o SDK dos Dispositivos de Fala e o dispositivo Roobo, confira [SDK dos Dispositivos de Fala](speech-devices-sdk.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma chave de assinatura do Azure para os Serviços de Fala. [Obtenha uma gratuitamente](get-started.md) ou crie-a no [portal do Azure](https://portal.azure.com).
* Um bot criado anteriormente, configurado com o [canal de Fala do Direct Line](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
* [Android Studio](https://developer.android.com/studio/) v3.3 ou posterior

    > [!NOTE]
    > A Direct Line Speech (Versão prévia) está disponível atualmente em um subconjunto de regiões dos Serviços de Fala. Confira [a lista de regiões compatíveis com assistentes virtuais com prioridade no uso de voz](regions.md#Voice-first virtual assistants) e implante seus recursos em uma dessas regiões.

## <a name="create-and-configure-a-project"></a>Criar e configurar um projeto

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-java-android-create-proj.md)]

## <a name="create-user-interface"></a>Criar interface do usuário

Nesta seção, criaremos uma IU (interface do usuário) básica para o aplicativo. Vamos começar abrindo a atividade principal: `activity_main.xml`. O modelo básico inclui uma barra de título com o nome do aplicativo e uma `TextView` com a mensagem “Olá, Mundo!”.

Agora, substitua o conteúdo de `activity_main.xml` pelo seguinte código:

   ```xml
   <?xml version="1.0" encoding="utf-8"?>
   <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:onClick="onBotButtonClicked"
        android:text="Talk to your bot" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Recognition Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/recoText"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="  \n(Recognition goes here)\n" />

    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="Activity Data"
        android:textSize="18dp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/activityText"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:scrollbars="vertical"
        android:text="  \n(Activities go here)\n" />

   </LinearLayout>
   ```

Esse XML define uma interface do usuário simples para interação com seu bot.

* O elemento `button` inicia uma interação e chama o método `onBotButtonClicked` quando clicado.
* O elemento `recoText` exibe os resultados de conversão de fala em texto enquanto você fala com o bot.
* O elemento `activityText` exibe o conteúdo JSON da atividade Bot Framework mais recente de seu bot.

O texto e a representação gráfica da sua interface do usuário agora devem ser semelhantes a isto:

![](media/sdk/qs-java-android-assistant-designer-ui.png)

## <a name="add-sample-code"></a>Adicionar código de exemplo

1. Abra `MainActivity.java` e substitua o conteúdo pelo código a seguir:

   ```java
    package samples.speech.cognitiveservices.microsoft.com;

    import android.media.AudioFormat;
    import android.media.AudioManager;
    import android.media.AudioTrack;
    import android.support.v4.app.ActivityCompat;
    import android.support.v7.app.AppCompatActivity;
    import android.os.Bundle;
    import android.text.method.ScrollingMovementMethod;
    import android.view.View;
    import android.widget.TextView;

    import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
    import com.microsoft.cognitiveservices.speech.audio.PullAudioOutputStream;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConfig;
    import com.microsoft.cognitiveservices.speech.dialog.DialogServiceConnector;

    import org.json.JSONException;
    import org.json.JSONObject;

    import static android.Manifest.permission.*;

    public class MainActivity extends AppCompatActivity {
        // Replace below with your bot's own Direct Line Speech channel secret
        private static String channelSecret = "YourChannelSecret";
        // Replace below with your own speech subscription key
        private static String speechSubscriptionKey = "YourSpeechSubscriptionKey";
        // Replace below with your own speech service region (note: only a subset of regions are currently supported)
        private static String serviceRegion = "YourSpeechServiceRegion";

        private DialogServiceConnector connector;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);

            TextView recoText = (TextView) this.findViewById(R.id.recoText);
            TextView activityText = (TextView) this.findViewById(R.id.activityText);
            recoText.setMovementMethod(new ScrollingMovementMethod());
            activityText.setMovementMethod(new ScrollingMovementMethod());

            // Note: we need to request permissions for audio input and network access
            int requestCode = 5; // unique code for the permission request
            ActivityCompat.requestPermissions(MainActivity.this, new String[]{RECORD_AUDIO, INTERNET}, requestCode);
        }

        public void onBotButtonClicked(View v) {
            // Recreate the DialogServiceConnector on each button press, ensuring that the existing one is closed
            if (connector != null) {
                connector.close();
                connector = null;
            }

            // Create the DialogServiceConnector from the channel and speech subscription information
            DialogServiceConfig config = DialogServiceConfig.fromBotSecret(channelSecret, speechSubscriptionKey, serviceRegion);
            connector = new DialogServiceConnector(config, AudioConfig.fromDefaultMicrophoneInput());

            // Optional step: preemptively connect to reduce first interaction latency
            connector.connectAsync();

            // Register the DialogServiceConnector's event listeners
            registerEventListeners();

            // Begin sending audio to your bot
            connector.listenOnceAsync();
        }

        private void registerEventListeners() {
            TextView recoText = (TextView) this.findViewById(R.id.recoText); // 'recoText' is the ID of your text view
            TextView activityText = (TextView) this.findViewById(R.id.activityText); // 'activityText' is the ID of your text view

            // Recognizing will provide the intermediate recognized text while an audio stream is being processed
            connector.recognizing.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognizing: " + recoArgs.getResult().getText());
            });

            // Recognized will provide the final recognized text once audio capture is completed
            connector.recognized.addEventListener((o, recoArgs) -> {
                recoText.setText("  Recognized: " + recoArgs.getResult().getText());
            });

            // SessionStarted will notify when audio begins flowing to the service for a turn
            connector.sessionStarted.addEventListener((o, sessionArgs) -> {
                recoText.setText("Listening...");
            });

            // SessionStopped will notify when a turn is complete and it's safe to begin listening again
            connector.sessionStopped.addEventListener((o, sessionArgs) -> {
            });

            // Canceled will be signaled when a turn is aborted or experiences an error condition
            connector.canceled.addEventListener((o, canceledArgs) -> {
                recoText.setText("Canceled (" + canceledArgs.getReason().toString() + ") error details: {}" + canceledArgs.getErrorDetails());
                connector.disconnectAsync();
            });

            // ActivityReceived is the main way your bot will communicate with the client and uses bot framework activities.
            connector.activityReceived.addEventListener((o, activityArgs) -> {
                try {
                    // Here we use JSONObject only to "pretty print" the condensed Activity JSON
                    String rawActivity = activityArgs.getActivity().serialize();
                    String formattedActivity = new JSONObject(rawActivity).toString(2);
                    activityText.setText(formattedActivity);
                } catch (JSONException e) {
                    activityText.setText("Couldn't format activity text: " + e.getMessage());
                }

                if (activityArgs.hasAudio()) {
                    // Text-to-speech audio associated with the activity is 16 kHz 16-bit mono PCM data
                    final int sampleRate = 16000;
                    int bufferSize = AudioTrack.getMinBufferSize(sampleRate, AudioFormat.CHANNEL_OUT_MONO, AudioFormat.ENCODING_PCM_16BIT);

                    AudioTrack track = new AudioTrack(
                            AudioManager.STREAM_MUSIC,
                            sampleRate,
                            AudioFormat.CHANNEL_OUT_MONO,
                            AudioFormat.ENCODING_PCM_16BIT,
                            bufferSize,
                            AudioTrack.MODE_STREAM);

                    track.play();

                    PullAudioOutputStream stream = activityArgs.getAudio();

                    // Audio is streamed as it becomes available. Play it as it arrives.
                    byte[] buffer = new byte[bufferSize];
                    long bytesRead = 0;

                    do {
                        bytesRead = stream.read(buffer);
                        track.write(buffer, 0, (int) bytesRead);
                    } while (bytesRead == bufferSize);

                    track.release();
                }
            });
        }
    }
   ```

   * O método `onCreate` inclui código que exige permissões de microfone e Internet.

   * O método `onBotButtonClicked` é, conforme observado anteriormente, o manipulador de clique do botão. O pressionamento de um botão dispara uma interação única (“turno”) com o bot.

   * O método `registerEventListeners` demonstra os eventos usados pelo `DialogServiceConnector` e o tratamento básico das atividades de entrada.

1. No mesmo arquivo, substitua as cadeia de caracteres de configuração para corresponder aos seus recursos:

    * Substitua `YourChannelSecret` pelo segredo do canal de Direct Line Speech do bot.

    * Substitua `YourSpeechSubscriptionKey` por sua chave de assinatura.

    * Substitua `YourServiceRegion` pela [região](regions.md) associada à assinatura. Somente um subconjunto de regiões dos Serviços de Fala são compatíveis atualmente com a Direct Line Speech. Para saber mais, confira [regiões](regions.md#voice-first-virtual-assistants).

## <a name="build-and-run-the-app"></a>Compilar e executar o aplicativo

1. Conecte o dispositivo Android ao PC de desenvolvimento. Verifique se você habilitou o [modo de desenvolvimento e depuração de USB](https://developer.android.com/studio/debug/dev-options) no dispositivo.

1. Para compilar o aplicativo, pressione Ctrl+F9 ou escolha **Construir** > **Criar um Projeto** na barra de menus.

1. Para inicializar o aplicativo, pressione Shift+F10 ou escolha **Executar** > **Executar 'aplicativo'** .

1. Na janela de destino da implantação exibida, escolha seu dispositivo Android.

   ![Captura de tela da janela Selecionar Destino da Implantação](media/sdk/qs-java-android-12-deploy.png)

Depois que o aplicativo e sua atividade forem iniciados, clique no botão para começar a falar com o bot. O texto transcrito aparecerá enquanto você fala e a última atividade recebida do bot aparecerá quando for recebida. Se seu bot estiver configurado para dar respostas faladas, a conversão de fala em texto será reproduzida automaticamente.

![Captura de tela do aplicativo Android](media/sdk/qs-java-android-assistant-completed-turn.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Criar e implantar um bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)

## <a name="see-also"></a>Consulte também
- [Sobre assistentes virtuais com prioridade no uso de voz](voice-first-virtual-assistants.md)
- [Obter gratuitamente uma chave de assinatura do Serviço de Fala](get-started.md)
- [Palavras de ativação personalizadas](speech-devices-sdk-create-kws.md)
- [Conectar a Direct Line Speech ao seu bot](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
- [Explorar amostras de Java no GitHub](https://aka.ms/csspeech/samples)
