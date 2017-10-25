---
title: "Monitorar clusters Hadoop no HDInsight usando a API do Ambari – Azure | Microsoft Docs"
description: Use as APIs do Apache Ambari para criar, gerenciar e monitorar clusters do Hadoop. As APIs e ferramentas intuitivas do operador do ocultam a complexidade do Hadoop.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
editor: cgronlun
manager: jhubbard
ms.assetid: 052135b3-d497-4acc-92ff-71cee49356ff
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: b6fc2098027690eb76b69b1427f0e9541b8a7a69
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-hadoop-clusters-in-hdinsight-using-the-ambari-api"></a>Monitorar clusters Hadoop no HDInsight usando a API da Ambari
Saiba como monitorar os clusters HDInsight por meio de APIs do Ambari.

> [!NOTE]
> As informações neste artigo são principalmente para clusters do HDInsight baseados no Windows, que fornecem uma versão somente leitura da API REST do Ambari. Para clusters baseados em Linux, consulte [Gerenciar clusters do Hadoop usando Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="what-is-ambari"></a>O que é Ambari?
O [Apache Ambari][ambari-home] é usado para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Para obter mais informações sobre APIs, consulte [Referência da API do Ambari][ambari-api-reference]. 

Atualmente, o HDInsight dá suporte apenas ao recurso de monitoramento da Ambari. A API da Ambari v1.0 tem suporte pelos clusters HDInsight versões 3.0 e 2.1. Este artigo aborda o acesso às APIs da Ambari em clusters do HDInsight versões 3.1 e 2.1. A principal diferença entre os dois é que alguns dos componentes foram alterados com a introdução de novos recursos (como o Servidor de histórico de trabalho). 

**Pré-requisitos**

Antes de começar este tutorial, você deve ter os seguintes itens:

* **Uma estação de trabalho com o PowerShell do Azure**.
* (Opcional) [cURL][curl]. Para instalá-lo, consulte [Downloads e versões de cURL][curl-download].
  
  > [!NOTE]
  > Quando usar o comando cURL no Windows, use aspas duplas em vez de aspas simples para os valores de opção.
  > 
  > 
* **Um cluster Azure HDInsight**. Para obter informações sobre como provisionar um cluster, consulte [Introdução ao HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision]. Você precisará dos seguintes dados para percorrer o tutorial:
  
  | Propriedade do cluster | Nome de variável do PowerShell do Azure | Valor | Descrição |
  | --- | --- | --- | --- |
  |   Nome do cluster HDInsight |$clusterName | |O nome do seu cluster HDInsight. |
  |   Nome de usuário do cluster |$clusterUsername | |Nome de usuário do cluster especificado quando o cluster foi criado. |
  |   Senha do cluster |$clusterPassword | |Senha do usuário do cluster. |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Início rápido
Há várias maneiras de usar a Ambari para monitorar os clusters HDInsight.

**Usar o Azure PowerShell**

O script do Azure PowerShell a seguir obtém as informações do rastreador de trabalho MapReduce *em um cluster HDInsight 3.5.*  A principal diferença é que esses detalhes agora serão extraídos do serviço YARN (e não do MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

O script do PowerShell a seguir obtém as informações do rastreador de trabalho MapReduce *em um cluster HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

A saída é:

![Saída de jobtracker][img-jobtracker-output]

**Usar cURL**

Veja seguir um exemplo para obter informações sobre clusters usando cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

A saída é:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Para o lançamento de 8/10/2014**:

Ao usar o ponto de extremidade da Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", o campo *host_name* retorna o nome de domínio totalmente qualificado (FQDN) do nó em vez de apenas o nome do host. Antes do lançamento de 8/10/2014, este exemplo retornava simplesmente "**headnode0**". Após o lançamento de 8/10/2014, o FQDN “**headnode0.{ClusterDNS}.azurehdinsight.net**” passou a ser retornado como mostrado no exemplo acima. Essa alteração foi necessária para facilitar cenários em que vários tipos de cluster, como HBase e Hadoop, podem ser implantados em uma VNET (rede virtual). Isso acontece, por exemplo, ao usar HBase como uma plataforma de back-end para o Hadoop.

## <a name="ambari-monitoring-apis"></a>APIs de monitoramento da Ambari
A tabela a seguir lista algumas das chamadas de API para monitoramento da Ambari. Para obter mais informações sobre a API, consulte [Referência de API da Ambari][ambari-api-reference].

| Monitorar a chamada de API | URI | Descrição |
| --- | --- | --- |
| Obter clusters |`/api/v1/clusters` | |
| Obter informações do cluster. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |clusters, serviços, hosts |
| Obter serviços |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |O serviços incluem: hdfs, mapreduce |
| Obter informações dos serviços. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Obter componentes do serviço |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Obter informações do componente. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, host-components, métricas |
| Obter hosts |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Obter informações do host. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Obter componentes do host |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Obter informações de componente do host. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, componente, host, métrica |
| Obter configurações |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Tipos de configuração: core-site, hdfs-site, mapred-site, hive-site |
| Obter informações de configuração. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Tipos de configuração: core-site, hdfs-site, mapred-site, hive-site |

## <a name="next-steps"></a>Próximas etapas
Você aprendeu como usar as chamadas de API para monitoramento da Ambari. Para obter mais informações, consulte:

* [Gerenciar clusters HDInsight usando o Portal do Azure][hdinsight-admin-portal]
* [Gerenciar clusters HDInsight usando o Azure PowerShell][hdinsight-admin-powershell]
* [Gerenciar clusters HDInsight usando a interface de linha de comando][hdinsight-admin-cli]
* [Documentação do HDInsight][hdinsight-documentation]
* [Introdução ao HDInsight][hdinsight-get-started]

[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
