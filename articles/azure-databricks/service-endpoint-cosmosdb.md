---
title: Implementar o Azure Databricks com um ponto de extremidade do Cosmos DB
description: Este tutorial descreve como implementar o Azure Databricks em uma rede virtual com um Ponto de Extremidade de Serviço habilitado para o Cosmos DB.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 04/17/2019
ms.openlocfilehash: d1268ea2cfc22e6350edb32230588a497be8bc79
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67054610"
---
# <a name="tutorial-implement-azure-databricks-with-a-cosmos-db-endpoint"></a>Tutorial: Implementar o Azure Databricks com um ponto de extremidade do Cosmos DB

Este tutorial descreve como implementar uma rede virtual ambiente injetadas do Databricks com um Ponto de Extremidade de Serviço habilitado para o Cosmos DB.

Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Criar um workspace do Azure Databricks em uma rede virtual
> * Criar um ponto de extremidade de serviço do Cosmos DB
> * Criar uma conta do Cosmos DB e importar dados
> * Criar um cluster do Azure Databricks
> * Consultar o Cosmos DB de um notebook do Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, faça o seguinte:

* Criar um [workspace do Azure Databricks em uma rede virtual](quickstart-create-databricks-workspace-vnet-injection.md).

* Baixe o [conector do Spark](https://search.maven.org/remotecontent?filepath=com/microsoft/azure/azure-cosmosdb-spark_2.4.0_2.11/1.3.4/azure-cosmosdb-spark_2.4.0_2.11-1.3.4-uber.jar).

* Baixar dados de exemplo dos [Centros Nacionais para Informações Ambientais da NOAA](https://www.ncdc.noaa.gov/stormevents/). Selecione um estado ou uma área e selecione **Pesquisar**. Na página seguinte, aceite os padrões e selecione **Pesquisar**. Em seguida, selecione **Baixar o CSV** no lado esquerdo da página para baixar os resultados.

* Baixe o [binário pré-compilado](https://aka.ms/csdmtool) da Ferramenta de Migração de Dados do Azure Cosmos DB.

## <a name="create-a-cosmos-db-service-endpoint"></a>Criar um ponto de extremidade de serviço do Cosmos DB

1. Quando você tiver implantado um workspace do Azure Databricks para uma rede virtual, navegue até a rede virtual no [portal do Azure](https://portal.azure.com). Observe as sub-redes públicas e privadas que foram criadas por meio da implantação do Databricks.

   ![Sub-redes da rede virtual](./media/service-endpoint-cosmosdb/virtual-network-subnets.png)

2. Selecione *public-subnet* e crie um ponto de extremidade de serviço do Cosmos DB. Em seguida, **Salve**.
   
   ![Adicionar um ponto de extremidade de serviço do Cosmos DB](./media/service-endpoint-cosmosdb/add-cosmosdb-service-endpoint.png)

## <a name="create-a-cosmos-db-account"></a>Criar uma conta do BD Cosmos

1. Abra o portal do Azure. No lado superior esquerdo da tela, selecione **Criar um recurso > Bancos de dados > Azure Cosmos DB**.

2. Preencha os **Detalhes da Instância** na guia **Noções Básicas** com as seguintes configurações:

   |Configuração|Valor|
   |-------|-----|
   |Assinatura|*sua assinatura*|
   |Grupo de recursos|*seu grupo de recursos*|
   |Nome da conta|db-vnet-service-endpoint|
   |API|Núcleo (SQL)|
   |Local padrão|Oeste dos EUA|
   |Redundância geográfica|Desabilitar|
   |Gravações de várias regiões|Habilitar|

   ![Adicionar um ponto de extremidade de serviço do Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-basics.png)

3. Selecione a guia **Rede** e configure sua rede virtual. 

   a. Escolha a rede virtual que você criou como pré-requisito e, em seguida, selecione *public-subnet*. Observe que *private-subnet* tem a observação *O ponto de extremidade 'Microsoft AzureCosmosDB' está ausente '* . Isso ocorre porque você habilitou apenas o ponto de extremidade de serviço do Cosmos DB na *public-subnet*.

   b. Verifique se você tem **Permitir acesso do portal do Azure** habilitado. Essa configuração permite que você acesse sua conta do Cosmos DB no portal do Azure. Se essa opção estiver definida como **Negar**, você receberá erros ao tentar acessar sua conta. 

   > [!NOTE]
   > Não é necessário para este tutorial, mas você também pode habilitar *Permitir acesso do meu IP* se quiser ter a capacidade de acessar sua conta do Cosmos DB em seu computador local. Por exemplo, se você estiver se conectando à sua conta usando o SDK do Cosmos DB, precisará habilitar essa configuração. Se estiver desabilitada, você receberá erros de "Acesso Negado".

   ![Configurações de rede da Conta do Cosmos DB](./media/service-endpoint-cosmosdb/create-cosmosdb-account-network.png)

4. Selecione **Examinar + Criar** e então **Criar** para criar sua conta do Cosmos DB dentro da rede virtual.

5. Quando sua conta do Cosmos DB tiver sido criada, navegue até **Chaves** em **Configurações**. Copie a cadeia de conexão primária e salve-a em um editor de texto para uso posterior.

    ![Página de chaves da conta do Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-keys.png)

6. Selecione **Data Explorer** e **nova coleção** para adicionar um novo banco de dados e uma coleção à conta do Cosmos DB.

    ![Nova coleção do Cosmos DB](./media/service-endpoint-cosmosdb/new-collection.png)

## <a name="upload-data-to-cosmos-db"></a>Carregar dados para o Cosmos DB

1. Abra a versão da interface gráfica da [ferramenta de migração de dados para o Cosmos DB](https://aka.ms/csdmtool), **Dtui.exe**.

    ![Ferramenta de Migração de Dados do Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-data-migration-tool.png)

2. Na guia **Informações de Origem**, selecione **Arquivo(s) CSV** na lista suspensa **Importar de**. Em seguida, selecione **Adicionar Arquivos** e adicione os dados CSV que você baixou do storm como um pré-requisito.

    ![Informações de origem da Ferramenta de Migração de Dados do Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-source-information.png)

3. Na guia **Informações sobre o Destino**, insira sua cadeia de conexão. O formato da cadeia de conexão é `AccountEndpoint=<URL>;AccountKey=<key>;Database=<database>`. AccountEndpoint e AccountKey estão incluídos na cadeia de conexão primária que você salvou na seção anterior. Acrescente `Database=<your database name>` ao final da cadeia de conexão e selecione **Verificar**. Em seguida, adicione a chave de partição e o nome da Coleção.

    ![Informações de destino da Ferramenta de Migração de Dados do Cosmos DB](./media/service-endpoint-cosmosdb/cosmos-target-information.png)

4. Selecione **Avançar** até chegar à página de Resumo. Em seguida, selecione **Importar**.

## <a name="create-a-cluster-and-add-library"></a>Criar um cluster e adicionar a biblioteca

1. Navegue até o serviço do Azure Databricks no [portal do Azure](https://portal.azure.com) e selecione **Inicializar workspace**.

   ![Iniciar o workspace do Azure Databricks](./media/service-endpoint-cosmosdb/launch-workspace.png)

2. Crie um novo cluster. Escolha um Nome de Cluster e aceite as configurações padrão restantes.

   ![Configurações de novo cluster](./media/service-endpoint-cosmosdb/create-cluster.png)

3. Depois que o cluster é criado, navegue até a página do cluster e selecione a guia **Bibliotecas**. Selecione **Instalar Novo** e carregue o arquivo de jar do conector do Spark para instalar a biblioteca.

    ![Instalar a biblioteca do conector Spark](./media/service-endpoint-cosmosdb/install-cosmos-connector-library.png)

    Você pode verificar se a biblioteca foi instalada na guia **Bibliotecas**.

    ![Guia Bibliotecas do cluster do Databricks](./media/service-endpoint-cosmosdb/installed-library.png)

## <a name="query-cosmos-db-from-a-databricks-notebook"></a>Consultar o Cosmos DB de um notebook do Databricks

1. Navegue até seu workspace do Azure Databricks e crie um novo notebook do Python.

    ![Criar um novo notebook do Databricks](./media/service-endpoint-cosmosdb/new-python-notebook.png)

2. Execute o seguinte código do Python para definir a configuração de conexão do Cosmos DB. Altere o **Ponto de Extremidade**, a **Chave Mestra**, o **Banco de Dados** e a **Coleção** adequadamente.

    ```python
    connectionConfig = {
      "Endpoint" : "https://<your Cosmos DB account name.documents.azure.com:443/",
      "Masterkey" : "<your Cosmos DB primary key>",
      "Database" : "<your database name>",
      "preferredRegions" : "West US 2",
      "Collection": "<your collection name>",
      "schema_samplesize" : "1000",
      "query_pagesize" : "200000",
      "query_custom" : "SELECT * FROM c"
    }
    ```

3. Use o seguinte código Python para carregar os dados e criar uma exibição temporária.

    ```python
    users = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**connectionConfig).load()
    users.createOrReplaceTempView("storm")
    ```

4. Use o seguinte comando mágico para executar uma instrução SQL que retorna dados.

    ```python
    %sql
    select * from storm
    ```

    Você conectou com sucesso seu workspace do Databricks injetado por VNet a um recurso do Cosmos DB habilitado para ponto de extremidade de serviço. Para ler mais sobre como conectar-se ao Cosmos DB, veja [Conector do Azure Cosmos DB para Apache Spark](https://github.com/Azure/azure-cosmosdb-spark).

## <a name="clean-up-resources"></a>Limpar recursos

Quando não forem mais necessário, exclua o grupo de recursos, o workspace do Azure Databricks e todos os recursos relacionados. Excluir o trabalho evita cobrança desnecessária. Se você está planejando usar o workspace do Azure Databricks no futuro, pode parar o cluster e reiniciá-lo mais tarde. Se você não pretende continuar a usar esse workspace do Azure Databricks, exclua todos os recursos criados neste tutorial usando as seguintes etapas:

1. No menu à esquerda no portal do Azure, clique em **Grupos de recursos** e depois clique no nome do grupo de recursos criado.

2. Em sua página de grupo de recursos, selecione **Excluir**, digite o nome do recurso a ser excluído na caixa de texto e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você implantou um workspace do Azure Databricks a uma rede virtual e usou o conector Spark do Cosmos DB para consultar dados do Cosmos DB do Databricks. Para saber mais sobre como trabalhar com o Azure Databricks em uma rede virtual, continue com o tutorial usando o SQL Server com o Azure Databricks.

> [!div class="nextstepaction"]
> [Tutorial: Consultar um contêiner do Docker do Linux do SQL Server em uma rede virtual de um notebook do Azure Databricks](vnet-injection-sql-server.md)
