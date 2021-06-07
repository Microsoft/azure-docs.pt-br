---
title: Conectar-se à API de Serviços de Mídia do Azure v3 – Java
description: Este artigo descreve como se conectar à API de Serviços de Mídia do Azure v3 com o Java.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/17/2020
ms.custom: devx-track-java
ms.author: inhenkel
ms.openlocfilehash: 06923d7c198edc324d85b726cf91694d8cf7e1ca
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961346"
---
# <a name="connect-to-media-services-v3-api---java"></a>Conectar-se à API de Serviços de Mídia v3 – Java

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Este artigo mostra como se conectar ao SDK do Java dos Serviços de Mídia do Azure v3 usando o método de entrada da entidade de serviço.

Neste artigo, o Visual Studio Code é usado para desenvolver o aplicativo de exemplo.

## <a name="prerequisites"></a>Pré-requisitos

- Siga [Como escrever Java com o Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) para instalar:

   - JDK
   - Apache Maven
   - Pacote de Extensão Java
- Lembre-se de definir as variáveis de ambiente `JAVA_HOME` e `PATH`.
- [Crie uma conta de Serviços de Mídia](./account-create-how-to.md). Lembre-se do nome do grupo de recursos e da conta dos Serviços de Mídia.
- Siga as etapas descritas no tópico [APIs de acesso](./access-api-howto.md). Registre a ID da assinatura, a ID do aplicativo (ID do cliente), a chave de autenticação (segredo) e a ID do locatário que você precisará em uma etapa posterior.

Examine também:

- [Java no Visual Studio Code](https://code.visualstudio.com/docs/languages/java)
- [Gerenciamento de projetos Java no VS Code](https://code.visualstudio.com/docs/java/java-project)

> [!IMPORTANT]
> Examine [Convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

## <a name="create-a-maven-project"></a>Criar um projeto Maven

Abra uma ferramenta de linha de comando e execute `cd` em um diretório em que deseja criar o projeto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Quando você executa o comando, o `pom.xml`, o `App.java` e outros arquivos são criados. 

## <a name="add-dependencies"></a>Adicionar dependências

1. No Visual Studio Code, abra a pasta na qual o projeto está localizado
1. Encontre e abra o `pom.xml`
1. Adicione as dependências necessárias.

   Confira `pom.xml` no exemplo [Codificação de vídeo](https://github.com/Azure-Samples/media-services-v3-java/blob/master/VideoEncoding/EncodingWithMESCustomPreset/pom.xml).

## <a name="connect-to-the-java-client"></a>Conectar-se ao cliente Java

1. Abra o arquivo `App.java` em `src\main\java\com\azure\ams` e verifique se o pacote está incluído na parte superior:

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
1. Para criar as credenciais do Active Directory necessárias para fazer solicitações, adicione o seguinte código ao método principal da classe App e defina os valores obtidos nas [APIs de acesso](./access-api-howto.md):
   
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

## <a name="see-also"></a>Confira também

- [Conceitos dos Serviços de Mídia](concepts-overview.md)
- [Java SDK](https://aka.ms/ams-v3-java-sdk)
- [Referência do Java](/java/api/overview/azure/mediaservices/management)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Próximas etapas

Agora você pode incluir `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` e iniciar a manipulação de entidades.

Para obter mais exemplos de código, confira o repositório de [exemplos de SDK do Java](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/).
