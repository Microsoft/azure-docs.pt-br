---
title: Usar a configuração dinâmica em um aplicativo Spring Boot
titleSuffix: Azure App Configuration
description: Saiba como atualizar dados de configuração dinamicamente em aplicativos Spring Boot
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 590f221b0a4980d462267dd8c3a73ca7d02583fd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625510"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>Tutorial: Usar a configuração dinâmica em um aplicativo Java Spring

A Configuração de Aplicativos tem duas bibliotecas para Spring. `spring-cloud-azure-appconfiguration-config` exige o Spring Boot e assume uma dependência de `spring-cloud-context`. `spring-cloud-azure-appconfiguration-config-web` exige o Spring Web junto com o Spring Boot. As duas bibliotecas dão suporte ao disparo manual para verificar se há valores de configuração atualizados. `spring-cloud-azure-appconfiguration-config-web` também adiciona suporte para a verificação automática da atualização de configuração.

A atualização permite que você atualize seus valores de configuração sem precisar reiniciar o aplicativo, embora isso faça com que todos os beans no `@RefreshScope` sejam recriados. A biblioteca de clientes armazena em cache uma ID de hash das configurações carregadas atualmente para evitar um excesso de chamadas para o repositório de configurações. A operação de atualização não atualiza o valor até que o valor armazenado em cache expire, mesmo quando o valor foi alterado no repositório de configurações. O tempo de expiração padrão de cada solicitação é de 30 segundos. Ele pode ser substituído, se necessário.

A atualização automatizada do `spring-cloud-azure-appconfiguration-config-web` é disparada com base na atividade, especificamente o `ServletRequestHandledEvent` do Spring Web. Se um `ServletRequestHandledEvent` não for disparado, a atualização automatizada de `spring-cloud-azure-appconfiguration-config-web` não disparará uma atualização mesmo que o tempo de expiração do cache tenha transcorrido.

## <a name="use-manual-refresh"></a>Usar a atualização manual

A Configuração de Aplicativos expõe `AppConfigurationRefresh`, que pode ser usada para verificar se o cache expirou e, caso tenha expirado, disparar uma atualização.

```java
import com.microsoft.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`refreshConfigurations()` de `AppConfigurationRefresh` retornará um `Future` que será true se uma atualização tiver sido disparada e false se não tiver. False significa que o tempo de expiração do cache não transcorreu, que não houve alteração ou que outro thread está verificando uma atualização no momento.

## <a name="use-automated-refresh"></a>Usar a atualização automatizada

Para usar a atualização automatizada, comece com um aplicativo Spring Boot que usa a Configuração de Aplicativos, como o aplicativo criado com o [Início rápido do Spring Boot para a Configuração de Aplicativos](quickstart-java-spring-app.md).

Em seguida, abra o arquivo *pom.xml* em um editor de texto e adicione uma `<dependency>` a `spring-cloud-azure-appconfiguration-config-web`.

**Spring Cloud 1.1.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.1.5</version>
</dependency>
```

**Spring Cloud 1.2.x**

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>spring-cloud-azure-appconfiguration-config-web</artifactId>
    <version>1.2.7</version>
</dependency>
```

## <a name="run-and-test-the-app-locally"></a>Executar e testar o aplicativo localmente

1. Compile o aplicativo Spring Boot com Maven e execute-o.

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. Abra uma nova janela do navegador e acesse a URL: `http://localhost:8080`.  Você verá a mensagem associada à sua chave.

    Você também pode usar a *ondulação* para testar o aplicativo, por exemplo: 
    
    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. Para testar a configuração dinâmica, abra o portal de Configuração de Aplicativos do Azure associado ao aplicativo. Selecione **Gerenciador de Configurações** e atualize o valor da chave exibida, por exemplo:

    | Chave | Valor |
    |---|---|
    | application/config.message | Hello - Updated |

1. Atualize a página do navegador para ver a nova mensagem exibida.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você habilitou seu aplicativo Spring Boot para atualizar dinamicamente as definições de configuração na Configuração de Aplicativos. Para saber como usar uma identidade gerenciada pelo Azure para simplificar o acesso à Configuração de Aplicativos, passe para o próximo tutorial.

> [!div class="nextstepaction"]
> [Integração de identidade gerenciada](./howto-integrate-azure-managed-service-identity.md)
