---
title: 'Início Rápido: Controlar um dispositivo no Hub IoT do Azure (Android) | Microsoft Docs'
description: Neste início rápido, você executa dois aplicativos Java de exemplo. Um deles é um aplicativo de serviço que pode controlar remotamente os dispositivos conectados ao seu hub. O outro aplicativo é executado em um dispositivo físico ou simulado, conectado ao seu hub, que pode ser controlado remotamente.
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.author: wesmc
ms.openlocfilehash: d125328d903b419aa81c54ffecf1f549d4cb4fe2
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330798"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-android"></a>Início Rápido: Controlar um dispositivo conectado a um hub IoT (Android)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

O Hub IoT é um serviço do Azure que permite a ingestão de grandes volumes de telemetria de seus dispositivos IoT na nuvem e o gerenciamento de seus dispositivos pela nuvem. Neste início rápido, você usa um *método direto* para controlar um dispositivo simulado conectado ao seu hub IoT. Você pode usar métodos diretos para alterar remotamente o comportamento de um dispositivo conectado ao seu hub IoT.

O início rápido usa dois aplicativos previamente escritos em Java:

* Um aplicativo de dispositivo simulado que responde aos métodos diretos chamados de um aplicativo de serviço de back-end. Para receber as chamadas de método direto, esse aplicativo se conecta a um ponto de extremidade específico do dispositivo em seu hub IoT.

* Um aplicativo de serviço que chama o método direto no dispositivo Android. Para chamar um método direto em um dispositivo, esse aplicativo se conecta a um ponto de extremidade do lado do serviço em seu hub IoT.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Android Studio de https://developer.android.com/studio/. Para obter mais informações sobre a instalação do Android Studio, confira [instalação do Android](https://developer.android.com/studio/install).

* O SDK do Android 27 é usado pelo exemplo deste artigo.

* Execute o comando a seguir para adicionar a Extensão do Microsoft Azure IoT para a CLI do Azure à instância do Cloud Shell. A Extensão de IoT adiciona comandos específicos do Hub IoT, do IoT Edge e do DPS (Serviço de Provisionamento de Dispositivos) no IoT à CLI do Azure.

   ```azurecli-interactive
   az extension add --name azure-cli-iot-ext
   ```

* Dois aplicativos de exemplo são necessários para este início rápido: o [aplicativo Android de exemplo do SDK do dispositivo](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/device/AndroidSample) e o [aplicativo Android de exemplo do SDK do serviço](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/iot-hub/Samples/service/AndroidSample). Ambos os exemplos fazem parte do repositório azure-iot-samples-java no GitHub. Baixe ou clone o repositório [azure-iot-samples-java](https://github.com/Azure-Samples/azure-iot-samples-java).

## <a name="create-an-iot-hub"></a>Crie um hub IoT

Se tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um Hub IoT](quickstart-send-telemetry-android.md) anterior, ignore esta etapa e use o hub IoT já criado.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar um dispositivo

Se tiver concluído o [Início Rápido: Enviar telemetria de um dispositivo para um hub IoT](quickstart-send-telemetry-android.md) anterior, você poderá ignorar esta etapa e usar o mesmo dispositivo registrado anteriormente.

Um dispositivo deve ser registrado no hub IoT antes de poder se conectar. Neste início rápido, você usa o Azure Cloud Shell para registrar um dispositivo simulado.

1. Execute o comando a seguir no Azure Cloud Shell para criar a identidade do dispositivo.

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

   **MyAndroidDevice**: esse valor é o nome fornecido para o dispositivo registrado. Use o MyAndroidDevice conforme mostrado. Se você escolher um nome diferente para seu dispositivo, talvez precise usar esse nome ao longo deste artigo e atualizar o nome de dispositivo nos aplicativos de exemplo antes de executá-los.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyAndroidDevice
    ```

2. Execute os seguintes comandos no Azure Cloud Shell para obter a _cadeia de conexão de dispositivo_ referente ao dispositivo que você acabou de registrar:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyAndroidDevice \
      --output table
    ```

    Tome nota da cadeia de conexão do dispositivo, que se parece com:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}`

    Você usará esse valor posteriormente no início rápido.

## <a name="retrieve-the-service-connection-string"></a>Recuperar a cadeia de conexão de serviço

Você também precisa de uma _cadeia de conexão de serviço_ para permitir que os aplicativos de serviço de back-end se conectem ao hub IoT para executar os métodos e recuperar as mensagens. O comando abaixo recupera a cadeia de conexão de serviço para o hub IoT:

**YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
```

Tome nota da cadeia de conexão de serviço, que se parece com:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Você usará esse valor posteriormente no início rápido. A cadeia de conexão do serviço é diferente da cadeia de conexão do dispositivo.

## <a name="listen-for-direct-method-calls"></a>Escutar chamadas de método direto

O aplicativo de exemplo do SDK do dispositivo pode ser executado em um dispositivo Android físico ou em um emulador do Android. O exemplo se conecta a um ponto de extremidade específico do dispositivo no hub IoT, envia telemetria simulada e escuta chamadas de método direto de seu hub. Neste início rápido, a chamada de método direto do hub informa ao dispositivo para alterar o intervalo de envio da telemetria. O dispositivo simulado envia uma confirmação para o hub depois de executar o método direto.

1. Abra o projeto Android de exemplo do GitHub no Android Studio. O projeto está localizado no diretório a seguir da sua cópia baixada ou clonada do repositório [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\device\AndroidSample

2. No Android Studio, abra *gradle.properties* do projeto de exemplo e substitua o espaço reservado **Device_Connection_String** pela cadeia de conexão de dispositivo que você anotou anteriormente.

    ```
    DeviceConnectionString=HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyAndroidDevice;SharedAccessKey={YourSharedAccessKey}
    ```

3. No Android Studio, clique em **Arquivo** > **Sincronizar Projeto com Arquivos do Gradle**. Verifique se que a compilação foi concluída.

   > [!NOTE]
   > Se a sincronização de projeto falhar, poderá ser por um dos seguintes motivos:
   >
   > * As versões do plug-in Android Gradle e do Gradle referenciadas no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referenciar e instalar as versões corretas do plug-in e do Gradle para sua instalação.
   > * O contrato de licença para o SDK do Android não foi assinado. Siga as instruções na saída do build para assinar o contrato de licença e baixar o SDK.

4. Depois que a compilação for concluída, clique em **Executar** > **Executar “aplicativo”** . Configure o aplicativo para ser executado em um dispositivo Android físico ou em um emulador do Android. Para obter mais informações sobre como executar um aplicativo Android em um dispositivo físico ou emulador, confira [Executar seu aplicativo](https://developer.android.com/training/basics/firstapp/running-app).

5. Depois que o aplicativo for carregado, clique no botão **Iniciar** para começar a enviar dados telemétricos ao Hub IoT:

    ![Captura de tela de exemplo de aplicativo de dispositivo cliente Android](media/quickstart-control-device-android/sample-screenshot.png)

Esse aplicativo precisa ser executado um emulador ou dispositivo físico enquanto você executa o exemplo do SDK de serviço para atualizar o intervalo de telemetria durante o tempo de execução.

## <a name="read-the-telemetry-from-your-hub"></a>Ler a telemetria do hub

Nesta seção, você usará o Azure Cloud Shell com a [extensão de IoT](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest) para monitorar as mensagens de dispositivo enviadas pelo dispositivo Android.

1. Usando o Azure Cloud Shell, execute o seguinte comando para se conectar e ler mensagens do hub IoT:

   **YourIoTHubName**: substitua o espaço reservado abaixo pelo nome escolhido para o hub IoT.

    ```azurecli-interactive
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

    A captura de tela abaixo mostra a saída à medida que o Hub IoT recebe a telemetria enviada pelo dispositivo Android:

      ![Leia as mensagens do dispositivo usando a CLI do Azure](media/quickstart-control-device-android/read-data.png)

Por padrão, o aplicativo de telemetria está enviando dados telemétricos do dispositivo Android a cada cinco segundos. Na próxima seção, você usará uma chamada de método direto para atualizar o intervalo de telemetria do dispositivo IoT Android.

## <a name="call-the-direct-method"></a>Chamar o método direto

O aplicativo de serviço se conecta a um ponto de extremidade do lado do serviço em seu Hub IoT. O aplicativo faz chamadas de método direto para um dispositivo por meio de seu hub IoT e espera confirmações.

Execute esse aplicativo em um dispositivo Android físico ou emulador do Android diferente.

Um aplicativo de serviço de back-end do Hub IoT normalmente é executado na nuvem, onde é mais fácil reduzir os riscos associados à cadeia de conexão confidencial que controla todos os dispositivos em um Hub IoT. Neste exemplo, estamos executando-o como um aplicativo Android somente para fins de demonstração. As outras versões de linguagem deste início rápido fornecem outros exemplos que se alinham mais estritamente com um aplicativo de serviço de back-end.

1. Abra o projeto Android de exemplo de serviço do GitHub no Android Studio. O projeto está localizado no diretório a seguir da sua cópia baixada ou clonada do repositório [azure-iot-sample-java](https://github.com/Azure-Samples/azure-iot-samples-java).

        \azure-iot-samples-java\iot-hub\Samples\service\AndroidSample

2. No Android Studio, abra *gradle.properties* do exemplo de projeto e atualize o valor das propriedades **ConnectionString** e **DeviceId** com a cadeia de conexão que você anotou anteriormente e a ID do dispositivo Android registrado.

    ```
    ConnectionString=HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}
    DeviceId=MyAndroidDevice
    ```

3. No Android Studio, clique em **Arquivo** > **Sincronizar Projeto com Arquivos do Gradle**. Verifique se que a compilação foi concluída.

   > [!NOTE]
   > Se a sincronização de projeto falhar, poderá ser por um dos seguintes motivos:
   >
   > * As versões do plug-in Android Gradle e do Gradle referenciadas no projeto estão desatualizadas para a sua versão do Android Studio. Siga [estas instruções](https://developer.android.com/studio/releases/gradle-plugin) para referenciar e instalar as versões corretas do plug-in e do Gradle para sua instalação.
   > * O contrato de licença para o SDK do Android não foi assinado. Siga as instruções na saída do build para assinar o contrato de licença e baixar o SDK.

4. Depois que a compilação for concluída, clique em **Executar** > **Executar “aplicativo”** . Configure o aplicativo para ser executado em um dispositivo Android físico ou em um emulador do Android diferente. Para obter mais informações sobre como executar um aplicativo Android em um dispositivo físico ou emulador, confira [Executar seu aplicativo](https://developer.android.com/training/basics/firstapp/running-app).

5. Depois que o aplicativo for carregado, atualize o valor **Definir Intervalo de Mensagens** como **1000** e clique em **Invocar**.

    O intervalo de mensagens de telemetria é da ordem de milissegundos. O intervalo de telemetria padrão do exemplo de dispositivo está definido como cinco segundos. Essa alteração atualizará o dispositivo IoT Android para que a telemetria seja enviada a cada segundo.

    ![Inserir intervalo de telemetria](media/quickstart-control-device-android/enter-telemetry-interval.png)

6. O aplicativo receberá uma confirmação indicando se o método foi executado com êxito ou não.

    ![Confirmação de método direto](media/quickstart-control-device-android/direct-method-ack.png)

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você já chamou um método direto em um dispositivo de um aplicativo de back-end e respondeu à chamada de método direto em um aplicativo de dispositivo simulado.

Para saber como rotear mensagens de dispositivo para nuvem para destinos diferentes na nuvem, continue n próximo tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Encaminhar a telemetria para pontos de extremidade diferentes para processamento](tutorial-routing.md)
