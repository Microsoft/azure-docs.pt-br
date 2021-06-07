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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670691"
---
# <a name="use-extended-apache-spark-history-server-to-debug-and-diagnose-apache-spark-applications"></a>Usar o servidor de histórico do Apache Spark estendido para depurar e diagnosticar aplicativos Apache Spark

Este artigo fornece diretrizes de como usar o servidor de histórico do Apache Spark estendido para depurar e diagnosticar aplicativos Spark concluídos e em execução.

A extensão inclui guias de dados, de grafo e de diagnóstico. Use a guia **Dados** para verificar os dados de entrada e de saída do trabalho do Spark. A guia **Grafo** mostra os fluxos de dados e a reprodução do grafo do trabalho. A guia **Diagnóstico** mostra a **Distorção de Dados**, a **Distorção de Tempo** e a **Análise de Uso do Executor**.

## <a name="access-the-apache-spark-history-server"></a>Acesse o servidor de histórico do Apache Spark

O servidor de histórico do Apache Spark é a interface do usuário da Web para aplicativos Spark concluídos e em execução. Você pode abrir a interface da Web do servidor de histórico do Apache Spark no Azure Synapse Analytics.

### <a name="open-the-spark-history-server-web-ui-from-apache-spark-applications-node"></a>Abrir a interface do usuário da Web do servidor de histórico do Spark usando o nó de aplicativos do Apache Spark

1. Abra o [Azure Synapse Analytics](https://web.azuresynapse.net/).

2. Selecione **Monitorar** e escolha **Aplicativos Apache Spark**.

    ![Selecionar Monitorar e Aplicativo Spark.](./media/apache-spark-history-server/click-monitor-spark-application.png)

3. Selecione um aplicativo e abra a **Consulta de log** selecionando-a.

    ![Abrir janela de consulta de log.](./media/apache-spark-history-server/open-application-window.png)

4. Selecione **Servidor de histórico do Spark** e a interface do usuário da Web do servidor de histórico do Spark aparecerá.

    ![Abrir o servidor de histórico do Spark.](./media/apache-spark-history-server/open-spark-history-server.png)

### <a name="open-the-spark-history-server-web-ui-from-data-node&quot;></a>Abrir a interface do usuário da Web do servidor de histórico do Spark usando o nó de Dados

1. Em seu notebook do Azure Synapse Studio, selecione **Servidor de histórico do Spark** na célula de saída de execução do trabalho ou no painel de status na parte inferior do documento do notebook. Selecione **Detalhes da sessão**.

   ![Iniciar o servidor de histórico do Spark 1](./media/apache-spark-history-server/launch-history-server2.png &quot;Iniciar o servidor de histórico do Spark")

2. Selecione **Servidor de histórico do Spark** no painel deslizante.

   ![Iniciar o servidor de histórico do Spark 2](./media/apache-spark-history-server/launch-history-server.png "Iniciar o servidor de histórico do Spark")

## <a name="explore-the-data-tab-in-spark-history-server"></a>Explorar a guia Dados no servidor de histórico do Spark

Selecione a ID do trabalho que deseja ver. Selecione **Dados** no menu de ferramentas para acessar a exibição de dados. Esta seção mostra como realizar várias tarefas na guia Dados.

* Confira as **Entradas**, as **Saídas** e as **Operações de Tabelas** selecionando as guias separadamente.

    ![Guias de dados para o aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-tabs.png)

* Copie todas as linhas selecionando **Copiar**.

    ![Cópia de dados para o aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-copy.png)

* Salve todos os dados como um arquivo CSV selecionando **csv**.

    ![Salvar dados para o aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-save.png)

* Pesquise inserindo palavras-chave no campo **Pesquisar**. Os resultados da pesquisa são exibidos imediatamente.

    ![Pesquisa de dados para o aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-search.png)

* Clique no cabeçalho da coluna para classificar a tabela, selecione o sinal de adição para expandir uma linha e mostrar mais detalhes ou o sinal de subtração para recolher uma linha.

    ![Tabela de dados para o aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-table.png)

* Baixe apenas um arquivo selecionando **Download parcial**. O arquivo selecionado será baixado para o local. Se o arquivo não existir mais, será exibida uma nova guia com uma mensagem de erro.

    ![Download de linha de dados para o aplicativo Spark](./media/apache-spark-history-server/sparkui-data-download-row.png)

* Para copiar um caminho completo ou relativo, selecione as opções **Copiar Caminho Completo** ou **Copiar Caminho Relativo** no menu suspenso. Para arquivos do Azure Data Lake Storage, **Abrir no Gerenciador de Armazenamento do Azure** iniciará o Gerenciador de Armazenamento do Azure e localizará a pasta quando você estiver conectado.

    ![Caminho de cópia de dados para o aplicativo Spark](./media/apache-spark-history-server/sparkui-data-copy-path.png)

* Selecione os números de página sob a tabela para navegar pelas páginas quando houver linhas demais para serem exibidas em uma página.

    ![Página de dados para o aplicativo Spark](./media/apache-spark-history-server/apache-spark-data-page.png)

* Focalize o ponto de interrogação ao lado de **Dados** para exibir a dica de ferramenta ou selecione o ponto de interrogação para obter mais informações.

    ![Mais informações de dados para o aplicativo Spark](./media/apache-spark-history-server/sparkui-data-more-info.png)

* Envie comentários sobre problemas selecionando **Fornecer comentários**.

    ![Fornecer comentários no grafo do Spark novamente](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="graph-tab-in-apache-spark-history-server"></a>Guia Grafo no servidor de histórico do Apache Spark

Selecione a ID do trabalho que deseja ver. Em seguida, selecione **Grafo** no menu de ferramentas para acessar a exibição de grafo do trabalho.

### <a name="overview"></a>Visão geral

Você pode ter uma visão geral do trabalho no grafo do trabalho gerado. Por padrão, o grafo mostra todos os trabalhos. Você pode filtrar a exibição por **ID do Trabalho**.

![Aplicativo Spark e ID do trabalho do grafo do trabalho](./media/apache-spark-history-server/apache-spark-graph-jobid.png)

### <a name="display"></a>Monitor

Por padrão, a exibição de **Progresso** é selecionada. Você pode verificar o fluxo de dados selecionando **Lido** ou **Gravado** na lista suspensa **Exibir**.

![Aplicativo Spark e exibição do grafo do trabalho](./media/apache-spark-history-server/sparkui-graph-display.png)

O nó do grafo exibe as cores mostradas na legenda do mapa de calor.

![Aplicativo Spark e mapa de calor do grafo do trabalho](./media/apache-spark-history-server/sparkui-graph-heatmap.png)

### <a name="playback"></a>Reprodução

Para reproduzir o trabalho, selecione **Reprodução**. Selecione **Parar** a qualquer momento para parar. As cores da tarefa mostram status diferentes durante a reprodução:

|Cor|Significado|
|-|-|
|Verde|Bem-sucedido: o trabalho foi concluído com êxito.|
|Laranja|Repetido: instâncias de tarefas que falharam, mas não afetam o resultado final do trabalho. Essas tarefas tiveram instâncias duplicadas ou repetidas que poderão ser bem-sucedidas mais tarde.|
|Azul|Em execução: a tarefa está em execução.|
|Branca|Aguardando ou ignorado: a tarefa está esperando para ser executada ou a fase foi ignorada.|
|Vermelho|Falha: a tarefa falhou.|

A imagem a seguir mostra as cores de status verde, laranja e azul.

![Aplicativo Spark e exemplo de cor do grafo do trabalho, em execução](./media/apache-spark-history-server/sparkui-graph-color-running.png)

A imagem a seguir mostra as cores de status verde e branco.

![Aplicativo Spark e exemplo de cor do grafo do trabalho, ignorar](./media/apache-spark-history-server/sparkui-graph-color-skip.png)

A imagem a seguir mostra as cores de status vermelho e verde.

![Aplicativo Spark e exemplo de cor do grafo do trabalho, falha](./media/apache-spark-history-server/sparkui-graph-color-failed.png)

> [!NOTE]  
> É permitida a reprodução de cada trabalho. Para trabalhos incompletos, a reprodução não tem suporte.

### <a name="zoom"></a>Zoom

Use a rolagem do mouse para ampliar e reduzir o grafo do trabalho ou selecione **Aplicar Zoom para Ajustar** para fazer com que ele se ajuste à tela.

![Aplicativo Spark e grafo do trabalho com zoom para ajustar](./media/apache-spark-history-server/sparkui-graph-zoom2fit.png)

### <a name="tooltips"></a>Dicas de ferramenta

Focalize o nó do grafo para ver a dica de ferramenta quando houver tarefas com falha e selecione uma fase para abrir a página referente a ela.

![Aplicativo Spark e dica de ferramenta do grafo do trabalho](./media/apache-spark-history-server/sparkui-graph-tooltip.png)

Na guia do grafo do trabalho, as fases têm uma dica de ferramenta e um pequeno ícone exibido se houver tarefas que atendam às seguintes condições:

|Condição|Descrição|
|-|-|
|Distorção de dados|tamanho dos dados lidos > tamanho médio dos dados lidos de todas as tarefas dentro desta fase * 2 e tamanho dos dados lidos > 10 MB|
|Distorção de tempo|tempo de execução > tempo médio de execução de todas as tarefas dentro desta fase * 2 e tempo de execução > 2 minutos|
   
![Aplicativo Spark e ícone de distorção do grafo do trabalho](./media/apache-spark-history-server/sparkui-graph-skew-icon.png)

### <a name="graph-node-description"></a>Descrição do nó do grafo

O nó do grafo do trabalho exibe as seguintes informações de cada fase:

  * ID.
  * Nome ou descrição.
  * Número total de tarefas.
  * Dados lidos: tamanho de leitura a soma do tamanho de entrada e em ordem aleatória.
  * Gravação de dados: a soma do tamanho de saída e do tamanho das gravações em ordem aleatória.
  * Tempo de execução: o tempo entre a hora de início da primeira tentativa e a hora de conclusão da última tentativa.
  * Contagem de linhas: a soma dos registros de entrada, registros de saída, registros de leitura aleatória e registros de gravação aleatória.
  * Progresso.

    > [!NOTE]  
    > Por padrão, o nó do grafo do trabalho exibe informações da última tentativa de cada fase (exceto pelo tempo de execução da fase). No entanto, durante a reprodução, o nó do grafo mostra informações sobre cada tentativa.
    >  
    > O tamanho dos dados de leitura e gravação é de 1MB = 1000 KB = 1000 * 1000 bytes.

### <a name="provide-feedback"></a>Fornecer comentários

Envie comentários sobre problemas selecionando **Fornecer comentários**.

![Aplicativo Spark e comentários do grafo do trabalho](./media/apache-spark-history-server/sparkui-graph-feedback.png)

## <a name="explore-the-diagnosis-tab-in-apache-spark-history-server"></a>Explorar a guia Diagnóstico no servidor de histórico do Apache Spark

Para acessar a guia Diagnóstico, selecione uma ID de trabalho. Em seguida, selecione **Diagnóstico** no menu de ferramentas para acessar a exibição de Diagnóstico do trabalho. A guia de diagnóstico inclui **Distorção de dados**, **Distorção de tempo** e **Análise de uso do executor**.

Confira as informações de **Distorção de dados**, **Distorção de tempo** e **Análise de uso do executor** selecionando as respectivas guias.

![Guia de distorção de dados do diagnóstico da SparkUI novamente](./media/apache-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorção de dados

Quando você seleciona a guia **Distorção de Dados**, as tarefas distorcidas correspondentes são exibidas com base nos parâmetros especificados.

* **Especificar Parâmetros** – a primeira seção exibe os parâmetros, que são usados para detectar a Distorção de dados. A regra padrão é: a Leitura de Dados da Tarefa é maior que três vezes a média da leitura de dados da tarefa e a leitura de dados da tarefa é superior a 10 MB. Se quiser definir sua regra para tarefas com distorção, você poderá escolher seus parâmetros. As seções de **Fase Distorcida** e **Gráfico de Distorção** são atualizadas de acordo.

* **Fase Distorcida** – a segunda seção exibe as fases que têm tarefas distorcidas que atendem aos critérios especificados acima. Se houver mais de uma tarefa distorcida em uma fase, a tabela de fase distorcida exibirá apenas a tarefa mais distorcida (por exemplo, com os maiores dados para distorção de dados).

    ![guia de distorção de dados do diagnóstico da sparkui](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

* **Gráfico de Distorção** – quando uma linha na tabela da fase com distorção é selecionada, o gráfico de distorção exibe mais detalhes de distribuições da tarefa com base na leitura de dados e no tempo de execução. As tarefas distorcidas são marcadas em vermelho e as normais são marcadas em azul. O gráfico exibe até 100 tarefas de exemplo e os detalhes da tarefa são exibidos no painel inferior direito.

    ![gráfico de distorção de sparkui para a fase 10](./media/apache-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorção de tempo

A guia **Distorção de Tempo** exibe tarefas distorcidas com base no tempo de execução da tarefa.

* **Especificar Parâmetros** – a primeira seção exibe os parâmetros, que são usados para detectar a Distorção de tempo. Os critérios padrão para detectar a distorção de tempo são: o tempo de execução da tarefa é maior do que três vezes o tempo médio de execução e o tempo de execução da tarefa é maior que 30 segundos. Você pode alterar os parâmetros com base em suas necessidades. A **Fase Distorcida** e o **Gráfico de Distorção** exibem as informações sobre as fases e as tarefas correspondentes, assim como a guia **Distorção de Dados** acima.

* Selecione **Distorção de Tempo** e o resultado filtrado será exibido na seção **Fase Distorcida** de acordo com os parâmetros definidos na seção **Especificar Parâmetros**. Ao selecionar um item na seção **Fase Distorcida**, o gráfico correspondente será exibido na seção 3 e os detalhes da tarefa serão exibidos no painel inferior direito.

    ![seção de distorção de tempo no diagnóstico da sparkui](./media/apache-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis"></a>Análise de uso do executor

O Grafo de Uso do Executor visualiza a alocação do executor do trabalho do Spark e o status da execução.  

1. Selecione **Análise de Uso do Executor** e quatro curvas de tipos sobre o uso do executor serão traçadas, incluindo **Executores Alocados**, **Executores em Execução**, **Executores Ociosos** e **Máximo de Instâncias de Executor**. Para os executores alocados, cada evento de "Executor adicionado" ou "Executor removido" aumentará ou diminuirá os executores alocados. Você pode verificar "Linha do tempo do evento" na guia "Trabalhos" para fazer mais comparações.

   ![guia executores no diagnóstico da sparkui](./media/apache-spark-history-server/sparkui-diagnosis-executors.png)

2. Selecione o ícone colorido para selecionar ou desmarcar o conteúdo correspondente em todos os rascunhos.

    ![seleção de gráfico no diagnóstico da sparkui](./media/apache-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="known-issues"></a>Problemas conhecidos

Dados de entrada/saída usando RDDs (conjuntos de dados distribuídos resilientes) não são mostrados na guia de dados.

## <a name="next-steps"></a>Próximas etapas

- [Azure Synapse Analytics](../overview-what-is.md)
- [Documentação do .NET para Apache Spark](/dotnet/spark)

