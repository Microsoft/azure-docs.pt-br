---
title: Otimizar clusters com o Apache Ambari no Azure HDInsight
description: Use a interface do usuário da Web do Apache amAmbari para configurar e otimizar clusters do Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 14268e58296554cedc9488e48b41719421797a57
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933287"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Otimizar clusters com o Apache Ambari no Azure HDInsight

O HDInsight fornece clusters Apache Hadoop para aplicativos de processamento de dados de larga escala. O gerenciamento, o monitoramento e a otimização desses clusters complexos de vários nós podem ser desafiadores. O Apache Ambari é uma interface da Web para gerenciar e monitorar clusters HDInsight do Linux.

Para ver uma introdução ao uso da interface do usuário da Web do Ambari, consulte [Gerenciar clusters HDInsight usando a interface de usuário do Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)

Faça logon no Ambari em `https://CLUSTERNAME.azurehdidnsight.net` usando suas credenciais do cluster. A tela inicial exibe um painel de visão geral.

![Painel do usuário do Apache Ambari exibido](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

A interface do usuário da Web do amAmbari é usada para gerenciar hosts, serviços, alertas, configurações e exibições. Ambari não pode ser usado para criar um cluster HDInsight ou atualizar serviços. Também não é possível gerenciar pilhas e versões, descomissionar ou recomissionar hosts ou adicionar serviços ao cluster.

## <a name="manage-your-clusters-configuration"></a>Gerenciar a configuração do cluster

As configurações ajudam a ajustar a um serviço específico. Para modificar as definições de configuração de um serviço, selecione o serviço na barra lateral **Serviços** (à esquerda). Em seguida, navegue até a guia **configurações** na página detalhes do serviço.

![Barra lateral dos serviços Apache Ambari](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>Modificar o tamanho do heap do NameNode Java

O tamanho do heap do NameNode Java depende de muitos fatores, como a carga no cluster. Além disso, os números de arquivos e os números de blocos. O tamanho padrão de 1 GB funciona bem com a maioria dos clusters, embora algumas cargas de trabalho possam exigir mais ou menos memória.

Para modificar o tamanho do heap do NameNode Java:

1. Selecione **HDFS** na barra lateral Serviços e navegue até a guia **Configurações**.

    ![Configuração do Ambari HDFS do Apache](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. Localize a configuração **Tamanho do heap do NameNode Java**. Também é possível usar a caixa de texto **filtro** para digitar e localizar uma configuração específica. Selecione o ícone de **caneta** ao lado do nome da configuração.

    ![Tamanho do heap de Java do Apache Ambari NameNode](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Digite o novo valor na caixa de texto e pressione **Enter** para salvar a alteração.

    ![Ambari editar heap NameNode Java Size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. O tamanho do heap do Java NameNode é alterado para 1 GB de 2 GB.

    ![Size2 de heap do NameNode editado](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Salve as alterações clicando no botão verde **Salvar** na parte superior da tela de configuração.

    ![' Salvar configurações do Apache Ambari '](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar clusters do HDInsight com a interface de usuário da Web do Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Otimizar o Apache HBase](./optimize-hbase-ambari.md)
* [Otimizar o Apache Hive](./optimize-hive-ambari.md)
* [Otimizar o Apache Pig](./optimize-pig-ambari.md)
