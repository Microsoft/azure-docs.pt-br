---
title: "Criar painéis personalizados no Azure Application Insights | Microsoft Docs"
description: "Tutorial para criar painéis de KPI personalizados usando o Azure Application Insights."
keywords: 
services: application-insights
author: bwren
ms.author: bwren
ms.date: 09/20/2017
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: 3ad1bfe2c45fd087f094aad5b2028ab488dbb2a9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Criar painéis de KPI personalizados usando o Azure Application Insights

Você pode criar vários painéis no portal do Azure que incluam dados de visualização de blocos de vários recursos do Azure entre diferentes assinaturas e os grupos de recursos.  Você pode fixar diferentes gráficos e modos de exibição do Azure Application Insights para criar painéis personalizados que forneçam um panorama completo da integridade e do desempenho do seu aplicativo.  Este tutorial orienta você durante a criação de um painel personalizado que inclui vários tipos de dados e visualizações do Azure Application Insights.  Você aprenderá como:

> [!div class="checklist"]
> * Criar um painel personalizado no Azure
> * Adicionar um bloco da Galeria de blocos
> * Adicionar métricas padrão no Application Insights ao painel 
> * Adicionar um gráfico de métricas personalizadas do Application Insights ao painel
> * Adicionar os resultados de uma consulta do Analytics ao painel 



## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](app-insights-asp-net.md). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Crie um novo painel
Um único painel pode conter recursos de vários aplicativos, grupos de recursos e assinaturas.  Inicie o tutorial criando um novo painel para seu aplicativo.  

2.  Na tela principal do portal, selecione **Novo painel**.

    ![Novo painel](media/app-insights-tutorial-dashboards/new-dashboard.png)

3. Digite um nome para o painel.
4. Examine a **Galeria de blocos** para uma variedade de blocos que você pode adicionar ao seu painel.  Além de adicionar blocos da galeria, você pode fixar gráficos e outros modos de exibição diretamente do Application Insights ao painel.
5. Localize o bloco **Markdown** e arraste-o para seu painel.  Esse bloco permite que você adicione texto formatado em markdown, o que é ideal para adicionar texto descritivo ao seu painel.
6. Adicione texto às propriedades do bloco e redimensione-o na tela do painel.
    
    ![Editar bloco de markdown](media/app-insights-tutorial-dashboards/edit-markdown.png)

6. Clique em **Personalização concluída** na parte superior da tela para sair do modo de personalização de bloco e, em seguida, **Publicar alterações** para salvar suas alterações.

    ![Painel com bloco de markdown](media/app-insights-tutorial-dashboards/dashboard-01.png)


## <a name="add-health-overview"></a>Adicionar a visão geral da integridade
Um painel com texto estático apenas não é muito interessante, então agora adicione um bloco do Application Insights para mostrar informações sobre o seu aplicativo.  Você pode adicionar blocos do Application Insights da Galeria de blocos ou você pode fixá-los diretamente das telas do Application Insights.  Isso permite que você configure gráficos e exibições com os quais você já está familiarizado antes de fixá-los ao seu painel.  Comece adicionando a visão geral de integridade padrão para seu aplicativo.  Isso não requer nenhuma configuração e permite personalização mínima no painel.


1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione seu aplicativo.
2. No **Cronograma de visão geral**, selecione o menu de contexto e clique em **Fixar ao painel**.  Isso adiciona o bloco ao último painel que você estava exibindo.  

    ![Fixar linha do tempo de Visão Geral](media/app-insights-tutorial-dashboards/pin-overview-timeline.png)
 
3. Na parte superior da tela, clique em **Exibir painel** para retornar ao painel.
4. A linha do tempo de Visão Geral agora é adicionada ao seu painel.  Clique e arraste-a para a posição e, em seguida, clique em **Personalização concluída** e **Publicar alterações**.  Seu painel agora tem um bloco com algumas informações úteis.

    ![Linha do tempo de Painel com de Visão Geral](media/app-insights-tutorial-dashboards/dashboard-02.png)



## <a name="add-custom-metric-chart"></a>Adicionar gráfico de métrica personalizada
O painel **Métricas** permite fazer o gráfico de uma métrica coletada pelo Application Insights ao longo do tempo com filtros opcionais e agrupamento.  Como tudo no Application Insights, você pode adicionar este gráfico ao painel.  Isso requer um pouco personalização primeiro.

1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione seu aplicativo.
1. Selecione **Métricas**.  
2. Um gráfico vazio já foi criado e você é solicitado a adicionar uma métrica.  Adicionar uma métrica ao gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo a seguir mostra o número de solicitações de servidor agrupadas por sucesso.  Isso apresenta uma exibição em execução de solicitações bem-sucedidas e malsucedidas.

    ![Adicionar métrica](media/app-insights-tutorial-dashboards/metrics-chart.png)

4. Selecione o menu de contexto do gráfico e selecione **Fixar ao painel**.  Isso adiciona a exibição ao último painel com o qual você estava exibindo.

    ![Fixar gráfico de métrica](media/app-insights-tutorial-dashboards/pin-metrics-chart.png)

3. Na parte superior da tela, clique em **Exibir painel** para retornar ao painel.

4. A Linha do Tempo de Visão Geral agora é adicionada ao seu painel.  Clique e arraste-a para a posição e, em seguida, clique em **Personalização concluída** e **Publicar alterações**. 

    ![Painel com métricas](media/app-insights-tutorial-dashboards/dashboard-03.png)


## <a name="metrics-explorer"></a>Metrics Explorer
O **Metrics Explorer** é semelhante a Métricas, embora permita significativamente mais personalização quando adicionado ao painel.  O que você usa para colocar suas métricas em gráfico depende das suas preferência e necessidades específicas.

1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione seu aplicativo.
1. Selecione **Metrics Explorer**. 
2. Clique para editar o gráfico e selecione uma ou mais métricas e, opcionalmente, uma configuração detalhada.  O exemplo exibe um gráfico de linhas acompanhando o tempo médio de resposta da página.
3. Clique no ícone de fixar no canto superior direito para adicionar o gráfico ao seu painel e, em seguida, arrastá-lo para a posição.

    ![Metrics Explorer](media/app-insights-tutorial-dashboards/metrics-explorer.png)

4. O bloco do Metrics Explorer permite mais personalização depois de ele ser adicionado ao painel.  Clique com o botão direito do mouse no bloco e selecione **Editar título** para adicionar um título personalizado.  Vá em frente e faça outras personalizações, se desejar.

    ![Painel com Metrics Explorer](media/app-insights-tutorial-dashboards/dashboard-04a.png)

5. Agora você tem o gráfico do Metrics Explorer adicionado ao seu painel.

    ![Painel com Metrics Explorer](media/app-insights-tutorial-dashboards/dashboard-04.png)

## <a name="add-analytics-query"></a>Adicionar uma consulta do Analytics
O Azure Application Insights Analytics oferece uma linguagem de consulta avançada que permite analisar todos os dados coletados pelo Application Insights.  Assim como os gráficos e outros modos de exibição, você pode adicionar a saída de uma consulta do Analytics ao seu painel.   

Como o Azure Applications Insights Analytics é um serviço separado, você precisa compartilhar seu painel para que ele inclua uma consulta do Analytics.  Quando você compartilha um painel do Azure, publica-o como um recurso do Azure que pode disponibilizá-lo a outros usuários e recursos.  

1. Na parte superior da tela do painel, clique em **Compartilhamento**.

    ![Publicar painel](media/app-insights-tutorial-dashboards/publish-dashboard.png)

2. Mantenha o **Nome do painel** igual e selecione o **Nome da assinatura** para compartilhar o painel.  Clique em **Publicar**.  O painel de controle agora está disponível para outros serviços e assinaturas.  Opcionalmente, você pode definir usuários específicos que devem ter acesso ao painel.
1. Selecione **Application Insights** no menu do Azure e, em seguida, selecione seu aplicativo.
2. Clique em **Analytics** na parte superior da tela para abrir o portal do Analytics.

    ![Iniciar o Analytics](media/app-insights-tutorial-dashboards/start-analytics.png)

3. Digite a consulta a seguir, que retorna as 10 páginas mais solicitadas e sua contagem de solicitação:

    ```
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10 
    ```

4. Clique em **Ir** para validar os resultados da consulta.
5. Clique no ícone de fixar e selecione o nome do seu painel.  O para motivo pelo qual essa opção fez você selecionar um painel, ao contrário das etapas anteriores, em que o último painel era usado, é que o console do Analytics é um serviço separado e precisa selecionar de todos os painéis compartilhados disponíveis.

    ![Fixar consulta do Analytics](media/app-insights-tutorial-dashboards/analytics-pin.png)

5. Antes de você voltar para o painel, adicione outra consulta, mas desta vez renderize-a como um gráfico para ver as diferentes maneiras de visualizar uma consulta do Analytics no painel.  Comece com a consulta a seguir que resume as 10 principais operações com a maioria das exceções.

    ```
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10 
    ```

6. Selecione **Gráfico** e, em seguida, altere para **Rosca** para visualizar a saída.

    ![Gráfico de do Analytics](media/app-insights-tutorial-dashboards/analytics-chart.png)

6. Clique no ícone de pino para fixar o gráfico ao seu painel e, desta vez, selecione o link para retornar ao seu painel.
4. Os resultados das consultas agora são adicionados ao painel no formato selecionado.  Clique e arraste cada um para a posição e, em seguida, clique em **Concluir edição**.
5. Clique com o botão direito do mouse em cada um dos blocos e selecione **Editar título** dar a ele um título descritivo.

    ![Painel de com o Analytics](media/app-insights-tutorial-dashboards/dashboard-05.png)

5. Clique em **Publicar alterações** para confirmar as alterações ao seu painel, que agora inclui uma variedade de gráficos e visualizações do Application Insights.


## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a criar painéis personalizados, dê uma olhada no restante da documentação do Application Insights, incluindo um estudo de caso.

> [!div class="nextstepaction"]
> [Diagnóstico profundo](app-insights-devops.md)