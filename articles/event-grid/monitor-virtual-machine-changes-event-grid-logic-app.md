---
title: "Monitoramento das alterações de máquina virtual - Grade de Eventos do Azure e Aplicativos Lógicos | Microsoft Docs"
description: "Verifique as alterações de configuração em máquinas virtuais (VMs) usando a Grade de Eventos do Azure e os aplicativos lógicos"
keywords: "aplicativos lógicos, grades de eventos, máquina virtual, VM"
services: logic-apps
author: ecfan
manager: anneta
ms.assetid: 
ms.workload: logic-apps
ms.service: logic-apps
ms.topic: article
ms.date: 08/16/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 4d4c16860dbec10162797a13c8f9f57106abd17f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-virtual-machine-changes-with-azure-event-grid-and-logic-apps"></a>Como monitorar alterações de máquina virtual com a Grade de Eventos do Azure e os aplicativos lógicos

Você pode iniciar um [fluxo de trabalho do aplicativo lógico](../logic-apps/logic-apps-what-are-logic-apps.md) automatizado quando eventos específicos ocorrem em recursos do Azure ou de terceiros. Esses recursos podem publicar esses eventos em uma [Grade de Eventos do Azure](../event-grid/overview.md). Por sua vez, a grade de eventos envia os eventos aos assinantes com filas, webhooks, ou [hubs de eventos](../event-hubs/event-hubs-what-is-event-hubs.md) como pontos de extremidade. Como um assinante, o seu aplicativo lógico pode aguardar os eventos da grade de eventos antes de executar fluxos de trabalho automatizados para realizar tarefas - sem escrever qualquer código.

Por exemplo, aqui estão alguns eventos que editores podem enviar aos assinantes por meio do serviço de Grade de Eventos do Azure:

* Criar, ler, atualizar ou excluir um recurso. Por exemplo, você pode monitorar as alterações que podem incorrer em encargos na sua assinatura do Azure e afetar sua fatura. 
* Adicionar ou remover uma pessoa de uma assinatura do Azure.
* Executar uma ação específica no seu aplicativo.
* Exibir uma nova mensagem em uma fila.

Este tutorial cria um aplicativo lógico que monitora as alterações feitas em uma máquina virtual e envia emails sobre essas alterações. Quando você cria um aplicativo lógico com uma assinatura de evento para um recurso do Azure, os eventos são enviados desse recurso por meio de uma grade de eventos para o aplicativo lógico. O tutorial percorre a compilação desse aplicativo lógico:

![Visão geral – como monitorar uma máquina virtual com a grade de eventos e o aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/monitor-virtual-machine-event-grid-logic-app-overview.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo lógico que monitora os eventos de uma grade de eventos.
> * Adicionar uma condição que verifica especificamente se há alterações na máquina virtual.
> * Enviar email quando sua máquina virtual é alterada.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta de email de [qualquer provedor de email compatível com Aplicativos Lógicos do Azure](../connectors/apis-list.md), como o Office 365 Outlook, Outlook.com ou Gmail para envio de notificações. Este tutorial usa o Office 365 Outlook.

* Uma [máquina virtual](https://azure.microsoft.com/services/virtual-machines). Se você ainda não fez isso, crie uma máquina virtual por meio de um [tutorial sobre como criar uma VM](https://docs.microsoft.com/azure/virtual-machines/). Para fazer com que a máquina virtual publique eventos, [não é necessário fazer mais nada](../event-grid/overview.md).

## <a name="create-a-logic-app-that-monitors-events-from-an-event-grid"></a>Criar um aplicativo lógico que monitora os eventos de uma grade de eventos

Primeiro, crie um aplicativo lógico e adicione um gatilho de Grade de eventos que monitora o grupo de recursos de sua máquina virtual. 

1. Entre no [Portal do Azure](https://portal.azure.com). 

2. No canto superior esquerdo do menu principal do Azure, escolha **Novo** > **Integração Empresarial** > **Aplicativo Lógico**.

   ![Criar aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/azure-portal-create-logic-app.png)

3. Crie seu aplicativo lógico com as configurações especificadas na tabela a seguir:

   ![Defina os detalhes do aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/create-logic-app-for-event-grid.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *{nome de seu aplicativo lógico}* | Forneça um nome exclusivo de aplicativo lógico. | 
   | **Assinatura** | *{sua assinatura do Azure}* | Selecione a mesma assinatura do Azure para todos os serviços neste tutorial. | 
   | **Grupo de recursos** | *{seu grupo de recursos do Azure}* | Selecione o mesmo grupo de recursos do Azure para todos os serviços neste tutorial. | 
   | **Localidade** | *{sua região do Azure}* | Selecione a mesma região para todos os serviços neste tutorial. | 
   | | | 

4. Quando estiver pronto, selecione **Fixar no painel** e, em seguida, **Criar**.

   Agora você criou um recurso do Azure para o seu aplicativo lógico. 
   Depois que o Azure implanta o aplicativo lógico, o designer de aplicativos lógicos mostra modelos para padrões comuns para que você possa começar o quanto antes.

   > [!NOTE] 
   > Quando você seleciona **Fixar no painel**, seu aplicativo lógico abre automaticamente no designer de aplicativos lógicos. Se isso não acontecer, encontre e abra o aplicativo lógico manualmente.

5. Agora escolha um modelo de aplicativo lógico. Em **Modelos**, escolha **Aplicativo lógico em branco** para compilar seu aplicativo lógico do zero.

   ![Escolher o modelo de aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/choose-logic-app-template.png)

   Agora, o Designer de Aplicativos Lógicos mostra os [*conectores*](../connectors/apis-list.md) e os [*gatilhos*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) que podem ser usados para iniciar seu aplicativo lógico, além de ações que podem ser adicionadas depois do gatilho para desempenhar tarefas. Um gatilho é um evento que cria uma instância do aplicativo lógico e inicia o fluxo de trabalho do aplicativo lógico. 
   O primeiro item do aplicativo lógico deve ser um gatilho.

6. Insira "grade de eventos" como filtro na caixa de pesquisa. Selecione este gatilho: **Grade de Eventos do Azure - em um evento de recurso**

   ![Selecione este gatilho: "Grade de Eventos do Azure - em um evento de recurso"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger.png)

7. Quando receber a solicitação, entre na Grade de Eventos do Azure com suas credenciais do Azure.

   ![Entrar com suas credenciais do Azure](./media/monitor-virtual-machine-changes-event-grid-logic-app/sign-in-event-grid.png)

   > [!NOTE]
   > Se você estiver conectado com uma conta pessoal da Microsoft, como @outlook.com ou @hotmail.com, talvez o gatilho da Grade de Eventos não apareça corretamente. Como alternativa, escolha [Conectar-se à Entidade de Serviço](/azure-resource-manager/resource-group-create-service-principal-portal.md) ou autentique como membro do Azure Active Directory associado à sua assinatura do Azure, por exemplo, *nome de usuário*@emailoutlook.onmicrosoft.com.

8. Agora, assine seu aplicativo lógico para eventos do publicador. Forneça os detalhes para a sua assinatura de evento, conforme especificado na tabela a seguir:

   ![Forneça detalhes para a assinatura de evento](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details-generic.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Assinatura** | *{assinatura de máquina virtual do Azure}* | Selecione a assinatura do Azure do publicador do evento. Para este tutorial, selecione a assinatura do Azure de sua máquina virtual. | 
   | **Tipo de recurso** | Microsoft.Resources.resourceGroups | Selecione o tipo de recurso do publicador do evento. Para este tutorial, selecione o valor especificado para que seu aplicativo lógico monitore somente os grupos de recursos. | 
   | **Nome do recurso** | *{nome do grupo de recursos da máquina virtual}* | Selecione o nome do recurso do publicador. Para este tutorial, selecione o nome do grupo de recursos para sua máquina virtual. | 
   | Para acessar as configurações opcionais, escolha **Mostrar opções avançadas**. | *{consulte as descrições}* | * **Filtro do prefixo**: para este tutorial, deixe essa configuração em branco. O comportamento padrão corresponde a todos os valores. No entanto, você pode especificar uma cadeia de caracteres de prefixo como filtro, por exemplo, um caminho e um parâmetro para um recurso específico. <p>* **Filtro de sufixo**: para este tutorial, deixe essa configuração em branco. O comportamento padrão corresponde a todos os valores. No entanto, você pode especificar uma cadeia de caracteres de sufixo como filtro, por exemplo, uma extensão de nome de arquivo, quando quiser tipos específicos de arquivo.<p>* **Nome da Assinatura**: forneça um nome exclusivo para a assinatura de evento. |
   | | | 

   Quando terminar, seu gatilho da grade de eventos poderá parecer com este exemplo:
   
   ![Exemplo: detalhes de gatilho de grade de eventos](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-trigger-details.png)

9. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer. Para recolher e ocultar os detalhes de uma ação no seu aplicativo lógico, selecione a barra de título da ação.

   ![Salve seu aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save.png)

   Ao salvar seu aplicativo lógico com um gatilho de grade de eventos, o Azure cria automaticamente uma assinatura de evento para seu aplicativo lógico para o recurso selecionado. Portanto, quando o recurso publica um evento para a grade de eventos, essa grade de eventos envia o evento automaticamente para o seu aplicativo lógico. Este evento dispara seu aplicativo lógico, depois cria e executa uma instância do fluxo de trabalho que será definida nas próximas etapas.

Seu aplicativo lógico já está ativo e escuta eventos da grade de eventos, mas não fará nada até que você adicione ações ao fluxo de trabalho. 

## <a name="add-a-condition-that-checks-for-virtual-machine-changes"></a>Como adicionar uma condição que verifica se há alterações na máquina virtual

Para executar o fluxo de trabalho de seu aplicativo lógico somente quando ocorre um evento específico, adicione uma condição que verifica operações de gravação na máquina virtual. Quando essa condição é verdadeira, o aplicativo lógico envia um email com detalhes sobre a máquina virtual atualizada.

1. No designer de aplicativo lógico, no gatilho de grade de eventos, escolha **Nova etapa** > **Adicionar uma condição**.

   ![Como adicionar uma condição ao aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-add-condition-step.png)

   O Designer de Aplicativo Lógico adiciona uma condição vazia ao seu fluxo de trabalho, incluindo caminhos de ação a seguir baseado no status da condição: verdadeira ou falsa.

   ![Condição vazia](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-add-empty-condition.png)

2. Na caixa **Condição**, escolha **Editar no modo avançado**.
Digite esta expressão:

   `@equals(triggerBody()?['data']['operationName'], 'Microsoft.Compute/virtualMachines/write')`

   Agora, sua condição é semelhante a este exemplo:

   ![Condição vazia](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-expression.png)

   Essa expressão verifica o evento `body` em busca de um objeto `data`, cuja propriedade `operationName` é a operação `Microsoft.Compute/virtualMachines/write`. 
   Saiba mais sobre [Esquema de grade de eventos do evento](../event-grid/event-schema.md).

3. Para fornecer uma descrição para a condição, selecione o botão **elipses** (**...**) no formato da condição e, depois, escolha **Renomear**.

   > [!NOTE] 
   > Os últimos exemplos deste tutorial também fornecem descrições para as etapas no fluxo de trabalho do aplicativo lógico.

4. Agora, escolha **Editar no modo básico** para que a expressão seja resolvida automaticamente, conforme exibido:

   ![Condição de aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-1.png)

5. Salve seu aplicativo lógico.

## <a name="send-email-when-your-virtual-machine-changes"></a>Enviar email quando sua máquina virtual é alterada

Agora, adicione uma [*ação*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts) para enviar um email quando a condição for definida como verdadeira.

1. Na caixa **Se verdadeiro** da condição, escolha **Adicionar uma ação**.

   ![Como adicionar uma ação para condição definida como verdadeira](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-condition-2.png)

2. Na caixa de pesquisa, insira "email" como filtro. Com base no seu provedor de email, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar email" para o conector. Por exemplo: 

   * Por exemplo, se você tiver uma conta corporativa ou de estudante do Azure, selecione o conector Office 365 Outlook. 
   * Para contas pessoais da Microsoft, selecione o conector do Outlook.com. 
   * Para as contas do Gmail, selecione o conector do Gmail. 

   Vamos continuar com o conector do Office 365 Outlook. 
   Se você usar outro provedor, as etapas serão as mesmas, mas a IU pode aparecer diferente. 

   ![Selecione a ação "enviar email"](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email.png)

3. Se você ainda não tiver uma conexão para o seu provedor de email, quando solicitado, acesse o seu e-mail para autenticação.

4. Forneça detalhes para o email conforme especificado na tabela a seguir:

   ![Ação de esvaziar email](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-empty-email-action.png)

   > [!TIP]
   > Para selecionar os campos disponíveis em seu fluxo de trabalho, clique em uma caixa de edição assim que a lista **Conteúdo dinâmico** abre, ou escolha **Adicionar conteúdo dinâmico**. Para ver mais campos, escolha **Ver mais** para cada seção na lista. Para fechar a lista **Conteúdo dinâmico**, escolha **Adicionar conteúdo dinâmico**.

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Para** | *{endereço de email do destinatário}* |Insira o endereço de email do destinatário. Para fins de teste, você pode usar seu próprio endereço de email. | 
   | **Assunto** | Recurso atualizado: **Assunto**| Insira o conteúdo do assunto do email. Para este tutorial, insira o texto sugerido e selecione o campo **Assunto** do evento. Aqui, o assunto do email inclui o nome do recurso atualizado (máquina virtual). | 
   | **Corpo** | Grupo de recursos: **Tópico** <p>Tipo de evento: **Tipo de Evento**<p>ID do evento: **ID**<p>Hora: **Hora do Evento** | Insira o conteúdo do corpo do email. Para este tutorial, insira o texto sugerido e selecione os campos **Tópico**, **Tipo de Evento**, **ID** e **Hora do Evento** para que seu email inclua o nome do grupo de recursos, o tipo de evento, o carimbo de data/hora do evento e a ID do evento para a atualização. <p>Para adicionar linhas em branco em seu conteúdo, pressione Shift + Enter. | 
   | | | 

   > [!NOTE] 
   > Se você selecionar um campo que representa uma matriz, o designer adiciona automaticamente um loop **para cada** em torno da ação que faz referência à matriz. Dessa forma, seu aplicativo lógico executará essa ação em cada item da matriz.

   Agora, a ação do email pode parecer com este exemplo:

   ![Selecione as saídas para incluir no email](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-send-email-details.png)

   Seu aplicativo lógico concluído pode parecer com este exemplo:

   ![Aplicativo lógico concluído](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-completed.png)

5. Salve seu aplicativo lógico. Para recolher e ocultar os detalhes de cada ação no seu aplicativo lógico, escolha a barra de título da ação.

   ![Salve seu aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-event-grid-save-completed.png)

   Seu aplicativo lógico já está ativo, mas aguardará as alterações na sua máquina virtual antes de realizar qualquer ação. 
   Para testar o seu aplicativo lógico agora, siga para a próxima seção.

## <a name="test-your-logic-app-workflow"></a>Testar o fluxo de trabalho de seu aplicativo lógico

1. Para verificar se o seu aplicativo lógico está obtendo os eventos especificados, atualize sua máquina virtual. 

   Por exemplo, você pode redimensionar a máquina virtual no portal do Azure ou [redimensionar a VM com o Azure PowerShell](../virtual-machines/windows/resize-vm.md). 

   Você receberá um email em alguns instantes. Por exemplo:

   ![Email sobre a atualização da máquina virtual](./media/monitor-virtual-machine-changes-event-grid-logic-app/email.png)

2. Para analisar o histórico de gatilho e execução do seu aplicativo lógico, escolha **Visão geral** no menu do aplicativo lógico. Para exibir mais detalhes sobre uma execução, escolha a linha para essa execução.

   ![Histórico de execuções do aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history.png)

3. Para exibir as entradas e saídas de cada etapa, expanda a etapa que você deseja examinar. Essas informações podem ajudar você a diagnosticar e depurar problemas em seu aplicativo lógico.
 
   ![Detalhes do histórico de execução do aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/logic-app-run-history-details.png)

Parabéns, você acabou de criar e executar um aplicativo lógico capaz de monitorar eventos de recursos por uma grade de eventos e envio de emails para notificar o acontecimento de tais eventos. Você também aprendeu como é fácil criar fluxos de trabalho que automatizam processos e integram sistemas e serviços de nuvem.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial usa recursos e executa ações que geram encargos na sua assinatura do Azure. Ao concluir o tutorial e testar, confira se desabilitou ou excluiu todos os recursos que você não deseja para evitar encargos.

Você pode impedir a execução e o envio de email do aplicativo lógico sem excluir o aplicativo. No menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Desabilitar**.

![Como desabilitar o aplicativo lógico](./media/monitor-virtual-machine-changes-event-grid-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>Perguntas frequentes

**P**: que outras tarefas de monitoração de máquina virtual eu posso executar com grades de eventos e aplicativos lógicos? </br>
**R**: você pode monitorar outras alterações de configuração, por exemplo:

* Uma máquina virtual obtém direitos de controle de acesso baseado em função (RBAC).
* As alterações são feitas em um grupo de segurança de rede (NSG) em um adaptador de rede (NIC).
* Discos para uma máquina virtual são adicionados ou removidos.
* Um endereço IP público é atribuído a uma máquina virtual NIC.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da grade de eventos](../event-grid/overview.md)
* [Conceitos da grade de eventos](../event-grid/concepts.md)
* [Início rápido: Como criar e encaminhar eventos personalizados com a grade de eventos](../event-grid/custom-event-quickstart.md)
* [Esquema de evento da grade de eventos](../event-grid/event-schema.md)
* [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md)
* [Criar fluxos de trabalho de aplicativo lógico com modelos predefinidos](../logic-apps/logic-apps-use-logic-app-templates.md)