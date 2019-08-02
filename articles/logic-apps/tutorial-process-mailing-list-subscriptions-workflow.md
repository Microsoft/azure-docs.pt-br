---
title: Criar fluxos de trabalho de aprovação para processar solicitações de lista de endereçamento - Aplicativos Lógicos do Azure | Microsoft Docs
description: 'Tutorial: Como criar fluxos de trabalho de aprovação automáticos para processar assinaturas da lista de endereçamento com Aplicativos Lógicos do Azure'
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/12/2018
ms.openlocfilehash: 2b28c38d2444f227d26df1f9ca2d70876ff41064
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/16/2019
ms.locfileid: "68260606"
---
# <a name="manage-mailing-list-requests-with-azure-logic-apps"></a>Gerenciar solicitações da lista de endereçamento com os Aplicativos Lógicos do Azure

Os Aplicativos Lógicos do Azure ajudam a automatizar fluxos de trabalho e a integrar os dados entre os serviços do Azure, os serviços da Microsoft, outros aplicativos SaaS (software como serviço) e sistemas locais. Este tutorial mostra como você pode criar um [aplicativo lógico](../logic-apps/logic-apps-overview.md) que processa solicitações de assinatura para uma lista de endereçamento gerenciado pelo serviço [MailChimp](https://mailchimp.com/).
Este aplicativo lógico monitora uma conta de email para essas solicitações, envia essas solicitações para aprovação e adiciona membros aprovados para a lista de endereçamento.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo lógico em branco.
> * Adicionar um gatilho que monitora emails com solicitações de assinatura.
> * Adicionar uma ação que envia emails para aprovar ou rejeitar essas solicitações.
> * Adicionar uma condição que verifica a resposta de aprovação.
> * Adicionar uma ação que adiciona membros aprovados à lista de endereçamento.
> * Adicionar uma condição que verifica se esses membros ingressaram na lista com êxito.
> * Adicionar uma ação que envia emails confirmando se esses membros ingressaram na lista com êxito.

Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Aplicativo lógico concluído em alto nível](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-overview.png)

Caso você não tenha uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">crie uma conta gratuita do Azure</a> antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta do MailChimp. Crie uma lista chamada "test-members-ML" em que o aplicativo lógico pode adicionar endereços de email para membros aprovados. Se você não tiver uma conta, [inscreva-se em uma conta gratuita](https://login.mailchimp.com/signup/) e saiba [como criar uma lista](https://us17.admin.mailchimp.com/lists/#). 

* Uma conta de email com o Outlook do Office 365 ou o Outlook.com que dê suporte a fluxos de trabalho de aprovação. Este artigo usa o Outlook do Office 365. Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário poderá parecer um pouco diferente.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no <a href="https://portal.azure.com" target="_blank">portal do Azure</a> com suas credenciais da conta do Azure.

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. No menu principal do Azure, escolha **Criar um recurso** > **Enterprise Integration** > **Aplicativo Lógico**.

   ![Criar aplicativo lógico](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app.png)

2. Em **Criar aplicativo lógico**, forneça essas informações sobre seu aplicativo lógico, como mostrado e descrito. Quando tiver concluído, escolha **Fixar no painel** > **Criar**.

   ![Fornecer informações de aplicativo lógico](./media/tutorial-process-mailing-list-subscriptions-workflow/create-logic-app-settings.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Nome** | LA-MailingList | O nome do seu aplicativo lógico | 
   | **Assinatura** | <*nome-da-sua-assinatura-do-Azure*> | O nome e a ID da assinatura do Azure | 
   | **Grupo de recursos** | LA-MailingList-RG | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) usado para organizar os recursos relacionados | 
   | **Localidade** | Leste dos EUA 2 | A região na qual armazenar informações sobre o seu aplicativo lógico | 
   | **Log Analytics** | Desativar | Mantenha a configuração **Desligado** para o log de diagnósticos. | 
   |||| 

3. Depois que o Azure implanta o aplicativo, o Designer de Aplicativos Lógicos é exibido e mostra uma página com um vídeo introdutório e modelos de padrões de aplicativo lógico comuns. Em **Modelos**, escolha **Aplicativo lógico em branco**.

   ![Escolha o modelo de aplicativo lógico em branco](./media/tutorial-process-mailing-list-subscriptions-workflow/choose-logic-app-template.png)

Em seguida, adicione um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) que escuta emails de entrada com solicitações de assinatura.
Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando novos dados atendem a uma condição específica. Para saber mais, confira [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-trigger-to-monitor-emails"></a>Adiciona gatilho para monitorar emails

1. No designer, digite "quando o email chega" na caixa de pesquisa. Selecione esse disparador para seu provedor de email:  **< *seu-provedor-de-email*> - Quando um novo email é recebido**
   
   ![Selecione esse gatilho para o provedor de email: "Quando um novo email é recebido"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-new-email.png)

   * Para as contas corporativas ou de estudante do Azure, selecione o Outlook do Office 365.
   * Para contas pessoais da Microsoft, selecione Outlook.com.

2. Se forem solicitadas credenciais, entre na sua conta de email para que os Aplicativos Lógicos possam uma conexão à sua conta de email.

3. Agora, especifique os critérios que o gatilho verifica em todos os novos emails.

   1. Especifique a pasta, o intervalo e a frequência de verificação de emails.

      ![Especificar a pasta, intervalo e frequência de verificação de emails](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-up-email.png)

      | Configuração | Valor | DESCRIÇÃO | 
      | ------- | ----- | ----------- | 
      | **Pasta** | Caixa de Entrada | A pasta de email a ser monitorada | 
      | **Intervalo** | 1 | O número de intervalos de espera entre as verificações | 
      | **Frequência** | Hora | A unidade de tempo para cada intervalo entre verificações  | 
      |  |  |  | 

   2. Escolha **Mostrar opções avançadas**. Na caixa **Filtro de Assunto**, digite este texto para o disparador localizar no assunto do email:```subscribe-test-members-ML```

      ![Definir opções avançadas](./media/tutorial-process-mailing-list-subscriptions-workflow/add-trigger-set-advanced-options.png)

4. Por enquanto, para ocultar os detalhes do gatilho, clique na barra de título do gatilho.

   ![Recolher forma para ocultar detalhes](./media/tutorial-process-mailing-list-subscriptions-workflow/collapse-trigger-shape.png)

5. Salve seu aplicativo lógico. Clique em **Salvar** na barra de ferramentas do designer.

   Seu aplicativo lógico agora está ativo, mas ele não faz nada além de verificar o recebimento de emails. 
   Portanto, adicione uma ação que responde quando o gatilho é acionado.

## <a name="send-approval-email"></a>Enviar email de aprovação

Agora que você tem um gatilho, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que envia um email para aprovar ou rejeitar a solicitação. 

1. No gatilho, escolha **+ Nova etapa** > **Adicionar uma ação**. Pesquise "aprovação" e selecione esta ação:  **< *seu provedor de email*>-Enviar email de aprovação**

   ![Escolher "<seu-provedor-email> - Enviar email de aprovação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-send-approval-email.png)

2. Forneça informações para essa ação, como mostrado e descrito: 

   ![Definir configurações de email de aprovação](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-approval-email-settings.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Para** | <*approver-email-address*> | Endereço de email do aprovador. Para fins de teste, você pode usar seu próprio endereço. | 
   | **Opções de Usuário** | Aprovar, Rejeitar | As opções de resposta que o aprovador pode escolher. Por padrão, o aprovador pode escolher "Aprovar" ou "Rejeitar" como resposta. | 
   | **Assunto** | Aprovar solicitação de membro para test-members-ML | Um assunto de email descritivo | 
   |  |  |  | 

   Por enquanto, ignore a lista de conteúdo dinâmico ou a lista de parâmetros embutidos que aparece quando você clica em caixas de edição específicas. 
   A lista permite selecionar os parâmetros de ações anteriores que você pode usar como entradas no seu fluxo de trabalho. 
   A largura do navegador determina a lista exibida. 
 
4. Salve seu aplicativo lógico.

Em seguida, adicione uma condição para verificar a resposta escolhida do aprovador.

## <a name="check-approval-response"></a>Verificar resposta de aprovação

1. Na ação **Enviar email de aprovação**, escolha **+ Nova etapa** > **Adicionar uma condição**.

   A forma da condição é exibida, juntamente com os parâmetros disponíveis que você pode incluir como entrada para o fluxo de trabalho. 

2. Renomeie a condição com uma descrição melhor.

   1. Na barra de título da condição, escolha o botão de **reticências** ( **...** ) > **Renomear**.

      Por exemplo, se seu navegador estiver na exibição estreita:

      ![Renomear condição](./media/tutorial-process-mailing-list-subscriptions-workflow/condition-rename.png)

      Se seu navegador estiver na exibição ampla e a lista de conteúdo dinâmico bloquear o acesso ao botão de reticências, feche a lista escolhendo **Adicionar conteúdo dinâmico** dentro da condição.

   2. Renomeie sua condição com esta descrição:```If request approved```

3. Crie uma condição que verifica se o aprovador selecionou **Aprovar**:

   1. Dentro da condição, clique na caixa **Escolha um valor**, que está à esquerda (exibição de navegador ampla) ou na parte superior (exibição de navegador estreita).
   Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione o campo **SelectedOption** em **Enviar email de aprovação**.

      Por exemplo, se você estiver trabalhando na exibição ampla, sua condição será semelhante a este exemplo:

      ![Em "Enviar email de aprovação", selecionar "SelectedOption"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response.png)

   2. Na caixa de operador de comparação, selecione este operador: **é igual a**

   3. Na caixa à direita (exibição ampla) ou na parte inferior (exibição estreita) **Escolha um valor**, insira este limite:```Approve```

      Ao concluir, a sua condição será semelhante a este exemplo:

      ![Condição completa](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-approval-response-2.png)

4. Salve seu aplicativo lógico.

Em seguida, especifique a ação que seu aplicativo lógico executará quando o revisor aprovar a solicitação. 

## <a name="add-member-to-mailchimp-list"></a>Adicionar membros à lista do MailChimp

Agora, adicione uma ação que adiciona o membro aprovado à sua lista de endereçamento.

1. Na ramificação **Se verdadeiro** da condição, escolha **Adicionar uma ação**.
Pesquise "mailchimp" e selecione esta ação: **MailChimp – Adicionar membro à lista**

   ![Selecionar "MailChimp - Adicionar membro à lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member.png)

3. Se você for solicitado a entrar na conta do MailChimp, entre com suas credenciais do MailChimp.

4. Forneça informações para essa ação, como mostrado e descrito aqui:

   ![Fornecer informações para "Adicionar membros à lista"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-mailchimp-add-member-settings.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **ID da lista** | test-members-ML | O nome da lista de endereçamento do MailChimp | 
   | **Status** | subscribed | O status da assinatura para o novo membro. Para saber mais, confira <a href="https://developer.mailchimp.com/documentation/mailchimp/guides/manage-subscribers-with-the-mailchimp-api/" target="_blank">Gerenciar assinantes com a API do MailChimp</a>. | 
   | **Endereço de Email** | <*new-member-email-address*> | Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione **De** em **Quando um novo email é recebido**, que transmite o endereço de email para o novo membro. 
   |  |  |  | 

5. Salve seu aplicativo lógico.

Em seguida, adicione uma condição para que você possa verificar se o novo membro ingressou na lista de endereçamento com êxito. Dessa forma, seu aplicativo lógico notifica se essa operação for bem-sucedida ou apresentou falha.

## <a name="check-for-success-or-failure"></a>Verificar êxito ou falha

1. Na ramificação **If true** da ação **Adicionar membro à lista**, escolha **Mais...**   >  **Adicionar uma condição**.

2. Renomeie a condição com esta descrição:```If add member succeeded```

3. Crie uma condição que verifica se o membro aprovado teve êxito ou falha ao ingressar na lista de endereçamento:

   1. Dentro da condição, clique na caixa **Escolha um valor**, que está à esquerda (exibição de navegador ampla) ou na parte superior (exibição de navegador estreita).
   Na lista de parâmetros ou na lista de conteúdo dinâmico, selecione o campo **Status** em **Adicionar membro à lista**.

      Por exemplo, se você estiver trabalhando na exibição ampla, sua condição será semelhante a este exemplo:

      ![Em "Adicionar membro à lista", selecionar "Status"](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member.png)

   2. Na caixa de operador de comparação, selecione este operador: **é igual a**

   3. Na caixa à direita (exibição ampla) ou na parte inferior (exibição estreita) **Escolha um valor**, insira este limite:```subscribed```

   Ao concluir, a sua condição será semelhante a este exemplo:

   ![Condição concluída](./media/tutorial-process-mailing-list-subscriptions-workflow/build-condition-check-added-member-2.png)

Em seguida, configure os emails para envio quando o membro aprovado tem êxito ou falha ao ingressar na lista de endereçamento.

## <a name="send-email-if-member-added"></a>Enviar email se o membro foi adicionado

1. Na ramificação **If true** para a condição **Se adicionar membro foi bem-sucedido**, escolha **Adicionar uma ação**.

   ![Na ramificação "If true" para a condição, escolha "Adicionar uma ação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success.png)

2. Pesquise "envio de email do outlook" e selecione a ação:  **< *seu-provedor-de-email*> - Enviar um email**

   ![Adicionar ação para "Enviar um email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-2.png)

3. Renomeie a ação com esta descrição:```Send email on success```

4. Forneça informações para essa ação, como mostrado e descrito:

   ![Fornecer informações de email de êxito](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-success-settings.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Para** | <*your-email-address*> | O endereço de email para onde enviar o email de êxito. Para fins de teste, você pode usar seu próprio endereço de email. | 
   | **Assunto** | <*subject-for-success-email*> | O assunto do email de êxito. Para este tutorial, insira este texto e selecione o campo especificado em **Adicionar membros à lista** na lista de parâmetros ou na lista dinâmica de conteúdo: <p>“Sucesso! Membro adicionado a 'test-members-ML': **Endereço de Email**" | 
   | **Corpo** | <*body-for-success-email*> | O conteúdo do corpo do email de êxito. Para este tutorial, insira este texto e selecione os campos especificados em **Adicionar membros à lista** na lista de parâmetros ou na lista dinâmica de conteúdo:  <p>"O novo membro entrou para 'test-members-ML': **Endereço de Email**"</br>"Status de aceitação do membro: **Status**" | 
   | | | | 

5. Salve seu aplicativo lógico.

## <a name="send-email-if-member-not-added"></a>Enviar email se membro não foi adicionado

1. Na ramificação **If false** para a condição **Se adicionar membro foi bem-sucedido**, escolha **Adicionar uma ação**.

   ![Na ramificação "If false" para a condição, escolha "Adicionar uma ação"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed.png)

2. Pesquise "envio de email do outlook" e selecione a ação:  **< *seu-provedor-de-email*> - Enviar um email**

   ![Adicionar ação para "Enviar um email"](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-2.png)

3. Renomeie a ação com esta descrição:```Send email on failure```

4. Forneça informações para essa ação, como mostrado e descrito aqui:

   ![Fornecer informações de email de falha](./media/tutorial-process-mailing-list-subscriptions-workflow/add-action-email-failed-settings.png)

   | Configuração | Valor | DESCRIÇÃO | 
   | ------- | ----- | ----------- | 
   | **Para** | <*your-email-address*> | O endereço de email para onde enviar o email de falha. Para fins de teste, você pode usar seu próprio endereço de email. | 
   | **Assunto** | <*subject-for-failure-email*> | O assunto do email de falha. Para este tutorial, insira este texto e selecione o campo especificado em **Adicionar membros à lista** na lista de parâmetros ou na lista dinâmica de conteúdo: <p>"Falha; membro não adicionado a 'test-members-ML': **Endereço de Email**" | 
   | **Corpo** | <*body-for-failure-email*> | O conteúdo do corpo do email de falha. Para este tutorial, digite este texto: <p>"O membro talvez já exista. Verifique sua conta do MailChimp." | 
   | | | | 

5. Salve seu aplicativo lógico. 

Em seguida, teste seu aplicativo lógico, que agora se parece com este exemplo:

 ![Aplicativo lógico concluído](./media/tutorial-process-mailing-list-subscriptions-workflow/tutorial-complete.png)

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

1. Envie uma solicitação de email a si mesmo para ingressar na lista de endereçamento.
Aguarde até que a solicitação seja exibida na caixa de entrada.

3. Para executar manualmente o aplicativo lógico, escolha **Executar** na barra de ferramentas do designer. 

   Se seu email tiver uma entidade que corresponde ao filtro de assunto do disparador, o aplicativo lógico envia um email para aprovar a solicitação de assinatura.

4. No email de aprovação, escolha **Aprovar**.

5. Se o endereço de email do assinante não existe na sua lista de endereçamento, o aplicativo lógico adiciona o endereço de email dessa pessoa e envia um email como neste exemplo:

   ![Email de êxito](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-success.png)

   Se seu aplicativo lógico não consegue adicionar o assinante, você recebe um email como neste exemplo:

   ![Email de falha](./media/tutorial-process-mailing-list-subscriptions-workflow/add-member-failed.png)

   Se você não receber nenhum email, verifique a pasta de Lixo eletrônico. 
   O filtro de lixo de email pode redirecionar esses tipos de mensagens. 
   Caso contrário, se você não tiver certeza de que seu aplicativo lógico foi executado corretamente, confira [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Parabéns, você criou e executou um aplicativo lógico que integra informações entre serviços Azure, Microsoft e outros aplicativos SaaS.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados. No menu principal do Azure, vá para **Grupos de recursos** e selecione o grupo de recursos do seu aplicativo lógico. Selecione **Excluir grupo de recursos**. Insira o nome do grupo de recursos como confirmação e escolha **Excluir**.

![“Visão Geral” > “Excluir grupo de recursos”.](./media/tutorial-process-mailing-list-subscriptions-workflow/delete-resource-group.png)

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo lógico que gerencia as aprovações para solicitações de lista de endereçamento. Agora, aprenda a criar um aplicativo lógico que processa e armazena anexos de email integrando serviços do Azure, como o Armazenamento do Azure e o Azure Functions.

> [!div class="nextstepaction"]
> [Processar anexos do email](../logic-apps/tutorial-process-email-attachments-workflow.md)
