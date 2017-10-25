---
title: "Usar as propriedades do dispositivo gêmeo do Hub IoT do Azure (Java) | Microsoft Docs"
description: "Como usar dispositivos gêmeos do Hub IoT do Azure para configurar dispositivos. Use o SDK do dispositivo IoT do Azure para Java para implementar um aplicativo de dispositivo simulado e o SDK do serviço IoT do Azure para Java para implementar um aplicativo de serviço que modifique uma configuração de dispositivo usando um dispositivo gêmeo."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Usar as propriedades desejadas para configurar os dispositivos

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Ao fim deste tutorial, você tem dois aplicativos de console do Java:

* **simulated-device**, um aplicativo de dispositivo simulado que aguarda uma atualização da configuração desejada e reporta o status de um processo simulado de atualização de configuração.
* **set-configuration**, um aplicativo de back-end que define a configuração desejada em um dispositivo e consulta o processo de atualização de configuração.

> [!NOTE]
> O artigo [SDKs do IoT do Azure][lnk-hub-sdks] fornece informações sobre os SDKs do IoT do Azure que você pode usar para compilar dispositivos e aplicativos back-end.
> 
> 

Para concluir este tutorial, você precisará do seguinte:

* A versão mais recente do [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Uma conta ativa do Azure. (Se você não tem uma conta, pode criar uma [conta gratuita][lnk-free-trial] em apenas alguns minutos.)

Caso tenha seguido o tutorial [Introdução aos dispositivos gêmeos][lnk-twin-tutorial], você já terá um Hub IoT e uma identidade de dispositivo chamada **myDeviceId**. Nesse caso, você pode pular para a seção [Criar o aplicativo do dispositivo simulado][lnk-how-to-configure-createapp].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>Criar o aplicativo de dispositivo simulado
Nesta seção, você cria um aplicativo de console Java que se conecta ao seu hub como **myDeviceId**, aguarda uma atualização de configuração desejada e, em seguida, reporta atualizações sobre o processo simulado de atualização de configuração.

1. Crie uma pasta vazia chamada dt-get-started.

1. Na pasta dt-get-started, crie um projeto Maven denominado **simulated-device** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. No prompt de comando, navegue até a pasta simulated-device.

1. Usando um editor de texto, abra o arquivo pom.xml na pasta simulated-device e adicione a dependência a seguir ao nó **dependencies**. Essa dependência permite que você use o pacote iot-service-client em seu aplicativo para se comunicar com seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
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

1. Usando um editor de texto, abra o arquivo de origem simulated-device\src\main\java\com\mycompany\app\App.java.

1. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua **{yourdeviceconnectionstring}** pela cadeia de conexão de dispositivo anotada na seção *Criar uma identidade de dispositivo*:

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Para implementar um manipulador de retorno de chamada para eventos de status de dispositivo gêmeo (para fins de depuração), adicione a seguinte classe aninhada à classe de **Aplicativo**:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Adicione a classe aninhada a seguir à classe **App** :

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > A classe TelemetryConfig estende a [classe de Dispositivo][lnk-java-device-class] para acessar os retornos de chamada de propriedades desejados.

1. Modifique a assinatura do método **principal** para lançar as seguintes exceções:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Adicione o seguinte código ao método **principal** para instanciar um **DeviceClient** e **TelemetryConfig**:

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Adicione o seguinte código ao método **principal** para iniciar os serviços de dispositivos gêmeos:

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Adicione o seguinte código ao método **principal** para desligar o simulador de dispositivo quando necessário:

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Salve e feche o arquivo simulated-device\src\main\java\com\mycompany\app\App.java.

1. Crie o aplicativo de back-end **simulated-device** e corrija os erros. No prompt de comando, navegue até a pasta simulated-device e execute o seguinte comando:

    `mvn clean package -DskipTests`

   > [!NOTE]
   > Este tutorial não simula nenhum comportamento para atualizações de configuração simultâneas. Alguns processos de atualização de configuração podem ser capazes de acomodar as alterações de configuração de destino enquanto a atualização está em execução, outros podem colocá-las em fila e outros poderiam ainda rejeitá-las com uma condição de erro. Certifique-se de considerar o comportamento desejado para o seu processo de configuração específico e adicione a lógica apropriada antes de iniciar a alteração de configuração.
   > 
   > 

## <a name="create-the-service-app"></a>Criar o aplicativo do serviço
Nesta seção, você cria um aplicativo de console Java que atualiza as *propriedades desejadas* no dispositivo gêmeo associado à **myDeviceId** com um novo objeto de configuração de telemetria. Ele então consulta os dispositivos gêmeos armazenados no Hub IoT e mostra a diferença entre as configurações desejadas e reportadas do dispositivo.

1. Na pasta dt-get-started, crie um projeto Maven denominado **set-configuration** usando o comando a seguir no prompt de comando. Observe que este é um comando único e longo:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. No prompt de comando, navegue até a pasta set-configuration.

1. Usando um editor de texto, abra o arquivo pom.xml na pasta trigger-reboot e adicione a dependência a seguir ao nó **dependencies**. Essa dependência permite que você use o pacote iot-service-client em seu aplicativo para se comunicar com seu hub IoT:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Você pode verificar a versão mais recente do **iot-service-client** usando [pesquisa Maven][lnk-maven-service-search].

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

1. Usando um editor de texto, abra o arquivo de origem set-configuration\src\main\java\com\mycompany\app\App.java.

1. Adicione as seguintes instruções **import** ao arquivo:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Adicione as seguintes variáveis no nível da classe à classe **App** . Substitua **{youriothubconnectionstring}** pela cadeia de conexão do hub IoT anotada na seção *Criar um Hub IoT*:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Para consultar e atualizar os dispositivos gêmeos no dispositivo simulado, adicione o seguinte código ao método **principal**:

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Salve e feche o arquivo set-desired-configuration\src\main\java\com\mycompany\app\App.java.

1. Crie o aplicativo de back-end **set-configuration** e corrija os erros. No prompt de comando, navegue até a pasta set-configuration e execute o seguinte comando:

    `mvn clean package -DskipTests`
   
    Esse código recupera o dispositivo gêmeo para **myDeviceId** e então atualiza suas propriedades desejadas com um novo objeto de configuração de telemetria.
    Depois disso, a cada 10 segundos, ele consulta os dispositivos gêmeos armazenados no Hub IoT e imprime as configurações de telemetria desejadas e relatadas. Consulte a [Linguagem de consulta de Hub IoT][lnk-query] para saber como gerar relatórios em todos os seus dispositivos.
   
   > [!IMPORTANT]
   > Esse aplicativo consulta o Hub IoT a cada 10 segundos para fins ilustrativos até que o dispositivo tenha sido atualizado. Use consultas para gerar relatórios voltados para o usuário em vários dispositivos, não para detectar alterações. Se sua solução exigir notificações em tempo real de eventos de dispositivo, use [notificações gêmeas][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Há um atraso de até um minuto entre a operação de relatório de dispositivo e o resultado da consulta. Isso é para habilitar a infraestrutura de consulta a trabalhar em escala muito alta.  Para recuperar os modos de exibição consistentes de um único dispositivo gêmeo, use o método **getDeviceTwin** na classe **DeviceTwin**.
   > 
   > 

## <a name="run-the-apps"></a>Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. Em um prompt de comando, na pasta simulated-device, execute o seguinte comando para começar a escutar chamadas do dispositivo gêmeo de seu Hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Em um prompt de comando na pasta set-configuration, execute o seguinte comando para consultar e atualizar os dispositivos gêmeos no seu dispositivo simulado do Hub IoT:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. O dispositivo simulado responde à chamada de método direto de reinicialização:

    ![O aplicativo de dispositivo simulado Java do Hub IoT responde à chamada do dispositivo gêmeo][img-deviceconfigured]

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você definiu uma configuração desejada como *propriedades desejadas* no back-end da solução e criou um aplicativo de dispositivo para detectar essa alteração e simular um processo de atualização de várias etapas, relatando seu status por meio das propriedades relatadas.

Veja os recursos a seguir para saber como:

* Enviar telemetria de dispositivos com o tutorial [Introdução ao Hub IoT][lnk-iothub-getstarted].
* Agendar ou executar operações em grandes conjuntos de dispositivos. Veja o tutorial [Schedule and broadcast jobs][lnk-schedule-jobs] (Agendar e transmitir trabalhos).
* Controlar dispositivos interativamente (como ativar uma ventoinha de um aplicativo controlado pelo usuário), com o tutorial [Uso de métodos diretos][lnk-methods-tutorial].

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
