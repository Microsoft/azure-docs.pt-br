---
title: Criar e compartilhar painéis de dados do Azure Log Analytics | Microsoft Docs
description: Este tutorial ajuda você a entender como os painéis do Log Analytics mostram todas as suas consultas de log salvas, fornecendo a você uma exibição única do seu ambiente.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c859fb9b8b3509e8369559a3a9a4d45cb4e34125
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414143"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Criar e compartilhar painéis de dados do Log Analytics

Os painéis do Log Analytics podem mostrar todas as consultas de logs salvas, oferecendo a você a capacidade de localizar, correlacionar e compartilhar dados operacionais de TI na organização.  Este tutorial aborda a criação de uma consulta de logs que será usada para dar suporte a um painel compartilhado que, por sua vez, será acessado por sua equipe de suporte de operações de TI.  Você aprenderá como:

> [!div class="checklist"]
> * Criar um painel compartilhado no portal do Azure
> * Visualizar uma consulta de logs de desempenho 
> * Adicionar uma consulta de logs a um painel compartilhado 
> * Personalizar um bloco em um painel compartilhado

Para concluir o exemplo neste tutorial, você deve ter uma máquina virtual existente [conectada ao espaço de trabalho do Log Analytics](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Entre no portal do Azure
Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Criar um painel compartilhado
Selecione **Painel** para abrir o [painel](../../azure-portal/azure-portal-dashboards.md) padrão. Seu painel terá uma aparência diferente do exemplo a seguir.

![Painel do portal do Azure](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Aqui você pode reunir dados operacionais que são mais importantes para a TI em todos os seus recursos do Azure, incluindo a telemetria do Azure Log Analytics.  Antes de começarmos a visualizar uma consulta de logs, vamos criar um painel e compartilhá-lo.  Depois, podemos nos concentrar em nosso exemplo de consulta do log de desempenho, que será renderizado como um gráfico de linhas, e adicioná-lo ao painel.  

Para criar um painel, selecione o botão **Novo painel** ao lado do nome do painel atual.

![Criar um novo painel no portal do Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Essa ação cria um painel novo, vazio, privado e coloca você no modo de personalização onde é possível nomear seu painel e adicionar ou reorganizar blocos. Edite o nome do painel e especifique *Painel de Exemplo* para este tutorial e, em seguida, selecione **Personalização concluída**.<br><br> ![Salvar o painel personalizado do Azure](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Quando você cria um painel, ele será privado por padrão, o que significa que você é a única pessoa que pode vê-lo. Para torná-lo visível para outras pessoas, use o botão **Compartilhar** exibido juntamente com os outros comandos do painel.

![Compartilhar um novo painel no portal do Azure](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

Será solicitado que você escolha uma assinatura e o grupo de recursos para que seu painel seja publicado. Para sua conveniência, a experiência de publicação do portal guia você por um padrão onde você coloca painéis em um grupo de recursos chamado **painéis**.  Confirme a assinatura selecionada e, em seguida, clique em **Publicar**.  O acesso às informações exibidas no painel é controlado com o [Controle de Acesso Baseado em Recursos do Azure](../../role-based-access-control/role-assignments-portal.md).   

## <a name="visualize-a-log-query"></a>Visualizar uma consulta de logs
[Log Analytics](../log-query/get-started-portal.md) é um portal dedicado usado para trabalhar com consultas de log e seus resultados. Os recursos incluem a capacidade de editar uma consulta em várias linhas e de executar código seletivamente, além do IntelliSense sensível ao contexto e da análise inteligente. Neste tutorial, você usará o Log Analytics para criar uma exibição de desempenho em formato gráfico, salvá-la para pesquisa futura e fixá-la no painel compartilhado criado anteriormente.

Abra o Log Analytics selecionando **Logs** no menu do Azure Monitor. Ele começa com uma nova consulta em branco.

![Página inicial](media/tutorial-logs-dashboards/homepage.png)

Insira a seguinte consulta para retornar registros de utilização de processador para computadores Windows e Linux, agrupados por Computer e TimeGenerated, e exibidos em um gráfico de visual. Clique em **Executar** para executar a consulta e exibir o gráfico resultante.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Salve a consulta selecionando o botão **Salvar** na parte superior da página.

![Salvar consulta](media/tutorial-logs-dashboards/save-query.png)

No painel de controle **Salvar Consulta**, forneça um nome como *VMs do Azure - Utilização do Processador* e uma categoria como *Painéis* e depois clique em **Salvar**.  Dessa forma, você pode criar uma biblioteca de consultas comuns que pode ser usada e modificada.  Por fim, fixe isso no painel compartilhado criado anteriormente selecionando o botão **Fixar no painel** no canto superior direito da página e, em seguida, selecionando o nome do painel.

Agora que temos uma consulta fixada no painel, você observará que ele tem um título genérico e um comentário abaixo.

![Amostra de painel do Azure](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Devemos renomeá-lo com um nome significativo que pode ser compreendido com facilidade por aqueles que o exibem.  Clique no botão Editar para personalizar o título e o subtítulo do bloco e, depois, clique em **Atualizar**.  Uma faixa será exibida perguntando se você desejar publicar as alterações ou descartá-las.  Clique em **Salvar uma cópia**.  

![Configuração concluída do painel de exemplo](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você aprendeu a criar um painel no portal do Azure e adicionar uma consulta de logs a ele.  Vá para o próximo tutorial para saber mais sobre as diferentes respostas que podem ser implementadas com base nos resultados da consulta de logs.  

> [!div class="nextstepaction"]
> [Responder a eventos com alertas do Log Analytics](tutorial-response.md)
