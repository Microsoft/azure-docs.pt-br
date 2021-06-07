---
title: Monitorar e gerenciar o Azure HDInsight usando a IU da Web do Ambari
description: Saiba como usar a interface do usuário do Apache Ambari para monitorar e gerenciar clusters HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 1b4570b09994f292de2b175396e4b6d1882658d9
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104866481"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Gerenciar clusters HDInsight usando a interface do usuário da Web do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

O Apache Ambari simplifica o gerenciamento e o monitoramento de um cluster Apache Hadoop. Essa simplificação é feita fornecendo uma interface do usuário da Web fácil de usar e a API REST. O Ambari está incluído em clusters HDInsight e é usado para monitorar o cluster e fazer alterações de configuração.

Neste documento, você aprenderá a usar a interface de usuário do Ambari Web com um cluster HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>O que é o Apache Storm?

O [Apache Ambari](https://ambari.apache.org) simplifica o gerenciamento do Hadoop, fornecendo uma interface do usuário da Web fácil de usar. Você pode usar o Ambari para gerenciar e monitorar clusters do Hadoop. Os desenvolvedores podem integrar essas funcionalidades em seus aplicativos usando as [APIs REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Conectividade

A interface do usuário da Web do amAmbari está disponível em seu cluster HDInsight em `https://CLUSTERNAME.azurehdinsight.net` , em que `CLUSTERNAME` é o nome do cluster.

> [!IMPORTANT]  
> Conectar-se ao Ambari no HDInsight requer HTTPS. Quando a autenticação for solicitada, use o nome e a senha da conta do administrador que você forneceu quando o cluster foi criado. Se as credenciais não forem solicitadas, verifique as configurações de rede para confirmar se não há nenhum problema de conectividade entre o cliente e os clusters do Azure HDInsight.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

Embora o Ambari para o cluster seja acessível diretamente pela Internet, alguns links da interface do usuário da Web do amAmbari (por exemplo, para o JobTracker) não são expostos na Internet. Para acessar esses serviços, você deve criar um túnel SSH. Para obter mais informações, consulte [Usar túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interface do usuário da Ambari Web

> [!WARNING]  
> Nem todos os recursos da interface do usuário da Web do Ambari têm suporte no HDInsight. Para obter mais informações, consulte a seção [Operações sem suporte](#unsupported-operations) deste documento.

Ao conectar-se à interface do usuário da Web do amAmbari, você será solicitado a autenticar-se na página. Use a senha e o usuário administrador de cluster (Admin padrão) usados durante a criação do cluster.

Quando a página se abrir, observe a barra na parte superior. Essa barra contém as seguintes informações e controles:

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png" alt-text="Visão geral do painel do Apache Ambari":::

|Item |Descrição |
|---|---|
|Logotipo do Ambari|Abre o painel, que pode ser usado para monitorar o cluster.|
|Nome do cluster # Ops|Exibe o número de operações de Ambari em andamento. A seleção do nome do cluster ou **Nº operações** exibe uma lista de operações em segundo plano.|
|n º de alertas|Exibe avisos ou alertas críticos, se houver, para o cluster.|
|Painel|Exibe o painel.|
|Serviços|Informações e definições de configuração para os serviços no cluster.|
|Hosts|Informações e definições de configuração para os nós no cluster.|
|Alertas|Um log de informações, avisos e alertas críticos.|
|Admin|Pilha de software/serviços que estão instalados no cluster, informações de conta de serviço e segurança Kerberos.|
|Botão administrador|Gerenciamento de Ambari, configurações de usuário e sair.|

## <a name="monitoring"></a>Monitoramento

### <a name="alerts"></a>Alertas

A lista a seguir contém os status de alerta comuns usados pelo Ambari:

* **OK**
* **Aviso**
* **DRASTICAMENTE**
* **UNKNOWN**

Os alertas diferentes de **OK** fazem com que a entrada **nº alertas** na parte superior da página exiba o número de alertas. A seleção dessa entrada exibe os alertas e seus status.

Os alertas estão organizados em vários grupos padrão, que podem ser exibidos na página **Alertas** .

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png" alt-text="Resumo da página de alertas do Apache Ambari":::

Você pode gerenciar os grupos usando o menu **Ações** e selecionando **Gerenciar Grupos de Alerta**.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png" alt-text="Apache Ambari gerenciar grupos de alertas":::

Você gerencia os métodos de alerta e cria notificações de alerta no menu **ações** selecionando __gerenciar notificações__. Quaisquer notificações atuais são exibidas. Crie notificações aqui. As notificações podem ser enviadas por **EMAIL** ou **SNMP** quando ocorrerem combinações específicas de alerta/gravidade. Por exemplo, você pode enviar uma mensagem de email quando qualquer um dos alertas no grupo **YARN Padrão** está definido para **Crítico**.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png" alt-text="Notificação de alerta de criação do Apache Ambari":::

Por fim, selecionar __Gerenciar Definições de Alerta__ do menu __Ações__ permite que você defina o número de vezes que um alerta deve ocorrer antes do envio de uma notificação. Essa configuração pode ser usada para evitar notificações de erros transitórios.

Para obter um tutorial de uma notificação de alerta usando uma conta gratuita do [SendGrid](../sendgrid-dotnet-how-to-send-email.md), consulte [configurar notificações por email do Apache Ambari no Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Cluster

A guia **Métricas** do painel contém uma série de widgets que facilitam monitorar o status do cluster em um relance. Vários widgets, tais como **Uso de CPU**, fornecem informações adicionais quando são clicados.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png" alt-text="Painel do Apache Ambari com métricas":::

A guia **Mapa de Dados** exibe as métricas na forma de mapas de dados coloridos, que vão do verde ao vermelho.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png" alt-text="Painel do Apache Ambari com calor":::

Para obter mais informações sobre os nós no cluster, selecione **Hosts**. Em seguida, selecione o nó específico em que você está interessado.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png" alt-text="Detalhes do resumo do host Apache Ambari":::

### <a name="services"></a>Serviços

A barra lateral **Serviços** no painel oferece uma visão rápida do status dos serviços em execução no cluster. Ícones diversos são usados para indicar o status ou ações que devem ser realizadas. Por exemplo, um símbolo amarelo de reciclagem será exibido se um serviço precisar ser reciclado.


:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png" alt-text="Barra lateral dos serviços Apache Ambari":::

> [!NOTE]  
> Os serviços exibidos variam de acordo com versões e tipos de cluster HDInsight. Os serviços exibidos aqui podem ser diferentes dos serviços exibidos para o cluster.

A seleção de um serviço exibirá informações mais detalhadas sobre o serviço.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png" alt-text="Informações de resumo do serviço Apache Ambari":::

#### <a name="quick-links"></a>Links rápidos

Alguns serviços exibem um link **Links Rápidos** na parte superior da página. Esse link pode ser usado para acessar interfaces do site da Web específicas do serviço, como:

* **Histórico de Trabalhos** : histórico de trabalhos do MapReduce.
* **Resource Manager** -interface do usuário do yarn Resource Manager.
* **NameNode** : IU NameNode no HDFS (Sistema de Arquivos Distribuído do Hadoop).
* **IU da Web do Oozie** : IU do Oozie.

A seleção de qualquer um desses links abrirá uma nova guia em seu navegador, que exibirá a página selecionada.

> [!NOTE]  
> Selecionar a entrada **Links Rápidos** para um serviço pode retornar um erro de "servidor não encontrado". Se você encontrar esse erro, você deverá usar um túnel SSH ao usar a entrada **Links rápidos** para esse serviço. Para obter informações, consulte [Usar túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gerenciamento

### <a name="ambari-users-groups-and-permissions"></a>Usuários, grupos e permissões do Ambari

Há suporte para o trabalho com usuários, grupos e permissões. Para a administração local, consulte [autorizar usuários para exibições do Apache Ambari](./hdinsight-authorize-users-to-ambari.md). Para clusters ingressados no domínio, consulte [gerenciar clusters HDInsight ingressados no domínio](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Não exclua nem altere a senha do Watchdog do Ambari (hdinsightwatchdog) em seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar as ações de script ou executar operações de dimensionamento com o cluster.

### <a name="hosts"></a>Hosts

A página **Hosts** lista todos os hosts no cluster. Para gerenciar hosts, siga estas etapas.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png" alt-text="Visão geral da página de hosts Apache Ambari":::

> [!NOTE]  
> As ações de adicionar, encerrar e reativar um host não devem ser realizadas com clusters HDInsight.

1. Selecione o host que você deseja gerenciar.

2. Use o menu **ações** para selecionar a ação que você deseja fazer:

    |Item |Descrição |
    |---|---|
    |Iniciar todos os componentes|Inicie todos os componentes no host.|
    |Parar todos os componentes|Pare todos os componentes no host.|
    |Reiniciar todos os componentes|Pare e inicie todos os componentes no host.|
    |Ativar o modo de manutenção|Suprime alertas para o host. Esse modo deve ser habilitado se você estiver fazendo ações que geram alertas. Por exemplo, parar e iniciar um serviço.|
    |Desligar o modo de manutenção|Retorna o host para alertas normais.|
    |Parar|Interrompe datanode ou NodeManagers no host.|
    |Iniciar|Inicia datanode ou NodeManagers no host.|
    |Reiniciar|Para e inicia o datanode ou NodeManagers no host.|
    |Desprogramar|Remove um host do cluster. **Não use essa ação em clusters HDInsight.**|
    |Reativação|Adiciona um host anterior descomissionado ao cluster. **Não use essa ação em clusters HDInsight.**|

### <a name="services"></a><a id="service"></a>Serviços

Na página **Painel** ou **Serviços**, use o botão **Ações** na parte inferior da lista de serviços para interromper e iniciar todos os serviços.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Lista de ações do serviço Apache Ambari." border="true":::

> [!WARNING]  
> Novos serviços devem ser adicionados usando uma Ação de Script durante o provisionamento do cluster. Para saber mais sobre o uso de ações de script, consulte [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

Embora o botão **Ações** possa reiniciar todos os serviços, muitas vezes convém iniciar, parar ou reiniciar um serviço específico. Use as seguintes etapas para executar ações em um serviço individual:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Na parte superior da guia **Resumo**, use o botão **Ações de Serviço** e selecione a ação a tomar. Esta ação reinicia o serviço em todos os nós.

    :::image type="content" source="./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png" alt-text="Ações de serviço individual do Apache Ambari":::

   > [!NOTE]  
   > Reiniciar alguns serviços enquanto o cluster estiver em execução pode gerar alertas. Para evitar alertas, você pode usar o botão **Ações de Serviço** para habilitar o **Modo de manutenção** para o serviço antes de executar a reinicialização.

3. Depois que uma ação é selecionada, a entrada **Nº operações** na parte superior da página aumenta para mostrar que está ocorrendo uma operação em segundo plano. Se a exibição estiver configurada, a lista de operações em segundo plano será exibida.

   > [!NOTE]  
   > Se você habilitou o **modo Manutenção** para o serviço, lembre-se de desabilitá-lo usando o botão **Ações de Serviço** quando a operação for concluída.

Para configurar um serviço, use as seguintes etapas:

1. Na página **Painel** ou **Serviços**, selecione um serviço.

2. Selecione a guia **configurações** . A configuração atual é exibida. Uma lista das configurações anteriores também é exibida.

    :::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png" alt-text="Configuração do serviço Apache Ambari":::

3. Use os campos exibidos para modificar a configuração e selecione **Salvar**. Ou selecione uma configuração anterior e clique em **Tornar atual** para reverter para as configurações anteriores.

## <a name="ambari-views"></a>Exibições do Ambari

As Exibições do Ambari permitem que os desenvolvedores conectem elementos de interface do usuário à interface do usuário da Web do Apache Ambari usando a Estrutura de modos de exibição do Ambari. O HDInsight fornece as seguintes exibições com tipos de cluster do Hadoop:

* Exibição do Hive: a Exibição do Hive permite executar consultas de Hive diretamente do seu navegador da Web. Você pode salvar consultas, exibir os resultados, salvar os resultados no armazenamento de cluster ou baixar os resultados no sistema local. Para obter mais informações sobre como usar Exibições do Hive, consulte [Usar Exibições do Apache Hive com HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Exibição Tez: a exibição Tez permite que você compreenda melhor e otimize os trabalhos. Você pode exibir informações sobre como os trabalhos do Tez são executados e quais recursos são usados.

## <a name="unsupported-operations"></a>Operações sem suporte

As seguintes operações Ambari não têm suporte no HDInsight:

* __Mover o serviço do Coletor de métricas__. Ao exibir as informações sobre o serviço do Coletor de métricas, uma das ações disponíveis no menu de Ações do serviço é __Mover o Coletor de métricas__. Não há suporte para essa ação com o HDInsight.

## <a name="next-steps"></a>Próximas etapas

* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) com o HDInsight.
* [Use o Apache Ambari para otimizar as configurações de cluster do HDInsight](./hdinsight-changing-configs-via-ambari.md)
* [Dimensionar clusters do Azure HDInsight](./hdinsight-scaling-best-practices.md)
