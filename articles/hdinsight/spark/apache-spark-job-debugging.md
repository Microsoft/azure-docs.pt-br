---
title: Depurar trabalhos do Apache Spark em execução no Azure HDInsight
description: Use a interface do usuário do YARN, a interface do usuário do Spark e o Servidor de Histórico do Spark para rastrear e depurar trabalhos em execução no cluster Spark no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 366c77ff94773163b71845b1ccbc6072c503734a
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822294"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Depurar trabalhos do Apache Spark em execução no Azure HDInsight

Neste artigo, você aprenderá a controlar e depurar Apache Spark trabalhos em execução em clusters HDInsight. Depurar usando a interface do usuário do Apache Hadoop YARN, a interface do usuário do Spark e o servidor de histórico do Spark. Você começará um trabalho do Spark usando um notebook disponível com o cluster Spark, **Aprendizado de máquina: análise preditiva nos dados de inspeção de alimentos usando MLLib**. Use as etapas a seguir para acompanhar um aplicativo que você enviou usando qualquer outra abordagem também, por exemplo, **Spark-Submit**.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Você deve ter começado a executar o notebook **[Machine Learning: análise preditiva nos dados de inspeção de alimentos usando MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Para obter instruções sobre como executar este notebook, siga o link.  

## <a name="track-an-application-in-the-yarn-ui"></a>Rastrear um aplicativo na interface do usuário do YARN

1. Inicie a interface do usuário do YARN. Selecione **yarn** em **painéis de cluster**.

    ![portal do Azure iniciar a interface do usuário do YARN](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > Alternativamente, também é possível iniciar a interface do usuário do YARN na interface do usuário do Ambari. Para iniciar a interface do usuário do amAmbari, selecione **Ambari página inicial** em **painéis do cluster**. Na interface do usuário do amAmbari, navegue até **yarn**  >  **links rápidos** > o Active Resource Manager > **interface do usuário do Resource Manager**.

2. Como você iniciou o trabalho do Spark usando notebooks Jupyter, o aplicativo tem o nome **remotesparkmagics** (o nome de todos os aplicativos iniciados a partir dos notebooks). Selecione a ID do aplicativo em relação ao nome do aplicativo para obter mais informações sobre o trabalho. Essa ação inicia a exibição do aplicativo.

    ![Servidor de histórico do Spark localizar ID do aplicativo Spark](./media/apache-spark-job-debugging/find-application-id1.png)

    Para esses aplicativos que são iniciados a partir dos Notebooks Jupyter, o status é sempre **em execução** até que você saia do notebook.

3. Na exibição de aplicativo, você pode fazer drill down para descobrir os contêineres associados ao aplicativo e os logs (stdout/stderr). Você também pode iniciar a interface do usuário do Spark clicando no link correspondente para a **URL de Rastreamento**, conforme mostrado abaixo.

    ![Servidor de histórico do Spark-baixar logs de contêiner](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Rastrear um aplicativo na interface do usuário do Spark

Na interface do usuário do Spark, é possível fazer drill down em trabalhos do Spark que são gerados pelo aplicativo iniciado anteriormente.

1. Para iniciar a interface do usuário do Spark, na exibição do aplicativo, selecione o link em relação à **URL de rastreamento**, conforme mostrado na captura de tela acima. Você pode ver todos os trabalhos do Spark que são iniciados pelo aplicativo em execução no Jupyter Notebook.

    ![Guia trabalhos do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. Selecione a guia **executores** para ver as informações de processamento e armazenamento de cada executor. Você também pode recuperar a pilha de chamadas selecionando o link de **despejo de thread** .

    ![Guia executores do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-spark-executors.png)

3. Selecione a guia **estágios** para ver os estágios associados ao aplicativo.

    ![Guia de estágios do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-apache-spark-stages.png "Exibir estágios do Spark")

    Cada estágio pode ter várias tarefas para as quais você pode exibir estatísticas de execução, como mostrado abaixo.

    ![Detalhes da guia de estágios do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-spark-stages-details.png "Exibir detalhes de estágios do Spark")

4. Na página de detalhes do estágio, você pode iniciar Visualização de DAG. Expanda o link **DAG Visualization** (Visualização de DAG) na parte superior da página, como mostrado abaixo.

    ![Exibir a visualização de DAG dos estágios do Spark](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    O DAG ou Grafo Acíclico Direto representa os diferentes estágios no aplicativo. Cada caixa azul no grafo representa uma operação do Spark iniciada do aplicativo.

5. Na página de detalhes do estágio, você também pode iniciar o modo de exibição de linha do tempo do aplicativo. Expanda o link **Event Timeline** (Linha do Tempo do Evento) na parte superior da página, como mostrado abaixo.

    ![Exibir linha do tempo de evento de estágios do Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    Essa imagem exibe os eventos do Spark na forma de uma linha do tempo. O modo de exibição de tempo está disponível em três níveis, entre trabalhos, dentro de um trabalho e dentro de um estágio. A imagem acima captura o modo de exibição de linha do tempo de um determinado estágio.

   > [!TIP]  
   > Se você selecionar a caixa de seleção **Enable zooming** (Habilitar zoom), poderá rolar para a esquerda e para a direita no modo de exibição de linha do tempo.

6. Outras guias na interface do usuário do Spark fornecem informações úteis sobre a instância do Spark.

   * Guia armazenamento – se seu aplicativo criar um RDD, você poderá encontrar informações na guia armazenamento.
   * Guia ambiente-essa guia fornece informações úteis sobre a instância do Spark, como:
     * Versão da escala
     * Diretório de log de eventos associado ao cluster
     * Número de núcleos de executor do aplicativo

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Encontrar informações sobre trabalhos concluídos usando o Servidor de Histórico do Spark

Quando um trabalho é concluído, as informações sobre ele são mantidas no Servidor de Histórico do Spark.

1. Para iniciar o servidor de histórico do Spark, na página **visão geral** , selecione **servidor de histórico do Spark** em **painéis do cluster**.

    ![portal do Azure iniciar o servidor de histórico do Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png "Iniciar Server1 do histórico do Spark")

   > [!TIP]  
   > Alternativamente, também é possível iniciar a interface do usuário do Servidor de Histórico do Spark na interface do usuário do Ambari. Para iniciar a interface do usuário do amAmbari, na folha visão geral, selecione **Ambari página inicial** em **painéis do cluster**. Na interface do usuário do amAmbari, navegue até **Spark2**  >  **links rápidos**  >  **Spark2 UI do servidor de histórico**.

2. Você verá todos os aplicativos concluídos listados. Selecione uma ID de aplicativo para fazer uma busca detalhada em um aplicativo para obter mais informações.

    ![Aplicativos concluídos do servidor de histórico do Spark](./media/apache-spark-job-debugging/view-completed-applications.png "Iniciar Server2 do histórico do Spark")

## <a name="see-also"></a>Consulte também

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](apache-spark-resource-manager.md)
* [Depure as tarefas do Spark do Apache usando o Extended History Server estendido](apache-azure-spark-history-server.md)
* [Depurar Apache Spark aplicativos com Azure Toolkit for IntelliJ por meio do SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
