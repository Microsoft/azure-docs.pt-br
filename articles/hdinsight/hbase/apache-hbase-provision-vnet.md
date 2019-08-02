---
title: Criar clusters HBase na rede virtual do Azure
description: Introdução ao uso do HBase no Azure HDInsight. Saiba como criar clusters HBase do HDInsight na rede virtual do Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: hrasheed
ms.openlocfilehash: 9d81e5e69837f6074d94278f4e54f9178a656335
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67433782"
---
# <a name="create-apache-hbase-clusters-on-hdinsight-in-azure-virtual-network"></a>Criar clusters do Apache HBase no HDInsight na rede virtual do Azure
Saiba como criar clusters Azure HDInsight Apache HBase em uma [rede Virtual do Azure][1].

Com a integração de rede virtual, os clusters Apache HBase podem ser implantados na mesma rede virtual dos seus aplicativos para que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

* Conectividade direta do aplicativo Web com os nós do cluster do HBase, que permite a comunicação usando APIs RPC (chamada de procedimento remoto) Java do HBase.
* Desempenho aprimorado, evitando que o tráfego percorra diversos gateways e balanceadores de carga.
* Capacidade de processar informações confidenciais de maneira mais segura, sem expor um ponto de extremidade público.

### <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter os seguintes itens:

* **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Uma estação de trabalho com o PowerShell do Azure.** Consulte [Instalar e usar o PowerShell do Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-apache-hbase-cluster-into-virtual-network"></a>Criar cluster do Apache HBase na rede virtual
Nesta seção, você cria um cluster Apache HBase baseado em Linux com a conta do Armazenamento do Azure dependente em uma rede virtual do Azure usando um [modelo do Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md). Para outros métodos de criação de cluster e noções básicas sobre as configurações, confira [Criar clusters do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Para obter mais informações sobre como usar um modelo para criar clusters do Apache Hadoop no HDInsight, consulte [Criar clusters do Apache Hadoop no HDInsight usando modelos do Azure Resource Manager](../hdinsight-hadoop-create-linux-clusters-arm-templates.md)

> [!NOTE]  
> Algumas propriedades foram embutidas em código no modelo. Por exemplo:
>
> * **Local**: Leste dos EUA 2
> * **Versão do cluster**: 3.6
> * **Contagem de nós de trabalho do cluster**: 2
> * **Conta de armazenamento padrão**: uma cadeia de caracteres exclusiva
> * **Nome da rede virtual**: &lt;Nome do cluster>-vnet
> * **Espaço de endereço da rede virtual**: 10.0.0.0/16
> * **Nome da sub-rede**: subnet1
> * **Intervalo de endereços da sub-rede**: 10.0.0.0/24
>
> &lt;Nome do cluster > é substituído pelo nome do cluster que você fornecer ao usar o modelo.
>
>

1. Clique na imagem a seguir para abrir o modelo no portal do Azure. O modelo está localizado em [modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Na folha **Implantação personalizada**, insira as seguintes propriedades:

   * **Assinatura**: Selecione uma assinatura do Azure usada para criar o cluster HDInsight, a conta de armazenamento dependente e a rede virtual do Azure.
   * **Grupo de recursos**: Selecione **Criar novo** e especifique um novo nome do grupo de recursos.
   * **Local**: Selecione um local para o grupo de recursos.
   * **ClusterName**: Insira um nome para o cluster Hadoop a ser criado.
   * **Nome e senha de logon do cluster**: O nome padrão de logon é **admin**.
   * **Nome de usuário e senha SSH**: O nome de usuário padrão é **sshuser**.  Você pode renomeá-lo.
   * **Concordo com os termos e condições declarados acima**: (Selecionar)
3. Clique em **Comprar**. A criação de um cluster demora cerca de 20 minutos. Após a criação do cluster, você pode clicar na folha do cluster no portal para abri-la.

Depois de concluir o artigo, você talvez queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Para obter instruções sobre como excluir um cluster, consulte [Gerenciar clusters do Apache Hadoop no HDInsight usando o portal do Azure](../hdinsight-administer-use-portal-linux.md#delete-clusters).

Para começar a trabalhar com o novo cluster do HBase, você pode usar os procedimentos encontrados em [Introdução ao uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md).

## <a name="connect-to-the-apache-hbase-cluster-using-apache-hbase-java-rpc-apis"></a>Conectar-se ao cluster Apache HBase usando as APIs de RPC do Apache HBase Java
1. Crie uma máquina virtual IaaS (infraestrutura como serviço) na mesma rede virtual do Azure e na mesma sub-rede. Para obter instruções sobre como criar uma máquina virtual IaaS, confira [Criar uma máquina virtual executando o Windows Server](../../virtual-machines/windows/quick-create-portal.md). Ao seguir as etapas neste documento, você deve usar os seguintes valores para a configuração de Rede:

   * **Rede virtual**: &lt;Nome do cluster>-vnet
   * **Sub-rede**: subnet1

   > [!IMPORTANT]  
   > Substitua &lt;Nome do cluster > pelo nome que você usou ao criar o cluster HDInsight nas etapas anteriores.

   Ao usar esses valores, a máquina virtual é colocada na mesma rede virtual e na mesma sub-rede que o cluster HDInsight. Essa configuração permite que eles se comuniquem diretamente uns com os outros. Há uma maneira de criar um cluster HDInsight com um nó de borda vazio. O nó de borda pode ser usado para gerenciar o cluster.  Para saber mais, confira [Usar nós de borda vazia no HDInsight](../hdinsight-apps-use-edge-node.md).

2. Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o nome de domínio totalmente qualificado (FQDN). Para determiná-lo, é preciso obter o sufixo DNS específico da conexão do cluster do HBase. Para fazer isso, é possível usar um dos métodos a seguir:

   * Use um navegador da Web para fazer uma chamada do [Apache Ambari](https://ambari.apache.org/):

     Navegue até https://&lt;ClusterName>.azurehdinsight.net/api/v1/clusters/&lt;ClusterName>/hosts?minimal_response=true. Ele encontra um arquivo JSON com os sufixos DNS.
   * Use o site do Ambari:

     1. Navegue até https://&lt;ClusterName>.azurehdinsight.net.
     2. Clique em **Hosts** no menu superior.
   * Use o Curl para fazer chamadas REST:

     ```bash
        curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest
     ```

     Nos dados JSON (JavaScript Object Notation) retornados, localize a entrada "host_name". Contém o FQDN para os nós no cluster. Por exemplo:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     A parte do nome do domínio que começa com o nome do cluster é o sufixo DNS. Por exemplo, mycluster.b1.cloudapp.net.
   * Usar PowerShell do Azure

     Use o seguinte script do Azure PowerShell para registrar a função **Get-ClusterDetail** , que pode ser usada para retornar o sufixo DNS:

     ```powershell
        function Get-ClusterDetail(
            [String]
            [Parameter( Position=0, Mandatory=$true )]
            $ClusterDnsName,
            [String]
            [Parameter( Position=1, Mandatory=$true )]
            $Username,
            [String]
            [Parameter( Position=2, Mandatory=$true )]
            $Password,
            [String]
            [Parameter( Position=3, Mandatory=$true )]
            $PropertyName
            )
        {
        <#
            .SYNOPSIS
            Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
            .Description
            This command shows the following 4 properties of an HDInsight cluster:
            1. ZookeeperQuorum (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.quorum".
            2. ZookeeperClientPort (supports only HBase type cluster)
                Shows the value of HBase property "hbase.zookeeper.property.clientPort".
            3. HBaseRestServers (supports only HBase type cluster)
                Shows a list of host FQDNs that run the HBase REST server.
            4. FQDNSuffix (supports all cluster types)
                Shows the FQDN suffix of hosts in the cluster.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
            This command shows the value of HBase property "hbase.zookeeper.quorum".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
            This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
            This command shows a list of host FQDNs that run the HBase REST server.
            .EXAMPLE
            Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
            This command shows the FQDN suffix of hosts in the cluster.
        #>

            $DnsSuffix = ".azurehdinsight.net"

            $ClusterFQDN = $ClusterDnsName + $DnsSuffix
            $webclient = new-object System.Net.WebClient
            $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

            if($PropertyName -eq "ZookeeperQuorum")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
            }
            if($PropertyName -eq "ZookeeperClientPort")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
            }
            if($PropertyName -eq "HBaseRestServers")
            {
                $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                $Response1 = $webclient.DownloadString($Url1)
                $JsonObject1 = $Response1 | ConvertFrom-Json
                $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                $Response2 = $webclient.DownloadString($Url2)
                $JsonObject2 = $Response2 | ConvertFrom-Json
                foreach ($host_component in $JsonObject2.host_components)
                {
                    $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                    Write-host $ConnectionString
                }
            }
            if($PropertyName -eq "FQDNSuffix")
            {
                $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                $Response = $webclient.DownloadString($Url)
                $JsonObject = $Response | ConvertFrom-Json
                $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                $pos = $FQDN.IndexOf(".")
                $Suffix = $FQDN.Substring($pos + 1)
                Write-host $Suffix
            }
        }
     ```

     Após executar o script do PowerShell do Azure, use o seguinte comando para retornar o sufixo DNS usando a função **Get-ClusterDetail** . Especifique o nome do cluster do HBase do HDInsight, o nome e a senha do administrador ao usar esse comando.

     ```powershell
        Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>
     ```

     Esse comando retorna o sufixo DNS. Por exemplo, **yourclustername.b4.internal.cloudapp.net**.


<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix](./media/apache-hbase-provision-vnet/PrimaryDNSSuffix.png)
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Para verificar se a máquina virtual pode se comunicar com o cluster do HBase, use o seguinte comando `ping headnode0.<dns suffix>` por meio da máquina virtual. Por exemplo, envie ping a headnode0.mycluster.b1.cloudapp.net.

Para usar essas informações em um aplicativo Java, você pode seguir as etapas em [Use o Apache Maven para criar aplicativos Java que usem o Apache HBase com o HDInsight (Hadoop)](./apache-hbase-build-java-maven-linux.md) para criar um aplicativo. Para que o aplicativo se conecte a um servidor HBase remoto, modifique o arquivo **hbase-site.xml** nesse exemplo para usar o FQDN para ZooKeeper. Por exemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]  
> Para obter mais informações sobre a resolução de nome em redes virtuais do Azure, incluindo como usar seu próprio servidor DNS, consulte [Resolução do Nome (DNS)](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu como criar um cluster do Apache HBase. Para obter mais informações, consulte:

* [Introdução ao HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Usar nós de borda vazios no HDInsight](../hdinsight-apps-use-edge-node.md)
* [Configurar a replicação do Apache HBase no HDInsight](apache-hbase-replication.md)
* [Criar clusters do Apache Hadoop no HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [Introdução ao uso do Apache HBase com o Apache Hadoop no HDInsight](./apache-hbase-tutorial-get-started-linux.md)
* [Visão geral da Rede Virtual](../../virtual-network/virtual-networks-overview.md)

[1]: https://azure.microsoft.com/services/virtual-network/
[2]: https://technet.microsoft.com/library/ee176961.aspx
[3]: https://technet.microsoft.com/library/hh847889.aspx

