---
title: Agendar trabalhos com o Hub IoT do Azure (Java) | Microsoft Docs
description: Como agendar um trabalho do Hub IoT do Azure para invocar um método direto e definir uma propriedade desejada em vários dispositivos. Use o SDK do dispositivo IoT do Azure para Java para implementar os aplicativos de dispositivo e o SDK do serviço do IoT do Azure para Java para implementar um aplicativo de serviço que executa o trabalho.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/10/2017
ms.openlocfilehash: ce7c70eef2d030a956ca5cc1ea85aff008074edb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863875"
---
# <a name="schedule-and-broadcast-jobs-java"></a>Agendar e difundir trabalhos (Java)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Use o Hub IoT do Azure para agendar e controlar os trabalhos que atualizam milhões de dispositivos. Use trabalhos para:

* Atualizar as propriedades desejadas
* Marcas de atualização
* Chamar métodos diretos

Um trabalho encapsula uma dessas ações e controla a execução em um conjunto de dispositivos. Uma consulta dispositivo gêmeo define o conjunto de dispositivos para os quais o trabalho é executado. Por exemplo, um aplicativo de back-end pode usar um trabalho para invocar um método direto em 10.000 dispositivos que reinicie os dispositivos. Você especifica o conjunto de dispositivos com uma consulta de dispositivo gêmeo e agenda o trabalho para execução futura. O trabalho controla o andamento conforme cada um dos dispositivos recebe e executa o método direto de reinicialização.

Para saber mais sobre cada uma dessas capacidades, consulte:

* Dispositivo gêmeo e propriedades: [Introdução aos dispositivos gêmeos](iot-hub-java-java-twin-getstarted.md)

* Métodos diretos: [Guia do desenvolvedor do IoT Hub – métodos diretos](iot-hub-devguide-direct-methods.md) e [Tutorial: Usar métodos diretos](quickstart-control-device-java.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Este tutorial mostra como:

* Crie um aplicativo de dispositivo que implemente um método chamado **lockDoor**. O aplicativo de dispositivo também recebe as alterações de propriedade desejadas do aplicativo de back-end.

* Criar um aplicativo de back-end que gere um trabalho para chamar o método direto **lockDoor** em vários dispositivos. Outro trabalho envia as atualizações de propriedade desejadas para vários dispositivos.

Ao final deste tutorial, você terá um aplicativo de dispositivo de console Java e um aplicativo de back-end do console Java:

**simulated-device**, que se conecta ao seu hub IoT, implementa o método direto **lockDoor** e manipula as alterações de propriedade desejadas.

**schedule-jobs**, que usa trabalhos para chamar o método direto **lockDoor** e atualizar as propriedades desejadas do dispositivo gêmeo em vários dispositivos.

> [!NOTE]
> O artigo [SDKs de IoT do Azure](iot-hub-devguide-sdks.md) apresenta informações sobre os SDKs de IoT do Azure que você pode usar para criar dispositivos e aplicativos de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará:

* A versão mais recente do [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Recuperar a cadeia conexão para o hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

Você também pode usar a ferramenta [IoT extension for Azure CLI](https://github.com/Azure/azure-iot-cli-extension) para adicionar um dispositivo ao hub IoT.

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço

Nesta seção, você cria um aplicativo de console Java que usa trabalhos para:

* Chamar o método direto **lockDoor** em vários dispositivos.

* Enviar as propriedades desejadas para vários dispositivos.

Para criar o aplicativo:

1. No computador de desenvolvimento, crie uma pasta vazia chamada `iot-java-schedule-jobs`.

2. Na pasta `iot-java-schedule-jobs`, crie um projeto Maven chamado **schedule-jobs** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=schedule-jobs -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

3. No prompt de comando, navegue até a pasta `schedule-jobs`.

4. Usando um editor de texto, abra o arquivo `pom.xml` na pasta `schedule-jobs` e adicione a dependência a seguir ao nó **dependencies**. Essa dependência permite que você use o pacote **iot-service-client** em seu aplicativo para se comunicar com seu hub IoT:

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

6. Salve e feche o arquivo `pom.xml`.

7. Usando um editor de texto, abra o arquivo `schedule-jobs\src\main\java\com\mycompany\app\App.java`.

8. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Pair;
    import com.microsoft.azure.sdk.iot.service.devicetwin.Query;
    import com.microsoft.azure.sdk.iot.service.devicetwin.SqlQuery;
    import com.microsoft.azure.sdk.iot.service.jobs.JobClient;
    import com.microsoft.azure.sdk.iot.service.jobs.JobResult;
    import com.microsoft.azure.sdk.iot.service.jobs.JobStatus;

    import java.util.Date;
    import java.time.Instant;
    import java.util.HashSet;
    import java.util.Set;
    import java.util.UUID;
    ```

9. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua `{youriothubconnectionstring}` pela cadeia de conexão do hub IoT anotada na seção *Criar um Hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    // How long the job is permitted to run without
    // completing its work on the set of devices
    private static final long maxExecutionTimeInSeconds = 30;
    ```

10. Adicione o método a seguir à classe **Aplicativo** para agendar um trabalho para atualizar as propriedades **Prédio** e **Andar** desejadas no dispositivo gêmeo:

    ```java
    private static JobResult scheduleJobSetDesiredProperties(JobClient jobClient, String jobId) {
      DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
      Set<Pair> desiredProperties = new HashSet<Pair>();
      desiredProperties.add(new Pair("Building", 43));
      desiredProperties.add(new Pair("Floor", 3));
      twin.setDesiredProperties(desiredProperties);
      // Optimistic concurrency control
      twin.setETag("*");

      // Schedule the update twin job to run now
      // against a single device
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleUpdateTwin(jobId, 
          "deviceId='" + deviceId + "'",
          twin,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling desired properties job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    }
    ```

11. Para agendar um trabalho para chamar o método **lockDoor**, adicione o método a seguir à classe **Aplicativo**:

    ```java
    private static JobResult scheduleJobCallDirectMethod(JobClient jobClient, String jobId) {
      // Schedule a job now to call the lockDoor direct method
      // against a single device. Response and connection
      // timeouts are set to 5 seconds.
      System.out.println("Schedule job " + jobId + " for device " + deviceId);
      try {
        JobResult jobResult = jobClient.scheduleDeviceMethod(jobId,
          "deviceId='" + deviceId + "'",
          "lockDoor",
          5L, 5L, null,
          new Date(),
          maxExecutionTimeInSeconds);
        return jobResult;
      } catch (Exception e) {
        System.out.println("Exception scheduling direct method job: " + jobId);
        System.out.println(e.getMessage());
        return null;
      }
    };
    ```

12. Para monitorar um trabalho, adicione o seguinte método à classe **Aplicativo**:

    ```java
    private static void monitorJob(JobClient jobClient, String jobId) {
      try {
        JobResult jobResult = jobClient.getJob(jobId);
        if(jobResult == null)
        {
          System.out.println("No JobResult for: " + jobId);
          return;
        }
        // Check the job result until it's completed
        while(jobResult.getJobStatus() != JobStatus.completed)
        {
          Thread.sleep(100);
          jobResult = jobClient.getJob(jobId);
          System.out.println("Status " + jobResult.getJobStatus() + " for job " + jobId);
        }
        System.out.println("Final status " + jobResult.getJobStatus() + " for job " + jobId);
      } catch (Exception e) {
        System.out.println("Exception monitoring job: " + jobId);
        System.out.println(e.getMessage());
        return;
      }
    }
    ```

13. Para consultar detalhes dos trabalhos que você executou, adicione o seguinte método:

    ```java
    private static void queryDeviceJobs(JobClient jobClient, String start) throws Exception {
      System.out.println("\nQuery device jobs since " + start);

      // Create a jobs query using the time the jobs started
      Query deviceJobQuery = jobClient
          .queryDeviceJob(SqlQuery.createSqlQuery("*", SqlQuery.FromType.JOBS, "devices.jobs.startTimeUtc > '" + start + "'", null).getQuery());

      // Iterate over the list of jobs and print the details
      while (jobClient.hasNextJob(deviceJobQuery)) {
        System.out.println(jobClient.getNextJob(deviceJobQuery));
      }
    }
    ```

14. Atualize a assinatura do método **principal** para incluir a seguinte cláusula `throws`:

    ```java
    public static void main( String[] args ) throws Exception
    ```

15. Para executar e monitorar dois trabalhos em sequência, adicione o seguinte código ao método **main**:

    ```java
    // Record the start time
    String start = Instant.now().toString();

    // Create JobClient
    JobClient jobClient = JobClient.createFromConnectionString(iotHubConnectionString);
    System.out.println("JobClient created with success");

    // Schedule twin job desired properties
    // Maximum concurrent jobs is 1 for Free and S1 tiers
    String desiredPropertiesJobId = "DPCMD" + UUID.randomUUID();
    scheduleJobSetDesiredProperties(jobClient, desiredPropertiesJobId);
    monitorJob(jobClient, desiredPropertiesJobId);

    // Schedule twin job direct method
    String directMethodJobId = "DMCMD" + UUID.randomUUID();
    scheduleJobCallDirectMethod(jobClient, directMethodJobId);
    monitorJob(jobClient, directMethodJobId);

    // Run a query to show the job detail
    queryDeviceJobs(jobClient, start);

    System.out.println("Shutting down schedule-jobs app");
    ```

16. Salve e feche o arquivo `schedule-jobs\src\main\java\com\mycompany\app\App.java`

17. Compile o aplicativo **schedule-jobs** e corrija eventuais erros. No prompt de comando, navegue até a pasta `schedule-jobs` e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="create-a-device-app"></a>Criar um aplicativo de dispositivo

Nesta seção, você deve criar um aplicativo de console de Java que manipule as propriedades desejadas enviadas do Hub IoT e implemente a chamada de método direto.

1. Na pasta `iot-java-schedule-jobs`, crie um projeto Maven denominado **simulated-device** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

2. No prompt de comando, navegue até a pasta `simulated-device`.

3. Usando um editor de texto, abra o arquivo `pom.xml` na pasta `simulated-device` e adicione as dependências a seguir ao nó de **dependências**. Essa dependência permite que você use o pacote **iot-device-client** em seu aplicativo para se comunicar com seu hub IoT:

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

5. Salve e feche o arquivo `pom.xml`.

6. Usando um editor de texto, abra o arquivo `simulated-device\src\main\java\com\mycompany\app\App.java`.

7. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

8. Adicione as seguintes variáveis no nível da classe à classe **App** . Substituir `{youriothubname}` pelo nome do hub IoT e `{yourdevicekey}` pelo valor da chave do dispositivo gerado na seção *Criar uma identidade do dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Este aplicativo de exemplo usa a variável **protocol** quando cria uma instância de um objeto **DeviceClient**.

9. Para imprimir notificações do dispositivo gêmeo para o console, adicione a seguinte classe aninhada à classe **App**:

    ```java
    // Handler for device twin operation notifications from IoT Hub
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    ```

10. Para imprimir notificações do método direto para o console, adicione a seguinte classe aninhada à classe **App**:

    ```java
    // Handler for direct method notifications from IoT Hub
    protected static class DirectMethodStatusCallback implements IotHubEventCallback {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to direct method operation with status " + status.name());
      }
    }
    ```

11. Para manipular chamadas de método direto do Hub IoT, adicione a seguinte classe aninhada à classe **App**:

    ```java
    // Handler for direct method calls from IoT Hub
    protected static class DirectMethodCallback
        implements DeviceMethodCallback {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context) {
        DeviceMethodData deviceMethodData;
        switch (methodName) {
          case "lockDoor": {
            System.out.println("Executing direct method: " + methodName);
            deviceMethodData = new DeviceMethodData(METHOD_SUCCESS, "Executed direct method " + methodName);
            break;
          }
          default: {
            deviceMethodData = new DeviceMethodData(METHOD_NOT_DEFINED, "Not defined direct method " + methodName);
          }
        }
        // Notify IoT Hub of result
        return deviceMethodData;
      }
    }
    ```

12. Atualize a assinatura do método **principal** para incluir a seguinte cláusula `throws`:

    ```java
    public static void main( String[] args ) throws IOException, URISyntaxException
    ```

13. Adicione o seguinte código ao método **principal** para:
    * Criar um cliente de dispositivo para se comunicar com o Hub IoT.
    * Criar um objeto **Dispositivo** para armazenar as propriedades do dispositivo gêmeo.

    ```java
    // Create a device client
    DeviceClient client = new DeviceClient(connString, protocol);

    // An object to manage device twin desired and reported properties
    Device dataCollector = new Device() {
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context)
      {
        System.out.println("Received desired property change: " + propertyKey + " " + propertyValue);
      }
    };
    ```

14. Para iniciar os serviços de cliente do dispositivo, adicione o seguinte código ao método **main**:

    ```java
    try {
      // Open the DeviceClient
      // Start the device twin services
      // Subscribe to direct method calls
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
    } catch (Exception e) {
      System.out.println("Exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

15. Para aguardar o usuário pressionar a tecla **Enter** antes de desligar, adicione o seguinte código ao final do método **main**:

    ```java
    // Close the app
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    dataCollector.clean();
    client.closeNow();
    scanner.close();
    ```

16. Salve e feche o arquivo `simulated-device\src\main\java\com\mycompany\app\App.java`.

17. Compile o aplicativo **simulated-device** e corrija os erros. No prompt de comando, navegue até a pasta `simulated-device` e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos de console.

1. Em um prompt de comando na pasta `simulated-device`, execute o seguinte comando para iniciar o aplicativo de dispositivo escutando alterações de propriedade desejadas e chamadas de método direto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![O cliente do dispositivo é iniciado](./media/iot-hub-java-java-schedule-jobs/device-app-1.png)

2. Em um prompt de comando na pasta `schedule-jobs`, execute o comando a seguir para executar o aplicativo de serviço **schedule-jobs** para executar dois trabalhos. O primeiro define os valores de propriedade desejados, o segundo chama o método direto:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![O serviço de aplicativo de Hub IoT Java cria dois trabalhos](./media/iot-hub-java-java-schedule-jobs/service-app-1.png)

3. O aplicativo de dispositivo manipula a alteração da propriedade desejada e a chamada de método direto:

    ![O dispositivo cliente responde às alterações](./media/iot-hub-java-java-schedule-jobs/device-app-2.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você criou um aplicativo de back-end para executar os dois trabalhos. O primeiro trabalho definiu valores de propriedade desejados e o segundo trabalho chamou um método direto.

Veja os recursos a seguir para saber como:

* Envie telemetria de dispositivos com o tutorial [Introdução ao Hub IoT](quickstart-send-telemetry-java.md).

* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário) com o [usar métodos diretos](quickstart-control-device-java.md) tutorial.s