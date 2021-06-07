---
title: Como monitorar a disponibilidade do cluster com o Apache Ambari no Azure HDInsight
description: Saiba como usar o Apache Ambari para monitorar a integridade e a disponibilidade do cluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/01/2020
ms.openlocfilehash: dcbe44defeb89ad2f67833b263e5f4983070a46c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863523"
---
# <a name="how-to-monitor-cluster-availability-with-apache-ambari-in-azure-hdinsight"></a>Como monitorar a disponibilidade do cluster com o Apache Ambari no Azure HDInsight

Os clusters HDInsight incluem o Apache Ambari, que fornece informações de integridade em um relance e alertas predefinidos.

Este artigo mostra como usar o Ambari para monitorar o cluster e percorre alguns exemplos para configurar um alerta do Ambari, monitorar a taxa de disponibilidade do nó e criar um alerta de Azure Monitor que é acionado quando uma pulsação não foi recebida de um ou mais nós em cinco horas.

## <a name="dashboard"></a>Painel

O painel do Ambari pode ser acessado selecionando o link do **Ambari Home** na seção **painéis do cluster** da visão geral do HDInsight em portal do Azure, conforme mostrado abaixo. Como alternativa, ele pode ser acessado navegando para `https://CLUSTERNAME.azurehdinsight.net` em um navegador em que ClusterName é o nome do cluster.

:::image type="content" source="media/hdinsight-cluster-availability/azure-portal-dashboard-ambari.png" alt-text="Exibição do portal de recursos do HDInsight":::

Em seguida, você será solicitado a fornecer um nome de usuário e senha de logon de cluster. Insira as credenciais que você escolheu ao criar o cluster.

Em seguida, você será levado para o painel do Ambari, que contém widgets que mostram algumas métricas para fornecer uma visão geral rápida da integridade do seu cluster HDInsight. Esses widgets mostram métricas como o número de JournalNodes (nós de trabalho) em tempo real e o tempo de atividade (nó Zookeeper), a NameNodes (nós de cabeçalho), bem como métricas específicas para determinados tipos de cluster, como o tempo de atividade de ResourceManager de YARN para clusters Spark e Hadoop.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-dashboard.png" alt-text="Exibição do painel de uso do Apache Ambari":::

## <a name="hosts--view-individual-node-status"></a>Hosts – Exibir status de nó individual

Você também pode exibir informações de status para nós individuais. Selecione a guia **hosts** para exibir uma lista de todos os nós no cluster e ver informações básicas sobre cada nó. A marca de seleção verde à esquerda de cada nome de nó indica que todos os componentes estão ativos no nó. Se um componente estiver inoperante em um nó, você verá um triângulo de alerta vermelho em vez da marca de seleção verde.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts1.png" alt-text="Exibição de hosts Apache Ambari do HDInsight":::

Em seguida, você pode selecionar o **nome** de um nó para exibir métricas de host mais detalhadas para esse nó específico. Essa exibição mostra o status/disponibilidade de cada componente individual.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-hosts-node.png" alt-text="O Apache Ambari hospeda exibição de nó único":::

## <a name="ambari-alerts"></a>Alertas do Ambari

O Ambari também oferece vários alertas configuráveis que podem fornecer notificação de determinados eventos. Quando os alertas são disparados, eles são mostrados no canto superior esquerdo do Ambari em um emblema vermelho que contém o número de alertas. A seleção dessa notificação mostra uma lista de alertas atuais.

:::image type="content" source="media/hdinsight-cluster-availability/apache-ambari-alerts.png" alt-text="Contagem de alertas atuais do Apache Ambari":::

Para exibir uma lista de definições de alerta e seus status, selecione a guia **alertas** , conforme mostrado abaixo.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alerts-definitions.png" alt-text="Exibição de definições de alertas do Ambari":::

O Ambari oferece muitos alertas predefinidos relacionados à disponibilidade, incluindo:

| Nome do alerta                        | Descrição   |
|---|---|
| Resumo de integridade do datanode           | Esse alerta de nível de serviço é disparado se houver datanodes não íntegros|
| Integridade de alta disponibilidade do NameNode | Esse alerta de nível de serviço será disparado se o NameNode ativo ou o NameNode em espera não estiver em execução.|
| Porcentagem de JournalNodes disponíveis    | Esse alerta será disparado se o número de JournalNodes inativos no cluster for maior que o limite crítico configurado. Ele agrega os resultados das verificações de processo do JournalNode. |
| Porcentagem de datanodes disponíveis       | Esse alerta será disparado se o número de datanodes inativos no cluster for maior que o limite crítico configurado. Ele agrega os resultados de verificações de processo de datanode.|


Para exibir detalhes de um alerta ou modificar critérios, selecione o **nome** do alerta. Use o **Resumo de integridade do datanode** como um exemplo. Você pode ver uma descrição do alerta, bem como os critérios específicos que irão disparar um alerta de ' aviso ' ou ' crítico ' e o intervalo de verificação para os critérios. Para editar a configuração, selecione o botão **Editar** no canto superior direito da caixa de configuração.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration.png" alt-text="Configuração de alerta do Apache Ambari":::

Aqui, você pode editar a descrição e, o mais importante, o intervalo de verificação e os limites para alertas de aviso ou críticos.

:::image type="content" source="media/hdinsight-cluster-availability/ambari-alert-configuration-edit.png" alt-text="Exibição de edição de configurações de alerta do Ambari":::

Neste exemplo, você pode fazer com que dois datanodes não íntegros disparem um alerta crítico e um datanode não íntegro só dispare um aviso. Selecione **salvar** quando terminar de editar.

## <a name="email-notifications"></a>Notificações por email

Opcionalmente, você também pode configurar notificações por email para alertas do Ambari. Para fazer isso, na guia **alertas** , clique no botão **ações** no canto superior esquerdo e, em seguida, em **gerenciar notificações.**

:::image type="content" source="media/hdinsight-cluster-availability/ambari-manage-notifications.png" alt-text="Ação de gerenciar notificações do Ambari":::

Será aberta uma caixa de diálogo para gerenciar notificações de alerta. Selecione na **+** parte inferior da caixa de diálogo e preencha os campos necessários para fornecer Ambari com detalhes do servidor de email do qual enviar emails.

> [!TIP]
> A configuração de notificações por email do Ambari pode ser uma boa maneira de receber alertas em um único lugar ao gerenciar vários clusters HDInsight.

## <a name="next-steps"></a>Próximas etapas

- [Disponibilidade e confiabilidade dos clusters Apache Hadoop em HDInsight](./hdinsight-business-continuity.md)
- [Disponibilidade do cluster – Logs do Azure Monitor](./cluster-availability-monitor-logs.md)
- [Usar logs de Azure Monitor](hdinsight-hadoop-oms-log-analytics-tutorial.md)
- [Notificações por email do Apache Ambari](apache-ambari-email.md)
