---
title: Introdução ao gerenciamento de dispositivo do Hub IoT do Azure (Java) | Microsoft Docs
description: Como usar o gerenciamento de dispositivos do Hub IoT do Azure para iniciar uma reinicialização do dispositivo remoto. Use o SDK do dispositivo IoT do Azure para Java para implementar um aplicativo de dispositivo simulado que inclui um método direto e o SDK do serviço do Azure IoT para Java para implementar um aplicativo de serviço que invoca o método direto.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: c8528ac81f2248e417d7d25d0f3c2650845c3d7d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404312"
---
# <a name="get-started-with-device-management-java"></a>Introdução ao gerenciamento de dispositivos (Java)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Este tutorial mostra como:

* Usar o portal do Azure para criar um Hub IoT e criar uma identidade de dispositivo em seu Hub IoT.

* Crie um aplicativo de dispositivo simulado que implementa um método direto para reiniciar o dispositivo. Métodos diretos são invocados da nuvem.

* Crie um aplicativo que chama o método de reinicialização direta no aplicativo de dispositivo simulado por meio do hub IoT. Esse aplicativo monitora as propriedades relatadas do dispositivo para ver quando a operação de reinicialização é concluída.

Ao fim deste tutorial, você tem dois aplicativos de console do Java:

**simulated-device**. Este aplicativo:

* Conecta-se ao hub IoT com a identidade de dispositivo criada anteriormente.

* Recebe uma chamada de método direto de reinicialização.

* Simula uma reinicialização física.

* Relata a hora da última reinicialização por meio de uma propriedade relatada.

**disparar reinicialização**. Este aplicativo:

* Chama um método direto no aplicativo de dispositivo simulado.

* Exibe a resposta à chamada de método direto enviada pelo dispositivo simulado.

* Exibe as propriedades relatadas atualizadas.

> [!NOTE]
> Para obter informações sobre os SDKs que você pode usar para criar aplicativos para serem executados em dispositivos e seu back-end da solução, consulte [SDKs do IoT do Azure](iot-hub-devguide-sdks.md).

Para concluir este tutorial, você precisará:

* Java SE 8. <br/> [Prepare seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md) descreve como instalar o Java para este tutorial no Windows ou no Linux.

* Maven 3.  <br/> [Prepare seu ambiente de desenvolvimento](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md) descreve como instalar o [Maven](https://maven.apache.org/what-is-maven.html) para este tutorial no Windows ou no Linux.

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Disparar uma reinicialização remota no dispositivo usando um método direto

Nesta seção, você cria um aplicativo de console Java que:

1. Invoca um método direto de reinicialização no aplicativo de dispositivo simulado.

2. Exibe a resposta.

3. Sonda as propriedades relatadas enviadas do dispositivo para determinar quando a reinicialização está concluída.

Esse aplicativo de console se conecta ao Hub IoT para invocar o método direto e ler as propriedades relatadas.

1. Crie uma pasta vazia chamada dm-get-started.

2. Na pasta dm-get-started, crie um projeto Maven chamado **trigger-reboot** usando o comando a seguir no prompt de comando. Veja a seguir um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=trigger-reboot -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. No prompt de comando, navegue até a pasta trigger-reboot.

4. Usando um editor de texto, abra o arquivo pom.xml na pasta trigger-reboot e adicione a dependência a seguir ao nó **dependencies**. Essa dependência permite que você use o pacote iot-service-client em seu aplicativo para se comunicar com seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **iot-service-client** usando a [pesquisa do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

5. Adicione o seguinte nó **buid** após o nó **dependencies**. Esta configuração instrui o Maven a usar Java 1.8 para compilar o aplicativo:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

6. Salve e feche o arquivo pom.xml.

7. Usando um editor de texto, abra o arquivo de origem trigger-reboot\src\main\java\com\mycompany\app\App.java.

8. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

9. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua `{youriothubconnectionstring}` pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "reboot";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

10. Para implementar um thread que lê as propriedades relatadas do dispositivo gêmeo a cada 10 segundos, adicione a seguinte classe aninhada à classe de **Aplicativo**:

    ```java
    private static class ShowReportedProperties implements Runnable {
      public void run() {
        try {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          while (true) {
            System.out.println("Get reported properties from device twin");
            deviceTwins.getTwin(twinDevice);
            System.out.println(twinDevice.reportedPropertiesToString());
            Thread.sleep(10000);
          }
        } catch (Exception ex) {
          System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
      }
    }
    ```

11. Modifique a assinatura do método **principal** para lançar a seguinte exceção:

    ```java
    public static void main(String[] args) throws IOException
    ```

12. Para invocar o método direto de reinicialização do dispositivo simulado, adicione o seguinte código ao método **principal**:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      System.out.println("Invoke reboot direct method");
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, null);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }
      System.out.println("Invoked reboot on device");
      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }
    ```

13. Para iniciar o thread para sondar as propriedades relatadas do dispositivo simulado, adicione o seguinte código ao método **principal**:

    ```java
    ShowReportedProperties showReportedProperties = new ShowReportedProperties();
    ExecutorService executor = Executors.newFixedThreadPool(1);
    executor.execute(showReportedProperties);
    ```

14. Para que você possa interromper o aplicativo, adicione o seguinte código ao método **principal**:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    System.out.println("Shutting down sample...");
    ```

15. Salve e feche o arquivo trigger-reboot\src\main\java\com\mycompany\app\App.java.

16. Criar o aplicativo de back-end **trigger-reboot** e corrigir os erros. No prompt de comando, navegue até a pasta trigger-reboot e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console Java que simula um dispositivo. O aplicativo escuta a chamada de método direto de reinicialização do hub IoT e responde imediatamente à chamada. Em seguida, o aplicativo é suspenso durante algum tempo para simular o processo de reinicialização antes de usar uma propriedade relatada para notificar o aplicativo de back-end **trigger-reboot** de que a reinicialização foi concluída.

1. Na pasta dm-get-started, crie um projeto Maven denominado **simulated-device** usando o comando a seguir no prompt de comando. Veja a seguir um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. No prompt de comando, navegue até a pasta simulated-device.

3. Usando um editor de texto, abra o arquivo pom.xml na pasta simulated-device e adicione a dependência a seguir ao nó **dependencies**. Essa dependência permite que você use o pacote iot-service-client em seu aplicativo para se comunicar com seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **iot-device-client** usando a [pesquisa do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

4. Adicione o seguinte nó **buid** após o nó **dependencies**. Esta configuração instrui o Maven a usar Java 1.8 para compilar o aplicativo:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

5. Salve e feche o arquivo pom.xml.

6. Usando um editor de texto, abra o arquivo de origem simulated-device\src\main\java\com\mycompany\app\App.java.

7. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    ```

7. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua `{yourdeviceconnectionstring}` pela cadeia de conexão de dispositivo anotada na seção *Criar uma identidade de dispositivo*:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    ```

8. Para implementar um manipulador de retorno de chamada para eventos de status do método direto, adicione a seguinte classe aninhada à classe de **Aplicativo**:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

9. Para implementar um manipulador de retorno de chamada para eventos de status de dispositivo gêmeo, adicione a seguinte classe aninhada à classe de **Aplicativo**:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
        public void execute(IotHubStatusCode status, Object context)
        {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

10. Para implementar um manipulador de retorno de chamada para eventos de propriedade, adicione a seguinte classe aninhada à classe de **Aplicativo**:

    ```java
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

11. Para implementar um thread para simular a reinicialização do dispositivo, adicione a seguinte classe aninhada à classe de **Aplicativo**. O thread é suspenso por cinco segundos e define a propriedade relatada **lastReboot**:

    ```java
    protected static class RebootDeviceThread implements Runnable {
      public void run() {
        try {
          System.out.println("Rebooting...");
          Thread.sleep(5000);
          Property property = new Property("lastReboot", LocalDateTime.now());
          Set<Property> properties = new HashSet<Property>();
          properties.add(property);
          client.sendReportedProperties(properties);
          System.out.println("Rebooted");
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

12. Para implementar o método direto no dispositivo, adicione a seguinte classe aninhada à classe de **Aplicativo**. Quando o aplicativo simulado recebe uma chamada para o  método direto de reinicialização, ele retorna uma confirmação para o chamador e, em seguida, inicia um thread para processar a reinicialização:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "reboot" :
          {
            int status = METHOD_SUCCESS;
            System.out.println("Received reboot request");
            deviceMethodData = new DeviceMethodData(status, "Started reboot");
            RebootDeviceThread rebootThread = new RebootDeviceThread();
            Thread t = new Thread(rebootThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

13. Modifique a assinatura do método **principal** para lançar as seguintes exceções:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

14. Para instanciar um **DeviceClient**, adicione o seguinte código ao método **principal**:

    ```java
    System.out.println("Starting device client sample...");
    client = new DeviceClient(connString, protocol);
    ```

15. Para começar a escutar chamadas de método diretas, adicione o seguinte código ao método **principal**:

    ```java
    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Subscribed to direct methods and polling for reported properties. Waiting...");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

16. Para desligar o simulador de dispositivo, adicione o seguinte código ao método **principal**:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

17. Salve e feche o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

18. Crie o aplicativo de back-end **simulated-device** e corrija os erros. No prompt de comando, navegue até a pasta simulated-device e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando, na pasta simulated-device, execute o seguinte comando para começar a escutar chamadas de método de reinicialização de seu Hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplicativo de dispositivo simulado de Java do Hub IoT para escutar chamadas de método direto de reinicialização](./media/iot-hub-java-java-device-management-getstarted/launchsimulator.png)

2. Em um prompt de comando na pasta trigger-reboot, execute o seguinte comando para chamar o método de reinicialização no dispositivo simulado do Hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Serviço de aplicativo Java do Hub IoT para chamar o método de reinicialização direta](./media/iot-hub-java-java-device-management-getstarted/triggerreboot.png)

3. O dispositivo simulado responde à chamada de método direto de reinicialização:

    ![O aplicativo de dispositivo simulado Java do Hub IoT responde à chamada de método direto](./media/iot-hub-java-java-device-management-getstarted/respondtoreboot.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
