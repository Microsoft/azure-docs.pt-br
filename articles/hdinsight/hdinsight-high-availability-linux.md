---
title: "Alta disponibilidade para Hadoop – Azure HDInsight | Microsoft Docs"
description: "Saiba como clusters HDInsight melhoram a confiabilidade e a disponibilidade usando um nó principal adicional. Saiba como isso afeta os serviços do Hadoop, como o Ambari e o Hive, e também como se conectar individualmente com cada nó principal usando SSH."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
keywords: alta disponibilidade hadoop
ms.assetid: 99c9f59c-cf6b-4529-99d1-bf060435e8d4
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/19/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 56dd07de81e322aa66587ee160beb734811285dc
ms.contentlocale: pt-br
ms.lasthandoff: 07/28/2017

---
# <a name="availability-and-reliability-of-hadoop-clusters-in-hdinsight"></a>Disponibilidade e confiabilidade dos clusters Hadoop em HDInsight

Os clusters HDInsight fornecem dois nós de cabeçalho para aumentar a disponibilidade e a confiabilidade dos serviços e trabalhos do Hadoop em execução.

O Hadoop atinge a alta disponibilidade e confiabilidade replicando serviços e dados em múltiplos nós em um cluster. No entanto, em geral, as distribuições padrão do Hadoop têm apenas um único nó de cabeçalho. Qualquer falha do único nó de cabeçalho poderá fazer com que o cluster pare de funcionar. O HDInsight fornece dois nós principais para melhorar a disponibilidade e a confiabilidade do Hadoop.

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="availability-and-reliability-of-nodes"></a>Disponibilidade e confiabilidade dos nós

Os nós em um cluster HDInsight são implementados com o uso de Máquinas Virtuais do Azure. As seções a seguir abordam os tipos de nós individuais usados com o HDInsight. 

> [!NOTE]
> Nem todos os tipos de nó são usados para um tipo de cluster. Por exemplo, um tipo de cluster Hadoop não tem nenhum nó Nimbus. Para obter mais informações sobre os nós usados pelos tipos de cluster HDInsight, veja a seção “Tipos de cluster” do documento [Criar clusters Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

### <a name="head-nodes"></a>Nós de cabeçalho

Para garantir a alta disponibilidade dos serviços do Hadoop, o HDInsight oferece dois nós principais. Ambos os nós de cabeçalho estão ativos e em execução no cluster HDInsight simultaneamente. Alguns serviços, como HDFS ou YARN, só estão “ativos” em um nó de cabeçalho a qualquer momento. Outros serviços, como HiveServer2 ou MetaStore Hive estão ativos em ambos os nós de cabeçalho ao mesmo tempo.

Os nós de cabeçalho (e outros nós no HDInsight) tem um valor numérico como parte do nome do host do nó. Por exemplo, `hn0-CLUSTERNAME` ou `hn4-CLUSTERNAME`.

> [!IMPORTANT]
> Não associa o valor numérico com a possibilidade de um nó ser primário ou secundário. O valor numérico está presente apenas para fornecer um nome exclusivo para cada nó.

### <a name="nimbus-nodes"></a>Nós Nimbus

Nós Nimbus estão disponíveis nos clusters Storm. Os nós Nimbus fornecem funcionalidade semelhante ao JobTracker do Hadoop, distribuindo e monitorando o processamento nos nós de trabalho. O HDInsight oferece dois nós Nimbus para clusters Storm

### <a name="zookeeper-nodes"></a>Nós do Zookeeper

Os nós [ZooKeeper](http://zookeeper.apache.org/) são usados para eleição de líder de serviços mestres em nós principais. Eles também são usados para garantir que os serviços, nós de dados (trabalho) e gateways saibam em qual nó principal um serviço mestre está ativo. Por padrão, o HDInsight fornece três nós do ZooKeeper.

### <a name="worker-nodes"></a>Nós de trabalho

Os nós de trabalho executam a análise de dados real quando um trabalho é enviado para o cluster. Se um nó de trabalho falhar, a tarefa que ele estava executando será enviada para outro nó de trabalho. Por padrão, o HDInsight cria quatro nós de trabalho. Você pode alterar esse número para atender às suas necessidades, durante e após a criação do cluster.

### <a name="edge-node"></a>Nó de borda

Um nó de borda não participa ativamente na análise de dados dentro do cluster. Ele é usado por desenvolvedores ou cientistas de dados ao trabalhar com o Hadoop. O nó de borda reside na mesma Rede Virtual do Azure que os outros nós no cluster e pode acessar diretamente todos os outros nós. O nó de borda pode ser usado sem retirar recursos dos serviços críticos do Hadoop ou de trabalhos de análise.

Atualmente, o Servidor R no HDInsight é o único tipo de cluster que fornece um nó de borda por padrão. Para o Servidor R no HDInsight, o nó de borda é usado para testar o código R localmente no nó antes de enviá-lo ao cluster para o processamento distribuído.

Para saber mais sobre como usar um nó de borda com tipos de cluster diferentes do Servidor de R, consulte o documento [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="accessing-the-nodes"></a>Acessando os nós

O acesso ao cluster pela internet é fornecido por meio de um gateway público. O acesso é limitado à conexão com os nós principais e (se houver) do nó de borda. O acesso aos serviços em execução em nós principais não é afetado por ter vários nós de cabeça. O gateway público encaminha solicitações ao nó principal que hospeda o serviço solicitado. Por exemplo, se, no momento, o Ambari estiver hospedado no nó de cabeçalho secundário, o gateway encaminhará solicitações de entrada para o Ambari nesse nó.

O acesso por meio do gateway público é limitado à porta 443 (HTTPS), 22 e 23.

* A porta __443__ é usada para acessar a interface do usuário do Ambari ou de outras interfaces da Web ou APIs REST hospedadas em nós de cabeça.

* A porta __22__ é usada para acessar o nó principal primária ou o nó de borda com o SSH.

* A porta __23__ é usada para acessar o nó principal secundário com SSH. Por exemplo, o `ssh username@mycluster-ssh.azurehdinsight.net` se conectará ao nó principal de cabeçalho do cluster chamado **mycluster**.

Para saber mais sobre como usar SSH, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Nomes internos de domínio totalmente qualificado (FQDN)

Os nós em um cluster HDInsight possuem um endereço IP interno e o FQDN que só pode ser acessado do cluster. Ao acessar serviços em cluster usando o endereço IP ou FQDN interno, você deve usar Ambari para verificar o IP ou FQDN para usar quando acessar o serviço.

Por exemplo, o serviço de Oozie pode ser executado somente em um nó de cabeçalho e usar o comando `oozie` de uma sessão SSH requer a URL para o serviço. Essa URL pode ser recuperada do Ambari usando o seguinte comando:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Esse comando retorna um valor semelhante ao seguinte comando, que contém a URL interna para usar com o comando `oozie`:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Para saber mais sobre como trabalhar com a API REST do Ambari, consulte [Monitorar e gerenciar o HDInsight usando a API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Acessando outros tipos de nó

Você pode se conectar a nós que não estão diretamente acessíveis pela Internet usando os métodos a seguir:

* **SSH**: assim que estiver conectado a um nó de cabeçalho usando o SSH, você poderá usar o SSH por meio do nó de cabeçalho para se conectar aos outros nós no cluster. Para saber mais, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Túnel SSH**: caso precise acessar um serviço Web hospedado em um dos nós que não esteja exposto à Internet, será necessário usar um túnel SSH. Para saber mais, consulte o documento [Usar túnel SSH com HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

* **Rede Virtual do Azure**: caso o cluster HDInsight faça parte de uma Rede Virtual do Azure, qualquer recurso contido na mesma Rede Virtual poderá acessar diretamente todos os nós no cluster. Para saber mais, confira o documento [Estender o HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="how-to-check-on-a-service-status"></a>Como verificar o status do serviço

A interface do usuário da Web do Ambari ou a API REST do Ambari pode ser usada para verificar o status dos serviços executados nos nós de cabeçalho.

### <a name="ambari-web-ui"></a>Interface do usuário da Ambari Web

Abra a interface do usuário da Web do Ambari em https://CLUSTERNAME.azurehdinsight.net. Substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira as credenciais do usuário HTTP para o cluster. O nome de usuário padrão HTTP é **admin** e a senha é a senha que você digitou ao criar o cluster.

Ao chegar na página Ambari, os serviços instalados serão listados à esquerda da página.

![Serviços instalados](./media/hdinsight-high-availability-linux/services.png)

Há uma série de ícones que podem aparecer ao lado de um serviço para indicar o status. Todos os alertas relacionados a um serviço podem ser visualizados usando o link **Alertas** na parte superior da página. Você pode selecionar cada serviço para exibir mais informações sobre ele.

A página de serviço fornece informações sobre o status e a configuração de cada serviço, mas não fornece informações sobre o nó de cabeçalho no qual o serviço está em execução. Para exibir essas informações, use o link **Hosts** na parte superior da página. Isso exibe os hosts do cluster, incluindo os nós de cabeçalho.

![lista de hosts](./media/hdinsight-high-availability-linux/hosts.png)

Selecionar o link para um dos nós de cabeçalho exibirá os serviços e componentes em execução nesse nó.

![Status do componente](./media/hdinsight-high-availability-linux/nodeservices.png)

Para saber mais sobre como usar o Ambari, confira [Monitorar e gerenciar o HDInsight usando a interface de usuário da Web do Ambari](hdinsight-hadoop-manage-ambari.md).

### <a name="ambari-rest-api"></a>API REST do Ambari

A API REST do Ambari está disponível pela internet. O gateway público HDInsight manipula as solicitações de roteamento para o nó de cabeçalho que hospeda a API REST.

Você pode usar o seguinte comando para verificar o estado de um serviço por meio da API de REST do Ambari:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Substitua a **SENHA** pela senha da conta do usuário HTTP (administrador).
* Substitua **CLUSTERNAME** pelo nome do cluster.
* Substitua o **SERVICENAME** pelo nome do serviço desejado para verificar o status.

Por exemplo, para verificar o status do serviço **HDFS** em um cluster chamado **mycluster**, por uma senha de **senha**, você usaria o seguinte comando:

    curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

A resposta é semelhante ao JSON a seguir:

    {
      "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
      "ServiceInfo" : {
        "cluster_name" : "mycluster",
        "service_name" : "HDFS",
        "state" : "STARTED"
      }
    }

A URL indica que o serviço está sendo executado no **hn0-CLUSTERNAME**.

O estado indica que o serviço está sendo executado ou **INICIADO**.

Se você não souber quais serviços estão instalados no cluster, use o seguinte comando para recuperar uma lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

Para saber mais sobre como trabalhar com a API REST do Ambari, consulte [Monitorar e gerenciar o HDInsight usando a API REST do Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

#### <a name="service-components"></a>Componentes do serviço

Serviços podem conter componentes os quais você deseja verificar o status de individualmente. Por exemplo, o HDFS contém o componente NameNode. Para exibir informações sobre um componente, o comando seria:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Se você não souber quais componentes estão instalados no cluster, você pode usar o seguinte comando para recuperar uma lista:

    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

## <a name="how-to-access-log-files-on-the-head-nodes"></a>Como acessar arquivos de log nos nós de cabeçalho

### <a name="ssh"></a>SSH

Enquanto estiver conectado a um nó de cabeçalho por meio do SSH, os arquivos de log podem ser encontrados em **/var/log**. Por exemplo, **/var/log/hadoop-yarn/yarn** contêm logs para YARN.

Cada nó de cabeçalho pode ter entradas de log exclusivo, portanto você deve verificar os logs em ambos.

### <a name="sftp"></a>SFTP

Também é possível se conectar ao nó de cabeçalho usando o Protocolo FTP do SSH ou SFTP e baixar os arquivos de log diretamente.

Semelhante ao uso de um cliente SSH, ao se conectar com o cluster, é necessário fornecer o nome de conta de usuário SSH e o endereço SSH do cluster. Por exemplo: `sftp username@mycluster-ssh.azurehdinsight.net`. Forneça a senha da conta quando solicitado ou uma chave pública usando o parâmetro `-i`.

Depois de conectado, você verá um prompt `sftp>` . Neste prompt, é possível alterar os diretórios, além de carregar e baixar arquivos. Por exemplo, os seguintes comandos alteram os diretórios para o diretório **/var/log/hadoop/hdfs** e baixam todos os arquivos no diretório em seguida.

    cd /var/log/hadoop/hdfs
    get *

Para obter uma lista dos comandos disponíveis, insira `help` no prompt `sftp>`.

> [!NOTE]
> Há também interfaces gráficas que permitem visualizar o sistema de arquivos quando você estiver conectado usando SFTP. Por exemplo, [MobaXTerm](http://mobaxterm.mobatek.net/) permite que você procure o sistema de arquivos usando uma interface semelhante à do Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]
> Para acessar os arquivos de log usando o Ambari, use um túnel SSH. As interfaces da web para os serviços individuais não são expostas publicamente na Internet. Para saber mais sobre como usar um túnel SSH, veja o documento [Usar túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

Na interface de usuário da Web do Ambari, selecione o serviço do qual você deseja exibir os logs (por exemplo, YARN). Em seguida, use os **Links Rápidos** a fim de selecionar para qual nó de cabeçalho exibir os logs.

![Usando links rápidos para exibir logs](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Como configurar o tamanho do nó

O tamanho de um nó só pode ser selecionado durante a criação do cluster. Você pode encontrar uma lista de diferentes tamanhos de VM disponíveis para o HDInsight na [página de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Ao criar um cluster, você pode especificar o tamanho dos nós. As informações a seguir fornecem orientação sobre como especificar o tamanho usando o [portal do Azure][preview-portal], o [Azure PowerShell][azure-powershell] e a [CLI do Azure][azure-cli]:

* **Portal do Azure**: ao criar um cluster, você pode definir o tamanho dos nós usados pelo cluster:

    ![Imagem do Assistente de criação de cluster com a seleção de tamanho do nó](./media/hdinsight-high-availability-linux/headnodesize.png)

* **CLI do Azure**: ao usar o comando `azure hdinsight cluster create`, é possível definir o tamanho dos nós de cabeçalho, de trabalho e do ZooKeeper usando os parâmetros `--headNodeSize`, `--workerNodeSize` e `--zookeeperNodeSize`.

* **Azure PowerShell**: ao usar o `New-AzureRmHDInsightCluster` cmdlet , é possível definir o tamanho dos nós de cabeçalho, de trabalho e do ZooKeeper usando os parâmetros `-HeadNodeVMSize`, `-WorkerNodeSize` e `-ZookeeperNodeSize`.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para saber mais sobre os tópicos mencionados neste documento.

* [Referência REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalar e configurar a CLI do Azure.](../cli-install-nodejs.md)
* [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview)
* [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)
* [Provisionar os clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md

