---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 02/20/2020
ms.author: trbye
ms.openlocfilehash: 39cead8306ece6638f85813efc318dd41a6faf8c
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214705"
---
Neste início rápido, você aprenderá a usar o SDK de Dispositivos de Fala para Linux para criar um produto habilitado para fala ou usá-lo como um dispositivo de [transcrição de conversas](../conversation-transcription.md). Atualmente, há suporte apenas para o [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/).

O aplicativo é compilado com o pacote do SDK de Fala e o Java IDE do Eclipse (v4) no Linux 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8). Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

Este guia exige uma conta dos [Serviços Cognitivos do Azure](../overview.md#try-the-speech-service-for-free) com o recurso do Serviço de Fala. 

O código-fonte para o [aplicativo de exemplo](https://aka.ms/sdsdk-download-JRE) é incluído com o SDK de Dispositivos de Fala. Também está [disponível no GitHub](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK).

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido requer:

* Sistema Operacional: Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 7/8, CentOS 7/8)
* [Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)
* [Java IDE do Eclipse](https://www.eclipse.org/downloads/)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) ou [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html) apenas.
* Uma chave de assinatura do Azure para o serviço de Fala. [Obtenha uma gratuitamente](../overview.md#try-the-speech-service-for-free).
* Baixe a última versão do [SDK de Dispositivos de Fala](https://aka.ms/sdsdk-download-JRE) para Java e extraia o arquivo .zip para seu diretório de trabalho.
   > [!NOTE]
   > Este início rápido supõe que o aplicativo tenha sido extraído para /home/wcaltest/JRE-Sample-Release

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

* No RHEL/CentOS:
  
  ```sh
  sudo yum update
  sudo yum install alsa-lib openssl
  ```

  > [!NOTE]
  > - No RHEL/CentOS 7, siga as instruções sobre [como configurar o RHEL/CentOS 7 para o SDK de Fala](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - No RHEL/CentOS 8, siga as instruções em [como configurar o OpenSSL para Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

A transcrição de conversas no momento está disponível apenas para "en-US" e "zh-CN", nas regiões "centralus" e "eastasia". Você precisará ter uma chave de fala em uma dessas regiões para usar a transcrição de conversas.

Caso você planeje usar as intenções, será necessária uma assinatura do [LUIS (Reconhecimento vocal)](../../luis/luis-how-to-azure-subscription.md). Para saber mais sobre o LUIS e o reconhecimento de intenção, confira [Reconhecer intenções de fala com o LUIS, C#](../how-to-recognize-intents-from-speech-csharp.md). Um [modelo de exemplo do LUIS](https://aka.ms/sdsdk-luis) está disponível para este aplicativo.

## <a name="create-and-configure-the-project"></a>Criar e configurar o projeto

1. Inicie o Eclipse.

1. No **Inicializador do IDE do Eclipse**, no campo **Workspace**, insira o nome de um novo diretório de workspace. Em seguida, selecione **Iniciar**.

   ![Captura de tela que mostra o Inicializador do Eclipse.](../media/speech-devices-sdk/eclipse-launcher-linux.png)

1. Em alguns instantes, a janela principal do IDE do Eclipse aparece. Feche a tela de boas-vindas caso haja uma.

1. Na barra de menus do Eclipse, crie um novo projeto escolhendo **Arquivo** > **Novo** > **Projeto em Java**. Se não estiver disponível, escolha **Projeto** e, em seguida **Projeto em Java**.

1. O assistente **Novo Projeto em Java** é iniciado. **Procure** a localização do projeto de exemplo. Selecione **Concluir**.

   ![Captura de tela do assistente de Novo Projeto Java](../media/speech-devices-sdk/eclipse-new-java-project-linux.png)

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Configurar** > **Converter para Projeto Maven** no menu de contexto. Selecione **Concluir**.

   ![Captura de tela do Explorador de pacotes](../media/speech-devices-sdk/eclipse-convert-to-maven.png)

1. Abra o arquivo pom.xml e edite-o.

    No final do arquivo, antes da marca de fechamento `</project>`, crie os elementos `repositories` e `dependencies`, como mostrado aqui, e verifique se o `version` corresponde à sua versão atual:
    ```xml    
    <repositories>
         <repository>
             <id>maven-cognitiveservices-speech</id>
             <name>Microsoft Cognitive Services Speech Maven Repository</name>
             <url>https://csspeechstorage.blob.core.windows.net/maven/</url>
         </repository>
    </repositories>
 
    <dependencies>
        <dependency>
             <groupId>com.microsoft.cognitiveservices.speech</groupId>
             <artifactId>client-sdk</artifactId>
             <version>1.15.0</version>
        </dependency>
    </dependencies>
   ```

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Propriedades** e, em seguida, **Executar/Configurações de Depuração** > **Novo…** > **Aplicativo Java**. 

1. A janela **Editar Configuração** é exibida. No campo **Nome**, insira **Principal** e use **Pesquisar** para a **Classe Principal** para localizar e selecionar **com.microsoft.cognitiveservices.speech.samples.FunctionsList**.

   ![Captura de tela da configuração de inicialização da edição](../media/speech-devices-sdk/eclipse-edit-launch-configuration-linux.png)

1. Copie os binários de áudio para a arquitetura de destino, do **Linux-arm** ou do **Linux-x64** para o local do Projeto em Java, por exemplo, **/home/wcaltest/JRE-Sample-Release**

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

1. A palavra-chave padrão (palavra-chave) é "Computador". Você também pode tentar uma das outras palavras-chave fornecidas, assim como "Máquina" ou "Assistente". Os arquivos de recurso para essas alternativas de palavra-chave estão no SDK de Dispositivos de Fala, na pasta da palavra-chave. Por exemplo, `/home/wcaltest/JRE-Sample-Release/keyword/Computer` contém os arquivos usados para a palavra-chave "Computador".

   > [!TIP]
   > Você também pode [criar uma palavra-chave personalizada](../custom-keyword-basics.md).

    Para usar uma nova palavra-chave, atualize a linha a seguir em `FunctionsList.java` e copie a palavra-chave para o aplicativo. Por exemplo, para usar a palavra-chave “Computador” do pacote de palavras-chave `machine.zip`:

   * Copie o arquivo de `kws.table` do pacote zip para a pasta do projeto **target/classes**.

   * Atualize o `FunctionsList.java` com o nome da palavra-chave:

     ```java
     private static final String Keyword = "Machine";
     ```

## <a name="run-the-sample-application-from-eclipse"></a>Execute o aplicativo de exemplo do Eclipse

1. Na barra de menus do Eclipse, **Executar** > **Executar** 

1. O aplicativo de exemplo do SDK de Dispositivos de Fala é iniciado e exibe as seguintes opções:

   ![Captura de tela que mostra um exemplo de aplicativo do SDK de Dispositivos de Fala e as opções.](../media/speech-devices-sdk/java-sample-app-linux.png)

1. Experimente a nova demonstração da **transcrição de conversas**. Comece a transcrição com **Sessão** > **Iniciar Sessão**. Por padrão, todas as pessoas são convidados. No entanto, se você tiver assinaturas de voz do participante, elas poderão ser colocadas em `participants.properties` na pasta do projeto **target/classes**. Para gerar a assinatura de voz, examine [Transcrever conversas (SDK)](../how-to-use-conversation-transcription.md).

   ![Captura de tela que mostra um aplicativo de demonstração de transcrição de conversas.](../media/speech-devices-sdk/cts-sample-app-linux.png)

## <a name="create-and-run-standalone-the-application"></a>Criar e executar o aplicativo autônomo

1. No **Explorador de pacotes**, clique com o botão direito do mouse em seu projeto. Escolha **Exportar**. 
1. A janela **Exportar** é exibida. Expanda **Java** e selecione **Arquivo JAR executável** e, em seguida, selecione **Avançar**.

   ![Captura de tela que mostra a janela Exportar.](../media/speech-devices-sdk/eclipse-export-linux.png) 

1. A janela **Exportação de Arquivo JAR Executável** é exibida. Escolha um **Destino de exportação** para o aplicativo e, em seguida, selecione **Concluir**.
 
   ![Captura de tela que mostra a janela Exportação de Arquivo JAR Executável.](../media/speech-devices-sdk/eclipse-export-jar-linux.png)

1. Coloque `kws.table` e `participants.properties` na pasta de destino escolhida acima, pois o aplicativo precisa desses arquivos.

1. Defina LD_LIBRARY_LIB para a pasta que contém os arquivos *.so

     ```bash
     export LD_LIBRARY_PATH=/home/wcaltest/JRE-Sample-Release
     ```

1. Para executar o aplicativo autônomo

     ```bash
     java -jar SpeechDemo.jar
     ```