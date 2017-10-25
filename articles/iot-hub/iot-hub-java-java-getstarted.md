---
title: "Introdução ao Hub IoT do Azure (Java) | Microsoft Docs"
description: "Saiba como enviar mensagens de dispositivo para nuvem para o Hub IoT do Azure SDKs da IoT para Java. Crie um dispositivo simulado e aplicativos de serviço para registrar seu dispositivo, envie mensagens e leia mensagens no hub IoT."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 70dae4a8-0e98-4c53-b5a5-9d6963abb245
ms.service: iot-hub
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 88782515b28de361338879a209e483f4fc7ab648
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-device-to-your-iot-hub-using-java"></a>Conecte seu dispositivo ao hub IoT usando Java
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Ao fim deste tutorial, você tem três aplicativos de console do Java:

* **create-device-identity**, que cria uma identidade do dispositivo e uma chave de segurança associada para conectar o aplicativo de dispositivo.
* **read-d2c-messages**, que exibe a telemetria enviada pelo aplicativo de dispositivo.
* **simulated-device**, que conecta o Hub IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria a cada segundo usando o protocolo MQTT.

> [!NOTE]
> O artigo [SDKs de IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs de IoT do Azure que você pode usar para criar aplicativos executados em dispositivos e no back-end da solução.

Para concluir este tutorial, você precisará do seguinte:

* A versão mais recente do [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como etapa final, anote o valor da **Chave primária**. Em seguida, clique em **Pontos de extremidade** e o ponto de extremidade interno de **Eventos**. Na folha **Propriedades**, anote o **nome compatível com o Hub de Eventos** e o endereço do **ponto de extremidade compatível com o Hub de Eventos**. Você precisa desses três valores quando cria o aplicativo **read-d2c-messages**.

![Folha Mensagens do Hub IoT do Portal do Azure][6]

Você criou seu Hub IoT. Você tem o nome de host do Hub IoT, a cadeia de conexão do Hub IoT, a chave primária do Hub IoT, o nome compatível com o Hub de Eventos e ponto de extremidade compatível com o Hub de Eventos necessário para concluir este tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade do dispositivo
Nesta seção, você cria um aplicativo do console do Java que cria uma identidade do dispositivo no registro de identidade em seu Hub IoT. Um dispositivo não pode se conectar ao Hub IoT, a menos que ele tenha uma entrada no Registro de identidade. Para obter mais informações, consulte a seção **"Registro de identidade"** do [Guia do Desenvolvedor do Hub IoT][lnk-devguide-identity]. Quando você executar esse aplicativo de console, ele irá gerar uma ID e chave do dispositivo exclusivas com as quais seu dispositivo poderá identificar-se ao enviar mensagens entre o dispositivo e a nuvem para o Hub IoT.

1. Crie uma pasta vazia chamada iot-java-get-started. Na pasta iot-java-get-started, crie um projeto Maven denominado **create-device-identity** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a pasta create-device-identity.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta create-device-identity e adicione a dependência a seguir ao nó **dependencies** . Essa dependência permite que você use o pacote iot-service-client no aplicativo:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **iot-service-client** usando a [pesquisa Maven][lnk-maven-service-search].

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.Device;
    import com.microsoft.azure.sdk.iot.service.RegistryManager;
   
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione as seguintes variáveis no nível da classe à classe **App**, substituindo **{cadeiadeconexãodoseuhub}** pelo valor anotado anteriormente:

    ```java
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    ```
[!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

8. Modifique a assinatura do método **main** para incluir as exceções abaixo:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```

9. Adicione o código a seguir como o corpo do método **main** . Esse código cria um dispositivo denominado *javadevice* no registro de identidade do Hub IoT, caso ele ainda não exista. Ele exibe a ID e a chave do dispositivo que são necessárias posteriormente:

    ```java
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    // Create a device that's enabled by default, 
    // with an autogenerated key.
    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      // If the device already exists.
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }

    // Display information about the
    // device you created.
    System.out.println("Device Id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Salve e feche o arquivo App.java.

11. Para compilar o aplicativo **create-device-identity** usando o Maven, execute o comando a seguir no prompt de comando na pasta create-device-identity:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

12. Para executar o aplicativo **create-device-identity** usando o Maven, execute o comando a seguir no prompt de comando na pasta create-device-identity:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Anote a **Id do dispositivo** e a **Chave do dispositivo**. Você precisará desses valores mais tarde quando criar um aplicativo que conecta o Hub IoT como um dispositivo.

> [!NOTE]
> O Registro de identidade do Hub IoT armazena apenas as identidades de dispositivo para habilitar o acesso seguro ao Hub IoT. Ele armazena as IDs e as chaves do dispositivo a usar como credenciais de segurança e um sinalizador habilitado/desabilitado que você poderá usar para desabilitar o acesso de um dispositivo individual. Se seu aplicativo precisar armazenar outros metadados específicos do dispositivo, ele deverá usar um repositório específico do aplicativo. Para saber mais, confira o [Guia de Desenvolvedor do Hub IoT][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens do dispositivo para a nuvem

Nesta seção, você cria um aplicativo do console do Java que lê mensagens do dispositivo para a nuvem do Hub IoT. Um Hub IoT expõe um ponto de extremidade compatível com o [Hub de Eventos][lnk-event-hubs-overview] para permitir que você leia mensagens do dispositivo para a nuvem. Para simplificar, este tutorial cria um leitor básico que não é adequado para uma implantação de alta taxa de transferência. O tutorial [Processar mensagens do dispositivo para a nuvem][lnk-process-d2c-tutorial] mostra como processar as mensagens do dispositivo para a nuvem em escala. O tutorial [Introdução aos Hubs de Eventos][lnk-eventhubs-tutorial] oferece informações adicionais sobre como processar as mensagens dos Hubs de Eventos e é aplicável aos pontos de extremidade compatíveis com o Hub de Eventos do Hub IoT.

> [!NOTE]
> O ponto de extremidade compatível com o Hub de Eventos para ler mensagens do dispositivo para a nuvem sempre usa o protocolo AMQP.

1. Na pasta iot-java-get-started criada na seção *Criar uma identidade de dispositivo* , crie um projeto Maven denominado **read-d2c-messages** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a pasta read-d2c-messages.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta read-d2c-messages e adicione a dependência a seguir ao nó **dependencies** . Essa dependência permite usar o pacote eventhubs-client em seu aplicativo para ler do ponto de extremidade compatível com Hubs de Eventos:

    ```xml
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.15.0</version> 
    </dependency>
    ```

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;

    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.function.*;
    ```

7. Adicione as seguintes variáveis em nível de classe à classe **App** . Substitua **{suachavehubiot}**, **{pontoextremidadecompatívelcomseuhubdeeventos}** e **{nomecompatívelcomseuhubdeeventos}** pelos valores que você anotou anteriormente:

    ```java
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Adicione o seguinte método **receiveMessages** à classe **App**. Esse método cria uma instância **EventHubClient** para conectar-se ao ponto de extremidade compatível com o Hub de Eventos e cria assincronamente uma instância **PartitionReceiver** para ler uma partição do Hub de Eventos. Ele faz um loop contínuo e imprime os detalhes da mensagem até o aplicativo ser encerrado.

    ```java
    // Create a receiver on a partition.
    private static EventHubClient receiveMessages(final String partitionId) {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      } catch (Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        // Create a receiver using the
        // default Event Hubs consumer group
        // that listens for messages from now on.
        client.createReceiver(EventHubClient.DEFAULT_CONSUMER_GROUP_NAME, partitionId, Instant.now())
          .thenAccept(new Consumer<PartitionReceiver>() {
            public void accept(PartitionReceiver receiver) {
              System.out.println("** Created receiver on partition " + partitionId);
              try {
                while (true) {
                  Iterable<EventData> receivedEvents = receiver.receive(100).get();
                  int batchSize = 0;
                  if (receivedEvents != null) {
                    System.out.println("Got some evenst");
                    for (EventData receivedEvent : receivedEvents) {
                      System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s",
                        receivedEvent.getSystemProperties().getOffset(),
                        receivedEvent.getSystemProperties().getSequenceNumber(),
                        receivedEvent.getSystemProperties().getEnqueuedTime()));
                      System.out.println(String.format("| Device ID: %s",
                        receivedEvent.getSystemProperties().get("iothub-connection-device-id")));
                      System.out.println(String.format("| Message Payload: %s",
                        new String(receivedEvent.getBytes(), Charset.defaultCharset())));
                      batchSize++;
                    }
                  }
                  System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId, batchSize));
                }
              } catch (Exception e) {
                System.out.println("Failed to receive messages: " + e.getMessage());
              }
            }
          });
        } catch (Exception e) {
          System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

   > [!NOTE]
   > Esse método usa um filtro ao criar o receptor para que o receptor leia apenas as mensagens enviadas para o Hub IoT após o início da execução do receptor. Essa técnica é útil em um ambiente de teste para que você possa ver o conjunto atual de mensagens. Em um ambiente de produção, seu código deve processar todas as mensagens. Para obter mais informações, consulte o tutorial [Como processar as mensagens do dispositivo para a nuvem do Hub IoT][lnk-process-d2c-tutorial].

9. Modifique a assinatura do método **main** para incluir a exceção mostrada abaixo:

    ```java
    public static void main( String[] args ) throws IOException
    ```

10. Adicione o seguinte código ao método **main** na classe **App**. Esse código cria as duas instâncias **EventHubClient** e **PartitionReceiver**, e permite que você feche o aplicativo quando terminar de processar as mensagens:

    ```java
    // Create receivers for partitions 0 and 1.
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    } catch (Exception e) {
      System.exit(1);
    }
    ```

    > [!NOTE]
    > Esse código pressupõe que você tenha criado seu hub IoT na camada F1 (gratuita). Um hub IoT gratuito tem duas partições chamadas "0" e "1".

11. Salve e feche o arquivo App.java.

12. Para compilar o aplicativo **read-d2c-messages** usando o Maven, execute o comando a seguir no prompt de comando na pasta read-d2c-messages:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Criar um aplicativo de dispositivo
Nesta seção, você cria um aplicativo do console do Java que simula um dispositivo que envia mensagens do dispositivo para a nuvem para um Hub IoT.

1. Na pasta iot-java-get-started, criada na seção *Criar uma identidade do dispositivo*, crie um projeto Maven denominado **simulated-device** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a pasta simulated-device.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta simulated-device e adicione as seguintes dependências ao nó **dependencies** . Essa dependência permite que você use o pacote iothub-java-client em seu aplicativo para se comunicar com o hub IoT e para serializar objetos Java para JSON:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **iot-device-client** usando a [pesquisa Maven][lnk-maven-device-search].

4. Salve e feche o arquivo pom.xml.

5. Usando um editor de texto, abra o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.google.gson.Gson;

    import java.io.*;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Adicione as seguintes variáveis no nível da classe à classe **App** . Substituir **{nomedoseuhubiot}** pelo nome do hub IoT e **{chavedoseudispositivo}** pelo valor da chave do dispositivo gerado na seção *Criar uma identidade do dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```
   
    Este aplicativo de exemplo usa a variável **protocol** quando cria uma instância de um objeto **DeviceClient**. Você pode usar o protocolo MQTT, AMQP ou HTTPS para se comunicar com o Hub IoT.

8. Adicione a seguinte classe **TelemetryDataPoint** aninhada dentro da classe **App** para especificar os dados de telemetria que seu dispositivo envia para o hub IoT:

    ```java
    private static class TelemetryDataPoint {
      public String deviceId;
      public double temperature;
      public double humidity;
   
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```
9. Adicione a seguinte classe **EventCallback** aninhada dentro da classe **App** para exibir o status de confirmação que o hub IoT retorna ao processar uma mensagem do aplicativo de dispositivo. Esse método também notifica o thread principal do aplicativo quando a mensagem tiver sido processada:
   
    ```java
    private static class EventCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
   
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Adicione a seguinte classe **MessageSender** aninhada dentro da classe **App**. O método **run** nessa classe gera dados de telemetria de exemplo a serem enviados para o hub IoT e aguarda uma confirmação antes de enviar a próxima mensagem:

    ```java
    private static class MessageSender implements Runnable {
      public void run()  {
        try {
          double minTemperature = 20;
          double minHumidity = 60;
          Random rand = new Random();
    
          while (true) {
            double currentTemperature = minTemperature + rand.nextDouble() * 15;
            double currentHumidity = minHumidity + rand.nextDouble() * 20;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.temperature = currentTemperature;
            telemetryDataPoint.humidity = currentHumidity;
    
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            msg.setProperty("temperatureAlert", (currentTemperature > 30) ? "true" : "false");
            msg.setMessageId(java.util.UUID.randomUUID().toString()); 
            System.out.println("Sending: " + msgStr);
    
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
    
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Esse método envia uma nova mensagem de dispositivo para a nuvem um segundo depois que o hub IoT confirma a mensagem anterior. A mensagem contém um objeto serializado por JSON com a deviceid e um número gerado aleatoriamente para simular um sensor de temperatura e um sensor de umidade.

11. Substitua o método **main** pelo código a seguir, que cria um thread para enviar mensagens do dispositivo para a nuvem para o hub IoT:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();
    
      MessageSender sender = new MessageSender();
    
      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);
    
      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.closeNow();
    }
    ```

12. Salve e feche o arquivo App.java.

13. Para compilar o aplicativo **simulated-device** usando o Maven, execute o comando a seguir no prompt de comando na pasta simulated-device:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

> [!NOTE]
> Para simplificar, este tutorial não implementa nenhuma política de repetição. No código de produção, implemente políticas de repetição (como uma retirada exponencial), como sugerido no artigo [Tratamento de falhas transitórias][lnk-transient-faults] do MSDN.

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando na pasta read-d2c, execute o seguinte comando para iniciar o monitoramento da primeira partição do seu hub IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de serviço do Hub IoT do Java para monitorar mensagens do dispositivo para a nuvem][7]

2. Em um prompt de comando na pasta simulated-device, execute o seguinte comando para iniciar o envio de dados de telemetria para seu hub IoT:

    ```cmd/sh
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Aplicativo de dispositivo do Hub IoT Hub do Java para enviar mensagens do dispositivo para a nuvem][8]

3. O bloco **Uso** no [portal do Azure][lnk-portal] mostra o número de mensagens enviadas para o Hub IoT:

    ![Bloco de Uso do Portal do Azure mostrando o número de mensagens enviadas ao Hub IoT][43]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você usou essa identidade do dispositivo para habilitar o aplicativo de dispositivo para enviar as mensagens entre o dispositivo e a nuvem para o hub IoT. Você também criou um aplicativo que exibe as mensagens recebidas pelo Hub IoT.

Para continuar a introdução ao Hub IoT e explorar outros cenários de IoT, confira:

* [Conectando o dispositivo][lnk-connect-device]
* [Introdução ao gerenciamento de dispositivo][lnk-device-management]
* [Introdução ao Azure IoT Edge][lnk-iot-edge]

Para saber como estender sua solução IoT e processar as mensagens entre o dispositivo e a nuvem em escala, consulte o tutorial [Processar as mensagens entre o dispositivo e a nuvem][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-eventhubs-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22
