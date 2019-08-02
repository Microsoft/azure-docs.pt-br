---
title: 'Início Rápido: Analisar dados no Azure Data Lake Storage Gen2 usando o Azure Databricks | Microsoft Docs'
description: Saiba como executar um trabalho do Spark no Azure Databricks usando o portal do Azure e uma conta de armazenamento do Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 02/15/2019
ms.openlocfilehash: d27f89b5a6f6da0732b98fe7b15793e80d491038
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357491"
---
# <a name="quickstart-analyze-data-in-azure-data-lake-storage-gen2-by-using-azure-databricks"></a>Início Rápido: Analisar dados no Azure Data Lake Storage Gen2 usando o Azure Databricks

Este início rápido mostra como executar um trabalho do Apache Spark usando o Azure Databricks para executar uma análise dos dados armazenados em uma conta de armazenamento com o Azure Data Lake Storage Gen2 habilitado.

Como parte do trabalho do Spark, você analisará dados de uma assinatura de canal de rádio para obter insights sobre o uso gratuito/pago com base em dados demográficos.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Crie uma conta de armazenamento do Data Lake Gen2. Confira [Início Rápido: Criar uma conta de armazenamento do Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)

  Cole o nome da conta de armazenamento em um arquivo de texto. Você precisará dele em breve.

* Crie uma entidade de serviço. Confira [Como: Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

  Há algumas tarefas específicas que você precisará realizar conforme executar as etapas deste artigo.

  :heavy_check_mark: Ao executar as etapas da seção [Atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) do artigo, atribua a função **Colaborador dos Dados do Storage Blob** à entidade de serviço.

  > [!IMPORTANT]
  > Atribua a função no escopo da conta de armazenamento do Data Lake Storage Gen2. Você pode atribuir uma função ao grupo de recursos pai ou à assinatura, mas receberá erros relacionados a permissões até que essas atribuições de função sejam propagadas para a conta de armazenamento.

  :heavy_check_mark: Ao executar as etapas da seção [Obter valores para conexão](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) do artigo, cole a ID do locatário, a ID do aplicativo e os valores de senha em um arquivo de texto. Você precisará deles em breve.

## <a name="create-an-azure-databricks-workspace"></a>Criar um workspace do Azure Databricks

Nesta seção, você deve cria um workspace do Azure Databricks usando o Portal do Azure.

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/data-lake-storage-quickstart-create-databricks-account/azure-databricks-on-portal.png "Databricks no portal do Azure")

2. Em **Serviço do Azure Databricks**, forneça os valores para criar um workspace do Databricks.

    ![Criar um workspace do Azure Databricks](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-workspace.png "Criar um workspace do Azure Databricks")

    Forneça os seguintes valores:

    |Propriedade  |DESCRIÇÃO  |
    |---------|---------|
    |**Nome do workspace**     | Forneça um nome para seu workspace do Databricks        |
    |**Assinatura**     | Na lista suspensa, selecione sua assinatura do Azure.        |
    |**Grupo de recursos**     | Especifique se deseja criar um novo grupo de recursos ou usar um existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, consulte [Visão geral do Grupo de Recursos do Azure](../../azure-resource-manager/resource-group-overview.md). |
    |**Localidade**     | Selecione **Oeste dos EUA 2**. Fique à vontade para selecionar outra região pública se preferir.        |
    |**Tipo de preço**     |  Escolha entre o cluster **Standard** e o **Premium**. Para saber mais sobre essas camadas, confira [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

3. A criação da conta leva alguns minutos. Para monitorar o status da operação, veja a barra de progresso na parte superior.

4. Selecione **Fixar no painel** e depois **Criar**.

## <a name="create-a-spark-cluster-in-databricks"></a>Criar um cluster Spark no Databricks

1. No portal do Azure, vá para o workspace do Databricks que você criou e selecione **Inicializar Workspace**.

2. Você é redirecionado para o portal do Azure Databricks. No portal, selecione **Novo** > **Cluster**.

    ![Databricks no Azure](./media/data-lake-storage-quickstart-create-databricks-account/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, forneça os valores para criar um cluster.

    ![Criar cluster do Databricks Spark no Azure](./media/data-lake-storage-quickstart-create-databricks-account/create-databricks-spark-cluster.png "Criar cluster do Databricks Spark no Azure")

    Aceite todos os outros valores padrão que não sejam o seguinte:

    * Insira um nome para o cluster.
    * Crie um cluster com o tempo de execução **5.1**.
    * Verifique se você marcou a caixa de seleção **Terminar depois de 120 minutos de inatividade**. Forneça uma duração (em minutos) para encerrar o cluster caso ele não esteja sendo usado.

4. Selecione **Criar cluster**. Quando o cluster está em execução, você pode anexar blocos de notas a ele e executar trabalhos do Spark.

Para obter mais informações sobre como criar clusters, consulte [Criar um cluster Spark no Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## <a name="create-storage-account-file-system"></a>Criar sistema de arquivos da conta de armazenamento

Nesta seção, você cria um bloco de anotações no workspace do Azure Databricks e executa snippets de código para configurar a conta de armazenamento.

1. No [Portal do Azure](https://portal.azure.com), vá para o workspace do Azure Databricks que você criou e selecione **Inicializar Workspace**.

2. No painel esquerdo, escolha **Workspace**. Na lista suspensa **Workspace**, selecione **Criar** > **Notebook**.

    ![Criar bloco de notas em Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-create-notebook.png "Criar bloco de notas em Databricks")

3. Na caixa de diálogo **Criar Bloco de Anotações**, digite um nome para o bloco de anotações. Selecione **Scala** como linguagem e selecione o cluster Spark criado anteriormente.

    ![Criar bloco de notas em Databricks](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-details.png "Criar bloco de notas em Databricks")

    Selecione **Criar**.

4. Copie e cole o bloco de código a seguir na primeira célula, mas não execute esse código ainda.

   ```scala
   spark.conf.set("fs.azure.account.auth.type.<storage-account-name>.dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type.<storage-account-name>.dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id.<storage-account-name>.dfs.core.windows.net", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret.<storage-account-name>.dfs.core.windows.net", "<password>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint.<storage-account-name>.dfs.core.windows.net", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")

   ```

    > [!NOTE]
    > Esse bloco de código acessa diretamente o ponto de extremidade do Data Lake Gen2 usando o OAuth, mas há outras maneiras de conectar o workspace do Databricks à sua conta do Data Lake Storage Gen2. Por exemplo, você pode montar o sistema de arquivos usando o OAuth ou usar um acesso direto com uma Chave Compartilhada. <br>Para ver exemplos dessas abordagens, confira o artigo [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) no site do Azure Databricks.

5. Neste bloco de código, substitua os valores de espaço reservado `storage-account-name`, `appID`, `password` e `tenant-id` pelos valores coletados ao criar a entidade de serviço. Defina o valor de espaço reservado `file-system-name` com qualquer nome que deseja fornecer ao sistema de arquivos.

    > [!NOTE]
    > Em uma configuração de produção, considere armazenar sua chave de autenticação no Azure Databricks. Em seguida, adicione uma chave de pesquisa ao bloco de código em vez da chave de autenticação. Depois de concluir este início rápido, consulte o artigo [Azure Data Lake Storage Gen2](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html) no site Azure Databricks para ver exemplos dessa abordagem.

6. Pressione as teclas **SHIFT+ENTER** para executar o código nesse bloco.

## <a name="ingest-sample-data"></a>Ingerir dados de exemplo

Antes de iniciar esta seção, você deve concluir os pré-requisitos a seguir:

Insira o código a seguir em uma célula do bloco de anotações:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Na célula, pressione **SHIFT + ENTER** para executar o código.

Agora, em uma nova célula abaixo dessa, insira o seguinte código e substitua os valores exibidos entre colchetes pelos mesmos valores usados anteriormente:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://<file-system>@<account-name>.dfs.core.windows.net/")

Na célula, pressione **SHIFT + ENTER** para executar o código.

## <a name="run-a-spark-sql-job"></a>Executar um trabalho SQL do Spark

Execute as seguintes tarefas para executar um trabalho SQL do Spark nos dados.

1. Execute uma instrução SQL para criar uma tabela temporária usando dados do arquivo de dados JSON de exemplo, **small_radio_json.json**. No snippet de código a seguir, substitua os valores de espaço reservado pelo nome do seu sistema de arquivos e o nome da conta de armazenamento. Usando o bloco de notas criando anteriormente, cole o snippet de código em uma célula de código no bloco de notas e pressione SHIFT + ENTER.

    ```sql
    %sql
    DROP TABLE IF EXISTS radio_sample_data;
    CREATE TABLE radio_sample_data
    USING json
    OPTIONS (
     path  "abfss://<file-system-name>@<storage-account-name>.dfs.core.windows.net/small_radio_json.json"
    )
    ```

    Assim que o comando for concluído com êxito, você terá todos os dados do arquivo JSON como uma tabela no cluster do Databricks.

    O comando mágico de linguagem `%sql` permite que você execute um código SQL do bloco de notas, mesmo que o bloco seja de outro tipo. Para obter mais informações, consulte [Combinar linguagens em um bloco de notas](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

2. Vamos examinar um instantâneo dos dados do JSON de exemplo para entender melhor a consulta que você executou. Cole o snippet de código a seguir em uma célula de código e pressione **SHIFT+ENTER**.

    ```sql
    %sql
    SELECT * from radio_sample_data
    ```

3. Você verá uma saída tabular como mostrado na seguinte captura de tela (somente algumas colunas são mostradas):

    ![Dados JSON de exemplo](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sample-csv-data.png "Dados JSON de exemplo")

    Entre outros detalhes, os dados de exemplo capturam o gênero do público de um canal de rádio (nome da coluna: **gênero**) e se a sua assinatura é gratuita ou paga (nome da coluna: **nível**).

4. Agora crie uma representação visual dos dados para mostrar quantos usuários têm contas gratuitas e quantos são assinantes pagantes para cada gênero. Na parte inferior da saída tabular, clique no ícone de **Gráfico de barras** e depois em **Opções de plotagem**.

    ![Criar gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/create-plots-databricks-notebook.png "Criar gráfico de barras")

5. Em **Personalizar plotagem**, arraste e solte valores conforme mostrado na captura de tela.

    ![Personalizar o gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/databricks-notebook-customize-plot.png "Personalizar o gráfico de barras")

    - Definir **Chaves** como **gênero**.
    - Definir **Agrupamentos de série** como **nível**.
    - Definir **Valores** como **nível**.
    - Definir **Agregação** como **CONTAGEM**.

6. Clique em **Aplicar**.

7. A saída mostra a representação visual, conforme ilustrado na seguinte captura de tela:

     ![Personalizar o gráfico de barras](./media/data-lake-storage-quickstart-create-databricks-account/databricks-sql-query-output-bar-chart.png "Personalizar o gráfico de barras")

## <a name="clean-up-resources"></a>Limpar recursos

Depois de terminar o artigo, você poderá encerrar o cluster. No workspace do Azure Databricks, selecione **Clusters** e localize o cluster que você deseja encerrar. Passe o cursor do mouse sobre o botão de reticências na coluna **Ações** e selecione o ícone **Terminar**.

![Parar um cluster do Databricks](./media/data-lake-storage-quickstart-create-databricks-account/terminate-databricks-cluster.png "Parar um cluster do Databricks")

Se você não encerrar o cluster manualmente, ele é interrompido automaticamente, desde que você tenha selecionado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Se você definir essa opção, o cluster será interrompido depois de ficar inativo durante o período de tempo designado.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou um cluster Spark no Azure Databricks e executou um trabalho do Spark usando os dados em uma conta de armazenamento com o Data Lake Storage Gen2 habilitado. Você também pode examinar [Fontes de dados do Spark](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) para saber como importar dados de outras fontes de dados para o Azure Databricks. Siga até o próximo artigo para saber como executar uma operação de ETL (extração, transformação e carregamento de dados) usando o Azure Databricks.

> [!div class="nextstepaction"]
>[Extrair, transformar e carregar dados usando o Azure Databricks](../../azure-databricks/databricks-extract-load-sql-data-warehouse.md)
