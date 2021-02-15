---
title: Streaming do Apache Spark com Apache Kafka – Azure HDInsight
description: Saiba como usar o Apache Spark para transmitir dados dentro ou fora do Apache Kafka usando o DStreams. Neste exemplo, você transmite dados usando um Jupyter Notebook do Spark no HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d14b96843b489b28fc7d83348e39638272c06da5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942758"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Exemplo de streaming do Apache Spark (DStream) com o Apache Kafka no HDInsight

Saiba como usar [Apache Spark](https://spark.apache.org/) para transmitir dados para dentro ou para fora do [Apache Kafka](https://kafka.apache.org/) no HDInsight usando o [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Este exemplo usa um [Jupyter Notebook](https://jupyter.org/) que é executado no cluster do Spark.

> [!NOTE]  
> As etapas neste documento criam um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
>
> Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

> [!IMPORTANT]  
> Este exemplo usa DStreams, que é uma tecnologia de streaming do Spark mais antiga. Para obter um exemplo que utiliza recursos de streaming do Spark mais recentes, consulte o documento [Streaming estruturado do Spark com Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Criar os clusters

O Apache Kafka no HDInsight não fornece acesso para agentes de Kafka pela Internet pública. Qualquer coisa que se comunique com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, clusters de Spark e Kafka estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Embora Kafka em si esteja limitado a comunicação na rede virtual, outros serviços do cluster, como o SSH e Ambari podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual do Azure, Kafka e clusters de Spark manualmente, é mais fácil usar um modelo do Azure Resource Manager. Use as seguintes etapas para implantar uma rede virtual do Azure, Kafka e clusters de Spark para sua assinatura do Azure.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Para garantir a disponibilidade do Kafka no HDInsight, o cluster deve conter pelo menos três nós de trabalho. Este modelo cria um cluster de Kafka que contém três nós de trabalho.

    Este modelo cria um cluster HDInsight 3.6 para Kafka e Spark.

1. Use as seguintes informações para preencher as entradas na seção **Implantação personalizada**:

    |Propriedade |Valor |
    |---|---|
    |Resource group|Crie um grupo ou selecione um existente.|
    |Location|Escolha um local geograficamente perto de você.|
    |Nome do cluster de base|Esse valor é usado como o nome de base dos clusters Spark e Kafka. Por exemplo, a inserção de **hdistreaming** cria um cluster Spark chamado __spark-hdistreaming__ e um cluster Kafka chamado **kafka-hdistreaming**.|
    |Nome de usuário de logon do cluster|O nome do usuário administrador dos clusters Spark e Kafka.|
    |Senha de logon do cluster|A senha do usuário administrador dos clusters Spark e Kafka.|
    |Nome de usuário do SSH|O usuário SSH para criar para os clusters Spark e Kafka.|
    |Senha SSH|A senha para o usuário SSH dos clusters Kafka e Spark.|

    ![Parâmetros de implantação personalizada do HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

1. Por fim, selecione **Comprar**. Demora cerca de 20 minutos para criar os clusters.

Depois que os recursos tiverem sido criados, uma página de resumo será exibida.

![Resumo de grupo de recursos para a rede virtual e clusters](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Observe que os nomes dos clusters HDInsight são **spark-BASENAME** e **kafka-BASENAME**, em que BASENAME é o nome fornecido para o modelo. Você usa esses nomes em etapas posteriores ao se conectar aos clusters.

## <a name="use-the-notebooks"></a>Usar os blocos de anotações

O código para o exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>Excluir o cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como as etapas neste documento criam ambos os clusters no mesmo grupo de recursos do Azure, você pode excluir o grupo de recursos no portal do Azure. Excluir o grupo remove todos os recursos criados por este documento, a rede Virtual do Azure e a conta de armazenamento usada pelos clusters.

## <a name="next-steps"></a>Próximas etapas

Neste exemplo, você aprendeu a usar o Spark para ler e gravar em Kafka. Use os links a seguir para descobrir outras maneiras de trabalhar com Kafka:

* [Introdução ao Apache Kafka no HDInsight](kafka/apache-kafka-get-started.md)
* [Use MirrorMaker para criar uma réplica de Apache Kafka no HDInsight](kafka/apache-kafka-mirroring.md)
* [Use o Apache Storm com o Apache Kafka no HDInsight](hdinsight-apache-storm-with-kafka.md)
