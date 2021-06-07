---
title: Criar painéis personalizados no Azure Application Insights | Microsoft Docs
description: Tutorial para criar painéis de KPI personalizados usando o Azure Application Insights.
ms.topic: tutorial
author: lgayhardt
ms.author: lagayhar
ms.date: 09/30/2020
ms.custom: mvc, contperf-fy21q1
ms.openlocfilehash: 7bad0b17bb2fed9d9f54cdd44042ec88708f35fd
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103718"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Criar painéis de KPI personalizados usando o Azure Application Insights

Você pode criar vários painéis no portal do Azure que incluam dados de visualização de blocos de vários recursos do Azure entre diferentes assinaturas e os grupos de recursos.  Você pode fixar diferentes gráficos e modos de exibição do Azure Application Insights para criar painéis personalizados que forneçam um panorama completo da integridade e do desempenho do seu aplicativo. Este tutorial orienta você durante a criação de um painel personalizado que inclui vários tipos de dados e visualizações do Azure Application Insights.

 Você aprenderá como:

> [!div class="checklist"]
> * Criar um painel personalizado no Azure
> * Adicionar um bloco da Galeria de blocos
> * Adicionar métricas padrão no Application Insights ao painel
> * Adicionar um gráfico de métricas personalizadas do Application Insights ao painel
> * Adicionar os resultados de uma consulta Logs (Analytics) ao painel

## <a name="prerequisites"></a>Prerequisites

Para concluir este tutorial:

- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](../app/asp-net.md).

> [!NOTE]
> As permissões necessárias para trabalhar com painéis são discutidas no artigo sobre [Noções básicas do controle de acesso para painéis](../../azure-portal/azure-portal-dashboard-share-access.md#understanding-access-control-for-dashboards).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Crie um novo painel

> [!WARNING]
> Se você mover o recurso do Application Insights para um grupo de recursos ou uma assinatura diferente, será necessário atualizar manualmente o painel, removendo os blocos antigos e fixando novos blocos do mesmo recurso do Application Insights na nova localização.

Um único painel pode conter recursos de vários aplicativos, grupos de recursos e assinaturas.  Inicie o tutorial criando um novo painel para seu aplicativo.  

1. Na lista suspensa de menus à esquerda no portal do Azure, selecione **Painel**.

    ![Lista suspensa de menus do portal do Azure](media/tutorial-app-dashboards/dashboard-from-menu.png)

2. No painel, selecione **Novo painel** e **Painel em branco**.

   ![Novo painel](media/tutorial-app-dashboards/new-dashboard.png)

3. Digite um nome para o painel.
4. Examine a **Galeria de blocos** para uma variedade de blocos que você pode adicionar ao seu painel.  Além de adicionar blocos da galeria, você pode fixar gráficos e outros modos de exibição diretamente do Application Insights ao painel.
5. Localize o bloco **Markdown** e arraste-o para seu painel.  Esse bloco permite que você adicione texto formatado em markdown, o que é ideal para adicionar texto descritivo ao seu painel. Para saber mais, confira [Usar um bloco Markdown em painéis do Azure para mostrar conteúdo personalizado](../../azure-portal/azure-portal-markdown-tile.md).
6. Adicione texto às propriedades do bloco e redimensione-o na tela do painel.

    [![Editar bloco de markdown](media/tutorial-app-dashboards/markdown.png)](media/tutorial-app-dashboards/markdown.png#lightbox)

7. Selecione **Personalização concluída** na parte superior da tela para sair do modo de personalização de bloco.

## <a name="add-health-overview"></a>Adicionar a visão geral da integridade

Um painel com texto estático não é muito interessante, então agora adicione um bloco do Application Insights para mostrar informações sobre o seu aplicativo. Você pode adicionar blocos do Application Insights da Galeria de blocos ou você pode fixá-los diretamente das telas do Application Insights. Isso permite que você configure gráficos e exibições com os quais você já está familiarizado antes de fixá-los ao seu painel.  Comece adicionando a visão geral de integridade padrão para seu aplicativo.  Isso não requer nenhuma configuração e permite personalização mínima no painel.


1. Selecione o recurso do **Application Insights** na tela inicial.
2. No painel **Visão geral**, selecione o ícone de pino ![ícone de pino](media/tutorial-app-dashboards/pushpin.png) para adicionar o bloco a um painel.
3. Na guia "Fixar no painel", selecione o painel ao qual adicionar o bloco ou crie um.
 
3. No canto superior direito, será exibida uma notificação indicando que o bloco foi fixado no painel.  Selecione **Fixado no painel** na notificação para retornar ao painel ou usá-lo.
4. Agora, esse bloco foi adicionado ao painel. Selecione **Editar** para alterar o posicionamento do bloco. Selecione e arraste-o para a posição e selecione **Personalização concluída**. Seu painel agora tem um bloco com algumas informações úteis.

    [![Painel no modo de edição](media/tutorial-app-dashboards/dashboard-edit-mode.png)](media/tutorial-app-dashboards/dashboard-edit-mode.png#lightbox)

## <a name="add-custom-metric-chart"></a>Adicionar gráfico de métrica personalizada

O painel **Métricas** permite fazer o grafo de uma métrica coletada pelo Application Insights ao longo do tempo com filtros opcionais e agrupamento.  Como tudo no Application Insights, você pode adicionar este gráfico ao painel.  Isso requer um pouco personalização primeiro.

1. Selecione o recurso do **Application Insights** na tela inicial.
1. Selecione **Métricas**.  
2. Um gráfico vazio já foi criado e você é solicitado a adicionar uma métrica.  Adicionar uma métrica ao gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo a seguir mostra o número de solicitações de servidor agrupadas por sucesso.  Isso apresenta uma exibição em execução de solicitações bem-sucedidas e malsucedidas.

    [![Adicionar métrica](media/tutorial-app-dashboards/metrics.png)](media/tutorial-app-dashboards/metrics.png#lightbox)

4. Selecione **Fixar no painel** à direita.

3.  No canto superior direito, será exibida uma notificação indicando que o bloco foi fixado no painel. Selecione **Fixado no painel** na notificação para retornar ao painel ou usar a guia do painel.

4. Agora, esse bloco foi adicionado ao painel. Selecione **Editar** para alterar o posicionamento do bloco. Selecione e arraste o bloco para a posição e selecione **Personalização concluída**.

## <a name="add-logs-query"></a>Adicionar consulta de logs

Os Logs do Azure Application Insights oferecem uma linguagem de consulta avançada que permite analisar todos os dados coletados pelo Application Insights. Assim como os gráficos e outros modos de exibição, você pode adicionar a saída de uma consulta de logs ao seu painel.

1. Selecione o recurso do **Application Insights** na tela inicial.
2. Selecione **Logs** à esquerda, em "Monitoramento", para abrir a guia Logs.
3. Digite a consulta a seguir, que retorna as 10 páginas mais solicitadas e sua contagem de solicitação:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Selecione **Executar** para validar os resultados da consulta.
5. Selecione o ícone fixar ![Ícone de fixação](media/tutorial-app-dashboards/pushpin.png) e selecione o nome do seu painel.

5. Antes de voltar para o painel, adicione outra consulta, mas renderize-a como um gráfico para ver as diferentes maneiras de visualizar uma consulta de logs no painel. Comece com a consulta a seguir que resume as 10 principais operações com a maioria das exceções.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selecione **Gráfico** e, em seguida, altere para **Rosca** para visualizar a saída.

    [![Gráfico de rosca com a consulta acima](media/tutorial-app-dashboards/logs-doughnut.png)](media/tutorial-app-dashboards/logs-doughnut.png#lightbox)

6. Selecione o ícone fixar ![Ícone de fixação](media/tutorial-app-dashboards/pushpin.png) no canto superior direito para fixar o gráfico ao seu painel e retorne ao painel.
7. Os resultados das consultas agora são adicionados ao painel no formato selecionado. Selecione e arraste cada um deles para a posição e selecione **Personalização concluída**.
8. Selecione o ícone de lápis ![Ícone de lápis](media/tutorial-app-dashboards/pencil.png) em cada título para fornecer um título descritivo.

## <a name="share-dashboard"></a>Compartilhar painel

1. Na parte superior do painel, selecione **Compartilhar** para publicar as alterações.
2. Opcionalmente, você pode definir usuários específicos que devem ter acesso ao painel. Para obter mais informações, confira [Compartilhar painéis do Azure usando o controle de acesso baseado em função do Azure](../../azure-portal/azure-portal-dashboard-share-access.md).
3. Selecione **Publicar**.

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu a criar painéis personalizados, dê uma olhada no restante da documentação do Application Insights, incluindo um estudo de caso.

> [!div class="nextstepaction"]
> [Diagnóstico profundo](../app/devops.md)
