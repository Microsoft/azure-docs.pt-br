---
title: Entender seus clientes no Azure Application Insights | Microsoft Docs
description: "Tutorial sobre como usar o Azure Application Insights para entender como os clientes estão usando o seu aplicativo."
keywords: 
services: application-insights
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 7399de1df60d0583bc6efacd0074d3a00a086ff3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-application-insights-to-understand-how-customers-are-using-your-application"></a>Usar o Azure Application Insights para entender como os clientes estão usando o seu aplicativo

O Azure Application Insights coleta informações de uso para ajudá-lo a entender como os usuários interagem com o seu aplicativo.  Este tutorial o conduz pelos diferentes recursos disponíveis para analisar essas informações.  Você saberá como:

> [!div class="checklist"]
> * Analisar os detalhes sobre os usuários que acessam seu aplicativo
> * Use as informações de sessão para analisar como os clientes usam o seu aplicativo
> * Definir os funis que permitem que você compare sua atividade de usuário desejada com a atividade real 
> * Criar uma pasta de trabalho para consolidar visualizações e consultas em um único documento
> * Agrupe usuários semelhantes para analisá-los juntos
> * Saiba quais usuários estão retornando ao seu aplicativo
> * Inspecione como os usuários navegam no seu aplicativo


## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
    - Desenvolvimento Web e ASP.NET
    - Desenvolvimento do Azure
- Baixe e instale o [Depurador de Instantâneo do Visual Studio](http://aka.ms/snapshotdebugger).
- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](app-insights-asp-net.md). 
- [Enviar telemetria do seu aplicativo](app-insights-usage-overview.md#send-telemetry-from-your-app) para adicionar exibições de página/eventos personalizadas
- Enviar [contexto de usuário](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-usage-send-user-context) para acompanhar o que um usuário faz ao longo do tempo e utilizar totalmente os recursos de uso.

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="get-information-about-your-users"></a>Obtenha informações sobre seus usuários
O painel **Usuários** permite que você entenda detalhes importantes sobre seus usuários de várias maneiras. Você pode usar este painel para entender informações como o ponto do qual seus usuários estão se conectando, detalhes de seus clientes e quais áreas do aplicativo eles estão acessando. 

1. Selecione **Application Insights** e, em seguida, selecione sua assinatura.
2. Selecione **Usuários** no menu.
3. A exibição padrão mostra o número de usuários exclusivos que se conectaram ao seu aplicativo nas últimas 24 horas.  Você pode alterar a janela de tempo e definir vários outros critérios para filtrar essas informações.

    ![Construtor de Consultas](media\app-insights-tutorial-users\QueryBuilder.png)

6. Clique na lista suspensa **Durante** e altere a janela de tempo para sete dias.  Isso aumenta os dados incluídos nos diferentes gráficos no painel.

    ![Alterar intervalo de tempo](media\app-insights-tutorial-users\TimeRange.png)

4. Clique na lista suspensa **Dividir por** para adicionar uma divisão por propriedade de usuário ao gráfico.  Selecione **País ou região**.  O gráfico inclui os mesmos dados, mas permite que você exiba uma análise do número de usuários para cada país.

    ![Gráfico de País ou Região](media\app-insights-tutorial-users\CountryorRegion.png)

5. Posicione o cursor sobre diferentes barras no gráfico e observe que a contagem para cada país reflete somente a janela de tempo representada pela barra.
6. Examine a coluna **Insights** à direita que executa a análise em seus dados de usuário.  Isso fornece informações como o número de sessões exclusivas ao longo do tempo e os registros com propriedades comuns significativas dos dados do usuário 

    ![Coluna de informações](media\app-insights-tutorial-users\insights.png)


## <a name="analyze-user-sessions"></a>Analisar sessões de usuário
O painel **Sessões** é semelhante ao painel **Usuários**.  O item **Usuários** ajuda a entender os detalhes sobre os usuários que acessam seu aplicativo; o item **Sessões** ajuda a entender como os usuários usaram seu aplicativo.  

1. Selecione **Sessões** no menu.
2. Examine o gráfico e observe que você tem as mesmas opções para filtrar e dividir os dados como no painel **Usuários**.

    ![Construtor de Consultas de sessões](media\app-insights-tutorial-users\SessionsBuilder.png)

3. O painel **Exemplo dessas sessões** à direita lista sessões que incluem um grande número de eventos.  Estas são sessões interessantes a analisar.

    ![Exemplo dessas sessões](media\app-insights-tutorial-users\SessionsSample.png)

4. Clique em uma das sessões para exibir a **Linha do Tempo da Sessão**, que mostra todas as ações nas sessões.  Isso pode ajudá-lo a identificar informações como as sessões com um grande número de exceções.

    ![Linha do tempo de sessões](media\app-insights-tutorial-users\SessionsTimeline.png)

## <a name="group-together-similar-users"></a>Agrupe usuários semelhantes
Um **Coorte** é um conjunto de usuários agrupados conforme características semelhantes.  Você pode usar coortes para filtrar os dados em outros painéis, permitindo que você analise grupos específicos de usuários.  Por exemplo, você talvez queira analisar apenas os usuários que concluíram uma compra.

1.  Selecione **Coortes** no menu.
2.  Clique em **Novo** para criar um novo coorte.
3.  Selecione a lista suspensa **Quem usou** e selecione uma ação.  Somente os usuários que executaram esta ação na janela de tempo do relatório serão incluídos.

    ![Coorte que realizou as ações especificadas](media\app-insights-tutorial-users\CohortsDropdown.png)

4.  Selecione **Usuários** no menu.
5.  Na lista suspensa **Mostrar**, selecione a coorte você acabou de criar.  Os dados para o gráfico são limitados a esses usuários.

    ![Coorte na ferramenta de usuários](media\app-insights-tutorial-users\UsersCohort.png)


## <a name="compare-desired-activity-to-reality"></a>Compare a atividade desejada com a realidade
Enquanto os painéis anteriores são voltados para o que os usuários do seu aplicativo fizeram, o item **Funis** concentra-se no que você deseja que os usuários façam.  Um funil representa um conjunto de etapas em seu aplicativo e o percentual de usuários que se move entre as etapas.  Por exemplo, você pode criar um funil que meça o percentual de usuários que se conectam ao seu aplicativo que pesquisa produtos.  Então você pode ver o percentual de usuários que adicionam esse produto a um carrinho de compras e, em seguida, o percentual daqueles que concluem uma compra.

1. Selecione **Funis** no menu e clique em **Novo**. 

    ![](media\app-insights-tutorial-users\funnelsnew.png)

2. Digite um **Nome de funil**.
3. Crie um funil pelo menos duas etapas selecionando uma ação para cada etapa.  A lista de ações é criada com base em dados de uso coletados pelo Application Insights.

    ![](media\app-insights-tutorial-users\funnelsedit.png)

4. Clique em **Salvar** para salvar o funil e exibir seus resultados.  A janela à direita do funil mostra os eventos mais comuns antes da primeira atividade e depois da última atividade para ajudá-lo a entender as tendências do usuário em torno de determinada sequência.

    ![](media\app-insights-tutorial-users\funnelsright.png)


## <a name="learn-which-customers-return"></a>Saiba quais clientes retornam
**Retenção** o ajuda a entender quais usuários são voltando ao seu aplicativo.  

1. Selecione **Retenção** no menu.
2. Por padrão, as informações analisadas incluem usuários que executaram qualquer ação e então voltaram para realizar qualquer ação.  Você pode alterar esse filtro para qualquer inclusão, por exemplo, somente os usuários que retornaram após a conclusão de uma compra.

    ![](media\app-insights-tutorial-users\retentionquery.png)

3. Os usuários recorrentes que correspondem aos critérios são mostrados nos formatos de gráfico e tabela para diferentes durações de tempo.  O padrão comum é para uma queda gradual nos usuários recorrentes ao longo do tempo.  Uma queda repentina de um período para a próximo pode gerar uma preocupação. 

    ![](media\app-insights-tutorial-users\retentiongraph.png)

## <a name="analyze-user-navigation"></a>Analisar a navegação do usuário
Um **Fluxo de usuário** visualiza como os usuários navegam entre as páginas e recursos de seu aplicativo.  Isso ajuda a responder perguntas como a movimentação típica dos usuários de uma página específica, como eles costumam sair de seu aplicativo e se há alguma ação repetida regularmente.

1.  Selecione **Fluxos do usuário** no menu.
2.  Clique em **Novo** para criar um novo fluxo de usuário e, em seguida, clique em **Editar** para editar seus detalhes.
3.  Aumente o **Intervalo de tempo** para sete dias e, em seguida, selecione um evento inicial.  O fluxo rastreará as sessões do usuário que começam com esse evento.

    ![](media\app-insights-tutorial-users\flowsedit.png)

4.  O fluxo de usuário é exibido e você pode ver os diferentes caminhos de usuário e suas contagens de sessão.  Linhas azuis indicam uma ação que o usuário executou depois da ação atual.  Uma linha vermelha indica o fim da sessão do usuário.

    ![](media\app-insights-tutorial-users\flows.png)

5.  Para remover um evento do fluxo, clique no **x** no canto da ação e clique em **Criar Gráfico**.  O gráfico é redesenhado com todas as instâncias desse evento removidas.  Clique em **Editar** para ver se o evento agora foi adicionado aos **Eventos excluídos**.

    ![](media\app-insights-tutorial-users\flowsexclude.png)

## <a name="consolidate-usage-data"></a>Consolidar dados de uso
**Pastas de trabalho** combinam visualizações de dados, consultas do Analytics e texto em documentos interativos.  Você pode usar pastas de trabalho para agrupar informações de uso comum, consolidar informações de um incidente específico ou relatar para sua equipe sobre o uso do aplicativo.

1.  Selecione **Pastas de trabalho** no menu.
2.  Clique em **Novo** para criar uma nova pasta de trabalho.
3.  Já é fornecida uma consulta que inclui todos os dados de uso no último dia, exibido como um gráfico de barras.  Você pode usar essa consulta, editá-la manualmente ou clicar em **Exemplos de consultas** para selecionar outras consultas úteis.

    ![](media\app-insights-tutorial-users\samplequeries.png)

4.  Clique em **Edição concluída**.
5.  Clique em **Editar** no painel superior para editar o texto na parte superior da pasta de trabalho.  Isso é formatado usando markdown.

    ![](media\app-insights-tutorial-users\markdown.png)

6.  Clique em **Adicionar usuários** para adicionar um gráfico com informações do usuário.  Edite os detalhes do gráfico se quiser e, em seguida, clique em **Concluir a edição** para salvar.


## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a analisar seus usuários, avance para o próximo tutorial para aprender a criar painéis personalizados que combinam essas informações a outros dados úteis sobre o seu aplicativo.

> [!div class="nextstepaction"]
> [Criar painéis personalizados](app-insights-tutorial-dashboards.md)