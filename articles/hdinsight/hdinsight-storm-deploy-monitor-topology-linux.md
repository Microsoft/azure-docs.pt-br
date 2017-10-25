---
title: Implantar e gerenciar topologias Apache Storm no HDInsight baseado em Linux | Microsoft Docs
description: Aprenda a implantar, monitorar e gerenciar topologias do Apache Storm usando o Painel do Storm no HDInsight baseado em Linux. Use as ferramentas do Hadoop para Visual Studio.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/07/2017
ms.author: larryfr
ms.openlocfilehash: 218e818f48adee0b4e7ecb0b184098a9e3273afd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Implantar e gerenciar topologias Apache Storm no HDInsight

Neste documento, conheça as noções básicas de gerenciamento e monitoramento de topologias Storm em execução no Storm em clusters HDInsight.

> [!IMPORTANT]
> As etapas deste artigo exigem um Storm baseado em Linux no cluster HDInsight. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 
>
> Para obter informações sobre as topologias de implantação e monitoramento em HDInsight baseado em Windows, consulte [Implantar e gerenciar topologias do Apache Storm no HDInsight baseado em Windows](hdinsight-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Pré-requisitos

* **Storm baseado em Linux no cluster HDInsight**: consulte [Introdução com o Apache Storm no HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md) para etapas para criar um cluster

* (Opcional) **Familiaridade com SSH e SCP**: para saber mais, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcional) **Visual Studio**: SDK do Azure 2.5.1 ou mais novo e as Ferramentas do Data Lake para Visual Studio. Para obter mais informações, consulte [Introdução ao uso das Ferramentas do Data Lake para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

    Uma das seguintes versões do Visual Studio:

  * Visual Studio 2012 com [Atualização 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 com [Atualização 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (qualquer edição)

  * Visual Studio 2017 (qualquer edição). As Ferramentas do Data Lake para Visual Studio 2017 são instaladas como parte da Carga de Trabalho do Azure.

## <a name="submit-a-topology-visual-studio"></a>Enviar uma topologia: Visual Studio

As Ferramentas do HDInsight podem ser usadas para enviar topologias C# ou híbridas para seu cluster do Storm. As etapas a seguir usam um aplicativo de exemplo. Para obter informações sobre como criar usando as Ferramentas do HDInsight, consulte [Desenvolver topologias em C# usando as Ferramentas do HDInsight para Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

1. Se você ainda não instalou a última versão das Ferramentas do Data Lake para Visual Studio, consulte [Introdução ao uso das Ferramentas do Data Lake para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Anteriormente, as Ferramentas do Data Lake para Visual Studio se chamavam Ferramentas do HDInsight para Visual Studio.
    >
    > As Ferramentas do Data Lake para Visual Studio são incluídas na __Carga de Trabalho do Azure__ do Visual Studio 2017.

2. Abra o Visual Studio, selecione **Arquivo** > **Novo** > **Projeto**.

3. Na caixa de diálogo **Novo Projeto**, expanda **Instalados** > **Modelos** e selecione **HDInsight**. Na lista de modelos, selecione **Amostra do Storm**. Na parte inferior da caixa de diálogo, digite um nome para o aplicativo.

    ![imagem](./media/hdinsight-storm-deploy-monitor-topology-linux/sample.png)

4. No **Gerenciador de Soluções**, clique com o botão direito do mouse no projeto e selecione **Enviar para o Storm no HDInsight**.

   > [!NOTE]
   > Se solicitado, insira as credenciais de logon para sua assinatura do Azure. Se você tiver mais de uma assinatura, faça o logon naquela que contém seu Storm no cluster HDInsight.

5. Selecione seu Storm no cluster HDInsight no menu suspenso **Cluster Storm** e selecione **Enviar**. Você pode monitorar se o envio teve êxito ou não usando a janela **Saída** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Enviar uma topologia: SSH e o comando do Storm

1. Use o SSH para conectar ao cluster HDInsight. Substitua o **USERNAME** pelo nome do seu logon SSH. Substitua o **CLUSTERNAME** pelo nome do seu cluster HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Para saber mais sobre como usar o SSH para se conectar ao cluster HDInsight, confira [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o comando a seguir para iniciar uma topologia de exemplo:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    Esse comando inicia a topologia de WordCount de exemplo no cluster. Essa topologia gera frases aleatoriamente e conta a ocorrência de cada palavra nas frases.

   > [!NOTE]
   > Ao enviar a topologia para o cluster, primeiro você deverá copiar o arquivo jar com o cluster antes de usar o comando `storm`. Para copiar o arquivo para o cluster, é possível usar o comando `scp`. Por exemplo, `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > O exemplo de WordCount e outros exemplos de storm starter já estão incluídos no cluster em `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Enviar uma topologia: de forma programática

É possível implantar programaticamente uma topologia usando o serviço Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) fornece um exemplo de aplicativo Java que demonstra como implantar e iniciar uma topologia por meio do serviço Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorar e gerenciar: Visual Studio

Quando uma topologia for enviada usando o Visual Studio, a exibição **Topologias do Storm** será mostrada. Selecione a topologia da lista para exibir informações sobre a topologia em execução.

![monitor do visual studio](./media/hdinsight-storm-deploy-monitor-topology/vsmonitor.png)

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

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorar e gerenciar: SSH e o comando Storm

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

## <a name="monitor-and-manage-storm-ui"></a>Monitorar e gerenciar: interface do usuário do Storm

A IU do Storm fornece uma interface Web para trabalhar com as topologias em funcionamento, e é incluída no seu cluster HDInsight. Para ver a interface de usuário do Storm, use um navegador da Web para abrir **https://CLUSTERNAME.azurehdinsight.net/stormui**, em que **CLUSTERNAME** é o nome do seu cluster.

> [!NOTE]
> Se solicitado a forneça um nome de usuário e senha, insira o administrador de cluster (admin) e a senha que você usou ao criar o cluster.

### <a name="main-page"></a>Página principal

A página principal da interface do usuário do Storm fornece as seguintes informações:

* **Resumo do cluster**: informações básicas sobre o cluster do Storm
* **Resumo da topologia**: uma lista das topologias em execução. Use os links desta seção para exibir mais informações sobre topologias específicas.
* **Resumo do Supervisor**: informações sobre o supervisor do Storm.
* **Configuração do Nimbus**: configuração do Nimbus para o cluster.

### <a name="topology-summary"></a>Resumo da topologia

Selecionar um link na seção **Resumo da topologia** exibirá as seguintes informações sobre a topologia:

* **Resumo da topologia**: informações básicas sobre a topologia.
* **Ações da topologia**: ações de gerenciamento que podem ser executadas para a topologia.

  * **Ativar**: retoma o processamento de uma topologia de desativada.
  * **Desativar**: pausa uma topologia em execução.
  * **Reequilibrar**: ajusta o paralelismo da topologia. Você deve reequilibrar topologias em execução depois de alterar o número de nós no cluster. Essa operação permite que a topologia ajuste o paralelismo para compensar o aumento ou a diminuição do número de nós no cluster.

    Para saber mais, consulte <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Noções básicas sobre o paralelismo de uma topologia do Storm</a>.
  * **Eliminar**: encerra uma topologia Storm após o tempo limite especificado.
* **Estatísticas da topologia**: estatísticas sobre a topologia. Para definir o período de tempo para as entradas restantes na página, use os links da coluna **Janela**.
* **Spouts**: os spouts usados pela topologia. Use os links desta seção para exibir mais informações sobre spouts específicos.
* **Bolts**: os bolts usados pela topologia. Use os links desta seção para exibir mais informações sobre bolts específicos.
* **Configuração da topologia**: a configuração da topologia selecionada.

### <a name="spout-and-bolt-summary"></a>Resumo de Spout e Bolt

Selecionar um spout nas seções **Spouts** ou **Bolts** exibirá as seguintes informações sobre o item selecionado:

* **Resumo do componente**: informações básicas sobre o spout ou o bolt.
* **Estatísticas de Spout/Bolt**: estatísticas sobre o spout ou o bolt. Para definir o período de tempo para as entradas restantes na página, use os links da coluna **Janela**.
* **Estatísticas de entrada** (somente bolt): informações sobre os streams de entrada consumidos pelo bolt.
* **Estatísticas de saída**: informações sobre os streams emitidos pelo spout ou pelo bolt.
* **Executores**: informações sobre as instâncias do spout ou bolt. Selecione a entrada **Porta** gerada por um executor específico para exibir um log de informações de diagnóstico produzido para esta instância.
* **Erros**: qualquer informação de erro para este spout ou bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitorar e gerenciar: API REST

A interface do usuário do Storm é criada sobre a API REST e, portanto, você pode realizar gerenciamento semelhante e monitorar a funcionalidade usando a API REST. Você pode usar a API REST para criar ferramentas personalizadas para o gerenciamento e o monitoramento de topologias Storm.

Para obter mais informações, veja [API REST da interface do usuário do Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). As informações a seguir são específicas para o uso da API REST com Apache Storm no HDInsight.

> [!IMPORTANT]
> A API do REST Storm não está disponível publicamente pela Internet, e deve ser acessada usando um túnel SSH para o nó principal do cluster HDInsight. Para obter informações sobre como criar e usar um túnel SSH, consulte [Usar um túnel SSH para acessar a interface do usuário da Web do Ambari, ResourceManager, JobHistory, NameNode, Oozie e outras interfaces do usuário da Web](hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>URI de base

O URI base da API REST em clusters HDInsight baseados em Linux está disponível no nó de cabeçalho em **https://HEADNODEFQDN:8744/api/v1/**. O nome de domínio do nó de cabeçalho é gerado durante a criação do cluster e não é estático.

Você pode encontrar o FQDN (Nome de Domínio Totalmente Qualificado) para o nó de cabeçalho do cluster de várias maneiras diferentes:

* **De uma sessão SSH**: use o comando `headnode -f` de uma sessão SSH para o cluster.
* **Do Ambari Web**: selecione **Serviços** na parte superior da página, em seguida, selecione **Storm**. Na guia **Resumo** selecione **Servidor de IU do Storm**. O FQDN do nó que hospeda a interface do usuário do Storm e a API REST são exibidos na parte superior da página.
* **Da API REST do Ambari**: use o comando `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` para recuperar informações sobre o nó no qual a interface do usuário do Storm e a API REST estão sendo executados. Substitua **SENHA** pela senha do administrador do cluster. Substitua **CLUSTERNAME** pelo nome do cluster. Na resposta, a entrada "host_name" contém o FQDN do nó.

### <a name="authentication"></a>Autenticação

As solicitações para a API REST devem usar a **autenticação básica**e, portanto, você usará o nome do administrador e a senha do cluster HDInsight.

> [!NOTE]
> Como a autenticação básica é enviada usando texto não criptografado, você **sempre** deverá usar HTTPS para proteger as comunicações com o cluster.

### <a name="return-values"></a>Valores de retorno

As informações retornadas pela API REST só podem ser usadas dentro do cluster. Por exemplo, o FQDN (nome de domínio totalmente qualificado) retornado para servidores Zookeeper não é acessível pela Internet.

## <a name="next-steps"></a>Próximas etapas

Saiba como [Desenvolver topologias baseadas em Java usando o Maven](hdinsight-storm-develop-java-topology.md).

Para obter mais topologias de exemplo, consulte [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md).
