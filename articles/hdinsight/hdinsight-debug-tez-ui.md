---
title: "Usar a interface do usuário do Tez com o HDInsight baseado em Windows – Azure | Microsoft Docs"
description: "Saiba como usar a interface de usuário do Tez para depurar trabalhos do Tez no HDInsight baseado no Windows."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: a55bccb9-7c32-4ff2-b654-213a2354bd5c
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 3889fa1c3523eb0330cbe3b7640fd8590a5ceadf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-tez-ui-to-debug-tez-jobs-on-windows-based-hdinsight"></a>Usar a interface de usuário do Tez para depurar trabalhos no HDInsight baseado no Windows
A interface de usuário do Tez é uma página da Web que pode ser usada para entender e depurar trabalhos que usam o Tez como o mecanismo de execução em clusters HDInsight baseados no Windows. A interface de usuário do Tez permite que você visualize o trabalho como um gráfico de itens conectados, detalhe cada item e recupere estatísticas e informações de registro.

> [!IMPORTANT]
> As etapas deste documento exigem um cluster HDInsight que usa Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster HDInsight baseado no Windows. Para obter as etapas de criação de um novo cluster, confira [Introdução ao uso do HDInsight baseado no Windows](hdinsight-hadoop-tutorial-get-started-windows.md).

  > [!IMPORTANT]
  > A interface de usuário do Tez está disponível apenas em clusters HDInsight baseados no Windows criados depois de 8 de fevereiro de 2016.
  >
  >
* Um cliente da Área de Trabalho Remota baseado no Windows.

## <a name="understanding-tez"></a>Noções básicas sobre o Tez
Tez é uma estrutura extensível para processamento de dados no Hadoop que fornece maior velocidade de processamento do que o MapReduce tradicional. Para os clusters HDInsight baseados no Windows, é um mecanismo opcional que você pode habilitar para o Hive usando o comando a seguir como parte de sua consulta do Hive:

    set hive.execution.engine=tez;

Quando o trabalho é enviado ao Tez, ele cria um DAG (Gráfico Acíclico Dirigido) que descreve a ordem de execução das ações exigidas pelo trabalho. As ações individuais são chamadas de vértices e executam uma parte do trabalho geral. A execução real do trabalho descrita por um vértice é chamada de tarefa e pode ser distribuída em vários nós no cluster.

### <a name="understanding-the-tez-ui"></a>Noções básicas sobre a interface de usuário do Tez
A interface de usuário do Tez é uma página da Web que fornece informações sobre processos em execução ou que foram executados anteriormente com o Tez. Ela permite exibir o DAG gerado pelo Tez, como ele é distribuído entre os clusters, contadores, como a memória usada por tarefas e vértices, além de informações de erro. Ela pode oferecer informações úteis nos seguintes cenários:

* Monitoramento de processos de longa execução, exibindo o andamento de tarefas map e reduce.
* Análise de dados históricos para processos com ou sem êxito, a fim de saber como o processamento pode ser aprimorado ou qual foi o motivo da falha.

## <a name="generate-a-dag"></a>Gerar um DAG
A interface de usuário do Tez conterá apenas dados se um trabalho que usa o mecanismo Tez estiver sendo executado no momento ou se tiver sido executado anteriormente. Normalmente, as consultas simples do Hive podem ser resolvidas sem usar o Tez. Porém, as consultas mais complexas que realizam filtragem, agrupamento, ordenação, associações etc. normalmente exigirão o Tez.

Use as etapas a seguir para executar uma consulta do Hive usando o Tez.

1. Abra um navegador da Web, navegue para https://CLUSTERNAME.azurehdinsight.net, em que **CLUSTERNAME** é o nome do seu cluster HDInsight.
2. No menu, na parte superior da página, escolha o **Editor do Hive**. Essa ação exibirá uma página com o exemplo de consulta a seguir.

        Select * from hivesampletable

    Apague o exemplo de consulta e substitua-o pelo que se segue.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
3. Selecione o botão **Enviar**. A seção **Sessão de Trabalho**, na parte inferior da página, exibirá o status da consulta. Depois que o status mudar para **Concluído**, selecione o link **Exibir Detalhes** para exibir os resultados. A **Saída do Trabalho** deve ser semelhante a esta:

        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

## <a name="use-the-tez-ui"></a>Usar a interface de usuário do Tez
> [!NOTE]
> A interface de usuário do Tez está disponível na área de trabalho dos nós de cabeçalho do cluster, de modo que é preciso usar a Área de Trabalho Remota para se conectar a esses nós de cabeçalho.
>
>

1. No [Portal do Azure](https://portal.azure.com), escolha o cluster HDInsight. Na parte superior da folha HDInsight, selecione o ícone **Área de Trabalho Remota**. Isso exibirá a folha da área de trabalho remota

    ![Ícone da área de trabalho remota](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)
2. Na folha Área de Trabalho Remota, selecione **Conectar** para se conectar ao nó de cabeçalho do cluster. Quando solicitado, use o nome do usuário e a senha da Área de Trabalho Remota do cluster para autenticar a conexão.

    ![Ícone de conexão da área de trabalho remota](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

   > [!NOTE]
   > Se você não tiver habilitado a conectividade da Área de Trabalho Remota, forneça um nome de usuário, uma senha e a data de vencimento, então selecione **Habilitar** para habilitar a Área de Trabalho Remota. Depois da habilitação, use as etapas anteriores para se conectar.
   >
   >
3. Depois de conectado, abra o Internet Explorer na área de trabalho remota, selecione o ícone de engrenagem no canto superior direito do navegador e selecione **Configurações do Modo de Exibição de Compatibilidade**.
4. Na parte inferior de **Configurações do Modo de Exibição de Compatibilidade**, desmarque a caixa de seleção de **Exibir sites da intranet no Modo de Exibição de Compatibilidade** e **Usar listas de compatibilidade da Microsoft** e selecione **Fechar**.
5. No Internet Explorer, navegue até http://headnodehost:8188/tezui/#/. Isso exibirá a interface de usuário do Tez

    ![Interface de usuário do Tez](./media/hdinsight-debug-tez-ui/tezui.png)

    Quando a interface de usuário do Tez for carregada, você verá uma lista de DAGs atualmente em execução ou que foram executados no cluster. O modo de exibição padrão inclui Nome, Id, Remetente, Status, Hora de Início, Hora de Término, Duração, ID do Aplicativo e Fila do Dag. É possível adicionar mais colunas usando o ícone de engrenagem no canto direito da página.

    Se você tiver apenas uma entrada, ela será para a consulta que você executou na seção anterior. Se você tiver várias entradas, será possível pesquisar ao inserir critérios de pesquisa nos campos acima dos DAGs e pressionar **Enter**.
6. Escolha o **Nome do Dag** para a entrada mais recente do DAG. Isso exibirá informações sobre o DAG, bem como a opção para baixar um zip de arquivos JSON que contêm informações sobre o DAG.

    ![Detalhes do DAG](./media/hdinsight-debug-tez-ui/dagdetails.png)
7. Acima dos **Detalhes do DAG**, há vários links que podem ser usados para exibir informações sobre o DAG.

   * **Contadores de DAG** exibe informações de contadores do DAG em questão.
   * **Modo de Exibição Gráfico** exibe uma representação gráfica deste DAG.
   * **Todos os Vértices** exibe uma lista dos vértices neste DAG.
   * **Todas as Tarefas** exibe uma lista das tarefas de todos os vértices neste DAG.
   * **Todas as Tentativas de Tarefa** exibe informações sobre as tentativas de execução de tarefas para este DAG.

     > [!NOTE]
     > Se você rolar a exibição da coluna até Vértices, Tarefas e Tentativas de Tarefa, observe que há links para exibir **contadores** e **exibir ou baixar logs** para cada linha.
     >
     >

     Se houver uma falha com o trabalho, os Detalhes do DAG exibirão um status de FALHA, juntamente com links para informações sobre a tarefa com falha. As informações de diagnóstico serão exibidas abaixo dos detalhes do DAG.
8. Escolha **Modo de Exibição Gráfico**. Isso exibe uma representação gráfica do DAG. Você pode colocar o mouse sobre cada vértice no modo de exibição para exibir informações sobre ele.

    ![Modo de Exibição Gráfico](./media/hdinsight-debug-tez-ui/dagdiagram.png)
9. Clicar em um vértice carregará os **Detalhes do Vértice** para esse item. Clique no vértice **Mapa 1** para exibir detalhes do item em questão. Selecione **Confirmar** para confirmar a navegação.

    ![Detalhes do Vértice](./media/hdinsight-debug-tez-ui/vertexdetails.png)
10. Observe que agora você tem links relacionados às tarefas e aos vértices na parte superior da página.

    > [!NOTE]
    > Você também pode chegar a essa página voltando para **Detalhes do DAG**, selecionando **Detalhes do Vértice** e selecionando o vértice **Mapa 1**.
    >
    >

    * **Contadores de Vértice** exibe informações de contadores do vértice em questão.
    * **Tarefas** exibe tarefas do vértice em questão.
    * **Tentativas de Tarefa** exibe informações sobre as tentativas de execução de tarefas do vértice em questão.
    * **Fontes e Coletores** exibe fontes de dados e coletores do vértice em questão.

      > [!NOTE]
      > Assim como no menu anterior, você pode rolar a exibição da coluna para Tarefas, Tentativas de Tarefa e Fontes de Coletores para exibir links para outras informações sobre cada item.
      >
      >
11. Escolha **Tarefas** e selecione o item chamado **00_000000**. Isso exibirá os **Detalhes da Tarefa** desta tarefa. Nessa tela, você pode exibir **Contadores de Tarefa** e **Tentativas de Tarefa**.

    ![Detalhes de tarefa](./media/hdinsight-debug-tez-ui/taskdetails.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a usar o modo de exibição do Tez, saiba mais sobre [Como usar o Hive no HDInsight](hdinsight-use-hive.md).

Para obter informações técnicas mais detalhadas sobre o Tez, confira a [página sobre o Tez em Hortonworks](http://hortonworks.com/hadoop/tez/).
