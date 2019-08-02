---
title: Tutorial para acessar o Armazenamento de Blobs do Azure no Azure Databricks usando o Azure Key Vault
description: Este tutorial descreve como acessar o Armazenamento de Blobs do Azure no Azure Databricks usando os segredos armazenados em um cofre de chaves.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 4d80755cdf49246a8772cca82e2a71c6cccbf13a
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371386"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Tutorial: acessar o Armazenamento de Blobs do Azure no Azure Databricks usando o Azure Key Vault

Este tutorial descreve como acessar o Armazenamento de Blobs do Azure no Azure Databricks usando os segredos armazenados em um cofre de chaves.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento e um contêiner de blobs
> * Criar um Azure Key Vault e adicionar um segredo
> * Criar um workspace do Azure Databricks e adicionar um escopo de segredo
> * Acessar seu contêiner de blobs do Azure Databricks

## <a name="prerequisites"></a>Pré-requisitos

- Assinatura do Azure - [criar uma gratuitamente](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/). Este tutorial não pode ser realizado usando a Assinatura de avaliação gratuita do Azure. Antes de criar o cluster, acesse seu perfil e altere a assinatura para **paga conforme o uso**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="create-a-storage-account-and-blob-container"></a>Criar uma conta de armazenamento e um contêiner de blobs

1. No portal do Azure, selecione **Criar um recurso** > **Armazenamento**. Depois selecione **Conta de armazenamento**.

   ![Localize o recurso da Conta de Armazenamento do Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Selecione a assinatura e o grupo de recursos ou crie um grupo de recursos. Em seguida, insira o nome de uma conta de armazenamento e escolha um local. Selecione **Examinar + criar**.

   ![Definir as propriedades da conta de armazenamento](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Se a validação não for bem-sucedida, resolva os problemas e tente novamente. Se a validação for bem-sucedida, selecione **Criar** e aguarde até que a conta de armazenamento seja criada.

4. Navegue até sua conta de armazenamento recém-criada e selecione **Blobs** em **serviços**, na página de **Visão geral**. Em seguida, selecione **+ contêiner** e insira um nome de contêiner. Selecione **OK**.

   ![Criar um novo contêiner](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Localize um arquivo que deseja carregar no contêiner de armazenamento de blobs. Se você não tiver um arquivo, use um editor de texto para criar um arquivo de texto com algumas informações. Neste exemplo, um arquivo chamado **hw.txt** contém o texto "Olá, mundo". Salve o arquivo de texto localmente e carregue-o no contêiner de armazenamento de blobs.

   ![Carregue o arquivo no contêiner](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Retorne para sua conta de armazenamento e selecione **Chaves de acesso** em **Configurações**. Copie o **nome da conta de armazenamento** e a **chave 1** para um editor de texto para uso posterior neste tutorial.

   ![Localize as chaves de acesso da conta de armazenamento](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Criar um Azure Key Vault e adicionar um segredo

1. No portal do Azure, selecione **Criar um recurso** e insira **Key Vault** na caixa de pesquisa.

   ![Crie uma caixa de pesquisa de recursos do Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. O recurso do Key Vault é selecionado automaticamente. Selecione **Criar**.

   ![Crie um recurso do Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Na página **Criar cofre de chaves**, insira as informações a seguir e mantenha os valores padrão para os campos restantes:

   |Propriedade|Descrição|
   |--------|-----------|
   |NOME|Um nome exclusivo para seu cofre de chaves.|
   |Subscription|Escolha uma assinatura.|
   |Resource group|Escolha um grupo de recursos ou crie um.|
   |Location|Escolha um local.|

   ![Propriedades do cofre de chaves do Azure](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Depois de fornecer as informações acima, selecione **Criar**. 

4. Navegue até o cofre de chaves recém-criado no portal do Azure e selecione **Segredos**. Em seguida, selecione **+ Gerar/Importar**. 

   ![Gerar novo segredo para o cofre de chaves](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Na página **Criar segredo**, insira as informações a seguir e mantenha os valores padrão para os campos restantes:

   |Propriedade|Valor|
   |--------|-----------|
   |Opções de upload|Manual|
   |NOME|Nome amigável para sua chave de conta de armazenamento.|
   |Valor|key1 da sua conta de armazenamento.|

   ![Propriedades do novo segredo do cofre de chaves](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Salve o nome da chave em um editor de texto para uso posterior neste tutorial e selecione **Criar**. Em seguida, navegue até o menu **Propriedades**. Copie o **Nome DNS** e a **ID do Recurso** para um editor de texto para uso posterior no tutorial.

   ![Copie o nome DNS e a ID do recurso do Azure Key Vault](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Criar um workspace do Azure Databricks e adicionar um escopo de segredo

1. No Portal do Azure, selecione **Criar um recurso** > **Análise** > **Azure Databricks**.

    ![Databricks no portal do Azure](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. Em **Serviço do Azure Databricks**, forneça os valores a seguir para criar um workspace do Databricks.

   |Propriedade  |DESCRIÇÃO  |
   |---------|---------|
   |Nome do workspace     | Forneça um nome para seu workspace do Databricks        |
   |Subscription     | Na lista suspensa, selecione sua assinatura do Azure.        |
   |Resource group     | Selecione o mesmo grupo de recursos que contém o seu cofre de chaves. |
   |Location     | Selecione o mesmo local que o seu Azure Key Vault. Para todas as regiões disponíveis, consulte os [serviços do Azure disponíveis por região](https://azure.microsoft.com/regions/services/).        |
   |Camada de preços     |  Escolha entre o cluster **Standard** e o **Premium**. Para saber mais sobre essas camadas, confira [Página de preços do Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Propriedades do workspace do Databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Selecione **Criar**.

3. Navegue até o recurso recém-criado do Azure Databricks no portal do Azure e selecione **Iniciar Workspace**.

   ![Iniciar workspace do Azure Databricks](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Quando o workspace do Azure Databricks for aberto em uma janela separada, acrescente **#secrets/createScope** à URL. A URL deve ter o seguinte formato: 

   **https://<\local>.azuredatabricks.net/?o=<\id>#secrets/createScope**.

5. Insira um nome de escopo e depois o nome DNS e a ID do Recurso do Azure Key Vault salvos anteriormente. Salve o nome do escopo em um editor de texto para uso posterior neste tutorial. Em seguida, selecione **Criar**.

   ![Criar escopo do segredo no workspace do Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Acessar seu contêiner de blobs do Azure Databricks

1. Na página inicial do workspace do Azure Databricks, selecione **Novo cluster** em **Tarefas comuns**.

   ![Criar um notebook do Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Insira um nome de cluster e selecione **Criar cluster**. A criação do cluster leva alguns minutos para ser concluída.

3. Depois que o cluster for criado, navegue até a página inicial do workspace do Azure Databricks e selecione **Novo notebook** em **Tarefas comuns**.

   ![Criar um notebook do Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Insira um nome de notebook e defina a linguagem como Python. Defina o cluster com o nome do cluster que você criou na etapa anterior.

5. Execute o comando a seguir para montar o contêiner de armazenamento de blobs. Lembre-se de alterar os valores para as seguintes propriedades:

   * your-container-name
   * your-storage-account-name
   * mount-name
   * config-key
   * scope-name
   * key-name

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** é um caminho DBFS que representa onde será montado o contêiner de armazenamento de blobs ou uma pasta dentro do contêiner (especificada na fonte).
   * **conf-key** pode ser `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` ou `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** é o nome do escopo do segredo que você criou na seção anterior. 
   * **key-name** é o nome do segredo que você criou para a chave da conta de armazenamento no cofre de chaves.

   ![Criar montagem de armazenamento de blobs no notebook](./media/store-secrets-azure-key-vault/command1.png)

6. Execute o comando a seguir para ler o arquivo de texto em seu contêiner de armazenamento de blobs para um dataframe. Altere os valores no comando para corresponder aos seus nomes de montagem e do arquivo.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Ler arquivo para dataframe](./media/store-secrets-azure-key-vault/command2.png)

7. Use o comando a seguir para exibir o conteúdo do seu arquivo.

   ```python
   df.show()
   ```
   ![Mostrar dataframe](./media/store-secrets-azure-key-vault/command3.png)

8. Para desmontar seu armazenamento de blobs, execute o comando a seguir:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Desmontar conta de armazenamento](./media/store-secrets-azure-key-vault/command4.png)

9. Observe que depois que a montagem tiver sido desmontada, você não poderá mais ler da conta de armazenamento de blobs.

   ![Erro ao desmontar conta de armazenamento](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Limpar recursos

Se você não quiser continuar usando este aplicativo, exclua o grupo de recursos inteiro por meio das seguintes etapas:

1. No menu à esquerda, no portal do Azure, selecione **Grupos de recursos** e navegue até o seu grupo de recursos.

2. Selecione **Excluir grupo de recursos** e digite o nome do seu grupo de recursos. Em seguida, selecione **Excluir**. 

## <a name="next-steps"></a>Próximas etapas

Avance para o próximo artigo para saber como implementar um ambiente do Databricks injetado com VNet com um Ponto de Extremidade de Serviço habilitado para o Cosmos DB.
> [!div class="nextstepaction"]
> [Tutorial: implementar o Azure Databricks com um ponto de extremidade do Cosmos DB](service-endpoint-cosmosdb.md)
