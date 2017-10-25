---
title: "Criar e compartilhar painéis de dados do Azure Log Analytics | Microsoft Docs"
description: "Este tutorial ajuda a entender como os painéis do Log Analytics podem visualizar todas as pesquisas de logs salvas, fornecendo a você uma lente única para exibir seu ambiente."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: b065269a27ad3764399802b4a2bc3076b7349623
ms.contentlocale: pt-br
ms.lasthandoff: 09/25/2017

---

# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Criar e compartilhar painéis de dados do Log Analytics

Os painéis do Log Analytics podem visualizar todas as pesquisas de logs salvas, oferecendo a você a capacidade de localizar, correlacionar e compartilhar dados operacionais de TI na organização.  Este tutorial aborda a criação de uma pesquisa de logs que será usada para dar suporte a um painel compartilhado que, por sua vez, será acessado por sua equipe de suporte de operações de TI.  Você aprenderá como:

> [!div class="checklist"]
> * Criar um painel compartilhado no portal do Azure
> * Visualizar uma pesquisa de logs de desempenho 
> * Adicionar uma pesquisa de logs a um painel compartilhado 
> * Personalizar um bloco em um painel compartilhado

Para concluir o exemplo neste tutorial, você deve ter uma máquina virtual existente [conectada ao espaço de trabalho do Log Analytics](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Fazer logon no portal do Azure
Faça logon no portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Criar um painel compartilhado

A primeira coisa que você vê depois de entrar no portal do Microsoft Azure é um [painel](../azure-portal/azure-portal-dashboards.md).<br> ![Painel do portal do Azure](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Aqui você pode reunir dados operacionais que são mais importantes para a TI em todos os seus recursos do Azure, incluindo a telemetria do Azure Log Analytics.  Antes de começarmos a visualizar uma pesquisa de logs, primeiro vamos criar um painel e compartilhá-lo.  Isso nos permite tirá-lo daqui antes de usarmos nossa pesquisa de logs de desempenho de exemplo, que será renderizada como um gráfico de linhas e a adicionarmos ao painel.  

Para criar um painel, selecione o botão **Novo painel** ao lado do nome do painel atual.<br> ![Criar um novo painel no portal do Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Essa ação cria um painel novo, vazio, privado e coloca você no modo de personalização onde é possível nomear seu painel e adicionar ou reorganizar blocos. Edite o nome do painel e especifique *Painel de Exemplo* para este tutorial e, em seguida, selecione **Personalização concluída**.<br><br> ![Salvar o painel personalizado do Azure](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Quando você cria um painel, ele será privado por padrão, o que significa que você é a única pessoa que pode vê-lo. Para torná-lo visível para outras pessoas, use o botão **Compartilhar** exibido juntamente com os outros comandos do painel.<br> ![Compartilhar um novo painel no portal do Azure](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

Será solicitado que você escolha uma assinatura e o grupo de recursos para que seu painel seja publicado. Para sua conveniência, a experiência de publicação do portal guia você por um padrão onde você coloca painéis em um grupo de recursos chamado **painéis**.  Confirme a assinatura selecionada e, em seguida, clique em **Publicar**.  O acesso às informações exibidas no painel é controlado com o [Controle de Acesso Baseado em Recursos do Azure](../active-directory/role-based-access-control-configure.md).   

## <a name="visualize-a-log-search"></a>Visualizar uma pesquisa de logs

Crie consultas básicas em uma única linha por meio do portal da Pesquisa de Logs no portal do Azure. O portal Pesquisa de Logs pode ser usado sem iniciar um portal externo e você pode usá-lo para executar uma variedade de funções com pesquisas de logs, incluindo a criação de regras de alerta, a criação de grupos de computadores e a exportação dos resultados da consulta. 

O [portal de Análise Avançada](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) é um portal dedicado que fornece funcionalidade avançada não disponível no portal da Pesquisa de Logs. Os recursos incluem a capacidade de editar uma consulta em várias linhas e de executar código seletivamente, além do IntelliSense sensível ao contexto e da análise inteligente. No portal de Análise Avançada, você vai criar uma exibição de desempenho em formato gráfico, salvá-la para uma pesquisa futura e fixá-la no painel compartilhado criado anteriormente.   

Você inicia o portal Análise Avançada de um link no portal Pesquisa de Logs.<br> ![Iniciar o portal de Análise Avançada](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

No portal de Análise, insira a seguinte consulta para retornar apenas registros de utilização de processador para computadores Windows e Linux, agrupados por Computer e TimeGenerated, e exibidos em um gráfico de visual:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

Salve a consulta selecionando o botão **Salvar consulta** no canto superior direito.<br> ![Salvar a consulta por meio do portal de Análise Avançada](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> No painel de controle **Salvar Consulta**, forneça um nome como *VMs do Azure – Utilização do Processador* e, em seguida, clique em **Salvar**.  Dessa forma, você pode criar uma biblioteca de consultas comuns com a qual pesquisar ou modificá-la sem a necessidade de reescrevê-la por completo.  Por fim, fixe isso no painel compartilhado criado anteriormente selecionando o botão **Fixar gráfico no painel do Azure** do canto direito ao centro da página.  

Agora que temos uma consulta fixada no painel, você observará que ele tem um título genérico e um comentário abaixo.<br> ![Amostra de painel do Azure](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Devemos renomeá-lo com um nome significativo que pode ser compreendido com facilidade por aqueles que o exibem.  Clique com o botão direito do mouse no bloco e selecione **Editar bloco**.  Depois de terminar de personalizar o título e o subtítulo do bloco, clique em **Atualizar**.  Uma faixa será exibida perguntando se você desejar publicar as alterações ou descartá-las.  Clique em **Publicar alterações** e, em seguida, feche o painel de controle **Editar Bloco**.  

![Configuração concluída do painel de exemplo](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar um painel no portal do Azure e adicionar uma pesquisa de logs a ele.  Vá para o próximo tutorial para saber mais sobre as diferentes respostas que podem ser implementadas com base nos resultados da pesquisa de logs.  

> [!div class="nextstepaction"]
> [Responder a eventos com alertas do Log Analytics](log-analytics-tutorial-response.md)
