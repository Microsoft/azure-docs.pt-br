---
title: Compilar um aplicativo Web do Java no Linux – Serviço de Aplicativo do Azure
description: Compilar, implantar e dimensionar aplicativos Web de Spring Boot Java com o Serviço de Aplicativo do Azure no Linux e Azure Cosmos DB.
author: rloutlaw
ms.author: routlaw
manager: angerobe
ms.service: app-service-web
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: seodec18, seo-java-july2019
ms.openlocfilehash: 625e40b4dcb8ac558831508b292fd5cbcfb90b5b
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489446"
---
# <a name="tutorial-build-a-java-web-app-using-spring-and-azure-cosmos-db"></a>Tutorial: Compilar um aplicativo Web do Java usando Spring e Azure Cosmos DB

Este tutorial orienta você pelo processo de criar, configurar, implantar e dimensionar aplicativos Web Java no Azure. Quando tiver terminado, você terá um aplicativo [Spring Boot](https://projects.spring.io/spring-boot/) armazenando dados no [Azure Cosmos DB](/azure/cosmos-db) em execução em [Serviço de Aplicativo do Azure no Linux](/azure/app-service/containers).

![Aplicativo Java em execução no serviço de aplicativo do Azure](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Crie um banco de dados do Cosmos DB.
> * Conectar um aplicativo de exemplo ao banco de dados e testá-lo localmente
> * Implantar o aplicativo de exemplo no Azure
> * Logs de diagnóstico do fluxo do Serviço de Aplicativo
> * Adicionar instâncias extras para expandir o aplicativo de exemplo

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

* [CLI do Azure](https://docs.microsoft.com/cli/azure/overview), instalada em seu próprio computador. 
* [Git](https://git-scm.com/)
* [Java JDK](https://aka.ms/azure-jdks)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Clonar o aplicativo TODO de exemplo e preparar o repositório

Este tutorial usa um aplicativo TODO de exemplo com uma interface do usuário Web que chama uma API REST do Spring apoiada pelo [Azure Cosmos DB do Spring Data](https://github.com/Microsoft/spring-data-cosmosdb). O código para o aplicativo está disponível [no GitHub](https://github.com/Microsoft/spring-todo-app). Para saber mais sobre como escrever aplicativos Java usando o Spring e o Cosmos DB, confira [Iniciador do Spring Boot com o tutorial da API do SQL do Azure Cosmos DB](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db ) e o [Início rápido do Azure Cosmos DB do Spring Data](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Execute os seguintes comandos no seu terminal para clonar o repositório de exemplo e configurar o ambiente de aplicativo de exemplo.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Criar um Azure Cosmos DB

Siga estas etapas para criar um banco de dados do Azure Cosmos DB em sua assinatura. O aplicativo de lista TODO vai se conectar a esse banco de dados e armazenar seus dados durante a execução, mantendo o estado do aplicativo, não importa em que local você executa o aplicativo.

1. Faça logon da sua CLI do Azure e, opcionalmente, configure sua assinatura se tiver mais de um usuário conectado às suas credenciais de logon.

    ```bash
    az login
    az account set -s <your-subscription-id>
    ```   

2. Crie um Grupo de Recursos do Azure observando o nome do grupo de recursos.

    ```bash
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Criar Azure Cosmos DB com o tipo `GlobalDocumentDB`. O nome do Cosmos DB deve usar apenas letras minúsculas. Anote o campo `documentEndpoint` na resposta do comando.

    ```bash
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Obtenha sua chave do Azure Cosmos DB para se conectar ao aplicativo. Mantenha o `primaryMasterKey` e o `documentEndpoint` próximos, pois você precisará deles na próxima etapa.

    ```bash
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>Configurar as propriedades do aplicativo TODO

Abra um terminal no seu computador. Copie o arquivo de script de exemplo no repositório clonado para que possa personalizá-lo para o banco de dados do Cosmos DB criado recentemente.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Edite `.scripts/set-env-variables.sh` em seu editor favorito e forneça informações de conexão do Azure Cosmos DB. Para a configuração do Serviço de Aplicativo do Linux, use a mesma região de antes (`your-resource-group-region`) e o grupo de recursos (`your-azure-group-name`) usado ao criar o banco de dados do Cosmos DB. Escolha um WEBAPP_NAME exclusivo, pois ele não pode duplicar nenhum nome do aplicativo Web em nenhuma implantação do Azure.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Em seguida, execute o script:

```bash
source .scripts/set-env-variables.sh
```
   
Essas variáveis de ambiente são usadas em `application.properties` no aplicativo de lista TODO. Os campos no arquivo de propriedades definem uma configuração de repositório padrão para o Spring Data:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

Em seguida, o aplicativo de exemplo usa a anotação `@Document` importada de `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` para configurar um tipo de entidade a ser armazenado e gerenciado pelo Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo

Use o Maven para executar o exemplo.

```bash
mvn package spring-boot:run
```

A saída deve parecer com o seguinte.

```bash
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

Você pode acessar o Aplicativo TODO do Spring localmente depois que o aplicativo for iniciado usando este link: [http://localhost:8080/](http://localhost:8080/).

 ![Aplicativo Java em execução no serviço de aplicativo do Azure](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Se você vir exceções em vez da mensagem "TodoApplication iniciado", verifique se o script `bash` na etapa anterior exportou as variáveis de ambiente corretamente e se os valores estão corretos para o banco de dados do Azure Cosmos DB que você criou.

## <a name="configure-azure-deployment"></a>Configurar a implantação do Azure

Abra o arquivo `pom.xml` no diretório `initial/spring-boot-todo` e adicione a seguinte configuração de [Plug-in do Maven para Serviço de Aplicativo do Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md).

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-webapp-maven-plugin</artifactId>
            <version>1.4.0</version>
            <configuration>
            <deploymentType>jar</deploymentType>
            
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
            
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>jre8</linuxRuntime>
            
            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property>
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>
            
        </configuration>
    </plugin>            
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Implantar no Serviço de Aplicativo no Linux

Use a meta `azure-webapp:deploy` do Maven para implantar o aplicativo TODO no Serviço de Aplicativo do Azure no Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.4.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Authenticate with Azure CLI 2.0
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2018-10-28T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

A saída contém a URL para seu aplicativo implantado (neste exemplo, `https://spring-todo-app.azurewebsites.net`). Você pode copiar essa URL no navegador da Web ou executar o seguinte comando na janela do Terminal para carregar seu aplicativo.

```bash
open https://spring-todo-app.azurewebsites.net
```

Você deve ver o aplicativo em execução com a URL remota na barra de endereços:

 ![Aplicativo Java em execução no serviço de aplicativo do Azure](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>Expandir o aplicativo TODO

Expanda o aplicativo adicionando outro trabalho:

```bash
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Limpar recursos

Se não precisar desses recursos para outro tutorial (consulte [Próximas etapas](#next)), você poderá excluí-los executando o seguinte comando no Cloud Shell: 
  
```bash
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Próximas etapas

[Azure para Desenvolvedores Java](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot), [Spring Data para Cosmos DB](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db?view=azure-java-stable), [Azure Cosmos DB](/azure/cosmos-db/sql-api-introduction) e [Serviço de Aplicativo no Linux](app-service-linux-intro.md).

Saiba mais sobre como executar aplicativos Java no Serviço de Aplicativo no Linux no guia do desenvolvedor.

> [!div class="nextstepaction"] 
> [Guia de desenvolvimento do Java no Serviço de Aplicativo no Linux](configure-language-java.md)
