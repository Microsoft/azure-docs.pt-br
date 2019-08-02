---
title: 'Início Rápido: Executar o SDK de Dispositivos de Fala no Linux – Serviços de Fala'
titleSuffix: Azure Cognitive Services
description: Pré-requisitos e instruções para começar a trabalhar com o SDK de Dispositivos de Fala do Linux.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/10/2019
ms.author: erhopf
ms.openlocfilehash: d755f3388466369ee1edc3d9ff1e353173babc10
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723297"
---
# <a name="quickstart-run-the-speech-devices-sdk-sample-app-on-linux"></a>Início Rápido: Executar o aplicativo de exemplo do SDK de Dispositivos de Fala no Linux

Neste início rápido, você aprenderá a usar o SDK de Dispositivos de Fala para Linux para criar um produto habilitado para fala ou usá-lo como um dispositivo de [transcrição de conversas](conversation-transcription-service.md). Atualmente, há suporte apenas para o [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/).

O aplicativo é compilado com o pacote do SDK de Fala e o Java IDE do Eclipse (v4) no Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9). Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

Este guia exige uma conta dos [Serviços Cognitivos do Azure](get-started.md) com um recurso dos Serviços de Fala. Se não tiver uma conta, você poderá usar a [avaliação gratuita](https://azure.microsoft.com/try/cognitive-services/) para obter uma chave de assinatura.

O código-fonte para o [aplicativo de exemplo](https://aka.ms/sdsdk-download-JRE) é incluído com o SDK de Dispositivos de Fala. Também está [disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema operacional: Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Java IDE do Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) apenas.
* Uma chave de assinatura do Azure para o Serviço de Fala. [Obtenha uma gratuitamente](get-started.md).
* Baixe a última versão do [SDK de Dispositivos de Fala](https://aka.ms/sdsdk-download-JRE) para Java e extraia o arquivo .zip para seu diretório de trabalho.
   > [!NOTE]
   > O arquivo JRE-Sample-Release.zip inclui o aplicativo de exemplo do JRE e este início rápido pressupõe que o aplicativo seja extraído para /home/wcaltest/JRE-Sample-Release

Instale essas dependências antes de iniciar o Eclipse.

* No Ubuntu:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.0 libasound2
  ```

* No Debian 9:

  ```sh
  sudo apt-get update
  sudo apt-get install libssl1.0.2 libasound2
  ```

A transcrição de conversas no momento está disponível apenas para "en-US" e "zh-CN", nas regiões "centralus" e "eastasia". Você precisará ter uma chave de fala em uma dessas regiões para usar a transcrição de conversas.

Caso você planeje usar as intenções, será necessária uma assinatura do [LUIS (Reconhecimento vocal)](https://docs.microsoft.com/azure/cognitive-services/luis/azureibizasubscription). Para saber mais sobre o LUIS e o reconhecimento de intenção, confira [Reconhecer intenções de fala com o LUIS, C#](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-recognize-intents-from-speech-csharp). Um [modelo de exemplo do LUIS](https://aka.ms/sdsdk-luis) está disponível para este aplicativo.

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No **Inicializador do IDE do Eclipse**, no campo **Workspace**, insira o nome de um novo diretório de workspace. Em seguida, selecione **Iniciar**.

   ![Captura de tela do Inicializador do Eclipse](media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Em alguns instantes, a janela principal do IDE do Eclipse aparece. Feche a tela de boas-vindas caso haja uma.

1. Na barra de menus do Eclipse, crie um novo projeto escolhendo **Arquivo** > **Novo** > **Projeto em Java**. Se não estiver disponível, escolha **Projeto** e, em seguida **Projeto em Java**.

1. O assistente **Novo Projeto em Java** é iniciado. **Procure** a localização do projeto de exemplo. Selecione **Concluir**.

   ![Captura de tela do assistente de Novo Projeto Java](media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Configurar** > **Converter para Projeto Maven** no menu de contexto. Selecione **Concluir**.

   ![Captura de tela do Explorador de pacotes](media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Propriedades** e, em seguida, **Executar/Configurações de Depuração** > **Novo…** > **Aplicativo Java**. 

1. A janela **Editar Configuração** é exibida. No campo **Nome**, insira **Principal** e use **Pesquisar** para a **Classe Principal** para localizar e selecionar **com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Captura de tela da configuração de inicialização da edição](media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Também na janela **Editar Configuração**, selecione a página **Ambiente** e **Novo**. A janela **Nova Variável de Ambiente** é exibida. No campo **Nome**, insira **LD_LIBRARY_PATH** e, no campo **valor**, insira a pasta contendo os arquivos *.so, por exemplo, **/home/wcaltest/JRE-Sample-Release**

1. Copie `kws.table` e `participants.properties` para a pasta do projeto **target/classes**


## <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

1. Adicione sua chave de assinatura de fala ao código-fonte. Se você quiser experimentar o reconhecimento de intenção, adicione também sua chave de assinatura [Serviço Inteligente de Reconhecimento Vocal](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/) e ID do aplicativo.

   Para fala e LUIS, suas informações são inseridas em `FunctionsList.java`:

   ```java
    // Subscription
    private static String SpeechSubscriptionKey = "<enter your subscription info here>";
    private static String SpeechRegion = "westus"; // You can change this if your speech region is different.
    private static String LuisSubscriptionKey = "<enter your subscription info here>";
    private static String LuisRegion = "westus2"; // you can change this, if you want to test the intent, and your LUIS region is different.
    private static String LuisAppId = "<enter your LUIS AppId>";
   ```

    Se você estiver usando a transcrição de conversas, suas informações de chave de fala e região também serão necessárias em `Cts.java`:

   ```java
    private static final String CTSKey = "<Conversation Transcription Service Key>";
    private static final String CTSRegion="<Conversation Transcription Service Region>";// Region may be "centralus" or "eastasia"
    ```

1. A palavra de ativação padrão (palavra-chave) é "Computador". Você também pode tentar uma das outras palavras de ativação fornecidas, "Máquina" e "Assistente". Os arquivos de recurso para essas palavras alternativas de ativação estão no SDK de Dispositivos de Fala, na pasta de palavra-chave. Por exemplo, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` contém os arquivos usados para a palavra de ativação "Computador".

   > [!TIP]
   > Você também pode [criar uma palavra de ativação personalizada](speech-devices-sdk-create-kws.md).

    Para usar uma nova palavra de ativação, atualize as duas linhas a seguir em `FunctionsList.java` e copie o pacote de palavras de ativação para o aplicativo. Por exemplo, para usar a palavra de ativação 'Computador' do pacote de palavras de ativação `kws-machine.zip`:

   * Copie o pacote de palavra de ativação para a pasta do projeto **target/classes**.

   * Atualize o `FunctionsList.java` com a palavra-chave e o nome do pacote:

     ```java
     private static final String Keyword = "Machine";
     private static final String KeywordModel = "kws-machine.zip" // set your own keyword package name.
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Execute o aplicativo de exemplo do Eclipse

1. Na barra de menus do Eclipse, **Executar** > **Executar** 

1. O aplicativo de exemplo do SDK de Dispositivos de Fala é iniciado e exibe as seguintes opções:

   ![Aplicativo de exemplo e opções do SDK de Dispositivos de Fala de Exemplo](media/speech-devices-sdk/java-sample-app-linux.png)

1. Experimente a nova demonstração da **transcrição de conversas**. Comece a transcrição com **Sessão** > **Iniciar Sessão**. Por padrão, todas as pessoas são convidados. No entanto, se você tiver assinaturas de voz do participante, elas poderão ser colocadas em `participants.properties` na pasta do projeto **target/classes**. Para gerar a assinatura de voz, examine [Transcrever conversas (SDK)](how-to-use-conversation-transcription-service.md).

   ![Aplicativo de transcrição de conversas de demonstração](media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Criar e executar o aplicativo autônomo

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Exportar**. 
1. A janela **Exportar** é exibida. Expanda **Java** e selecione **Arquivo JAR executável** e, em seguida, selecione **Avançar**.

   ![Captura de tela da janela Exportar](media/speech-devices-sdk/eclipse-export-linux.png) 

1. A janela **Exportação de Arquivo JAR Executável** é exibida. Escolha um **Destino de exportação** para o aplicativo e, em seguida, selecione **Concluir**.
 
   ![Captura da tela de Exportação de Arquivo JAR Executável](media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Coloque `kws.table` e `participants.properties` na pasta de destino escolhida acima, pois o aplicativo precisa desses arquivos.

1. Defina LD_LIBRARY_LIB para a pasta que contém os arquivos *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Para executar o aplicativo autônomo

     ```bash
     java -jar SpeechDemo.jar
     ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Examinar as notas de versão](devices-sdk-release-notes.md)
