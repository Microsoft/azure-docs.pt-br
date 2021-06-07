---
title: 'Início Rápido: Biblioteca de clientes de gerenciamento do Azure para Java'
description: Neste início rápido, comece a usar a biblioteca de clientes de gerenciamento do Azure para Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 4c0d4dd1a834e42a75da5199b7aaed0e123f8e63
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879701"
---
[Documentação de referência](/java/api/com.microsoft.azure.management.cognitiveservices) | [Código-fonte da biblioteca](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18) | [Pacote (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>Pré-requisitos para usar o Java

* Uma assinatura válida do Azure; [crie uma gratuitamente](https://azure.microsoft.com/free/).
* A versão atual do [JDK (Java Development Kit)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [ferramenta de build Gradle](https://gradle.org/install/) ou outro gerenciador de dependência.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Criar um aplicativo Java

Em uma janela de console (como cmd, PowerShell ou Bash), crie um novo diretório para seu aplicativo e navegue até ele. 

```console
mkdir myapp && cd myapp
```

Execute o comando `gradle init` em seu diretório de trabalho. Esse comando criará arquivos de build essenciais para o Gradle, incluindo *build.gradle.kts*, que é usado em runtime para criar e configurar seu aplicativo.

```console
gradle init --type basic
```

Quando solicitado a escolher uma **DSL**, escolha **Kotlin**.

Do diretório de trabalho, execute o seguinte comando:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Instalar a biblioteca de clientes

Este início rápido usa o gerenciador de dependência do Gradle. Você pode encontrar a biblioteca de clientes e informações para outros gerenciadores de dependência no [Repositório Central do Maven](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

No arquivo *build.gradle.kts* do projeto, inclua a biblioteca de clientes como uma instrução `implementation`, juntamente com as configurações e os plug-ins necessários.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Importar bibliotecas

Navegue até a nova pasta **src/main/java** e crie um arquivo chamado *Management.java*. Abra-a no editor ou IDE de sua preferência e adicione as seguintes instruções `import`:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autenticar o cliente

Adicione uma classe em *Management.java* e adicione os campos e os valores dentro dele a seguir. Preencha os valores usando a entidade de serviço que você criou e outras informações da conta do Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Em seguida, no método **main**, use esses valores para construir um objeto **CognitiveServicesManager**. Esse objeto é necessário para todas as operações de gerenciamento do Azure.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Chamar métodos gerenciamento

Adicione o código a seguir ao método **Main** para listar os recursos disponíveis, criar um recurso de exemplo, listar os recursos de sua propriedade e excluir o recurso de exemplo. Você definirá esses métodos nas etapas a seguir.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>Criar um recurso dos Serviços Cognitivos (Java)

Para criar e assinar um recurso dos Serviços Cognitivos, use o método **create**. Esse método adiciona um novo recurso que pode ser cobrado ao grupo de recursos que você passa. Ao criar o recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o tipo de preço (ou SKU) desejado e um local do Azure. O método a seguir usa todos esses argumentos e cria um recurso.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Escolher um serviço e um tipo de preço

Ao criar um recurso, você precisará saber qual "tipo" de serviço deseja usar, bem como o [tipo de preço](https://azure.microsoft.com/pricing/details/cognitive-services/) (ou SKU) desejado. Você usará essa e outras informações como parâmetros ao criar o recurso. Você pode encontrar uma lista dos "tipos" de Serviços Cognitivos disponíveis chamando o seguinte método:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Exibir os recursos

Para exibir todos os recursos em sua conta do Azure (em todos os grupos de recursos), use o seguinte método:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Excluir um recurso

O método a seguir exclui o recurso especificado do grupo de recursos fornecido.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Veja também

* Confira **[Autenticar solicitações para os Serviços Cognitivos do Azure](../../authentication.md)** para saber como trabalhar com segurança com os Serviços Cognitivos.
* Confira **[O que são os Serviços Cognitivos do Azure?](../../what-are-cognitive-services.md)** para obter uma lista de categorias diferentes dentro dos Serviços Cognitivos.
* Confira **[Suporte para linguagem natural](../../language-support.md)** para ver a lista de linguagens naturais a que os Serviços Cognitivos dão suporte.
* Confira **[Usar os Serviços Cognitivos como contêineres](../../cognitive-services-container-support.md)** para saber como usar os Serviços Cognitivos no local.
* Confira **[Planejar e gerenciar custos para os Serviços Cognitivos](../../plan-manage-costs.md)** para estimar o custo de uso dos Serviços Cognitivos.
* Confira a **[documentação de referência do SDK de Gerenciamento do Azure](/java/api/com.microsoft.azure.management.cognitiveservices)** para obter mais detalhes sobre o SDK de gerenciamento.
