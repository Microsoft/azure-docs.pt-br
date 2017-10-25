---
title: "Usar os métodos diretos do Hub IoT do Azure (Java) | Microsoft Docs"
description: "Como usar os métodos diretos do Hub IoT do Azure. Use o SDK do dispositivo IoT do Azure para Java para implementar um aplicativo de dispositivo simulado que inclui um método direto e o SDK do serviço do Azure IoT para Java para implementar um aplicativo de serviço que invoca o método direto."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 4fb759ecd7767c126bc22165494652039ba1caa4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-java"></a>Usar métodos diretos (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Neste tutorial, você cria dois aplicativos de console do Java:

* **invoke-direct-method**, um aplicativo de back-end Java que chama um método no aplicativo do dispositivo simulado e exibe a resposta.
* **simulated-device**, um aplicativo Java que simula um dispositivo em conexão com seu Hub IoT com a identidade do dispositivo criada por você. Este aplicativo responde à invocação direta do back-end.

> [!NOTE]
> Para saber mais sobre os SDKs que você pode usar para compilar aplicativos executados em dispositivos e no back-end da solução, veja [SDKs de IoT do Azure][lnk-hub-sdks].

Para concluir este tutorial, você precisará:

* Java SE 8. <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o Java para este tutorial no Windows ou no Linux.
* Maven 3.  <br/> [Preparar o ambiente de desenvolvimento][lnk-dev-setup] descreve como instalar o [Maven][lnk-maven] para este tutorial no Windows ou no Linux.
* [Versão do Node.js 0.10.0 ou posterior](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Criar um aplicativo de dispositivo simulado

Nesta seção, você cria um aplicativo de console Java que responde a um método chamado pelo back-end da solução.

1. Crie uma pasta vazia chamada iot-java-direct-method.

1. Na pasta iot-java-direct-method, crie um projeto Maven denominado **simulated-device** usando o comando a seguir no prompt de comando. O comando a seguir é um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. No prompt de comando, navegue até a pasta simulated-device.

1. Usando um editor de texto, abra o arquivo pom.xml na pasta simulated-device e adicione as seguintes dependências ao nó **dependencies** . Essa dependência permite que você use o pacote iot-device-client em seu aplicativo para se comunicar com seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **iot-device-client** usando a [pesquisa Maven][lnk-maven-device-search].

1. Adicione o seguinte nó **buid** após o nó **dependencies**. Esta configuração instrui o Maven a usar Java 1.8 para compilar o aplicativo:

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

1. Salve e feche o arquivo pom.xml.

1. Usando um editor de texto, abra o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

1. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adicione as seguintes variáveis no nível da classe à classe **App** . Substituir `{youriothubname}` pelo nome do hub IoT e `{yourdevicekey}` pelo valor da chave do dispositivo gerado na seção *Criar uma identidade do dispositivo*:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Este aplicativo de exemplo usa a variável **protocol** quando cria uma instância de um objeto **DeviceClient**. 

1. Para retornar um código de status ao seu hub IoT, adicione a seguinte classe aninhada à classe **App**:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. Adicione a seguinte classe aninhada à classe **App** para tratar das invocações de método direto do back-end da solução:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
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

1. Adicione um método **main** à classe **App** para criar um **DeviceClient** e escutar invocações de método direto:

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Salve e feche o arquivo simulated-device\src\main\java\com\mycompany\app\App.java

1. Compile o aplicativo **simulated-device** e corrija os erros. No prompt de comando, navegue até a pasta simulated-device e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>Chama um método direto em um dispositivo

Nesta seção, você cria um aplicativo de console Java que invoca um método direto e depois exibe a resposta. Esse aplicativo de console se conecta ao seu Hub IoT para invocar o método direto.

1. Na pasta iot-java-direct-method, crie um projeto Maven denominado **invoke-direct-method** usando o comando a seguir no prompt de comando. O comando a seguir é um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. No prompt de comando, navegue até a pasta invoke-direct-method.

1. Usando um editor de texto, abra o arquivo pom.xml na pasta invoke-direct-method e adicione a dependência a seguir ao nó **dependencies** . Essa dependência permite que você use o pacote iot-service-client em seu aplicativo para se comunicar com seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **iot-service-client** usando a [pesquisa Maven][lnk-maven-service-search].

1. Adicione o seguinte nó **buid** após o nó **dependencies**. Esta configuração instrui o Maven a usar Java 1.8 para compilar o aplicativo:

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

1. Salve e feche o arquivo pom.xml.

1. Usando um editor de texto, abra o arquivo invoke-direct-method\src\main\java\com\mycompany\app\App.java.

1. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua `{youriothubconnectionstring}` pela cadeia de conexão do hub IoT anotada na seção *Criar um Hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. Para invocar o método no dispositivo simulado, adicione o seguinte código ao método **main**:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. Salve e feche o arquivo invoke-direct-method\src\main\java\com\mycompany\app\App.java

1. Crie o aplicativo **invoke-direct-method** e corrija os erros. No prompt de comando, navegue até a pasta invoke-direct-method e execute o seguinte comando:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos de console.

1. Em um prompt de comando, na pasta simulated-device, execute o seguinte comando para começar a escutar chamadas de método de seu Hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplicativo de dispositivo simulado de Java do Hub IoT para escutar chamadas de método direto][8]

1. Em um prompt de comando, na pasta invoke-direct-method, execute o seguinte comando para chamar um método em seu dispositivo simulado de seu Hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Aplicativo de serviço Java do Hub IoT para chamar um método direto][7]

1. O dispositivo simulado responde à chamada de método direto:

    ![O aplicativo de dispositivo simulado Java do Hub IoT responde à chamada de método direto][9]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você usou essa identidade do dispositivo para habilitar o aplicativo do dispositivo simulado para reagir aos métodos invocados pela nuvem. Você também criou um aplicativo que invoca métodos no dispositivo e exibe a resposta do dispositivo.

Para explorar outros cenários de IoT, confira [Agendar trabalhos em vários dispositivos][lnk-devguide-jobs].

Para saber como estender sua solução de IoT e agendar chamadas de método em vários dispositivos, confira o tutorial [Agendar e difundir trabalhos][lnk-tutorial-jobs].

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
