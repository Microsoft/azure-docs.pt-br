---
title: "Diagnosticar exceções no tempo de execução usando o Azure Application Insights | Microsoft Docs"
description: "Tutorial para localizar e diagnosticar exceções de tempo de execução em seu aplicativo usando o Azure Application Insights."
services: application-insights
keywords: 
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/19/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 11e0f2f19acc843f1c558b5d0cfe84291035a6a5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
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


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET
    - Desenvolvimento do Azure
- Baixe e instale o [Depurador de Instantâneo do Visual Studio](http://aka.ms/snapshotdebugger).
- Habilite o [Depurador de Instantâneo do Visual Studio](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-snapshot-debugger)
- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](app-insights-asp-net.md). 
- O tutorial acompanha a identificação de uma exceção em seu aplicativo, portanto, modifique seu código no seu ambiente de desenvolvimento ou teste para gerar uma exceção. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com).


## <a name="analyze-failures"></a>Analisar falhas
O Application Insights coleta quaisquer falhas em seu aplicativo e permite que você exiba sua frequência em diferentes operações para ajudá-lo a concentrar seus esforços naquelas que têm mais impacto.  Então você pode fazer drill down nos detalhes dessas falhas para identificar a causa raiz.   

1. Selecione **Application Insights** e, em seguida, sua assinatura.  
1. Para abrir o painel **Falhas**, selecione **Falhas** no menu **Investigar** ou clique no gráfico **Solicitações com falha**.

    ![Solicitações falhas](media/app-insights-tutorial-runtime-exceptions/failed-requests.png)

2. O painel **Solicitações com falha** mostra a contagem de solicitações com falha e o número de usuários afetados para cada operação para o aplicativo.  Ao classificar essas informações por usuário, você pode identificar as falhas que mais afetam os usuários.  Neste exemplo, o **GET Employees/Create** e **GET Customers/Details** são fortes candidatos a investigação devido ao seu grande número de falhas e usuários afetados.  Selecionar uma operação mostra mais informações sobre essa operação no painel à direita.

    ![Painel de solicitações com falha](media/app-insights-tutorial-runtime-exceptions/failed-requests-blade.png)

3. Reduza a janela de tempo para ampliar o período em que a taxa de falhas mostra um pico.

    ![Janela de solicitações com falha](media/app-insights-tutorial-runtime-exceptions/failed-requests-window.png)

4. Clique em **Exibir detalhes** para ver os detalhes para a operação.  Isso inclui um gráfico de Gantt que mostra duas dependências com falha que coletivamente levaram quase meio segundo para serem concluídas.  Você pode encontrar mais informações sobre a análise de problemas de desempenho concluindo o tutorial [Localizar e diagnosticar problemas de desempenho com o Azure Application Insights](app-insights-tutorial-performance.md).

    ![Detalhes das solicitações com falha](media/app-insights-tutorial-runtime-exceptions/failed-requests-details.png)

5. Os detalhes das operações também mostram uma FormatException que parece ter causado a falha.  Clique na exceção ou na contagem dos **Três principais tipos de exceção** para exibir os detalhes.  Você pode ver que se deve a um CEP inválido.

    ![Detalhes da exceção](media/app-insights-tutorial-runtime-exceptions/failed-requests-exception.png)



## <a name="identify-failing-code"></a>Como identificar falhas de código
O depurador de instantâneo coleta instantâneos das exceções mais frequentes em seu aplicativo para ajudá-lo a diagnosticar a causa raiz em produção.  Você pode exibir instantâneos de depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Em seguida, você pode depurar o código-fonte baixando o instantâneo e abrindo-o no Visual Studio 2017.

1. Nas propriedades da exceção, clique em **Abrir instantâneo de depuração**.
2. O painel **Instantâneo de depuração** é aberto com a pilha de chamadas para a solicitação.  Clique em qualquer método para exibir os valores de todas as variáveis locais no momento da solicitação.  Começando com o método superior neste exemplo, podemos ver variáveis locais que não têm nenhum valor.

    ![Depurar instantâneo](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-01.png)

4. A primeira chamada que tem valores válidos é **ValidZipCode** e podemos ver que um CEP foi informado com letras que não podem ser convertidas em um número inteiro.  Isso parece ser o erro no código que precisa ser corrigido.

    ![Depurar instantâneo](media/app-insights-tutorial-runtime-exceptions/debug-snapshot-02.png)

5. Para baixar esse instantâneo para o Visual Studio em que podemos localizar o código real que precisa ser corrigido, clique em **Baixar Instantâneo**.
6. O instantâneo é carregado no Visual Studio.
7. Agora você pode executar uma sessão de depuração no Visual Studio que identifique rapidamente a linha de código que causou a exceção.

    ![Exceção no código](media/app-insights-tutorial-runtime-exceptions/exception-code.png)


## <a name="use-analytics-data"></a>Usar dados de análise
Todos os dados coletados pelo Application Insights são armazenados no Azure Log Analytics, que fornece uma linguagem de consulta eficiente que permite que você analise os dados de várias maneiras.  Podemos usar esses dados para analisar as solicitações que geraram a exceção que estamos pesquisando. 

8. Clique nas informações do CodeLens acima do código para exibir a telemetria fornecida pelo Application Insights.

    ![Código](media/app-insights-tutorial-runtime-exceptions/codelens.png)

9. Clique em **Analisar o impacto** para abrir o Application Insights Analytics.  Ele é preenchido com várias consultas que fornecem detalhes sobre solicitações com falha, como os usuários afetados, navegadores e regiões.<br><br>![Analytics](media/app-insights-tutorial-runtime-exceptions/analytics.png)<br>

## <a name="add-work-item"></a>Adicionar item de trabalho
Se você conectar o Application Insights a um sistema de acompanhamento, como Visual Studio Team Services ou GitHub, poderá criar um item de trabalho diretamente do Application Insights.

1. Volte para o painel **Propriedades de Exceção** no Application Insights.
2. Clique em **Novo Item de Trabalho**.
3. O painel **Novo Item de Trabalho** abre com os detalhes sobre a exceção já preenchidos.  Você pode adicionar qualquer informação adicional antes de salvá-lo.

    ![Novo Item de Trabalho](media/app-insights-tutorial-runtime-exceptions/new-work-item.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a identificar exceções de tempo de execução, avance para o próximo tutorial para aprender a identificar e diagnosticar problemas de desempenho.

> [!div class="nextstepaction"]
> [Identificar problemas de desempenho](app-insights-tutorial-performance.md)