---
title: Automatizar o registro e a descoberta de serviços
description: Saiba como automatizar a descoberta e o registro de serviços usando o registro de serviço do Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 1e60799878cc30b729344c03df36a4c5e4f4a199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90904214"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Descobrir e registrar seus serviços de nuvem Spring

A descoberta de serviço é um requisito fundamental para uma arquitetura baseada em microserviço.  Configurar cada cliente manualmente leva tempo e introduz a possibilidade de erro humano.  O registro do serviço de nuvem do Azure Spring resolve esse problema.  Uma vez configurado, um servidor de registro de serviço controlará o registro e a descoberta do serviço para os microserviços do seu aplicativo. O servidor do registro de serviço mantém um registro dos microserviços implantados, habilita o balanceamento de carga do lado do cliente e dissocia os provedores de serviço de clientes sem depender do DNS.

::: zone pivot="programming-language-csharp"
Para obter informações sobre como configurar o registro de serviço para um aplicativo Steeltoe, consulte [preparar um aplicativo Spring Java para implantação no Azure Spring Cloud](spring-cloud-tutorial-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrar seu aplicativo usando o registro de serviço do Spring Cloud

Antes que seu aplicativo possa gerenciar o registro de serviço e a descoberta usando o registro de serviço do Spring Cloud, várias dependências devem ser incluídas no arquivo de *pom.xml* do aplicativo.
Inclua dependências para *Spring-Cloud-startr-Netflix-Eureka-Client* e *Spring-Cloud-startr-Azure-Spring-Cloud-client* ao seu *pom.xml*

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
        <version>2.1.0</version>
    </dependency>
```

## <a name="update-the-top-level-class"></a>Atualizar a classe de nível superior

Por fim, adicionamos uma anotação à classe de nível superior do seu aplicativo

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.cloud.client.SpringCloudApplication;

    @SpringCloudApplication
    public class DemoApplication {
        public static void main(String... args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

O ponto de extremidade do servidor do registro do serviço de nuvem Spring será injetado como uma variável de ambiente em seu aplicativo.  Os microserviços agora serão capazes de se registrar no servidor do registro de serviço e descobrir outros microserviços dependentes.

Observe que pode levar alguns minutos para que as alterações sejam propagadas do servidor para todos os microserviços.
::: zone-end
