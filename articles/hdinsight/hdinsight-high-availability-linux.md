---
title: Alta disponibilidade para Hadoop – Azure HDInsight
description: Saiba como clusters HDInsight melhoram a confiabilidade e a disponibilidade usando um nó principal adicional. Saiba como isso afeta os serviços do Hadoop, como o Ambari e o Hive, e também como se conectar individualmente com cada nó principal usando SSH.
ms.reviewer: jasonh
author: hrasheed-msft
keywords: alta disponibilidade hadoop
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 596b53d468a7dfc719c16dc6e6339492381d7f41
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763800"
---
# <a name="availability-and-reliability-of-apache-hadoop-clusters-in-hdinsight"></a>Disponibilidade e confiabilidade dos clusters Apache Hadoop em HDInsight

Os clusters HDInsight fornecem dois nós de cabeçalho para aumentar a disponibilidade e a confiabilidade dos serviços e trabalhos do Apache Hadoop em execução.

O Hadoop atinge a alta disponibilidade e confiabilidade replicando serviços e dados em múltiplos nós em um cluster. No entanto, em geral, as distribuições padrão do Hadoop têm apenas um único nó de cabeçalho. Qualquer falha do único nó de cabeçalho poderá fazer com que o cluster pare de funcionar. O HDInsight fornece dois nós principais para melhorar a disponibilidade e a confiabilidade do Hadoop.

[!INCLUDE [windows-retirement-notice](../../includes/windows-retirement-notice.md)]

## <a name="availability-and-reliability-of-nodes"></a>Disponibilidade e confiabilidade dos nós

Os nós em um cluster HDInsight são implementados com o uso de Máquinas Virtuais do Azure. As seções a seguir abordam os tipos de nós individuais usados com o HDInsight. 

> [!NOTE]  
> Nem todos os tipos de nó são usados para um tipo de cluster. Por exemplo, um tipo de cluster Hadoop não tem nenhum nó Nimbus. Para obter mais informações sobre os nós usados pelos tipos de cluster HDInsight, veja a seção “Tipos de cluster” do documento [Criar clusters Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

### <a name="head-nodes"></a>Nós de cabeçalho

Para garantir a alta disponibilidade dos serviços do Hadoop, o HDInsight oferece dois nós principais. Ambos os nós de cabeçalho estão ativos e em execução no cluster HDInsight simultaneamente. Alguns serviços, como Apache HDFS ou Apache Hadoop YARN, só estão “ativos” em um nó de cabeçalho a qualquer momento. Outros serviços, como HiveServer2 ou MetaStore Hive estão ativos em ambos os nós de cabeçalho ao mesmo tempo.

Os nós de cabeçalho (e outros nós no HDInsight) tem um valor numérico como parte do nome do host do nó. Por exemplo, `hn0-CLUSTERNAME` ou `hn4-CLUSTERNAME`.

> [!IMPORTANT]  
> Não associa o valor numérico com a possibilidade de um nó ser primário ou secundário. O valor numérico está presente apenas para fornecer um nome exclusivo para cada nó.

### <a name="nimbus-nodes"></a>Nós Nimbus

Nós Nimbus estão disponíveis nos clusters Apache Storm. Os nós Nimbus fornecem funcionalidade semelhante ao JobTracker do Hadoop, distribuindo e monitorando o processamento nos nós de trabalho. O HDInsight oferece dois nós Nimbus para clusters Storm

### <a name="apache-zookeeper-nodes"></a>Nós do Apache ZooKeeper 3.4.6

Os nós [ZooKeeper](https://zookeeper.apache.org/) são usados para eleição de líder de serviços mestres em nós principais. Eles também são usados para garantir que os serviços, nós de dados (trabalho) e gateways saibam em qual nó principal um serviço mestre está ativo. Por padrão, o HDInsight fornece três nós do ZooKeeper.

### <a name="worker-nodes"></a>Nós de trabalho

Os nós de trabalho executam a análise de dados real quando um trabalho é enviado para o cluster. Se um nó de trabalho falhar, a tarefa que ele estava executando será enviada para outro nó de trabalho. Por padrão, o HDInsight cria quatro nós de trabalho. Você pode alterar esse número para atender às suas necessidades, durante e após a criação do cluster.

### <a name="edge-node"></a>Nó de borda

Um nó de borda não participa ativamente na análise de dados dentro do cluster. Ele é usado por desenvolvedores ou cientistas de dados ao trabalhar com o Hadoop. O nó de borda reside na mesma Rede Virtual do Azure que os outros nós no cluster e pode acessar diretamente todos os outros nós. O nó de borda pode ser usado sem retirar recursos dos serviços críticos do Hadoop ou de trabalhos de análise.

Atualmente, o ML Services no HDInsight é o único tipo de cluster que fornece um nó de borda por padrão. Para o ML Services no HDInsight, o nó de borda é usado para testar o código R localmente no nó antes de enviá-lo ao cluster para o processamento distribuído.

Para obter informações sobre como usar um nó de borda com outros tipos de cluster, consulte o documento [Usar nós de borda no HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="accessing-the-nodes"></a>Acessando os nós

O acesso ao cluster pela internet é fornecido por meio de um gateway público. O acesso é limitado à conexão com os nós principais e (se houver) do nó de borda. O acesso aos serviços em execução em nós principais não é afetado por ter vários nós de cabeça. O gateway público encaminha solicitações ao nó principal que hospeda o serviço solicitado. Por exemplo, se, no momento, o Apache Ambari estiver hospedado no nó de cabeçalho secundário, o gateway encaminhará solicitações de entrada para o Ambari nesse nó.

O acesso por meio do gateway público é limitado à porta 443 (HTTPS), 22 e 23.

* A porta __443__ é usada para acessar a interface do usuário do Ambari ou de outras interfaces da Web ou APIs REST hospedadas em nós de cabeça.

* A porta __22__ é usada para acessar o nó principal primária ou o nó de borda com o SSH.

* A porta __23__ é usada para acessar o nó principal secundário com SSH. Por exemplo, o `ssh username@mycluster-ssh.azurehdinsight.net` se conectará ao nó principal de cabeçalho do cluster chamado **mycluster**.

Para saber mais sobre como usar SSH, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="internal-fully-qualified-domain-names-fqdn"></a>Nomes internos de domínio totalmente qualificado (FQDN)

Os nós em um cluster HDInsight possuem um endereço IP interno e o FQDN que só pode ser acessado do cluster. Ao acessar serviços em cluster usando o endereço IP ou FQDN interno, você deve usar Ambari para verificar o IP ou FQDN para usar quando acessar o serviço.

Por exemplo, o serviço de Apache Oozie pode ser executado somente em um nó de cabeçalho e usar o comando `oozie` de uma sessão SSH requer a URL para o serviço. Essa URL pode ser recuperada do Ambari usando o seguinte comando:

    curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Esse comando retorna um valor semelhante ao seguinte comando, que contém a URL interna para usar com o comando `oozie`:

    "oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

Para saber mais sobre como trabalhar com a API REST do Ambari, consulte [Monitorar e gerenciar o HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

### <a name="accessing-other-node-types"></a>Acessando outros tipos de nó

Você pode se conectar a nós que não estão diretamente acessíveis pela Internet usando os métodos a seguir:

* **SSH**: Assim que estiver conectado a um nó de cabeçalho usando o SSH, você poderá usar o SSH por meio do nó de cabeçalho para se conectar aos outros nós no cluster. Para saber mais, consulte o documento [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* **Túnel SSH**: Caso precise acessar um serviço Web hospedado em um dos nós que não esteja exposto à Internet, será necessário usar um túnel SSH. Para saber mais, consulte o documento [Usar túnel SSH com HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

* **Rede Virtual do Azure**: Caso o cluster HDInsight faça parte de uma Rede Virtual do Azure, qualquer recurso contido na mesma Rede Virtual poderá acessar diretamente todos os nós no cluster. Para saber mais, confira o documento [Estender o HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

## <a name="how-to-check-on-a-service-status"></a>Como verificar o status do serviço

A interface do usuário da Web do Ambari ou a API REST do Ambari pode ser usada para verificar o status dos serviços executados nos nós de cabeçalho.

### <a name="ambari-web-ui"></a>Interface do usuário da Ambari Web

A interface do usuário da Ambari Web é visível em https://CLUSTERNAME.azurehdinsight.net. Substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira as credenciais do usuário HTTP para o cluster. O nome de usuário padrão HTTP é **admin** e a senha é a senha que você digitou ao criar o cluster.

Ao chegar na página Ambari, os serviços instalados serão listados à esquerda da página.

![Serviços instalados](./media/hdinsight-high-availability-linux/services.png)

Há uma série de ícones que podem aparecer ao lado de um serviço para indicar o status. Todos os alertas relacionados a um serviço podem ser visualizados usando o link **Alertas** na parte superior da página.  Ambari oferece vários alertas predefinidos.

Os seguintes alertas ajudam a monitorar a disponibilidade de um cluster:

| Nome do alerta                               | DESCRIÇÃO                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Status do Monitor de métrica                    | Esse alerta indica o status do processo de Monitor de métricas, conforme determinado pelo script de status do monitor.                                                                                   |
| Pulsação do agente do Ambari                   | Este alerta é disparado se o servidor perdeu contato com um agente.                                                                                                                        |
| Processo de servidor do zooKeeper                 | Este alerta de nível de host é disparado se o processo do servidor ZooKeeper não puder ser determinado esteja ativo e ouvindo na rede.                                                               |
| Status do servidor IOCache metadados           | Esse alerta de nível de host é disparado se o servidor de metadados IOCache não puder ser determinado como backup e respondendo a solicitações de cliente                                                            |
| Interface do usuário da Web JournalNode                       | Este alerta de nível de host é disparado se a interface do usuário da Web de JournalNode está inacessível.                                                                                                                 |
| Servidor Thrift Spark2                     | Este alerta de nível de host é disparado se o servidor de Thrift Spark2 não puder ser determinado devem estar ativos.                                                                                                |
| Processo do servidor de histórico                   | Este alerta de nível de host é disparado se o processo do servidor de histórico não pode ser estabelecido esteja ativo e escuta na rede.                                                                |
| Interface do usuário de Web de servidor de histórico                    | Este alerta de nível de host é disparado se a interface do usuário do histórico de servidor Web está inacessível.                                                                                                              |
| Interface do usuário da Web de ResourceManager                   | Este alerta de nível de host é disparado se a interface do usuário do ResourceManager Web está inacessível.                                                                                                             |
| Resumo de integridade do NodeManager               | Esse nível de serviço de alerta é disparada se não houver NodeManagers não íntegro                                                                                                                    |
| Aplicativo Web de linha do tempo da interface do usuário                      | Esse alerta de nível de host será acionada se a IU da Web do aplicativo de linha do tempo do servidor está inacessível.                                                                                                         |
| Resumo de integridade DataNode                  | Esse nível de serviço de alerta é disparada se não houver DataNodes não íntegro                                                                                                                       |
| Interface do usuário da Web NameNode                          | Este alerta de nível de host é disparado se a interface do usuário do NameNode na Web está inacessível.                                                                                                                    |
| Processo de Failover de controlador do zooKeeper    | Este alerta de nível de host é disparado se o processo do controlador de Failover do ZooKeeper não pode ser confirmada esteja ativo e escuta na rede.                                                   |
| Interface do usuário da Web de servidor Oozie                      | Este alerta de nível de host é disparado se o servidor Web da interface do usuário do Oozie está inacessível.                                                                                                                |
| Oozie Server Status                      | Este alerta de nível de host é disparado se o servidor Oozie não puder ser determinado como backup e respondendo às solicitações do cliente.                                                                      |
| Processo de Metastore do hive                   | Este alerta de nível de host é disparado se o processo de Metastore do Hive não puder ser determinado esteja ativo e ouvindo na rede.                                                                 |
| Processo do HiveServer2                      | Este alerta de nível de host é disparado se o HiveServer não puder ser determinado como backup e respondendo às solicitações do cliente.                                                                        |
| Status do servidor WebHCat                    | Este alerta de nível de host é disparado se o status do servidor templeton não está íntegro.                                                                                                            |
| Porcentagem de ZooKeeper servidores disponíveis      | Este alerta é disparado se o número de servidores do ZooKeeper no cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo do ZooKeeper.     |
| Spark2 Livy Server                       | Este alerta de nível de host é disparado se o servidor Livy2 não puder ser determinado devem estar ativos.                                                                                                        |
| Servidor de histórico de Spark2                    | Este alerta de nível de host é disparado se o servidor de histórico Spark2 não puder ser determinado devem estar ativos.                                                                                               |
| Processo do coletor de métricas                | Este alerta é disparado se o coletor de métricas não pode ser confirmada esteja ativo e ouvindo na porta configurada para o número de segundos iguais ao limite.                                 |
| Coletor de métricas - HBase Master processo | Este alerta é disparado se processos de mestre de HBase do coletor de métricas não podem ser confirmada esteja ativo e escuta na rede para o limite crítico configurado, fornecido em segundos. |
| Monitores de porcentagem de métricas disponíveis       | Este alerta é disparado se um percentual do Monitor de métricas de processos não estão funcionando e escuta na rede para os limites críticos e de aviso configurado.                             |
| Porcentagem NodeManagers disponíveis           | Este alerta é disparado se o número de baixo NodeManagers no cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo do NodeManager.        |
| Integridade do NodeManager                       | Esse alerta de nível de host verifica a propriedade de integridade de nó disponível do componente NodeManager.                                                                                              |
| IU da Web do NodeManager                       | Este alerta de nível de host é disparado se a IU da Web do NodeManager está inacessível.                                                                                                                 |
| Integridade da disponibilidade alta NameNode        | Esse nível de serviço de alerta é disparada se o Active Directory NameNode ou NameNode em espera não estiver executando.                                                                                     |
| Processo de DataNode                         | Este alerta de nível de host é disparado se os processos de DataNode individuais não podem ser estabelecida esteja ativo e ouvindo na rede.                                                         |
| Interface do usuário da Web DataNode                          | Este alerta de nível de host é disparado se a interface do usuário da Web de DataNode está inacessível.                                                                                                                    |
| Porcentagem JournalNodes disponíveis           | Este alerta é disparado se o número de baixo JournalNodes do cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo JournalNode.        |
| Porcentagem DataNodes disponíveis              | Este alerta é disparado se o número de baixo DataNodes do cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo DataNode.              |
| Status do servidor Zeppelin                   | Este alerta de nível de host é disparado se o servidor do Zeppelin não puder ser determinado como backup e respondendo às solicitações do cliente.                                                                   |
| Processo interativo do HiveServer2          | Este alerta de nível de host é disparado se o HiveServerInteractive não puder ser determinado como backup e respondendo às solicitações do cliente.                                                             |
| Aplicativo LLAP                         | Este alerta é disparado se o aplicativo LLAP não puder ser determinado esteja operante e respondendo a solicitações.                                                                                    |

Você pode selecionar cada serviço para exibir mais informações sobre ele.

A página de serviço fornece informações sobre o status e a configuração de cada serviço, mas não fornece informações sobre o nó de cabeçalho no qual o serviço está em execução. Para exibir essas informações, use o link **Hosts** na parte superior da página. Isso exibe os hosts do cluster, incluindo os nós de cabeçalho.

![lista de hosts](./media/hdinsight-high-availability-linux/hosts.png)

Selecionar o link para um dos nós de cabeçalho exibirá os serviços e componentes em execução nesse nó.

![Status do componente](./media/hdinsight-high-availability-linux/nodeservices.png)

Para saber mais sobre como usar o Ambari, confira [Monitorar e gerenciar o HDInsight usando a interface de usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md).

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

Para saber mais sobre como trabalhar com a API REST do Ambari, consulte [Monitorar e gerenciar o HDInsight usando a API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md).

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

Semelhante ao uso de um cliente SSH, ao se conectar com o cluster, é necessário fornecer o nome de conta de usuário SSH e o endereço SSH do cluster. Por exemplo, `sftp username@mycluster-ssh.azurehdinsight.net`. Forneça a senha da conta quando solicitado ou uma chave pública usando o parâmetro `-i`.

Depois de conectado, você verá um prompt `sftp>` . Neste prompt, é possível alterar os diretórios, além de carregar e baixar arquivos. Por exemplo, os seguintes comandos alteram os diretórios para o diretório **/var/log/hadoop/hdfs** e baixam todos os arquivos no diretório em seguida.

    cd /var/log/hadoop/hdfs
    get *

Para obter uma lista dos comandos disponíveis, insira `help` no prompt `sftp>`.

> [!NOTE]  
> Há também interfaces gráficas que permitem visualizar o sistema de arquivos quando você estiver conectado usando SFTP. Por exemplo, [MobaXTerm](https://mobaxterm.mobatek.net/) permite que você procure o sistema de arquivos usando uma interface semelhante à do Windows Explorer.

### <a name="ambari"></a>Ambari

> [!NOTE]  
> Para acessar os arquivos de log usando o Ambari, use um túnel SSH. As interfaces da web para os serviços individuais não são expostas publicamente na Internet. Para saber mais sobre como usar um túnel SSH, veja o documento [Usar túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).

Na interface de usuário da Web do Ambari, selecione o serviço do qual você deseja exibir os logs (por exemplo, YARN). Em seguida, use os **Links Rápidos** a fim de selecionar para qual nó de cabeçalho exibir os logs.

![Usando links rápidos para exibir logs](./media/hdinsight-high-availability-linux/viewlogs.png)

## <a name="how-to-configure-the-node-size"></a>Como configurar o tamanho do nó

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

O tamanho de um nó só pode ser selecionado durante a criação do cluster. Você pode encontrar uma lista de diferentes tamanhos de VM disponíveis para o HDInsight na [página de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Ao criar um cluster, você pode especificar o tamanho dos nós. As informações a seguir fornecem orientação sobre como especificar o tamanho usando o [portal do Azure][preview-portal], o [Azure PowerShell][azure-powershell] e a [CLI Clássica do Azure][azure-cli]:

* **Portal do Azure**: Ao criar um cluster, você pode definir o tamanho dos nós usados pelo cluster:

    ![Imagem do Assistente de criação de cluster com a seleção de tamanho do nó](./media/hdinsight-high-availability-linux/headnodesize.png)

* **CLI Clássica do Azure**: Ao usar o comando `azure hdinsight cluster create`, é possível definir o tamanho dos nós de cabeçalho, de trabalho e do ZooKeeper usando os parâmetros `--headNodeSize`, `--workerNodeSize` e `--zookeeperNodeSize`.

* **Azure PowerShell**: Ao usar o cmdlet `New-AzHDInsightCluster`, é possível definir o tamanho dos nós de cabeçalho, de trabalho e do ZooKeeper usando os parâmetros `-HeadNodeVMSize`, `-WorkerNodeSize` e `-ZookeeperNodeSize`.

## <a name="next-steps"></a>Próximas etapas

Use os links a seguir para saber mais sobre os tópicos mencionados neste documento.

* [Referência REST do Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
* [Instalar e configurar a CLI Clássica do Azure](../cli-install-nodejs.md)
* [Instalar e configurar o PowerShell do Azure](/powershell/azure/overview)
* [Gerenciar clusters HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Provisionar os clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: /powershell/azureps-cmdlets-docs
[azure-cli]: ../cli-install-nodejs.md
