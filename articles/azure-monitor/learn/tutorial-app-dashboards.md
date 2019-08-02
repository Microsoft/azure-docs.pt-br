---
title: Criar painéis personalizados no Azure Application Insights | Microsoft Docs
description: Tutorial para criar painéis de KPI personalizados usando o Azure Application Insights.
keywords: ''
services: application-insights
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: fa91933c2243cd1a2737f93f796314330b6cf5a0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541485"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Criar painéis de KPI personalizados usando o Azure Application Insights

Você pode criar vários painéis no portal do Azure que incluam dados de visualização de blocos de vários recursos do Azure entre diferentes assinaturas e os grupos de recursos.  Você pode fixar diferentes gráficos e modos de exibição do Azure Application Insights para criar painéis personalizados que forneçam um panorama completo da integridade e do desempenho do seu aplicativo. Este tutorial orienta você durante a criação de um painel personalizado que inclui vários tipos de dados e visualizações do Azure Application Insights.  Você aprenderá como:

> [!div class="checklist"]
> * Criar um painel personalizado no Azure
> * Adicionar um bloco da Galeria de blocos
> * Adicionar métricas padrão no Application Insights ao painel
> * Adicionar um gráfico de métricas personalizadas do Application Insights ao painel
> * Adicionar os resultados de uma consulta Logs (Analytics) ao painel



## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Implante um aplicativo .NET para Azure e [habilite o SDK do Application Insights](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Crie um novo painel
Um único painel pode conter recursos de vários aplicativos, grupos de recursos e assinaturas.  Inicie o tutorial criando um novo painel para seu aplicativo.  

1. No painel, selecione **Novo painel**.

   ![Novo painel](media/tutorial-app-dashboards/1newdashboard.png)

1. Digite um nome para o painel.
1. Examine a **Galeria de blocos** para uma variedade de blocos que você pode adicionar ao seu painel.  Além de adicionar blocos da galeria, você pode fixar gráficos e outros modos de exibição diretamente do Application Insights ao painel.
1. Localize o bloco **Markdown** e arraste-o para seu painel.  Esse bloco permite que você adicione texto formatado em markdown, o que é ideal para adicionar texto descritivo ao seu painel.
1. Adicione texto às propriedades do bloco e redimensione-o na tela do painel.
    
    ![Editar bloco de markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Clique em **Personalização concluída** na parte superior da tela para sair do modo de personalização de bloco.

## <a name="add-health-overview"></a>Adicionar a visão geral da integridade
Um painel com texto estático não é muito interessante, então agora adicione um bloco do Application Insights para mostrar informações sobre o seu aplicativo.  Você pode adicionar blocos do Application Insights da Galeria de blocos ou você pode fixá-los diretamente das telas do Application Insights.  Isso permite que você configure gráficos e exibições com os quais você já está familiarizado antes de fixá-los ao seu painel.  Comece adicionando a visão geral de integridade padrão para seu aplicativo.  Isso não requer nenhuma configuração e permite personalização mínima no painel.


1. Selecione o recurso do **Application Insights** na tela inicial.
2. No painel **Visão geral**, clique no ícone de fixação ![ícone de fixação](media/tutorial-app-dashboards/pushpin.png) para adicionar o bloco ao painel que foi exibido por último.  
 
3. No canto superior direito, será exibida uma notificação indicando que o bloco foi fixado no painel. Clique em **Fixado no painel** na notificação para retornar ao painel ou usar o painel.
4. Agora, esse bloco foi adicionado ao painel. Selecione **Editar** para alterar o posicionamento do bloco. Clique e arraste-o para a posição e, em seguida, clique em **Personalização concluída**. Seu painel agora tem um bloco com algumas informações úteis.

    ![Linha do tempo de Painel com de Visão Geral](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Adicionar gráfico de métrica personalizada
O painel **Métricas** permite fazer o grafo de uma métrica coletada pelo Application Insights ao longo do tempo com filtros opcionais e agrupamento.  Como tudo no Application Insights, você pode adicionar este gráfico ao painel.  Isso requer um pouco personalização primeiro.

1. Selecione o recurso do **Application Insights** na tela inicial.
1. Selecione **Métricas**.  
2. Um gráfico vazio já foi criado e você é solicitado a adicionar uma métrica.  Adicionar uma métrica ao gráfico e, opcionalmente, adicione um filtro e um agrupamento.  O exemplo a seguir mostra o número de solicitações de servidor agrupadas por sucesso.  Isso apresenta uma exibição em execução de solicitações bem-sucedidas e malsucedidas.

    ![Adicionar métrica](media/tutorial-app-dashboards/metrics.png)

4. Selecione **Fixar no painel** à direita. Isso adiciona a exibição ao último painel com o qual você estava exibindo.

3.  No canto superior direito, será exibida uma notificação indicando que o bloco foi fixado no painel. Clique em **Fixado no painel** na notificação para retornar ao painel ou usar a folha do painel.

4. Agora, esse bloco foi adicionado ao painel. Selecione **Editar** para alterar o posicionamento do bloco. Clique e arraste-o para a posição e, em seguida, clique em **Personalização concluída**.

## <a name="add-logs-analytics-query"></a>Adicionar uma consulta Logs (Analytics)
O Logs (Analytics) do Azure Application Insights oferece uma linguagem de consulta avançada que permite analisar todos os dados coletados pelo Application Insights. Assim como os gráficos e outros modos de exibição, você pode adicionar a saída de uma consulta de logs ao seu painel.

Como o Logs (Analytics) do Azure Applications Insights é um serviço separado, é preciso compartilhar seu painel para que ele inclua uma consulta de logs. Ao compartilhar um painel do Azure, publique-o como um recurso do Azure que pode disponibilizá-lo a outros usuários e recursos.  

1. Na parte superior da tela do painel, clique em **Compartilhamento**.

    ![Publicar painel](media/tutorial-app-dashboards/8dashboard-share.png)

2. Mantenha o **Nome do painel** igual e selecione o **Nome da assinatura** para compartilhar o painel.  Clique em **Publicar**.  O painel de controle agora está disponível para outros serviços e assinaturas.  Opcionalmente, você pode definir usuários específicos que devem ter acesso ao painel.
1. Selecione o recurso do **Application Insights** na tela inicial.
2. Clique em **Logs (Analytics)** à esquerda sob Monitoramento para abrir o portal correspondente.
3. Digite a consulta a seguir, que retorna as 10 páginas mais solicitadas e sua contagem de solicitação:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Clique em **Executar** para validar os resultados da consulta.
5. Clique no ícone de fixação ![Ícone de fixação](media/tutorial-app-dashboards/pushpin.png) e selecione o nome do seu painel. O para motivo pelo qual essa opção fez você selecionar um painel, ao contrário das etapas anteriores, em que era usado o painel mais recente, é que o console Logs (Analytics) é um serviço separado e precisa selecionar de todos os painéis compartilhados disponíveis.

5. Antes de voltar para o painel, adicione outra consulta, mas desta vez renderize-a como um gráfico para ver as diferentes maneiras de visualizar uma consulta de logs no painel. Comece com a consulta a seguir que resume as 10 principais operações com a maioria das exceções.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Selecione **Gráfico** e, em seguida, altere para **Rosca** para visualizar a saída.

    ![Gráfico de Logs (Analytics)](media/tutorial-app-dashboards/11querychart.png)

6. Clique no ícone de fixação ![Ícone de fixação](media/tutorial-app-dashboards/pushpin.png) no canto superior direito para fixar o gráfico ao seu painel e, desta vez, selecione o link para retornar ao painel.
4. Os resultados das consultas agora são adicionados ao painel no formato selecionado.  Clique e arraste cada um para a posição e, em seguida, clique em **Personalização concluída**.
5. Selecione o ícone de lápis ![Ícone de lápis](media/tutorial-app-dashboards/pencil.png) em cada título para fornecer um título descritivo.

5. Selecione **Compartilhar** para publicar as alterações novamente no painel, que agora inclui uma variedade de gráficos e visualizações do Application Insights.


## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a criar painéis personalizados, dê uma olhada no restante da documentação do Application Insights, incluindo um estudo de caso.

> [!div class="nextstepaction"]
> [Diagnóstico profundo](../../azure-monitor/app/devops.md)
