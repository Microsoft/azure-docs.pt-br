---
title: Diagnosticar exceções no tempo de execução usando o Azure Application Insights | Microsoft Docs
description: Tutorial para localizar e diagnosticar exceções de tempo de execução em seu aplicativo usando o Azure Application Insights.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/19/2017
ms.custom: mvc
ms.openlocfilehash: 1d8dfb3eaf3ea1ab901df4bef62210d28e02c3a4
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383144"
---
# <a name="find-and-diagnose-run-time-exceptions-with-azure-application-insights"></a>Localizar e diagnosticar exceções de tempo de execução com o Azure Application Insights

O Azure Application Insights coleta a telemetria do seu aplicativo para ajudar a identificar e diagnosticar exceções de tempo de execução.  Este tutorial o conduz por esse processo com o seu aplicativo.  Você aprenderá como:

> [!div class="checklist"]
> * Modificar o projeto para habilitar o acompanhamento de exceção
> * Identificar exceções para diferentes componentes do seu aplicativo
> * Exibir detalhes de uma exceção
> * Baixar um instantâneo da exceção para o Visual Studio para depuração
> * Analisar os detalhes de solicitações com falha usando a linguagem de consulta
> * Criar um novo item de trabalho para corrigir o código com defeito


## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial:

- Instale o [Visual Studio 2019](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET
    - Desenvolvimento do Azure
- Baixe e instale o [Depurador de Instantâneo do Visual Studio](https://aka.ms/snapshotdebugger).
- Habilite o [Depurador de Instantâneo do Visual Studio](../app/snapshot-debugger.md)
- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](../app/asp-net.md). 
- O tutorial acompanha a identificação de uma exceção em seu aplicativo, portanto, modifique seu código no seu ambiente de desenvolvimento ou teste para gerar uma exceção. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analisar falhas
O Application Insights coleta quaisquer falhas em seu aplicativo e permite que você exiba sua frequência em diferentes operações para ajudá-lo a concentrar seus esforços naquelas que têm mais impacto.  Então você pode fazer drill down nos detalhes dessas falhas para identificar a causa raiz.   

1. Selecione **Application Insights** e, em seguida, sua assinatura.  
2. Para abrir o painel **Falhas**, selecione **Falhas** no menu **Investigar** ou clique no grafo **Solicitações com falha**.

    ![Solicitações falhas](media/tutorial-runtime-exceptions/failed-requests.png)

3. O painel **Solicitações com falha** mostra a contagem de solicitações com falha e o número de usuários afetados para cada operação para o aplicativo.  Ao classificar essas informações por usuário, você pode identificar as falhas que mais afetam os usuários.  Neste exemplo, o **GET Employees/Create** e **GET Customers/Details** são fortes candidatos a investigação devido ao seu grande número de falhas e usuários afetados.  Selecionar uma operação mostra mais informações sobre essa operação no painel à direita.

    ![Painel de solicitações com falha](media/tutorial-runtime-exceptions/failed-requests-blade.png)

4. Reduza a janela de tempo para ampliar o período em que a taxa de falhas mostra um pico.

    ![Janela de solicitações com falha](media/tutorial-runtime-exceptions/failed-requests-window.png)

5. Consulte os exemplos relacionados clicando no botão com o número de resultados filtrados. Os exemplos "sugeridos" relacionaram a telemetria de todos os componentes, mesmo que a amostragem possa ter estado em vigor em qualquer um deles. Clique em um resultado de pesquisa para ver os detalhes da falha.

    ![Exemplos de solicitações com falha](media/tutorial-runtime-exceptions/failed-requests-search.png)

6. Os detalhes das solicitações com falha exibem o gráfico de Gantt, que mostra que havia duas falhas de dependência nessa transação, o que também atribuiu mais de 50% da duração total da transação. Essa experiência apresenta toda a telemetria, entre os componentes de um aplicativo distribuído que estão relacionadas a essa ID de operação. [Saiba mais sobre a nova experiência](../app/transaction-diagnostics.md). Você pode selecionar qualquer um dos itens para ver seus detalhes no lado direito. 

    ![Detalhes da solicitação com falha](media/tutorial-runtime-exceptions/failed-request-details.png)

7. Os detalhes das operações também mostram uma FormatException que parece ter causado a falha.  Você pode ver que se deve a um CEP inválido. É possível abrir o instantâneo de depuração para ver as informações do nível de depuração de código no Visual Studio.

    ![Detalhes da exceção](media/tutorial-runtime-exceptions/failed-requests-exception.png)

## <a name="identify-failing-code"></a>Como identificar falhas de código
O depurador de instantâneo coleta instantâneos das exceções mais frequentes em seu aplicativo para ajudá-lo a diagnosticar a causa raiz em produção.  Você pode exibir instantâneos de depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Depois disso, você tem a opção de depurar o código-fonte baixando o instantâneo e abrindo-o no Visual Studio 2019 Enterprise.

1. Nas propriedades da exceção, clique em **Abrir instantâneo de depuração**.
2. O painel **Instantâneo de depuração** é aberto com a pilha de chamadas para a solicitação.  Clique em qualquer método para exibir os valores de todas as variáveis locais no momento da solicitação.  Começando com o método superior neste exemplo, podemos ver variáveis locais que não têm nenhum valor.

    ![Depurar instantâneo](media/tutorial-runtime-exceptions/debug-snapshot-01.png)

3. A primeira chamada que tem valores válidos é **ValidZipCode** e podemos ver que um CEP foi informado com letras que não podem ser convertidas em um número inteiro.  Isso parece ser o erro no código que precisa ser corrigido.

    ![Captura de tela que mostra um erro no código que precisa ser corrigido.    ](media/tutorial-runtime-exceptions/debug-snapshot-02.png)

4. Você então tem a opção de baixar esse instantâneo para o Visual Studio em que podemos localizar o código real que precisa ser corrigido. Para fazer isso, clique em **Baixar Instantâneo**.
5. O instantâneo é carregado no Visual Studio.
6. Agora você pode executar uma sessão de depuração no Visual Studio Enterprise que identifique rapidamente a linha de código que causou a exceção.

    ![Exceção no código](media/tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Usar dados de análise
Todos os dados coletados pelo Application Insights são armazenados no Azure Log Analytics, que fornece uma linguagem de consulta eficiente que permite que você analise os dados de várias maneiras.  Podemos usar esses dados para analisar as solicitações que geraram a exceção que estamos pesquisando. 

1. Clique nas informações do CodeLens acima do código para exibir a telemetria fornecida pelo Application Insights.

    ![Código](media/tutorial-runtime-exceptions/codelens.png)

1. Clique em **Analisar o impacto** para abrir o Application Insights Analytics.  Ele é preenchido com várias consultas que fornecem detalhes sobre solicitações com falha, como os usuários afetados, navegadores e regiões.<br><br>![A captura de tela mostra a janela Application Insights que inclui várias consultas.](media/tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Adicionar item de trabalho
Se você conectar o Application Insights a um sistema de acompanhamento, como Azure DevOps ou GitHub, poderá criar um item de trabalho diretamente do Application Insights.

1. Volte para o painel **Propriedades de Exceção** no Application Insights.
2. Clique em **Novo Item de Trabalho**.
3. O painel **Novo Item de Trabalho** abre com os detalhes sobre a exceção já preenchidos.  Você pode adicionar qualquer informação adicional antes de salvá-lo.

    ![Novo Item de Trabalho](media/tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a identificar exceções de tempo de execução, avance para o próximo tutorial para aprender a identificar e diagnosticar problemas de desempenho.

> [!div class="nextstepaction"]
> [Identificar problemas de desempenho](./tutorial-performance.md)

