---
title: Implantar e gerenciar topologias Apache Storm no Microsoft Azure HDInsight
description: Aprenda a implantar, monitorar e gerenciar topologias do Apache Storm usando o Painel do Storm no HDInsight baseado em Linux. Use as ferramentas do Hadoop para Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: ac1a4c77589f4ef88c9ee862cb871b376ca8a0fe
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67483844"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Implantar e gerenciar topologias Apache Storm no Microsoft Azure HDInsight 

Neste documento, conheça as noções básicas de gerenciamento e monitoramento de topologias [Apache Storm](https://storm.apache.org/) em execução no Storm em clusters HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Storm no HDInsight. Ver [criar Apache clusters Hadoop usando o portal do Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) e selecione **Storm** para **tipo de Cluster**.


* (Opcional) Familiaridade com SSH e SCP: Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) Visual Studio: SDK do Azure 2.5.1 ou mais recente e as Ferramentas do Data Lake para Visual Studio. Para obter mais informações, consulte [Introdução ao uso das Ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Uma das seguintes versões do Visual Studio:

  * Visual Studio 2012 com Atualização 4

  * Visual Studio 2013 com Atualização 4 ou [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (qualquer edição)

  * Visual Studio 2017 (qualquer edição). As Ferramentas do Data Lake para Visual Studio 2017 são instaladas como parte da Carga de Trabalho do Azure.

## <a name="submit-a-topology-visual-studio"></a>Envie uma topologia: Visual Studio

As Ferramentas do HDInsight podem ser usadas para enviar topologias C# ou híbridas para seu cluster do Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre como criar usando as Ferramentas do HDInsight, consulte [Desenvolver topologias em C# usando as Ferramentas do HDInsight para Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Se você ainda não instalou a última versão das Ferramentas do Data Lake para Visual Studio, consulte [Introdução ao uso das Ferramentas do Data Lake para Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Anteriormente, as Ferramentas do Data Lake para Visual Studio se chamavam Ferramentas do HDInsight para Visual Studio.
    >
    > As Ferramentas do Data Lake para Visual Studio são incluídas na __Carga de Trabalho do Azure__ do Visual Studio 2017.

2. Abra o Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados** > **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Amostra do Storm**. Na parte inferior da caixa de diálogo, digite um nome para o aplicativo.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

   > [!NOTE]  
   > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster HDInsight.

5. Selecione seu Storm no cluster HDInsight no menu suspenso **Cluster Storm** e selecione **Enviar**. Você pode monitorar se o envio teve êxito ou não usando a janela **Saída** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Envie uma topologia: Usando SSH e o comando do Storm

1. Use o SSH para conectar ao cluster HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para saber mais sobre como usar o SSH para se conectar ao cluster HDInsight, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o comando a seguir para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Esse comando inicia a topologia de WordCount de exemplo no cluster. Essa topologia gera frases aleatoriamente e conta a ocorrência de cada palavra nas frases.

   > [!NOTE]  
   > Ao enviar a topologia para o cluster, primeiro você deverá copiar o arquivo jar com o cluster antes de usar o comando `storm`. Para copiar o arquivo para o cluster, é possível usar o comando `scp`. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Enviar uma topologia: de forma programática

É possível implantar programaticamente uma topologia usando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicativo Java que demonstra como implantar e iniciar uma topologia por meio do serviço Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitore e gerencie: Visual Studio

Quando uma topologia for enviada usando o Visual Studio, a exibição **Topologias do Storm** será mostrada. Selecione a topologia da lista para exibir informações sobre a topologia em execução.

![monitor do visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> Você também pode exibir **Topologias Storm** no **Gerenciador de Servidores** expandindo **Azure** > **HDInsight** e clicando com o botão direito do mouse em um Storm no cluster do HDInsight e selecionando **Exibir Topologias Storm**.

Escolha a forma dos spouts ou bolts para exibir informações sobre esses componentes. Uma nova janela será aberta para cada item selecionado.

### <a name="deactivate-and-reactivate"></a>Desativar e reativar

A desativação de uma topologia pausa até que seja interrompido ou reativado. Para executar essas operações, use os botões __Desativar__ e __Reativar__ na parte superior do __Resumo da Topologia__.

### <a name="rebalance"></a>Rebalanceamento

Rebalancear uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você tiver redimensionado o cluster para adicionar mais anotações, o rebalanceamento permitirá que uma topologia veja os novos nós.

Para redistribuir uma topologia, use o botão __Redistribuir__ na parte superior do __Resumo da Topologia__.

> [!WARNING]  
> Rebalancear uma topologia primeiro desativa a topologia, em seguida, redistribui os trabalhos uniformemente no cluster e finalmente retorna a topologia para o estado que estava antes do rebalanceamento. Portanto, se a topologia estava ativa, ela ficará ativa novamente. Se ela foi desativada, ela permanecerá desativada.

### <a name="kill-a-topology"></a>Encerrar uma topologia

As topologias Storm continuarão em execução até serem paradas ou até que o cluster seja excluído. Para interromper uma topologia, use o botão __Encerrar__ na parte superior do __Resumo da Topologia__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitore e gerencie: Usando SSH e o comando do Storm

O `storm` utilitário permite que você trabalhe com as topologias de execução na linha de comando. Use `storm -h` para uma lista completa de comandos.

### <a name="list-topologies"></a>Topologias de lista

Use o comando a seguir para listar todas as topologias em execução:

    storm list

Esse comando retorna informações semelhantes ao seguinte texto:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Desativar e reativar

A desativação de uma topologia pausa até que seja interrompido ou reativado. Use o seguinte comando para desativar e reativar:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Eliminar uma topologia em execução

As topologias Storm, depois de iniciadas, continuarão em execução até serem interrompidas. Para interrompê-la, use o comando a seguir:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Rebalanceamento

Rebalancear uma topologia permite que o sistema revise o paralelismo da topologia. Por exemplo, se você tiver redimensionado o cluster para adicionar mais anotações, o rebalanceamento permitirá que uma topologia veja os novos nós.

> [!WARNING]  
> Rebalancear uma topologia primeiro desativa a topologia, em seguida, redistribui os trabalhos uniformemente no cluster e finalmente retorna a topologia para o estado que estava antes do rebalanceamento. Portanto, se a topologia estava ativa, ela ficará ativa novamente. Se ela foi desativada, ela permanecerá desativada.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitore e gerencie: Interface do Usuário do Storm

A IU do Storm fornece uma interface Web para trabalhar com as topologias em funcionamento, e é incluída no seu cluster HDInsight. Para exibir a interface do Storm, use um navegador da Web para abrir **https://CLUSTERNAME.azurehdinsight.net/stormui** , onde **CLUSTERNAME** é o nome do cluster.

> [!NOTE]  
> Se solicitado a forneça um nome de usuário e senha, insira o administrador de cluster (admin) e a senha que você usou ao criar o cluster.

### <a name="main-page"></a>Página principal

A página principal da interface do usuário do Storm fornece as seguintes informações:

* **Resumo do cluster**: Informações básicas sobre o cluster do Storm.
* **Resumo da topologia**: Uma lista das topologias em execução. Use os links desta seção para exibir mais informações sobre topologias específicas.
* **Resumo do Supervisor**: Informações sobre o supervisor do Storm.
* **Configuração do Nimbus**: Configuração do Nimbus para o cluster.

### <a name="topology-summary"></a>Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia:

* **Resumo da topologia**: Informações básicas sobre a topologia.
* **Ações de topologia**: Ações de gerenciamento que podem ser executadas para a topologia.

  * **Ativar**: Retoma o processamento de uma topologia desativada.
  * **Desativar**: Pausa uma topologia em execução.
  * **Redistribuir**: Ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Essa operação permite que a topologia ajuste o paralelismo para compensar o aumento ou a diminuição do número de nós no cluster.

    Para saber mais, consulte <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia do Apache Storm</a>.
  * **Encerrar**: Termina uma topologia do Storm após o tempo limite especificado.
* **Estatísticas de topologia**: Estatísticas sobre a topologia. Para definir o período de tempo para as entradas restantes na página, use os links da coluna **Janela**.
* **Spouts**: Os spouts usados pela topologia. Use os links desta seção para exibir mais informações sobre spouts específicos.
* **Bolts**: Os bolts usados pela topologia. Use os links desta seção para exibir mais informações sobre bolts específicos.
* **Configuração de topologia**: A configuração da topologia selecionada.

### <a name="spout-and-bolt-summary"></a>Resumo de Spout e Bolt

Selecionar um spout nas seções **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado:

* **Resumo do componente**: Informações básicas sobre o spout ou o bolt.
* **Estatísticas de Spout/Bolt**: Estatísticas sobre o spout ou o bolt. Para definir o período de tempo para as entradas restantes na página, use os links da coluna **Janela**.
* **Estatísticas de entrada** (somente bolt): Informações sobre os fluxos de entrada consumidos pelo bolt.
* **Estatísticas de saída**: Informações sobre os fluxos emitidos pelo spout ou pelo bolt.
* **Executores**: Informações sobre as instâncias do spout ou bolt. Selecione a entrada **Porta** gerada por um executor específico para exibir um log de informações de diagnóstico produzido para esta instância.
* **Erros**: Qualquer informação de erro para o spout ou bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitore e gerencie: API REST

A interface do usuário do Storm é criada sobre a API REST e, portanto, você pode realizar gerenciamento semelhante e monitorar a funcionalidade usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, veja [API REST da interface do usuário do Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

> [!IMPORTANT]  
> A API do REST Storm não está disponível publicamente pela Internet, e deve ser acessada usando um túnel SSH para o nó principal do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [Usar um túnel SSH para acessar a interface do usuário da Web do Apache Ambari, ResourceManager, JobHistory, NameNode, Apache Oozie e outras interfaces do usuário da Web](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

O URI de base para a API REST em clusters HDInsight baseados em Linux está disponível no nó principal em **https:\//HEADNODEFQDN:8744/api/v1/** . O nome de domínio do nó de cabeçalho é gerado durante a criação do cluster e não é estático.

Você pode encontrar o FQDN (Nome de Domínio Totalmente Qualificado) para o nó de cabeçalho do cluster de várias maneiras diferentes:

* **De uma sessão SSH**: Use o comando `headnode -f` de uma sessão SSH para o cluster.
* **Do Ambari Web**: Selecione **Serviços** na parte superior da página e, em seguida, selecione **Storm**. Na guia **Resumo** selecione **Servidor de IU do Storm**. O FQDN do nó que hospeda a interface do usuário do Storm e a API REST são exibidos na parte superior da página.
* **Da API REST do Ambari**: Use o comando `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó em que a interface do usuário do Storm e a API REST estão em execução. Substitua **CLUSTERNAME** pelo nome do cluster. Quando solicitado, insira a senha para a conta de logon (administrador). Na resposta, a entrada "host_name" contém o FQDN do nó.

### <a name="authentication"></a>Authentication

As solicitações para a API REST devem usar a **autenticação básica**e, portanto, você usará o nome do administrador e a senha do cluster HDInsight.

> [!NOTE]  
> Como a autenticação básica é enviada usando texto não criptografado, você **sempre** deverá usar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações retornadas pela API REST só podem ser usadas dentro do cluster. Por exemplo, o FQDN (nome de domínio totalmente qualificado) retornado para servidores [Apache ZooKeeper](https://zookeeper.apache.org/) não é acessível pela Internet.

## <a name="next-steps"></a>Próximas etapas

A seguir, aprenda a [Desenvolver topologias baseadas em Java usando o Apache Maven](apache-storm-develop-java-topology.md).

Para obter mais topologias de exemplo, consulte [Topologias de exemplo para Apache Storm no HDInsight](apache-storm-example-topology.md).
