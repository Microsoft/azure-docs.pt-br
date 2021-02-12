---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 3b473d82eb879955a6cbdce58ba66176f5c6d6f5
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214165"
---
Neste início rápido, você aprenderá a usar o SDK de Dispositivos de Fala para Android para criar um produto habilitado para fala ou usá-lo como um dispositivo de [transcrição de conversas](../conversation-transcription.md).

Este guia exige uma conta dos [Serviços Cognitivos do Azure](../overview.md#try-the-speech-service-for-free) com o recurso do Serviço de Fala.

O código-fonte para o aplicativo de exemplo é incluído com o SDK de Dispositivos de Fala. Também está [disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a usar o SDK de Dispositivos de Fala, você precisará:

- Seguir as instruções fornecidas com o [kit de desenvolvimento](../get-speech-devices-sdk.md) para ligar o dispositivo.

- Baixar a última versão do [SDK de Dispositivos de Fala](../speech-devices-sdk.md) e extrair o arquivo .zip no diretório de trabalho.

  > [!NOTE]
  > Este início rápido supõe que o aplicativo tenha sido extraído para C:\SDSDK\Android-Sample-Release

- Para obter uma [chave de assinatura do Azure para o Serviço de Fala](../overview.md#try-the-speech-service-for-free)

- Se você pretende usar a Transcrição de Conversas, use um [dispositivo de microfone circular](../get-speech-devices-sdk.md). Atualmente, esse recurso só está disponível para "en-US" e "zh-CN" nas regiões "centralus" e "eastasia". Você precisará ter uma chave de fala em uma dessas regiões para usar a transcrição de conversas.

- Se pretende usar o Serviço de Fala para identificar intenções (ou ações) com base em enunciados do usuário, você precisará de uma assinatura do [LUIS (Serviço de Reconhecimento Vocal)](../../luis/luis-how-to-azure-subscription.md). Para saber mais sobre o LUIS e o reconhecimento de intenção, confira [Reconhecer intenções de fala com o LUIS, C#](../how-to-recognize-intents-from-speech-csharp.md).

  Você pode [criar um modelo simples de LUIS](../../luis/index.yml) ou usar o modelo LUIS, LUIS-example.json. O exemplo de modelo LUIS está disponível na [site de download do SDK de Dispositivos de Fala](https://aka.ms/sdsdk-luis). Para carregar o arquivo JSON do seu modelo para o [portal LUIS](https://www.luis.ai/home), selecione **Importar novo aplicativo** e, em seguida, selecione o arquivo JSON.

- Instale o [Android Studio](https://developer.android.com/studio/) e [Vysor](https://vysor.io/download/) em seu computador.

## <a name="set-up-the-device"></a>Configurar o dispositivo

1. Inicie o Vysor no seu computador.

   ![Vysor](../media/speech-devices-sdk/qsg-3.png)

1. Seu dispositivo deve estar listado em **Escolha um dispositivo**. Clique no botão **Exibir** ao lado do dispositivo.

1. Conecte-se à sua rede sem fio, selecionando o ícone de pasta e, em seguida, selecione **Configurações** > **WLAN**.

   ![Vysor WLAN](../media/speech-devices-sdk/qsg-4.png)

   > [!NOTE]
   > Se sua empresa tem políticas sobre como conectar dispositivos ao seu sistema de Wi-Fi, é neecessário obter o endereço MAC e entrar em contato com seu departamento de TI sobre como conectar-se ao Wi-Fi de sua empresa.
   >
   > Para localizar o endereço MAC do kit de desenvolvimento, selecione o ícone de pasta de arquivo na área de trabalho do kit de desenvolvimento.
   >
   > ![Pasta de arquivo do Vysor](../media/speech-devices-sdk/qsg-10.png)
   >
   > Escolha a opção **Configurações**. Pesquise "endereço mac" e, em seguida, selecione **endereço Mac** > **WLAN Avançado**. Anote o endereço MAC que aparece na parte inferior da caixa de diálogo.
   >
   > ![Endereço MAC do Vysor](../media/speech-devices-sdk/qsg-11.png)
   >
   > Algumas empresas podem ter um tempo limite de quanto tempo um dispositivo pode ser conectado a seus sistemas Wi-Fi. Talvez seja necessário estender o registro do kit de desenvolvimento com o sistema Wi-Fi após um determinado número de dias.

## <a name="run-the-sample-application"></a>Executar o aplicativo de exemplo

Para validar sua instalação do kit de desenvolvimento, crie e instale o aplicativo de exemplo:

1. Inicie o Android Studio.

1. Selecionar **Abrir um projeto existente do Android Studio**.

   ![Android Studio - Abrir um projeto existente](../media/speech-devices-sdk/qsg-5.png)

1. Vá para C:\SDSDK\Android-Sample-Release\example. Selecione **Ok** para abrir o projeto de exemplo.

1. Configure o Gradle para fazer referência ao SDK de Fala. Encontre os arquivos a seguir em **Scripts do Gradle** no Android Studio.

    Atualize o **build.gradle(Project:example)** , o bloco allprojects deve estar igual ao mostrado abaixo, adicionando as linha de maven.

    ```xml
    allprojects {
        repositories {
            google()
            jcenter()
            mavenCentral()
            maven {
                url 'https://csspeechstorage.blob.core.windows.net/maven/'
            }
        }
    }
    ```

    Atualize o **build.gradle(Module:app)** adicionando esta linha à seção de dependências. 
    
    ```xml
    implementation'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
    ```
    
1. Adicione sua chave de assinatura de fala ao código-fonte. Se você quiser experimentar o reconhecimento de intenção, adicione também sua chave de assinatura [Serviço Inteligente de Reconhecimento Vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) e ID do aplicativo.

   Para a fala e o LUIS, suas informações são inseridas em MainActivity.java:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

   Se você estiver usando a transcrição de conversas, suas informações de chave de fala e região também serão necessárias em conversation.java:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
   ```

1. A palavra-chave padrão é "Computador". Você também pode tentar uma das outras palavras-chave fornecidas, assim como "Máquina" ou "Assistente". Os arquivos de recurso para essas alternativas de palavra-chave estão no SDK de Dispositivos de Fala, na pasta da palavra-chave. Por exemplo, C:\SDSDK\Android-Sample-Release\keyword\Computer contém os arquivos usados para a palavra-chave "Computador".

   > [!TIP]
   > Você também pode [criar uma palavra-chave personalizada](../custom-keyword-basics.md).

   Para usar uma nova palavra-chave, atualize as duas linhas a seguir em `MainActivity.java` e copie o pacote de palavras-chave para o aplicativo. Por exemplo, para usar a palavra-chave “Computador” do pacote de palavras-chave kws-machine.zip:

   - Copie o pacote de palavras-chave para a pasta "C:\SDSDK\Android-Sample-Release\example\app\src\main\assets\".
   - Atualize o `MainActivity.java` com a palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

1. Atualize as seguintes linhas, que contêm as configurações de geometria de matriz de microfone:

   ```java
   private static final String DeviceGeometry = "Circular6+1";
   private static final String SelectedGeometry = "Circular6+1";
   ```

   Esta tabela lista os valores compatíveis:

   | Variável | Significado | Valores disponíveis |
   | -------- | ------- | ---------------- |
   | `DeviceGeometry` | Configuração de microfone físico | Para um kit de desenvolvimento circular: `Circular6+1` |
   |          |         | Para um kit de desenvolvimento linear: `Linear4` |
   | `SelectedGeometry` | Configuração do microfone do software | Para um kit de desenvolvimento circular que usa todos os microfones: `Circular6+1` |
   |          |         | Para um kit de desenvolvimento circular que usa os quatro microfones: `Circular3+1` |
   |          |         | Para um kit de desenvolvimento linear que usa todos os microfones: `Linear4` |
   |          |         | Para um kit de desenvolvimento linear que usa dois microfones: `Linear2` |

1. Para compilar o aplicativo, no menu **Executar**, selecione **Executar 'aplicativo'** . A caixa de diálogo **Selecionar o Destino de Implantação** aparece.

1. Selecione seu dispositivo e, em seguida, selecione **OK** para implantar o aplicativo no dispositivo.

   ![Selecione a caixa de diálogo Destino da Implantação](../media/speech-devices-sdk/qsg-7.png)

1. O aplicativo de exemplo do SDK de Dispositivos de Fala é iniciado e exibe as seguintes opções:

   ![Aplicativo de exemplo e opções do SDK de Dispositivos de Fala de Exemplo](../media/speech-devices-sdk/qsg-8.png)

1. Experimente a nova demonstração da transcrição de conversas. Comece a transcrição com 'Iniciar Sessão'. Por padrão, todas as pessoas são convidados. No entanto, se você tiver assinaturas de voz do participante, elas poderão ser colocadas em um arquivo `/video/participants.properties` no dispositivo. Para gerar a assinatura de voz, examine [Transcrever conversas (SDK)](../how-to-use-conversation-transcription.md).

   ![Aplicativo de transcrição de conversas de demonstração](../media/speech-devices-sdk/qsg-15.png)

1. Experimento!

## <a name="troubleshooting"></a>Solução de problemas

Se você não puder se conectar ao Dispositivo de Fala. Digite o comando a seguir em uma janela do prompt de comando. Ele retornará uma lista de dispositivos:

```powershell
 adb devices
```

> [!NOTE]
> Esse comando usa o Android Debug Bridge, `adb.exe`, que faz parte da instalação do Android Studio. Essa ferramenta está localizada em C:\Users\[user name]\AppData\Local\Android\Sdk\platform-tools. Você pode adicionar esse diretório para o caminho para que seja mais conveniente para invocar `adb`. Caso contrário, você deve especificar o caminho completo para a instalação do adb.exe em cada comando que invoca `adb`.
>
> Se um erro `no devices/emulators found` for exibido, verifique se o cabo USB está conectado e se um cabo de alta qualidade está sendo usado.