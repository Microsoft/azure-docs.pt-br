---
title: 'Tutorial: Criar um pipeline de ETL de ponta a ponta para obter insights de vendas no Azure HDInsight'
description: Saiba como usar criar pipelines de ETL com o Azure HDInsight para obter insights de dados de vendas usando clusters sob demanda do Spark e o Power BI.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 04/15/2020
ms.openlocfilehash: 140709a0ddb548c126ceca208fdeef2db77616bf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761791"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Tutorial: Criar um pipeline de dados de ponta a ponta para obter insights de vendas no Azure HDInsight

Neste tutorial, você criará um pipeline de dados ponta a ponta, que executa operações de ETL (extração, transformação e carregamento). O pipeline usará clusters [Apache Spark](./spark/apache-spark-overview.md) e Apache Hive em execução no Azure HDInsight para consulta e manipulação dos dados. Você também usará tecnologias como o Azure Data Lake Storage Gen2 para armazenamento de dados e o Power BI para visualização.

Esse pipeline de dados combina os dados de vários repositórios, remove os dados indesejados, acrescenta novos dados e carrega tudo isso novamente no armazenamento para visualizar insights empresariais. Leia mais sobre pipelines de ETL [(extração, transformação e carregamento) em escala](./hadoop/apache-hadoop-etl-at-scale.md).

:::image type="content" source="./media/hdinsight-sales-insights-etl/architecture.png" alt-text="Arquitetura de ETL" border="false":::

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure – versão 2.2.0 ou posterior. Confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

* jq, um processador JSON de linha de comando.  Confira [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* Um membro da [Função interna do Azure – proprietário](../role-based-access-control/built-in-roles.md).

* Se você estiver usando o PowerShell para disparar o pipeline do Data Factory, precisará do [módulo Az](/powershell/azure/).

* [Power BI Desktop](https://aka.ms/pbiSingleInstaller) para visualizar insights empresariais no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonar o repositório com scripts e dados

1. Acesse a sua assinatura do Azure. Se você pretende usar o Azure Cloud Shell, selecione **Experimentar** no canto superior direito do bloco de código. Caso contrário, insira o comando a seguir:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Verifique se você é membro da função [proprietário](../role-based-access-control/built-in-roles.md) do Azure. Substitua `user@contoso.com` pela sua conta e, em seguida, insira o comando:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Se nenhum registro foi retornado, você não é um membro e não pode concluir este tutorial.

1. Baixe os dados e os scripts deste tutorial no [repositório de ETL de insights de vendas do HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl). Insira o seguinte comando:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Verifique se `salesdata scripts templates` foi criado. Verifique com o seguinte comando:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Implantar os recursos do Azure necessários para o pipeline

1. Adicione permissões de execução para todos os scripts digitando:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Defina a variável para o grupo de recursos. Substitua `RESOURCE_GROUP_NAME` pelo nome de um grupo de recursos novo ou existente e, em seguida, digite o comando:

    ```bash
    resourceGroup="RESOURCE_GROUP_NAME"
    ```

1. Execute o script. Substitua `LOCATION` por um valor desejado e, em seguida, digite o comando:

    ```bash
    ./scripts/resources.sh $resourceGroup LOCATION
    ```

    Se você não tiver certeza de qual região especificar, poderá recuperar uma lista de regiões compatíveis com a assinatura com o comando [az account list-locations](/cli/azure/account#az_account_list_locations).

    O comando exibirá os seguintes recursos:

    * Uma conta de Armazenamento de Blobs do Azure. Essa conta armazenará os dados de vendas da empresa.
    * Uma conta do Azure Data Lake Storage Gen2. Essa conta servirá como a conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre o HDInsight e o Data Lake Storage Gen2 na [integração do Azure HDInsight ao Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Uma identidade gerenciada atribuída ao usuário. Essa conta fornece aos clusters HDInsight o acesso à conta do Data Lake Storage Gen2.
    * Um cluster do Apache Spark. Esse cluster será usado para limpar e transformar os dados brutos.
    * Um cluster [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) do Apache Hive. Esse cluster permitirá consultar os dados de vendas visualizando-os com o Power BI.
    * Uma rede virtual do Azure compatível com as regras do NSG (grupo de segurança de rede). Essa rede virtual permite que os clusters se comuniquem e protejam suas comunicações.

A criação do cluster pode levar cerca de 20 minutos.

A senha padrão usada para acesso SSH aos clusters é `Thisisapassword1`. Caso deseje alterar a senha, vá até o arquivo `./templates/resourcesparameters_remainder.json` e altere a senha para os parâmetros `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implantação e coletar informações de recursos

1. Caso deseje verificar o status da implantação, vá até o grupo de recursos no portal do Azure. Em **Configurações**, selecione **Implantações** e depois a sua implantação. Aqui você pode ver os recursos que foram implantados com êxito e aqueles que ainda estão em andamento.

1. Para exibir os nomes dos clusters, digite o seguinte comando:

    ```bash
    sparkClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.sparkClusterName.value')
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')

    echo "Spark Cluster" $sparkClusterName
    echo "LLAP cluster" $llapClusterName
    ```

1. Para exibir a conta de armazenamento e a chave de acesso do Azure, insira o seguinte comando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Para exibir a conta e a chave de acesso do Azure Data Lake Storage Gen2, insira o seguinte comando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Criar uma data factory

O Azure Data Factory é uma ferramenta que ajuda a automatizar Azure Pipelines. Não é a única maneira de realizar essas tarefas, mas é uma ótima forma de automatizar esses processos. Para obter mais informações sobre o Azure Data Factory, veja a [Documentação do Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Esse data factory terá um pipeline com duas atividades:

* A primeira atividade copiará os dados do Armazenamento de Blobs do Azure para a conta de armazenamento do Data Lake Storage Gen 2 para simular a ingestão de dados.
* A segunda atividade transformará os dados no cluster Spark. O script transforma os dados removendo colunas indesejadas. Ele também acrescenta uma nova coluna que calcula a receita que uma única transação gera.

Para configurar o pipeline do Azure Data Factory, execute o comando abaixo.  Você ainda deve estar no diretório `hdinsight-sales-insights-etl`.

```bash
blobStorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.blobStorageName.value')
ADLSGen2StorageName=$(cat resourcesoutputs_storage.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

./scripts/adf.sh $resourceGroup $ADLSGen2StorageName $blobStorageName
```

Esse script realiza as seguintes ações:

1. Cria uma entidade de serviço com permissões `Storage Blob Data Contributor` na conta de armazenamento do Data Lake Storage Gen2.
1. Obtém um token de autenticação para autorizar solicitações POST para a [API REST do sistema de arquivos do Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real de sua conta de armazenamento do Data Lake Storage Gen2 nos arquivos `sparktransform.py` e `query.hql`.
1. Obtém as chaves de armazenamento para o Data Lake Storage Gen2 e as Contas de Armazenamento de Blobs.
1. Cria outra implantação de recurso para criar um pipeline do Azure Data Factory, com suas atividades e seus serviços vinculados associados. Ela passa as chaves de armazenamento como parâmetros para o arquivo de modelo, de modo que os serviços vinculados possam acessar as contas de armazenamento corretamente.

## <a name="run-the-data-pipeline"></a>Executar o pipeline de dados

### <a name="trigger-the-data-factory-activities"></a>Disparar atividades do Data Factory

A primeira atividade no pipeline do Data Factory que você criou move os dados do Armazenamento de Blobs para o Data Lake Storage Gen2. A segunda atividade aplica as transformações do Spark nos dados e salva os arquivos .csv transformados em uma nova localização. O pipeline inteiro pode levar alguns minutos para ser concluído.

Para recuperar o nome do Data Factory, digite o seguinte comando:

```azurecli
cat resourcesoutputs_adf.json | jq -r '.properties.outputs.factoryName.value'
```

Para disparar o pipeline, você pode escolher uma das seguintes alternativas:

* Dispare o pipeline do Data Factory no PowerShell. Substitua `RESOURCEGROUP` e `DataFactoryName` pelos valores apropriados e, em seguida, execute os seguintes comandos:

    ```powershell
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"

    $resourceGroup="RESOURCEGROUP"
    $dataFactory="DataFactoryName"

    $pipeline =Invoke-AzDataFactoryV2Pipeline `
        -ResourceGroupName $resourceGroup `
        -DataFactory $dataFactory `
        -PipelineName "IngestAndTransform"

    Get-AzDataFactoryV2PipelineRun `
        -ResourceGroupName $resourceGroup  `
        -DataFactoryName $dataFactory `
        -PipelineRunId $pipeline
    ```

    Execute novamente `Get-AzDataFactoryV2PipelineRun` conforme necessário para monitorar o progresso.

    Ou

* Abra o data factory e selecione **Criar e Monitorar**. Dispare o pipeline `IngestAndTransform` no portal. Para obter informações sobre como disparar pipelines por meio do portal, veja [Criar clusters Apache Hadoop sob demanda no HDInsight usando o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Para verificar se o pipeline foi executado, execute uma das seguintes etapas:

* Vá até a seção **Monitorar** no data factory por meio do portal.
* Em Gerenciador de Armazenamento do Azure, vá para sua conta de armazenamento do Data Lake Storage Gen 2. Vá para o sistema de arquivos `files` e, em seguida, vá para a pasta `transformed` e verifique seu conteúdo para ver se o pipeline foi bem-sucedido.

Para obter outras maneiras de transformar dados usando o HDInsight, veja [artigo sobre como usar o Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Criar uma tabela no cluster da Consulta Interativa para exibir dados no Power BI

1. Copie o arquivo `query.hql` para o cluster LLAP usando o SCP. Insira o comando:

    ```bash
    llapClusterName=$(cat resourcesoutputs_remainder.json | jq -r '.properties.outputs.llapClusterName.value')
    scp scripts/query.hql sshuser@$llapClusterName-ssh.azurehdinsight.net:/home/sshuser/
    ```

    Lembrete: A senha padrão é `Thisisapassword1`.

1. Use SSH para acessar o cluster LLAP. Insira o comando:

    ```bash
    ssh sshuser@$llapClusterName-ssh.azurehdinsight.net
    ```

1. Use o comando a seguir para executar o script:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

    Esse script criará uma tabela gerenciada no cluster da Consulta Interativa que pode ser acessado no Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um dashboard do Power BI dos dados de vendas

1. Abra o Power BI Desktop.

1. No menu, navegue até **Obter dados** > **Mais...**  > **Azure** > **Interactive Query do HDInsight**.

1. Selecione **Conectar**.

1. Na caixa de diálogo **Interactive Query do HDInsight**:
    1. Na caixa de texto **Servidor**, digite o nome do cluster LLAP no formato `https://LLAPCLUSTERNAME.azurehdinsight.net`.
    1. Na caixa de texto **banco de dados**, digite `default`.
    1. Selecione **OK**.

1. Na caixa de diálogo **AzureHive**:
    1. Na caixa de texto **Nome de usuário**, digite `admin`.
    1. Na caixa de texto **Senha**, insira `Thisisapassword1`.
    1. Selecione **Conectar**.

1. No **Navegador**, selecione `sales` e/ou `sales_raw` para visualizar os dados. Depois que os dados forem carregados, você poderá experimentar o painel que deseja criar. Confira os seguintes links para obter uma introdução aos dashboards do Power BI:

* [Introdução aos dashboards para designers do Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Introdução ao serviço do Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não desejar continuar usando este aplicativo, exclua todos os recursos usando as etapas a seguir para não ser cobrado por eles.

1. Para remover o grupo de recursos, insira o comando:

    ```azurecli
    az group delete -n $resourceGroup
    ```

1. Para remover a entidade de serviço, insira os comandos:

    ```azurecli
    servicePrincipal=$(cat serviceprincipal.json | jq -r '.name')
    az ad sp delete --id $servicePrincipal
    ```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [ETL (extração, transformação e carregamento) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
