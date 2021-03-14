---
title: 'Tutorial: Extrair dados de formulário em massa usando o Azure Data Factory – Reconhecimento de Formulários'
titleSuffix: Azure Cognitive Services
description: Configure atividades do Azure Data Factory para acionar o treinamento e a execução de modelos do Reconhecimento de Formulários para digitalizar uma grande lista de pendências de documentos.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: pafarley
ms.openlocfilehash: 6faa612f55b4114b4242c48d43aae9aac8c56582
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699990"
---
# <a name="tutorial-extract-form-data-in-bulk-using-azure-data-factory"></a>Tutorial: Extrair dados de formulário em massa usando o Azure Data Factory

Neste tutorial, veremos como usar os serviços do Azure para ingerir um grande lote de formulários em mídia digital. Este tutorial mostrará como automatizar a ingestão de dados de um Azure Data Lake de documentos em um banco de dados SQL do Azure. Você poderá treinar modelos e processar novos documentos rapidamente com apenas alguns cliques.

## <a name="business-need"></a>Necessidade comercial

A maioria das organizações está ciente do quanto os dados que elas têm em diferentes formatos (PDF, imagens, vídeos) são valiosos. Elas estão buscando práticas recomendadas e as maneiras mais econômicas de digitalizar esses ativos.

Além disso, frequentemente nossos clientes têm diferentes tipos de formulários provenientes de seus diversos clientes. Diferente dos [guias de início rápido](./quickstarts/client-library.md), este tutorial mostra como treinar automaticamente um modelo com tipos novos e diferentes de formulários usando uma abordagem controlada por metadados. Se você não tiver um modelo existente para o tipo de formulário fornecido, o sistema criará um para você e fornecerá a ID do modelo. 

Ao extrair os dados de formulários e combiná-los com sistemas operacionais e data warehouses existentes, as empresas podem obter insights e fornecer valor para seus clientes e usuários corporativos.

Com o Reconhecimento de Formulários do Azure, ajudamos as organizações a aproveitar seus dados, automatizar processos (pagamentos de fatura, processamento de impostos etc.), economizar tempo e dinheiro e tirar proveito da maior precisão de dados.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o Azure Data Lake para armazenar seus formulários
> * Usar um banco de dados do Azure para criar uma tabela de parametrização
> * Usar o Azure Key Vault para armazenar credenciais confidenciais
> * Treinar o modelo do Reconhecimento de Formulários em um notebook do Databricks
> * Extrair dados do formulário usando um notebook do Databricks
> * Automatizar o treinamento e a extração de formulários com o Azure Data Factory

## <a name="prerequisites"></a>Pré-requisitos

* Assinatura do Azure – [Criar uma gratuitamente](https://azure.microsoft.com/free/cognitive-services/)
* Depois de ter sua assinatura do Azure, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="Criar um recurso do Reconhecimento de Formulários"  target="_blank">crie um Reconhecimento de Formulários <span class="docon docon-navigate-external x-hidden-focus"></span></a> no portal do Azure para obter a chave e o ponto de extremidade. Após a implantação, selecione **Ir para recurso**.
    * Você precisará da chave e do ponto de extremidade do recurso criado para conectar seu aplicativo à API do Reconhecimento de Formulários. Cole a chave e o ponto de extremidade no código abaixo mais adiante no guia de início rápido.
    * Use o tipo de preço gratuito (`F0`) para experimentar o serviço e atualizar mais tarde para um nível pago para produção.
* Um conjunto com pelo menos cinco formulários do mesmo tipo. Idealmente, esse fluxo de trabalho deve dar suporte a grandes conjuntos de documentos. Confira [Criar um conjunto de dados de treinamento](./build-training-data-set.md) para obter dicas e opções para compilar o conjunto de dados de treinamento. Para este tutorial, você pode usar os arquivos na pasta **Train** do [conjunto de dados de exemplo](https://go.microsoft.com/fwlink/?linkid=2128080).

## <a name="project-architecture"></a>Arquitetura do projeto 

Este projeto representa um conjunto de pipelines do Azure Data Factory usado para disparar notebooks Python que treinam, analisam e extraem dados de documentos em uma conta de armazenamento do Azure Data Lake.

O API REST do Reconhecimento de Formulários requer alguns parâmetros como entrada. Por motivos de segurança, alguns desses parâmetros serão armazenados em um Azure Key Vault, enquanto outros parâmetros menos confidenciais, como o nome da pasta do blob de armazenamento, serão armazenados em uma tabela de parametrização em um banco de dados SQL do Azure.

Para o tipo de formulário a ser analisado, os engenheiros ou cientistas de dados preencherão uma linha da tabela de parâmetros. Em seguida, eles usarão o Azure Data Factory para iterar sobre a lista de tipos de formulários detectados e passar os parâmetros relevantes para um notebook do Databricks para treinar ou treinar novamente os modelos do Reconhecimento de Formulários. Uma função do Azure também pode ser usada aqui.

Em seguida, o notebook do Azure Databricks usa os modelos treinados para extrair dados do formulário e exporta esses dados para um banco de dados SQL do Azure.

:::image type="content" source="./media/tutorial-bulk-processing/architecture.png" alt-text="arquitetura do projeto":::


## <a name="set-up-azure-data-lake"></a>Configurar o Azure Data Lake

Sua lista de pendências de formulários pode estar em seu ambiente local ou em um servidor (s)FTP. Este tutorial usa formulários em uma conta de armazenamento do Azure Data Lake Gen 2. Você pode transferir os arquivos usando o Azure Data Factory, o Gerenciador de Armazenamento do Azure ou AzCopy. Os conjuntos de dados de treinamento e pontuação podem estar em contêineres diferentes, mas os conjuntos de dados de treinamento para todos os tipos de formulário devem estar no mesmo contêiner (embora possam estar em pastas diferentes).

Para criar um Data Lake, siga as instruções em [Criar uma conta de armazenamento para usá-la com o Azure Data Lake Storage Gen2](../../storage/blobs/create-data-lake-storage-account.md).

## <a name="create-a-parameterization-table"></a>Criar uma tabela de parametrização

Em seguida, criaremos uma tabela de metadados em um Banco de Dados SQL do Azure. Esta tabela conterá os dados não confidenciais exigidos pela API REST do Reconhecimento de Formulários. Sempre que houver um novo tipo de formulário em nosso conjunto de dados, vamos inserir um novo registro nessa tabela e acionar o pipeline de treinamento e pontuação (a ser implementado posteriormente).

Os seguintes campos serão usados na tabela:

* **form_description**: esse campo não é necessário como parte do treinamento. Ele fornece uma descrição do tipo de formulários para os quais estamos treinando o modelo (por exemplo, "formulários do cliente A", "formulários do Hotel B").
* **training_container_name**: este campo é o nome do contêiner da conta de armazenamento em que armazenamos o conjunto de dados de treinamento. Ele pode ser o mesmo contêiner que **scoring_container_name**.
* **training_blob_root_folder**: a pasta dentro da conta de armazenamento na qual armazenaremos os arquivos para treinamento do modelo.
* **scoring_container_name**: esse campo é o nome do contêiner da conta de armazenamento em que armazenamos os arquivos dos quais desejamos extrair os pares de chave/valor. Ele pode ser o mesmo contêiner que **training_container_name**.
* **scoring_input_blob_folder**: a pasta na conta de armazenamento na qual armazenaremos os arquivos dos quais os dados serão extraídos.
* **model_id**: a ID do modelo que desejamos treinar novamente. Para a primeira execução, o valor deve ser definido como -1, o que fará com que o notebook de treinamento crie um modelo personalizado para treinar. O notebook de treinamento retornará a ID de modelo recém-criada para a instância do Azure Data Factory e, usando uma atividade de procedimento armazenado, atualizaremos esse valor no banco de dados SQL do Azure.

  Sempre que desejar ingerir um novo tipo de formulário, você precisará redefinir manualmente a ID do modelo como -1 antes de treinar o modelo.

* **file_type**: os tipos de arquivo com suporte são `application/pdf`, `image/jpeg`, `image/png` e `image/tif`.

  Se tiver formulários com tipos de arquivo diferentes, você precisará alterar esse valor e **model_id** ao treinar um novo tipo de formulário.
* **form_batch_group_id**: ao longo do tempo, você poderá ter vários tipos de formulário que treina em relação ao mesmo modelo. A **form_batch_group_id** permitirá que você especifique todos os tipos de formulários que foram treinados usando um modelo específico.

### <a name="create-the-table"></a>Criar a tabela

[Crie um Banco de Dados SQL do Azure](https://ms.portal.azure.com/#create/Microsoft.SQLDatabase) e execute o script SQL a seguir no [editor de consultas](../../azure-sql/database/connect-query-portal.md) para criar a tabela necessária.

```sql
CREATE TABLE dbo.ParamFormRecogniser(
    form_description varchar(50) NULL,
  training_container_name varchar(50) NOT NULL,
    training_blob_root_folder varchar(50) NULL,
    scoring_container_name varchar(50) NOT NULL,
    scoring_input_blob_folder varchar(50) NOT NULL,
    scoring_output_blob_folder varchar(50) NOT NULL,
    model_id varchar(50) NULL,
    file_type varchar(50) NULL
) ON PRIMARY
GO
```

Execute o script a seguir para criar o procedimento para atualizar automaticamente **model_id** uma vez treinado.

```SQL
CREATE PROCEDURE [dbo].[update_model_id] ( @form_batch_group_id  varchar(50),@model_id varchar(50))
AS
BEGIN 
    UPDATE [dbo].[ParamFormRecogniser]   
        SET [model_id] = @model_id  
    WHERE form_batch_group_id =@form_batch_group_id
END
```

## <a name="use-azure-key-vault-to-store-sensitive-credentials"></a>Usar o Azure Key Vault para armazenar credenciais confidenciais

Por motivos de segurança, não queremos armazenar certas informações confidenciais na tabela de parametrização no banco de dados SQL do Azure. Armazenaremos parâmetros confidenciais como segredos no Azure Key Vault.

### <a name="create-an-azure-key-vault"></a>Criar um Cofre de chaves do Azure

[Criar um recurso do Key Vault](https://ms.portal.azure.com/#create/Microsoft.KeyVault). Em seguida, navegue até o recurso do Key Vault após ele ter sido criado e, na seção **Configurações**, selecione **segredos** para adicionar os parâmetros.

Uma nova janela será exibida, selecione **Gerar/importar**. Insira o nome do parâmetro e seu valor e clique em criar. Faça isso para os seguintes parâmetros:

* **CognitiveServiceEndpoint**: a URL do ponto de extremidade da API do Reconhecimento de Formulários.
* **CognitiveServiceSubscriptionKey**: a chave de acesso para o serviço de Reconhecimento de Formulários. 
* **StorageAccountName**: a conta de armazenamento em que o conjunto de dados de treinamento e os formulários dos quais desejamos extrair pares chave-valor estão armazenados. Se eles estiverem em contas diferentes, insira o nome de cada uma das contas como segredos separados. Lembre-se de que os conjuntos de dados de treinamento devem estar no mesmo contêiner para todos os tipos de formulário, mas podem estar em pastas diferentes.
* **StorageAccountSasKey**: a SAS (assinatura de acesso compartilhado) da conta de armazenamento. Para recuperar a URL da SAS, vá para o recurso de armazenamento e selecione a guia **Gerenciador de Armazenamento**. Navegue até o contêiner, clique com o botão direito do mouse e selecione **Obter assinatura de acesso compartilhado**. É importante obter o SAS para o contêiner, não para a conta de armazenamento em si. Verifique se as permissões de **Leitura** e **Lista** estão marcadas e clique em **Criar**. Em seguida, copie o valor na seção **URL**. Deve ter o formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

## <a name="train-your-form-recognizer-model-in-a-databricks-notebook"></a>Treinar o modelo do Reconhecimento de Formulários em um notebook do Databricks

Você usará o Azure Databricks para armazenar e executar o código Python que interage com o serviço de Reconhecimento de Formulários.

### <a name="create-a-notebook-in-databricks"></a>Criar um notebook no Databricks

[Crie um recurso do Azure Databricks](https://ms.portal.azure.com/#create/Microsoft.Databricks) no portal do Azure. Navegue até o recurso após ele ter sido criado e inicie o workspace.

### <a name="create-a-secret-scope-backed-by-azure-key-vault"></a>Criar um escopo de segredo apoiado pelo Azure Key Vault

Para referenciar os segredos no Azure Key Vault que criamos acima, você precisará criar um escopo de segredo no Databricks. Siga as etapas em [Criar um escopo de segredo apoiado pelo Azure Key Vault](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope).

### <a name="create-a-databricks-cluster"></a>Criar um cluster Databricks

Um cluster é uma coleção de recursos de computação do Databricks. Para criar um cluster:

1. Na barra lateral, clique no botão **Clusters**.
1. Na página **Clusters**, clique em **Criar Cluster**.
1. Na página **Criar Cluster**, especifique um nome para o cluster e selecione **7.2 (Scala 2.12, Spark 3.0.0)** na lista suspensa Versão do Databricks Runtime.
1. Clique em **Criar Cluster**.

### <a name="write-a-settings-notebook"></a>Escrever um notebook de configurações

Agora, você está pronto para adicionar notebooks Python. Primeiro, crie um notebook chamado **Configurações**, que atribuirá os valores em sua tabela de parametrização a variáveis no script. Posteriormente, os valores serão passados como parâmetros pelo Azure Data Factory. Também atribuiremos valores dos segredos no Key Vault às variáveis. 

1. Para criar o notebook **Configurações**, clique no botão **workspace**. Na nova guia, clique na lista suspensa e selecione **criar** e **notebook**.
1. Na janela pop-up, insira o nome que deseja dar ao notebook e selecione **Python** como idioma padrão. Selecione o cluster do Databricks e selecione **Criar**.
1. Na primeira célula do notebook, recuperamos os parâmetros passados pelo Azure Data Factory.

    ```python 
    dbutils.widgets.text("form_batch_group_id", "","")
    dbutils.widgets.get("form_batch_group_id")
    form_batch_group_id = getArgument("form_batch_group_id")
    
    dbutils.widgets.text("model_id", "","")
    dbutils.widgets.get("model_id")
    model_id = getArgument("model_id")
    
    dbutils.widgets.text("training_container_name", "","")
    dbutils.widgets.get("training_container_name")
    training_container_name = getArgument("training_container_name")
    
    dbutils.widgets.text("training_blob_root_folder", "","")
    dbutils.widgets.get("training_blob_root_folder")
    training_blob_root_folder=  getArgument("training_blob_root_folder")
    
    dbutils.widgets.text("scoring_container_name", "","")
    dbutils.widgets.get("scoring_container_name")
    scoring_container_name=  getArgument("scoring_container_name")
    
    dbutils.widgets.text("scoring_input_blob_folder", "","")
    dbutils.widgets.get("scoring_input_blob_folder")
    scoring_input_blob_folder=  getArgument("scoring_input_blob_folder")
    
    
    dbutils.widgets.text("file_type", "","")
    dbutils.widgets.get("file_type")
    file_type = getArgument("file_type")
    
    dbutils.widgets.text("file_to_score_name", "","")
    dbutils.widgets.get("file_to_score_name")
    file_to_score_name=  getArgument("file_to_score_name")
    ```

1. Na segunda célula, recuperamos segredos do Key Vault e os atribuímos a variáveis.

    ```python 
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    training_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    ScoredFile = file_to_score_name+ "_output.json"
    training_storage_account_url="https://"+training_storage_account_name+".blob.core.windows.net/"+training_container_name+storage_account_sas_key 
    ```

### <a name="write-a-training-notebook"></a>Escrever um notebook de treinamento

Agora que concluímos o notebook de **Configurações**, podemos criar um notebook para treinar o modelo. Conforme mencionado acima, usaremos os arquivos armazenados em uma pasta em uma conta de armazenamento do Azure Data Lake Gen 2 (**training_blob_root_folder**). O nome da pasta foi passado como uma variável. Cada conjunto de tipos de formulário estará na mesma pasta e, à medida que executarmos um loop sobre a tabela de parâmetros, treinaremos o modelo usando todos os tipos de formulário. 

1. Crie um notebook chamado **TrainFormRecognizer**. 
1. Na primeira célula, execute o notebook Configurações:

    ```python
    %run "./Settings"
    ```

1. Na próxima célula, atribua variáveis do arquivo **Configurações** e treine dinamicamente o modelo para cada tipo de formulário, aplicando o código no [Início rápido do REST](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/rest/python-train-extract.md#get-training-results%20).

    ```python
    import json
    import time
    from requests import get, post
    
    post_url = cognitive_service_endpoint + r"/formrecognizer/v2.0/custom/models"
    source = training_storage_account_url
    prefix= training_blob_root_folder
    
    includeSubFolders=True
    useLabelFile=False
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    body =     {
        "source": source
        ,"sourceFilter": {
            "prefix": prefix,
            "includeSubFolders": includeSubFolders
       },
    }
    if model_id=="-1": # if you don't already have a model you want to retrain. In this case, we create a model and use it to extract the key-value pairs
      try:
          resp = post(url = post_url, json = body, headers = headers)
          if resp.status_code != 201:
              print("POST model failed (%s):\n%s" % (resp.status_code, json.dumps(resp.json())))
              quit()
          print("POST model succeeded:\n%s" % resp.headers)
          get_url = resp.headers["location"]
          model_id=get_url[get_url.index('models/')+len('models/'):]     
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    else :# if you already have a model you want to retrain, we reuse it and (re)train with the new form types.  
      try:
        get_url =post_url+r"/"+model_id
          
      except Exception as e:
          print("POST model failed:\n%s" % str(e))
          quit()
    ```

1. A etapa final do processo de treinamento é obter o resultado do treinamento em um formato JSON.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
        try:
            resp = get(url = get_url, headers = headers)
            resp_json = resp.json()
            print (resp.status_code)
            if resp.status_code != 200:
                print("GET model failed (%s):\n%s" % (resp.status_code, json.dumps(resp_json)))
                n_try += 1
                quit()
            model_status = resp_json["modelInfo"]["status"]
            print (model_status)
            if model_status == "ready":
                print("Training succeeded:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            if model_status == "invalid":
                print("Training failed. Model is invalid:\n%s" % json.dumps(resp_json))
                n_try += 1
                quit()
            # Training still running. Wait and retry.
            time.sleep(wait_sec)
            n_try += 1
            wait_sec = min(2*wait_sec, max_wait_sec)     
            print (n_try)
        except Exception as e:
            msg = "GET model failed:\n%s" % str(e)
            print(msg)
            quit()
    print("Train operation did not complete within the allocated time.")
    ```

## <a name="extract-form-data-using-a-notebook"></a>Extrair dados do formulário usando um notebook

### <a name="mount-the-azure-data-lake-storage"></a>Montar o Azure Data Lake Storage

A próxima etapa é pontuar os diferentes formulários que temos usando o modelo treinado. Montaremos a conta de armazenamento do Azure Data Lake no Databricks e faremos referência à montagem durante o processo de ingestão.

Assim como no estágio de treinamento, usaremos o Azure Data Factory para invocar a extração dos pares de chave-valor dos formulários. Executaremos um loop sobre os formulários nas pastas especificadas na tabela de parâmetros.

1. Vamos criar o notebook para montar a conta de armazenamento no Databricks. Vamos chamá-lo de **MountDataLake**. 
1. Você precisará chamar o notebook **Configurações** primeiro:

    ```python
    %run "./Settings"
    ```

1. Na segunda célula, definiremos variáveis para os parâmetros confidenciais, que recuperaremos de nossos segredos do Key Vault.

    ```python
    cognitive_service_subscription_key = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveserviceSubscriptionKey")
    cognitive_service_endpoint = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "CognitiveServiceEndpoint")
    
    scoring_storage_account_name = dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountName")
    scoring_storage_account_sas_key= dbutils.secrets.get(scope = "FormRecognizer_SecretScope", key = "StorageAccountSasKey")  
    
    scoring_mount_point = "/mnt/"+scoring_container_name 
    scoring_source_str = "wasbs://{container}@{storage_acct}.blob.core.windows.net/".format(container=scoring_container_name, storage_acct=scoring_storage_account_name) 
    scoring_conf_key = "fs.azure.sas.{container}.{storage_acct}.blob.core.windows.net".format(container=scoring_container_name, storage_acct=scoring_storage_account_name)
    
    ```

1. Em seguida, tentaremos desmontar a conta de armazenamento caso ela tenha sido montada anteriormente.

    ```python
    try:
      dbutils.fs.unmount(scoring_mount_point) # Use this to unmount as needed
    except:
      print("{} already unmounted".format(scoring_mount_point))
    
    ```

1. Por fim, montaremos a conta de armazenamento.


    ```python
    try: 
      dbutils.fs.mount( 
        source = scoring_source_str, 
        mount_point = scoring_mount_point, 
        extra_configs = {scoring_conf_key: scoring_storage_account_sas_key} 
      ) 
    except Exception as e: 
      print("ERROR: {} already mounted. Run previous cells to unmount first".format(scoring_mount_point))
    
    ```

    > [!NOTE]
    > Montamos apenas a conta de armazenamento de treinamento&mdash;nesse caso, os arquivos de treinamento e os arquivos dos quais desejamos extrair pares de chave-valor estão na mesma conta de armazenamento. Se suas contas de armazenamento de pontuação e de treinamento forem diferentes, será necessário montar as duas contas de armazenamento aqui. 

### <a name="write-the-scoring-notebook"></a>Escrever o notebook de pontuação

Agora, podemos criar um notebook de pontuação. De maneira semelhante ao notebook de treinamento, usaremos os arquivos armazenados em pastas na conta de armazenamento do Azure Data Lake que acabamos de montar. O nome da pasta é passado como uma variável. Executaremos um loop sobre todos os formulários na pasta especificada e extrairemos os pares de chave-valor deles. 

1. Crie um notebook e dê a ele o nome **ScoreFormRecognizer**. 
1. Execute os notebooks **Configurações** e **MountDataLake**.

    ```python
    %run "./Settings"
    %run "./MountDataLake"
    ```

1. Em seguida, adicione o código a seguir, que chama a API de [Análise](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm).

    ```python
    ########### Python Form Recognizer Async Analyze #############
    import json
    import time
    from requests import get, post 
    
    
    #prefix= TrainingBlobFolder
    post_url = cognitive_service_endpoint + "/formrecognizer/v2.0/custom/models/%s/analyze" % model_id
    source = r"/dbfs/mnt/"+scoring_container_name+"/"+scoring_input_blob_folder+"/"+file_to_score_name
    output = r"/dbfs/mnt/"+scoring_container_name+"/scoringforms/ExtractionResult/"+os.path.splitext(os.path.basename(source))[0]+"_output.json"
    
    params = {
        "includeTextDetails": True
    }
    
    headers = {
        # Request headers
        'Content-Type': file_type,
        'Ocp-Apim-Subscription-Key': cognitive_service_subscription_key,
    }
    
    with open(source, "rb") as f:
        data_bytes = f.read()
    
    try:
        resp = post(url = post_url, data = data_bytes, headers = headers, params = params)
        if resp.status_code != 202:
            print("POST analyze failed:\n%s" % json.dumps(resp.json()))
            quit()
        print("POST analyze succeeded:\n%s" % resp.headers)
        get_url = resp.headers["operation-location"]
    except Exception as e:
        print("POST analyze failed:\n%s" % str(e))
        quit() 
    ```

1. Na próxima célula, obteremos os resultados da extração do par chave-valor. Esta célula produzirá o resultado. Como queremos que o resultado no formato JSON seja processado ainda mais em nosso Banco de Dados SQL do Azure ou Cosmos DB, gravaremos o resultado em um arquivo. JSON. O nome do arquivo de saída será o nome do arquivo pontuado, concatenado com "_output.json". O arquivo será armazenado na mesma pasta que o arquivo de origem.

    ```python
    n_tries = 10
    n_try = 0
    wait_sec = 5
    max_wait_sec = 5
    while n_try < n_tries:
       try:
           resp = get(url = get_url, headers = {"Ocp-Apim-Subscription-Key": cognitive_service_subscription_key})
           resp_json = resp.json()
           if resp.status_code != 200:
               print("GET analyze results failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           status = resp_json["status"]
           if status == "succeeded":
               print("Analysis succeeded:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           if status == "failed":
               print("Analysis failed:\n%s" % json.dumps(resp_json))
               n_try += 1
               quit()
           # Analysis still running. Wait and retry.
           time.sleep(wait_sec)
           n_try += 1
           wait_sec = min(2*wait_sec, max_wait_sec)     
       except Exception as e:
           msg = "GET analyze results failed:\n%s" % str(e)
           print(msg)
           n_try += 1
           print("Analyze operation did not complete within the allocated time.")
           quit()
    
    ```
1. Execute o procedimento de gravação de arquivo em uma nova célula:

    ```python
    import requests
    file = open(output, "w")
    file.write(str(resp_json))
    file.close()
    ```

## <a name="automate-training-and-scoring-with-azure-data-factory"></a>Automatizar o treinamento e a pontuação com o Azure Data Factory

A única etapa restante é configurar o serviço do ADF (Azure Data Factory) para automatizar os processos de treinamento e pontuação. Primeiro, siga as etapas em [Criar um data factory](../../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory). Depois de criar o recurso do ADF, você precisará criar três pipelines: um para treinamento e dois para pontuação (explicados abaixo).

### <a name="training-pipeline"></a>Pipeline de treinamento

A primeira atividade no pipeline de treinamento é uma Pesquisa para ler e retornar os valores na tabela de parametrização no banco de dados SQL do Azure. Como todos os conjuntos de dados de treinamento estarão na mesma conta de armazenamento e contêiner (mas potencialmente em pastas diferentes), manteremos o atributo **Primeira linha apenas** com o valor padrão nas configurações da atividade de pesquisa. Para cada tipo de formulário com o qual treinar o modelo, treinaremos o modelo usando todos os arquivos em **training_blob_root_folder**.

:::image type="content" source="./media/tutorial-bulk-processing/training-pipeline.png" alt-text="pipeline de treinamento no data factory":::

O procedimento armazenado usa dois parâmetros: **model_id** e **form_batch_group_id**. O código para retornar a ID do modelo do notebook do Databricks é `dbutils.notebook.exit(model_id)`, e o código para ler o código na atividade de procedimento armazenado no data factory é `@activity('GetParametersFromMetadaTable').output.firstRow.form_batch_group_id`.

### <a name="scoring-pipelines"></a>Pipelines de pontuação

Para extrair os pares chave-valor, examinaremos todas as pastas na tabela parametrização e, para cada pasta, extrairemos os pares chave-valor de todos os arquivos contidos. Até o momento, o ADF não dá suporte a loops ForEach aninhados. Sendo assim, criaremos dois pipelines. O primeiro pipeline fará a pesquisa da tabela de parametrização e passará a lista de pastas como um parâmetro para o segundo pipeline.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1a.png" alt-text="primeiro pipeline de pontuação no data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-1b.png" alt-text="primeiro pipeline de pontuação no data factory, detalhes":::

O segundo pipeline usará uma atividade GetMeta para obter a lista dos arquivos na pasta e passá-la como um parâmetro para o notebook do Databricks de pontuação.

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2a.png" alt-text="segundo pipeline de pontuação no data factory":::

:::image type="content" source="./media/tutorial-bulk-processing/scoring-pipeline-2b.png" alt-text="segundo pipeline de pontuação no data factory, detalhes":::

### <a name="specify-a-degree-of-parallelism"></a>Especificar um grau de paralelismo

Nos pipelines de treinamento e de pontuação, você pode especificar o grau de paralelismo para processar vários formulários simultaneamente.

Para definir o grau de paralelismo no pipeline do ADF:

* Selecione a atividade ForEach.
* Desmarque a caixa **Sequencial**.
* Defina o grau de paralelismo na caixa de texto **Contagem em lote**. Recomendamos uma contagem máxima de lotes de 15 para a pontuação.

:::image type="content" source="./media/tutorial-bulk-processing/parallelism.png" alt-text="configuração de paralelismo para a atividade de pontuação no ADF":::

## <a name="how-to-use"></a>Como usar

Agora você tem um pipeline automatizado para digitalizar sua lista de pendências de formulários e executar alguma análise sobre ele. Quando você adiciona novos formulários de um tipo conhecido a uma pasta de armazenamento existente, basta executar novamente os pipelines de pontuação e eles atualizarão todos os arquivos de saída, incluindo os arquivos de saída dos novos formulários. 

Se você adicionar novos formulários de um novo tipo, também precisará carregar um conjunto de dados de treinamento para o contêiner apropriado. Em seguida, adicione uma nova linha à tabela de parametrização, inserindo os locais dos novos documentos e seu respectivo conjunto de dados de treinamento. Insira um valor de -1 para **model_ID** para indicar que um novo modelo deve ser treinado para esses formulários. Em seguida, execute o pipeline de treinamento no ADF. Ele lerá da tabela, treinará um modelo e substituirá a ID do modelo na tabela. Em seguida, você pode chamar os pipelines de pontuação para começar a gravar os arquivos de saída.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você configurou pipelines do Azure Data Factory para acionar o treinamento e a execução de modelos do Reconhecimento de Formulários para digitalizar uma grande lista de pendências de arquivos. Em seguida, explore a API do Reconhecimento de Formulários para ver o que mais você pode fazer com ele.

* [API REST do Reconhecimento de Formulários](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeBusinessCardAsync)