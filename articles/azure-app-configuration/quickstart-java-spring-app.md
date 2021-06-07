---
title: Início Rápido para aprender a usar a Configuração de Aplicativos do Azure
description: Neste guia de início rápido, crie um aplicativo Java Spring com a Configuração de Aplicativos do Azure para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 04/18/2020
ms.custom: devx-track-java
ms.author: alkemper
ms.openlocfilehash: 40c8fae292f465b3ecc8778a21e4903bdf423a3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932023"
---
# <a name="quickstart-create-a-java-spring-app-with-azure-app-configuration"></a>Início Rápido: Criar um aplicativo Java Spring com a Configuração de Aplicativos do Azure

Neste guia de início rápido, você incorpora a Configuração de Aplicativos do Azure em um aplicativo Java Spring para centralizar o armazenamento e o gerenciamento de configurações de aplicativo separadas do seu código.

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
- Um [JDK (Java Development Kit)](/java/azure/jdk) com suporte na versão 8.
- [Apache Maven](https://maven.apache.org/download.cgi), versão 3.0 ou posterior.

## <a name="create-an-app-configuration-store"></a>Criar um repositório de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Configurações** >  **+ Criar** > **Chave-valor** para adicionar os seguintes pares chave-valor:

    | Chave | Valor |
    |---|---|
    | /application/config.message | Olá |

    Deixe **Rótulo** e **Tipo de Conteúdo** vazios por enquanto.

8. Escolha **Aplicar**.

## <a name="create-a-spring-boot-app"></a>Criar um aplicativo Spring Boot

Use o [Spring Initializr](https://start.spring.io/) para criar um novo projeto Spring Boot.

1. Navegue até <https://start.spring.io/>.

1. Especifique as seguintes opções:

   - Gere um projeto **Maven** com **Java**.
   - Especifique uma versão do **Spring Boot** igual ou maior que 2.0.
   - Especifique os nomes de **Grupo** e **Artefato** do aplicativo.
   - Adicione a dependência do **Spring Web**.

1. Após especificar as opções anteriores, selecione **Gerar Projeto**. Quando solicitado, baixe o projeto para um caminho no computador local.

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Após extrair os arquivos no sistema local, o aplicativo Spring Boot simples estará pronto para edição. Localize o arquivo *pom.xml* no diretório raiz do aplicativo.

1. Abra o arquivo *pom.xml* em um editor de texto e adicione o iniciador da Configuração do Azure do Spring Cloud à lista de `<dependencies>`:

    **Spring Cloud 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.1.5</version>
    </dependency>
    ```

    **Spring Cloud 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config</artifactId>
        <version>1.2.7</version>
    </dependency>
    ```

1. Crie um arquivo Java chamado *MessageProperties.java* no diretório do pacote do aplicativo. Adicione as linhas a seguir:

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;

    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

1. Crie um arquivo Java chamado *HelloController.java* no diretório do pacote do aplicativo. Adicione as linhas a seguir:

    ```java
    package com.example.demo;

    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

1. Abra o arquivo Java do aplicativo principal e adicione `@EnableConfigurationProperties` para habilitar esse recurso.

    ```java
    import org.springframework.boot.context.properties.EnableConfigurationProperties;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {
        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Crie um novo arquivo nomeado `bootstrap.properties` no diretório de recursos do aplicativo e adicione as seguintes linhas ao arquivo. Substitua os valores de exemplo pelas propriedades apropriadas do repositório de Configuração de Aplicativos.

    ```CLI
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. Defina uma variável de ambiente chamada **APP_CONFIGURATION_CONNECTION_STRING** e defina-a como a chave de acesso ao repositório de Configuração de Aplicativos. Na linha de comando, execute o seguinte comando e reinicie o prompt de comando para permitir que a alteração entre em vigor:

    ```cmd
    setx APP_CONFIGURATION_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    Se você usa o Windows PowerShell, execute o comando a seguir:

    ```azurepowershell
    $Env:APP_CONFIGURATION_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    Se você usa macOS ou Linux, execute o comando a seguir:

    ```cmd
    export APP_CONFIGURATION_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Compile o aplicativo Spring Boot com Maven e execute-o, por exemplo:

    ```cmd
    mvn clean package
    mvn spring-boot:run
    ```

2. Depois que o aplicativo estiver em execução, use *curl* para testar o aplicativo, por exemplo:

      ```cmd
      curl -X GET http://localhost:8080/
      ```

    Você verá a mensagem inserida no repositório de Configuração de Aplicativos.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um repositório de Configuração de Aplicativos e o usou com um aplicativo Java Spring. Para obter mais informações, consulte [Spring no Azure](/java/azure/spring-framework/). Para saber como habilitar o aplicativo Java Spring a atualizar dinamicamente as configurações, prossiga para o próximo tutorial.

> [!div class="nextstepaction"]
> [Habilitar configuração dinâmica](./enable-dynamic-configuration-java-spring-app.md)