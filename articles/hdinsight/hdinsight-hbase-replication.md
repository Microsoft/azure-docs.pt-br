---
title: "Configurar a replicação de cluster HBase nas redes virtuais do Azure | Microsoft Docs"
description: "Saiba como configurar a replicação de HBase de uma versão do HDInsight para outra para balanceamento de carga, alta disponibilidade, migração sem tempo de inatividade, atualizações e recuperação de desastre."
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/09/2017
ms.author: jgao
ms.openlocfilehash: fbd6ff573a1d4f7fe2754935dd8c199092076725
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-hbase-cluster-replication-in-azure-virtual-networks"></a>Configurar a replicação de cluster HBase nas redes virtuais do Azure

Saiba como configurar a replicação de HBase em uma rede virtual ou entre duas redes virtuais no Azure.

A replicação de cluster usa uma metodologia de envio de origem. Um cluster HBase pode ser uma fonte, um destino ou pode atender a ambas as funções de uma vez. A replicação é assíncrona. A meta da replicação é a consistência eventual. Quando a origem recebe uma edição para uma família de coluna com replicação habilitada, essa edição é propagada para todos os clusters de destino. Quando os dados são replicados de um cluster para outro, o cluster de origem e todos os clusters que já consumiram os dados são rastreados para evitar loops de replicação.

Neste tutorial, uma replicação de origem/destino será configurada. Para obter outras topologias de cluster, consulte o [Guia de referência do Apache HBase](http://hbase.apache.org/book.html#_cluster_replication).

Casos de uso de replicação de HBase para uma única rede virtual:

* Balanceamento de carga. Por exemplo, é possível executar verificações ou trabalhos MapReduce no cluster de destino e ingerir de dados no cluster de origem.
* Adicionar alta disponibilidade.
* Migrar dados de um cluster de HBase para outro.
* Atualizar um cluster HDInsight do Azure de uma versão para outra.

Casos de uso de replicação de HBase para duas redes virtuais:

* Configurar uma recuperação de desastre.
* Balanceamento de carga e particionamento do aplicativo.
* Adicionar alta disponibilidade.

É possível replicar clusters usando os scripts [ação de script](hdinsight-hadoop-customize-cluster-linux.md) do [GitHub](https://github.com/Azure/hbase-utils/tree/master/replication).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deverá ter uma assinatura do Azure. Consulte [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## <a name="set-up-the-environments"></a>Configurar os ambientes

Há três opções de configuração:

- Dois clusters HBase em uma rede virtual do Azure.
- Dois clusters HBase em duas redes virtuais diferentes na mesma região.
- Dois clusters HBase em duas redes virtuais diferentes em duas regiões diferentes (replicação geográfica).

Para facilitar a configuração dos ambientes, alguns [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) foram criados. Se você preferir configurar os ambientes usando outros métodos, consulte:

- [Criar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Criar clusters HBase na rede virtual do Azure](hdinsight-hbase-provision-vnet.md)

### <a name="set-up-one-virtual-network"></a>Configurar uma rede virtual

Para criar dois clusters HBase na mesma rede virtual, selecione a imagem a seguir. O modelo está armazenado em [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-one-vnet/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-one-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

### <a name="set-up-two-virtual-networks-in-the-same-region"></a>Configurar duas redes virtuais na mesma região

Para criar duas redes virtuais com emparelhamento de rede virtual e dois clusters HBase na mesma região, selecione a imagem a seguir. O modelo está armazenado em [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-two-vnets-same-region/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-two-vnets-same-region%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>



Esse cenário requer [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md). O modelo permite o emparelhamento de rede virtual.   

A replicação de HBase usa endereços IP das VMs do ZooKeeper. É necessário configurar endereços IP estáticos para os nós de destino do HBase ZooKeeper.

**Para configurar endereços IP estáticos**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione **Grupos de Recursos** no menu esquerdo.
3. Selecione o grupo de recursos que contém o cluster HBase de destino. Esse é o grupo de recursos que você especificou ao usar o modelo do Resource Manager para criar o ambiente. Use o filtro para restringir a lista. Uma lista de recursos que contém as duas redes virtuais será exibida.
4. Selecione a rede virtual que contém o cluster HBase de destino. Por exemplo, selecione **xxxx-vnet2**. Três dispositivos com nomes que começam com **nic-zookeepermode-** serão mostrados. Esses dispositivos são as três VMs do ZooKeeper.
5. Selecione uma das VMs do ZooKeeper.
6. Selecione **Configurações de IP**.
7. Na lista, selecione **ipConfig1**.
8. Selecione **Estático** e copie ou anote o endereço IP real. Você precisa do endereço IP ao executar a ação de script para habilitar a replicação.

  ![Endereço IP estático do ZooKeeper para replicação de HBase no HDInsight](./media/hdinsight-hbase-replication/hdinsight-hbase-replication-zookeeper-static-ip.png)

9. Repita a etapa 6 para definir o endereço IP estático para os outros dois nós ZooKeeper.

Para o cenário entre redes virtuais, é necessário usar a opção **-ip** ao chamar a ação de script `hdi_enable_replication.sh`.

### <a name="set-up-two-virtual-networks-in-two-different-regions"></a>Configurar duas redes virtuais em duas regiões diferentes

Para criar duas redes virtuais em duas regiões diferentes e a conexão VPN entre VNets, clique na imagem a seguir. O modelo está armazenado em [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-replication-geo%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-replication/deploy-to-azure.png" alt="Deploy to Azure"></a>

Alguns dos valores embutidos em código no modelo:

**VNet 1**

| Propriedade | Valor |
|----------|-------|
| Local | Oeste dos EUA |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet1 |
| Prefixo de espaço de endereço | 10.1.0.0/16 |
| Nome da sub-rede | Sub-rede 1 |
| Prefixo de sub-rede | 10.1.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo da sub-rede (gateway) | 10.1.255.0/27 |
| Nome do Gateway | vnet1gw |
| Tipo de gateway | Vpn |
| Tipo de VPN de gateway | RouteBased |
| SKU de gateway | Basic |
| IP do gateway | vnet1gwip |
| Nome do cluster | &lt;ClusterNamePrefix>1 |
| Versão do cluster | 3.6 |
| Tipo de cluster | hbase |
| Contagem de nós de trabalho do cluster | 2 |


**VNet 2**

| Propriedade | Valor |
|----------|-------|
| Local | Leste dos EUA |
| Nome da VNet | &lt;ClusterNamePrevix>-vnet2 |
| Prefixo de espaço de endereço | 10.2.0.0/16 |
| Nome da sub-rede | Sub-rede 1 |
| Prefixo de sub-rede | 10.2.0.0/24 |
| Nome da sub-rede (gateway) | GatewaySubnet (não pode ser alterado) |
| Prefixo da sub-rede (gateway) | 10.2.255.0/27 |
| Nome do Gateway | vnet2gw |
| Tipo de gateway | Vpn |
| Tipo de VPN de gateway | RouteBased |
| SKU de gateway | Basic |
| IP do gateway | vnet1gwip |
| Nome do cluster | &lt;ClusterNamePrefix>2 |
| Versão do cluster | 3.6 |
| Tipo de cluster | hbase |
| Contagem de nós de trabalho do cluster | 2 |

A replicação de HBase usa os endereços IP das VMs do ZooKeeper. É necessário configurar endereços IP estáticos para os nós de destino do HBase ZooKeeper. Para configurar o endereço IP estático, consulte [Configurar duas redes virtuais na mesma região](#set-up-two-virtual-networks-in-the-same-region), neste artigo.

Para o cenário entre redes virtuais, é necessário usar a opção **-ip** ao chamar a ação de script `hdi_enable_replication.sh`.

## <a name="load-test-data"></a>Carregar dados de teste

Ao replicar um cluster, é necessário especificar as tabelas a serem replicadas. Nesta seção, você carrega alguns dados no cluster de origem. Na próxima seção, você habilitará a replicação entre os dois clusters.

Para criar uma tabela de **Contatos** e inserir alguns dados na tabela, siga as instruções em [Tutorial HBase: Introdução ao uso do Apache HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md).

## <a name="enable-replication"></a>Habilitar a replicação

As etapas a seguir mostram como chamar o script de ação de script no Portal do Azure. Para obter informações sobre como executar uma ação de script usando o Azure PowerShell e a ferramenta de linha de comando do Azure (CLI do Azure), consulte [Personalizar clusters HDInsight usando a ação de script](hdinsight-hadoop-customize-cluster-linux.md).

**Para habilitar a replicação de HBase no Portal do Azure**

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Abra o cluster HBase de origem.
3. No menu do cluster, selecione **Ações de Script**.
4. Na parte superior da página, selecione **Enviar Novo**.
5. Selecione ou insira as seguintes informações:

  1. **Nome**: insira **Habilitar a replicação**.
  2. **URL do Script Bash**: insira **https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh**.
  3.  **Cabeçalho**: verifique se essa opção está selecionada. Desmarque os outros tipos de nós.
  4. **Parâmetros**: os seguintes parâmetros de exemplo habilitam a replicação de todas as tabelas existentes e copiam todos os dados do cluster de origem para o cluster de destino:

          -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -copydata
    
    >[!note]
    >
    > Use o nome do host em vez de FQDN para o nome DNS do cluster de origem e de destino.

6. Selecione **Criar**. O script pode demorar, especialmente quando o argumento **-copydata** for usado.

Argumentos necessários:

|Nome|Descrição|
|----|-----------|
|-s, --src-cluster | Especifica o nome DNS do cluster HBase de origem. Por exemplo: -s hbsrccluster, --src-cluster=hbsrccluster |
|-d, --dst-cluster | Especifica o nome DNS do cluster HBase de destino (réplica). Por exemplo: -s dsthbcluster, --src-cluster=dsthbcluster |
|-sp, --src-ambari-password | Especifica a senha de administrador para Ambari no cluster HBase de origem. |
|-dp, --dst-ambari-password | Especifica a senha de administrador para Ambari no cluster HBase de destino.|

Argumentos opcionais:

|Nome|Descrição|
|----|-----------|
|-su, --src-ambari-user | Especifica o nome de usuário administrador para Ambari no cluster HBase de origem. O valor padrão é **admin**. |
|-du, --dst-ambari-user | Especifica o nome de usuário administrador para Ambari no cluster HBase de destino. O valor padrão é **admin**. |
|-t, --table-list | Especificas as tabelas a serem replicadas. Por exemplo: --table-list="table1;table2;table3". Se você não especificar tabelas, todas as tabelas HBase existentes serão replicadas.|
|-m, --machine | Especifica o nó de cabeçalho em que a ação de script é executada. O valor é **hn1** ou **hn0**. Como o nó de cabeçalho **hn0** normalmente é mais ocupado, recomenda-se usar o **hn1**. Use essa opção quando estiver executando o script de $0 como uma ação de script do portal do HDInsight ou do Azure PowerShell.|
|-ip | Necessário quando você estiver habilitando a replicação entre duas redes virtuais. Esse argumento atua como uma opção para usar os endereços IP estáticos dos nós ZooKeeper de clusters de réplica em vez de nomes FQDN. Antes de habilitar a replicação, é necessário pré-configurar os endereços IP estáticos. |
|-cp, -copydata | Habilita a migração dos dados existentes nas tabelas em que a replicação está habilitada. |
|-rpm, -replicate-phoenix-meta | Habilita a replicação nas tabelas do sistema Phoenix. <br><br>*Use esta opção com cuidado.* É recomendável que você recrie tabelas Phoenix em clusters de réplica antes de usar esse script. |
|-h, --help | Exibe informações de uso. |

A seção `print_usage()` do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_enable_replication.sh) fornece uma explicação detalhada dos parâmetros.

Após a ação de script ser implantada com êxito, é possíve usar o SSH para se conectar ao cluster HBase de destino e verificar se os dados foram replicados.

### <a name="replication-scenarios"></a>Cenários de replicação

A lista a seguir mostra alguns casos de uso geral e suas configurações de parâmetro:

- **Habilitar a replicação em todas as tabelas entre os dois clusters**. Esse cenário não requer a cópia ou migração dos dados existentes nas tabelas e não usa tabelas Phoenix. Use os seguintes parâmetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password>  

- **Habilitar a replicação em tabelas específicas**. Use os parâmetros a seguir para habilitar a replicação em table1, table2 e table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3"

- **Habilitar a replicação em tabelas específicas e copiar os dados existentes**. Use os parâmetros a seguir para habilitar a replicação em table1, table2 e table3:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -copydata

- **Habilitar a replicação em todas as tabelas e replicar metadados Phoenix da origem para o destino**. A replicação de metadados Phoenix não é perfeita. Use-a com atenção. Use os seguintes parâmetros:

        -m hn1 -s <source cluster DNS name> -d <destination cluster DNS name> -sp <source cluster Ambari password> -dp <destination cluster Ambari password> -t "table1;table2;table3" -replicate-phoenix-meta

## <a name="copy-and-migrate-data"></a>Copiar e migrar dados

Há dois scripts de ação de script separados disponíveis para copiar ou migrar dados após a replicação ser habilitada:

- [Script para tabelas pequenas](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_copy_table.sh) (tabelas com apenas alguns gigabytes de tamanho, a cópia leva menos de uma hora para terminar)

- [Script para tabelas grandes](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/nohup_hdi_copy_table.sh) (tabelas que devem levar mais de uma hora para copiar)

É possível seguir o mesmo procedimento descrito em [Habilitar replicação](#enable-replication) para chamar a ação de script. Use os seguintes parâmetros:

    -m hn1 -t <table1:start_timestamp:end_timestamp;table2:start_timestamp:end_timestamp;...> -p <replication_peer> [-everythingTillNow]

A seção `print_usage()` do [script](https://github.com/Azure/hbase-utils/blob/master/replication/hdi_copy_table.sh) fornece uma descrição detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Copiar tabelas específicas (test1, test2 e test3) para todas as linhas editadas até o momento (carimbo de hora atual)**:

        -m hn1 -t "test1::;test2::;test3::" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow
  Ou:

        -m hn1 -t "test1::;test2::;test3::" --replication-peer="zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure" -everythingTillNow


- **Copiar tabelas específicas com intervalo de tempo especificado**:

        -m hn1 -t "table1:0:452256397;table2:14141444:452256397" -p "zk5-hbrpl2;zk1-hbrpl2;zk5-hbrpl2:2181:/hbase-unsecure"


## <a name="disable-replication"></a>Desabilitar a replicação

Para desabilitar a replicação, use outro script de ação de script do [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh). É possível seguir o mesmo procedimento descrito em [Habilitar replicação](#enable-replication) para chamar a ação de script. Use os seguintes parâmetros:

    -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> <-all|-t "table1;table2;...">  

A seção `print_usage()` do [script](https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_disable_replication.sh) fornece uma explicação detalhada dos parâmetros.

### <a name="scenarios"></a>Cenários

- **Desabilitar a replicação em todas as tabelas**:

        -m hn1 -s <source cluster DNS name> -sp Mypassword\!789 -all
  ou o

        --src-cluster=<source cluster DNS name> --dst-cluster=<destination cluster DNS name> --src-ambari-user=<source cluster Ambari user name> --src-ambari-password=<source cluster Ambari password>

- **Desabilitar a replicação em tabelas específicas (table1, table2 e table3)**:

        -m hn1 -s <source cluster DNS name> -sp <source cluster Ambari password> -t "table1;table2;table3"

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a configurar a replicação de HBase em uma rede virtual ou entre duas redes virtuais. Para saber mais sobre HDInsight e HBase, consulte estes artigos:

* [Introdução ao Apache HBase no HDInsight][hdinsight-hbase-get-started]
* [Visão geral do HDInsight HBase][hdinsight-hbase-overview]
* [Criar clusters HBase na rede virtual do Azure][hdinsight-hbase-provision-vnet]
* [Analisar dados de sensor com o Storm e o HBase no HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started-linux.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
