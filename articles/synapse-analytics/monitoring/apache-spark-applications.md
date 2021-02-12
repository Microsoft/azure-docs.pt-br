---
title: Monitorar Apache Spark aplicativos usando o Synapse Studio
description: Use o Synapse Studio para monitorar seus aplicativos Apache Spark.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 00407b4a55b3c074fa77d4c3664f6e218c23fe21
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584492"
---
# <a name="use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Use o Synapse Studio para monitorar seus aplicativos Apache Spark

Com o Azure Synapse Analytics, você pode usar Apache Spark para executar blocos de anotações, trabalhos e outros tipos de aplicativos em seus pools de Apache Spark em seu espaço de trabalho.

Este artigo explica como monitorar seus aplicativos Apache Spark, permitindo que você fique atento ao status, aos problemas e ao progresso mais recentes.

Este tutorial cobre as seguintes tarefas:

* Monitorar a execução do aplicativo Apache Spark
* Exibir Apache Spark aplicativo concluído
* Exibir Apache Spark aplicativo cancelado
* Falha na depuração Apache Spark aplicativo

## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar este tutorial, verifique se você atende aos seguintes requisitos:

- Um espaço de trabalho do Synapse Studio. Para obter instruções, consulte [criar um espaço de trabalho do Synapse Studio](../../machine-learning/how-to-manage-workspace.md#create-a-workspace).

- Um pool do Apache Spark.

## <a name="view-apache-spark-applications"></a>Exibir Apache Spark aplicativos 
Você pode exibir todos os aplicativos de Apache Spark de **monitorar**  ->  **Apache Spark aplicativos**.
   ![aplicativos Apache Spark](./media/how-to-monitor-spark-applications/apache-spark-applications.png)

## <a name="view-completed-apache-spark-application"></a>Exibir Apache Spark aplicativo concluído

Abra o **Monitor** e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os aplicativos Apache Spark concluídos, selecione o aplicativo Apache Spark e exiba os detalhes.

  ![selecionar trabalho concluído](./media/how-to-monitor-spark-applications/select-completed-job.png)

1. Verifique as **tarefas concluídas**, o **status** e a **duração total**.

2. Atualizar consulta de log.

3. Abra Apache Spark link do servidor de histórico clicando em **servidor de histórico do Spark**.

4. Verifique as informações de **Resumo** .

5. Verifique os **logs**. Você pode selecionar diferentes tipos de logs na lista suspensa, e pode baixar as informações de log clicando em **baixar logs** e marcar a caixa de seleção de **erros e avisos de filtro** para filtrar os erros e avisos necessários.

6. Você pode ver uma visão geral do seu trabalho no grafo de trabalho gerado. Por padrão, o grafo mostra todos os trabalhos. Você pode filtrar essa exibição por **ID do trabalho**.

7. Por padrão, a exibição de **progresso** é selecionada. Você pode verificar o fluxo de dados selecionando **progresso** / **leitura** / **gravada** / **duração** na lista suspensa **Exibir** .

8. Para reproduzir o trabalho, clique no botão **reproduzir** . Você pode clicar no botão **parar** a qualquer momento para parar.

9. Use a barra de rolagem ou rolagem do mouse para ampliar e reduzir o grafo do trabalho, você também pode selecionar **zoom para ajustar** -se à tela.

10. O nó do gráfico de trabalho exibe as seguintes informações de cada estágio:

    * ID.

    * Nome ou descrição.

    * Número total de tarefas.

    * Dados lidos: tamanho de leitura a soma do tamanho de entrada e em ordem aleatória.

    * Gravação de dados: a soma do tamanho da saída e das gravações de ordem aleatória.

    * Tempo de execução: o tempo entre a hora de início da primeira tentativa e a hora de conclusão da última tentativa.

    * Contagem de linhas: a soma dos registros de entrada, registros de saída, registros de leitura aleatória e registros de gravação aleatória.

    * Progresso.

     ![Exibir trabalho concluído](./media/how-to-monitor-spark-applications/view-completed-job.png)
    
11. Clique em **Exibir detalhes** no grafo, os detalhes do estágio serão mostrados.

    ![detalhes do estágio](./media/how-to-monitor-spark-applications/details-for-stage.png)


## <a name="monitor-running-apache-spark-application"></a>Monitorar a execução do aplicativo Apache Spark

Abra o **Monitor** e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os Apache Spark aplicativos que estão em execução, selecione o Apache Spark aplicativo de envio e exiba os detalhes. Se o aplicativo Apache Spark ainda estiver em execução, você poderá monitorar o progresso.

   ![selecionar trabalho em execução](./media/how-to-monitor-spark-applications/select-running-job.png)

1. Verifique as **tarefas concluídas**, o **status** e a **duração total**.

2. **Cancele** o aplicativo Apache Spark.

3. **Atualizar** Consulta de log.

4. Clique no botão de **interface do usuário do Spark** para ir para a página do trabalho do Spark.

5. Exiba o grafo. Você pode ver uma visão geral do seu trabalho no grafo de trabalho gerado. Consulte a etapa 6, 7, 8, 9, 10 da [exibição concluída Apache Spark aplicativo](#view-completed-apache-spark-application).

6. Verifique as informações de **Resumo** .

7. Verifique o diagnóstico na guia **diagnóstico** .

8. Verifique os **logs** nesta guia. Você pode selecionar diferentes tipos de logs na lista suspensa, e pode baixar as informações de log clicando em **baixar logs** e marcar a caixa de seleção de **erros e avisos de filtro** para filtrar os erros e avisos necessários.

    ![Exibir trabalho em execução](./media/how-to-monitor-spark-applications/view-running-job.png)

## <a name="view-canceled-apache-spark-application"></a>Exibir Apache Spark aplicativo cancelado

Abra o **Monitor** e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os aplicativos Apache Spark cancelados, selecione o aplicativo Apache Spark e exiba os detalhes.

 ![selecionar trabalho cancelado](./media/how-to-monitor-spark-applications/select-cancelled-job.png) 

1. Verifique as **tarefas concluídas**, o **status** e a **duração total**.

2. Atualize a consulta de log.

3. Abra o link do servidor de histórico do Apache clicando em **servidor de histórico do Spark**.

4. Exiba o grafo. Você pode ver uma visão geral do seu trabalho no grafo de trabalho gerado. Consulte a etapa 6, 7, 8, 9, 10 da [exibição concluída Apache Spark aplicativo](#view-completed-apache-spark-application).

5. Verifique as informações de **Resumo** .

6. Verifique os **logs**. Você pode selecionar diferentes tipos de logs na lista suspensa, e pode baixar as informações de log clicando em **baixar** logs e marcar a caixa de seleção de **erros e avisos de filtro** para filtrar os erros e avisos necessários.

7. Clique em **Exibir detalhes** no grafo, os detalhes do estágio serão mostrados.

   ![Exibir trabalho cancelado](./media/how-to-monitor-spark-applications/view-cancelled-job.png)

## <a name="debug-failed-apache-spark-application"></a>Falha na depuração Apache Spark aplicativo

Abra o **Monitor** e, em seguida, selecione **Apache Spark aplicativos**. Para exibir os detalhes sobre os aplicativos Apache Spark com falha, selecione o aplicativo Apache Spark e exiba os detalhes.

![selecionar trabalho com falha](./media/how-to-monitor-spark-applications/select-failed-job.png)

1. Verifique as **tarefas concluídas**, o **status** e a **duração total**.

2. Atualizar consulta de log.

3. Abra Apache Spark link do servidor de histórico clicando em **servidor de histórico do Spark**.

4. Exiba o grafo. Você pode ver uma visão geral do seu trabalho no grafo de trabalho gerado. Consulte a etapa 6, 7, 8, 9, 10 da [exibição concluída Apache Spark aplicativo](#view-completed-apache-spark-application)

5. Verifique as informações de **Resumo** .

6. Verifique as informações do erro.

   ![informações do trabalho com falha](./media/how-to-monitor-spark-applications/failed-job-info.png)

## <a name="compare-apache-spark-applications"></a>Comparar aplicativos Apache Spark
Clique no botão **comparar aplicativos** e escolha um aplicativo para comparar o desempenho. os usuários podem ver de forma intuitiva a diferença entre os dois aplicativos.

![comparar aplicativos](./media/how-to-monitor-spark-applications/compare-applications.png)

1. Use o mouse para focalizar um aplicativo e, em seguida, o ícone **comparar aplicativos** será exibido.

2. Clique no ícone **comparar aplicativos** e a página comparar aplicativos será exibida.

3. Clique no botão **escolher aplicativo** para abrir a página **escolher aplicativo de comparação** .

4. Ao escolher o aplicativo de comparação, os usuários precisam inserir a URL do aplicativo ou escolher na lista recorrente. Em seguida, clique no botão **OK** . 

   ![escolher aplicativo de comparação](./media/how-to-monitor-spark-applications/choose-comparison-application.png)

5. O resultado da comparação será exibido na página comparar aplicativos.

   ![resultado da comparação](./media/how-to-monitor-spark-applications/comparison-result.png)


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o monitoramento de execuções de pipeline, consulte o artigo [monitorar execuções de pipeline usando o Synapse Studio](how-to-monitor-pipeline-runs.md) .
