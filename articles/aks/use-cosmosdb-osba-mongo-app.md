---
title: Integrar aplicativo MongoDB existente com a API do Azure Cosmos DB para MongoDB e OSBA (Open Service Broker para Azure)
description: Neste artigo, você aprenderá a integrar um aplicativo Java e MongoDB existente com a API do Azure Cosmos DB para MongoDB usando OSBA (Open Service Broker para Azure).
services: azure-dev-spaces
author: zr-msft
manager: jeconnoc
ms.service: azure-dev-spaces
ms.topic: article
ms.date: 01/25/2019
ms.author: zarhoads
ms.custom: mvc
keywords: Cosmos DB, Open Service Broker, Open Service Broker para Azure
ms.openlocfilehash: 46fa5564e5dd3429f812b263295044d867a8511c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61028400"
---
# <a name="integrate-existing-mongodb-application-with-azure-cosmos-db-api-for-mongodb-and-open-service-broker-for-azure-osba"></a>Integrar aplicativo MongoDB existente com a API do Azure Cosmos DB para MongoDB e OSBA (Open Service Broker para Azure)

O Azure Cosmos DB é um serviço de multimodelo de banco de dados distribuído globalmente. Adicionalmente, fornece compatibilidade com protocolo de fios com várias APIs de NoSQL, incluindo para MongoDB. A API do Cosmos DB para MongoDB permite que você use o Cosmos DB com o aplicativo MongoDB existente, sem a necessidade de alterar os drivers ou a implementação do banco de dados do aplicativo. Também é possível provisionar um serviço do Cosmos DB usando Open Service Broker para Azure.

Neste artigo, você seleciona um aplicativo Java existente que usa um banco de dados MongoDB e atualiza-o para usar um banco de dados do Cosmos DB usando Open Service Broker para Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de continuar, você precisará:
    
* Ter criado um [cluster do Serviço de Kubernetes do Azure](kubernetes-walkthrough.md).
* Ter o [Open Service Broker para Azure instalado e configurado no cluster do AKS](integrate-azure.md). 
* Ter a [CLI do Catálogo de Serviços](https://svc-cat.io/docs/install/) instalada e configurada para executar `svcat` comandos.
* Ter um banco de dados [MongoDB](https://www.mongodb.com/) existente. Por exemplo, é possível ter o MongoDB em execução no [computador de desenvolvimento](https://docs.mongodb.com/manual/administration/install-community/) ou em uma [VM do Azure](../virtual-machines/linux/install-mongodb.md).
* Ter um meio de conectar e consultar o banco de dados MongoDB como, por exemplo, o [mongo shell](https://docs.mongodb.com/manual/mongo/).

## <a name="get-application-code"></a>Obter o código de aplicativo
    
Neste artigo, você usa o [aplicativo de exemplo spring music do Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) para demonstrar um aplicativo que usa um banco de dados MongoDB.
    
Clone o aplicativo do GitHub e navegue para seu diretório:
    
```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring-music
```

## <a name="prepare-the-application-to-use-your-mongodb-database"></a>Preparar o aplicativo para usar o banco de dados MongoDB

O aplicativo de exemplo spring music fornece muitas opções para fontes de dados. Neste artigo, ele é configurado para usar um banco de dados MongoDB existente. 

Adicione o YAML seguinte no final do *src/main/resources/application.yml*. Essa adição cria um perfil chamado *mongodb* e configura um nome de banco de dados e URI. Substitua o URI pelas informações de conexão com o banco de dados MongoDB existente. Adicionar o URI, que contém um nome de usuário e senha, diretamente a esse arquivo é apenas para **uso de desenvolvimento** e **nunca deve ser adicionado ao controle de versão**.

```yaml
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://user:password@serverAddress:port/musicdb"
      database: musicdb
```



Ao iniciar o aplicativo e informá-lo para usar o perfil *mongodb*, ele conectará o banco de dados do MongoDB e irá utilizá-lo para armazenar os dados do aplicativo.

Para criar o aplicativo:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```

Inicie o aplicativo e informe-o para usar o perfil *mongodb*:

```cmd
java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Navegue até `http://localhost:8080` no navegador.

![Aplicativo Spring Music com os dados padrão](media/music-app.png)

Observe que o aplicativo foi preenchido com alguns [dados padrão](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). Interaja com o aplicativo, excluindo alguns álbuns existentes e criando alguns novos.

É possível verificar se o aplicativo está usando o banco de dados MongoDB, conectando-o e consultando o banco de dados *musicdb*:

```cmd
mongo serverAddress:port/musicdb -u user -p password
use musicdb
db.album.find()

{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446d"), "title" : "Nevermind", "artist" : "Nirvana", "releaseYear" : "1991", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446e"), "title" : "Pet Sounds", "artist" : "The Beach Boys", "releaseYear" : "1966", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
{ "_id" : ObjectId("5c1bb6f5df0e66f13f9c446f"), "title" : "What's Going On", "artist" : "Marvin Gaye", "releaseYear" : "1971", "genre" : "Rock", "trackCount" : 0, "_class" : "org.cloudfoundry.samples.music.domain.Album" }
...
```

O exemplo anterior usa o [mongo shell](https://docs.mongodb.com/manual/mongo/) para conectar o banco de dados MongoDB e consultá-lo. Além disso, é possível verificar se as alterações persistem, parando o aplicativo, reiniciando-o e navegando de volta até ele no navegador. Observe que as alterações que você fez ainda estão lá.


## <a name="create-a-cosmos-db-database"></a>Criar um banco de dados do Cosmos DB

Para criar um banco de dados do Cosmos DB no Azure usando o Open Service Broker, use o comando `svcat provision`:

```cmd
svcat provision musicdb --class azure-cosmosdb-mongo-account --plan account  --params-json '{
  "location": "eastus",
  "resourceGroup": "MyResourceGroup",
  "ipFilters" : {
    "allowedIPRanges" : ["0.0.0.0/0"]
  }
}'
```

O comando anterior provisiona um banco de dados do Cosmos DB no Azure no grupo de recursos *MyResourceGroup* na região *eastus*. Mais informações sobre *resourceGroup*, *location* e outros parâmetros JSON específicos do Azure estão disponíveis na [documentação de referência do módulo do Cosmos DB](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/cosmosdb.md#provision-3).

Para verificar se o banco de dados concluiu o provisionamento, use o comando `svcat get instance`:

```cmd
$ svcat get instance musicdb

   NAME     NAMESPACE              CLASS                PLAN     STATUS
+---------+-----------+------------------------------+---------+--------+
  musicdb   default     azure-cosmosdb-mongo-account   account   Ready
```

O banco de dados estará pronto quando visualizar *Pronto* no *STATUS*.

Depois que o banco de dados concluir o provisionamento, será necessário associar os metadados a um [Segredo do Kubernetes](https://kubernetes.io/docs/concepts/configuration/secret/). Outros aplicativos poderão acessar esses dados, após terem sido associados a um segredo. Para associar os metadados do banco de dados a um segredo, use o comando `svcat bind`:

```cmd
$ svcat bind musicdb

  Name:        musicdb
  Namespace:   default
  Status:
  Secret:      musicdb
  Instance:    musicdb

Parameters:
  No parameters defined
```


## <a name="use-the-cosmos-db-database-with-your-application"></a>Usar o banco de dados do Cosmos DB com o aplicativo

Para usar o banco de dados do Cosmos DB com o aplicativo, é necessário conhecer o URI para conectá-lo. Para obter essas informações, use o comando `kubectl get secret`:

```cmd
$ kubectl get secret musicdb -o=jsonpath='{.data.uri}' | base64 --decode

mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb
```

O comando anterior obtém o segredo do *musicdb* e exibe apenas o URI. Os segredos são armazenados no formato Base64, portanto, o comando anterior também o decodifica.

Usando o URI do banco de dados do Cosmos DB, atualize *src/main/resources/application.yml* para usá-lo:

```yaml
...
---
spring:
  profiles: mongodb
  data:
    mongodb:
      uri: "mongodb://12345678-90ab-cdef-1234-567890abcdef:aaaabbbbccccddddeeeeffffgggghhhhiiiijjjjkkkkllllmmmmnnnnooooppppqqqqrrrrssssttttuuuuvvvv@098765432-aaaa-bbbb-cccc-1234567890ab.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"
      database: musicdb
```

Atualizar o URI, que contém um nome de usuário e senha, diretamente a esse arquivo é apenas para **uso de desenvolvimento** e **nunca deve ser adicionado ao controle de versão**.

Recrie e inicie o aplicativo para começar a usar o banco de dados do Cosmos DB:

```cmd
./gradlew clean assemble

java -jar -Dspring.profiles.active=mongodb build/libs/spring-music-1.0.jar
```

Observe que o aplicativo ainda usa o perfil *mongodb* e um URI que começa com *mongodb://* para conectar o banco de dados do Cosmos DB. A [API do Azure Cosmos DB para MongoDB](../cosmos-db/mongodb-introduction.md) fornece essa compatibilidade. Ela permite que o aplicativo continue operando como se estivesse usando um banco de dados MongoDB, mas na verdade está usando o Cosmos DB.

Navegue até `http://localhost:8080` no navegador. Observe que os dados padrão foram restaurados. Interaja com o aplicativo, excluindo alguns álbuns existentes e criando alguns novos. É possível verificar se as alterações persistem, parando o aplicativo, reiniciando-o e navegando de volta até ele no navegador. Observe que as alterações que você fez ainda estão lá. As alterações são persistidas no banco de dados do Cosmos que você criou usando o Open Service Broker para Azure.


## <a name="run-your-application-on-your-aks-cluster"></a>Executar o aplicativo no cluster do AKS

Você pode usar o [Azure Dev Spaces](../dev-spaces/azure-dev-spaces.md) para implantar o aplicativo no cluster do AKS. Espaços de desenvolvimento do Azure ajuda você a gerar artefatos, como Dockerfiles e gráficos Helm e implantar e executar um aplicativo no AKS.

Para habilitar o Azure Dev Spaces no cluster do AKS:

```cmd
az aks enable-addons --addons http_application_routing -g MyResourceGroup -n MyAKS
az aks use-dev-spaces -g MyResourceGroup -n MyAKS
```

Use o conjunto de ferramentas do Azure Dev Spaces para preparar o aplicativo para ser executado no AKS:

```cmd
azds prep --public
```

Esse comando gera vários artefatos, incluindo uma pasta *charts/*, que é o gráfico do Helm, na raiz do projeto. Esse comando não pode gerar um *Dockerfile* para este projeto específico, portanto, será necessário criá-lo.

Crie um arquivo na raiz do projeto nomeado *Dockerfile* com este conteúdo:

```Dockerfile
FROM openjdk:8-jdk-alpine
EXPOSE 8080
WORKDIR /app
COPY build/libs/spring-music-1.0.jar .
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
```

Além disso, é necessário atualizar a propriedade *configurations.develop.build* em *azds.yaml* para *falso*:
```yaml
...
configurations:
  develop:
    build:
      useGitIgnore: false
```

Atualize também o atributo *containerPort* para *8080* em *charts/spring-music/templates/deployment.yaml*:

```yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 8080
              protocol: TCP
```

Para implantar o aplicativo no AKS:

```cmd
$ azds up

Using dev space 'default' with target 'MyAKS'
Synchronizing files...1m 18s
Installing Helm chart...5s
Waiting for container image build...23s
Building container image...
Step 1/5 : FROM openjdk:8-jdk-alpine
Step 2/5 : EXPOSE 8080
Step 3/5 : WORKDIR /app
Step 4/5 : COPY build/libs/spring-music-1.0.jar .
Step 5/5 : ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-Dspring.profiles.active=mongodb","-jar","/app/spring-music-1.0.jar"]
Built container image in 21s
Waiting for container...8s
Service 'spring-music' port 'http' is available at http://spring-music.1234567890abcdef1234.eastus.aksapp.io/
Service 'spring-music' port 8080 (TCP) is available at http://localhost:57892
press Ctrl+C to detach
...
```

Navegue até a URL exibida nos logs. No exemplo anterior, você usaria *http://spring-music.1234567890abcdef1234.eastus.aksapp.io/*. 

Verifique se você vê o aplicativo junto com suas alterações.

## <a name="next-steps"></a>Próximas etapas

Este artigo descreveu como atualizar um aplicativo existente usando o MongoDB para usar a API do Cosmos DB API para MongoDB. Este artigo também abordou como provisionar um serviço do Cosmos DB usando Open Service Broker para Azure e implantar esse aplicativo no AKS com o Azure Dev Spaces.

Para obter mais informações sobre o Cosmos DB, Open Service Broker para Azure e Azure Dev Spaces, consulte:
* [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/)
* [Open Service Broker para Azure](https://osba.sh)
* [Desenvolver com Dev Spaces](../dev-spaces/azure-dev-spaces.md)
