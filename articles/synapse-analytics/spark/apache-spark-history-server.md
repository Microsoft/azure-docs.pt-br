---
title: Usar o servidor de histórico do Spark estendido para depurar aplicativos
description: Use o servidor de histórico do Spark estendido para depurar e diagnosticar aplicativos Spark no Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 10/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4471fed7b423fe05147db30afe57f6c845fe640e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670691"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Usar o servidor de histórico de Apache Spark estendido para depurar e diagnosticar Apache Spark aplicativos

Este artigo fornece orientação sobre como usar o servidor de histórico de Apache Spark estendido para depurar e diagnosticar aplicativos Spark concluídos e em execução.

A extensão inclui uma guia dados, guia gráfico e guia diagnóstico. Use a guia **dados** para verificar os dados de entrada e saída do trabalho do Spark. A guia **grafo** mostra o fluxo de dados e a reprodução do grafo de trabalho. A guia **diagnóstico** mostra a  **distorção de dados**, a **distorção de tempo** e a análise de uso de **executor**.

## <a name="access-the-apache-spark-history-server"></a>Acesse o servidor de histórico do Apache Spark

Apache Spark servidor de histórico é a interface do usuário da Web para aplicativos Spark concluídos e em execução. Você pode abrir a interface da Web do servidor de histórico de Apache Spark da análise de Synapse do Azure.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Abra a interface do usuário da Web do servidor de histórico do Spark do nó aplicativos do Apache Spark

1. Abra o [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Selecione **Monitor** e, em seguida, selecione **Apache Spark aplicativos**.

    ![Selecione monitor e, em seguida, selecione aplicativo Spark.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selecione um aplicativo e, em seguida, abra a **consulta de log** selecionando-o.

    ![Abrir janela de consulta de log.](./media/apache-spark-history-server/open-application-window.png)

4. Selecione **servidor de histórico do Spark** e, em seguida, a interface do usuário da Web do servidor de histórico do Spark aparecerá.

    ![Abra o servidor de histórico do Spark.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node"></a>Abrir a interface do usuário da Web do servidor de histórico do Spark do nó de dados

1. No seu notebook do Azure Synapse Studio, selecione **servidor de histórico do Spark** na célula saída de execução do trabalho ou no painel de status na parte inferior do documento do notebook. Selecione **Detalhes da sessão**.

   ![Inicie o servidor de histórico do Spark 1](./media/apache-spark-history-server/launch-history-server2.png "Iniciar o servidor de histórico do Spark")

2. Selecione **servidor de histórico do Spark** no painel deslizar.

   ![Inicie o servidor de histórico do Spark 2](./media/apache-spark-history-server/launch-history-server.png "Iniciar o servidor de histórico do Spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Explorar a guia dados no servidor de histórico do Spark

Selecione a ID do trabalho que você deseja exibir. Em seguida, selecione **dados** no menu ferramenta para obter a exibição de dados. Esta seção mostra como realizar várias tarefas na guia dados.

* Confira as **Entradas**, as **Saídas** e as **Operações de Tabelas** selecionando as guias separadamente.

    ![Dados para guias do aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Copie todas as linhas selecionando **copiar**.

    ![Dados para cópia do aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Salve todos os dados como arquivo CSV selecionando **CSV**.

    ![Dados para salvar aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Pesquise inserindo palavras-chave na **pesquisa** de campo. Os resultados da pesquisa são exibidos imediatamente.

    ![Dados para pesquisa de aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Selecione o cabeçalho de coluna para classificar a tabela, selecione o sinal de adição para expandir uma linha para mostrar mais detalhes ou selecione o sinal de menos para recolher uma linha.

    ![Dados para a tabela de aplicativos Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Baixe um único arquivo selecionando **Download parcial**. O arquivo selecionado é baixado para local. Se o arquivo não existir mais, uma nova guia será exibida com uma mensagem de erro.

    ![Dados para a linha de download do aplicativo Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Para copiar um caminho completo ou relativo, selecione as opções **Copiar caminho completo** ou **Copiar caminho relativo** que se expande no menu suspenso. Para Azure Data Lake Storage arquivos, **abra no Gerenciador de armazenamento do Azure** inicia Gerenciador de armazenamento do Azure e localiza a pasta quando você está conectado.

    ![Dados para o caminho de cópia do aplicativo Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selecione números de página abaixo da tabela para navegar pelas páginas quando houver muitas linhas a serem exibidas em uma página.

    ![Dados para a página do aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Focalize o ponto de interrogação ao lado dos **dados** para mostrar a dica de ferramenta ou selecione o ponto de interrogação para obter mais informações.

    ![Dados para o aplicativo Spark mais informações](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Envie comentários com problemas selecionando **fornecer comentários**.

    ![O grafo do Spark nos fornece comentários novamente](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Guia grafo no servidor de histórico de Apache Spark

Selecione a ID do trabalho que você deseja exibir. Em seguida, selecione **grafo** no menu ferramenta para obter o modo de exibição de gráfico de trabalho.

### <a name="overview"></a>Visão geral

Você pode ver uma visão geral do seu trabalho no grafo de trabalho gerado. Por padrão, o grafo mostra todos os trabalhos. Você pode filtrar essa exibição por **ID do trabalho**.

![Aplicativo Spark e ID do trabalho do grafo do trabalho](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Monitor

Por padrão, a exibição de **progresso** é selecionada. Você pode verificar o fluxo de dados selecionando **ler** ou **gravado** na lista suspensa **Exibir** .

![Vídeo do aplicativo Spark e do grafo de trabalho](./media/apache-spark-history-server/sparkui-graph-display.png)

O nó do grafo exibe as cores mostradas na legenda calor.

![Aplicativo Spark e grafo de trabalho calor](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Reprodução

Para reproduzir o trabalho, selecione **reprodução**. Você pode selecionar **parar** a qualquer momento para parar. As cores da tarefa mostram status diferentes ao executar novamente:

|Cor|Significado|
|-|-|
|Verde|Êxito: o trabalho foi concluído com êxito.|
|Laranja|Repetidas: instâncias de tarefas que falharam, mas não afetam o resultado final do trabalho. Essas tarefas tiveram instâncias duplicadas ou repetidas que poderão ser bem-sucedidas mais tarde.|
|Azul|Em execução: a tarefa está em execução.|
|Branca|Aguardando ou ignorado: a tarefa está aguardando para ser executada ou o estágio foi ignorado.|
|Vermelho|Falha: a tarefa falhou.|

A imagem a seguir mostra as cores de status verde, laranja e azul.

![Exemplo de cor do grafo de aplicativo e trabalho do Spark, executando](./media/apache-spark-history-server/sparkui-graph-color-running.png)

A imagem a seguir mostra as cores de status verde e branco.

![Exemplo de cor do grafo de aplicativo e trabalho do Spark, ignorar](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

A imagem a seguir mostra as cores de status vermelho e verde.

![Exemplo de cor de grafo de trabalho e aplicativo Spark, com falha](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> É permitida a reprodução de cada trabalho. Para trabalhos incompletos, não há suporte para reprodução.

### <a name="zoom"></a>Zoom

Use a rolagem do mouse para ampliar e reduzir no grafo do trabalho ou selecione **aplicar zoom para ajustá** -lo à tela.

![Ajuste de zoom do grafo de trabalho e aplicativo Spark](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Dicas de ferramenta

Focalize o nó do grafo para ver a dica de ferramenta quando houver tarefas com falha e selecione um estágio para abrir sua página de estágio.

![Dica de ferramenta de aplicativo Spark e grafo de trabalho](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Na guia gráfico de trabalho, os estágios têm uma dica de ferramenta e um pequeno ícone exibido se tiverem tarefas que atendam às seguintes condições:

|Condição|Descrição|
|-|-|
|Distorção de dados|tamanho de leitura de dados > tamanho médio de leitura de dados de todas as tarefas dentro deste estágio * 2 e tamanho de leitura de dados > 10 MB|
|Distorção de tempo|tempo de execução > tempo médio de execução de todas as tarefas dentro deste estágio * 2 e tempo de execução > 2 minutos|
   
![Ícone de distorção do grafo de trabalho e aplicativo Spark](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Descrição do nó do gráfico

O nó do gráfico de trabalho exibe as seguintes informações de cada estágio:

  * ID.
  * Nome ou descrição.
  * Número total de tarefas.
  * Dados lidos: tamanho de leitura a soma do tamanho de entrada e em ordem aleatória.
  * Gravação de dados: a soma do tamanho da saída e das gravações de ordem aleatória.
  * Tempo de execução: o tempo entre a hora de início da primeira tentativa e a hora de conclusão da última tentativa.
  * Contagem de linhas: a soma dos registros de entrada, registros de saída, registros de leitura aleatória e registros de gravação aleatória.
  * Progresso.

    > [!NOTE]  
    > Por padrão, o nó do grafo de trabalho exibe informações da última tentativa de cada estágio (exceto para o tempo de execução do estágio). No entanto, durante a reprodução, o nó do grafo mostra informações de cada tentativa.
    >  
    > O tamanho dos dados de leitura e gravação é 1MB = 1000 KB = 1000 * 1000 bytes.

### <a name="provide-feedback"></a>Fornecer comentários

Envie comentários com problemas selecionando **fornecer comentários**.

![Comentários do grafo de trabalho e aplicativo Spark](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Explore a guia diagnóstico no servidor de histórico Apache Spark

Para acessar a guia diagnóstico, selecione uma ID de trabalho. Em seguida, selecione **diagnóstico** no menu ferramenta para obter a exibição de diagnóstico de trabalho. A guia de diagnóstico inclui **Distorção de dados**, **Distorção de tempo** e **Análise de uso do executor**.

Confira as informações de **Distorção de dados**, **Distorção de tempo** e **Análise de uso do executor** selecionando as respectivas guias.

![Guia de distorção de dados de diagnóstico SparkUI novamente](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorção de dados

Quando você seleciona a guia **distorção de dados** , as tarefas distorcidas correspondentes são exibidas com base nos parâmetros especificados.

* **Especificar Parâmetros** – a primeira seção exibe os parâmetros, que são usados para detectar a Distorção de dados. A regra padrão é: a leitura dos dados da tarefa é maior que três vezes a média de dados da tarefa lida e a leitura dos dados da tarefa é maior que 10 MB. Se você quiser definir sua própria regra para tarefas distorcidas, poderá escolher seus parâmetros. As seções de **caractere** de inclinação e distorção **inclinadas** são atualizadas de acordo.

* **Fase Distorcida** – a segunda seção exibe as fases que têm tarefas distorcidas que atendem aos critérios especificados acima. Se houver mais de uma tarefa distorcida em uma fase, a tabela de fase distorcida exibirá apenas a tarefa mais distorcida (por exemplo, com os maiores dados para distorção de dados).

    ![Guia de distorção de dados de diagnóstico sparkui](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Gráfico de distorção** – quando uma linha na tabela de estágio de distorção é selecionada, o gráfico de distorção exibe mais detalhes de distribuição de tarefas com base no tempo de leitura e de execução de dados. As tarefas distorcidas são marcadas em vermelho e as normais são marcadas em azul. O gráfico exibe até 100 tarefas de exemplo e os detalhes da tarefa são exibidos no painel inferior direito.

    ![gráfico de distorção sparkui para o estágio 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorção de tempo

A guia **Distorção de Tempo** exibe tarefas distorcidas com base no tempo de execução da tarefa.

* **Especificar Parâmetros** – a primeira seção exibe os parâmetros, que são usados para detectar a Distorção de tempo. Os critérios padrão para detectar a distorção de tempo são: o tempo de execução da tarefa é maior do que três vezes o tempo médio de execução e o tempo de execução da tarefa é maior que 30 segundos. Você pode alterar os parâmetros com base em suas necessidades. A **Fase Distorcida** e o **Gráfico de Distorção** exibem as informações sobre as fases e as tarefas correspondentes, assim como a guia **Distorção de Dados** acima.

* Selecione **distorção de tempo** e o resultado filtrado é exibido na seção de **estágio distorcido** de acordo com os parâmetros definidos na seção **especificar parâmetros**. Selecione um item na seção **estágio inclinado** , o gráfico correspondente é exibido em section3 e os detalhes da tarefa são exibidos no painel inferior direito.

    ![seção de distorção de tempo de diagnóstico sparkui](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Análise de uso do executor

O grafo de uso do executor visualiza a alocação e o status de execução do executor de trabalho do Spark.  

1. Selecione **análise de uso de executor**, quatro tipos de curvas de uso de executor são rascunhos, incluindo **executores alocados**, **execuções em execução**, **executores ociosos** e **instâncias de executor máximo**. Para executores alocados, cada evento "executor adicionado" ou "executor removido" aumenta ou diminui os executores alocados. Você pode verificar "Linha do tempo do evento" na guia "Trabalhos" para fazer mais comparações.

   ![guia executores de diagnóstico do sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selecione o ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os rascunhos.

    ![sparkui diagnostica o gráfico Select](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Problemas conhecidos

Os dados de entrada/saída usando conjuntos de dados distribuídos resilientes (RDDs) não são mostrados na guia Data.

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](../overview-what-is.md)
- [Documentação do .NET para Apache Spark](/dotnet/spark)

