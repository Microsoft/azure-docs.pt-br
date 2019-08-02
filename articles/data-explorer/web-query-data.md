---
title: 'Início Rápido: Consultar dados na interface do usuário Web do Azure Data Explorer'
description: Neste início rápido, você aprenderá a consultar e compartilhar dados na interface do usuário da Web do Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 07/04/2019
ms.openlocfilehash: 145e452a3a914e1026d6da11f2ce38e85fa724f0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797184"
---
# <a name="quickstart-query-data-in-azure-data-explorer-web-ui"></a>Início Rápido: Consultar dados na interface do usuário Web do Azure Data Explorer

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. O Azure Data Explorer fornece um aplicativo Web que permite executar e compartilhar consultas. O aplicativo está disponível no portal do Azure e como um aplicativo Web autônomo. Neste artigo, você trabalhará na versão autônoma que permite conectar vários clusters e compartilhar links diretos para suas consultas.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Além de uma assinatura do Azure, é necessário ter [um cluster de teste e um banco de dados](create-cluster-database-portal.md) para concluir este início rápido.

## <a name="sign-in-to-the-application"></a>Entrar no aplicativo

Entre no [aplicativo](https://dataexplorer.azure.com/).

## <a name="add-clusters"></a>Adicionar clusters

Ao abrir o aplicativo pela primeira vez, não haverá conexões.

![Adicionar cluster](media/web-query-data/add-cluster.png)

É necessário adicionar uma conexão a pelo menos um cluster antes de começar a executar consultas. Nesta seção, você adicionará conexões ao *cluster de ajuda* do Azure Data Explorer, que configuramos para ajudar no aprendizado, e ao cluster de teste que você criou em um início rápido anterior.

1. No canto superior esquerdo do aplicativo, selecione **Adicionar cluster**.

1. Na caixa de diálogo **Adicionar cluster**, insira o URI e, em seguida, selecione **Adicionar**.

   Você pode usar o URI do cluster de ajuda, `https://help.kusto.windows.net`. Se você tiver seu próprio cluster, forneça o URI do cluster. Por exemplo, `https://mydataexplorercluster.westus.kusto.windows.net` como na imagem a seguir:

    ![URI do servidor no portal](media/web-query-data/server-uri.png)

1. No painel esquerdo, você deverá ver o cluster de **ajuda**. Expanda o banco de dados de **Exemplos** para que seja possível visualizar as tabelas de exemplos às quais você tem acesso.

    ![Bancos de dados de exemplo](media/web-query-data/sample-databases.png)

    Usamos a tabela **StormEvents** mais adiante neste início rápido e em outros artigos do Azure Data Explorer.

Agora, adicione o cluster de teste que você criou.

1. Selecione **Adicionar cluster**.

1. Na caixa de diálogo **Adicionar cluster** insira a URL do cluster de teste no formulário `https://<ClusterName>.<Region>.kusto.windows.net/` e, em seguida, selecione **Adicionar**.

    No exemplo abaixo, você visualiza o cluster de **ajuda** e um novo cluster, **docscluster.westus** (a URL completa é `https://docscluster.westus.kusto.windows.net/`).

    ![Testar cluster](media/web-query-data/test-cluster.png)

## <a name="run-queries"></a>Executar consultas

Agora, é possível executar consultas em qualquer cluster ao qual esteja conectado (supondo que você tenha dados no cluster de teste). Vamos nos concentrar no cluster de **ajuda**.

1. No painel esquerdo, no cluster de **ajuda**, selecione o banco de dados de **Exemplos**.

1. Copie e cole a consulta a seguir na janela de consulta. Na parte superior da janela, selecione **Executar**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Essa consulta retorna os dez registros mais recentes na tabela **StormEvents**. O lado esquerdo do resultado deve ser semelhante à tabela a seguir.

    ![Conjunto de resultados da consulta](media/web-query-data/result-set-01.png)

    A imagem a seguir mostra o estado em que o aplicativo deve estar agora, com clusters adicionados e uma consulta com resultados.

    ![Aplicação completa](media/web-query-data/full-application.png)

1. Copie e cole a consulta a seguir na janela de consulta, abaixo da primeira consulta. Observe como não está formatada em linhas separadas, como a primeira consulta.

    ```Kusto
    StormEvents | sort by StartTime desc | project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative | take 10
    ```

1. Clique na nova consulta na janela, que seleciona a consulta. Pressione Shift+Alt+F para formatar a consulta, de modo que seja semelhante ao seguinte.

    ![Consulta formatada](media/web-query-data/formatted-query.png)

1. Pressione Shift+Enter, que é um atalho para executar uma consulta.

   Essa consulta retorna os mesmos registros que a primeira, mas inclui apenas as colunas especificadas na instrução `project`. O resultado deve ser semelhante à tabela a seguir.

    ![Conjunto de resultados da consulta](media/web-query-data/result-set-02.png)

1. Na parte superior da janela de consulta, selecione **Recall**.

    A janela de consulta agora mostra o conjunto de resultados da primeira consulta, sem a necessidade de executar novamente a consulta. Muitas vezes, durante a análise, você executa várias consultas e o **Recall** permite rever os resultados das consultas anteriores.

1. Vamos executar mais uma consulta para ver um tipo diferente de saída.

    ```Kusto
    StormEvents
    | summarize event_count=count(), mid = avg(BeginLat) by State
    | sort by mid
    | where event_count > 1800
    | project State, event_count
    | render columnchart
    ```
    O resultado deve ser semelhante ao gráfico a seguir.

    ![Gráfico de colunas](media/web-query-data/column-chart.png)

## <a name="work-with-the-table-grid"></a>Trabalhar com a grade de tabela

Você viu como as consultas básicas funcionam, agora vamos saber como é possível usar a grade de tabela para personalizar os resultados e fazer análises adicionais.

1. Execute novamente a primeira consulta. Passe o mouse sobre a coluna **Estado**, selecione o menu e, em seguida, selecione **Agrupar por Estado**.

    ![Agrupar por estado](media/web-query-data/group-by.png)

1. Na grade, expanda **Califórnia** para ver os registros desse estado.

    ![Conjunto de resultados da consulta](media/web-query-data/result-set-03.png)

    Esse tipo de agrupamento pode ser útil ao fazer uma análise exploratória.

1. Passe o mouse sobre a coluna **Grupo** e, em seguida, selecione **Redefinir Colunas**.

    ![Redefinir Colunas](media/web-query-data/reset-columns.png)

    Isso retorna a grade ao seu estado original.

1. Execute a consulta a seguir.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | where DamageProperty > 5000
    | project StartTime, State, EventType, DamageProperty, Source
    | take 10
    ```

1. No lado direito da grade, selecione **Colunas** para ver o painel de ferramentas.

    ![Painel de ferramentas](media/web-query-data/tool-panel.png)

    Esse painel funciona de maneira semelhante à lista de campos da tabela dinâmica no Excel, permitindo que você faça mais análises na própria grade.

1. Selecione **Modo de Tabela Dinâmica**; em seguida, arraste colunas da seguinte maneira: **Estado** para **Grupos de Linhas**; **DamageProperty** para **Valores**; e **EventType** para **Rótulos de Coluna**.  

    ![Modo de tabela dinâmica](media/web-query-data/pivot-mode.png)

    O resultado deve ser semelhante à tabela dinâmica a seguir.

    ![Tabela dinâmica](media/web-query-data/pivot-table.png)

    Observe como Vermont e Alabama têm dois eventos na mesma categoria, enquanto o Texas tem dois eventos em diferentes categorias. Tabelas dinâmicas permitem que você identifique rapidamente informações como essas, já que são uma ótima ferramenta para análise rápida.

## <a name="share-queries"></a>Compartilhar consultas

Muitas vezes, você quer compartilhar as consultas que você cria. É possível fornecer um link direto para que outros usuários com acesso ao cluster possam executar as consultas.

1. Na janela de consulta, selecione a primeira consulta que você copiou.

1. Na parte superior da janela de consulta, selecione **Compartilhar**.

1. Selecione **Link, consulta para área de transferência**.

1. Copie o link e consulte um arquivo de texto.

1. Cole o link em uma nova janela do navegador. O resultado deve ser semelhante ao seguinte após a execução da consulta.

    ![Consulta compartilhada](media/web-query-data/shared-query.png)

## <a name="provide-feedback"></a>Fornecer comentários

O Data Explorer atualmente está em versão prévia e nós agradecemos seus comentários sobre a sua experiência. Você pode fazer isso agora ou aguardar até utilizá-lo por mais tempo.

1. No canto superior direito do aplicativo, selecione o ícone de comentários: ![Ícone de comentários](media/web-query-data/icon-feedback.png).

1. Insira seu comentário e selecione **Enviar**.

## <a name="clean-up-resources"></a>Limpar recursos

Você não criou nenhum recurso neste início rápido, mas se desejar remover um ou ambos os clusters do aplicativo, clique com o botão direito do mouse no cluster e selecione **Remover conexão**.

## <a name="next-steps"></a>Próximas etapas

[Gravar consultas para Azure Data Explorer](write-queries.md)
