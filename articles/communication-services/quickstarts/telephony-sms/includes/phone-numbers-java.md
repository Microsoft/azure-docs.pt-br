---
ms.openlocfilehash: 4891a602e9fb5ebbdaadc980c1960b95816c6653
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113370"
---
## <a name="prerequisites"></a>Pré-requisitos

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [JDK (Java Development Kit)](/java/azure/jdk/?preserve-view=true&view=azure-java-stable) versão 8 ou superior.
- [Apache Maven](https://maven.apache.org/download.cgi)– um recurso e uma cadeia de conexão dos Serviços de Comunicação implantados. [Crie um recurso dos Serviços de Comunicação](../../create-communication-resource.md).

## <a name="setting-up"></a>Ao configurar o

### <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Abra o terminal ou a janela Comando. Navegue até o diretório em que você deseja criar o aplicativo Java. Execute o comando abaixo para gerar o projeto Java no modelo maven-archetype-quickstart.

```console
mvn archetype:generate -DgroupId=com.communication.quickstart -DartifactId=communication-quickstart -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

Você observará que a tarefa 'gerar' criou um diretório com o mesmo nome que o `artifactId`. Nesse diretório, o diretório src/main/java contém o código-fonte do projeto, o `src/test/java directory` contém a fonte de teste e o arquivo `pom.xml` é o Modelo de Objeto do Projeto, ou POM.

### <a name="install-the-package"></a>Instalar o pacote

Abra o arquivo **pom.xml** no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-communication-phonenumbers</artifactId>
    <version>1.0.0-beta.7</version>
</dependency>

<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-identity</artifactId>
    <version>1.2.3</version>
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
import com.azure.communication.phonenumbers.*;
import com.azure.communication.phonenumbers.models.*;
import java.io.*;
import com.azure.core.util.Context;
import com.azure.core.util.polling.PollResponse;
import com.azure.identity.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
        System.out.println("Azure Communication Services - Phone Numbers Quickstart");
        // Quickstart code goes here
    }
}
```

## <a name="authenticate-the-phone-numbers-client"></a>Autenticar o Cliente de Números de Telefone

O PhoneNumberClientBuilder está habilitado para usar a autenticação do Azure Active Directory
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L52-L62 -->
```java
// You can find your endpoint and access key from your resource in the Azure portal
String endpoint = "https://<RESOURCE_NAME>.communication.azure.com";

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .endpoint(endpoint)
    .credential(new DefaultAzureCredentialBuilder().build())
    .buildClient();
```

Como alternativa, o uso do ponto de extremidade e da chave de acesso do recurso de comunicação para autenticar também é possível.
<!-- embedme ./src/samples/java/com/azure/communication/phonenumbers/ReadmeSamples.java#L30-L41 -->
```java
// You can find your connection string from your resource in the Azure portal
String connectionString = "https://<RESOURCE_NAME>.communication.azure.com/;accesskey=<ACCESS_KEY>";

PhoneNumbersClient phoneNumberClient = new PhoneNumbersClientBuilder()
    .connectionString(connectionString)
    .buildClient();
```

## <a name="manage-phone-numbers"></a>Gerenciar números de telefone

### <a name="search-for-available-phone-numbers"></a>Pesquisar números de telefone disponíveis

Para comprar números de telefone, primeiro você precisa procurar números de telefone disponíveis. Para procurar números de telefone, forneça o código de área, o tipo de atribuição, as [funcionalidades do número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services), o [tipo de número de telefone](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services) e a quantidade. Observe que para números de telefone do tipo chamada gratuita, fornecer o código de área é opcional.

```java
 PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities()
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PhoneNumberSearchOptions searchOptions = new PhoneNumberSearchOptions().setAreaCode("833").setQuantity(1);

PhoneNumberSearchResult searchResult = phoneNumberClient
    .beginSearchAvailablePhoneNumbers("US", PhoneNumberType.TOLL_FREE, PhoneNumberAssignmentType.APPLICATION, capabilities,  searchOptions, Context.NONE)
    .getFinalResult();

System.out.println("Searched phone numbers: " + searchResult.getPhoneNumbers());
System.out.println("Search expires by: " + searchResult.getSearchExpiresBy());
System.out.println("Phone number costs:" + searchResult.getCost().getAmount());
```

### <a name="purchase-phone-numbers"></a>Comprar números de telefone

O resultado da pesquisa por números de telefone é um PhoneNumberSearchResult. Ela contém um `searchId` que pode ser passado para a API de comprar números para adquirir os números na pesquisa. Observe que chamar a API de comprar números de telefone resultará em um encargo para sua conta do Azure.

```java
PollResponse<PhoneNumberOperation> purchaseResponse = phoneNumberClient.beginPurchasePhoneNumbers(searchResult.getSearchId(), Context.NONE).waitForCompletion();
System.out.println("Purchase phone numbers operation is: " + purchaseResponse.getStatus());
```

### <a name="get-phone-numbers"></a>Obter números de telefone

Depois de comprar um número, você poderá recuperá-lo do cliente.
```java
PurchasedPhoneNumber phoneNumber = phoneNumberClient.getPurchasedPhoneNumber("+14255550123");
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

Você também pode recuperar todos os números de telefone comprados.
``` java
PagedIterable<PurchasedPhoneNumber> phoneNumbers = phoneNumberClient.listPurchasedPhoneNumbers(Context.NONE);
PurchasedPhoneNumber phoneNumber = phoneNumbers.iterator().next();
System.out.println("Phone Number Country Code: " + phoneNumber.getCountryCode());
```

### <a name="update-phone-number-capabilities"></a>Atualizar as funcionalidades do número de telefone

Com um número comprado, você pode atualizar as funcionalidades.
```java
PhoneNumberCapabilities capabilities = new PhoneNumberCapabilities();
capabilities
    .setCalling(PhoneNumberCapabilityType.INBOUND)
    .setSms(PhoneNumberCapabilityType.INBOUND_OUTBOUND);
PurchasedPhoneNumber phoneNumber = phoneNumberClient.beginUpdatePhoneNumberCapabilities("+14255550123", capabilities, Context.NONE).getFinalResult();

System.out.println("Phone Number Calling capabilities: " + phoneNumber.getCapabilities().getCalling()); //Phone Number Calling capabilities: inbound
System.out.println("Phone Number SMS capabilities: " + phoneNumber.getCapabilities().getSms()); //Phone Number SMS capabilities: inbound+outbound
```

### <a name="release-phone-number"></a>Liberar número de telefone

Você pode liberar um número de telefone comprado.
```java
PollResponse<PhoneNumberOperation> releaseResponse =
    phoneNumberClient.beginReleasePhoneNumber("+14255550123", Context.NONE).waitForCompletion();
System.out.println("Release phone number operation is: " + releaseResponse.getStatus());
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

A saída do aplicativo descreve cada ação concluída:
<!---cSpell:disable --->
```console
Azure Communication Services - Phone Numbers Quickstart

Searched phone numbers: [+18001234567]

Purchase phone numbers operation is: SUCCESSFULLY_COMPLETED

Phone Number Country Code: US

Phone Number Calling capabilities: inbound

Phone Number SMS capabilities: inbound

Release phone number operation is: SUCCESSFULLY_COMPLETED

```
<!---cSpell:enable --->