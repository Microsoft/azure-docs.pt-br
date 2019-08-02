---
title: Introdução aos dispositivos gêmeos do Hub IoT do Azure (Java) | Microsoft Docs
description: Como usar dispositivos gêmeos do Hub IoT do Azure para adicionar marcas e usar uma consulta do Hub IoT. Use o SDK do dispositivo IoT do Azure para Java para implementar o aplicativo de dispositivo e o SDK do serviço IoT do Azure para Java para implementar um aplicativo de serviço que adiciona as marcas e executa a consulta do Hub IoT.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/04/2017
ms.openlocfilehash: 6d2d0540786f1aa4bec35cf4bec26212cb7df7ae
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404198"
---
# <a name="get-started-with-device-twins-java"></a>Introdução ao dispositivos gêmeos (Java)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Neste tutorial, você cria dois aplicativos de console do Java:

* **add-tags-query**, um aplicativo de back-end Java que adiciona dispositivos gêmeos de marcas e consultas.
* **simulated-device**, um aplicativo de dispositivo Java que se conecta ao hub IoT e relata sua condição de conectividade usando uma propriedade relatada.

> [!NOTE]
> O artigo [SDKs de IoT do Azure](iot-hub-devguide-sdks.md) apresenta informações sobre os SDKs de IoT do Azure que você pode usar para criar dispositivos e aplicativos de back-end.

Para concluir este tutorial, você precisará:

* A versão mais recente do [Java SE Development Kit 8](https://aka.ms/azure-jdks)

* [Maven 3](https://maven.apache.org/install.html)

* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

## <a name="create-an-iot-hub"></a>Crie um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrar um novo dispositivo no hub IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Obter a cadeia de conexão do Hub IoT

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço

Nesta seção, você cria um aplicativo Java que adiciona metadados de local associado como uma marca ao dispositivo gêmeo no Hub IoT associado a **myDeviceId**. O aplicativo primeiro consulta o hub IoT para dispositivos localizados nos EUA e, em seguida, para dispositivos que reportam a uma conexão de rede de celular.

1. No computador de desenvolvimento, crie uma pasta vazia chamada `iot-java-twin-getstarted`.

2. Na pasta `iot-java-twin-getstarted`, crie um projeto Maven chamado **add-tags-query** usando o seguinte comando no prompt de comando. Observe que este é um comando único e longo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=add-tags-query -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

3. No prompt de comando, navegue até a pasta `add-tags-query`.

4. Usando um editor de texto, abra o arquivo `pom.xml` na pasta `add-tags-query` e adicione a dependência a seguir ao nó **dependencies**. Essa dependência permite que você use o pacote **iot-service-client** em seu aplicativo para se comunicar com seu hub IoT:

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

7. Usando um editor de texto, abra o arquivo `add-tags-query\src\main\java\com\mycompany\app\App.java`.

8. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

9. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua `{youriothubconnectionstring}` pela cadeia de conexão do Hub IOT que você copiou anteriormente em [obter a cadeia de conexão do Hub IOT](#get-the-iot-hub-connection-string):

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String region = "US";
    public static final String plant = "Redmond43";
    ```

10. Atualize a assinatura do método **principal** para incluir a seguinte cláusula `throws`:

    ```java
    public static void main( String[] args ) throws IOException
    ```

11. Adicione o seguinte código ao método **principal** para criar os objetos **DeviceTwin** e **DeviceTwinDevice**. O objeto **DeviceTwin** lida com a comunicação com seu hub IoT. O objeto **DeviceTwinDevice** representa o dispositivo gêmeo com suas propriedades e marcas:

    ```java
    // Get the DeviceTwin and DeviceTwinDevice objects
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
    ```

12. Adicione o seguinte bloco `try/catch` ao método **principal**:

    ```java
    try {
      // Code goes here
    } catch (IotHubException e) {
      System.out.println(e.getMessage());
    } catch (IOException e) {
      System.out.println(e.getMessage());
    }
    ```

13. Para atualizar as marcas do dispositivo gêmeo **região** e **planta** no seu dispositivo gêmeo, adicione o seguinte código ao bloco `try`:

    ```java
    // Get the device twin from IoT Hub
    System.out.println("Device twin before update:");
    twinClient.getTwin(device);
    System.out.println(device);

    // Update device twin tags if they are different
    // from the existing values
    String currentTags = device.tagsToString();
    if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {
      // Create the tags and attach them to the DeviceTwinDevice object
      Set<Pair> tags = new HashSet<Pair>();
      tags.add(new Pair("region", region));
      tags.add(new Pair("plant", plant));
      device.setTags(tags);

      // Update the device twin in IoT Hub
      System.out.println("Updating device twin");
      twinClient.updateTwin(device);
    }

    // Retrieve the device twin with the tag values from IoT Hub
    System.out.println("Device twin after update:");
    twinClient.getTwin(device);
    System.out.println(device);
    ```

14. Para consultar os dispositivos gêmeos no hub IoT, adicione o seguinte código ao bloco `try` após o código adicionado na etapa anterior. O código executa duas consultas. Cada consulta retorna um máximo de 100 dispositivos:

    ```java
    // Query the device twins in IoT Hub
    System.out.println("Devices in Redmond:");

    // Construct the query
    SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

    // Run the query, returning a maximum of 100 devices
    Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }

    System.out.println("Devices in Redmond using a cellular network:");

    // Construct the query
    sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

    // Run the query, returning a maximum of 100 devices
    twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
    while (twinClient.hasNextDeviceTwin(twinQuery)) {
      DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
      System.out.println(d.getDeviceId());
    }
    ```

15. Salve e feche o arquivo `add-tags-query\src\main\java\com\mycompany\app\App.java`

16. Crie o aplicativo **add-tags-query** e corrija os erros. No prompt de comando, navegue até a pasta `add-tags-query` e execute o seguinte comando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-device-app"></a>Criar um aplicativo de dispositivo

Nesta seção, você cria um aplicativo de console Java que define um valor da propriedade relatado enviado ao Hub IoT.

1. Na pasta `iot-java-twin-getstarted`, crie um projeto Maven denominado **simulated-device** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. No prompt de comando, navegue até a pasta `simulated-device`.

3. Usando um editor de texto, abra o arquivo `pom.xml` na pasta `simulated-device` e adicione as dependências a seguir ao nó de **dependências**. Essa dependência permite que você use o pacote **iot-device-client** em seu aplicativo para se comunicar com seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.14.2</version>
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
    private static String deviceId = "myDeviceId";
    ```

    Este aplicativo de exemplo usa a variável **protocol** quando cria uma instância de um objeto **DeviceClient**. 

1. Adicione o seguinte método à classe **App** para imprimir informações sobre atualizações gêmeas:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
          System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
      }
    ```

9. Adicione o seguinte código ao método **principal** para:
    * Criar um cliente de dispositivo para se comunicar com o Hub IoT.
    * Criar um objeto **Dispositivo** para armazenar as propriedades do dispositivo gêmeo.

      ```java
      DeviceClient client = new DeviceClient(connString, protocol);

      // Create a Device object to store the device twin properties
      Device dataCollector = new Device() {
      // Print details when a property value changes
      @Override
      public void PropertyCall(String propertyKey, Object propertyValue, Object context) {
        System.out.println(propertyKey + " changed to " + propertyValue);
      }
      };
      ```

10. Adicione o seguinte código para ao método **principal** para criar uma propriedade relatada **connectivityType** e enviá-la ao Hub IoT:

    ```java
    try {
      // Open the DeviceClient and start the device twin services.
      client.open();
      client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, dataCollector, null);

      // Create a reported property and send it to your IoT hub.
      dataCollector.setReportedProp(new Property("connectivityType", "cellular"));
      client.sendReportedProperties(dataCollector.getReportedProp());
    }
    catch (Exception e) {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
      dataCollector.clean();
      client.closeNow();
      System.out.println("Shutting down...");
    }
    ```

11. Adicione o seguinte código ao final do método **principal**. Aguardar a tecla **Enter** dá tempo para o Hub IoT relatar o status das operações do dispositivo gêmeo:

    ```java
    System.out.println("Press any key to exit...");

    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    dataCollector.clean();
    client.close();
    ```

1. Modifique a assinatura do método **main** para incluir as exceções abaixo:

     ```java
     public static void main(String[] args) throws URISyntaxException, IOException
     ```

1. Salve e feche o arquivo `simulated-device\src\main\java\com\mycompany\app\App.java`.

13. Compile o aplicativo **simulated-device** e corrija os erros. No prompt de comando, navegue até a pasta `simulated-device` e execute o seguinte comando:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos de console.

1. Em um prompt de comando na pasta `add-tags-query`, execute o seguinte comando para executar o aplicativo de serviço **add-tags-query**:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de serviço do Hub IoT Java para atualizar valores de marca e executar consultas de dispositivo](./media/iot-hub-java-java-twin-getstarted/service-app-1.png)

    Você pode ver as marcas **planta** e **região** adicionadas ao dispositivo gêmeo. A primeira consulta retorna seu dispositivo, mas a segunda, não.

2. Em um prompt de comando na pasta `simulated-device`, execute o seguinte comando para adicionar a propriedade relatada **connectivityType** para o dispositivo gêmeo:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![O cliente do dispositivo adiciona a propriedade relatada **connectivityType**](./media/iot-hub-java-java-twin-getstarted/device-app-1.png)

3. Em um prompt de comando na pasta `add-tags-query`, execute o seguinte comando para executar o aplicativo de serviço **add-tags-query** uma segunda vez:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicativo de serviço do Hub IoT Java para atualizar valores de marca e executar consultas de dispositivo](./media/iot-hub-java-java-twin-getstarted/service-app-2.png)

    Agora seu dispositivo enviou a propriedade **connectivityType** ao Hub IoT; a segunda consulta retorna seu dispositivo.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou um novo hub IoT no portal do Azure e depois criou uma identidade do dispositivo no Registro de identidade do Hub IoT. Você adicionou os metadados do dispositivo como marcações de um aplicativo de back-end e escreveu um aplicativo de dispositivo para relatar informações de conectividade no dispositivo gêmeo. Você também aprendeu a consultar as informações do dispositivo gêmeo usando a linguagem de consulta do Hub IoT semelhante a SQL.

Veja os recursos a seguir para saber como:

* Envie telemetria de dispositivos com o tutorial [Introdução ao Hub IoT](quickstart-send-telemetry-java.md).

* Controle dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário) com o tutorial [Usar métodos diretos](quickstart-control-device-java.md).