---
title: Acessar logs do aplicativo Apache Hadoop YARN no HDInsight baseado em Linux - Azure
description: Saiba como acessar os logs do aplicativo YARN em um cluster HDInsight (Apache Hadoop) baseado em Linux usando a linha de comando e um navegador da web.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.openlocfilehash: 0a3411cc4cc32c3e54583ab81ee98f2e151d4384
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62098432"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-linux-based-hdinsight"></a>Acessar logs do aplicativo Apache Hadoop YARN no HDInsight baseado em Linux

Saiba como acessar os logs para os aplicativos [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) (Ainda Outro Negociador de Recursos) em um cluster do [Apache Hadoop](https://hadoop.apache.org/) no Azure HDInsight.

> [!IMPORTANT]  
> As etapas deste documento exigem um cluster HDInsight que usa Linux. O Linux é o único sistema operacional usado na versão 3.6 ou superior do HDInsight. Para obter mais informações, consulte [Controle de versão do componente do HDInsight](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="YARNTimelineServer"></a>YARN Timeline Server

O [Servidor de Linha de Tempo do Apache Hadoop YARN](https://hadoop.apache.org/docs/r2.7.3/hadoop-yarn/hadoop-yarn-site/TimelineServer.html) fornece informações genéricas sobre aplicativos concluídos

O YARN Timeline Server inclui o seguinte tipo de dados:

* A ID do aplicativo, um identificador exclusivo de um aplicativo
* O usuário que iniciou o aplicativo
* Informações sobre as tentativas feitas para concluir o aplicativo
* Os contêineres usados por uma determinada tentativa de aplicativo

## <a name="YARNAppsAndLogs"></a>Logs e aplicativos YARN

O YARN suporta vários modelos de programação ([Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) sendo um deles) desacoplando o gerenciamento de recursos do agendamento/monitoramento de aplicativos. O YARN usa um RM (*ResourceManager*) global, NMs (*NodeManagers*) por nó de trabalho e AMs (*ApplicationMasters*) por aplicativo. O aplicativo AM negocia recursos (CPU, memória, disco e rede) para executar o aplicativo com o RM. O RM atua junto com os NMs para conceder esses recursos na forma de *contêineres*. O AM é responsável por controlar o andamento dos contêineres atribuídos pelo RM. Um aplicativo pode exigir um número de contêineres dependendo da natureza do aplicativo.

Cada aplicativo pode consistir em várias *tentativas do aplicativo*. Se um aplicativo falhar, ele poderá ser repetido como uma nova tentativa. Cada tentativa é executado em um contêiner. De certa forma, um contêiner fornece o contexto para a unidade básica de trabalho executado por um aplicativo YARN. Todo trabalho é feito no contexto de um contêiner é executado no nó de trabalho único no qual o contêiner foi alocado. Consulte [Conceitos do Apache Hadoop YARN][YARN-concepts] para referência futura.

Os logs de aplicativos (e os logs de contêiner associado) são essenciais na depuração de aplicativos problemáticos do Hadoop. O YARN fornece uma ótima estrutura para coletar, agregar e armazenar logs de aplicativos com o recurso [Agregação de Logs][log-aggregation]. O recurso de agregação de logs permite acessar logs de aplicativos mais determinista. Ele agrega logs em todos os contêineres em um nó de trabalho e as armazena como um arquivo de log agregado por nó de trabalho. O log é armazenado no sistema de arquivos padrão após a conclusão de um aplicativo. O aplicativo deve usar centenas ou milhares de contêineres, mas logs para todos os contêineres executados em um nó único de trabalhado sempre são agregados a um único arquivo. Assim, há apenas 1 log por nó de trabalho usado pelo seu aplicativo. A Agregação de Log é habilitada por padrão em clusters HDInsight versão 3.0 e superior. Logs agregados estão localizados no armazenamento padrão do cluster. O caminho a seguir é o caminho do HDFS para os logs:

    /app-logs/<user>/logs/<applicationId>

No caminho, `user` é o nome do usuário que iniciou o aplicativo. O `applicationId` é o identificador exclusivo atribuído a um aplicativo pelo RM do YARN.

Os logs agregados não são diretamente legíveis, já que são gravados em um [TFile][T-file], [formato binário][binary-format] indexado pelo contêiner. Use as ferramentas dos logs ResourceManager do YARN ou CLI para exibir esses logs como texto sem formatação para os aplicativos ou contêineres de interesse.

## <a name="yarn-cli-tools"></a>Ferramentas CLI do YARN

Para usar as ferramentas CLI do YARN, você deve primeiro se conectar ao cluster HDInsight usando o SSH. Para obter informações, consulte [Usar SSH com HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

É possível exibir esses logs como texto sem formatação, executando um dos seguintes comandos:

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

Especifique as informações &lt;applicationId>, &lt;user-who-started-the-application>, &lt;containerId> e &lt;worker-node-address> ao executar esses comandos.

## <a name="yarn-resourcemanager-ui"></a>IU do ResourceManager YARN

A interface do usuário do ResourceManager YARN é executada no nó de cabeçalho do cluster. Ele é acessado por meio da interface do usuário da Web do Ambari. Execute as etapas a seguir para exibir os logs do YARN:

1. No navegador da Web, navegue até https://CLUSTERNAME.azurehdinsight.net. Substitua CLUSTERNAME com o nome do cluster HDInsight.
2. Na lista de serviços à esquerda da página, selecione **YARN**.

    ![Serviço de yarn selecionado](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. Na lista suspensa **Links rápidos**, selecione um dos nós principais do cluster e selecione **Log do ResourceManager**.

    ![Links rápidos do yarn](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    Você verá uma lista de links para os logs do YARN.

[YARN-timeline-server]:https://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
