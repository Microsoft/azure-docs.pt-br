---
title: APIs do Cliente Java do Azure Service Fabric | Microsoft Docs
description: Gerar e usar as API do cliente Java do Service Fabric utilizando a especificação API REST do cliente Service Fabric
services: service-fabric
documentationcenter: java
author: rapatchi
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/27/2017
ms.author: rapatchi
ms.openlocfilehash: 97bba87331965b0f7ce20ec2ee089e0e18f72457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60720273"
---
# <a name="azure-service-fabric-java-client-apis"></a>APIs do Cliente Java do Microsoft Azure Service Fabric

As APIs do cliente do Service Fabric permitem a implantação e gerenciamento de contêineres e aplicativos baseados em microsserviços em um Cluster do Service Fabric no Azure, no computador de desenvolvimento local ou em outra nuvem local. Este artigo descreve como gerar e usar APIs do cliente Java do Service Fabric sobre as APIs REST do cliente Service Fabric

## <a name="generate-the-client-code-using-autorest"></a>Gerar o código cliente usando AutoRest

[AutoRest](https://github.com/Azure/autorest) é uma ferramenta que gera bibliotecas de cliente para acessar os serviços Web RESTful. Entrada para AutoRest é uma especificação que descreve a API REST usando o formato Especificação de OpenAPI. As [APIs REST do cliente Service Fabric](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) seguem essa especificação.

Siga as etapas mencionadas abaixo para gerar o código cliente Java do Service Fabric usando a ferramenta AutoRest.

1. Instalar o nodejs e o NPM em seu computador

    Se você estiver usando Linux, então:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Se você estiver usando o Mac OS X, então:
    ```bash
    brew install node
    ```

2. Instale AutoRest usando NPM.
    ```bash
    npm install -g autorest
    ```

3. Bifurcar e clonar o repositório [azure-rest-api-specs](https://github.com/Azure/azure-rest-api-specs) no computador local e vá para o local clonado do terminal do computador.


4. Vá para o local mencionado abaixo no repositório clonado.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Se a versão do cluster não for 6.0. *, então, vá para o diretório apropriado na pasta estável.
    >   

5. Execute o seguinte comando autorest para gerar o código cliente java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Abaixo, está um exemplo que demonstra o uso do autorest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   O comando a seguir considera o arquivo de especificação ``servicefabric.json`` como entrada e gera o código cliente java na pasta ``java-rest-api-     code`` e circunscreve o código no namespace ``servicefabricrest``. Após essa etapa, deve-se localizar duas pastas ``models``, ``implementation`` e dois arquivos ``ServiceFabricClientAPIs.java`` e ``package-info.java`` gerados na pasta ``java-rest-api-code``.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Inclua e use o cliente gerado em seu projeto

1. Adicione o código gerado apropriadamente ao seu projeto. É recomendável criar uma biblioteca usando o código gerado e incluir essa biblioteca no seu projeto.
2. Se você estiver criando uma biblioteca, inclua a seguinte dependência no projeto da biblioteca. Se você estiver seguindo uma abordagem diferente, então, inclua a dependência apropriadamente.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Por exemplo, se estiver usando o sistema de compilação Maven, inclua o seguinte no arquivo ``pom.xml``:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Crie um RestClient usando o seguinte código:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Use o objeto do cliente e faça as chamadas apropriadas, conforme necessário. Aqui estão alguns exemplos que demonstram o uso do objeto do cliente. Vamos supor que o pacote de aplicativos é construído e carregado no repositório de imagens antes de usar as API abaixo.
    * Provisionar um aplicativo
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Criar um aplicativo

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Compreendendo o código gerado
Para cada API, você encontrará quatro sobrecargas de implementação. Se houver parâmetros opcionais, então, você encontrará mais quatro variações, incluindo esses parâmetros opcionais. Por exemplo, considere a API ``removeReplica``.
 1. **public void removeReplica(String nodeName, UUID partitionId, String replicaId, Boolean forceRemove, Long timeout)**
    * Essa é a variante síncrona da chamada à API removeReplica
 2. **público ServiceFuture\<Void > removeReplicaAsync (nodeName, partitionId UUID, replicaId de cadeia de caracteres, booliano forceRemove, tempo limite longo ServiceCallback final da cadeia de caracteres\<Void > serviceCallback)**
    * Essa variante de chamada à API poderá ser utilizada, se você quiser usar a programação assíncrona baseada no futuro e usar retornos de chamada
 3. **público observável\<Void > removeReplicaAsync (cadeia de caracteres nodeName, partitionId UUID, replicaId de cadeia de caracteres)**
    * Essa variante da chamada à API poderá ser utilizada, se você quiser usar a programação assíncrona reativa
 4. **público observável\<ServiceResponse\<Void >> removeReplicaWithServiceResponseAsync (cadeia de caracteres nodeName, partitionId UUID, replicaId de cadeia de caracteres)**
    * Essa variante da chamada à API poderá ser utilizada, se você quiser usar a programação assíncrona reativa e lidar com resposta de REST bruta

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre as [APIS REST do Service Fabric REST](https://docs.microsoft.com/rest/api/servicefabric/)

