---
title: 'Tutorial: Implementar o padrão de captura do data lake para atualizar uma tabela do Azure Databricks Delta | Microsoft Docs'
description: Este tutorial mostra como usar uma assinatura de Grade de Eventos, uma função do Azure e um trabalho de Azure Databricks para inserir linhas de dados em uma tabela que é armazenada no Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: normesta
ms.reviewer: sumameh
ms.custom: devx-track-csharp
ms.openlocfilehash: f5fa4ad357e937fed7df5be24a1fc78409a0259b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100516389"
---
# <a name="tutorial-implement-the-data-lake-capture-pattern-to-update-a-databricks-delta-table"></a>Tutorial: Implementar o padrão de captura do data lake para atualizar uma tabela do Databricks Delta

Este tutorial mostra como manipular eventos em uma conta de armazenamento que tem um namespace hierárquico.

Você criará uma pequena solução que permite que um usuário preencha uma tabela do Databricks Delta carregando um arquivo CSV (de valores separados por vírgula) que descreve uma ordem de venda. Você criará essa solução conectando uma assinatura de Grade de Eventos, uma função do Azure e um [trabalho](/azure/databricks/jobs) no Azure Databricks.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar uma assinatura da Grade de Eventos que chama uma função do Azure.
> * Criar uma função do Azure que recebe uma notificação de um evento e, em seguida, executa o trabalho no Azure Databricks.
> * Criar um trabalho do Databricks que insere uma ordem de cliente em uma tabela do Databricks Delta que está localizada na conta de armazenamento.

Criaremos essa solução em ordem inversa, começando com o workspace do Azure Databricks.

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

* Crie uma conta de armazenamento que tem um namespace hierárquico (Azure Data Lake Storage Gen2). Este tutorial usa uma conta de armazenamento nomeada `contosoorders`. Verifique se a sua conta de usuário tem a [função Colaborador de Dados do Storage Blob](../common/storage-auth-aad-rbac-portal.md) atribuída a ela.

   Confira [Criar uma conta de armazenamento para usar com o Azure Data Lake Storage Gen2](create-data-lake-storage-account.md).

* Crie uma entidade de serviço. Veja [Como Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](../../active-directory/develop/howto-create-service-principal-portal.md).

  Há algumas tarefas específicas que você precisará realizar conforme executar as etapas deste artigo.

  :heavy_check_mark: Ao executar as etapas da seção [Atribuir o aplicativo a uma função](../../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) do artigo, atribua a função **Colaborador dos Dados do Storage Blob** à entidade de serviço.

  > [!IMPORTANT]
  > Atribua a função no escopo da conta de armazenamento do Data Lake Storage Gen2. Você pode atribuir uma função ao grupo de recursos pai ou à assinatura, mas receberá erros relacionados a permissões até que essas atribuições de função sejam propagadas para a conta de armazenamento.

  :heavy_check_mark: Ao executar as etapas da seção [Obter valores para conexão](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) do artigo, cole a ID do locatário, a ID do aplicativo e os valores de senha em um arquivo de texto. Você precisará desses valores mais tarde.

## <a name="create-a-sales-order"></a>Criar uma ordem de venda

Primeiro, crie um arquivo CSV que descreva uma ordem de venda e, em seguida, carregue esse arquivo na conta de armazenamento. Posteriormente, você usará os dados desse arquivo para preencher a primeira linha em nossa tabela do Databricks Delta.

1. Abra o Gerenciador de Armazenamento do Azure. Em seguida, navegue até a conta de armazenamento e, na seção **Contêineres de Blob**, crie um novo contêiner denominado **dados**.

   ![pasta de dados](./media/data-lake-storage-events/data-container.png "pasta de dados")

   Para obter mais informações sobre como usar Gerenciador de Armazenamento, confira [Usar o Gerenciador de Armazenamento do Azure para gerenciar dados em uma conta do Azure Data Lake Storage Gen2](data-lake-storage-explorer.md).

2. No contêiner **data**, crie uma pasta chamada **input**.

3. Em um editor de texto, cole o texto a seguir.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536365,85123A,WHITE HANGING HEART T-LIGHT HOLDER,6,12/1/2010 8:26,2.55,17850,United Kingdom
   ```

4. Salve esse arquivo em seu computador local e dê a ele o **nome data.csv**.

5. Em Gerenciador de Armazenamento, carregue esse arquivo na pasta **input**.  

## <a name="create-a-job-in-azure-databricks"></a>Criar um trabalho no Azure Databricks

Nesta seção, você executará estas tarefas:

* Criar um workspace do Azure Databricks.
* Crie um notebook.
* Criar e popular uma tabela do Databricks Delta.
* Adicionar código que insere linhas na tabela do Databricks Delta.
* Criar um trabalho.

### <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

Nesta seção, você deve cria um workspace do Azure Databricks usando o Portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Serviço do Azure Databricks**, forneça os valores para criar um workspace do Databricks.

    ![Crie um workspace do Azure Databricks](./media/data-lake-storage-events/new-databricks-service.png "Criar um workspace do Azure Databricks")

    A criação do workspace leva alguns minutos. Para monitorar o status da operação, veja a barra de progresso na parte superior.

### <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster Spark no Databricks

1. No [portal do Azure](https://portal.azure.com), vá para o workspace do Azure Databricks que você criou e selecione **Inicializar Workspace**.

2. Você é redirecionado para o portal do Azure Databricks. No portal, selecione **Novo** > **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-events/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, forneça os valores para criar um cluster.

    ![Criar um cluster Spark do Databricks no Azure](./media/data-lake-storage-events/create-databricks-spark-cluster.png "Criar um cluster Spark do Databricks no Azure")

    Aceite todos os outros valores padrão que não sejam o seguinte:

    * Insira um nome para o cluster.
    * Verifique se você marcou a caixa de seleção **Terminar depois de 120 minutos de inatividade**. Forneça uma duração (em minutos) para encerrar o cluster caso ele não esteja sendo usado.

4. Selecione **Criar cluster**. Quando o cluster está em execução, você pode anexar blocos de notas a ele e executar trabalhos do Spark.

Para obter mais informações sobre como criar clusters, consulte [Criar um cluster Spark no Azure Databricks](/azure/databricks/clusters/create).

### <a name="create-a-notebook"></a>Criar um notebook

1. No painel esquerdo, escolha **Workspace**. Na lista suspensa **Workspace**, selecione **Criar** > **Notebook**.

    ![Criar notebook no Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Criar notebook no Databricks")

2. Na caixa de diálogo **Criar Bloco de Anotações**, digite um nome para o bloco de anotações. Selecione **Python** como a linguagem e, em seguida, selecione o cluster Spark criado anteriormente.

    ![Captura de tela que mostra a caixa de diálogo Criar Notebook e onde selecionar o Python como a linguagem.](./media/data-lake-storage-events/new-databricks-notebook.png "Criar notebook no Databricks")

    Selecione **Criar**.

### <a name="create-and-populate-a-databricks-delta-table"></a>Criar e popular uma tabela do Databricks Delta

1. No notebook criado, copie e cole o bloco de código a seguir na primeira célula, mas não execute esse código ainda.  

   Substitua os valores de espaço reservado `appId`, `password` e `tenant` neste código pelos valores coletados ao concluir os pré-requisitos deste tutorial.

    ```Python
    dbutils.widgets.text('source_file', "", "Source File")

    spark.conf.set("fs.azure.account.auth.type", "OAuth")
    spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
    spark.conf.set("fs.azure.account.oauth2.client.id", "<appId>")
    spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")
    spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant>/oauth2/token")

    adlsPath = 'abfss://data@contosoorders.dfs.core.windows.net/'
    inputPath = adlsPath + dbutils.widgets.get('source_file')
    customerTablePath = adlsPath + 'delta-tables/customers'
    ```

    Esse código cria um widget chamado **source_file**. Posteriormente, você criará uma função do Azure que chama esse código e passa um caminho de arquivo para esse widget.  Esse código também autentica a entidade de serviço com a conta de armazenamento e cria algumas variáveis que você usará em outras células.

    > [!NOTE]
    > Em uma configuração de produção, considere armazenar sua chave de autenticação no Azure Databricks. Em seguida, adicione uma chave de pesquisa ao bloco de código em vez da chave de autenticação. <br><br>Por exemplo, em vez de usar esta linha de código `spark.conf.set("fs.azure.account.oauth2.client.secret", "<password>")`:, você usaria a seguinte linha de código: `spark.conf.set("fs.azure.account.oauth2.client.secret", dbutils.secrets.get(scope = "<scope-name>", key = "<key-name-for-service-credential>"))`. <br><br>Depois de concluir este tutorial, confira o artigo [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) no site Azure Databricks para ver exemplos dessa abordagem.

2. Pressione as teclas **SHIFT+ENTER** para executar o código nesse bloco.

3. Copie e cole o bloco de código a seguir em uma célula diferente e pressione as teclas **SHIFT + ENTER** para executar o código nesse bloco.

   ```Python
   from pyspark.sql.types import StructType, StructField, DoubleType, IntegerType, StringType


   inputSchema = StructType([
   StructField("InvoiceNo", IntegerType(), True),
   StructField("StockCode", StringType(), True),
   StructField("Description", StringType(), True),
   StructField("Quantity", IntegerType(), True),
   StructField("InvoiceDate", StringType(), True),
   StructField("UnitPrice", DoubleType(), True),
   StructField("CustomerID", IntegerType(), True),
   StructField("Country", StringType(), True)
   ])

   rawDataDF = (spark.read
    .option("header", "true")
    .schema(inputSchema)
    .csv(adlsPath + 'input')
   )

   (rawDataDF.write
     .mode("overwrite")
     .format("delta")
     .saveAsTable("customer_data", path=customerTablePath))
   ```

   Esse código cria a tabela do Databricks Delta na conta de armazenamento e, em seguida, carrega alguns dados iniciais do arquivo CSV que você carregou anteriormente.

4. Depois que esse bloco de código for executado com êxito, remova-o do notebook.

### <a name="add-code-that-inserts-rows-into-the-databricks-delta-table"></a>Adicionar código que insere linhas na tabela do Databricks Delta

1. Copie e cole o bloco de código a seguir em uma célula diferente, mas não execute essa célula.

   ```Python
   upsertDataDF = (spark
     .read
     .option("header", "true")
     .csv(inputPath)
   )
   upsertDataDF.createOrReplaceTempView("customer_data_to_upsert")
   ```

   Esse código insere dados em uma exibição de tabela temporária usando dados de um arquivo CSV. O caminho para esse arquivo CSV é proveniente do widget de entrada que você criou em uma etapa anterior.

2. Adicione o código a seguir para mesclar o conteúdo da exibição de tabela temporária com a tabela do Databricks Delta.

   ```
   %sql
   MERGE INTO customer_data cd
   USING customer_data_to_upsert cu
   ON cd.CustomerID = cu.CustomerID
   WHEN MATCHED THEN
     UPDATE SET
       cd.StockCode = cu.StockCode,
       cd.Description = cu.Description,
       cd.InvoiceNo = cu.InvoiceNo,
       cd.Quantity = cu.Quantity,
       cd.InvoiceDate = cu.InvoiceDate,
       cd.UnitPrice = cu.UnitPrice,
       cd.Country = cu.Country
   WHEN NOT MATCHED
     THEN INSERT (InvoiceNo, StockCode, Description, Quantity, InvoiceDate, UnitPrice, CustomerID, Country)
     VALUES (
       cu.InvoiceNo,
       cu.StockCode,
       cu.Description,
       cu.Quantity,
       cu.InvoiceDate,
       cu.UnitPrice,
       cu.CustomerID,
       cu.Country)
   ```

### <a name="create-a-job"></a>Crie um trabalho

Crie um trabalho que execute o notebook que você criou anteriormente. Posteriormente, você criará uma função do Azure que executará esse trabalho quando um evento for gerado.

1. Clique em **Trabalhos**.

2. Na página **Trabalhos**, clique em **Criar Trabalho**.

3. Dê um nome ao trabalho e escolha a pasta de trabalho `upsert-order-data`.

   ![Criar um trabalho](./media/data-lake-storage-events/create-spark-job.png "Criar um trabalho")

## <a name="create-an-azure-function"></a>Criar uma Função do Azure

Crie uma função do Azure que executa o trabalho.

1. No canto superior do workspace do Databricks, escolha o ícone de pessoas e, em seguida, escolha **Configurações do usuário**.

   ![Gerenciar conta](./media/data-lake-storage-events/generate-token.png "Configurações do usuário")

2. Clique no botão **Gerar novo token** e, em seguida, clique no botão **Gerar**.

   Verifique se você copiou o token para um local seguro. Para que a função do Azure possa executar o trabalho, ela precisa desse token para autenticar com o Databricks.
  
3. Selecione o botão **Criar um recurso** no canto superior esquerdo do portal do Azure e selecione **Computação > Aplicativo de Funções**.

   ![Criar uma função do Azure](./media/data-lake-storage-events/function-app-create-flow.png "Criar função do Azure")

4. Na página **Criar** do aplicativo de funções, é preciso que você selecione o **.NET Core** para a pilha de runtime e que configure uma instância de Application Insights.

   ![Configurar o aplicativo de funções](./media/data-lake-storage-events/new-function-app.png "Configurar o aplicativo de funções")

5. Na página **Visão geral** do aplicativo de funções, clique em **Configuração**.

   ![Captura de tela que realça a opção Configuração em Recursos configurados.](./media/data-lake-storage-events/configure-function-app.png "Configurar o aplicativo de funções")

6. Na página **Configurações do Aplicativo**, escolha o botão **Nova configuração de aplicativo** para adicionar cada configuração.

   ![Adicionar uma definição de configuração](./media/data-lake-storage-events/add-application-setting.png "Adicionar uma definição de configuração")

   Adicione as seguintes configurações:

   |Nome da configuração | Valor |
   |----|----|
   |**DBX_INSTANCE**| A região do workspace do Databricks. Por exemplo: `westus2.azuredatabricks.net`|
   |**DBX_PAT**| O token de acesso pessoal que você gerou anteriormente. |
   |**DBX_JOB_ID**|O identificador do trabalho em execução. Em nosso caso, esse valor é `1`.|
7. Na página de visão geral do aplicativo de funções, clique no botão **Nova função**.

   ![Nova função](./media/data-lake-storage-events/new-function.png "Nova função")

8. Escolha **Gatilho de Grade de Eventos do Azure**.

   Instale a extensão **Microsoft.Azure.WebJobs.Extensions.EventGrid** se for solicitado que você faça isso. Se precisar instalá-la, você precisará escolher o **Gatilho de Grade de Eventos do Azure** novamente para criar a função.

   O painel **Nova Função** é exibido.

9. No painel **Nova Função**, nomeie a função **UpsertOrder** e, em seguida, clique no botão **Criar**.

10. Substitua o conteúdo do arquivo de código por este código e, em seguida, clique no botão **Salvar**:

    ```cs
    using "Microsoft.Azure.EventGrid"
    using "Newtonsoft.Json"
    using Microsoft.Azure.EventGrid.Models;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;

    private static HttpClient httpClient = new HttpClient();

    public static async Task Run(EventGridEvent eventGridEvent, ILogger log)
    {
        log.LogInformation("Event Subject: " + eventGridEvent.Subject);
        log.LogInformation("Event Topic: " + eventGridEvent.Topic);
        log.LogInformation("Event Type: " + eventGridEvent.EventType);
        log.LogInformation(eventGridEvent.Data.ToString());

        if (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated" | | eventGridEvent.EventType == "Microsoft.Storage.FileRenamed") {
            var fileData = ((JObject)(eventGridEvent.Data)).ToObject<StorageBlobCreatedEventData>();
            if (fileData.Api == "FlushWithClose") {
                log.LogInformation("Triggering Databricks Job for file: " + fileData.Url);
                var fileUrl = new Uri(fileData.Url);
                var httpRequestMessage = new HttpRequestMessage {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri(String.Format("https://{0}/api/2.0/jobs/run-now", System.Environment.GetEnvironmentVariable("DBX_INSTANCE", EnvironmentVariableTarget.Process))),
                    Headers = {
                        { System.Net.HttpRequestHeader.Authorization.ToString(), "Bearer " +  System.Environment.GetEnvironmentVariable ("DBX_PAT", EnvironmentVariableTarget.Process)},
                        { System.Net.HttpRequestHeader.ContentType.ToString (), "application/json" }
                    },
                    Content = new StringContent(JsonConvert.SerializeObject(new {
                        job_id = System.Environment.GetEnvironmentVariable ("DBX_JOB_ID", EnvironmentVariableTarget.Process) ,
                        notebook_params = new {
                            source_file = String.Join("", fileUrl.Segments.Skip(2))
                        }
                    }))
                 };
                var response = await httpClient.SendAsync(httpRequestMessage);
                response.EnsureSuccessStatusCode();
            }
        }
    }
    ```

   Esse código analisa informações sobre o evento de armazenamento que foi gerado e, em seguida, cria uma mensagem de solicitação com a URL do arquivo que disparou o evento. Como parte da mensagem, a função passa um valor para o widget **source_file** que você criou anteriormente. O código de função envia a mensagem para o trabalho do Databricks e usa o token que você obteve anteriormente como autenticação.

## <a name="create-an-event-grid-subscription"></a>Criar uma assinatura na Grade de Eventos

Nesta seção, você criará uma assinatura da Grade de Eventos que chama a função do Azure quando os arquivos são carregados na conta de armazenamento.

1. Na página de código da função, clique no botão **Adicionar assinatura da Grade de Eventos**.

   ![Captura de tela que realça o botão Adicionar assinatura da Grade de Eventos.](./media/data-lake-storage-events/new-event-subscription.png "Nova assinatura de evento")

2. Na página **Criar Assinatura de Evento**, nomeie a assinatura e, em seguida, use os campos na página para selecionar a conta de armazenamento.

   ![Nova assinatura de evento](./media/data-lake-storage-events/new-event-subscription-2.png "Nova assinatura de evento")

3. Na lista suspensa **Filtrar para Tipos de Eventos**, selecione os eventos **Blob Criado** e **Blob Excluído** e clique no botão **Criar**.

## <a name="test-the-event-grid-subscription"></a>Testar a assinatura de Grade de Eventos

1. Crie um arquivo chamado `customer-order.csv`, cole as informações a seguir nesse arquivo e salve-o no computador local.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,228,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

2. Em Gerenciador de Armazenamento, carregue esse arquivo na pasta **input** da conta de armazenamento.

   O upload de um arquivo gera o evento **Microsoft.Storage.BlobCreated**. A Grade de Eventos notifica todos os assinantes desse evento. Em nosso caso, a função do Azure é a única assinante. A função do Azure analisa os parâmetros do evento para determinar qual evento ocorreu. Em seguida, ela passa a URL do arquivo para o trabalho do Databricks. O trabalho do databricks lê o arquivo e adiciona uma linha à tabela do Databricks Delta que está localizada na conta de armazenamento.

3. Para verificar se o trabalho foi bem-sucedido, abra o workspace do Databricks, clique no botão **Trabalhos** e abra o trabalho.

4. Selecione o trabalho para abrir a respectiva página.

   ![Trabalho do Spark](./media/data-lake-storage-events/spark-job.png "Trabalho do Spark")

   Quando o trabalho for concluído, você verá um status de conclusão.

   ![Trabalho concluído com êxito](./media/data-lake-storage-events/spark-job-completed.png "Trabalho concluído com êxito")

5. Em uma nova célula da pasta de trabalho, execute esta consulta em uma célula para ver a tabela delta atualizada.

   ```
   %sql select * from customer_data
   ```

   A tabela retornada mostra o registro mais recente.

   ![O registro mais recente é exibido na tabela](./media/data-lake-storage-events/final_query.png "O registro mais recente é exibido na tabela")

6. Para atualizar esse registro, crie um arquivo chamado `customer-order-update.csv`, cole as informações a seguir nesse arquivo e salve-o no computador local.

   ```
   InvoiceNo,StockCode,Description,Quantity,InvoiceDate,UnitPrice,CustomerID,Country
   536371,99999,EverGlow Single,22,1/1/2018 9:01,33.85,20993,Sierra Leone
   ```

   Esse arquivo csv é quase idêntico ao anterior, exceto que a quantidade do pedido é alterada de `228` para `22`.

7. Em Gerenciador de Armazenamento, carregue esse arquivo na pasta **input** da conta de armazenamento.

8. Execute a consulta `select` novamente para ver a tabela delta atualizada.

   ```
   %sql select * from customer_data
   ```

   A tabela retornada mostra o registro atualizado.

   ![O registro atualizado é exibido na tabela](./media/data-lake-storage-events/final_query-2.png "O registro atualizado é exibido na tabela")

## <a name="clean-up-resources"></a>Limpar os recursos

Quando não forem mais necessários, exclua o grupo de recursos e todos os recursos relacionados. Para fazer isso, selecione o grupo de recursos da conta de armazenamento e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Reagindo a eventos de Armazenamento de Blobs](storage-blob-event-overview.md)