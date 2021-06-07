---
title: Início Rápido para adicionar sinalizadores de recursos ao Spring Boot com a Configuração de Aplicativos do Azure
description: Adicionar sinalizadores de recursos a aplicativos Spring Boot e gerenciá-los usando a Configuração de Aplicativos do Azure
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 08/06/2020
ms.author: alkemper
ms.custom: devx-track-java
ms.openlocfilehash: e931b974fc48d07237b2698e7782c48be20745d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930698"
---
# <a name="quickstart-add-feature-flags-to-a-spring-boot-app"></a>Início Rápido: Adicionar sinalizadores de recursos a um aplicativo Spring Boot

Neste início rápido, você incorpora a Configuração de Aplicativos do Azure a um aplicativo Web Spring Boot para criar uma implementação de ponta a ponta do gerenciamento de recursos. Você pode usar o serviço de Configuração de Aplicativos para armazenar de forma centralizada todos os sinalizadores de recurso e controlar seus estados.

As bibliotecas do Gerenciamento de Recursos do Spring Boot estendem a estrutura com suporte abrangente para sinalizadores de recursos. Essas bibliotecas **não** têm uma dependência de nenhuma biblioteca do Azure. Elas se integram perfeitamente à Configuração de Aplicativos por meio de seu provedor de configuração do Spring Boot.

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)
* Um [SDK do Java Development Kit](/java/azure/jdk) com suporte na versão 8.
* [Apache Maven](https://maven.apache.org/download.cgi), versão 3.0 ou posterior.

## <a name="create-an-app-configuration-instance"></a>Criar uma instância de Configuração de Aplicativos

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selecione **Gerenciador de Recursos** >  **+Adicionar** para adicionar um sinalizador de recurso chamado `Beta`.

    > [!div class="mx-imgBorder"]
    > ![Habilitar o sinalizador de recurso chamado Beta](media/add-beta-feature-flag.png)

    Mantenha `label` indefinido por enquanto.

## <a name="create-a-spring-boot-app"></a>Criar um aplicativo Spring Boot

Use o [Spring Initializr](https://start.spring.io/) para criar um novo projeto Spring Boot.

1. Navegue até <https://start.spring.io/>.

1. Especifique as seguintes opções:

   * Gere um projeto **Maven** com **Java**.
   * Especifique uma versão do **Spring Boot** igual ou maior que 2.0.
   * Especifique os nomes de **Grupo** e **Artefato** do aplicativo.  Este artigo usa `com.example` e `demo`.
   * Adicione a dependência do **Spring Web**.

1. Após especificar as opções anteriores, selecione **Gerar Projeto**. Quando solicitado, baixe o projeto para o seu computador local.

## <a name="add-feature-management"></a>Adicionar gerenciamento de recursos

1. Após extrair os arquivos no sistema local, o aplicativo Spring Boot estará pronto para edição. Localize *pom.xml* no diretório raiz do aplicativo.

1. Abra o arquivo *pom.xml* em um editor de texto e adicione o seguinte conteúdo à lista de `<dependencies>`:

    **Spring Cloud 1.1.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.1.5</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

    **Spring Cloud 1.2.x**

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-azure-feature-management-web</artifactId>
        <version>1.2.7</version>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>
    ```

> [!Note]
> Há uma Biblioteca de Gerenciamento de Recursos não Web que não tem uma dependência do spring-web. Veja a [documentação](https://github.com/microsoft/spring-cloud-azure) do GitHub para obter diferenças.

## <a name="connect-to-an-app-configuration-store"></a>Conectar um repositório de Configuração de Aplicativos

1. Navegue até o diretório `resources` do seu aplicativo e abra `bootstrap.properties`.  Se o arquivo não existir, crie um. Adicione a seguinte linha ao arquivo.

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].connection-string= ${APP_CONFIGURATION_CONNECTION_STRING}
    ```

1. No portal da Configuração de Aplicativos do seu repositório de configuração, selecione `Access keys` na barra lateral. Selecione a guia Chaves somente leitura. Copie o valor da cadeia de conexão primária.

1. Adicione a cadeia de conexão primária como uma variável de ambiente usando o nome da variável `APP_CONFIGURATION_CONNECTION_STRING`.

1. Abra o arquivo Java do aplicativo principal e adicione `@EnableConfigurationProperties` para habilitar esse recurso.

    ```java
    package com.example.demo;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.boot.context.properties.EnableConfigurationProperties;
    import org.springframework.boot.autoconfigure.SpringBootApplication;

    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
    ```

1. Crie um arquivo Java chamado *MessageProperties.java* no diretório do pacote do aplicativo.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.context.annotation.Configuration;

    @Configuration
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

1. Crie um arquivo Java chamado *HelloController.java* no diretório do pacote do aplicativo.

    ```java
    package com.example.demo;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;

    import com.microsoft.azure.spring.cloud.feature.manager.FeatureManager;
    import org.springframework.web.bind.annotation.GetMapping;


    @Controller
    @ConfigurationProperties("controller")
    public class HelloController {

        private FeatureManager featureManager;

        public HelloController(FeatureManager featureManager) {
            this.featureManager = featureManager;
        }

        @GetMapping("/welcome")
        public String mainWithParam(Model model) {
            model.addAttribute("Beta", featureManager.isEnabledAsync("featureManagement.Beta").block());
            return "welcome";
        }
    }
    ```

1. Crie um arquivo HTML chamado *welcome.html* no diretório de modelos do seu aplicativo.

    ```html
    <!DOCTYPE html>
    <html lang="en" xmlns:th="http://www.thymeleaf.org">
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <title>Feature Management with Spring Cloud Azure</title>

        <link rel="stylesheet" href="/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/css/bootstrap.min.css" integrity="sha384-ggOyR0iXCbMQv3Xipma34MD+dH/1fQ784/j6cY/iJTQUOhcWr7x9JvoRxT2MZw1T" crossorigin="anonymous">

        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.7/umd/popper.min.js" integrity="sha384-UO2eT0CpHqdSJQ6hJty5KVphtPhzWj9WO1clHTMGa3JDZwrnQq4sF86dIHNDz0W1" crossorigin="anonymous"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.3.1/js/bootstrap.min.js" integrity="sha384-JjSmVgyd0p3pXB1rRibZUAYoIIy6OrQ6VrjIEaFf/nJGzIxFDsf4x0xIM+B07jRM" crossorigin="anonymous"></script>

    </head>
    <body>
        <header>
        <!-- Fixed navbar -->
        <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
            <a class="navbar-brand" href="#">TestFeatureFlags</a>
            <button class="navbar-toggler" aria-expanded="false" aria-controls="navbarCollapse" aria-label="Toggle navigation" type="button" data-target="#navbarCollapse" data-toggle="collapse">
            <span class="navbar-toggler-icon"></span>
            </button>
            <div class="collapse navbar-collapse" id="navbarCollapse">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item active">
                <a class="nav-link" href="#">Home <span class="sr-only">(current)</span></a>
                </li>
                <li class="nav-item" th:if="${Beta}">
                <a class="nav-link" href="#">Beta</a>
                </li>
                <li class="nav-item">
                <a class="nav-link" href="#">Privacy</a>
                </li>
            </ul>
            </div>
        </nav>
        </header>
        <div class="container body-content">
            <h1 class="mt-5">Welcome</h1>
            <p>Learn more about <a href="https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/appconfiguration/azure-spring-cloud-feature-management/README.md">Feature Management with Spring Cloud Azure</a></p>

        </div>
        <footer class="footer">
            <div class="container">
            <span class="text-muted">&copy; 2019 - Projects</span>
        </div>

        </footer>
    </body>
    </html>

    ```

1. Crie uma pasta chamada CSS em `static` e, dentro dela, um arquivo CSS chamado *main.css*.

    ```css
    html {
     position: relative;
     min-height: 100%;
    }
    body {
     margin-bottom: 60px;
    }
    .footer {
     position: absolute;
     bottom: 0;
     width: 100%;
     height: 60px;
     line-height: 60px;
     background-color: #f5f5f5;
    }

    body > .container {
     padding: 60px 15px 0;
    }

    .footer > .container {
     padding-right: 15px;
     padding-left: 15px;
    }

    code {
     font-size: 80%;
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilar e executar o aplicativo localmente

1. Compile o aplicativo Spring Boot com Maven e execute-o.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Abra uma nova janela do navegador e acesse a URL: `http://localhost:8080/welcome`.

    ![A captura de tela mostra uma janela de navegador com uma mensagem de boas-vindas.](./media/quickstarts/spring-boot-feature-flag-local-before.png)

1. No portal da Configuração de Aplicativos, selecione **Gerenciador de Recursos** e altere o estado da chave **Beta** para **Ativado**:

    | Chave | Estado |
    |---|---|
    | Beta | Por |

1. Atualize a página do navegador para ver as novas definições de configuração.

    ![A captura de tela mostra uma janela do navegador com uma mensagem de boas-vindas e um link Beta destacado.](./media/quickstarts/spring-boot-feature-flag-local-after.png)

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você criou um novo repositório de configurações de aplicativos e o usou para gerenciar recursos em um aplicativo Web Spring Boot por meio das [bibliotecas do Gerenciamento de Recursos](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration).

* Saiba mais sobre o [gerenciamento de recursos](./concept-feature-management.md).
* [Gerenciar sinalizadores de recursos](./manage-feature-flags.md).
* [Usar sinalizadores de recursos em um aplicativo Spring Boot Core](./use-feature-flags-spring-boot.md).