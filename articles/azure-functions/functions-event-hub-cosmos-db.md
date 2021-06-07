---
title: 'Tutorial: Usar funções do Java com o Azure Cosmos DB e Hubs de Eventos'
description: Este tutorial mostra como consumir eventos de Hubs de Eventos para fazer atualizações no Azure Cosmos DB usando uma função escrita em Java.
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0d5d285812aa61cf9fe684b268dc07718f6d1a1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708740"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>Tutorial: Criar uma função em Java com um gatilho do Hub de Eventos e uma associação de saída do Azure Cosmos DB

Este tutorial mostra como usar Azure Functions para criar uma função Java que analisa um fluxo contínuo de dados de temperatura e de pressão. Eventos de Hub de Eventos que representam leituras de sensor disparam a função. A função processa os dados do evento e adiciona entradas de status a um Azure Cosmos DB.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar e configurar os recursos do Azure usando a CLI do Azure.
> * Crie e teste funções Java que interagem com esses recursos.
> * Implante suas funções no Azure e monitore-as com o Application Insights.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará ter os seguintes itens instalados:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versão 8
- [Apache Maven](https://maven.apache.org), versão 3.0 ou posterior
- [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) versão 2.6.666 ou posterior[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]
> A variável de ambiente `JAVA_HOME` deve ser definida como a localização de instalação do JDK para concluir este tutorial.

Se você preferir usar o código para este tutorial diretamente, confira o repositório de exemplo [java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb).

## <a name="create-azure-resources"></a>Criar recursos do Azure

Neste tutorial, você precisará destes recursos:

* Um grupo de recursos para conter os outros recursos
* Um namespace de Hubs de Eventos, um hub de eventos e uma regra de autorização
* Uma conta, um banco de dados e uma coleção do Cosmos DB
* Um aplicativo de funções e uma conta de armazenamento para hospedá-lo

As seções a seguir mostram como criar esses recursos usando a CLI do Azure.

### <a name="set-environment-variables"></a>Definir variáveis de ambiente

Em seguida, crie algumas variáveis de ambiente para os nomes e a localização dos recursos que você criará. Use os comandos a seguir, substituindo os espaços reservados `<value>` por valores de sua escolha. Os valores devem estar em conformidade com as [regras de nomenclatura e restrições para recursos do Azure](/azure/architecture/best-practices/resource-naming). Para a variável `LOCATION`, use um dos valores produzidos pelo comando `az functionapp list-consumption-locations`.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set EVENT_HUB_NAMESPACE=<value>
set EVENT_HUB_NAME=<value>
set EVENT_HUB_AUTHORIZATION_RULE=<value>
set COSMOS_DB_ACCOUNT=<value>
set STORAGE_ACCOUNT=<value>
set FUNCTION_APP=<value>
set LOCATION=<value>
```

---

O restante deste tutorial usa essas variáveis. Lembre-se de que essas variáveis são mantidas somente pela duração da sua sessão do Cloud Shell ou CLI do Azure atual. Você precisará executar esses comandos novamente se usar uma janela de terminal local diferente ou se a sessão do Cloud Shell atingir o tempo limite.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O Azure usa grupos de recursos para coletar todos os recursos relacionados em sua conta. Desse modo, você pode exibi-los como uma unidade e excluí-los com um único comando quando tiver terminado de usá-los.

Use o comando a seguir para criar um grupo de recursos:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group create ^
    --name %RESOURCE_GROUP% ^
    --location %LOCATION%
```

---

### <a name="create-an-event-hub"></a>Criar um Hub de Evento

Em seguida, crie um namespace dos Hubs de Eventos do Azure, o hub de eventos e a regra de autorização usando os seguintes comandos:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventhubs namespace create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAMESPACE%
az eventhubs eventhub create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --message-retention 1
az eventhubs eventhub authorization-rule create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_AUTHORIZATION_RULE% ^
    --eventhub-name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --rights Listen Send
```

---

O namespace de Hubs de Eventos contém o hub de eventos real e sua regra de autorização. A regra de autorização permite que suas funções enviem mensagens para o hub e escutem os eventos correspondentes. Uma função envia mensagens que representam dados de telemetria. Outra função escuta eventos, analisa os dados do evento e armazena os resultados no Azure Cosmos DB.

### <a name="create-an-azure-cosmos-db"></a>Criar um Azure Cosmos DB

Em seguida, crie uma conta do Azure Cosmos DB, um banco de dados e uma coleção usando os seguintes comandos:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb sql database create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --name TelemetryDb
az cosmosdb sql container create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --database-name TelemetryDb \
    --name TelemetryInfo \
    --partition-key-path '/temperatureStatus'
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az cosmosdb create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %COSMOS_DB_ACCOUNT%
az cosmosdb sql database create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --name TelemetryDb
az cosmosdb sql container create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --database-name TelemetryDb ^
    --name TelemetryInfo ^
    --partition-key-path "/temperatureStatus"
```

---

O valor de `partition-key-path` particiona seus dados com base no valor de `temperatureStatus` de cada item. A chave de partição permite que o Cosmos DB aumente o desempenho dividindo os dados em subconjuntos distintos que ele pode acessar independentemente.

### <a name="create-a-storage-account-and-function-app"></a>Criar uma conta de armazenamento e um aplicativo de funções

Em seguida, crie uma conta de Armazenamento do Azure, a qual é exigida pelo Azure Functions, depois crie o aplicativo de funções. Use os seguintes comandos:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java \
    --functions-version 2
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az storage account create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %STORAGE_ACCOUNT% ^
    --sku Standard_LRS
az functionapp create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --storage-account %STORAGE_ACCOUNT% ^
    --consumption-plan-location %LOCATION% ^
    --runtime java ^
    --functions-version 2
```

---

Quando o comando `az functionapp create` cria seu aplicativo de funções, ele também cria um recurso do Application Insights com o mesmo nome. O aplicativo de funções é configurado automaticamente com uma configuração chamada `APPINSIGHTS_INSTRUMENTATIONKEY` que o conecta ao Application Insights. Você pode exibir a telemetria do aplicativo depois de implantar suas funções no Azure, conforme descrito posteriormente neste tutorial.

## <a name="configure-your-function-app"></a>Configurar seu aplicativo de funções

Seu aplicativo de funções precisará acessar os outros recursos para funcionar corretamente. As seções a seguir mostram como configurar seu aplicativo de funções para que ele possa ser executado no computador local.

### <a name="retrieve-resource-connection-strings"></a>Recuperar cadeias de conexão de recurso

Use os comandos a seguir para recuperar as cadeias de conexão de armazenamento, de hub de eventos e do Cosmos DB e salvá-las em variáveis de ambiente:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query 'connectionStrings[0].connectionString' \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
FOR /F "delims=" %X IN (' ^
    az storage account show-connection-string ^
        --name %STORAGE_ACCOUNT% ^
        --query connectionString ^
        --output tsv') DO SET AZURE_WEB_JOBS_STORAGE=%X
FOR /F "delims=" %X IN (' ^
    az eventhubs eventhub authorization-rule keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %EVENT_HUB_AUTHORIZATION_RULE% ^
        --eventhub-name %EVENT_HUB_NAME% ^
        --namespace-name %EVENT_HUB_NAMESPACE% ^
        --query primaryConnectionString ^
        --output tsv') DO SET EVENT_HUB_CONNECTION_STRING=%X
FOR /F "delims=" %X IN (' ^
    az cosmosdb keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %COSMOS_DB_ACCOUNT% ^
        --type connection-strings ^
        --query connectionStrings[0].connectionString ^
        --output tsv') DO SET COSMOS_DB_CONNECTION_STRING=%X
```

---

Essas variáveis são definidas como valores recuperados dos comandos da CLI do Azure. Cada comando usa uma consulta JMESPath para extrair a cadeia de conexão do conteúdo JSON retornado. As cadeias de conexão também são exibidas usando `echo` para que você possa confirmar que elas foram recuperadas com êxito.

### <a name="update-your-function-app-settings"></a>Atualizar as configurações do aplicativo de funções

Em seguida, use o seguinte comando para transferir os valores da cadeia de conexão para as configurações do aplicativo em sua conta de Azure Functions:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az functionapp config appsettings set ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --settings ^
        AzureWebJobsStorage=%AZURE_WEB_JOBS_STORAGE% ^
        EventHubConnectionString=%EVENT_HUB_CONNECTION_STRING% ^
        CosmosDBConnectionString=%COSMOS_DB_CONNECTION_STRING%
```

---

Os recursos do Azure agora foram criados e configurados para funcionar corretamente juntos.

## <a name="create-and-test-your-functions"></a>Criar e testar suas funções

Em seguida, você criará um projeto no computador local, adicionará código Java a ele e o testará. Você usará comandos que funcionam com o Plug-in do Azure Functions para Maven e o Azure Functions Core Tools. Suas funções serão executadas localmente, mas usarão os recursos baseados em nuvem que você criou. Depois que você conseguir que as funções funcionem localmente, poderá usar o Maven para implantá-las na nuvem e então ver seus dados e análises se acumularem.

Se tiver usado o Cloud Shell para criar seus recursos, você não ficará conectado ao Azure localmente. Nesse caso, use o comando `az login` para iniciar o processo de logon baseado em navegador. Em seguida, se necessário, defina a assinatura padrão com `az account set --subscription` seguido pela ID da assinatura. Por fim, execute os comandos a seguir para recriar algumas variáveis de ambiente em seu computador local. Substitua os espaços reservados `<value>` pelos mesmos valores que você usou anteriormente.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set FUNCTION_APP=<value>
```

---

### <a name="create-a-local-functions-project"></a>Criar um projeto local do Functions

Use o comando do Maven a seguir para criar um projeto do Functions e adicionar as dependências necessárias.

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DappRegion=$LOCATION \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate --batch-mode ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype ^
    -DappName=%FUNCTION_APP% ^
    -DresourceGroup=%RESOURCE_GROUP% ^
    -DappRegion=%LOCATION% ^
    -DgroupId=com.example ^
    -DartifactId=telemetry-functions
```

---

Esse comando gera vários arquivos dentro de uma pasta `telemetry-functions`:

* Um arquivo `pom.xml` para uso com o Maven
* Um arquivo `local.settings.json` para armazenar configurações de aplicativo para teste local
* Um arquivo `host.json` que habilita o pacote de extensões do Azure Functions, necessário para associação de saída do Cosmos DB em sua função de análise de dados
* Um arquivo `Function.java` que inclui uma implementação de função padrão
* Alguns arquivos de teste dos quais este tutorial não precisa

Para evitar erros de compilação, você precisará excluir os arquivos de teste. Execute os comandos a seguir para navegar até a nova pasta do projeto e exclua a pasta de teste:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
cd telemetry-functions
rm -r src/test
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd telemetry-functions
rmdir /s /q src\test
```

---

### <a name="retrieve-your-function-app-settings-for-local-use"></a>Recuperar as configurações do aplicativo de funções para uso local

Para testes locais, seu projeto de função precisará das cadeias de conexão que você adicionou ao aplicativo de funções no Azure, anteriormente neste tutorial. Use o comando do Azure Functions Core Tools a seguir, que recupera todas as configurações do aplicativo de funções armazenadas na nuvem e as adiciona ao arquivo `local.settings.json`:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
func azure functionapp fetch-app-settings %FUNCTION_APP%
```

---

### <a name="add-java-code"></a>Adicionar código Java

Em seguida, abra o arquivo `Function.java` e substitua o conteúdo pelo código a seguir.

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

Como podemos ver, esse arquivo contém duas funções, `generateSensorData` e `processSensorData`. A função `generateSensorData` simula um sensor que envia as leituras de temperatura e de pressão para o hub de eventos. Um gatilho de temporizador executa a função a cada dez segundos e uma associação de saída do hub de eventos envia o valor retornado para o hub de eventos.

Quando o hub de eventos recebe a mensagem, ele gera um evento. A função `processSensorData` é executada quando ela recebe o evento. Em seguida, ela processa os dados do evento e usa uma associação de saída do Azure Cosmos DB para enviar os resultados para o Azure Cosmos DB.

Os dados usados por essas funções são armazenados usando uma classe chamada `TelemetryItem`, que você precisará implementar. Crie um novo arquivo chamado `TelemetryItem.java` na mesma localização que `Function.java` e adicione o seguinte código:

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>Executar localmente

Agora você pode criar e executar as funções localmente e ver os dados aparecerem em seu Azure Cosmos DB.

Execute os comandos do Maven a seguir para compilar e executar as funções:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn clean package
mvn azure-functions:run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn clean package
mvn azure-functions:run
```

---

Após algumas mensagens de build e de inicialização, você verá uma saída semelhante ao exemplo a seguir para cada vez que as funções forem executadas:

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

Em seguida, acesse o [portal do Azure](https://portal.azure.com) e navegue até sua conta do Azure Cosmos DB. Selecione **Data Explorer**, expanda **TelemetryInfo** e, em seguida, selecione **Itens** para exibir seus dados quando eles chegarem.

![Data Explorer do Cosmos DB](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Implantar no Azure e exibir a telemetria de aplicativo

Por fim, você pode implantar seu aplicativo no Azure e verificar se ele continua funcionando da mesma maneira que fazia localmente.

Implante seu projeto no Azure usando o seguinte comando:

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn azure-functions:deploy
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn azure-functions:deploy
```

---

Suas funções agora são executadas no Azure e continuam a acumular dados em seu Azure Cosmos DB. Você pode exibir o aplicativo de funções implantado no portal do Azure e exibir a telemetria do aplicativo por meio do recurso Application Insights conectado, conforme mostrado nas seguintes capturas de tela:

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**Desempenho:**

![Folha Desempenho do Application Insights](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando tiver terminado de usar os recursos do Azure criados neste tutorial, será possível excluí-los usando o seguinte comando:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group delete --name %RESOURCE_GROUP%
```

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar uma função do Azure que manipula eventos do Hub de Eventos e atualiza um Cosmos DB. Para obter mais informações, confira o [Guia do desenvolvedor de Java do Azure Functions](./functions-reference-java.md). Para obter informações sobre as anotações usadas, confira a referência [com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation).

Este tutorial usou variáveis de ambiente e configurações de aplicativo para armazenar segredos, tais como cadeias de conexão. Para obter informações sobre como armazenar esses segredos no Azure Key Vault, confira [Usar referências do Key Vault para o Serviço de Aplicativo e o Azure Functions](../app-service/app-service-key-vault-references.md).

Em seguida, aprenda como usar CI/CD do Azure Pipelines para implantação automatizada:

> [!div class="nextstepaction"]
> [Criar e implantar Java no Azure Functions](/azure/devops/pipelines/ecosystems/java-function)
