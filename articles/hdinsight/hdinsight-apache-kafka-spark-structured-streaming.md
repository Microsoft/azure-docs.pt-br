---
title: "Streaming Estruturado do Apache Spark com Kafka – Azure HDInsight | Microsoft Docs"
description: "Saiba como usar o streaming do Apache Spark (DStream) para transmitir dados para dentro ou fora do Apache Kafka. Neste exemplo, você deve transmitir dados usando um bloco de anotações do Jupyter do Spark no HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: larryfr
ms.openlocfilehash: 565f840e0ac7ec1a68361ac1613da5d42459c979
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-spark-structured-streaming-with-kafka-preview-on-hdinsight"></a>Use o Streaming Estruturado do Spark com o Kafka (versão prévia) no HDInsight

Saiba como usar o Streaming Estruturado do Spark para ler dados do Apache Kafka no Azure HDInsight.

O streaming estruturado do Spark é um mecanismo de processamento de fluxo baseado no Spark SQL. Ele permite expressar cálculos de streaming do mesmo modo que cálculos de lote em dados estáticos. Para obter mais informações sobre Streaming Estruturado, consulte o [Guia de Programação de Streaming Estruturado [Versão Alfa]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) em Apache.org.

> [!IMPORTANT]
> Este exemplo usou o Spark 2.1 no HDInsight 3.6. O Streaming Estruturado é considerado __versão alfa__ no Spark 2.1.
>
> As etapas neste documento criam um grupo de recursos do Azure que contém um Spark no HDInsight e um Kafka no cluster de HDInsight. Esses clusters são ambos localizados em uma Rede Virtual do Azure, que permite que o cluster Spark se comunique diretamente com o cluster Kafka.
>
> Quando você terminar as etapas neste documento, lembre-se de excluir os clusters para evitar cobranças em excesso.

## <a name="create-the-clusters"></a>Criar os clusters

Apache Kafka no HDInsight não fornece acesso para Agentes de Kafka pela internet pública. Qualquer coisa que se comunique com Kafka deve estar na mesma rede virtual do Azure que os nós no cluster Kafka. Para este exemplo, clusters de Spark e Kafka estão localizados em uma rede virtual do Azure. O diagrama a seguir mostra como a comunicação flui entre os clusters:

![Diagrama de clusters Spark e Kafka em uma rede virtual do Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> O serviço Kafka é limitado a comunicação dentro da rede virtual. Outros serviços no cluster, como SSH e Ambari, podem ser acessados pela Internet. Para obter mais informações sobre as portas públicas disponíveis com o HDInsight, consulte [portas e URIs usados pelo HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Enquanto você pode criar uma rede virtual do Azure, Kafka e clusters de Spark manualmente, é mais fácil usar um modelo do Azure Resource Manager. Use as seguintes etapas para implantar uma rede virtual do Azure, Kafka e clusters de Spark para sua assinatura do Azure.

1. Use o botão a seguir para entrar no Azure e abra o modelo do Gerenciador de Recursos no portal do Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo do Azure Resource Manager está localizado em **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    Este modelo cria os seguintes recursos:

    * Um cluster Kafka no HDInsight 3.5.
    * Um cluster Spark no HDInsight 3.6.
    * Uma Rede Virtual do Azure, que contém os clusters HDInsight.

    > [!IMPORTANT]
    > O bloco de anotações de streaming estruturado usado neste exemplo requer o Spark no HDInsight 3.6. Se você usar uma versão anterior do Spark no HDInsight, você receberá erros ao usar o bloco de anotações.

2. Use as seguintes informações para preencher as entradas na seção **Implantação personalizada**:
   
    ![Implantação personalizada do HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: Crie um grupo ou selecione um existente. Esse grupo contém o cluster HDInsight.

    * **Local**: escolha um local geograficamente perto de você.

    * **Nome do Cluster de base**: esse valor é usado como o nome de base para os clusters Spark e Kafka. Por exemplo, inserir **hdi** cria um cluster Spark chamado hdi__ spark e um cluster Kafka chamado **kafka hdi**.

    * **Nome de usuário de logon do cluster**: O nome de usuário do administrador para os clusters Spark e Kafka.

    * **Senha de logon do cluster**: A senha de usuário do administrador para os clusters Spark e Kafka.

    * **Nome de usuário SSH**: O usuário SSH para criar para os clusters Spark e Kafka.

    * **Senha SSH**: a senha para o usuário SSH dos clusters Kafka e Spark.

3. Leia **Termos e Condições**, e depois selecione **Concordo com os termos e condições declarados acima**.

4. Por fim, marque **Fixar no painel** e selecione **Comprar**. Demora cerca de 20 minutos para criar os clusters.

Depois que os recursos tiverem sido criados, uma página de resumo será exibida.

![Informações de grupo de recursos para a VNet e os clusters](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Observe que os nomes dos clusters HDInsight são **spark-BASENAME** e **kafka-BASENAME**, em que BASENAME é o nome fornecido para o modelo. Você usa esses nomes em etapas posteriores ao se conectar aos clusters.

## <a name="get-the-kafka-brokers"></a>Obter os agentes de Kafka

O código neste exemplo conecta-se aos hosts de agente Kafka no cluster do Kafka. Para localizar os endereço dos dois hosts de agente Kafka, use o exemplo do PowerShell ou Bash a seguir:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Quando solicitado, insira a senha para a conta de logon do cluster (admin)

> [!NOTE]
> Este exemplo espera `$CLUSTERNAME` para conter o nome do cluster Kafka.
>
> Este exemplo usa o utilitário [jq](https://stedolan.github.io/jq/) para analisar dados fora do documento JSON.

A saída é semelhante ao texto a seguir:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Salve essas informações, pois elas são usadas nas diversas etapas deste documento.

## <a name="get-the-notebooks"></a>Obter os blocos de anotações

O código do exemplo descrito neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Carregar os blocos de anotações

Use as etapas a seguir para carregar os blocos de anotações do projeto para o Spark no cluster HDInsight:

1. No navegador da Web, conecte-se ao bloco de anotações do Jupyter em seu cluster Spark. Na URL a seguir, substitua `CLUSTERNAME` pelo nome do cluster Kafka:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Quando solicitado, insira o nome de usuário e senha que você inseriu ao criar o cluster.

2. No canto superior direito da página, use o botão __Carregar__ para carregar o arquivo __Stream-Tweets-To_Kafka.ipynb__ no cluster. Selecione __Abrir__ para iniciar o upload.

    ![Use o botão Carregar para selecionar e carregar um notebook](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Selecione o arquivo KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. Localize a entrada __Stream-Tweets-To_Kafka.ipynb__ na lista de blocos de anotações e selecione o botão __Carregar__ ao lado dela.

    ![Para carregar o notebook, use o botão carregar da entrada KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)

4. Repita as etapas de 1 a 3 para carregar o bloco de anotações __Spark-Structured-Streaming-From-Kafka.ipynb__.

## <a name="load-tweets-into-kafka"></a>Carregar tweets no Kafka

Depois que o arquivo foi carregado, selecione a entrada __Stream-Tweets-To_Kafka.ipynb__ para abrir o bloco de anotações. Siga as etapas no bloco de anotações para carregar tweets no Kafka.

## <a name="process-tweets-using-spark-structured-streaming"></a>Processar tweets usando o streaming estruturado do Spark

Na home page do Jupyter Notebook, selecione a entrada __Spark-Structured-Streaming-From-Kafka.ipynb__. Siga as etapas no bloco de anotações para carregar tweets de Kafka usando o Streaming Estruturado do Spark.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a usar o Streaming Estruturado do Spark, consulte os documentos a seguir para saber mais sobre como trabalhar com Spark e Kafka:

* [Como usar o streaming do Spark (DStream) com Kafka](hdinsight-apache-spark-with-kafka.md).
* [Iniciar com o bloco de anotações do Jupyter e Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md)