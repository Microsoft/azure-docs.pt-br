---
title: "Criação de um módulo Azure IoT Edge com Java | Microsoft Docs"
description: "Este tutorial mostra como escrever um módulo conversor de dados BLE usando os pacotes do Azure IoT Edge Maven mais recentes."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.openlocfilehash: eddeb5cc13aac7ab33305adcd266465a5b143462
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Como criar um módulo do Azure IoT Edge com Java

Este tutorial mostra como criar um módulo para Azure IoT Edge em Java.

Neste tutorial, vamos percorrer a instalação de ambiente e como escrever um módulo conversor de dados [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) usando os pacotes Maven mais recentes do Azure IoT Edge.

## <a name="prerequisites"></a>Pré-requisitos

Nesta seção, você configurará o ambiente para o desenvolvimento do módulo IoT Edge. Ela se aplica aos sistemas operacionais *Windows de 64 bits* e *Linux de 64 bits (Ubuntu/Debian 8)*.

O seguinte software é necessário:

* [Cliente Git](https://git-scm.com/downloads).
* [**x64** JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html).

Abra uma janela de terminal de linha de comando e clone o repositório a seguir:

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Arquitetura geral

A plataforma Azure IoT Edge adota consistentemente a [arquitetura Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). O que significa que a arquitetura inteira do Azure IoT Edge é um sistema que processa a entrada e produz a saída. E cada módulo individual também é um pequeno subsistema de entrada e saída. Neste tutorial, apresentaremos os dois seguintes módulos:

1. Um módulo que recebe um sinal [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulado e o converte em uma mensagem com formatação [JSON](https://en.wikipedia.org/wiki/JSON).
2. Um módulo que imprime a mensagem [JSON](https://en.wikipedia.org/wiki/JSON) recebida.

A imagem a seguir exibe o fluxo de dados de ponta a ponta típico para este projeto:

![Fluxo de dados entre três módulos](media/iot-hub-iot-edge-create-module/dataflow.png "Entrada: módulo BLE simulado; Processador: módulo conversor; Saída: módulo de impressão")

## <a name="understanding-the-code"></a>Compreender o código

### <a name="maven-project-structure"></a>Estrutura do projeto Maven

Como os pacotes do Azure IoT Edge são baseados em Maven, precisamos criar uma estrutura típica de projeto Maven, que contém um arquivo `pom.xml`.

O POM herda o pacote `com.microsoft.azure.gateway.gateway-module-base`, que declara todas as dependências necessárias a um projeto de módulo que inclui os binários de tempo de execução, o caminho de arquivo de configuração de gateway e o comportamento de execução. Isso economiza muito tempo e elimina a necessidade de escrever e regravar centenas de linhas de código repetidamente.

É necessário atualizar o arquivo de pom.xml declarando dependências/plug-ins necessários e o nome do arquivo de configuração a ser usado pelo nosso módulo, conforme mostrado no seguinte trecho de código.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Noções básicas sobre um módulo do Azure IoT Edge

É possível tratar um módulo do Azure IoT Edge como um processador de dados cujo trabalho é: receber a entrada, processá-la e produzir uma saída.

A entrada pode ser dados de hardware (como um detector de movimento), uma mensagem de outros módulos e outros como, por exemplo, um número aleatório gerado periodicamente por um timer.

O resultado é semelhante à entrada, ele pode disparar o comportamento de hardware (como o LED piscando), uma mensagem para outros módulos e outros como, por exemplo, impressão para o console.

Os módulos comunicam entre si usando classe `com.microsoft.azure.gateway.messaging.Message`. O **conteúdo** de um `Message` é uma matriz de bytes capaz de representar qualquer tipo de dados que você quiser. As **propriedades** também estão disponíveis no `Message` e são simplesmente um mapeamento de cadeia de caracteres. Você pode pensar em **Propriedades** como sendo os cabeçalhos em uma solicitação HTTPS, ou os metadados de um arquivo.

Para desenvolver um módulo do Azure IoT Edge em Java, você precisa criar uma nova classe de módulo que herda de `com.microsoft.azure.gateway.core.GatewayModule` e implementa os métodos abstratos necessários `receive()` e `destroy()`. Neste ponto, você também pode optar por também implementar os métodos`start()` ou `create()` opcionais. O trecho de código a seguir mostra como começar a criação de um módulo do Azure IoT Edge.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Módulo conversor

| Entrada                    | Processador                              | Saída                 | Arquivo de código-fonte            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Mensagem de dados de temperatura | Análise e criação de uma nova mensagem JSON | Mensagem de JSON de estrutura | `ConverterModule.java` |

Este é um módulo típico do Azure IoT Edge. Ele aceita mensagens de temperatura de outros módulos (um módulo de hardware, ou, nesse caso, nosso módulo BLE simulado), e, em seguida, normaliza a mensagem de temperatura em uma mensagem de JSON estruturada (incluindo o ID da mensagem, definindo a propriedade do que for necessário para disparar o alerta de temperatura e assim por diante).

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Módulo de impressão

| Entrada                          | Processador | Saída                     | Arquivo de código-fonte          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Qualquer mensagem de outros módulos | N/D       | A mensagem de log para o console | `PrinterModule.java` |

Este é um módulo de simples e autoexplicativo, que exporta as mensagens recebidas para a janela de terminal.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Configuração do Azure IoT Edge

Na etapa final, antes de executar os módulos, vamos configurar o Azure IoT Edge e estabelecer as conexões entre os módulos.

Primeiro, precisamos declarar nosso carregador de Java (já que o Azure IoT Edge oferece suporte a carregadores de diversos idiomas), que pode ser referenciado por seu `name` nas seções posteriores.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

Depois de declararmos nossos carregadores, também precisamos declarar os nossos módulos. Assim como a declaração dos carregadores, eles também podem ser referenciados por seus `name` atributos. Ao declarar um módulo, é necessário especificar qual carregador deve ser usado (qual deve ser o definido anteriormente) e o ponto de entrada (deve ser o nome da classe normalizado do nosso módulo) para cada módulo. O módulo `simulated_device` é um módulo nativo que é incluído no pacote principal de tempo de execução do Azure IoT Edge. Você sempre deve incluir `args` em arquivo JSON mesmo se ele for `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

No final da configuração, podemos estabelecer as conexões. Cada conexão é expressa por `source` e `sink`. Ambas fazem referência a um módulo predefinido. A mensagem de saída de módulo `source` será encaminhado para a entrada do módulo `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>Executando os módulos

Use `mvn package` para compilar tudo para a pasta `target/`. `mvn clean package` também é recomendado para uma compilação limpa.

Use `mvn exec:exec` para executar o Azure IoT Edge e para observar que os dados de temperatura e todas as propriedades são impressas no console com uma taxa fixa.

Se você deseja encerrar o aplicativo, pressione a chave `<Enter>`.

> [!IMPORTANT]
> Não é recomendável usar Ctrl + C para encerrar o aplicativo de gateway do IoT Edge. Uma vez que isso pode causar uma anomalia no encerramento do processo.

