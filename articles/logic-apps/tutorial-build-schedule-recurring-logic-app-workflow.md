---
title: Criar fluxos de trabalho de automação com base em agendamento com o Azure
description: Tutorial – Criar um fluxo de trabalho de automação recorrente e com base em agendamento que seja integrado aos serviços de nuvem usando os Aplicativos Lógicos do Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 0dab2f49043f3a59dd4f06633e3f0228fc39a424
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792085"
---
# <a name="tutorial-create-schedule-based-and-recurring-automation-workflows-with-azure-logic-apps"></a>Tutorial: criar fluxos de trabalho de automação recorrentes e com base em agendamento com os Aplicativos Lógicos do Azure

Este tutorial mostra como criar um exemplo de [aplicativo lógico](../logic-apps/logic-apps-overview.md) que automatize um fluxo de trabalho executado em um agendamento recorrente. Especificamente, esse aplicativo lógico de exemplo verifica o tempo de viagem, incluindo o tráfego, entre dois locais e é executado todas as manhãs dos dias da semana. Se o tempo excede um limite específico, o aplicativo lógico envia um email que inclui o tempo de viagem e o tempo extra necessário para chegar ao seu destino. O fluxo de trabalho inclui várias etapas, que começam com um gatilho baseado em agendamento seguido por uma ação do Bing Mapas, uma ação de operações de dados, uma ação de fluxo de controle e uma ação de notificação por email.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um aplicativo lógico em branco.
> * Adicionar um gatilho Recorrência que especifica a agenda para seu aplicativo lógico.
> * Adicionar uma ação de Mapas do Bing que obtém o tempo de viagem para uma rota.
> * Adicionar uma ação que cria uma variável, converte o tempo de viagem de segundos a minutos e armazena o resultado na variável.
> * Adicionar uma condição que compara o tempo de viagem em relação a um limite especificado.
> * Adicionar uma ação que enviará um email a você se o tempo de viagem exceder o limite.

Quando terminar, o aplicativo lógico ficará parecido com este fluxo de trabalho em alto nível:

![Captura de tela que mostra a visão geral de alto nível de um fluxo de trabalho de aplicativo lógico de exemplo.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-overview.png)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e uma assinatura do Azure. Se você não tem uma assinatura, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/).

* Uma conta de email de qualquer provedor de email compatível com os Aplicativos Lógicos, como o Outlook do Office 365, o Outlook.com ou o Gmail. Para outros provedores, [revise a lista de conectores aqui](/connectors/). Este início rápido usa o Outlook do Office 365 com uma conta corporativa ou de estudante. Se você usar uma conta de email diferente, as etapas gerais serão as mesmas, mas a interface do usuário será ligeiramente diferente.

  > [!IMPORTANT]
  > Se você quiser usar o conector do Gmail, somente as contas comerciais do G Suite poderão usar esse conector sem restrição nos aplicativos lógicos. Se você tiver uma conta de consumidor do Gmail, poderá usar esse conector somente com serviços específicos do Google aprovados ou poderá [criar um aplicativo cliente do Google para usar para autenticação com o conector do Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Para obter mais informações, confira [Políticas de privacidade e segurança de dados para os conectores do Google nos Aplicativos Lógicos do Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Para obter o tempo de viagem de uma rota, você precisará de uma chave de acesso para a API do Bing Mapas. Para obter essa chave, siga as etapas para saber [como obter uma chave do Bing Mapas](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key).

* Se o seu aplicativo lógico precisar se comunicar por um firewall que limita o tráfego a endereços IP específicos, esse firewall precisará permitir o acesso *tanto* para os endereços IP de [entrada](logic-apps-limits-and-config.md#inbound) quanto para os de [saída](logic-apps-limits-and-config.md#outbound) usados pelo runtime ou serviço de Aplicativos Lógicos na região do Azure em que seu aplicativo lógico existe. Se o seu aplicativo lógico também usar [conectores gerenciados](../connectors/managed.md), como o conector Outlook do Office 365 ou SQL, ou usar [conectores personalizados](/connectors/custom-connectors/), o firewall também precisará permitir o acesso para *todos* os [endereços IP de saída do conector gerenciado](logic-apps-limits-and-config.md#outbound) na região do Azure do seu aplicativo lógico.

## <a name="create-your-logic-app"></a>Criar seu aplicativo lógico

1. Entre no [portal do Azure](https://portal.azure.com) com suas credenciais da conta do Azure. Na página inicial do Azure, selecione **Criar um recurso**.

1. No menu do Azure Marketplace, selecione **Integração** > **Aplicativo Lógico**.

   ![Captura de tela que mostra o menu do Azure Marketplace com "Integração" e "Aplicativo Lógico" selecionados.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-new-logic-app-resource.png)

1. No painel **Aplicativo Lógico**, forneça as informações descritas aqui sobre o aplicativo lógico que você deseja criar.

   ![Captura de tela que mostra o painel de criação do Aplicativo Lógico e as informações a serem fornecidas para o novo aplicativo lógico.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-logic-app-settings.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Assinatura** | <*Azure-subscription-name*> | Seu nome da assinatura do Azure. Este exemplo usa o `Pay-As-You-Go`. |
   | **Grupo de recursos** | LA-TravelTime-RG | O nome do [grupo de recursos do Azure](../azure-resource-manager/management/overview.md), usado para organizar os recursos relacionados. Esse exemplo cria um grupo de recursos chamado `LA-TravelTime-RG`. |
   | **Nome** | LA-TravelTime | O nome do seu aplicativo lógico, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`), parênteses (`(`, `)`) e pontos (`.`). Este exemplo usa o `LA-TravelTime`. |
   | **Localidade** | Oeste dos EUA | A região em que as informações de seu aplicativo lógico serão armazenadas. Este exemplo usa o `West US`. |
   | **Log Analytics** | Desativado | Mantenha a configuração **Desligado** para o log de diagnósticos. |
   ||||

1. Quando terminar, selecione **Review + create**. Depois que o Azure validar as informações sobre seu aplicativo lógico, selecione **Criar**.

1. Após o Azure implantar o aplicativo, selecione **Ir para o recurso**.

   O Azure abre o painel de seleção de modelo dos Aplicativos Lógicos, que mostra um vídeo de introdução, gatilhos comumente usados e padrões de modelo de aplicativo lógico.

1. Role para baixo após o vídeo e as seções de gatilhos comuns até a seção **Modelos** e selecione **Aplicativo Lógico em Branco**.

   ![Captura de tela que mostra o painel de seleção de modelo dos Aplicativos Lógicos com "Aplicativo Lógico em Branco" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-logic-app-template.png)

Em seguida, adicione o [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) Recorrência, que executa o fluxo de trabalho com base em um agendamento especificado. Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando novos dados atendem a uma condição específica. Para saber mais, confira [Criar seu primeiro aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-the-recurrence-trigger"></a>Adicionar gatilho Recorrência

1. Na caixa de pesquisa de Designer de Aplicativos Lógicos, insira `recurrence` e selecione o gatilho chamado **Recorrência**.

   ![Captura de tela que mostra a caixa de pesquisa de Designer de Aplicativos Lógicos que contém o termo de pesquisa "recorrência" e, na lista "Gatilhos", o gatilho "Recorrência" aparece selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-schedule-recurrence-trigger.png)

1. Na forma **Recorrência**, selecione o botão de **reticências** ( **...** ) e, em seguida, **Renomear**. Renomeie o gatilho com esta descrição:`Check travel time every weekday morning`

   ![Captura de tela que mostra o botão de reticências selecionado, a lista "Configurações" aberta e o comando "Renomear" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/rename-recurrence-schedule-trigger.png)

1. Dentro do gatilho, altere estas propriedades conforme descrito e mostrado aqui.

   ![Captura de tela que mostra as alterações no intervalo e na frequência do gatilho.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/change-interval-frequency.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Intervalo** | Sim | 1 | O número de intervalos de espera entre as verificações |
   | **Frequência** | Sim | Semana | A unidade de tempo a ser usada para a recorrência |
   |||||

1. Em **Intervalo** e **frequência**, abra a lista **Adicionar novo parâmetro** e selecione essas propriedades para adicionar ao gatilho.

   * **Nestes dias**
   * **A estas horas**
   * **A estes minutos**

   ![Captura de tela que mostra a lista "Adicionar novo parâmetro" aberta e as seguintes propriedades selecionadas: "Nos seguintes dias", "Nestas horas" e "Nestes minutos".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-trigger-properties.png)

1. Agora defina os valores das propriedades adicionais, conforme mostrado e descrito aqui.

   ![Captura de tela que mostra as propriedades adicionais definidas para os valores, conforme descrito na tabela a seguir.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/recurrence-trigger-property-values.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nestes dias** | Segunda-feira, terça-feira, quarta-feira, quinta-feira, sexta-feira | Essa configuração está disponível somente quando você define a **Frequência** como **Semana**. |
   | **A estas horas** | 7,8,9 | Essa configuração está disponível somente quando você define a **Frequência** como **Semana** ou **Dia**. Para essa recorrência, selecione as horas do dia. Esse exemplo é executado nas marcas de horas `7`,`8` e `9`. |
   | **A estes minutos** | 0,15,30,45 | Essa configuração está disponível somente quando você define a **Frequência** como **Semana** ou **Dia**. Para essa recorrência, selecione os minutos do dia. Esse exemplo é executado a cada 15 minutos, começando na marca da hora zero. |
   ||||

   Esse gatilho é acionado todos os dias da semana, a cada 15 minutos, começando às 7h e terminando às 9h45. A caixa **Visualização** mostra a agenda de recorrência. Para saber mais, confira [Agendar tarefas e fluxos de trabalho](../connectors/connectors-native-recurrence.md) e [Gatilhos e ações de fluxo de trabalho](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger).

1. Por enquanto, para ocultar os detalhes do gatilho, recolha a forma clicando na barra de título dela.

   ![Captura de tela que mostra a forma do gatilho recolhida.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/collapse-trigger-shape.png)

1. Salve seu aplicativo lógico. Selecione **Salvar** na barra de ferramentas do designer.

Seu aplicativo lógico agora está ativo no portal do Azure, mas não faz nada além de ser executado com base no agendamento especificado. Portanto, adicione uma ação que responde quando o gatilho é acionado.

## <a name="get-the-travel-time-for-a-route"></a>Obter o tempo de viagem para uma rota

Agora que você tem um gatilho, adicione uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) que obtém o tempo de viagem entre dois lugares. Os Aplicativos Lógicos fornecem um conector para a API do Bing Mapas para que você possa obter essas informações facilmente. Antes de começar essa tarefa, verifique se você tem uma chave de API do Bing Mapas conforme descrito nos pré-requisitos deste tutorial.

1. No Designer de Aplicativos Lógicos, no gatilho Recorrência, selecione **Nova etapa**.

1. Em **Escolher uma operação**, selecione **Padrão**. Na caixa de pesquisa, insira `bing maps` e selecione a ação chamada **Obter rota**.

   ![Captura de tela que mostra a lista "Escolher uma operação" filtrada por ações do "Bing Mapas" e a ação "Obter rota" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-get-route-action.png)

1. Se você não tiver uma conexão do Bing Mapas, a criação será solicitada. Forneça os detalhes da conexão conforme mostrado e descrito, depois selecione **Criar**.

   ![Captura de tela que mostra a caixa de conexão do Bing Mapas com o nome de conexão especificado e a chave de API do Bing Mapas.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/create-maps-connection.png)

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome da conexão** | Sim | BingMapsConnection | Forneça um nome para a conexão. Este exemplo usa o `BingMapsConnection`. |
   | **Chave de API** | Sim | <*Chave de API do Bing Mapas*> | Insira a chave de API do Bing Mapas que você recebeu anteriormente. Se você não tiver uma chave do Bing Mapas, saiba [como obter uma chave](/bingmaps/getting-started/bing-maps-dev-center-help/getting-a-bing-maps-key). |
   |||||

1. Renomeie a ação com esta descrição: `Get route and travel time with traffic`.

1. Nessa ação, abra a lista **Adicionar novo parâmetro** e selecione estas propriedades.

   * **Otimizar**
   * **Unidades de distância**
   * **Modo de navegação**

   ![Captura de tela que mostra a ação "Obter rota..." ação com as propriedades "Otimizar", "Unidade de distância" e "Modo de viagem" selecionadas.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-bing-maps-action-properties.png) 

1. Agora, insira os valores para as propriedades, conforme mostrado e descrito aqui.

   ![Captura de tela que mostra valores de propriedade adicionais para a ação "Obter rota".](./media/tutorial-build-scheduled-recurring-logic-app-workflow/get-route-action-settings.png) 

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Localizador 1** | Sim | <*start-location*> | Origem da sua rota. Esse exemplo especifica um endereço inicial de exemplo. |
   | **Localizador 2** | Sim | <*end-location*> | Destino da sua rota. Esse exemplo especifica um endereço de destino de exemplo. |
   | **Otimizar** | Não | timeWithTraffic | Um parâmetro para otimizar a rota, como distância, viagem de tempo com tráfego atual e assim por diante. Selecione o valor do parâmetro, **timeWithTraffic**. |
   | **Unidades de distância** | Não | <*your-preference*> | A unidade de distância da rota. Esse exemplo usa **Milha** como a unidade. |
   | **Modo de navegação** | Não | Automóvel | O modo de viagem da rota. Selecione o modo **Automóvel**. |
   |||||

   Para saber mais sobre esses parâmetros e valores, confira [Calcular uma rota](/bingmaps/rest-services/routes/calculate-a-route).

1. Selecione **Salvar** na barra de ferramentas do designer.

Em seguida, crie uma variável para que você possa converter e armazenar o tempo de viagem atual como minutos em vez de segundos. Dessa forma, você pode evitar repetir a conversão e usar o valor mais facilmente em etapas posteriores. 

## <a name="create-a-variable-to-store-travel-time"></a>Criar uma variável para armazenar o tempo de viagem

Às vezes, convém executar operações em dados no fluxo de trabalho e, em seguida, usar os resultados em ações posteriores. Para salvar esses resultados e poder reutilizar ou fazer referência a eles facilmente, você pode criar variáveis que armazenam os resultados depois de processá-los. Você pode criar variáveis somente no nível superior no aplicativo lógico.

Por padrão, a ação **Obter rota** retorna o tempo de viagem atual com tráfego em segundos da propriedade **Tráfego de Duração da Viagem**. Ao converter e armazenar esse valor como minutos, você facilita a reutilização do valor posteriormente sem convertê-lo novamente.

1. No designer, na ação **Obter rota**, selecione **Nova etapa**.

1. Em **Escolher uma operação**, selecione **Interno**. Na caixa de pesquisa, insira `variables` e selecione a ação chamada **Inicializar variável**.

   ![Captura de tela que mostra a ação "Inicializar variável" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-initialize-variable-action.png)

1. Renomear a ação com esta descrição:`Create variable to store travel time`

1. Forneça estas informações para sua variável, conforme mostrado nesta tabela e nas etapas abaixo dela:

   | Propriedade | Obrigatório | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Nome** | Sim | travelTime | O nome da sua variável. Este exemplo usa o `travelTime`. |
   | **Tipo** | Sim | Integer | O tipo de dados da variável |
   | **Valor** | Não | Uma expressão que converte o tempo de viagem atual de segundos em minutos (confira as etapas abaixo desta tabela). | O valor inicial da variável |
   |||||

   1. Para criar a expressão da propriedade **Valor**, clique na caixa para que seja exibida a lista de conteúdo dinâmico. Se necessário, amplie o navegador até que seja exibida a lista dinâmica. Na lista de conteúdo dinâmico, selecione **Expressão**, que mostra o editor de expressão.

      ![Captura de tela que mostra a ação "Inicializar variável" com o cursor dentro da propriedade "Valor", que abre a lista de conteúdo dinâmico.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings.png)

      A lista de conteúdo dinâmico mostra as saídas de ações anteriores que estão disponíveis para você selecionar como entradas de ações subsequentes no fluxo de trabalho. A lista de conteúdo dinâmico inclui um editor de expressão que você pode usar para selecionar funções que executam operações em sua expressão. Esse editor de expressão está disponível apenas na lista de conteúdo dinâmico.

   1. No editor de expressão, digite esta expressão:`div(,60)`

      ![Captura de tela que mostra o editor de expressão com a expressão "div(,60)" inserida.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-2.png)

   1. Dentro da expressão, coloque o cursor entre o parêntese esquerdo ( **(** ) e a vírgula ( **,** ) e selecione **Conteúdo dinâmico**.

      ![Captura de tela que mostra onde colocar o cursor na expressão "div(,60)" com "Conteúdo dinâmico" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-3.png)

   1. Na lista de conteúdo dinâmico, selecione o valor da propriedade, **Tráfego de Duração da Viagem**.

      ![Captura de tela que mostra o valor da propriedade "Tráfego de Duração da Viagem" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-4.png)

   1. Depois que o valor da propriedade for resolvido dentro da expressão, selecione **OK**.

      ![Captura de tela que mostra o botão "OK" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-5.png)

      A propriedade **Valor** agora é exibida conforme mostrado aqui:

      ![Captura de tela que mostra a propriedade "Valor" com a expressão resolvida.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/initialize-variable-action-settings-6.png)

1. Salve seu aplicativo lógico.

Em seguida, adicione uma condição que verifica se o tempo de viagem atual é maior que um limite específico.

## <a name="compare-the-travel-time-with-limit"></a>Comparar o tempo de viagem com o limite

1. Na ação **Criar variável para armazenar o tempo de viagem**, selecione **Nova etapa**.

1. Em **Escolher uma operação**, selecione **Interno**. Na caixa de pesquisa, insira `condition`. E, na lista de ações, selecione a ação chamada **Condição**.

   ![Captura de tela que mostra a ação "Condição" selecionada](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-condition-action.png)

1. Renomeie a condição com esta descrição:`If travel time exceeds limit`

1. Crie uma condição que verifica se o valor da propriedade **travelTime** excede o limite especificado, como descrito e mostrado aqui:

   1. Clique dentro da caixa **Escolher um valor** no lado esquerdo da condição.

   1. Na lista de conteúdo dinâmico exibida, em **Variáveis**, selecione a propriedade chamada **travelTime**.

      ![Captura de tela que mostra a caixa "Escolher um valor" no lado esquerdo da condição com a lista de conteúdo dinâmico aberta e a propriedade "travelTime" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-left-side.png)

   1. Na caixa de comparação intermediária, selecione o operador chamado **é maior que**.

   1. Na caixa **Escolher um valor** à direita da condição, insira este limite: `15`

      Quando terminar, a condição será semelhante a este exemplo:

      ![Captura de tela que mostra a condição concluída para comparar o tempo de viagem com o limite especificado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/build-condition-check-travel-time.png)

1. Salve seu aplicativo lógico.

Em seguida, adicione a ação a ser executada quando o tempo de viagem excede seu limite.

## <a name="send-email-when-limit-exceeded"></a>Enviar email quando o limite é excedido

Agora, adicione uma ação que envia emails quando o tempo de viagem excede seu limite. Este email inclui o tempo de viagem atual e o tempo extra necessário para concluir a rota especificada.

1. No branch **Verdadeiro** da condição, selecione **Adicionar uma ação**.

1. Em **Escolher uma operação**, selecione **Padrão**. Na caixa de pesquisa, insira `send email`. A lista retorna muitos resultados, portanto, para filtrá-la, primeiro selecione o conector de email desejado.

   Por exemplo, se você tiver uma conta de email do Outlook, selecione o conector para seu tipo de conta:

   * Para as contas corporativas ou de estudante do Azure, selecione o **Outlook do Office 365**.
   * Para contas pessoais da Microsoft, selecione **Outlook.com**.

   Este exemplo continua selecionando o Outlook do Office 365.

   ![Captura de tela que mostra "Escolher uma lista de operações" com a categoria "Padrão" e o conector "Outlook do Office 365" selecionados.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/add-action-send-email.png)

1. Quando as ações do conector forem exibidas, selecione a ação que envia emails, por exemplo:

   ![Captura de tela que mostra a ação "Enviar um email" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-send-email-action.png)

1. Se você ainda não tiver uma conexão, entre e autentique o acesso à sua conta de email quando solicitado.

   Os Aplicativos Lógicos do Azure criam uma conexão à sua conta de email.

1. Renomeie a ação com esta descrição:`Send email with travel time`

1. Na propriedade **Para**, insira o endereço de email do destinatário. Para fins de teste, você pode usar seu endereço de email.

1. Para a propriedade **Assunto**, especifique o assunto do email e inclua a variável **travelTime** seguindo estas etapas:

   1. Insira o texto `Current travel time (minutes):` com um espaço à direita. Mantenha o cursor na caixa **Assunto** para que a lista de conteúdo dinâmico permaneça aberta.

   1. Na lista de conteúdo dinâmico, no título **Variáveis**, selecione **Ver mais** para que a variável chamada **travelTime** seja exibida.

      ![Captura de tela que mostra a lista de conteúdo dinâmico com a seção "Variáveis" e a opção "Ver mais" selecionadas.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-travelTime-variable.png)

      > [!NOTE]
      > A lista de conteúdo dinâmico não mostra automaticamente a variável **travelTime** porque a propriedade **Assunto** espera um valor de cadeia de caracteres, enquanto **travelTime** é um valor inteiro.

      ![Captura de tela que mostra a lista de conteúdo dinâmico com a variável "travelTime" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/select-travelTime-variable.png)

1. Na propriedade **Corpo**, especifique o conteúdo do corpo do email seguindo estas etapas:

   1. Insira o texto `Add extra travel time (minutes):` com um espaço à direita. Mantenha o cursor na caixa **Corpo** para que a lista de conteúdo dinâmico permaneça aberta.

   1. Na lista de conteúdo dinâmico, selecione **Expressão**, que mostra o editor de expressão.

      ![Captura de tela que mostra a lista de conteúdo dinâmico com a opção "Expressão" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings.png)

   1. No editor de expressão, digite `sub(,15)` para que você possa calcular o número de minutos que excedem o limite: 

      ![Captura de tela que mostra o editor de expressão com a expressão "sub(,15)" inserida.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-2.png)

   1. Dentro da expressão, coloque o cursor entre o parêntese esquerdo ( **(** ) e a vírgula ( **,** ) e selecione **Conteúdo dinâmico**.

      ![Captura de tela que mostra onde colocar o cursor na expressão "sub(,15)" com "Conteúdo dinâmico" selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-3.png)

   1. Em **Variáveis**, selecione **travelTime**.

      ![Captura de tela que mostra a lista de conteúdo dinâmico com a variável "travelTime" selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-4.png)

   1. Depois que a propriedade for resolvida dentro da expressão, selecione **OK**.

      ![Captura de tela que mostra a lista de conteúdo dinâmico e "OK" selecionados.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-5.png)

      A propriedade **Body** agora é exibida conforme mostrado aqui:

      ![Captura de tela que mostra a lista de conteúdo dinâmico com a expressão resolvida na propriedade "Corpo" da ação do email.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/send-email-body-settings-6.png)

1. Salve seu aplicativo lógico.

Em seguida, teste e execute o aplicativo lógico, que agora se parece com este exemplo:

![Captura de tela que mostra o exemplo de fluxo de trabalho do aplicativo lógico concluído](./media/tutorial-build-scheduled-recurring-logic-app-workflow/check-travel-time-finished.png)

## <a name="run-your-logic-app"></a>Executar seu aplicativo lógico

Para executar manualmente o aplicativo lógico, selecione **Executar** na barra de ferramentas do designer.

* Se o tempo de viagem atual permanecer abaixo do limite, o aplicativo lógico não fará nada e aguardará o próximo intervalo antes de verificar novamente. 

* Se o tempo de viagem atual exceder o limite, você receberá um email com o tempo de viagem atual e o número de minutos acima do limite. Aqui está um email de exemplo que seu aplicativo lógico envia:

  ![Captura de tela que mostra um email de exemplo que relata o tempo de viagem atual e o tempo de viagem extra que excede o limite especificado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/received-example-email-notification.png)

  > [!TIP]
  > Se você não receber nenhum email, verifique a pasta de Lixo eletrônico. O filtro de lixo de email pode redirecionar esses tipos de mensagens. Caso contrário, se você não tiver certeza de que seu aplicativo lógico foi executado corretamente, confira [Solução de problemas do aplicativo lógico](../logic-apps/logic-apps-diagnosing-failures.md).

Parabéns, você agora criou e executou um aplicativo lógico recorrente com base em agendamento. 

Para criar outros aplicativos lógicos que usam o gatilho **Recorrência**, confira estes modelos, que ficam disponíveis depois que você cria um aplicativo lógico:

* Receba lembretes diários.
* Exclua blobs do Azure mais antigos.
* Adicione uma mensagem a uma fila do Armazenamento do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Seu aplicativo lógico continuará sendo executado até que você o desabilite ou exclua. Quando você não precisar mais do aplicativo lógico de exemplo, exclua o grupo de recursos que contém o aplicativo lógico e os recursos relacionados.

1. Na caixa de pesquisa do portal do Azure, digite o nome do grupo de recursos que você criou. Nos resultados, em **Grupos de Recursos**, selecione o grupo de recursos.

   Este exemplo cria o grupo de recursos chamado `LA-TravelTime-RG`.

   ![Captura de tela que mostra a caixa de pesquisa do Azure com "la-travel-time-rg" inserido e **LA-TravelTime-RG** selecionado.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/find-resource-group.png)

   > [!TIP]
   > Se a página inicial do Azure mostrar o grupo de recursos em **Recursos recentes**, você poderá selecionar o grupo na página inicial.

1. No menu do grupo de recursos, verifique se a **Visão Geral** está selecionada. Na barra de ferramentas do painel **Visão Geral**, selecione **Excluir grupo de recursos**.

   ![Captura de tela que mostra o painel "Visão Geral" do grupo de recursos e, na barra de ferramentas do painel, a opção "Excluir grupo de recursos" está selecionada.](./media/tutorial-build-scheduled-recurring-logic-app-workflow/delete-resource-group.png)

1. No painel de confirmação exibido, insira o nome do grupo de recursos e selecione **Excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um aplicativo lógico que verifica o tráfego com base em um agendamento especificado (nas manhãs dos dias da semana) e executa uma ação (envia um email) quando o tempo de viagem excede um limite especificado. Agora, saiba como criar um aplicativo lógico que envia solicitações de lista de endereçamento para aprovação integrando serviços do Azure, serviços da Microsoft e outros aplicativos SaaS (Software como serviço).

> [!div class="nextstepaction"]
> [Gerenciar solicitações da lista de correspondência](../logic-apps/tutorial-process-mailing-list-subscriptions-workflow.md)
