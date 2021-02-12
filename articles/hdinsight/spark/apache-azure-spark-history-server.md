---
title: Usar os recursos estendidos no servidor de histórico de Apache Spark para depurar aplicativos – Azure HDInsight
description: Use os recursos estendidos no servidor de histórico de Apache Spark para depurar e diagnosticar aplicativos Spark – Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/25/2019
ms.openlocfilehash: 1d53d9e9ee2a7fa6588ea0993b3bebdb2a287351
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941047"
---
# <a name="use-the-extended-features-of-the-apache-spark-history-server-to-debug-and-diagnose-spark-applications"></a>Usar os recursos estendidos do servidor de histórico de Apache Spark para depurar e diagnosticar aplicativos Spark

Este artigo mostra como usar os recursos estendidos do servidor de histórico de Apache Spark para depurar e diagnosticar aplicativos Spark concluídos ou em execução. A extensão inclui uma guia **dados** , uma guia **gráfico** e uma guia **diagnóstico** . Na guia **dados** , você pode verificar os dados de entrada e saída do trabalho do Spark. Na guia **grafo** , você pode verificar o fluxo de dados e reproduzir o grafo do trabalho. Na guia **diagnóstico** , você pode consultar os recursos de **distorção de dados**, **distorção de tempo** e **análise de uso de executor** .

## <a name="get-access-to-the-spark-history-server"></a>Obter acesso ao servidor de histórico do Spark

O servidor de histórico do Spark é a interface do usuário da Web para aplicativos Spark concluídos e em execução. Você pode abri-lo do portal do Azure ou de uma URL.

### <a name="open-the-spark-history-server-web-ui-from-the-azure-portal"></a>Abra a interface do usuário da Web do servidor de histórico do Spark no portal do Azure

1. No [portal do Azure](https://portal.azure.com/), abra o cluster Spark. Para obter mais informações, consulte [Listar e mostrar clusters](../hdinsight-administer-use-portal-linux.md#showClusters).
2. Em **painéis de cluster**, selecione  **servidor de histórico do Spark**. Quando solicitado, insira as credenciais de administrador para o cluster Spark.

    ![Inicie o servidor de histórico do Spark por meio do portal do Azure.](./media/apache-azure-spark-history-server/azure-portal-dashboard-spark-history.png "Servidor de Histórico do Spark")

### <a name="open-the-spark-history-server-web-ui-by-url"></a>Abrir a interface do usuário da Web do servidor de histórico do Spark por URL

Abra o servidor de histórico do Spark navegando até `https://CLUSTERNAME.azurehdinsight.net/sparkhistory` , em que **ClusterName** é o nome do cluster Spark.

A interface do usuário da Web do servidor de histórico do Spark pode ser semelhante a esta imagem:

![A página do servidor de histórico do Spark.](./media/apache-azure-spark-history-server/hdinsight-spark-history-server.png)

## <a name="use-the-data-tab-in-the-spark-history-server"></a>Usar a guia dados no servidor de histórico do Spark

Selecione a ID do trabalho e, em seguida, selecione **dados** no menu ferramenta para ver a exibição de dados.

+ Examine as **entradas**, as **saídas** e **as operações de tabela** selecionando as guias individuais.

    ![Guias de dados na página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-tabs.png)

+ Copie todas as linhas selecionando o botão **copiar** .

    ![Copie dados na página do aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-copy.png)

+ Salve todos os dados como um. Arquivo CSV selecionando o botão **CSV** .

    ![Salve os dados como um. Arquivo CSV da página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-save.png)

+ Pesquise os dados inserindo palavras-chave no campo de **pesquisa** . Os resultados da pesquisa serão exibidos imediatamente.

    ![Pesquisar dados na página de aplicativo dados para Spark.](./media/apache-azure-spark-history-server/apache-spark-data-search.png)

+ Selecione o cabeçalho da coluna para classificar a tabela. Selecione o sinal de adição para expandir uma linha para mostrar mais detalhes. Selecione o sinal de menos para recolher uma linha.

    ![A tabela de dados na página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-table.png)

+ Baixe um único arquivo selecionando o botão **Download parcial** à direita. O arquivo selecionado será baixado localmente. Se o arquivo não existir mais, isso abrirá uma nova guia para mostrar as mensagens de erro.

    ![A linha de download de dados na página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/sparkui-data-download-row.png)

+ Copie um caminho completo ou um caminho relativo selecionando a opção **Copiar caminho completo** ou **Copiar caminho relativo** , que se expande no menu de download. Para Azure Data Lake Storage arquivos, selecione **abrir no Gerenciador de armazenamento do Azure** para iniciar Gerenciador de armazenamento do Azure e localize a pasta após a entrada.

    ![Copie o caminho completo e copie as opções de caminho relativo na página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/sparkui-data-copy-path.png)

+ Se houver muitas linhas a serem exibidas em uma única página, selecione os números de página na parte inferior da tabela para navegar.

    ![Números de página na página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/apache-spark-data-page.png)

+ Para obter mais informações, passe o mouse sobre ou selecione o ponto de interrogação ao lado de **dados para o aplicativo Spark** para mostrar a dica de ferramenta.

    ![Obtenha mais informações da página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/sparkui-data-more-info.png)

+  Para enviar comentários sobre problemas, selecione **fornecer comentários**.

    ![Forneça comentários da página dados para o aplicativo Spark.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-graph-tab-in-the-spark-history-server"></a>Usar a guia grafo no servidor de histórico do Spark

+ Selecione a ID do trabalho e, em seguida, selecione **grafo** no menu ferramenta para ver o grafo do trabalho. Por padrão, o grafo mostrará todos os trabalhos. Filtre os resultados usando o menu suspenso **ID do trabalho** .

    ![O menu suspenso ID do trabalho no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/apache-spark-graph-jobid.png)

+ O **progresso** é selecionado por padrão. Verifique o fluxo de dados selecionando **ler** ou **gravado** no menu suspenso **Exibir** .

    ![Verifique o fluxo de dados no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/sparkui-graph-display.png)

+ A cor do plano de fundo de cada tarefa corresponde a um mapa de calor.

   ![O mapa de calor no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/sparkui-graph-heatmap.png)


    |Color |DESCRIÇÃO |
    |---|---|
    |Verde|o trabalho foi concluído com êxito.|
    |Laranja|A tarefa falhou, mas isso não afeta o resultado final do trabalho. Essas tarefas têm instâncias duplicadas ou repetidas que podem ter sucesso mais tarde.|
    |Azul|a tarefa está em execução.|
    |Branca|a tarefa está aguardando para ser executada ou a fase foi ignorada.|
    |Vermelho|a tarefa falhou.|

     ![Executando uma tarefa no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/sparkui-graph-color-running.png)

     Os estágios ignorados são exibidos em branco.
    ![Uma tarefa ignorada no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/sparkui-graph-color-skip.png)

    ![Uma tarefa com falha no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/sparkui-graph-color-failed.png)

     > [!NOTE]  
     > A reprodução está disponível para trabalhos concluídos. Selecione o botão de **reprodução** para executar o trabalho de volta. Pare o trabalho a qualquer momento selecionando o botão parar. Quando um trabalho é reproduzido, cada tarefa exibirá seu status por cor. Não há suporte para reprodução de trabalhos incompletos.

+ Role para ampliar ou reduzir o grafo do trabalho ou selecione **aplicar zoom para ajustá** -lo à tela.

    ![Selecione zoom para ajustar na página do grafo de trabalho do aplicativo Spark &.](./media/apache-azure-spark-history-server/sparkui-graph-zoom2fit.png)

+ Quando as tarefas falharem, passe o mouse sobre o nó do grafo para ver a dica de ferramenta e selecione o estágio para abri-lo em uma nova página.

    ![Exiba a dica de ferramenta no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/sparkui-graph-tooltip.png)

+ No aplicativo Spark & página grafo de trabalho, os estágios exibirão dicas de ferramenta e ícones pequenos se as tarefas atenderem a essas condições:
  + Distorção de dados: tamanho de leitura de dados > tamanho médio de leitura de dados de todas as tarefas dentro deste estágio * 2 *e* tamanho de leitura de dados > 10 MB.
  + Distorção de tempo: tempo de execução > tempo médio de execução de todas as tarefas dentro deste estágio * 2 *e* tempo de execução > 2 minutos.

    ![O ícone de tarefa distorcida na página do grafo de trabalho do aplicativo Spark &.](./media/apache-azure-spark-history-server/sparkui-graph-skew-icon.png)

+ O nó do gráfico de trabalho exibirá as seguintes informações sobre cada estágio:
  + ID
  + Nome ou descrição
  + Número da tarefa total
  + Leitura de dados: a soma do tamanho de entrada e o tamanho de leitura em ordem aleatória
  + Gravação de dados: a soma do tamanho de saída e o tamanho de gravação de ordem aleatória
  + Tempo de execução: o tempo entre a hora de início da primeira tentativa e o tempo de conclusão da última tentativa
  + Contagem de linhas: a soma dos registros de entrada, os registros de saída, os registros de leitura em ordem aleatória e os registros de gravação em ordem aleatória
  + Progresso

    > [!NOTE]  
    > Por padrão, o nó do grafo de trabalho exibirá informações da última tentativa de cada estágio (exceto para o tempo de execução do estágio). Mas durante a reprodução, o nó do grafo de trabalho mostrará informações sobre cada tentativa.

    > [!NOTE]  
    > Para tamanhos de leitura de dados e gravação de dados, usamos 1MB = 1000 KB = 1000 * 1000 bytes.

+ Envie comentários sobre problemas selecionando **fornecer comentários**.

    ![A opção de comentários no aplicativo Spark & página grafo de trabalho.](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

## <a name="use-the-diagnosis-tab-in-the-spark-history-server"></a>Usar a guia diagnóstico no servidor de histórico do Spark

Selecione a ID do trabalho e, em seguida, selecione **diagnóstico** no menu ferramenta para ver o modo de exibição diagnóstico do trabalho. A guia **diagnóstico** inclui **distorção de dados**, **distorção de tempo** e análise de uso de **executor**.

+ Examine a **distorção de dados**, a **distorção de tempo** e a análise de uso do **executor** selecionando as guias respectivamente.

    ![A guia distorção de dados na guia diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-tabs.png)

### <a name="data-skew"></a>Distorção de dados

Selecione a guia **distorção de dados** . As tarefas distorcidas correspondentes são exibidas com base nos parâmetros especificados.

#### <a name="specify-parameters"></a>Especificar parâmetros

A seção **especificar parâmetros** exibe os parâmetros, que são usados para detectar a distorção de dados. A regra padrão é: a leitura de dados da tarefa é maior que três vezes a média de dados da tarefa lida e a leitura dos dados da tarefa é maior que 10 MB. Se você quiser definir sua própria regra para tarefas distorcidas, poderá escolher seus parâmetros. As seções **distorção** e **gráfico de distorção** serão atualizadas de acordo.

#### <a name="skewed-stage"></a>Estágio inclinado

A seção **estágio inclinado** exibe os estágios que têm tarefas distorcidas que atendem aos critérios especificados. Se houver mais de uma tarefa distorcida em um estágio, a seção **estágio distorcido** exibirá apenas a tarefa mais distorcida (ou seja, os maiores dados para distorção de dados).

![Exibição maior da guia de distorção de dados na guia diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section2.png)

##### <a name="skew-chart"></a>Gráfico de distorção

Quando você seleciona uma linha na tabela de **estágio de distorção** , o **gráfico de distorção** exibe mais detalhes de distribuição de tarefas com base em tempo de leitura e de execução de dados. As tarefas distorcidas são marcadas em vermelho e as tarefas normais são marcadas em azul. Para considerar o desempenho, o gráfico exibe até 100 tarefas de exemplo. Os detalhes da tarefa são exibidos no painel inferior direito.

![O gráfico de distorção para o estágio 10 na interface do usuário do Spark.](./media/apache-azure-spark-history-server/sparkui-diagnosis-dataskew-section3.png)

### <a name="time-skew"></a>Distorção de tempo

A guia **Distorção de Tempo** exibe tarefas distorcidas com base no tempo de execução da tarefa.

#### <a name="specify-parameters"></a>Especificar parâmetros

A seção **especificar parâmetros** exibe os parâmetros, que são usados para detectar a distorção de tempo. A regra padrão é: o tempo de execução da tarefa é maior que três vezes do tempo médio de execução e o tempo de execução da tarefa é maior que 30 segundos. Você pode alterar os parâmetros com base em suas necessidades. O **gráfico** de etapas e distorção **inclinada** exibe as informações de estágios e tarefas correspondentes, assim como na guia **distorção de dados** .

Quando você seleciona **distorção de tempo**, o resultado filtrado é exibido na seção **estágio inclinado** , de acordo com os parâmetros definidos na seção **especificar parâmetros** . Quando você seleciona um item na seção de **estágio distorcido** , o gráfico correspondente é rascunho na terceira seção e os detalhes da tarefa são exibidos no painel inferior direito.

![A guia de distorção de tempo dentro da guia diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-timeskew-section2.png)

### <a name="executor-usage-analysis-graphs"></a>Gráficos de análise de uso do executor

O **grafo uso do executor** exibe a alocação real do executor do trabalho e o status de execução.  

Quando você seleciona **análise de uso de executor**, quatro curvas diferentes sobre o uso do executor são rascunhos: **executores alocados**, **executores em execução**, **executores ociosos** e **instâncias de máximo de executor**. Cada **executor adicionado** ou o evento **removido do executor** aumentará ou diminuirá os executores alocados. Você pode verificar a **linha do tempo do evento** na guia **trabalhos** para obter mais comparações.

![A guia análise de uso do executor na guia diagnóstico.](./media/apache-azure-spark-history-server/sparkui-diagnosis-executors.png)

Selecione o ícone de cor para selecionar ou desmarcar o conteúdo correspondente em todos os rascunhos.

 ![Selecione o gráfico na guia análise de uso do executor.](./media/apache-azure-spark-history-server/sparkui-diagnosis-select-chart.png)

## <a name="faq"></a>Perguntas frequentes

### <a name="how-do-i-revert-to-the-community-version"></a>Como fazer reverter para a versão da Comunidade?

Para reverter para a versão da Comunidade, execute as etapas a seguir.

1. Abra o cluster no Ambari.
1. Navegue até   >  **configurações** do Spark2.
1. Selecione **Spark2 personalizados-padrões**.
1. Selecione **Adicionar Propriedade...**.
1. Adicione **Spark. UI. Enhancement. Enabled = False** e, em seguida, salve-o.
1. A propriedade define **falsos** agora.
1. Selecione **salvar** para salvar a configuração.

    ![Desative um recurso no Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-turn-off.png)

1. Selecione **Spark2** no painel esquerdo. Em seguida, na guia **Resumo** , selecione **servidor de histórico Spark2**.

    ![O modo de exibição de resumo no Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart1.png)

1. Para reiniciar o servidor de histórico do Spark, selecione o botão **iniciado** à direita do **Spark2 History Server** e, em seguida, selecione **reiniciar** no menu suspenso.

    ![Reinicie o servidor de histórico do Spark no Apache Ambari.](./media/apache-azure-spark-history-server/apache-spark-restart2.png)  

1. Atualize a interface do usuário da Web do servidor de histórico do Spark. Ele será revertido para a versão da Comunidade.

### <a name="how-do-i-upload-a-spark-history-server-event-to-report-it-as-an-issue"></a>Como fazer carregar um evento do servidor de histórico do Spark para relatá-lo como um problema?

Se você encontrar um erro no servidor de histórico do Spark, execute as etapas a seguir para relatar o evento.

1. Baixe o evento selecionando **baixar** na interface do usuário da Web do servidor de histórico do Spark.

    ![Baixe o evento na interface do usuário do servidor de histórico do Spark.](./media/apache-azure-spark-history-server/sparkui-download-event.png)

2. Selecione **fornecer comentários** no **aplicativo Spark & página grafo de trabalho** .

    ![Forneça comentários sobre o aplicativo Spark & página grafo de trabalho](./media/apache-azure-spark-history-server/sparkui-graph-feedback.png)

3. Forneça o título e uma descrição do erro. Em seguida, arraste o arquivo. zip para o campo de edição e selecione **Enviar novo problema**.

    ![Carregue e envie um novo problema.](./media/apache-azure-spark-history-server/apache-spark-file-issue.png)

### <a name="how-do-i-upgrade-a-jar-file-in-a-hotfix-scenario"></a>Como fazer atualizar um arquivo. jar em um cenário de hotfix?

Se você quiser atualizar com um hotfix, use o script a seguir, que será atualizado `spark-enhancement.jar*` .

**upgrade_spark_enhancement.sh**:

   ```bash
    #!/usr/bin/env bash

    # Copyright (C) Microsoft Corporation. All rights reserved.

    # Arguments:
    # $1 Enhancement jar path

    if [ "$#" -ne 1 ]; then
        >&2 echo "Please provide the upgrade jar path."
        exit 1
    fi

    install_jar() {
        tmp_jar_path="/tmp/spark-enhancement-hotfix-$( date +%s )"

        if wget -O "$tmp_jar_path" "$2"; then
            for FILE in "$1"/spark-enhancement*.jar
            do
                back_up_path="$FILE.original.$( date +%s )"
                echo "Back up $FILE to $back_up_path"
                mv "$FILE" "$back_up_path"
                echo "Copy the hotfix jar file from $tmp_jar_path   to $FILE"
                cp "$tmp_jar_path" "$FILE"

                "Hotfix done."
                break
            done
        else    
            >&2 echo "Download jar file failed."
            exit 1
        fi
    }

    jars_folder="/usr/hdp/current/spark2-client/jars"
    jar_path=$1

    if ls ${jars_folder}/spark-enhancement*.jar 1>/dev/null 2>&1;   then
        install_jar "$jars_folder" "$jar_path"
    else
        >&2 echo "There is no target jar on this node. Exit with no action."
        exit 0
    fi
   ```

#### <a name="usage"></a>Uso

`upgrade_spark_enhancement.sh https://${jar_path}`

#### <a name="example"></a>Exemplo

`upgrade_spark_enhancement.sh https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`

#### <a name="use-the-bash-file-from-the-azure-portal"></a>Usar o arquivo bash da portal do Azure

1. Inicie o [portal do Azure](https://ms.portal.azure.com)e, em seguida, selecione o cluster.
2. Conclua uma [ação de script](../hdinsight-hadoop-customize-cluster-linux.md) com os parâmetros a seguir.

    |Propriedade |Valor |
    |---|---|
    |Tipo de script|- Personalizado|
    |Nome|UpgradeJar|
    |URI do script Bash|`https://hdinsighttoolingstorage.blob.core.windows.net/shsscriptactions/upgrade_spark_enhancement.sh`|
    |Tipo(s) de nó|Cabeçalho, trabalhador|
    |Parâmetros|`https://${account_name}.blob.core.windows.net/packages/jars/spark-enhancement-${version}.jar`|

     ![Ação de enviar script portal do Azure](./media/apache-azure-spark-history-server/apache-spark-upload1.png)

## <a name="known-issues"></a>Problemas conhecidos

+ Atualmente, o servidor de histórico do Spark funciona apenas para Spark 2,3 e 2,4.

+ Os dados de entrada e saída que usam RDD não serão exibidos na guia **dados** .

## <a name="next-steps"></a>Próximas etapas

+ [Gerenciar recursos para um cluster do Apache Spark no HDInsight](apache-spark-resource-manager.md)
+ [Definir as configurações do Apache Spark](apache-spark-settings.md)

## <a name="suggestions"></a>Sugestões

Se você tiver algum comentário ou surgir algum problema ao usar essa ferramenta, envie um email para ( [hdivstool@microsoft.com](mailto:hdivstool@microsoft.com) ).
