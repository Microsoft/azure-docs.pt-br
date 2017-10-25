---
title: "Criar seu primeiro fluxo de trabalho automatizado entre os sistemas de nuvem e os serviços de nuvem - Aplicativos Lógicos do Azure | Microsoft Docs"
description: "Automatizar processos de negócios e fluxos de trabalho para os cenários de integração de sistemas e de EAI (integração de aplicativos empresariais) criando e executando aplicativos lógicos"
author: ecfan
manager: anneta
editor: 
services: logic-apps
keywords: "fluxo de trabalho, aplicativos de nuvem, serviços de nuvem, processos de negócios, integração de sistemas, integração de aplicativos empresariais, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>Criar seu primeiro aplicativo lógico para automatizar fluxos de trabalho e processos por meio do portal do Azure

Sem precisar codificar, você pode integrar sistemas e serviços criando e executando fluxos de trabalho automatizados com [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md). Para mostrar como é fácil automatizar tarefas com um fluxo de trabalho, este tutorial cria um aplicativo lógico básico que verifica um feed RSS para novo conteúdo em um site e envia um email para cada novo item no feed. 

![Visão geral: o primeiro exemplo do aplicativo lógico](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo lógico em branco.
> * Adicionar um gatilho para iniciar o aplicativo lógico quando um item do feed RSS é publicado.
> * Adicionar uma ação para enviar email com detalhes sobre o item do feed RSS.
> * Executar e testar o aplicativo lógico.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura, poderá [iniciar com uma conta gratuita do Azure](https://azure.microsoft.com/free/). Caso contrário, você pode [inscrever-se para uma assinatura Pré-paga](https://azure.microsoft.com/pricing/purchase-options/).

* Uma conta de email de [qualquer provedor de email com suporte pelos Aplicativos Lógicos do Azure](../connectors/apis-list.md) para enviar notificações. Por exemplo, você pode usar o Outlook do Office 365, o Outlook.com, o Gmail ou outro provedor com suporte. Este tutorial usa o Office 365 Outlook.

  > [!TIP]
  > Se você tiver uma [conta da Microsoft](https://account.microsoft.com/account) pessoal, terá uma conta do Outlook.com. Caso contrário, se tiver uma conta corporativa ou de estudante do Azure, terá uma conta do Outlook do Office 365.

* Um link para o RSS feed de um site. Este exemplo usa o [RSS feed para as principais matérias do site CNN.com](http://rss.cnn.com/rss/cnn_topstories.rss): `http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1. Criar um aplicativo lógico em branco 

1. Entre no [portal do Azure](https://portal.azure.com "portal do Azure").

2. No menu principal do Azure, escolha **Novo** > **Enterprise Integration** > **Aplicativo Lógico**.

   ![Portal do Azure, Novo, Enterprise Integration, Aplicativo Lógico](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. Crie seu aplicativo lógico com as configurações especificadas na tabela a seguir.

   ![Defina os detalhes do aplicativo lógico](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Nome** | *nome-seu-aplicativo-lógico* | Forneça um nome exclusivo de aplicativo lógico. | 
   | **Assinatura** | *sua-assinatura-Azure* | Selecione a assinatura do Azure que você deseja usar. | 
   | **Grupo de recursos** | *seu-grupo-recursos-Azure* | Crie ou selecione um grupo de recursos do Azure, o que ajuda a organizar e gerenciar os recursos do Azure relevantes. | 
   | **Localidade** | *sua-região-Azure* | Selecione a região do datacenter para implantar seu aplicativo lógico. | 
   |||| 

4. Quando estiver pronto, selecione **Fixar no painel** e escolha **Criar**.

   Agora você criou um recurso do Azure para o seu aplicativo lógico. 
   Depois que o Azure implanta o aplicativo lógico, o designer de aplicativos lógicos mostra modelos para padrões comuns para que você possa começar o quanto antes.

   > [!NOTE] 
   > Quando você selecionar **Fixar no painel**, seu aplicativo lógico aparecerá no painel do Azure após a implantação e será aberto automaticamente no Designer de Aplicativos Lógicos. Se isso não acontecer, encontre e abra o aplicativo lógico manualmente.

5. Por enquanto, em **Modelos**, escolha **Aplicativo lógico em branco** para poder compilar seu aplicativo lógico do zero.

   ![Escolher o modelo de aplicativo lógico](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   O Designer de Aplicativos Lógicos agora mostra os [ *conectores* ](../connectors/apis-list.md) disponíveis e seus [ *gatilhos*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), que você usa para iniciar o fluxo de trabalho do aplicativo lógico.

   ![Gatilhos do aplicativo lógico](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2. Adicionar um gatilho para iniciar o fluxo de trabalho

Cada aplicativo lógico deve começar com um [ *gatilho*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts). O gatilho é disparado quando ocorre um evento específico ou quando novos dados atendem à condição que você definiu. O mecanismo dos Aplicativos Lógicos cria uma instância de aplicativo lógico para executar o fluxo de trabalho. Cada vez que o gatilho é acionado, o mecanismo cria outra instância separada que executa o fluxo de trabalho do aplicativo lógico.

1. Na caixa de pesquisa, digite "rss" como filtro. Selecione este gatilho: **RSS - Quando um item do feed é publicado** 

   ![Selecione o gatilho: "RSS - Quando um item do feed é publicado"](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. Insira o link do RSS feed do site que você deseja acompanhar, por exemplo, `http://rss.cnn.com/rss/cnn_topstories.rss`. Defina o intervalo e a frequência da recorrência. Neste exemplo, defina essas propriedades para verificar o feed todos os dias. 

   ![Configurar um gatilho com o RSS feed, frequência e intervalo](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. Salve seu trabalho agora. Clique em **Salvar** na barra de ferramentas do designer.
Para recolher e ocultar detalhes do gatilho, escolha a barra de título do gatilho.

   ![Salve seu aplicativo lógico](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   Seu aplicativo lógico agora está ativo, mas não fará nada além de verificar novos itens no RSS feed até que você adicione ações ao fluxo de trabalho. 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3. Adicionar uma ação que responde ao gatilho

Agora, adicione uma [ *ação*](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts), que é uma tarefa que executa o fluxo de trabalho do aplicativo lógico. Neste exemplo, adicione uma ação que envia email quando um novo item é exibido no feed RSS.

1. No Designer de Aplicativos Lógicos, no gatilho, escolha **+ Nova etapa** > **Adicionar uma ação**.

   ![Adicionar uma ação](./media/logic-apps-create-a-logic-app/add-new-action.png)

   O designer mostra os [conectores disponíveis](../connectors/apis-list.md) para que você possa selecionar uma ação para executar quando o gatilho disparar.

   ![Selecionar na lista de ações](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. Na caixa de pesquisa, insira "enviar email" como filtro. Com base no seu provedor de email, localize e selecione o conector correspondente. Em seguida, selecione a ação "enviar email" para o conector. Por exemplo: 

   * Por exemplo, se você tiver uma conta corporativa ou de estudante do Azure, selecione o conector Office 365 Outlook. 
   * Para contas pessoais da Microsoft, selecione o conector do Outlook.com. 
   * Para as contas do Gmail, selecione o conector do Gmail. 

   Vamos continuar com o conector do Office 365 Outlook. 
   Se você usar outro provedor, as etapas serão as mesmas, mas a IU pode aparecer diferente. 

   ![Selecione esta ação: "Outlook do Office 365 - Enviar um email"](./media/logic-apps-create-a-logic-app/actions.png)

3. Quando as credenciais forem solicitadas, entre com o nome de usuário e a senha de sua conta de email. 

4. Forneça os detalhes especificados na tabela e escolha os campos que você deseja incluir no email.

   | Para | Etapas | 
   | -- | ----- | 
   | Selecione os campos disponíveis para o fluxo de trabalho. | Clique dentro de uma caixa de edição para abrir a lista **Conteúdo dinâmico** ou escolha **Adicionar conteúdo dinâmico**. | 
   | Exiba outros campos disponíveis. | Na lista **Conteúdo dinâmico** , escolha **Ver mais** para cada seção.  | 
   | Adicione linhas em branco a uma caixa de edição. | Pressione SHIFT+ENTER. | 
   | Feche a lista **Conteúdo dinâmico**. | Escolha **Adicionar conteúdo dinâmico** novamente. | 
   ||| 

   ![Selecionar os dados a incluir no email](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | Configuração | Valor sugerido | Descrição | 
   | ------- | --------------- | ----------- | 
   | **Para** | *endereço-email-destinatário* | Insira o endereço de email do destinatário. Para fins de teste, você pode usar seu próprio endereço de email. | 
   | **Assunto** | Nova postagem da CNN: **título do feed** | Insira o conteúdo do assunto do email. <p>Para este tutorial, insira o texto sugerido e selecione o campo **Título do feed** do gatilho, que mostra o título do item do feed. | 
   | **Corpo** | Título: **título do feed** <p>Data de publicação: **link principal do feed** <p>Link: **link principal do feed** | Insira o conteúdo do corpo do email. <p>Para este tutorial, insira o texto sugerido e selecione estes campos de gatilho: <p>- **Título do feed**, que mostra o título do item do feed novamente </br>- **Feed publicado em**, que mostra a data e hora de publicação do item </br>- **Link principal do feed**, que mostra a URL para o item do feed | 
   |||| 

   > [!NOTE] 
   > Se você selecionar um campo que armazena uma matriz, o designer adicionará automaticamente um loop “For each” em torno da ação que faz referência à matriz. Dessa forma, seu aplicativo lógico executará essa ação em cada item da matriz.

5. Quando terminar, salve as alterações. Clique em **Salvar** na barra de ferramentas do designer.

   ![Aplicativo lógico concluído](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   Para testar o seu aplicativo lógico agora, siga para a próxima seção.

## <a name="4-run-and-test-your-workflow"></a>4. Executar e testar o fluxo de trabalho

1. Para executar manualmente o aplicativo lógico para teste, escolha **Executar** na barra de ferramentas do designer. Você também pode deixar seu aplicativo lógico verificar o RSS feed especificado com base no agendamento configurado.

   ![Executar o aplicativo lógico](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   Se seu aplicativo lógico encontrar novos itens, ele enviará um email que inclui os dados selecionados, por exemplo:

   ![Email enviado para o novo item do feed RSS](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   Se seu aplicativo lógico não localiza novos itens, o aplicativo lógico ignora a ação que envia email e aguarda o próximo intervalo antes de verificar novamente. 

2. Para analisar o histórico de gatilho e execução do seu aplicativo lógico, escolha **Visão geral** no menu do aplicativo lógico.
Para exibir mais detalhes sobre uma execução, escolha a linha para essa execução.

   ![Monitorar e verificar a execução de seu aplicativo lógico e o histórico de gatilhos](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > Se você não encontrar os dados esperados, escolha **Atualizar** na barra de ferramentas.

   Se a execução foi aprovada ou falhou, o modo de exibição de detalhes da execução mostrará as etapas com aprovação ou falha. 

   ![Exibir os detalhes de uma execução de aplicativo lógico](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   Para saber mais sobre o status de seu aplicativo lógico, o histórico de gatilhos e o histórico de execução, ou para diagnosticar seu aplicativo lógico, confira [Solucionar problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

3. Para exibir as entradas e saídas de cada etapa, expanda a etapa que você deseja examinar. Essas informações podem ajudar você a diagnosticar e depurar problemas em seu aplicativo lógico. Por exemplo:

   ![Exibir detalhes da etapa](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   Para saber mais, confira [Monitorar seu aplicativo lógico](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Parabéns, você acabou de criar e executar seu primeiro aplicativo lógico básico. Este exemplo mostra como é fácil criar fluxos de trabalho que automatizam processos para integrar sistemas e serviços, tudo sem código.

> [!NOTE]
> Seu aplicativo lógico continuará sendo executado até que você desative o aplicativo. Para desligar seu aplicativo temporariamente, avance para a próxima seção.

## <a name="clean-up-resources"></a>Limpar recursos

Este tutorial usa recursos e executa ações que podem gerar encargos na sua assinatura do Azure. Ao concluir o tutorial e testar, confira se desabilitou ou excluiu todos os recursos que não devem gerar encargos.

Você pode impedir a execução e o envio de email do aplicativo lógico sem excluir o aplicativo. No menu do aplicativo lógico, escolha **Visão geral**. Na barra de ferramentas, escolha **Desabilitar**.

![Como desabilitar o aplicativo lógico](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>Perguntas frequentes

**P:** o que mais posso fazer com meu aplicativo lógico? </br>
**R:** há outras tarefas que você pode executar, por exemplo, editar, exibir a definição de JSON, examinar o log de atividades ou excluir o aplicativo lógico.

Para encontrar outras tarefas de gerenciamento do aplicativo lógico, revise estes comandos no menu do aplicativo lógico:

| Tarefa | Etapas | 
| ---- | ----- | 
| Exibir o status, o histórico de execução e de gatilhos e as informações gerais de seu aplicativo | Escolha **Visão Geral**. | 
| Editar seu aplicativo | Escolha **Designer de Aplicativos Lógicos**. | 
| Exibir a definição JSON do fluxo de trabalho de seu aplicativo | Escolha **Exibir Código do Aplicativo Lógico**. | 
| Exibir as operações executadas em seu aplicativo lógico | Escolha **Log de atividades**. | 
| Exibir as antigas versões de seu aplicativo lógico | Escolha **Versões**. | 
| Desativar temporariamente seu aplicativo | Escolha **Visão Geral** e, na barra de ferramentas, escolha **Desabilitar**. | 
| Excluir seu aplicativo | Escolha **Visão Geral** e, na barra de ferramentas, escolha **Excluir**. Insira o nome do seu aplicativo lógico e escolha **Excluir**. | 
||| 

## <a name="get-support"></a>Obtenha suporte

* Para perguntas sobre os Aplicativos Lógicos do Azure, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Deseja ajudar a melhorar os Aplicativos Lógicos do Azure e os conectores? Vote ou envie ideias no [site User Voice dos Aplicativos Lógicos do Azure](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* [Criar seu aplicativo lógico com o Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md)
* [Adicionar condições e executar fluxos de trabalho](../logic-apps/logic-apps-use-logic-app-features.md)
*   [Modelos de aplicativos lógicos](../logic-apps/logic-apps-use-logic-app-templates.md)
* [Criar aplicativos lógicos a partir dos modelos do Azure Resource Manager](../logic-apps/logic-apps-arm-provision.md)
* [Medição do uso de Aplicativos Lógicos](../logic-apps/logic-apps-pricing.md) 
* [Preços dos Aplicativos Lógicos](https://azure.microsoft.com/pricing/details/logic-apps)
