---
title: Conectar-se à API de v3 de serviços de mídia do Azure - Java
description: Saiba como se conectar à API do serviços de mídia v3 com Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: juliako
ms.openlocfilehash: 68e09ec6ce4aeb91e00c2a15caa8ec81f40064c1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60733905"
---
# <a name="connect-to-media-services-v3-api---java"></a>Conectar-se à API de v3 de serviços de mídia - Java

Este artigo mostra como conectar-se ao SDK dos serviços de mídia do Azure v3 Java usando o logon de entidade de serviço no método.

Neste artigo, o Visual Studio Code é usado para desenvolver o aplicativo de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

- Siga [escrita Java com o Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) instalar:

   - JDK
   - Apache Maven
   - Pacote de extensão Java
- Verifique se você definiu `JAVA_HOME` e `PATH` variáveis de ambiente.
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Certifique-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia.
- Siga as etapas a [acessar APIs](access-api-cli-how-to.md) tópico. Registre a ID da assinatura, ID do aplicativo (ID do cliente), a chave de autenticação (segredo) e a ID do locatário que você precisa em uma etapa posterior.

Examine também:

- [Java no Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gerenciamento de projetos de Java no VS Code](https://code.visualstudio.com/docs/java/java-project)

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Abra uma ferramenta de linha de comando e `cd` para um diretório onde você deseja criar o projeto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando você executa o comando, o `pom.xml`, `App.java`, e outros arquivos são criados. 

## <a name="add-dependencies"></a>Adicionar dependências

1. No Visual Studio Code, abra a pasta onde está o seu projeto
1. Localize e abra o `pom.xml`
1. Adicione as dependências necessárias

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
```

## <a name="connect-to-the-java-client"></a>Conectar-se para o cliente de Java

1. Abra o `App.java` do arquivo sob `src\main\java\com\azure\ams` e verifique se o seu pacote está incluído na parte superior:

    ```java
    package com.azure.ams;
    ```
1. Abaixo da declaração de pacote, adicione estas instruções de importação:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Para criar as credenciais do Active Directory que você precisa para fazer solicitações, adicione o seguinte código ao método principal da classe App e defina os valores que você obteve [acessar APIs](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Execute o aplicativo.

## <a name="see-also"></a>Consulte também

- [Conceitos dos Serviços de Mídia](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Referência do Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Próximas etapas

Agora você pode incluir `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` e comece a manipulação de entidades.
