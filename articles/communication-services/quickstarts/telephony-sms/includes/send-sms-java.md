---
title: incluir arquivo
description: incluir arquivo
services: azure-communication-services
author: chrwhit
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: chrwhit
ms.openlocfilehash: 1c4f3c47e3ac6e1e701b673574bb664237c1a9af
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244824"
---
Comece a usar os Serviços de Comunicação do Azure usando a biblioteca de clientes de SMS do Java dos Serviços de Comunicação para enviar mensagens SMS.

A realização deste início rápido gera um pequeno custo de alguns centavos de dólar ou menos em sua conta do Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Artifact (Maven)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK (Java Development Kit)](/java/azure/jdk/) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi).
- Um recurso e uma cadeia de conexão ativos dos Serviços de Comunicação. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).
- Um número de telefone habilitado para SMS. [Obter um número de telefone](../get-phone-number.md).

### <a name="prerequisite-check"></a>Verificação de pré-requisitos

- Em um terminal ou janela de comando, execute `mvn -v` para verificar se o maven está instalado.
- Para exibir os números de telefone associados ao seu recurso dos Serviços de Comunicação, entre no [portal do Azure](https://portal.azure.com/), localize o recurso em questão e abra a guia **números de telefone** no painel de navegação esquerdo.

## <a name="setting-up"></a>Configurando

### <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Abra o terminal ou a janela de comando e navegue até o diretório no qual você deseja criar o seu aplicativo Java. Execute o comando abaixo para gerar o projeto Java no modelo maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

A meta 'generate' criou um diretório com o mesmo nome que artifactId. Nesse diretório, o diretório **src/main/java** contém o código-fonte do projeto, o **src/test/java directory** contém a fonte de teste e o arquivo **pom.xml** é o Modelo de Objeto do Projeto (ou POM) do projeto.

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo **pom.xml** no seu editor de texto. Adicione o seguinte elemento de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-sms</artifactId>
    <version>1.0.0-beta.3</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>Configurar o framework de aplicativos

Adicione a dependência `azure-core-http-netty` ao seu arquivo **pom.xml**.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-core-http-netty</artifactId>
    <version>1.3.0</version>
</dependency>
```

Abra **/src/main/java/com/communication/quickstart/App.java** em um editor de texto, adicione diretivas de importação e remova a instrução `System.out.println("Hello world!");`:

```java
package com.communication.quickstart;

import java.io.IOException;
import java.security.InvalidKeyException;
import java.security.NoSuchAlgorithmException;
import java.util.ArrayList;
import java.util.List;

import com.azure.communication.common.PhoneNumber;
import com.azure.communication.sms.SmsClient;
import com.azure.communication.sms.SmsClientBuilder;
import com.azure.communication.sms.models.SendSmsOptions;
import com.azure.communication.sms.models.SendSmsResponse;
import com.azure.core.http.HttpClient;
import com.azure.core.http.netty.NettyAsyncHttpClientBuilder;

public class App
{
    public static void main( String[] args ) throws IOException, NoSuchAlgorithmException, InvalidKeyException
    {
        // Quickstart code goes here
    }
}

```

## <a name="object-model"></a>Modelo de objeto

As classes e as interfaces a seguir lidam com alguns dos principais recursos da biblioteca de clientes de SMS dos Serviços de Comunicação do Azure para Java.

| Name                                                             | Descrição                                                                                     |
| ---------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| SmsClientBuilder              | Essa classe cria o SmsClient. Ela é fornecida com o ponto de extremidade, a credencial e um cliente http. |
| SmsClient                    | Essa classe é necessária para toda a funcionalidade de SMS. Ela é usada para enviar mensagens SMS.                |
| SendSmsResponse               | Essa classe contém a resposta do serviço de SMS.                                          |
| PhoneNumber                   | Essa classe contém informações de número de telefone

## <a name="authenticate-the-client"></a>Autenticar o cliente

Crie uma instância de um `SmsClient` com a sua cadeia de conexão. O código a seguir recupera as cadeias de caracteres de credencial e o ponto de extremidade do recurso nas variáveis de ambiente chamadas `COMMUNICATION_SERVICES_ENDPOINT_STRING` e `COMMUNICATION_SERVICES_CREDENTIAL_STRING` (a credencial é a `Key` do portal do Azure). Saiba como [gerenciar a cadeia de conexão do recurso](../../create-communication-resource.md#store-your-connection-string).

Adicione o seguinte código ao método `main`:

```java
// Your can find your endpoint and access key from your resource in the Azure Portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";
String accessKey = "SECRET";

// Create an HttpClient builder of your choice and customize it
HttpClient httpClient = new NettyAsyncHttpClientBuilder().build();

// Configure and build a new SmsClient
SmsClient client = new SmsClientBuilder()
    .endpoint(endpoint)
    .accessKey(accessKey)
    .httpClient(httpClient)
    .buildClient();
```

Você pode inicializar o cliente com qualquer cliente HTTP personalizado que implemente a interface `com.azure.core.http.HttpClient`. O código acima demonstra o uso do [cliente HTTP do Azure Core Netty](/java/api/overview/azure/core-http-netty-readme) fornecido por `azure-core`.

Você também pode fornecer toda a cadeia de conexão usando a função connectionString() em vez de fornecer o ponto de extremidade e a chave de acesso. 
```java
// Your can find your connection string from your resource in the Azure Portal
String connectionString = "<connection_string>";
SmsClient client = new SmsClientBuilder()
    .connectionString(connectionString)
    .httpClient(httpClient)
    .buildClient();
```

## <a name="send-an-sms-message"></a>Enviar uma mensagem SMS

Envie uma mensagem SMS chamando o método sendMessage. Adicione este código ao final do método `main`:

```java
List<PhoneNumber> to = new ArrayList<PhoneNumber>();
to.add(new PhoneNumber("<to-phone-number>"));

// SendSmsOptions is an optional field. It can be used
// to enable a delivery report to the Azure Event Grid
SendSmsOptions options = new SendSmsOptions();
options.setEnableDeliveryReport(true);

// Send the message and check the response for a message id
SendSmsResponse response = client.sendMessage(
    new PhoneNumber("<leased-phone-number>"),
    to,
    "<message-text>",
    options);

System.out.println("MessageId: " + response.getMessageId());
```

Você deve substituir `<leased-phone-number>` pelo número de telefone habilitado para SMS associado ao seu recurso dos Serviços de Comunicação e `<to-phone-number>` pelo número de telefone para o qual você deseja enviar uma mensagem.

O parâmetro `enableDeliveryReport` é um parâmetro opcional que você pode usar para configurar o Relatório de Entrega. Isso é útil para cenários em que você deseja emitir eventos quando mensagens SMS são entregues. Confira o guia de início rápido [Manipular eventos de SMS](../handle-sms-events.md) para configurar o Relatório de Entrega das suas mensagens SMS.

<!--todo: the signature of the `sendMessage` method changes when configuring delivery reporting. Need to confirm that this is how our client library is to be used.-->

## <a name="run-the-code"></a>Executar o código

Navegue até o diretório que contém o arquivo **pom.xml** e compile o projeto usando o comando `mvn`.

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