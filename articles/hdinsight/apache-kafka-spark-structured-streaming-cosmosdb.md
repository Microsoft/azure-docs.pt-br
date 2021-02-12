---
title: Apache Spark & Apache Kafka com Cosmos DB-Azure HDInsight
description: Saiba como usar o Streaming Estruturado do Apache Spark para ler dados do Apache Kafka e, em seguida, armazená-los no Azure Cosmos DB. Neste exemplo, você transmite dados usando um Jupyter Notebook do Spark no HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 11/18/2019
ms.openlocfilehash: e14784459a3c7733af274197507b76341d68477e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946909"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Use o Streaming Estruturado do Apache Spark com o Apache Kafka e o Azure Cosmos DB

Saiba como usar o Fluxo Estruturado do [Apache Spark](https://spark.apache.org/) [ para ler ](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)dados do [Apache Kafka](https://kafka.apache.org/) no Azure HDInsight, e em seguida, armazenar os dados no Microsoft Azure Cosmos DB.

O [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) é um banco de dados de vários modelos distribuído globalmente. Este exemplo usa um modelo de banco de dados de API do SQL. Para obter mais informações, consulte o documento [Bem-vindo ao Azure Cosmos DB](../cosmos-db/introduction.md).

O streaming estruturado do Spark é um mecanismo de processamento de fluxo baseado no Spark SQL. Ele permite expressar cálculos de streaming do mesmo modo que cálculos de lote em dados estáticos. Para saber mais sobre Streaming Estruturado, confira o [Guia de programação de streaming estruturado](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) em Apache.org.

> [!IMPORTANT]  
> Este exemplo usou o Spark 2.2 no HDInsight 3.6.
>
> As etapas neste documento criam um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
>
> Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

O Apache Kafka no HDInsight não fornece acesso para agentes de Kafka pela Internet pública. Qualquer coisa que se comunique com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, clusters de Spark e Kafka estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> O serviço Kafka é limitado a comunicação dentro da rede virtual. Outros serviços no cluster, como SSH e Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual do Azure, Kafka e clusters de Spark manualmente, é mais fácil usar um modelo do Azure Resource Manager. Use as seguintes etapas para implantar uma rede virtual do Azure, Kafka e clusters de Spark para sua assinatura do Azure.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    O modelo de Azure Resource Manager está localizado no repositório GitHub para este projeto ( [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) ).

    Este modelo cria os seguintes recursos:

   * Um cluster Kafka no HDInsight 3.6.

   * Um cluster Spark no HDInsight 3.6.

   * Uma Rede Virtual do Azure, que contém os clusters HDInsight. A rede virtual criada pelo modelo usa o espaço de endereço 10.0.0.0/16.

   * Um banco de dados da API do SQL do Azure Cosmos DB.

    > [!IMPORTANT]  
    > O bloco de anotações de streaming estruturado usado neste exemplo requer o Spark no HDInsight 3.6. Se você usar uma versão anterior do Spark no HDInsight, você receberá erros ao usar o bloco de anotações.

1. Use as seguintes informações para preencher as entradas na seção **Implantação personalizada**:

    |Propriedade |Valor |
    |---|---|
    |Subscription|Selecione sua assinatura do Azure.|
    |Resource group|Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.|
    |Nome da conta de Cosmos DB|este valor é usado como o nome da conta do Cosmos DB. O nome pode conter apenas letras minúsculas, números e o caractere de hífen (-). Ela deve ter entre 3 e 31 caracteres.|
    |Nome do cluster de base|Esse valor é usado como o nome de base dos clusters Spark e Kafka. Por exemplo, inserir **myhdi** cria um cluster Spark chamado __spark-myhdi__ e um cluster Kafka chamado **kafka-myhdi**.|
    |Versão do cluster|a versão do cluster do HDInsight. Este exemplo é testado com 3.6 HDInsight e pode não funcionar com outros tipos de cluster.|
    |Nome de usuário de logon do cluster|O nome do usuário administrador dos clusters Spark e Kafka.|
    |Senha de logon do cluster|A senha do usuário administrador dos clusters Spark e Kafka.|
    |Nome de Usuário SSH|O usuário SSH para criar para os clusters Spark e Kafka.|
    |Senha SSH|A senha para o usuário SSH dos clusters Kafka e Spark.|

    ![Valores de implantação personalizada do HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

1. Por fim, selecione **Comprar**. A criação dos clusters, da rede virtual e da conta do Cosmos DB pode levar até 45 minutos.

## <a name="create-the-cosmos-db-database-and-collection"></a>Criar o banco de dados e a coleção do Cosmos DB

O projeto usado neste documento armazena dados no Cosmos DB. Antes de executar o código, você deve primeiro criar um _banco de dados_ e uma _coleção_ em sua instância do Cosmos DB. Você também deve recuperar o ponto de extremidade do documento e a _chave_ usada para autenticar solicitações para o Cosmos DB.

Uma forma de fazer isso é usar o [CLI do Azure](/cli/azure/). O script a seguir criará um banco de dados denominado `kafkadata` e uma coleção denominada `kafkacollection`. A chave primária será retornada.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter Notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

O ponto de extremidade do documento e as informações de chave primária são semelhantes ao seguinte texto:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Salve os valores de ponto de extremidade e de chave, visto que eles são necessários nos Blocos de notas Jupyter.

## <a name="get-the-notebooks"></a>Obter os blocos de anotações

O código para o exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb) .

## <a name="upload-the-notebooks"></a>Carregar os blocos de anotações

Use as etapas a seguir para carregar os blocos de anotações do projeto para o Spark no cluster HDInsight:

1. No navegador da Web, conecte-se ao Jupyter Notebook em seu cluster Spark. Na URL a seguir, substitua `CLUSTERNAME` pelo nome do cluster __Spark__:

    ```http
    https://CLUSTERNAME.azurehdinsight.net/jupyter
    ```

    Quando solicitado, insira o nome de usuário e senha que você inseriu ao criar o cluster.

2. No canto superior direito da página, use o botão __Carregar__ para carregar o arquivo __Stream-taxi-data-to-kafka.ipynb__ no cluster. Selecione __Abrir__ para iniciar o upload.

3. Encontre a entrada __Stream-taxi-data-to-kafka.ipynb__ na lista de notebooks e selecione o botão __Carregar__ ao lado dela.

4. Repita as etapas 1 a 3 para carregar o notebook __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__.

## <a name="load-taxi-data-into-kafka"></a>Carregar dados de táxi no Kafka

Depois que os arquivos forem carregados, selecione a entrada __Stream-taxi-data-to-kafka.ipynb__ para abrir o bloco de anotações. Siga as etapas no bloco de anotações para carregar dados no Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Processar dados de táxi usando o Streaming Estruturado do Spark

Na home page do [Jupyter Notebook](https://jupyter.org/), selecione a entrada __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__. Siga as etapas no bloco de anotações para transmitir dados do Kafka para o Azure Cosmos DB usando o Streaming Estruturado do Spark.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar Apache Spark streaming estruturado, consulte os documentos a seguir para saber mais sobre como trabalhar com Apache Spark, Apache Kafka e Azure Cosmos DB:

* [Como usar o fluxo do Apache Spark (DStream) com o Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Iniciar com o Jupyter Notebook e Apache Spark no HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Bem-vindo(a) ao Azure Cosmos DB](../cosmos-db/introduction.md)
