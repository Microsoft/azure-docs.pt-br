---
title: Criar clusters HBase na rede virtual do Azure
description: Introdução ao uso do HBase no Azure HDInsight. Saiba como criar clusters HBase do HDInsight na rede virtual do Azure.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/23/2019
ms.openlocfilehash: a9a1788473cb31f8e78aac0bbd5979b3d681ad32
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867586"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Criar clusters do Apache HBase no HDInsight na rede virtual do Azure

Aprenda como criar clusters do Azure HDInsight Apache HBase em uma [Rede Virtual do Azure](https://azure.microsoft.com/services/virtual-network/).

Com a integração de rede virtual, os clusters Apache HBase podem ser implantados na mesma rede virtual dos seus aplicativos para que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

* Conectividade direta do aplicativo Web com os nós do cluster do HBase, que permite a comunicação usando APIs RPC (chamada de procedimento remoto) Java do HBase.
* Desempenho aprimorado, evitando que o tráfego percorra diversos gateways e balanceadores de carga.
* Capacidade de processar informações confidenciais de maneira mais segura, sem expor um ponto de extremidade público.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Criar cluster do Apache HBase na rede virtual

Nesta seção, você cria um cluster Apache HBase baseado em Linux com a conta do Armazenamento do Azure dependente em uma rede virtual do Azure usando um [modelo do Azure Resource Manager](../../azure-resource-manager/templates/deploy-powershell.md). Para outros métodos de criação de cluster e noções básicas sobre as configurações, confira [Criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar um modelo para criar clusters do Apache Hadoop no HDInsight, consulte [Criar clusters do Apache Hadoop no HDInsight usando modelos do Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Algumas propriedades foram embutidas em código no modelo. Por exemplo:
>
> * **Local**: Leste dos EUA 2
> * **Versão do cluster**: 3.6
> * **Contagem de nós de trabalho do cluster:** 2
> * **Conta de armazenamento padrão**: uma cadeia de caracteres exclusiva
> * **Nome da rede virtual**: ClusterName-vnet
> * **Espaço de endereço de rede virtual**: 10.0.0.0/16
> * **Nome da sub-rede**: subnet1
> * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
>
> `CLUSTERNAME` é substituído pelo nome do cluster que você fornece ao usar o modelo.

1. Selecione a imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado em [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Na caixa de diálogo **implantação personalizada** , selecione **Editar modelo**.

1. Na linha 165, altere o valor `Standard_A3` para `Standard_A4_V2` . Em seguida, selecione **Salvar**.

1. Conclua o modelo restante com as seguintes informações:

    |Propriedade |Valor |
    |---|---|
    |Subscription|Selecione uma assinatura do Azure usada para criar o cluster HDInsight, a conta de armazenamento dependente e a rede virtual do Azure.|
    Resource group|Selecione **Criar novo** e especifique um novo nome do grupo de recursos.|
    |Location|Selecione um local para o grupo de recursos.|
    |Nome do cluster|Insira um nome para o cluster Hadoop a ser criado.|
    |Nome de usuário e senha de logon no cluster|O nome de usuário padrão é **admin**. Forneça uma senha.|
    |Nome de usuário e senha ssh|O nome de usuário padrão é **sshuser**.  Forneça uma senha.|

    Selecione **concordo com os termos e as condições declaradas acima**.

1. Selecione **Comprar**. A criação de um cluster demora cerca de 20 minutos. Depois que o cluster for criado, você poderá selecionar o cluster no portal para abri-lo.

Depois de concluir o artigo, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Para obter instruções sobre como excluir um cluster, consulte [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Para começar a trabalhar com o novo cluster do HBase, você pode usar os procedimentos encontrados em [Introdução ao uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Conectar-se ao cluster Apache HBase usando as APIs de RPC do Apache HBase Java

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Crie uma máquina virtual IaaS (infraestrutura como serviço) na mesma rede virtual do Azure e na mesma sub-rede. Para obter instruções sobre como criar uma máquina virtual IaaS, confira [Criar uma máquina virtual executando o Windows Server](../../virtual-machines/windows/quick-create-portal.md). Ao seguir as etapas neste documento, você deve usar os seguintes valores para a configuração de Rede:

* **Rede virtual**: ClusterName-vnet
* **Sub-rede**: subnet1

> [!IMPORTANT]  
> Substitua `CLUSTERNAME` pelo nome usado ao criar o cluster HDInsight nas etapas anteriores.

Ao usar esses valores, a máquina virtual é colocada na mesma rede virtual e na mesma sub-rede que o cluster HDInsight. Essa configuração permite que eles se comuniquem diretamente uns com os outros. Há uma maneira de criar um cluster HDInsight com um nó de borda vazio. O nó de borda pode ser usado para gerenciar o cluster.  Para saber mais, confira [Usar nós de borda vazia no HDInsight](../hdinsight-apps-use-edge-node.md).

### <a name="obtain-fully-qualified-domain-name"></a>Obter nome de domínio totalmente qualificado

Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o nome de domínio totalmente qualificado (FQDN). Para determiná-lo, é preciso obter o sufixo DNS específico da conexão do cluster do HBase. Para fazer isso, é possível usar um dos métodos a seguir:

* Use um navegador da Web para fazer uma chamada do [Apache Ambari](https://ambari.apache.org/):

    Navegue até `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts?minimal_response=true`. Ele retorna um arquivo JSON com os sufixos DNS.

* Use o site do Ambari:

    1. Navegue até `https://CLUSTERNAME.azurehdinsight.net`.
    2. Selecione **hosts** no menu superior.

* Use o Curl para fazer chamadas REST:

    ```bash
    curl -u <username>:<password> -k https://CLUSTERNAME.azurehdinsight.net/ambari/api/v1/clusters/CLUSTERNAME.azurehdinsight.net/services/hbase/components/hbrest
    ```

Nos dados JSON (JavaScript Object Notation) retornados, localize a entrada "host_name". Contém o FQDN para os nós no cluster. Por exemplo:

```json
"host_name" : "hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net"
```

A parte do nome do domínio que começa com o nome do cluster é o sufixo DNS. Por exemplo, `hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

### <a name="verify-communication-inside-virtual-network"></a>Verificar a comunicação dentro da rede virtual

Para verificar se a máquina virtual pode se comunicar com o cluster do HBase, use o seguinte comando `ping headnode0.<dns suffix>` por meio da máquina virtual. Por exemplo, `ping hn0-hbaseg.hjfrnszlumfuhfk4pi1guh410c.bx.internal.cloudapp.net`.

Para usar essas informações em um aplicativo Java, você pode seguir as etapas em [Use o Apache Maven para criar aplicativos Java que usem o Apache HBase com o HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) para criar um aplicativo. Para que o aplicativo se conecte a um servidor HBase remoto, modifique o arquivo **hbase-site.xml** nesse exemplo para usar o FQDN para ZooKeeper. Por exemplo:

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
</property>
```

> [!NOTE]  
> Para obter mais informações sobre a resolução de nome em redes virtuais do Azure, incluindo como usar seu próprio servidor DNS, consulte [Resolução do Nome (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu a criar um cluster do Apache HBase. Para obter mais informações, consulte:

* [Introdução ao HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Usar nós de borda vazios no HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configurar a replicação do Apache HBase no HDInsight](apache-hbase-replication.md)
* [Criar clusters do Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Introdução ao uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)
