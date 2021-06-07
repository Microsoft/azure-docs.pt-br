---
title: Monitoramento das alterações de máquinas virtuais – Grade de Eventos do Azure e Aplicativos Lógicos
description: Verificar as alterações de configuração em VMs (máquinas virtuais) usando a Grade de Eventos do Azure e os Aplicativos Lógicos
services: logic-apps, event-grid
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: estfan, LADocs
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 1a5d8c36382433024efd1f1cc6ba9fd878d28ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92329518"
---
# <a name="tutorial-monitor-virtual-machine-changes-by-using-azure-event-grid-and-logic-apps"></a>Tutorial: Monitorar as alterações de máquinas virtuais usando a Grade de Eventos do Azure e os Aplicativos Lógicos

Para monitorar e responder a eventos específicos que ocorrem em recursos do Azure ou em recursos de terceiros, você pode automatizar e executar tarefas como um fluxo de trabalho criando um [aplicativo lógico](../logic-apps/logic-apps-overview.md) que usa o código mínimo. Esses recursos podem publicar eventos em uma [Grade de Eventos do Azure](../event-grid/overview.md). Por sua vez, a grade de eventos envia os eventos aos assinantes com filas, webhooks, ou [hubs de eventos](../event-hubs/event-hubs-about.md) como pontos de extremidade. Como um assinante, o seu aplicativo lógico pode aguardar os eventos da grade de eventos antes de executar fluxos de trabalho automatizados para realizar tarefas.

Por exemplo, aqui estão alguns eventos que editores podem enviar aos assinantes por meio do serviço de Grade de Eventos do Azure:

* Criar, ler, atualizar ou excluir um recurso. Por exemplo, você pode monitorar as alterações que podem incorrer em encargos na sua assinatura do Azure e afetar sua fatura.

* Adicionar ou remover uma pessoa de uma assinatura do Azure.

* Executar uma ação específica no seu aplicativo.

* Exibir uma nova mensagem em uma fila.

Este tutorial cria um aplicativo lógico que monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. Quando você cria um aplicativo lógico com uma assinatura de evento para um recurso do Azure, os eventos são enviados desse recurso por meio de uma grade de eventos para o aplicativo lógico. O tutorial percorre a compilação desse aplicativo lógico:

![Captura de tela do Designer de Aplicativos Lógicos, mostrando o fluxo de trabalho para monitorar a VM com a Grade de Eventos.](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo lógico que monitora os eventos de uma grade de eventos.
> * Adicionar uma condição que verifica especificamente se há alterações na máquina virtual.
> * Enviar email quando sua máquina virtual é alterada.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma conta de email de qualquer provedor de email compatível com Aplicativos Lógicos para envio de notificações, como o Office 365 Outlook, o Outlook.com ou o Gmail. Para outros provedores, [revise a lista de conectores aqui](/connectors/).

  Este tutorial usa uma conta do Office 365 Outlook. Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário poderá parecer um pouco diferente.

  > [!IMPORTANT]
  > Se você quiser usar o conector do Gmail, somente as contas comerciais do G Suite poderão usar esse conector sem restrição nos aplicativos lógicos. Se você tiver uma conta de consumidor do Gmail, poderá usar esse conector somente com serviços específicos do Google aprovados ou poderá [criar um aplicativo cliente do Google para usar para autenticação com o conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Para obter mais informações, confira [Políticas de privacidade e segurança de dados para os conectores do Google nos Aplicativos Lógicos do Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Uma [máquina virtual](https://azure.microsoft.com/services/virtual-machines) sozinha em seu próprio grupo de recursos do Azure. Se você ainda não fez isso, crie uma máquina virtual por meio do [tutorial sobre como criar uma VM](../virtual-machines/windows/quick-create-portal.md). Para fazer com que a máquina virtual publique eventos, [não é necessário fazer mais nada](../event-grid/overview.md).

## <a name="create-blank-logic-app"></a>Criar um aplicativo lógico em branco

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure.

1. No menu principal do Azure, selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

   ![Captura de tela do portal do Azure, mostrando o botão para criar um recurso de aplicativo lógico.](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

1. Em **Aplicativo Lógico**, forneça informações sobre seu recurso de aplicativo lógico. Quando terminar, selecione **Criar**.

   ![Captura de tela do menu de criação de aplicativos lógicos, mostrando detalhes como nome, assinatura, grupo de recursos e localização.](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | <*logic-app-name*> | Forneça um nome exclusivo para seu aplicativo lógico. |
   | **Assinatura** | Sim | <*Azure-subscription-name*> | Selecione a mesma assinatura do Azure para todos os serviços neste tutorial. |
   | **Grupo de recursos** | Sim | <*Azure-resource-group*> | O nome do grupo de recursos do Azure para seu aplicativo lógico, que você pode selecionar para todos os serviços neste tutorial. |
   | **Localidade** | Sim | <*Azure-region*> | Selecione a mesma região para todos os serviços neste tutorial. |
   |||

1. Depois que o Azure implanta seu aplicativo lógico, o Designer de Aplicativos Lógicos mostra uma página com um vídeo de introdução os gatilhos normalmente usados. Role pelo vídeo e pelos gatilhos.

1. Em **Modelos**, selecione **Aplicativo lógico em branco**.

   ![Captura de tela dos modelos de Aplicativos Lógicos, mostrando a seleção para criar um aplicativo lógico em branco.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   O Designer de Aplicativos Lógicos agora mostra os [*gatilhos*](../logic-apps/logic-apps-overview.md#logic-app-concepts) que você pode usar para iniciar seu aplicativo lógico. Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Sempre que o gatilho é acionado, os Aplicativos Lógicos do Azure criam uma instância de fluxo de trabalho que executa seu aplicativo lógico.

## <a name="add-an-event-grid-trigger"></a>Adicionar um gatilho de Grade de Eventos

Agora, adicione o gatilho de Grade de Eventos que você usa para monitorar o grupo de recursos para sua máquina virtual.

1. No designer, na caixa de pesquisa, insira `event grid` como o filtro. Na lista de gatilhos, selecione o gatilho **Quando um evento de recurso ocorre**.

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando a seleção do gatilho da Grade de Eventos em um evento de recurso.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

1. Quando receber a solicitação, entre na Grade de Eventos do Azure com suas credenciais da conta do Azure. Na lista **Locatário**, que mostra o locatário do Azure Active Directory associado à sua assinatura do Azure, verifique se o locatário correto é exibido, por exemplo:

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o prompt de entrada do Azure para se conectar à Grade de Eventos.](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se você estiver conectado com uma conta pessoal da Microsoft, como @outlook.com ou @hotmail.com, talvez o gatilho da Grade de Eventos não apareça corretamente. Como alternativa, selecione [Conectar-se à Entidade de Serviço](../active-directory/develop/howto-create-service-principal-portal.md) ou autentique como membro do Azure Active Directory associado à sua assinatura do Azure, por exemplo, *nome de usuário*@emailoutlook.onmicrosoft.com.

1. Agora, assine seu aplicativo lógico nos eventos do editor. Forneça os detalhes da assinatura de evento conforme especificado na tabela a seguir, por exemplo:

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o editor de detalhes para o gatilho para quando ocorre um evento de recurso.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Assinatura** | Sim | <*event-publisher-Azure-subscription-name*> | Selecione o nome da assinatura do Azure associada ao *editor de eventos*. Para este tutorial, selecione o nome da assinatura do Azure de sua máquina virtual. |
   | **Tipo de recurso** | Sim | <*event-publisher-Azure-resource-type*> | Selecione o tipo de recurso do Azure para o editor de eventos. Para obter mais informações sobre os tipos de recursos do Azure, confira [Provedores e tipos de recursos do Azure](../azure-resource-manager/management/resource-providers-and-types.md). Para este tutorial, selecione o valor `Microsoft.Resources.ResourceGroups` para monitorar grupos de recursos do Azure. |
   | **Nome do recurso** |  Sim | <*event-publisher-Azure-resource-name*> | Selecione o nome do recurso do Azure para o editor de eventos. Essa lista varia de acordo com o tipo de recurso que você selecionou. Para este tutorial, selecione o nome do grupo de recursos do Azure que inclui sua máquina virtual. |
   | **Item do Tipo de Evento** |  Não | <*event-types*> | Selecione um ou mais tipos de eventos específicos para filtrar e enviar para a grade de eventos. Por exemplo, você pode, opcionalmente, adicionar estes tipos de evento para detectar quando os recursos são alterados ou excluídos: <p><p>- `Microsoft.Resources.ResourceActionSuccess` <br>- `Microsoft.Resources.ResourceDeleteSuccess` <br>- `Microsoft.Resources.ResourceWriteSuccess` <p>Para saber mais, consulte esses tópicos: <p><p>- [Esquema de eventos para assinatura da Grade de Eventos do Azure](../event-grid/event-schema-resource-groups.md) <br>- [Compreender a filtragem de eventos](../event-grid/event-filtering.md) <br>- [Filtrar eventos para a Grade de Eventos](../event-grid/how-to-filter-events.md) |
   | Para adicionar propriedades opcionais, selecione **Adicionar novo parâmetro** e, em seguida, selecione as propriedades desejadas. | Não | {consulte as descrições} | * **Filtro de prefixo**: Para este tutorial, deixe esta propriedade vazia. O comportamento padrão corresponde a todos os valores. No entanto, você pode especificar uma cadeia de caracteres de prefixo como filtro, por exemplo, um caminho e um parâmetro para um recurso específico. <p>* **Filtro de sufixo**: Para este tutorial, deixe esta propriedade vazia. O comportamento padrão corresponde a todos os valores. No entanto, você pode especificar uma cadeia de caracteres de sufixo como filtro, por exemplo, uma extensão de nome de arquivo, quando quiser tipos específicos de arquivo. <p>* **Nome da assinatura**: Para este tutorial, é possível fornecer um nome exclusivo para a assinatura de evento. |
   |||

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer. Para recolher e ocultar os detalhes de uma ação no seu aplicativo lógico, selecione a barra de título da ação.

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o botão Salvar para salvar as edições do fluxo de trabalho.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Ao salvar seu aplicativo lógico com um gatilho de grade de eventos, o Azure cria automaticamente uma assinatura de evento para seu aplicativo lógico para o recurso selecionado. Portanto, quando o recurso publica um evento para a grade de eventos, essa grade de eventos envia o evento automaticamente para o seu aplicativo lógico. Este evento dispara seu aplicativo lógico, depois cria e executa uma instância do fluxo de trabalho que será definida nas próximas etapas.

Seu aplicativo lógico já está ativo e escuta eventos da grade de eventos, mas não fará nada até que você adicione ações ao fluxo de trabalho.

## <a name="add-a-condition"></a>Adicione uma condição

Se você quiser que o aplicativo lógico execute somente quando uma operação ou evento específico ocorrer, adicione uma condição que verifica em busca da operação `Microsoft.Compute/virtualMachines/write`. Quando essa condição é verdadeira, o aplicativo lógico envia um email com detalhes sobre a máquina virtual atualizada.

1. No designer de aplicativo lógico, no gatilho de grade de eventos, selecione **Nova etapa**.

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o botão para adicionar uma nova etapa ao fluxo de trabalho.](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-new-step-condition.png)

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `condition` como o filtro. Na lista de ações, selecione a ação **Condição**.

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o botão para adicionar uma ação de condição.](./media/monitor-virtual-machine-changes-event-grid-logic-app/select-condition.png)

   O Designer de Aplicativo Lógico adiciona uma condição vazia ao seu fluxo de trabalho, incluindo caminhos de ação a seguir baseado no status da condição: verdadeira ou falsa.

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando uma condição vazia adicionada ao fluxo de trabalho.](./media/monitor-virtual-machine-changes-event-grid-logic-app/empty-condition.png)

1. Mude o nome o título da condição para `If a virtual machine in your resource group has changed`. Na barra de título da condição, selecione o botão de reticências ( **…** ) e selecione **Renomear**.

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o menu de contexto do editor de condição com a opção Renomear selecionada.](./media/monitor-virtual-machine-changes-event-grid-logic-app/rename-condition.png)

1. Criar uma condição que verifique o evento `body` para um objeto `data` no qual a propriedade `operationName` é igual à operação `Microsoft.Compute/virtualMachines/write`. Saiba mais sobre [Esquema de grade de eventos do evento](../event-grid/event-schema.md).

   1. Na primeira linha em **E**, clique na caixa à esquerda. Na lista de conteúdo dinâmico que é exibida, selecione **Expressão**.

      ![Captura de tela do Designer de Aplicativos Lógicos, mostrando a condição com o editor de expressão selecionado.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-choose-expression.png)

   1. No editor de expressão, insira esta expressão, que retorna o nome da operação do gatilho e seleciona **OK**:

      `triggerBody()?['data']['operationName']`

      Por exemplo:

      ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o editor de condição com a expressão para extrair o nome da operação.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-add-data-operation-name.png)

   1. Na caixa do meio, mantenha o operador **é igual a**.

   1. Na caixa à direita, insira esse valor, que é a operação específica que você deseja monitorar:

      `Microsoft.Compute/virtualMachines/write`

   Agora, sua condição concluída será semelhante a este exemplo:

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando uma condição que compara a operação.](./media/monitor-virtual-machine-changes-event-grid-logic-app/complete-condition.png)

   Se você alternar do modo de exibição de Design para a exibição de código e de volta para o modo de exibição de Design, a expressão que você especificou na condição será resolvida para o token **data.operationName**:

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando uma condição com tokens resolvidos.](./media/monitor-virtual-machine-changes-event-grid-logic-app/resolved-condition.png)

1. Salve seu aplicativo lógico.

## <a name="send-email-notifications"></a>Enviar notificações por email

Agora, adicione uma [*ação*](../logic-apps/logic-apps-overview.md#logic-app-concepts) para que você possa receber um email quando a condição for definida como verdadeira.

1. Na caixa **Se verdadeiro** da condição, selecione **Adicionar uma ação**.

   ![Captura de tela do editor de condição do Designer de Aplicativos Lógicos, mostrando o botão para adicionar uma ação quando a condição é verdadeira.](./media/monitor-virtual-machine-changes-event-grid-logic-app/condition-true-add-action.png)

1. Em **Escolher uma ação**, na caixa de pesquisa, insira `send an email` como o filtro. Com base no seu provedor de email, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar email" para o conector. Por exemplo:

   * Por exemplo, se você tiver uma conta corporativa ou de estudante do Azure, selecione o conector Office 365 Outlook.

   * Para contas pessoais da Microsoft, selecione o conector do Outlook.com.

   * Para as contas do Gmail, selecione o conector do Gmail.

   Este tutorial continua com o conector do Outlook do Office 365. Se você usar outro provedor, as etapas serão as mesmas, mas a interface do usuário poderá ter uma aparência levemente diferente.

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando a pesquisa para a ação Enviar um Email no conector do Outlook do Office 365.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

1. Se você ainda não tiver uma conexão para o seu provedor de email, quando solicitado, acesse o seu e-mail para autenticação.

1. Renomeie a ação de enviar email para este título: `Send email when virtual machine updated`

1. Forneça detalhes para o email conforme especificado na tabela a seguir:

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o conteúdo dinâmico sendo adicionado para a linha do assunto do email para uma condição verdadeira.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para selecionar uma saída das etapas anteriores no seu fluxo de trabalho, clique dentro de uma caixa de edição de modo que a lista de conteúdos dinâmicos apareça ou selecione **Adicionar conteúdo dinâmico**. Para mais resultados, selecione **Ver mais** para cada seção na lista. Para fechar a lista de conteúdos dinâmicos, selecione **Adicionar conteúdo dinâmico** novamente.

   | Propriedade | Obrigatório | Valor | Descrição |
   | -------- | -------- | ----- | ----------- |
   | **Para** | Sim | <*recipient\@domain*> | Insira o endereço de email do destinatário. Para fins de teste, você pode usar seu próprio endereço de email. |
   | **Assunto** | Sim | `Resource updated:` **Assunto** | Insira o conteúdo do assunto do email. Para este tutorial, insira o texto especificado e selecione o campo **Assunto** do evento. Aqui, o assunto do email inclui o nome do recurso atualizado (máquina virtual). |
   | **Corpo** | Sim | `Resource:` **Tópico** <p>`Event type:` **Tipo de Evento**<p>`Event ID:` **ID**<p>`Time:` **Hora do Evento** | Insira o conteúdo do corpo do email. Para este tutorial, insira o texto especificado e selecione os campos **Tópico**, **Tipo de Evento**, **ID** e **Hora do Evento** para que seu email inclua o recurso que disparou o evento, o tipo de evento, o carimbo de data/hora do evento e a ID do evento para a atualização. Para este tutorial, o recurso é o grupo de recursos do Azure selecionado no gatilho. <p>Para adicionar linhas em branco em seu conteúdo, pressione Shift + Enter. |
   ||||

   > [!NOTE]
   > Se você selecionar um campo que representa uma matriz, o designer adiciona automaticamente um loop **para cada** em torno da ação que faz referência à matriz. Dessa forma, seu aplicativo lógico executará essa ação em cada item da matriz.

   Agora, a ação do email pode parecer com este exemplo:

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando as saídas selecionadas para enviar no email quando a VM é atualizada.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Seu aplicativo lógico concluído pode parecer com este exemplo:

   ![Captura de tela do Designer de Aplicativos Lógicos, mostrando o aplicativo lógico criado com detalhes para gatilho e ações.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

1. Salve seu aplicativo lógico. Para recolher e ocultar os detalhes de cada ação no seu aplicativo lógico, selecione a barra de título da ação.

   Seu aplicativo lógico já está ativo, mas aguardará as alterações na sua máquina virtual antes de realizar qualquer ação. Para testar o seu aplicativo lógico agora, siga para a próxima seção.

## <a name="test-your-logic-app-workflow"></a>Testar o fluxo de trabalho de seu aplicativo lógico

1. Para verificar se o seu aplicativo lógico está obtendo os eventos especificados, atualize sua máquina virtual.

   Por exemplo, você pode redimensionar a máquina virtual no portal do Azure ou [redimensionar a VM com o Azure PowerShell](../virtual-machines/windows/resize-vm.md).

   Você receberá um email em alguns instantes. Por exemplo:

   ![Captura de tela do exemplo de email do Outlook, mostrando detalhes sobre a atualização da VM.](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

1. Para examinar o histórico de gatilho e execução do seu aplicativo lógico, selecione **Visão geral** no menu do aplicativo lógico. Para exibir mais detalhes sobre uma execução, selecione a linha para essa execução.

   ![Captura de tela da página de visão geral do aplicativo lógico, mostrando uma execução bem-sucedida selecionada.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

1. Para exibir as entradas e saídas de cada etapa, expanda a etapa que você deseja examinar. Essas informações podem ajudar você a diagnosticar e depurar problemas em seu aplicativo lógico.

   ![Captura de tela do histórico de execuções do aplicativo lógico, mostrando detalhes de cada execução.](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Parabéns, você acabou de criar e executar um aplicativo lógico capaz de monitorar eventos de recursos por uma grade de eventos e envio de emails para notificar o acontecimento de tais eventos. Você também aprendeu como é fácil criar fluxos de trabalho que automatizam processos e integram sistemas e serviços de nuvem.

Você pode monitorar outras alterações de configuração com grades de eventos e aplicativos lógicos, como por exemplo:

* Uma máquina virtual obtém direitos do Azure RBAC (controle de acesso baseado em função do Azure).
* As alterações são feitas em um grupo de segurança de rede (NSG) em um adaptador de rede (NIC).
* Discos para uma máquina virtual são adicionados ou removidos.
* Um endereço IP público é atribuído a uma máquina virtual NIC.

## <a name="clean-up-resources"></a>Limpar os recursos

Este tutorial usa recursos e executa ações que geram encargos na sua assinatura do Azure. Ao concluir o tutorial e testar, confira se desabilitou ou excluiu todos os recursos que você não deseja para evitar encargos.

* Para interromper a execução do seu aplicativo lógico sem excluir o seu trabalho, desabilite o aplicativo. No menu do aplicativo lógico, selecione **Visão geral**. Na barra de ferramentas, selecione **Desabilitar**.

  ![Captura de tela da visão geral do aplicativo lógico, mostrando o botão Desabilitar selecionado para desabilitar o aplicativo lógico.](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > Se o menu do aplicativo lógico não aparecer, tente retornar ao painel do Azure e abra novamente o aplicativo lógico.

* Para excluir seu aplicativo permanentemente, selecione **Visão geral** no menu do aplicativo lógico. Na barra de ferramentas, selecione **Excluir**. Confirme que você deseja excluir o aplicativo lógico e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

* [Criar e encaminhar eventos personalizados com a Grade de Eventos](../event-grid/custom-event-quickstart.md)
