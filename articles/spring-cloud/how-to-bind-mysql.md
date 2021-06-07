---
title: Como associar uma instância do Banco de Dados do Azure para MySQL ao seu aplicativo do Azure Spring Cloud
description: Saiba como associar uma instância do Banco de Dados do Azure para MySQL ao aplicativo do Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: cc1c186f9acb6d49314f5d581a4b51ffe49d6627
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "104877737"
---
# <a name="bind-an-azure-database-for-mysql-instance-to-your-azure-spring-cloud-application"></a>Associar uma instância do Banco de Dados do Azure para MySQL ao seu aplicativo Azure Spring Cloud 

**Este artigo aplica-se a:** ✔️ Java

Com o Azure Spring Cloud, é possível associar serviços do Azure selecionados aos seus aplicativos automaticamente, em vez de precisar configurar manualmente seu aplicativo Spring Boot. Este artigo mostra como associar seu aplicativo à instância do Banco de Dados do Azure para MySQL.

## <a name="prerequisites"></a>Pré-requisitos

* Uma instância do Azure Spring Cloud implantada
* Uma conta do Banco de Dados do Azure para MySQL
* CLI do Azure

Se você não tiver uma instância do Azure Spring Cloud implantada, siga as instruções em [Início Rápido: Iniciar um aplicativo do Azure Spring Cloud usando o portal do Azure](spring-cloud-quickstart.md) para implantar seu primeiro aplicativo Spring Cloud.

## <a name="bind-your-app-to-your-azure-database-for-mysql-instance"></a>Associar seu aplicativo à instância do Banco de Dados do Azure para MySQL

1. Anote o nome de usuário administrador e a senha de sua conta do Banco de Dados do Azure para MySQL. 

1. Conecte-se ao servidor, crie um banco de dados chamado **testdb** de um cliente MySQL e, em seguida, crie uma conta que não seja de administrador.

1. No seu arquivo *pom.xml* do projeto, adicione a dependência a seguir:

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    ```
1. No arquivo *application.properties*, remova as propriedades `spring.datasource.*`.

1. Atualize a implantação atual executando `az spring-cloud app update` ou crie uma implantação para essa alteração executando `az spring-cloud app deployment create`.  Esses comandos atualizam ou criam o aplicativo com a nova dependência.

1. Na portal do Azure, na página de serviço do **Azure Spring Cloud**, procure o **Painel do Aplicativo** e, em seguida, selecione o aplicativo a ser associado à instância do Banco de Dados do Azure para MySQL.  Esse é o mesmo aplicativo que você atualizou ou implantou na etapa anterior. 

1. Selecione **Associação de serviço** e, em seguida, selecione o botão **Criar associação de serviço**. 

1. Preencha o formulário, selecionando **MySQL do Azure** como o **Tipo de associação**, usando o mesmo nome de banco de dados usado anteriormente e o mesmo nome de usuário e senha que você anotou na primeira etapa.

1. Reinicie o aplicativo e essa associação agora deve funcionar.

1. Para garantir que a associação de serviço esteja correta, selecione o nome da associação e verifique seus detalhes. O campo `property` deve ter esta aparência:
    ```
    spring.datasource.url=jdbc:mysql://some-server.mysql.database.azure.com:3306/testdb?useSSL=true&requireSSL=false&useLegacyDatetimeCode=false&serverTimezone=UTC
    spring.datasource.username=admin@some-server
    spring.datasource.password=abc******
    spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL5InnoDBDialect
    ```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a associar um aplicativo do Azure Spring Cloud a uma instância do Banco de Dados do Azure para MySQL. Para saber mais sobre como associar serviços a um aplicativo, confira [Associar um banco de dados do Azure Cosmos DB a um aplicativo do Azure Spring Cloud](spring-cloud-howto-bind-cosmos.md).
