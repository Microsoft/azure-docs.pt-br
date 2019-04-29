---
title: Monitorar e gerenciar o Azure HDInsight usando a IU da Web do Ambari
description: Aprenda a usar o Ambari para monitorar e gerenciar clusters HDInsight baseados em Linux. Neste documento, você aprenderá a usar a interface de usuário do Ambari Web com clusters HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 1659ab72620b6bf91eb932f8414a0f6600350e37
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63761106"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

O Apache Ambari simplifica o gerenciamento e monitoramento de um cluster Apache Hadoop, fornecendo uma forma fácil de usar a interface do usuário da Web e a API REST. O Ambari está incluído em clusters HDInsight baseados em Linux e é usado para monitorar o cluster e fazer alterações de configuração.

Neste documento, você aprenderá a usar a interface de usuário do Ambari Web com um cluster HDInsight.

## <a id="whatis"></a>O que é o Apache Storm?

O [Apache Ambari](https://ambari.apache.org) simplifica o gerenciamento do Hadoop, fornecendo uma interface do usuário da Web fácil de usar. Você pode usar o Ambari para gerenciar e monitorar clusters do Hadoop. Os desenvolvedores podem integrar essas funcionalidades em seus aplicativos usando as [APIs REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

A IU da Web do Ambari é fornecida por padrão com clusters HDInsight que usam o sistema operacional Linux.

> [!IMPORTANT]  
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). 

## <a name="connectivity"></a>Conectividade

A IU da Web do Ambari está disponível no seu cluster HDInsight em HTTPS://CLUSTERNAME.azurehdinsight.net,em que **CLUSTERNAME** é o nome do cluster.

> [!IMPORTANT]  
> Conectar-se ao Ambari no HDInsight requer HTTPS. Quando a autenticação for solicitada, use o nome e a senha da conta do administrador que você forneceu quando o cluster foi criado.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

Enquanto o Ambari para o cluster é acessível diretamente pela Internet, alguns links da interface do usuário da Web do Ambari (como para o JobTracker) não são expostos na Internet. Para acessar esses serviços, você deve criar um túnel SSH. Para obter mais informações, consulte [Usar túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interface do usuário da Ambari Web

> [!WARNING]  
> Nem todos os recursos da interface do usuário da Web do Ambari têm suporte no HDInsight. Para obter mais informações, consulte a seção [Operações sem suporte](#unsupported-operations) deste documento.

Ao se conectar à interface do usuário do Ambari Web, você recebe uma solicitação para autenticar a página. Use a senha e o usuário administrador de cluster (Admin padrão) usados durante a criação do cluster.

Quando a página se abrir, observe a barra na parte superior. Essa barra contém as seguintes informações e controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logotipo do Ambari** : abre o painel, que pode ser usado para monitorar o cluster.

* **Nº operações do nome de cluster** : exibe o número de operações do Ambari em andamento. A seleção do nome do cluster ou **Nº operações** exibe uma lista de operações em segundo plano.

* **N°. de alertas**: avisos ou alertas críticos, se houver, para o cluster.

* **Painel** : exibe o painel.

* **Serviços** : informações e definições de configuração dos serviços do cluster.

* **Hosts** : informações e definições de configuração dos nós no cluster.

* **Alertas** : um log de informações, avisos e alertas críticos.

* **Admin** : pilha/serviços de software que estão instalados no cluster, informações de conta de serviço e segurança Kerberos.

* **Botão Admin** : gerenciamento, configurações de usuário e logoff do Ambari.

## <a name="monitoring"></a>Monitoramento

### <a name="alerts"></a>Alertas

A lista a seguir contém os status de alerta comuns usados pelo Ambari:

* **OK**
* **Aviso**
* **CRÍTICO**
* **DESCONHECIDO**

Os alertas diferentes de **OK** fazem com que a entrada **nº alertas** na parte superior da página exiba o número de alertas. A seleção dessa entrada exibe os alertas e seus status.

Os alertas estão organizados em vários grupos padrão, que podem ser exibidos na página **Alertas** .

![página alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Você pode gerenciar os grupos usando o menu **Ações** e selecionando **Gerenciar Grupos de Alerta**.

![diálogo gerenciar grupos de alerta](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Você também pode gerenciar os métodos de alerta e criar notificações de alerta no menu **Ações** selecionando __Gerenciar Notificações de Alerta__. Quaisquer notificações atuais são exibidas. Você também pode criar notificações daqui. As notificações podem ser enviadas por **EMAIL** ou **SNMP** quando ocorrerem combinações específicas de alerta/gravidade. Por exemplo, você pode enviar uma mensagem de email quando qualquer um dos alertas no grupo **YARN Padrão** está definido para **Crítico**.

![Diálogo Criar alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, selecionar __Gerenciar Definições de Alerta__ do menu __Ações__ permite que você defina o número de vezes que um alerta deve ocorrer antes do envio de uma notificação. Essa configuração pode ser usada para evitar notificações de erros transitórios.

### <a name="cluster"></a>HDInsight

A guia **Métricas** do painel contém uma série de widgets que facilitam monitorar o status do cluster em um relance. Vários widgets, tais como **Uso de CPU**, fornecem informações adicionais quando são clicados.

![painel com metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

A guia **Mapa de Dados** exibe as métricas na forma de mapas de dados coloridos, que vão do verde ao vermelho.

![painel com heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obter mais informações sobre os nós no cluster, selecione **Hosts**. Selecione o nó específico em que você está interessado.

![detalhes do host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Serviços

A barra lateral **Serviços** no painel oferece uma visão rápida do status dos serviços em execução no cluster. Ícones diversos são usados para indicar o status ou ações que devem ser realizadas. Por exemplo, um símbolo amarelo de reciclagem será exibido se um serviço precisar ser reciclado.

![barra lateral serviços](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> Os serviços exibidos variam de acordo com versões e tipos de cluster HDInsight. Os serviços exibidos aqui podem ser diferentes dos serviços exibidos para o cluster.

A seleção de um serviço exibirá informações mais detalhadas sobre o serviço.

![informações de resumo do serviço](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Links rápidos

Alguns serviços exibem um link **Links Rápidos** na parte superior da página. Ele pode ser usado para acessar IUs da Web específicas do serviço, como:

* **Histórico de Trabalhos** : histórico de trabalhos do MapReduce.
* **Gerenciador de Recursos** : IU do gerenciador de recursos YARN RM.
* **NameNode** : IU NameNode no HDFS (Sistema de Arquivos Distribuído do Hadoop).
* **IU da Web do Oozie** : IU do Oozie.

A seleção de qualquer um desses links abrirá uma nova guia em seu navegador, que exibirá a página selecionada.

> [!NOTE]  
> Selecionar a entrada **Links Rápidos** para um serviço pode retornar um erro de "servidor não encontrado". Se você encontrar esse erro, você deverá usar um túnel SSH ao usar a entrada **Links rápidos** para esse serviço. Para obter informações, consulte [Usar túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gerenciamento

### <a name="ambari-users-groups-and-permissions"></a>Usuários, grupos e permissões do Ambari

O trabalho com usuários, grupos e permissões tem suporte ao usar um cluster HDInsight [ingressado no domínio](./domain-joined/apache-domain-joined-introduction.md). Para obter informações sobre o uso da IU de Gerenciamento do Ambari em um cluster ingressado no domínio, consulte [Gerenciar clusters HDInsight ingressados no domínio](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]  
> Não altere a senha do watchdog Ambari (hdinsightwatchdog) no seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar as ações de script ou executar operações de dimensionamento com o cluster.

### <a name="hosts"></a>Hosts

A página **Hosts** lista todos os hosts no cluster. Para gerenciar hosts, siga estas etapas.

![página hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> As ações de adicionar, encerrar e reativar um host não devem ser realizadas com clusters HDInsight.

1. Selecione o host que você deseja gerenciar.

2. Use o menu **Ações** para selecionar a ação que deseja executar:

   * **Iniciar todos os componentes** : iniciar todos os componentes no host.

   * **Parar todos os componentes** : parar todos os componentes no host.

   * **Reiniciar todos os componentes** : parar e iniciar todos os componentes no host.

   * **Ativar o modo de manutenção** : suprime os alertas sobre o host. Esse modo deverá ser habilitado se você estiver realizando ações que geram alertas. Por exemplo, parar e iniciar um serviço.

   * **Desativar o modo de manutenção** : retorna o host para o modo de alerta normal.

   * **Parar** : Para o DataNode ou NodeManagers no host.

   * **Iniciar** : Inicia o DataNode ou NodeManagers no host.

   * **Reiniciar** : para e inicia o DataNode ou NodeManagers no host.

   * **Encerramento** : remove um host do cluster.

     > [!NOTE]  
     > Não use esta ação em clusters HDInsight.

   * **Reativação** : adiciona ao cluster um host que foi encerrado.

     > [!NOTE]  
     > Não use esta ação em clusters HDInsight.

### <a id="service"></a>Serviços

Na página **Painel** ou **Serviços**, use o botão **Ações** na parte inferior da lista de serviços para interromper e iniciar todos os serviços.

![Ações de Serviço](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> Enquanto **Adicionar Serviço** estiver listado nesse menu, ele não deverá ser usado para adicionar serviços ao cluster HDInsight. Novos serviços devem ser adicionados usando uma Ação de Script durante o provisionamento do cluster. Para saber mais sobre o uso de ações de script, consulte [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

Embora o botão **Ações** possa reiniciar todos os serviços, muitas vezes convém iniciar, parar ou reiniciar um serviço específico. Use as seguintes etapas para executar ações em um serviço individual:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Na parte superior da guia **Resumo**, use o botão **Ações de Serviço** e selecione a ação a tomar. Isso reinicia o serviço em todos os nós.

    ![ação de serviço](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Reiniciar alguns serviços enquanto o cluster estiver em execução pode gerar alertas. Para evitar alertas, você pode usar o botão **Ações de Serviço** para habilitar o **Modo de manutenção** para o serviço antes de executar a reinicialização.

3. Depois que uma ação é selecionada, a entrada **Nº operações** na parte superior da página aumenta para mostrar que está ocorrendo uma operação em segundo plano. Se a exibição estiver configurada, a lista de operações em segundo plano será exibida.

   > [!NOTE]  
   > Se você habilitou o **modo Manutenção** para o serviço, lembre-se de desabilitá-lo usando o botão **Ações de Serviço** quando a operação for concluída.

Para configurar um serviço, use as seguintes etapas:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Selecione a guia **Configurações** . A configuração atual é exibida. Uma lista das configurações anteriores também é exibida.

    ![configurações](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use os campos exibidos para modificar a configuração e selecione **Salvar**. Ou selecione uma configuração anterior e clique em **Tornar atual** para reverter para as configurações anteriores.

## <a name="ambari-views"></a>Exibições do Ambari

As Exibições do Ambari permitem que os desenvolvedores conectem elementos de interface do usuário à interface do usuário da Web do Apache Ambari usando a [Estrutura de modos de exibição do Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). O HDInsight fornece as seguintes exibições com tipos de cluster do Hadoop:


* Exibição de Hive: A Exibição de Hive permite executar consultas de Hive diretamente do navegador da Web. Você pode salvar consultas, exibir os resultados, salvar os resultados no armazenamento de cluster ou baixar os resultados no sistema local. Para obter mais informações sobre como usar Exibições do Hive, consulte [Usar Exibições do Apache Hive com HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Exibição do Tez: A exibição do Tez permite compreender melhor e otimizar os trabalhos. Você pode exibir informações sobre como os trabalhos do Tez são executados e quais recursos são usados.

## <a name="unsupported-operations"></a>Operações sem suporte

Não há suporte para as seguintes operações do Ambari no HDInsight:

* __Mover o serviço do Coletor de métricas__. Ao exibir as informações sobre o serviço do Coletor de métricas, uma das ações disponíveis no menu de Ações do serviço é __Mover o Coletor de métricas__. Essa propriedade não tem suporte com o HDInsight.

## <a name="next-steps"></a>Próximos passos

Saiba como usar a [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) com HDInsight.
