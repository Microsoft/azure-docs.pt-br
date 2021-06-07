---
title: Criar fluxos de trabalho automatizados baseados em aprovação
description: Tutorial – Criar um fluxo de trabalho automatizado baseado em aprovação que processa as assinaturas da lista de endereçamento usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: c6186e6e9f60e852f77943834bcd1ae3d526491d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777267"
---
# <a name="tutorial-create-automated-approval-based-workflows-by-using-azure-logic-apps"></a>Tutorial: Criar fluxos de trabalho automatizados baseados em aprovação usando os Aplicativos Lógicos do Azure

Este tutorial mostra como criar um [aplicativo lógico](../logic-apps/logic-apps-overview.md) de exemplo que automatiza um fluxo de trabalho baseado em aprovação. Especificamente, este aplicativo lógico de exemplo processa as solicitações de assinatura para uma lista de distribuição gerenciada pelo serviço [MailChimp](https://mailchimp.com/). Esse aplicativo lógico inclui várias etapas, que começam monitorando uma conta de email para solicitações, enviam essas solicitações para aprovação, verificam se a solicitação é ou não aprovada, adicionam membros aprovados à lista de distribuição e confirmam se novos membros são adicionados ou não à lista.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
>
> * Criar um aplicativo lógico em branco.
> * Adicionar um gatilho que monitora emails com solicitações de assinatura.
> * Adicionar uma ação que envia emails para aprovar ou rejeitar essas solicitações.
> * Adicionar uma condição que verifica a resposta de aprovação.
> * Adicionar uma ação que adiciona membros aprovados à lista de endereçamento.
> * Adicionar uma condição que verifica se esses membros ingressaram na lista com êxito.
> * Adicionar uma ação que envia emails confirmando se esses membros ingressaram na lista com êxito.

Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Visão geral de aplicativo lógico concluído em alto nível](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Uma conta do MailChimp em que você anteriormente criou uma lista chamada "test-members-ML", à qual o aplicativo lógico pode adicionar endereços de email para membros aprovados. Se você não tiver uma conta, [inscreva-se em uma conta gratuita](https://login.mailchimp.com/signup/) e, em seguida, saiba [como criar uma lista do MailChimp](https://us17.admin.mailchimp.com/lists/#).

* Uma conta de email de qualquer provedor de email compatível com os Aplicativos Lógicos, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros provedores, [revise a lista de conectores aqui](/connectors/). Este início rápido usa o Outlook do Office 365 com uma conta corporativa ou de estudante. Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário será ligeiramente diferente.

* Uma conta de email no Office 365 Outlook ou no Outlook.com, que dê suporte aos fluxos de trabalho de aprovação. Este tutorial usa o Office 365 Outlook. Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário poderá parecer um pouco diferente.

* Se o seu aplicativo lógico precisar se comunicar por um firewall que limita o tráfego a endereços IP específicos, esse firewall precisará permitir o acesso *tanto* para os endereços IP de [entrada](logic-apps-limits-and-config.md#inbound) quanto para os de [saída](logic-apps-limits-and-config.md#outbound) usados pelo runtime ou serviço de Aplicativos Lógicos na região do Azure em que seu aplicativo lógico existe. Se o seu aplicativo lógico também usar [conectores gerenciados](../connectors/managed.md), como o conector Outlook do Office 365 ou SQL, ou usar [conectores personalizados](/connectors/custom-connectors/), o firewall também precisará permitir o acesso para *todos* os [endereços IP de saída do conector gerenciado](logic-apps-limits-and-config.md#outbound) na região do Azure do seu aplicativo lógico.

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure. Na página inicial do Azure, selecione **Criar um recurso**.

1. No menu do Azure Marketplace, selecione **Integração** > **Aplicativo Lógico**.

   ![Captura de tela que mostra o menu do Azure Marketplace com "Integração" e "Aplicativo Lógico" selecionados.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-new-logic-app-resource.png)

1. No painel **Aplicativo Lógico**, forneça as informações descritas aqui sobre o aplicativo lógico que você deseja criar.

   ![Captura de tela que mostra o painel de criação do Aplicativo Lógico e as informações a serem fornecidas para o novo aplicativo lógico.](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Assinatura** | <*Azure-subscription-name*> | Seu nome da assinatura do Azure. Este exemplo usa o `Pay-As-You-Go`. |
   | **Grupo de recursos** | LA-MailingList-RG | O nome do [grupo de recursos do Azure](../azure-resource-manager/management/overview.md), usado para organizar os recursos relacionados. Esse exemplo cria um grupo de recursos chamado `LA-MailingList-RG`. |
   | **Nome** | LA-MailingList | O nome do seu aplicativo lógico, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) e pontos (`.`). Este exemplo usa o `LA-MailingList`. |
   | **Localidade** | Oeste dos EUA | A região em que as informações de seu aplicativo lógico serão armazenadas. Este exemplo usa o `West US`. |
   | **Log Analytics** | Desativado | Mantenha a configuração **Desligado** para o log de diagnósticos. |
   ||||

1. Quando terminar, selecione **Review + create**. Depois que o Azure validar as informações sobre seu aplicativo lógico, selecione **Criar**.

1. Após o Azure implantar o aplicativo, selecione **Ir para o recurso**.

   O Azure abre o painel de seleção de modelo dos Aplicativos Lógicos, que mostra um vídeo de introdução, gatilhos comumente usados e padrões de modelo de aplicativo lógico.

1. Role para baixo após o vídeo e as seções de gatilhos comuns até a seção **Modelos** e selecione **Aplicativo Lógico em Branco**.

   ![Captura de tela que mostra o painel de seleção de modelo dos Aplicativos Lógicos com "Aplicativo Lógico em Branco" selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-logic-app-template.png)

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) do Outlook que escuta emails de entrada com solicitações de assinatura. Cada aplicativo lógico precisa começar com um gatilho, disparado quando um evento específico ocorre ou quando novos dados atendem a uma condição específica. Para saber mais, confira [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Adiciona gatilho para monitorar emails

1. Na caixa de pesquisa do Designer de Aplicativos Lógicos, insira `when email arrives` e selecione o gatilho chamado **Quando um novo email for recebido**.

   * Para as contas corporativas ou de estudante do Azure, selecione o **Outlook do Office 365**.
   * Para contas pessoais da Microsoft, selecione **Outlook.com**.

   Este exemplo continua selecionando o Outlook do Office 365.

   ![Captura de tela que mostra a caixa de pesquisa do Designer de Aplicativos Lógicos que contém o termo de pesquisa "quando um email for recebido", e o gatilho "Quando um novo email for recebido" aparece selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

1. Se você ainda não tiver uma conexão, entre e autentique o acesso à sua conta de email quando solicitado.

   Os Aplicativos Lógicos do Azure criam uma conexão à sua conta de email.

1. No gatilho, forneça os critérios para verificar novos emails.

   1. Especifique a pasta para verificar emails e mantenha as outras propriedades definidas para os próprios valores padrão.

      ![Captura de tela que mostra o designer com a ação "Quando um novo email for recebido" e "Pasta" definidas como "Caixa de entrada".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

   1. Adicione a propriedade **Filtro de Assunto** do gatilho para que você possa filtrar emails com base na linha do assunto. Abra a lista **Adicionar novo parâmetro** e selecione **Filtro de Assunto**.

      ![Captura de tela que mostra a lista "Adicionar novo parâmetro" aberta com "Filtro de Assunto" selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-add-properties.png)

      Para obter mais informações sobre as propriedades desse gatilho, confira a [referência do conector do Office 365 Outlook](/connectors/office365/) ou a [referência do conector do Outlook.com](/connectors/outlook/).

   1. Depois que a propriedade for exibida no gatilho, insira este texto: `subscribe-test-members-ML`

      ![Captura de tela que mostra a propriedade "Filtro de Assunto" com o texto "subscribe-test-members-ML" inserido.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-subject-filter-property.png)

1. Por enquanto, para ocultar os detalhes do gatilho, recolha a forma clicando na barra de título dela.

   ![Captura de tela que mostra a forma do gatilho recolhida.](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Seu aplicativo lógico agora está ativo, mas ele não faz nada além de verificar o recebimento de emails. Portanto, adicione uma ação que responde quando o gatilho é acionado.

## <a name="send-approval-email"></a>Enviar email de aprovação

Agora que você tem um gatilho, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia um email para aprovar ou rejeitar a solicitação.

1. No Designer de Aplicativos Lógicos, no gatilho **Quando um novo email for recebido**, selecione **Nova etapa**.

1. Na caixa de pesquisa **Escolha uma operação**, insira `send approval` e selecione a ação nomeada **Enviar email de aprovação**.

   ![Captura de tela que mostra a lista "Escolha uma operação" filtrada por ações de "aprovação" e a ação "Enviar email de aprovação" selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

1. Agora, insira os valores para as propriedades especificadas mostradas e descritas aqui. deixando todos os outros com os valores padrão. Para obter mais informações sobre essas propriedades, confira a [referência do conector do Office 365 Outlook](/connectors/office365/) ou a [referência do conector do Outlook.com](/connectors/outlook/).

   ![Captura de tela que mostra as propriedades "Enviar email de aprovação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Para** | <*approval-email-address*> | Endereço de email do aprovador. Para fins de teste, você pode usar seu próprio endereço. Este exemplo usa o endereço de email fictício `sophiaowen@fabrikam.com`. |
   | **Assunto** | `Approve member request for test-members-ML` | Um assunto de email descritivo |
   | **Opções de Usuário** | `Approve, Reject` | Verifique se essa propriedade especifica as opções de resposta que o aprovador pode selecionar, que são **Aprovar** ou **Rejeitar** por padrão. |
   ||||

   > [!NOTE]
   > Quando você clica dentro de algumas caixas de edição, a lista de conteúdo dinâmico é exibida, que você pode ignorar por enquanto. O conteúdo dinâmico mostra as saídas de ações anteriores que estão disponíveis para você selecionar como entradas de ações subsequentes no fluxo de trabalho.
 
1. Salve seu aplicativo lógico.

Em seguida, adicione uma condição que verifica a resposta selecionada do aprovador.

## <a name="check-approval-response"></a>Verificar resposta de aprovação

1. Na ação **Enviar email de aprovação**, selecione **Nova etapa**.

1. Em **Escolher uma operação**, selecione **Interno**. Na caixa de pesquisa, insira `condition` e selecione a ação nomeada **Condição**.

   ![Captura de tela que mostra a caixa de pesquisa "Escolha uma operação" com "Interno" selecionado e "condição" como o termo de pesquisa, enquanto a ação "Condição" aparece selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/select-condition-action.png)

1. Na barra de título **Condição**, selecione o botão de **reticências** ( **...** ) e selecione **Renomear**. Renomeie a condição com esta descrição:`If request approved`

   ![Captura de tela que mostra o botão de reticências selecionado com a lista "Configurações" aberta e o comando "Renomear" selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/rename-condition-description.png)

1. Crie uma condição que verifica se o aprovador selecionou **Aprovar**.

   1. No lado esquerdo da condição, clique dentro da caixa **Escolha um valor**.

   1. Na lista de conteúdo dinâmico exibida, em **Enviar email de aprovação**, selecione a propriedade **SelectedOption**.

      ![Captura de tela que mostra a lista de conteúdo dinâmico em que, na seção "Enviar email de aprovação", a saída "SelectedOption" aparece selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   1. Na caixa de comparação intermediária, selecione o operador **é igual a**.

   1. Na caixa **Escolha um valor** à direita da condição, insira o texto `Approve`.

      Quando terminar, a condição será semelhante a este exemplo:

      ![Captura de tela que mostra a condição concluída para o exemplo de solicitação aprovada](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

1. Salve seu aplicativo lógico.

Em seguida, especifique a ação que seu aplicativo lógico executará quando o revisor aprovar a solicitação. 

## <a name="add-member-to-mailchimp-list"></a>Adicionar membros à lista do MailChimp

Agora adicione uma ação que adiciona o membro aprovado à sua lista de endereçamento.

1. No branch **Verdadeiro** da condição, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa **Escolha uma operação**, selecione **Tudo**. Na caixa de pesquisa, insira `mailchimp` e selecione a ação chamada **Adicionar membro à lista**.

   ![Captura de tela que mostra a caixa "Escolha uma operação" com o termo de pesquisa "mailchimp" e a ação "Adicionar membro à lista" selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

1. Se você ainda não tiver uma conexão com sua conta do MailChimp, você deverá se conectar.

1. Na ação **Adicionar membro à lista**, forneça as informações conforme mostrado e descrito aqui:

   ![Captura de tela que mostra as informações da ação "Adicionar membro à lista".](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **ID da lista** | Sim | <*mailing-list-name*> | Selecione o nome da sua lista de distribuição do MailChimp. Este exemplo usa o `test-members-ML`. |
   | **Endereço de Email** | Sim | <*new-member-email-address*> | Na lista de conteúdo dinâmico aberta, na seção **Quando um novo email for recebido**, selecione **De**, que é a saída do gatilho e especifique o endereço de email do novo membro. |
   | **Status** | Sim | <*member-subscription-status*> | Selecione o status da assinatura para definir o novo membro. Esse exemplo seleciona `subscribed`. <p>Para saber mais, confira [Gerenciar assinantes com a API do MailChimp](https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/). |
   |||||

   Para obter mais informações sobre as propriedades da ação **Adicionar membro à lista**, confira a [referência do conector do MailChimp](/connectors/mailchimp/).

1. Salve seu aplicativo lógico.

Em seguida, adicione uma condição para que você possa verificar se o novo membro ingressou na lista de endereçamento com êxito. Dessa forma, seu aplicativo lógico pode notificar se essa operação foi bem-sucedida ou apresentou falha.

## <a name="check-for-success-or-failure"></a>Verificar êxito ou falha

1. No branch **Verdadeiro**, na ação **Adicionar membro à lista**, selecione **Adicionar uma ação**.

1. Em **Escolher uma operação**, selecione **Interno**. Na caixa de pesquisa, insira `condition` e selecione a ação nomeada **Condição**.

1. Renomeie a condição com esta descrição:`If add member succeeded`

1. Crie uma condição que verifica se o membro aprovado teve êxito ou falha ao ingressar na lista de endereçamento:

   1. No lado esquerdo da condição, clique dentro da caixa **Escolha um valor**. Na lista conteúdo dinâmico exibida, na seção **Adicionar membro à lista**, selecione a propriedade **Status**.

      Por exemplo, sua condição é semelhante a este exemplo:

      ![Captura de tela que mostra a caixa "Escolha um valor" à esquerda da condição com "Status" inserido.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   1. Na caixa de comparação intermediária, selecione o operador **é igual a**.

   1. Na caixa **Escolher um valor** à direita da condição, insira este texto: `subscribed`

      Quando terminar, a condição será semelhante a este exemplo:

      ![Captura de tela que mostra a condição concluída para verificar a assinatura com êxito ou com falha.](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Em seguida, configure os emails para envio quando o membro aprovado tem êxito ou falha ao ingressar na lista de distribuição.

## <a name="send-email-if-member-added"></a>Enviar email se o membro foi adicionado

1. Na condição **Se adicionar membro com êxito**, no branch **Verdadeiro**, selecione **Adicionar uma ação**.

   ![Captura de tela que mostra o branch "Verdadeiro" da condição "Se adicionar membro foi bem-sucedido" com "Adicionar uma ação" selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

1. Na caixa de pesquisa **Escolha uma operação**, insira `outlook send email` e selecione a ação nomeada **Enviar um email**.

   ![Captura de tela que mostra a caixa de pesquisa "Escolha uma operação" com "enviar email do outlook" digitado e a ação "Enviar um email" selecionada para notificação.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

1. Renomeie a ação com esta descrição:`Send email on success`

1. Na ação **Enviar email em caso de êxito**, forneça as informações conforme mostrado e descrito aqui:

   ![Captura de tela que mostra a ação "Enviar email caso haja êxito" e as informações fornecidas para o email de êxito.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Corpo** | Sim | <*success-email-body*> | O conteúdo do corpo do email de êxito. Neste tutorial, siga estas etapas: <p>1. Insira este texto com um espaço à direita: `New member has joined "test-members-ML":` <p>2. Na lista de conteúdo dinâmico exibida, selecione a propriedade **Endereço de Email**. <p>**Observação**: Se essa propriedade não for exibida, ao lado da seção **Adicionar membro à lista**, selecione **Ver mais**. <p>3. Na próxima linha, insira este texto com um espaço à direita: `Member opt-in status: ` <p>4. Na lista conteúdo dinâmico, em **Adicionar membro à lista**, selecione a propriedade **Status**. |
   | **Assunto** | Sim | <*success-email-subject*> | O assunto do email de êxito. Neste tutorial, siga estas etapas: <p>1. Insira este texto com um espaço à direita: `Success! Member added to "test-members-ML": ` <p>2. Na lista conteúdo dinâmico, em **Adicionar membro à lista**, selecione a propriedade **Endereço de Email**. |
   | **Para** | Sim | <*your-email-address*> | O endereço de email para onde enviar o email de êxito. Para fins de teste, você pode usar seu próprio endereço de email. |
   |||||

1. Salve seu aplicativo lógico.

## <a name="send-email-if-member-not-added"></a>Enviar email se membro não foi adicionado

1. Na condição **Se adicionar membro com êxito**, no branch **Falso**, selecione **Adicionar uma ação**.

   ![Captura de tela que mostra o branch "Falso" da condição "Se adicionar membro foi bem-sucedido" com "Adicionar uma ação" selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

1. Na caixa de pesquisa **Escolha uma operação**, insira `outlook send email` e selecione a ação nomeada **Enviar um email**.

   ![Captura de tela que mostra a caixa de pesquisa "Escolha uma operação" com "enviar email do outlook" digitado e a ação "Enviar um email" selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

1. Renomeie a ação com esta descrição:`Send email on failure`

1. Forneça informações sobre essa ação, conforme mostrado e descrito aqui:

   ![Captura de tela que mostra a ação "Enviar email caso haja falha" e as informações fornecidas para o email de falha.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Corpo** | Sim | <*body-for-failure-email*> | O conteúdo do corpo do email de falha. Para este tutorial, digite este texto: <p>`Member might already exist. Check your MailChimp account.` |
   | **Assunto** | Sim | <*subject-for-failure-email*> | O assunto do email de falha. Neste tutorial, siga estas etapas: <p>1. Insira este texto com um espaço à direita: `Failed, member not added to "test-members-ML": ` <p>2. Na lista conteúdo dinâmico, em **Adicionar membro à lista**, selecione a propriedade **Endereço de Email**. |
   | **Para** | Sim | <*your-email-address*> | O endereço de email para onde enviar o email de falha. Para fins de teste, você pode usar seu próprio endereço de email. |
   |||||

1. Salve seu aplicativo lógico. 

Em seguida, teste seu aplicativo lógico, que agora se parece com este exemplo:

![Captura de tela que mostra o fluxo de trabalho do aplicativo lógico concluído.](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-high-level-completed.png)

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

1. Envie uma solicitação de email a si mesmo para ingressar na lista de endereçamento. Aguarde até que a solicitação seja exibida na caixa de entrada.

1. Para executar manualmente o aplicativo lógico, selecione **Executar** na barra de ferramentas do designer. 

   Se seu email tiver uma entidade que corresponde ao filtro de assunto do disparador, o aplicativo lógico envia um email para aprovar a solicitação de assinatura.

1. No email de aprovação que você receber, selecione **Aprovar**.

1. Se o endereço de email do assinante não existe na sua lista de endereçamento, o aplicativo lógico adiciona o endereço de email dessa pessoa e envia um email como neste exemplo:

   ![Captura de tela que mostra o email de exemplo para uma assinatura bem-sucedida.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-success.png)

   Se seu aplicativo lógico não consegue adicionar o assinante, você recebe um email como neste exemplo:

   ![Captura de tela que mostra o email de exemplo de uma assinatura com falha.](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-mailing-list-failed.png)

  > [!TIP]
  > Se você não receber nenhum email, verifique a pasta de Lixo eletrônico. O filtro de lixo de email pode redirecionar esses tipos de mensagens. Caso contrário, se você não tiver certeza de que seu aplicativo lógico foi executado corretamente, confira [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Parabéns, você criou e executou um aplicativo lógico que integra informações entre serviços Azure, Microsoft e outros aplicativos SaaS.

## <a name="clean-up-resources"></a>Limpar os recursos

Seu aplicativo lógico continuará sendo executado até que você o desabilite ou exclua. Quando você não precisar mais do aplicativo lógico de exemplo, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados.

1. Na caixa de pesquisa do portal do Azure, digite o nome do grupo de recursos que você criou. Nos resultados, em **Grupos de Recursos**, selecione o grupo de recursos.

   Este exemplo cria o grupo de recursos chamado `LA-MailingList-RG`.

   ![Captura de tela que mostra a caixa de pesquisa do Azure com "la-mailinglist-rg" inserido e **LA-MailingList-RG** selecionado.](./media/tutorial-process-mailing-list-subscriptions-workflow/find-resource-group.png)

   > [!TIP]
   > Se a página inicial do Azure mostrar o grupo de recursos em **Recursos recentes**, você poderá selecionar o grupo na página inicial.

1. No menu do grupo de recursos, verifique se a **Visão Geral** está selecionada. Na barra de ferramentas do painel **Visão Geral**, selecione **Excluir grupo de recursos**.

   ![Captura de tela que mostra o painel "Visão Geral" do grupo de recursos e, na barra de ferramentas do painel, a opção "Excluir grupo de recursos" está selecionada.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

1. No painel de confirmação exibido, insira o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo lógico que manipula as aprovações para solicitações de lista de distribuição. Agora, aprenda a criar um aplicativo lógico que processa e armazena anexos de email integrando serviços do Azure, como o Armazenamento do Azure e o Azure Functions.

> [!div class="nextstepaction"]
> [Processar anexos do email](../logic-apps/tutorial-process-email-attachments-workflow.md)
