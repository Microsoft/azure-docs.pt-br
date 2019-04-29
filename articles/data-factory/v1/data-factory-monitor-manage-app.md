---
title: Monitorar e gerenciar pipelines de dados - Azure | Microsoft Docs
description: Saiba como usar o aplicativo de Monitoramento e Gerenciamento para monitorar e gerenciar data factories e pipelines do Azure.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5b70edd4f65538b52c70881258bc500a34b04d80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60826351"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Monitorar e gerenciar os pipelines do Azure Data Factory usando o aplicativo de Monitoramento e Gerenciamento
> [!div class="op_single_selector"]
> * [Usando o Portal do Azure/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [Usando o aplicativo de Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Este artigo aplica-se à versão 1 do Data Factory. Se você estiver usando a versão atual do serviço Data Factory, consulte [Monitorar e gerenciar pipelines do Data Factory em](../monitor-visually.md).

Este artigo descreve como usar o aplicativo de Monitoramento e Gerenciamento para monitorar, gerenciar e depurar seus pipelines do Data Factory. Comece a usar o aplicativo assistindo ao seguinte vídeo:

> [!NOTE]
> A interface do usuário mostrada no vídeo pode não corresponder exatamente ao que você vê no portal. Ela é um pouco mais antiga, mas os conceitos permanecem os mesmos. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Iniciar o aplicativo de Monitoramento e Gerenciamento
Para iniciar o aplicativo de Monitoramento e Gerenciamento, clique no bloco **Monitorar e Gerenciar** na folha **Data Factory** do data factory.

![Bloco Monitoramento na home page do Data Factory](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Você verá que o aplicativo de Monitoramento e Gerenciamento foi iniciado em uma janela separada.  

![Aplicativo de Monitoramento e Gerenciamento](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Se você ver que o navegador da Web está bloqueado em "Autorizando...", desabilite/desmarque a caixa de seleção **Bloquear cookies de terceiros e dados de site** (ou) mantenha-a habilitada, crie uma exceção para **login.microsoftonline.com** e tente iniciar o aplicativo novamente.


Na lista Janelas de Atividades no painel central, você vê uma janela de atividade para cada execução de uma atividade. Por exemplo, se você tiver a atividade agendada para execução a cada hora durante cinco horas, você verá cinco janelas de atividades associadas a cinco fatias de dados. Se as janelas de atividades não forem exibidas na lista na parte inferior, faça o seguinte:
 
- Atualize os filtros **hora de início** e **hora de término** na parte superior para que correspondam às horas de início e término do pipeline e, em seguida, clique no botão **Aplicar**.  
- A lista Janelas de Atividades não é atualizada automaticamente. Clique no botão **Atualizar** na barra de ferramentas da lista **Janelas de Atividades**.  

Se você não tiver um aplicativo do Data Factory para ser testado com essas etapas, siga o tutorial [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL usando o Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>Noções básicas sobre o aplicativo de Monitoramento e Gerenciamento
Há três guias à esquerda: **Gerenciador de Recursos**, **Exibições de Monitoramento** e **Alertas**. A primeira guia (**Gerenciador de Recursos**) é selecionada por padrão.

### <a name="resource-explorer"></a>Gerenciador de Recursos
Você verá o seguinte:

* O **modo de exibição de árvore** do Gerenciador de Recursos no painel esquerdo.
* A **Exibição de Diagrama** na parte superior do painel central.
* A lista **Atividade do Windows** na parte inferior no painel central.
* As guias **Propriedades**, **Gerenciador de Janelas de Atividades** e **Script** no painel direito.

No Gerenciador de Recursos, você encontra todos os recursos (pipelines, conjuntos de dados, serviços vinculados) no data factory em um modo de exibição de árvore. Ao selecionar um objeto no Gerenciador de Recursos, você nota o seguinte:

* a entidade Data Factory associada está realçada na Exibição de Diagrama.
* [as janelas de atividades associadas](data-factory-scheduling-and-execution.md) são destacadas na lista Janelas de Atividades na parte inferior.  
* As propriedades do objeto selecionado são mostradas na janela Propriedades no painel direito.
* A definição de JSON do objeto selecionado, se aplicável. Por exemplo: um serviço vinculado, um conjunto de dados ou um pipeline.

![Gerenciador de Recursos](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Consulte o artigo [Agendamento e Execução](data-factory-scheduling-and-execution.md) para obter informações conceituais detalhadas sobre janelas de atividades.

### <a name="diagram-view"></a>Exibição de diagrama
A Exibição de Diagrama de um data factory fornece um único painel no qual você pode monitorar e gerenciar um data factory e seus ativos. Ao selecionar uma entidade Data Factory (conjunto de dados/pipeline) na Exibição de Diagrama, você observa o seguinte:

* a entidade data factory está selecionada no modo de exibição de árvore.
* as janelas de atividades associadas estão destacadas na lista Janelas de Atividades.
* as propriedades do objeto selecionado são mostradas na janela Propriedades.

Quando o pipeline for habilitado (quando não estiver em um estado em pausa), será mostrado com uma linha verde:

![Execução do pipeline](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Você pode pausar, retomar ou terminar um pipeline selecionando-o na exibição de diagrama e usando os botões da barra de comandos.

![Pausar/Retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Observe que há três botões de comando para o pipeline na Exibição de Diagrama. É possível usar o segundo botão para pausar o pipeline. Pausar não encerrará as atividades atualmente em execução e as deixará continuar até a conclusão. O terceiro botão pausa o pipeline e encerra as atividades de execução existentes. O primeiro botão retoma o pipeline. Quando o pipeline está em pausa, sua cor muda. Por exemplo, um pipeline em pausa é parecido com o mostrado na seguinte imagem: 

![Pipeline em pausa](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Você pode fazer uma seleção múltipla de dois ou mais pipelines usando a tecla Ctrl. Você pode usar os botões da barra de comandos para pausar/retomar vários pipelines de uma vez.

Você também pode clicar com o botão direito do mouse em um pipeline e selecionar opções para suspender, retomar ou terminar um pipeline. 

![Menu de contexto do pipeline](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Clique na opção **Abrir pipeline** para ver todas as atividades no pipeline. 

![Menu do pipeline aberto](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

Na exibição aberta do pipeline, você verá todas as atividades no pipeline. Neste exemplo, há apenas uma atividade: Atividade de cópia. 

![Pipeline aberto](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Para voltar para a exibição anterior, clique no nome do data factory, no menu da barra de endereços na parte superior.

Na exibição do pipeline, ao selecionar um conjunto de dados de saída ou mover o mouse sobre o conjunto de dados de saída, você verá a janela pop-up Janelas de Atividades do conjunto de dados.

![Janela pop-up de Atividades do Windows](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

É possível clicar em uma janela de atividade para ver detalhes dela na janela **Propriedades** no painel direito.

![Propriedades da Janela de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

No painel direito, mude para a guia **Gerenciador de Janelas de Atividades** para ver mais detalhes.

![Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Você também verá as **variáveis resolvidas** para cada tentativa de execução de atividade na seção **Tentativas**.

![Variáveis resolvidas](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Mude para a guia **Script** para ver a definição do script JSON para o objeto selecionado.   

![Guia Script](./media/data-factory-monitor-manage-app/ScriptTab.png)

Você pode ver janelas de atividades em três locais:

* Pop-up das Janelas de Atividades na Exibição de Diagrama (painel central).
* Gerenciador de Janelas de Atividades no painel direito.
* Lista de Janelas de Atividades no painel inferior.

No pop-up Janelas de Atividades e no Gerenciador de Janelas de Atividades, você pode rolar até a semana anterior e a próxima semana usando as setas para a esquerda e para a direita.

![Setas para a direita/esquerda do Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Na parte inferior da exibição de Diagrama, você deve ver estes botões: Aplicar Zoom, Reduzir, Ajustar Nível de Zoom, Zoom 100% e Bloquear Layout. O botão **Bloquear layout** impede que você mova acidentalmente tabelas e pipelines na Exibição de Diagrama. Ele está ligado por padrão. Você pode desativá-lo e mover entidades pelo diagrama. Ao desativá-lo, você poderá usar o último botão para posicionar as tabelas e os pipelines automaticamente. Você também pode ampliar ou reduzir o zoom usando a roda do mouse.

![Comandos de zoom da Exibição de Diagrama](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Lista Janelas de Atividades
A lista Janelas de Atividades na parte inferior do painel central exibe todas as janelas de atividades do conjunto de dados que você selecionou no Gerenciador de Recursos ou na Exibição de Diagrama. Por padrão, a lista está na ordem decrescente, o que significa que você verá a janela de atividades mais recente na parte de cima.

![Lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Essa lista não é atualizada automaticamente, portanto, use o botão de atualização na barra de ferramentas para atualizá-la manualmente.  

As janelas de atividades podem estar em um dos seguintes status:

<table>
<tr>
    <th align="left">Status</th><th align="left">Substatus</th><th align="left">DESCRIÇÃO</th>
</tr>
<tr>
    <td rowspan="8">Aguardando</td><td>ScheduleTime</td><td>Não chegou a hora de a janela de atividades ser executada.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências de upstream não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas executando outras janelas de atividades.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade estiver em pausa e não poderá executar as janelas de atividades até ser retomada.</td>
</tr>
<tr>
<td>Retry</td><td>A execução da atividade está sendo repetida.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não foi iniciada.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>A validação está aguardando para ser repetida.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Validando</td><td>Validação em andamento.</td>
</tr>
<td>-</td>
<td>A janela de atividades está sendo processada.</td>
</tr>
<tr>
<td rowspan="4">Com falha</td><td>TimedOut</td><td>A execução demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>A janela de atividade foi cancelada por ação do usuário.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td>-</td><td>Falha ao gerar e/ou validar a janela de atividades.</td>
</tr>
<td>Ready</td><td>-</td><td>A janela de atividades está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td>-</td><td>A janela de atividades não foi processada.</td>
</tr>
<tr>
<td>Nenhum</td><td>-</td><td>Uma janela de atividades que costumava existir com um status diferente, mas que foi redefinida.</td>
</tr>
</table>


Ao clicar em uma janela de atividades na lista, você verá detalhes sobre ela na janela **Gerenciador de Janelas de Atividades** ou nas **Propriedades** à direita.

![Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Atualizar janelas de atividades
Os detalhes não são atualizados automaticamente e, portanto, você precisa usar o botão de atualização (segundo botão) na barra de comandos para atualizar manualmente a lista de janelas de atividades.  

### <a name="properties-window"></a>Janela Propriedades
A janela Propriedades está no painel à extrema direita do Aplicativo de Monitoramento e Gerenciamento.

![Janela Propriedades](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Ela exibe as propriedades do item selecionado no Gerenciador de Recursos (modo de exibição de árvore), na Exibição de Diagrama ou na lista de Janelas de Atividades.

### <a name="activity-window-explorer"></a>Gerenciador de Janelas de Atividades
A janela **Gerenciador de Janelas de Atividades** está no painel mais à direita do aplicativo de Monitoramento e Gerenciamento. Ela exibe detalhes sobre a janela de atividades selecionada no pop-up Janelas de Atividades ou na lista Janelas de Atividades.

![Gerenciador de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Você pode mudar para outra janela de atividades clicando nela na exibição de calendário na parte superior. Também é possível usar os botões de seta para a esquerda/seta para a direita, na parte superior, para ver as janelas de atividades da semana anterior/próxima semana.

Você pode usar os botões da barra de ferramentas no painel inferior para executar novamente a janela de atividades ou para atualizar os detalhes no painel.

### <a name="script"></a>Script
Você pode usar a guia **Script** para exibir a definição de JSON da entidade selecionada do Data Factory (serviço vinculado, conjunto de dados ou pipeline).

![Guia Script](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Exibições do sistema
O aplicativo Monitoramento e Gerenciamento inclui exibições do sistema predefinidas (**Janelas de atividades recentes**, **Janelas de atividades com falha**, **Janelas de atividades em andamento**) que permitem exibir as janelas de atividades recentes/com falha/em andamento do seu data factory.

Mude para a guia **Exibições de Monitoramento** à esquerda ao clicar nela.

![Guia Exibições de Monitoramento](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Atualmente, há três exibições do sistema com suporte. Escolha uma opção para ver as janelas de atividades recentes, as janelas de atividades com falha ou as janelas de atividades em andamento na lista Janelas de Atividades (na parte inferior do painel central).

Ao escolher a opção **Janelas de atividades recentes**, você verá todas as janelas de atividades recentes na ordem decrescente da **hora da última tentativa**.

Você pode usar a exibição **Janelas de atividades com falha** para ver todas as janelas de atividade com falha na lista. Escolha uma janela de atividades com falha na lista para ver detalhes sobre ela na janela **Propriedades** ou no **Gerenciador de Janelas de Atividades**. Você também pode baixar todos os logs de uma janela de atividades com falha.

## <a name="sort-and-filter-activity-windows"></a>Classificar e filtrar janelas de atividades
Altere as configurações da **hora de início** e **hora de término** na barra de comandos para filtrar as janelas de atividades. Depois de alterar a hora de início e a hora de término, clique no botão ao lado da hora de término para atualizar a lista Janelas de Atividades.

![Horas de início e término](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Atualmente, todas as horas estão no formato UTC no aplicativo de Monitoramento e Gerenciamento.
>
>

Na **lista de Janelas de Atividades**, clique no nome de uma coluna (por exemplo: Status).

![Menu da coluna da lista de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Você pode fazer o seguinte:

* Classificar em ordem crescente.
* Classificar em ordem decrescente.
* Filtrar por um ou mais valores (Pronto, Aguardando etc.).

Ao especificar um filtro em uma coluna, você verá o botão de filtro habilitado para essa coluna, indicando que os valores na coluna são valores filtrados.

![Filtre uma coluna da lista de Janelas de Atividades](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Você pode usar a mesma janela pop-up para limpar filtros. Para limpar todos os filtros da lista de Janelas de Atividades, clique no botão Limpar filtro na barra de comandos.

![Limpar todos os filtros na lista Janelas de Atividades](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Execute ações em lote
### <a name="rerun-selected-activity-windows"></a>Executar novamente as janelas de atividades selecionadas
Escolha uma janela de atividades, clique na seta para baixo do primeiro botão da barra de comandos e selecione **Executar novamente** / **Executar novamente com upstream no pipeline**. Quando você escolhe a opção **Executar novamente com upstream no pipeline**, todas as janelas de atividades upstream também são executadas novamente.
    ![Executar novamente uma janela de atividades](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Também é possível escolher várias janelas de atividades na lista e executá-las novamente ao mesmo tempo. Você pode querer filtrar janelas de atividades com base no status (por exemplo: **Com falha**) e executar novamente as janelas de atividades com falha após corrigir o problema que faz com que as janelas de atividades falhem. Confira a seção a seguir para obter os detalhes sobre como filtrar janelas de atividades na lista.  

### <a name="pauseresume-multiple-pipelines"></a>Pausar/retomar vários pipelines
Você pode fazer uma seleção múltipla de dois ou mais pipelines usando a tecla Ctrl. Você pode usar os botões de barra de comandos (que são realçados no retângulo vermelho na imagem a seguir) para pausar/retomá-los.

![Pausar/Retomar na barra de comandos](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
