---
title: Criar tarefas de automação para gerenciar e monitorar recursos do Azure
description: Configure tarefas automatizadas que ajudam a gerenciar recursos do Azure e monitorar custos criando fluxos de trabalho que são executados em aplicativos lógicos do Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: conceptual
ms.date: 02/19/2021
ms.openlocfilehash: 8180fe8554e5fff83e4caef8c245839518649ca1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101719042"
---
# <a name="manage-azure-resources-and-monitor-costs-by-creating-automation-tasks-preview"></a>Gerenciar recursos do Azure e monitorar custos Criando tarefas de automação (versão prévia)

> [!IMPORTANT]
> Essa versão preliminar está na versão prévia pública, e é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho em produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Para ajudá-lo a gerenciar os [recursos do Azure](../azure-resource-manager/management/overview.md#terminology) com mais facilidade, você pode criar tarefas de gerenciamento automatizadas para um recurso ou grupo de recursos específico usando modelos de tarefa de automação, que variam em disponibilidade com base no tipo de recurso. Por exemplo, para uma [conta de armazenamento do Azure](../storage/common/storage-account-overview.md), você pode configurar uma tarefa de automação que envia o custo mensal para essa conta de armazenamento. Para uma [máquina virtual do Azure](https://azure.microsoft.com/services/virtual-machines/), você pode criar uma tarefa de automação que ativa ou desativa essa máquina virtual em um agendamento predefinido.

Nos bastidores, uma tarefa de automação é, na verdade, um fluxo de trabalho executado no serviço de [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) e é cobrado usando as mesmas [taxas de preços](https://azure.microsoft.com/pricing/details/logic-apps/) e o mesmo [modelo de preços](../logic-apps/logic-apps-pricing.md). Depois de criar a tarefa, você pode exibir e editar o fluxo de trabalho subjacente abrindo a tarefa no designer do aplicativo lógico. Depois que uma tarefa terminar pelo menos uma execução, você poderá examinar o status, o histórico, as entradas e as saídas de cada execução.

Aqui estão os modelos de tarefa disponíveis atualmente nesta visualização:

| Tipo de recurso | Modelos de tarefa de automação |
|---------------|---------------------------|
| Grupos de recursos do Azure | **Quando o recurso é excluído** |
| Todos os recursos do Azure | **Enviar custo mensal para o recurso** |
| Máquinas virtuais do Azure | Além disso: <p>- **Desligar a máquina virtual** <br>- **Iniciar máquina virtual** |
| Contas de armazenamento do Azure | Além disso: <p>- **Excluir BLOBs antigos** |
| Azure Cosmos DB | Além disso: <p>- **Enviar resultado da consulta por email** |
|||

Este artigo mostra como concluir as seguintes tarefas:

* [Crie uma tarefa de automação](#create-automation-task) para um recurso específico do Azure.

* [Examine o histórico de uma tarefa](#review-task-history), que inclui o status de execução, entradas, saídas e outras informações históricas.

* [Edite a tarefa](#edit-task) para que você possa atualizar a tarefa ou personalizar o fluxo de trabalho subjacente da tarefa no designer do aplicativo lógico.

<a name="differences"></a>

## <a name="how-do-automation-tasks-differ-from-azure-automation"></a>Como as tarefas de automação diferem da automação do Azure?

No momento, você pode criar uma tarefa de automação somente no nível de recurso, exibir o histórico de execuções da tarefa e editar o fluxo de trabalho do aplicativo lógico subjacente da tarefa, que é alimentado pelo serviço de [aplicativos lógicos do Azure](../logic-apps/logic-apps-overview.md) . As tarefas de automação são mais básicas e leves do que a [automação do Azure](../automation/automation-intro.md).

Por comparação, a automação do Azure é um serviço de configuração e automação baseado em nuvem que dá suporte ao gerenciamento consistente em seus ambientes Azure e não Azure. O serviço consiste [em automação de processo para orquestrar processos](../automation/automation-intro.md#process-automation) usando [runbooks](../automation/automation-runbook-execution.md), gerenciamento de configuração com [controle de alterações e inventário](../automation/change-tracking/overview.md), gerenciamento de atualizações, recursos compartilhados e recursos heterogêneos. A Automação oferece controle completo durante a implantação, operações e o encerramento de cargas de trabalho e de recursos.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* O recurso do Azure que você deseja gerenciar. Este artigo usa uma conta de armazenamento do Azure como exemplo.

* Uma conta do Office 365 se você quiser acompanhar o exemplo, que envia emails usando o Office 365 Outlook.

<a name="create-automation-task"></a>

## <a name="create-an-automation-task"></a>Criar uma tarefa de automação

1. No [portal do Azure](https://portal.azure.com), localize o recurso que você deseja gerenciar.

1. No menu de recursos, role até a seção **automação** e selecione **tarefas**

   ![Captura de tela que mostra o portal do Azure e um menu de recursos da conta de armazenamento em que a seção "automação" tem o item de menu "tarefas" selecionado.](./media/create-automation-tasks-azure-resources/storage-account-menu-automation-section.png)

1. No painel **tarefas** , selecione **Adicionar** para que você possa selecionar um modelo de tarefa.

   ![Captura de tela que mostra o painel "tarefas" da conta de armazenamento em que a barra de ferramentas tem "Adicionar" selecionado](./media/create-automation-tasks-azure-resources/add-automation-task.png)

1. No painel **Adicionar uma tarefa** , em **selecionar um modelo**, selecione o modelo para a tarefa que você deseja criar. Se a próxima página não for exibida, selecione **Avançar: autenticação**.

   Este exemplo continua selecionando o modelo de tarefa **Enviar custo mensal para o recurso** .

   ![Captura de tela que mostra as seleções, "enviar custo mensal para o recurso" e "Avançar: autenticação"](./media/create-automation-tasks-azure-resources/select-task-template.png)

1. Em **autenticação**, na seção **conexões** , selecione **criar** para cada conexão que aparece na tarefa para que você possa fornecer credenciais de autenticação para todas as conexões. Os tipos de conexões em cada tarefa variam de acordo com a tarefa.

   Este exemplo mostra apenas uma das conexões exigidas por essa tarefa.

   ![Captura de tela que mostra a opção "criar" selecionada para a conexão Azure Resource Manager](./media/create-automation-tasks-azure-resources/create-authenticate-connections.png)

1. Quando for solicitado, entre com suas credenciais de conta do Azure.

   ![Captura de tela que mostra a seleção, "entrar"](./media/create-automation-tasks-azure-resources/create-connection-sign-in.png)

   Cada conexão autenticada com êxito é semelhante a este exemplo:

   ![Captura de tela que mostra a conexão criada com êxito](./media/create-automation-tasks-azure-resources/create-connection-success.png)

1. Depois de autenticar todas as conexões, selecione **Avançar: configuração** se a próxima página não for exibida.

1. Em **configuração**, forneça um nome para a tarefa e qualquer outra informação necessária para a tarefa. Quando terminar, selecione **Criar**.

   > [!NOTE]
   > Você não pode alterar o nome da tarefa após a criação, portanto, considere um nome que ainda se aplica se você [Editar o fluxo de trabalho subjacente](#edit-task-workflow). As alterações feitas no fluxo de trabalho subjacente se aplicam somente à tarefa que você criou, não ao modelo de tarefa.
   >
   > Por exemplo, se você nomear sua tarefa `Send monthly cost` , mas posteriormente editar o fluxo de trabalho subjacente para ser executado semanalmente, não será possível alterar o nome da tarefa para `Send weekly cost` .

   As tarefas que enviam notificações por email exigem um endereço de email.

   ![Captura de tela que mostra as informações necessárias para a tarefa selecionada](./media/create-automation-tasks-azure-resources/provide-task-information.png)

   A tarefa que você criou, que é automaticamente ativa e em execução, agora aparece na lista **tarefas de automação** .

   ![Captura de tela que mostra a lista de tarefas de automação](./media/create-automation-tasks-azure-resources/automation-tasks-list.png)

   > [!TIP]
   > Se a tarefa não aparecer imediatamente, tente atualizar a lista de tarefas ou aguarde um pouco antes de atualizar. Na barra de ferramentas, selecione **Atualizar**.

   Depois que a tarefa selecionada for executada, você receberá um email semelhante a este exemplo:

   ![Captura de tela que mostra a notificação por email enviada por tarefa](./media/create-automation-tasks-azure-resources/email-notification-received.png)

<a name="review-task-history"></a>

## <a name="review-task-history"></a>Examinar o histórico de tarefas

Para exibir o histórico de uma tarefa de execuções junto com seus status, entradas, saídas e outras informações, siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), localize o recurso que tem o histórico de tarefas que você deseja examinar.

1. No menu do recurso, em **configurações**, selecione **tarefas de automação**.

1. Na lista tarefas, localize a tarefa que você deseja examinar. Na coluna **execuções** da tarefa, selecione **Exibir**.

   ![Captura de tela que mostra uma tarefa e a opção de "exibição" selecionada](./media/create-automation-tasks-azure-resources/view-runs-for-task.png)

   O painel **histórico de execuções** mostra todas as execuções para a tarefa junto com seus status, horários de início, identificadores e durações de execução.

   ![Captura de tela que mostra as execuções de uma tarefa, seus status e outras informações](./media/create-automation-tasks-azure-resources/view-runs-history.png)

   Aqui, os status possíveis para uma execução:

   | Status | Descrição |
   |--------|-------------|
   | **Cancelar** | A tarefa foi cancelada durante a execução. |
   | **Falha** | A tarefa tem pelo menos uma ação com falha, mas não existiam ações subsequentes para lidar com a falha. |
   | **Executando** | A tarefa está em execução no momento. |
   | **Êxito** | Todas as ações foram bem sucedidas. Uma tarefa ainda pode ser concluída com êxito se uma ação falhou, mas uma ação subsequente existia para lidar com a falha. |
   | **Aguardando** | A execução ainda não foi iniciada e está em pausa porque uma instância anterior da tarefa ainda está em execução. |
   |||

   Para obter mais informações, consulte [revisar histórico de execuções](../logic-apps/monitor-logic-apps.md#review-runs-history)

1. Para exibir os status e outras informações para cada etapa em uma execução, selecione essa execução.

   O painel **execução do aplicativo lógico** é aberto e mostra o fluxo de trabalho subjacente que foi executado.

   * Um fluxo de trabalho sempre começa com um [*gatilho*](../connectors/apis-list.md#triggers-actions). Para essa tarefa, o fluxo de trabalho começa com o gatilho de [ **recorrência**](../connectors/connectors-native-recurrence.md).

   * Cada etapa mostra o status e a duração da execução. As etapas que têm duração de 0 segundo levaram menos de 1 segundo para serem executadas.

   ![Captura de tela que mostra cada etapa na duração da execução, status e execução](./media/create-automation-tasks-azure-resources/runs-history-details.png)

1. Para examinar as entradas e saídas de cada etapa, selecione a etapa, que se expande.

   Este exemplo mostra as entradas para o gatilho de recorrência, que não tem saídas porque o gatilho especifica apenas quando o fluxo de trabalho é executado e não fornece nenhuma saída para as ações subsequentes a serem processadas.

   ![Captura de tela que mostra o gatilho expandido e as entradas](./media/create-automation-tasks-azure-resources/view-trigger-inputs.png)

   Por outro lado, a ação **enviar um email** tem entradas de ações anteriores no fluxo de trabalho e saídas.

   ![Captura de tela que mostra uma ação, entradas e saídas expandidas](./media/create-automation-tasks-azure-resources/view-action-inputs-outputs.png)

Para saber como você pode criar seus próprios fluxos de trabalho automatizados para que possa integrar aplicativos, dados, serviços e sistemas além do contexto de tarefas de automação para recursos do Azure, consulte [início rápido: criar seu primeiro fluxo de trabalho de integração usando aplicativos lógicos do Azure-portal do Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="edit-task"></a>

## <a name="edit-the-task"></a>Editar a tarefa

Para alterar uma tarefa, você tem estas opções:

* [Edite a tarefa "embutida"](#edit-task-inline) para que você possa alterar as propriedades da tarefa, como informações de conexão ou informações de configuração, por exemplo, seu endereço de email.

* [Edite o fluxo de trabalho subjacente da tarefa](#edit-task-workflow) no designer do aplicativo lógico.

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>Editar a tarefa embutida

1. No [portal do Azure](https://portal.azure.com), localize o recurso que tem a tarefa que você deseja atualizar.

1. No menu do recurso, em **automação**, selecione **tarefas**.

1. Na lista tarefas, localize a tarefa que você deseja atualizar. Abra o menu de reticências da tarefa (**...**) e selecione **Editar em linha**.

   ![Captura de tela que mostra o menu de reticências abertas e a opção selecionada, "Editar na linha"](./media/create-automation-tasks-azure-resources/view-task-inline.png)

   Por padrão, a guia **autenticação** é exibida e mostra as conexões existentes.

1. Para adicionar novas credenciais de autenticação ou selecionar credenciais de autenticação existentes diferentes para uma conexão, abra o menu de reticências (**...**) da conexão e selecione **Adicionar nova conexão** ou, se disponível, credenciais de autenticação diferentes.

   ![Captura de tela que mostra a guia Autenticação, conexões existentes e o menu de reticências selecionadas](./media/create-automation-tasks-azure-resources/edit-connections.png)

1. Para atualizar outras propriedades de tarefa, selecione **Avançar: configuração**.

   Para a tarefa neste exemplo, a única propriedade disponível para edição é o endereço de email.

   ![Captura de tela que mostra a guia Configuração](./media/create-automation-tasks-azure-resources/edit-task-configuration.png)

1. Quando terminar, selecione **Salvar**.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>Editar o fluxo de trabalho subjacente da tarefa

Quando você altera o fluxo de trabalho subjacente de uma tarefa de automação, suas alterações afetam apenas a instância de tarefa que você criou e não o modelo que cria a tarefa. Depois de fazer e salvar suas alterações, o nome que você forneceu para a tarefa original pode não descrever mais precisamente a tarefa, portanto, talvez seja necessário recriar a tarefa com um nome diferente.

> [!TIP]
> Como prática recomendada, clone o fluxo de trabalho subjacente para que você possa editar a versão copiada em vez disso. Dessa forma, você pode fazer e testar suas alterações na cópia, enquanto a tarefa de automação original continua funcionando e executada sem arriscar a interrupção ou interromper a funcionalidade existente. Depois que você concluir as alterações e estiver satisfeito com a execução bem-sucedida da nova versão, será possível desabilitar ou excluir a tarefa de automação original e usar a versão clonada para sua tarefa de automação. As etapas a seguir incluem informações sobre como clonar seu fluxo de trabalho.

1. No [portal do Azure](https://portal.azure.com), localize o recurso que tem a tarefa que você deseja atualizar.

1. No menu do recurso, em **automação**, selecione **tarefas**.

1. Na lista tarefas, localize a tarefa que você deseja atualizar. Abra o menu de reticências da tarefa (**...**) e selecione **abrir em aplicativos lógicos**.

   ![Captura de tela que mostra o menu de reticências abertas e a opção selecionada, "abrir em aplicativos lógicos"](./media/create-automation-tasks-azure-resources/edit-task-logic-app-designer.png)

   O fluxo de trabalho subjacente da tarefa é aberto no serviço de aplicativos lógicos do Azure e mostra o painel **visão geral** em que você pode exibir o mesmo histórico de execuções que está disponível para a tarefa.

   ![Captura de tela que mostra a tarefa no modo de exibição de aplicativos lógicos do Azure com o painel Visão geral selecionado](./media/create-automation-tasks-azure-resources/task-logic-apps-view.png)

1. Para abrir o fluxo de trabalho subjacente no designer do aplicativo lógico, no menu do aplicativo lógico, selecione **Designer de aplicativo lógico**.

   ![Captura de tela que mostra a opção de menu "designer de aplicativo lógico" selecionada e superfície de designer com o fluxo de trabalho subjacente](./media/create-automation-tasks-azure-resources/view-task-workflow-logic-app-designer.png)

   Agora você pode editar as propriedades do gatilho e das ações do fluxo de trabalho, bem como editar o gatilho e as ações que definem o próprio fluxo de trabalho. No entanto, como prática recomendada, siga as etapas para clonar seu fluxo de trabalho para que você possa fazer suas alterações em uma cópia enquanto o fluxo de trabalho original continua a funcionar e executar.

1. Para clonar o fluxo de trabalho e editar a versão copiada, siga estas etapas:

   1. No menu fluxo de trabalho do aplicativo lógico, selecione **visão geral**.

   1. Na barra de ferramentas do painel Visão geral, selecione **clonar**.

   1. No painel criação de aplicativo lógico, em **nome**, insira um novo nome para o fluxo de trabalho do aplicativo lógico copiado.

      Exceto para o **status do aplicativo lógico**, as outras propriedades não estão disponíveis para edição. 
      
   1. Em **status do aplicativo lógico**, selecione **desabilitado** para que o fluxo de trabalho clonado não seja executado enquanto você fizer as alterações. Você pode habilitar o fluxo de trabalho quando estiver pronto para testar suas alterações.

   1. Depois que o Azure concluir o provisionamento do fluxo de trabalho clonado, localize e abra esse fluxo de trabalho no designer do aplicativo lógico.

1. Para exibir as propriedades do gatilho ou de uma ação, expanda esse gatilho ou ação.

   Por exemplo, você pode alterar o gatilho de recorrência para ser executado semanalmente, em vez de mensal.

   ![Captura de tela que mostra o gatilho de recorrência expandido com a lista de frequência aberta para mostrar as opções de frequência disponíveis](./media/create-automation-tasks-azure-resources/edit-recurrence-trigger.png)

   Para obter mais informações sobre o gatilho de recorrência, consulte [criar, agendar e executar tarefas e fluxos de trabalho recorrentes com o gatilho de recorrência](../connectors/connectors-native-recurrence.md). Para obter mais informações sobre outros gatilhos e ações que você pode usar, consulte [conectores para aplicativos lógicos do Azure](../connectors/apis-list.md).

1. Para salvar suas alterações, na barra de ferramentas do designer, selecione **salvar**.

   ![Captura de tela que mostra a barra de ferramentas do designer e o comando "salvar" selecionado](./media/create-automation-tasks-azure-resources/save-updated-workflow.png)

1. Para testar e executar o fluxo de trabalho atualizado, na barra de ferramentas do designer, selecione **executar**.

   Após a conclusão da execução, o designer mostra os detalhes de execução do fluxo de trabalho.

   ![Captura de tela que mostra os detalhes de execução do fluxo de trabalho no designer](./media/create-automation-tasks-azure-resources/view-run-details-designer.png)

1. Para desabilitar o fluxo de trabalho para que a tarefa não continue em execução, consulte [gerenciar aplicativos lógicos no portal do Azure](../logic-apps/manage-logic-apps-with-azure-portal.md).

## <a name="provide-feedback"></a>Fornecer comentários

Gostaríamos de ouvi-lo! Para relatar bugs, fornecer comentários ou fazer perguntas sobre esse recurso de visualização, [entre em contato com a equipe de aplicativos lógicos do Azure](mailto:logicappspm@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar aplicativos lógicos no portal do Azure](../logic-apps/manage-logic-apps-with-azure-portal.md)
