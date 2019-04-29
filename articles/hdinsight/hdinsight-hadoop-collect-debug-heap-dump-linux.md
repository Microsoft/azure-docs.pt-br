---
title: Ativar despejos de heap para serviços do Apache Hadoop no HDInsight - Azure
description: Ative os dumps de heap para os serviços Apache Hadoop dos clusters HDInsight baseados em Linux para depuração e análise.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a1b816656e019a214e8c0dc72b79575c49d99e68
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098347"
---
# <a name="enable-heap-dumps-for-apache-hadoop-services-on-linux-based-hdinsight"></a>Ativar dumps de heap para serviços do Apache Hadoop no HDInsight baseado em Linux

[!INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Despejos de heap contêm um instantâneo da memória do aplicativo, incluindo os valores das variáveis no momento em que o despejo foi criado. Portanto, eles são úteis para diagnosticar problemas que ocorrem no tempo de execução.

> [!IMPORTANT]  
> As etapas neste documento funcionam somente com clusters HDInsight que usam Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="whichServices"></a>Serviços

Você pode habilitar o despejo de heap para os seguintes serviços:

* **Apache hcatalog** -tempelton
* **Hive Apache** -hiveserver2, metastore, derbyserver
* **mapreduce** - jobhistoryserver
* **Yarn Apache** -resourcemanager, nodemanager, timelineserver
* **Apache hdfs** -datanode, secondarynamenode namenode

Você também pode habilitar despejos de heap para os processos de mapeamento e redução executados pelo HDInsight.

## <a name="configuration"></a>Compreendendo a configuração do despejo de heap

Despejos de heap são habilitados transmitindo opções (às vezes conhecidas como opts, ou parâmetros) para a JVM quando um serviço é iniciado. Para a maioria dos serviços do [Apache Hadoop](https://hadoop.apache.org/), você pode modificar o script de shell usado para iniciar o serviço para passar essas opções.

Em cada script, há uma exportação para **\*\_OPTS**, que contém as opções passadas para a JVM. Por exemplo, no script **hadoop-env.sh**, a linha que começa com `export HADOOP_NAMENODE_OPTS=` contém as opções para o serviço NameNode.

Os processos de mapeamento e redução são ligeiramente diferentes, uma vez que essas operações são processos filho do serviço MapReduce. Cada processo de mapeamento ou redução é executado em um contêiner filho, e há duas entradas que contêm as opções de JVM. Contidos em **mapred-site.xml**:

* **mapreduce.admin.map.child.java.opts**
* **mapreduce.admin.reduce.child.java.opts**

> [!NOTE]  
> Recomendamos usar o [Apache Ambari](https://ambari.apache.org/) para modificar as configurações de scripts e mapred-site.xml, já que o Ambari manipula as alterações de replicação nos nós do cluster. Consulte a seção [Usando o Apache Ambari](#using-apache-ambari) para obter etapas específicas.

### <a name="enable-heap-dumps"></a>Habilitar despejos de heap

A seguinte opção habilita os despejos de heap quando ocorre um OutOfMemoryError:

    -XX:+HeapDumpOnOutOfMemoryError

O **+** indica que a opção está habilitada. Por padrão, ela fica desabilitada.

> [!WARNING]  
> Despejos de heap não são habilitados para serviços do Hadoop no HDInsight por padrão, pois os arquivos de despejo podem ser grandes. Se você habilitá-los para solução de problemas, lembre-se de desabilitá-los após ter reproduzido o problema e coletado os arquivos de despejo.

### <a name="dump-location"></a>Local do despejo

O local padrão para o arquivo de despejo é o diretório de trabalho atual. Você pode controlar onde o arquivo é armazenado usando a seguinte opção:

    -XX:HeapDumpPath=/path

Por exemplo, usar `-XX:HeapDumpPath=/tmp` fará com que os despejos sejam armazenados no diretório /tmp.

### <a name="scripts"></a>Scripts

Você também pode disparar um script quando um **OutOfMemoryError** ocorrer. Por exemplo, disparar uma notificação para que você saiba que o erro ocorreu. Use a opção a seguir para disparar um script em um __OutOfMemoryError__:

    -XX:OnOutOfMemoryError=/path/to/script

> [!NOTE]  
> Como o Apache Hadoop é um sistema distribuído, qualquer script usado deve ser colocado em todos os nós no cluster no qual o serviço é executado.
> 
> O script deve também estar em um local que seja acessível pela conta em que o serviço é executado e deve fornecer permissões de execução. Por exemplo, você pode optar por armazenar scripts em `/usr/local/bin` e usar `chmod go+rx /usr/local/bin/filename.sh` para conceder permissões de leitura e execução.

## <a name="using-apache-ambari"></a>Usando o Apache Ambari

Para modificar a configuração de um serviço, use as seguintes etapas:

1. Abra a UI da Web do Ambari para seu cluster. A URL é https://YOURCLUSTERNAME.azurehdinsight.net.

    Quando solicitado, autentique-se no site usando o nome da conta HTTP (padrão: admin) e a senha do seu cluster.

   > [!NOTE]  
   > O Ambari poderá solicitar o nome de usuário e senha mais uma vez. Nesse caso, insira o mesmo nome de conta e senha.

2. Usando a lista à esquerda, selecione a área de serviço que você deseja modificar. Por exemplo, **HDFS**. Na área central, selecione a guia **Configurações** .

    ![Imagem do Ambari Web com a guia de Configurações de HDFS selecionada](./media/hdinsight-hadoop-heap-dump-linux/serviceconfig.png)

3. Usando a entrada **Filtrar...**, insira **opts**. Apenas os itens que contêm esse texto são exibidos.

    ![Lista filtrada](./media/hdinsight-hadoop-heap-dump-linux/filter.png)

4. Encontre a entrada **\*\_OPTS** do serviço para o qual você deseja habilitar os despejos de heap e adicione as opções que deseja habilitar. Na imagem a seguir, adicionei `-XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/` à entrada **HADOOP\_NAMENODE\_OPTS**:

    ![HADOOP_NAMENODE_OPTS com -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=/tmp/](./media/hdinsight-hadoop-heap-dump-linux/opts.png)

   > [!NOTE]  
   > Ao habilitar despejos de heap para o processo filho de mapeamento ou redução, procure os campos denominados **mapreduce.admin.map.child.java.opts** e **mapreduce.admin.reduce.child.java.opts**.

    Use o botão **Salvar** para salvar as alterações. Você pode inserir uma nota breve que descreve as alterações.

5. Após as alterações serem aplicadas, o ícone **Reinicialização necessária** aparecerá ao lado de um ou mais serviços.

    ![botão de reinicialização necessária e botão de reinicialização](./media/hdinsight-hadoop-heap-dump-linux/restartrequiredicon.png)

6. Selecione cada serviço que precisa ser reiniciado e use o botão **Ações de Serviço** para **Ativar o Modo de Manutenção**. O modo de manutenção impede que alertas sejam gerados pelo serviço ao reiniciá-lo.

    ![Ativar o menu do modo de manutenção](./media/hdinsight-hadoop-heap-dump-linux/maintenancemode.png)

7. Após ter habilitado o modo de manutenção, use o botão **Reiniciar** para o serviço **Reiniciar Todos os Afetados**

    ![Entrada Reiniciar todos os afetados](./media/hdinsight-hadoop-heap-dump-linux/restartbutton.png)

   > [!NOTE]  
   > As entradas do botão **Reiniciar** podem ser diferentes para outros serviços.

8. Após os serviços serem reiniciados, use o botão **Ações de Serviço** para **Desativar o Modo de Manutenção**. Use este Ambari para retomar o monitoramento dos alertas do serviço.

