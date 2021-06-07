---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: a0f3e3547c38df63bdab77cf378525072d1e9ad4
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106125913"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK (Java Development Kit)](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-install) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso e uma cadeia de conexão dos Serviços de Comunicação implantados. [Crie um recurso dos Serviços de Comunicação](../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Abra o terminal ou a janela Comando. Navegue até o diretório em que você deseja criar o aplicativo Java. Execute o comando abaixo para gerar o projeto Java no modelo maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você observará que a tarefa 'gerar' criou um diretório com o mesmo nome que o `artifactId`. Nesse diretório, o diretório src/main/java contém o código-fonte do projeto, o `src/test/java directory` contém a fonte de teste e o arquivo `pom.xml` é o Modelo de Objeto do Projeto, ou POM.

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo **pom.xml** no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-identity</artifactId>
    <version>1.0.0</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

No diretório do projeto:

1. Navegue até o diretório */src/main/java/com/communication/quickstart*
1. Abra o arquivo *App.java* em seu editor
1. Substitua a instrução `System.out.println("Hello world!");`
1. Adicione diretivas `import`

Use o seguinte código para começar:

```java
package com.communication.quickstart;

import com.azure.communication.common.*;
import com.azure.communication.identity.*;
import com.azure.communication.identity.models.*;
import com.azure.core.credential.*;

import java.io.IOException;
import java.time.*;
import java.util.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Access Tokens Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma instância de um `CommunicationIdentityClient` com a chave de acesso e o ponto de extremidade do recurso. Saiba como [gerenciar a cadeia de conexão do seu recurso](../create-communication-resource.md#store-your-connection-string). Além disso, você pode inicializar o cliente com qualquer cliente HTTP personalizado que implemente a interface `com.azure.core.http.HttpClient`.

Adicione o seguinte código ao método `main`:

```java
// Your can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(new AzureKeyCredential(accessKey))
        .buildClient();
```

Você também pode fornecer toda a cadeia de conexão usando a função `connectionString()` em vez de fornecer o ponto de extremidade e a chave de acesso.
```java
// Your can find your connection string from your resource in the Azure portal
String connectionString = "<connection_string>";

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

Se você tiver configurado a identidade gerenciada, consulte [Usar identidades gerenciadas](../managed-identity.md). Também é possível autenticar com a identidade gerenciada.
```java
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
TokenCredential credential = new DefaultAzureCredentialBuilder().build();

CommunicationIdentityClient communicationIdentityClient = new CommunicationIdentityClientBuilder()
        .endpoint(endpoint)
        .credential(credential)
        .buildClient();
```

## <a name="create-an-identity"></a>Criar uma identidade

Os Serviços de Comunicação do Azure mantêm um diretório de identidade leve. Use o método `createUser` para criar uma entrada no diretório com um `Id` exclusivo. Armazene a identidade recebida com o mapeamento para os usuários do aplicativo. Por exemplo, armazenando-os no banco de dados do servidor de aplicativos. A identidade é necessária posteriormente para emitir tokens de acesso.

```java
CommunicationUserIdentifier user = communicationIdentityClient.createUser();
System.out.println("\nCreated an identity with ID: " + user.getId());
```

## <a name="issue-access-tokens"></a>Emitir tokens de acesso

Use o método `getToken` a fim de emitir um token de acesso para uma identidade existente dos Serviços de Comunicação. O parâmetro `scopes` define o conjunto de primitivos, que autorizará esse token de acesso. Confira a [lista de ações compatíveis](../../concepts/authentication.md). A nova instância do parâmetro `user` pode ser construída com base na representação da cadeia de caracteres da identidade do Serviço de Comunicação do Azure.

```java
// Issue an access token with the "voip" scope for a user identity
List<CommunicationTokenScope> scopes = new ArrayList<>(Arrays.asList(CommunicationTokenScope.VOIP));
AccessToken accessToken = communicationIdentityClient.getToken(user, scopes);
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'voip' scope that expires at: " + expiresAt + ": " + token);
```

## <a name="create-an-identity-and-issue-token-in-one-call"></a>Criar um token de identidade e emissão em uma chamada

Como alternativa, use o método 'createUserAndToken' para criar uma entrada no diretório com um `Id` exclusivo, bem como emitir um token de acesso.

```java
List<CommunicationTokenScope> scopes = Arrays.asList(CommunicationTokenScope.CHAT);
CommunicationUserIdentifierAndToken result = communicationIdentityClient.createUserAndToken(scopes);
CommunicationUserIdentifier user = result.getUser();
System.out.println("\nCreated a user identity with ID: " + user.getId());
AccessToken accessToken = result.getUserToken();
OffsetDateTime expiresAt = accessToken.getExpiresAt();
String token = accessToken.getToken();
System.out.println("\nIssued an access token with 'chat' scope that expires at: " + expiresAt + ": " + token);
```

Os tokens de acesso são credenciais de curta duração que precisam ser reemitidas. Deixar de fazer isso pode causar a interrupção da experiência dos usuários do aplicativo. A propriedade `expiresAt` indica o tempo de vida do token de acesso.

## <a name="refresh-access-tokens"></a>Tokens de acesso de atualização

Para atualizar um token de acesso, use o objeto `CommunicationUserIdentifier` para reemitir:

```java
// Value existingIdentity represents identity of Azure Communication Services stored during identity creation
CommunicationUserIdentifier identity = new CommunicationUserIdentifier(existingIdentity);
AccessToken response = communicationIdentityClient.getToken(identity, scopes);
```

## <a name="revoke-access-tokens"></a>Revogar tokens de acesso

Em alguns casos, você pode revogar explicitamente os tokens de acesso. Por exemplo, quando o usuário de um aplicativo altera a senha usada para realizar a autenticação no serviço. O método `revokeTokens` invalida todos os tokens de acesso ativos que foram emitidos para a identidade.

```java
communicationIdentityClient.revokeTokens(user);
System.out.println("\nSuccessfully revoked all access tokens for user identity with ID: " + user.getId());
```

## <a name="delete-an-identity"></a>Excluir uma identidade

A exclusão de uma identidade revoga todos os tokens de acesso ativos e impede que você emita novos tokens de acesso para a identidade. Isso também remove todo o conteúdo persistente associado à identidade.

```java
communicationIdentityClient.deleteUser(user);
System.out.println("\nDeleted the user identity with ID: " + user.getId());
```

## <a name="run-the-code"></a>Executar o código

Navegue até o diretório que contém o arquivo *pom.xml* e compile o projeto usando o comando `mvn` a seguir.

```console
mvn compile
```

Em seguida, compile o pacote.

```console
mvn package
```

Execute o comando `mvn` a seguir para executar o aplicativo.

```console
mvn exec:java -Dexec.mainClass="com.communication.quickstart.App" -Dexec.cleanupDaemonThreads=false
```
