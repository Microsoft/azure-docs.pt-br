---
title: Cenário de orçamento de gerenciamento de custos e cobrança do Azure | Microsoft Docs
description: Saiba como usar a automação do Azure para desligar as VMs com base nos limites do orçamento específico.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: db93f546-6b56-4b51-960d-1a5bf0274fc8
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 03/13/2019
ms.author: banders
ms.openlocfilehash: 37f129526cb184a2eeee9e36028e8f00b5bbc247
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443472"
---
# <a name="manage-costs-with-azure-budgets"></a>Gerenciar os custos com os Orçamentos do Azure

Controle de custo é um componente crítico para maximizar o valor de seu investimento na nuvem. Há vários cenários em que visibilidade de custo, relatórios e orquestração com base em custo são cruciais para a continuidade das operações de negócios. As [APIs de Gerenciamento de Custos do Azure](https://docs.microsoft.com/rest/api/consumption/) fornecem um conjunto de APIs para dar suporte a cada um desses cenários. As APIs fornecem detalhes de uso, permitindo que você exiba os custos granulares em nível de instância.

Orçamentos normalmente são usados como parte do controle de custos. O escopo de orçamentos pode ser definido no Azure. Por exemplo, você pode restringir sua exibição de orçamento com base em assinatura, grupos de recursos ou coleção de recursos. Além de usar a API de orçamentos para notificá-lo por email quando um limite de orçamento for atingido, você pode usar [grupos de ação do Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) para disparar um conjunto orquestrado de ações como resultado de um evento de orçamento.

Um cenário comum de orçamentos de um cliente executando uma carga de trabalho não crítica pode ocorrer quando ele deseja gerenciar em elação a um orçamento e também obter um custo previsível ao examinar a fatura mensal. Esse cenário requer alguma orquestração baseada em custo dos recursos que fazem parte do ambiente do Azure. Nesse cenário, um orçamento mensal de US$ 1.000 para a assinatura é definido. Além disso, os limites de notificação são definidos para disparar algumas orquestrações. Este cenário começa com um limite de custo de 80%, que interromperá todas as VMs no grupo de recursos **Opcional**. Em seguida, em um limite de custo de 100%, todas as instâncias VM serão interrompidas.
Para configurar esse cenário, você concluirá as seguintes ações seguindo as etapas apresentadas em cada seção deste tutorial.

Essas ações incluídas neste tutorial permitem que você:

- Crie um Runbook de Automação do Azure para interromper VMs usando webhooks.
- Crie um Aplicativo Lógico do Azure a ser disparado com base no valor de limite de orçamento e chame o runbook com os parâmetros corretos.
- Crie um Grupo de Ação do Azure Monitor que será configurado para disparar o Aplicativo Lógico do Azure quando o limite de orçamento for atingido.
- Crie o orçamento do Azure com os limites desejados e conecte-os ao grupo de ação.

## <a name="create-an-azure-automation-runbook"></a>Criar um Runbook de Automação do Azure

A [Automação do Azure](https://docs.microsoft.com/azure/automation/automation-intro) é um serviço que permite criar o script da maioria das tarefas de gerenciamento de recursos e executar essas tarefas de modo agendado ou sob demanda. Como parte desse cenário, você aprenderá a criar um [runbook de Automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types) que será usado para interromper as VMs. Você usará o runbook gráfico [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) na [galeria](https://docs.microsoft.com/azure/automation/automation-runbook-gallery) para criar esse cenário. Ao importar esse runbook para a sua conta do Azure e publicá-lo, você poderá interromper VMs quando um limite de orçamento for atingido.

### <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

1. Entre no [portal do Azure](https://portal.azure.com/) com suas credenciais da conta do Azure.
2. Clique no botão **Criar um recurso**, localizado no canto superior esquerdo do Azure.
3. Selecione **Ferramentas de Gerenciamento** > **Automação**.
   > [!NOTE]
   > Se você ainda não tiver uma conta do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/).
4. Insira as informações da sua conta. Para **Criar uma conta Executar como do Azure**, escolha **Sim** para habilitar automaticamente as configurações necessárias para simplificar a autenticação do Azure.
5. Ao concluir, clique em **Criar** para iniciar a implantação da conta de Automação.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importar o runbook Interromper VMs do Azure V2

Usando um [runbook de Automação do Azure](https://docs.microsoft.com/azure/automation/automation-runbook-types), importe o runbook gráfico [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) da galeria.

1.  Entre no [portal do Azure](https://portal.azure.com/) com suas credenciais da conta do Azure.
2.  Abra sua conta de Automação selecionando **Todos os serviços** > **Contas de Automação**. Em seguida, selecione sua Conta de Automação.
3.  Clique em **Galeria de runbooks** na seção **Automação de Processo**.
4.  Defina as **Fonte da Galeria** como **Script Center** e selecione **OK**.
5.  Localize e selecione o item da galeria [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) no portal do Azure.
6.  Clique no botão **Importar** para exibir a folha **Importar** e selecione **OK**. A folha de visão geral do runbook será exibida.
7.  Depois que o runbook tiver concluído o processo de importação, selecione **Editar** para exibir a opção de publicação e o editor de runbook gráfico.

    ![Azure – editar runbook gráfico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
8.  Clique no botão **Publicar** para publicar o runbook e, em seguida, selecione **Sim** quando solicitado. Quando você publica um runbook, substitui a versão Publicada existente pela versão de rascunho. Nesse caso, você não tem nenhuma versão publicada porque você criou o runbook.

    Para obter mais informações sobre como publicar um runbook, consulte [Criar um runbook gráfico](https://docs.microsoft.com/azure/automation/automation-first-runbook-graphical).

## <a name="create-webhooks-for-the-runbook"></a>Criar webhooks para o runbook

Usando o runbook gráfico [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b), você criará dois Webhooks para iniciar o runbook na Automação do Azure por meio de uma única solicitação HTTP. O primeiro webhook invocará o runbook em um limite de orçamento de 80% com o nome do grupo de recursos como um parâmetro, permitindo que as VMs opcionais sejam interrompidas. Em seguida, o segundo webhook invocará o runbook sem parâmetros (em 100%), o que interromperá todas as instâncias restantes da VM.

1. Na página **Runbooks** no [portal do Azure](https://portal.azure.com/), clique no runbook **StopAzureV2Vm** que exibe a folha de visão geral do runbook.
2. Clique em **Webhook** na parte superior da página para abrir a folha **Adicionar Webhook**.
3. Clique em **Criar novo webhook** para abrir a folha **Criar um novo webhook**.
4. Defina o **Nome** do Webhook como **Opcional**. A propriedade **Habilitado** deve ser **Sim**. O valor **Expira** não precisa ser alterado. Para obter mais informações sobre as propriedades do Webhook, veja [Detalhes de um webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#details-of-a-webhook).
5. Ao lado do valor de URL, clique no ícone de cópia para copiar a URL do webhook.
   > [!IMPORTANT]
   > Salve a URL do webhook chamada **Opcional** em um local seguro. Você usará a URL mais tarde neste tutorial. Por motivos de segurança, depois de criar o webhook, você não poderá exibir nem recuperar a URL novamente.
6. Clique em **OK** para criar o novo webhook.
7. Clique em **Configurar parâmetros e configurações de execução** para exibir o parâmetro de valores para o runbook.
   > [!NOTE]
   > Se o runbook tiver parâmetros obrigatórios, não será possível criar o webhook, a menos que os valores sejam fornecidos.
8. Clique em **OK** para aceitar os valores de parâmetro do webhook.
9. Clique em **Criar** para criar o webhook.
10. Então siga as etapas acima para criar um segundo webhook chamado **Concluir**.
    > [!IMPORTANT]
    > Salve ambas as URLs do webhook para uso posterior neste tutorial. Por motivos de segurança, depois de criar o webhook, você não poderá exibir nem recuperar a URL novamente.

Agora você deve ter dois webhooks configurados, cada um disponível usando as URLS salvas.

![Webhooks – opcional e completo](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Agora você concluiu a configuração da Automação do Azure. Você pode testar os webhooks com um simples teste Postman para validar que o webhook funciona. Em seguida, você deve criar o Aplicativo Lógico para orquestração.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Criar um Aplicativo Lógico do Azure para orquestração

Os Aplicativos Lógicos ajudam a criar, agendar e automatizar processos como fluxos de trabalho, para que você passa integrar aplicativos, dados, sistemas e serviços entre empresas ou organizações. Nesse cenário, o [Aplicativo Lógico](https://docs.microsoft.com/azure/logic-apps/) que você criou fará um pouco mais do que apenas chamar o webhook de automação que você criou.

Orçamentos podem ser configurados para disparar uma notificação quando um limite especificado for atingido. Você pode fornecer vários limites nos quais ser notificado e o Aplicativo Lógico demonstrará a capacidade de executar ações diferentes com base no limite atingido. Neste exemplo, você definirá a um cenário em que você obtém algumas notificações, sendo a primeira ao atingir 80% do orçamento e a segunda, ao atingir 100% do orçamento. O aplicativo lógico será usado para desligar todas as VMs no grupo de recursos. Primeiro, o limite **Opcional** será atingido em 80%, em seguida, o segundo limite será atingido, quando todas as VMs na assinatura serão desligadas.

Os aplicativos lógicos permitem que você forneça um esquema de amostra para o gatilho HTTP, mas exigem que você defina o cabeçalho **Content-Type**. Porque o grupo de ação não tem cabeçalhos personalizados para o webhook, você deve analisar a payload em uma etapa separada. Você usará a ação **Analisar** e fornecerá a ela uma carga de amostra.

### <a name="create-the-logic-app"></a>Criar o aplicativo lógico

O aplicativo lógico executará várias ações. A lista a seguir apresenta um conjunto de ações de alto nível que o aplicativo lógico executará:
- Reconhece quando uma solicitação HTTP é recebida
- Analisar os dados JSON passados para determinar o valor limite que foi atingido
- Use uma instrução condicional para verificar se o valor de limite atingiu 80% ou mais de um intervalo de orçamento, mas não é maior ou igual a 100%.
    - Se esse valor de limite for atingido, envie um HTTP POST usando o webhook chamado **Opcional**. Essa ação desligará as VMs no grupo "Opcional".
- Use uma instrução condicional para verificar se o valor de limite atingiu ou excedeu 100% do valor do orçamento.
    - Se o valor de limite tiver sido atingido, envie um HTTP POST usando o webhook chamado **Concluir**. Essa ação desligará todas as VMs restantes.

As etapas a seguir são necessários para criar o aplicativo lógico que executará as etapas acima:

1.  No [portal do Azure](https://portal.azure.com/), selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.

    ![Azure – Selecionar o recurso Aplicativo Lógico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
2.  Na folha **Criar aplicativo lógico**, forneça os detalhes necessários para criar seu aplicativo lógico, selecione **Fixar no painel** e clique em **Criar**.

    ![Azure – Criar um aplicativo lógico](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Depois que o Azure implantar o aplicativo lógico, o **Designer de Aplicativos Lógicos** será aberto e mostrará uma folha com um vídeo de introdução os gatilhos normalmente usados.

### <a name="add-a-trigger"></a>Adicione um gatilho

Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o disparador é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho. Ações são todas as etapas que ocorrem após o gatilho.

1.  Em **Modelos** da folha **Designer de Aplicativos Lógicos**, escolha **Aplicativo Lógico em Branco**.
2.  Adicione um [gatilho](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts) inserindo "solicitação http" na caixa de pesquisa **Designer de Aplicativos Lógicos** para localizar e selecionar o gatilho chamado **Solicitação – Quando uma solicitação HTTP é recebida**.

    ![Azure – Aplicativo lógico – Gatilho HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
3.  Selecione **Nova etapa** > **Adicionar uma ação**.

    ![Azure – Nova etapa – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
4.  Pesquise "analisar JSON" na caixa de pesquisa **Designer de Aplicativos Lógicos** para localizar e selecionar a **ação** [Operações de Dados – Analisar JSON](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview#logic-app-concepts).

    ![Azure – Aplicativo lógico – Adicionar ação de analisar JSON](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
5.  Insira "Carga" como o nome do **Conteúdo** da carga de Analisar JSON ou use a marca "Body" do conteúdo dinâmico.
6.  Selecione a opção **Usar payload de amostra para gerar esquema** na caixa **Analisar JSON**.

    ![Azure – Aplicativo lógico – Usar dados JSON de amostra para gerar o esquema](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
7.  Cole a seguinte payload de amostra JSON na caixa de texto: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`

    A caixa de texto será exibida da seguinte maneira:

    ![Azure – Aplicativo lógico – Payload JSON de amostra](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
8.  Clique em **Concluído**.

### <a name="add-the-first-conditional-action"></a>Adicionar a primeira ação condicional

Use uma instrução condicional para verificar se o valor de limite atingiu 80% ou mais de um intervalo de orçamento, mas não é maior ou igual a 100%. Se esse valor de limite for atingido, envie um HTTP POST usando o webhook chamado **Opcional**. Essa ação desligará as VMs no grupo **Opcional**.

1.  Selecione **Nova etapa** > **Adicionar uma condição**.

    ![Azure – Aplicativo lógico – Adicionar uma condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
2.  Na caixa **Condição**, clique na caixa de texto que contém **Escolha um valor** para exibir uma lista de valores disponíveis.

    ![Azure – Aplicativo lógico – Caixa de condição do Azure](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)

3.  Clique em **Expressão** na parte superior da lista e insira a expressão a seguir no editor de expressão: `float()`

    ![Azure – Aplicativo lógico – Expressão float](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)

4.  Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Selecione **é maior ou igual a** na caixa suspensa da **Condição**.
7.  Na caixa **Escolher um valor** da condição, insira `.8`.

    ![Azure – Aplicativo lógico – Expressão float com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)

8.  Clique em **Adicionar** > **Adicionar linha** na caixa Condição para adicionar uma parte adicional da condição.
9.  Na caixa **Condição**, clique na caixa de texto contendo **Escolher um valor**.
10. Clique em **Expressão** na parte superior da lista e insira a expressão a seguir no editor de expressão: `float()`
11. Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.
12. Selecione **OK** para definir a expressão.
13. Selecione **é menor que** na caixa suspensa da **Condição**.
14. Na caixa **Escolher um valor** da condição, insira `1`.

    ![Azure – Aplicativo lógico – Expressão float com um valor](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)

15. Na caixa **Se true**, selecione **Adicionar uma ação**. Você adicionará uma ação HTTP POST que desligará as VMs opcionais.

    ![Azure – Aplicativo lógico – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)

16. Insira **HTTP** para pesquisar a ação HTTP e selecione a ação **HTTP – HTTP**.

    ![Azure – Aplicativo lógico – Ação de adicionar HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)

17. Selecione **Post** como o para o valor **Método**.
18. Insira a URL para o webhook chamado **Opcional** que você criou neste tutorial como o valor do **Uri**.

    ![Azure – Aplicativo lógico – URI de ação HTTP](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)

19. Selecione **Adicionar uma ação** na caixa **Se true**. Você adicionará uma ação de email que enviará um email notificando o destinatário de que as VMs opcionais foram desligadas.
20. Pesquise "enviar email" e selecione uma ação de *enviar email* de acordo com o serviço de email que você usa.

    ![Azure – Aplicativo lógico – Ação de enviar email](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Para contas pessoais da Microsoft, selecione **Outlook.com**. Para as contas corporativas ou de estudante do Azure, selecione o **Outlook do Office 365**. Se você ainda não tiver uma conexão, será solicitado a entrar na sua conta de email. Os Aplicativos Lógicos criam uma conexão à sua conta de email.

    Você precisará permitir que o Aplicativo Lógico acesse suas informações de email.

    ![Azure – Aplicativo lógico – Aviso de acesso](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)

21. Adicione o texto **Para**, **Assunto** e **Corpo** para o email que notifica o destinatário de que as VMs opcionais foram sido desligadas. Use o conteúdo dinâmico **BudgetName** e **NotificationThresholdAmount** para preencher os campos de assunto e corpo.

    ![Azure – Aplicativo lógico – Detalhes de email](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Adicionar a segunda ação condicional

Use uma instrução condicional para verificar se o valor de limite atingiu ou excedeu 100% do valor do orçamento. Se o valor de limite tiver sido atingido, envie um HTTP POST usando o webhook chamado **Concluir**. Essa ação desligará todas as VMs restantes.

1.  Selecione **Nova etapa** > **Adicionar uma Condição**.

    ![Azure – Aplicativo lógico – Adicionar ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)

2.  Na caixa **Condição**, clique na caixa de texto que contém **Escolha um valor** para exibir uma lista de valores disponíveis.
3.  Clique em **Expressão** na parte superior da lista e insira a expressão a seguir no editor de expressão: `float()`
4.  Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.

    A expressão será a seguinte:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`

5.  Selecione **OK** para definir a expressão.
6.  Selecione **é maior ou igual a** na caixa suspensa da **Condição**.
7.  Na caixa **Escolher um valor** da condição, insira `1`.

    ![Azure – Aplicativo lógico – Definir valor da condição](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)

8.  Na caixa **Se true**, selecione **Adicionar uma ação**. Você adicionará uma ação HTTP POST que desligará todas as VMs restantes.

    ![Azure – Aplicativo lógico – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)

9.  Insira **HTTP** para pesquisar a ação HTTP e selecione a ação **HTTP – HTTP**.
10. Selecione **Post** como o para o valor **Método**.
11. Insira a URL para o webhook chamado **Completo** que você criou neste tutorial como o valor do **Uri**.

    ![Azure – Aplicativo lógico – Adicionar uma ação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)

12. Selecione **Adicionar uma ação** na caixa **Se true**. Você adicionará uma ação de email que enviará um email notificando o destinatário de que as VMs restantes foram desligadas.
13. Pesquise "enviar email" e selecione uma ação de *enviar email* de acordo com o serviço de email que você usa.
14. Adicione o texto **Para**, **Assunto** e **Corpo** para o email que notifica o destinatário de que as VMs opcionais foram sido desligadas. Use o conteúdo dinâmico **BudgetName** e **NotificationThresholdAmount** para preencher os campos de assunto e corpo.

    ![Azure – Aplicativo lógico – Enviar detalhes de email](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)

15. Clique em **Salvar** na parte superior da folha **Designer de Aplicativo Lógico**.

### <a name="logic-app-summary"></a>Resumo do Aplicativo Lógico

Esta será a aparência do seu Aplicativo Lógico quando você terminar. No mais básico dos cenários em que você não precisa de nenhuma orquestração com base no limite, você pode chamar diretamente o script de automação do **Monitor** e ignorar a etapa de **Aplicativo Lógico**.

   ![Azure – Aplicativo lógico – Exibição completa](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Quando você salvou seu aplicativo lógico, foi gerada uma URL que você poderá chamar. Você usará essa URL na próxima seção deste tutorial.

## <a name="create-an-azure-monitor-action-group"></a>Criar um grupo de ação do Azure Monitor

Um grupo de ação é uma coleção de preferências de notificação que você define. Quando um alerta é disparado, um grupo de ação específico pode receber o alerta sendo notificado. Um alerta do Azure gera proativamente uma notificação com base em condições específicas e oferece a oportunidade de agir. Um alerta pode usar dados de várias fontes, incluindo métricas e logs.

Grupos de ação são o único ponto de extremidade que você integrará ao seu orçamento. Você pode configurar notificações em vários canais, mas, para este cenário, você se concentrará no Aplicativo Lógico criado anteriormente neste tutorial.

### <a name="create-an-action-group-in-azure-monitor"></a>Criar um grupo de ações no Azure Monitor

Ao criar o grupo de ações, você apontará para o Aplicativo Lógico criado anteriormente neste tutorial.

1.  Se você não ainda tiver se conectado ao [portal do Azure](https://portal.azure.com/), faça isso e selecione **Todos os serviços** > **Monitor**.
2.  Selecione **Grupos de ações** na seção **Configuração**.
3.  Selecione **Adicionar um grupo de ações** na folha **Grupos de ações**.
4.  Adicione e verifique os itens a seguir:
    - Nome do grupo de ações
    - Nome curto
    - Assinatura
    - Grupo de recursos

    ![Azure – Aplicativo lógico – Adicionar um grupo de ações](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)

5.  No painel **Adicionar grupo de ações**, adicione uma ação LogicApp. Dê à ação o nome de **Budget-BudgetLA**. No painel **Aplicativo Lógico**, selecione a **Assinatura** e o **Grupo de recursos**. Em seguida, selecione o **Aplicativo lógico** que você criou anteriormente neste tutorial.
6.  Clique em **OK** para definir o aplicativo lógico. Em seguida, selecione **OK** no painel **Adicionar grupo de ações** para criar o grupo de ações.

Você concluiu todos os componentes de suporte necessários para orquestrar seu orçamento de maneira eficaz. Agora, tudo o que você precisa fazer é criar o orçamento e configurá-lo para usar o grupo de ação que você criou.

## <a name="create-the-azure-budget"></a>Criar o orçamento do Azure

Você pode criar um orçamento no portal do Azure usando o [recurso de orçamento](../cost-management/tutorial-acm-create-budgets.md) no gerenciamento de custos. Ou, você pode criar um orçamento usando as APIs REST, os cmdlets do PowerShell ou usar a CLI. O procedimento a seguir usa a API REST. Antes de chamar a API REST, você precisará de um token de autorização. Para criar um token de autorização, você pode usar o projeto [ARMClient](https://github.com/projectkudu/ARMClient). O **ARMClient** permite que você se autentique com o Azure Resource Manager e obtenha um token para chamar as APIs.

### <a name="create-an-authentication-token"></a>Criar um token de autenticação

1.  Navegue até o projeto [ARMClient](https://github.com/projectkudu/ARMClient) no GitHub.
2.  Clone o repositório para obter uma cópia local.
3.  Abra o projeto no Visual Studio e compile-o.
4.  Depois que o build for bem-sucedido, o executável deverá estar na pasta *\bin\debug*.
5.  Execute o ARMClient. Abra um prompt de comando e navegue até a pasta *\bin\debug* da raiz do projeto.
6.  Para fazer logon e autenticar-se, insira o seguinte comando no prompt de comando:<br>
    `ARMClient login prod`
7.  Copie o **guid de assinatura** da saída.
8.  Para copiar um token de autorização para a área de transferência, insira o seguinte comando no prompt de comando e use a ID de assinatura copiada na etapa anterior: <br>
    `ARMClient token <subscription GUID from previous step>`

    Depois de concluir a etapa acima, você verá o seguinte:<br>
    **Token copiado com êxito para a área de transferência.**
9.  Salve o token a ser usado para as etapas na próxima seção deste tutorial.

### <a name="create-the-budget"></a>Criar o orçamento

Em seguida, você configurará **Postman** para criar um orçamento, chamando as APIs REST de Consumo do Azure. Postman é um ambiente de Desenvolvimento de API. Você importará os arquivos de coleção e ambiente para o Postman. A coleção contém definições agrupadas de solicitações HTTP que chamam as APIs REST de Consumo do Azure. O arquivo de ambiente contém variáveis que são usadas pela coleção.

1.  Baixe e abra o [Cliente REST do Postman](https://www.getpostman.com/) para executar as APIs REST.
2.  No Postman, crie uma nova solicitação.

    ![Postman – Criar uma nova solicitação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)

3.  Salve a nova solicitação como uma coleção, de modo que a nova solicitação não tenha nada.

    ![Postman – Salvar a nova solicitação](./media/billing-cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)

4.  Altere a solicitação de uma ação `Get` para `Put`.
5.  Modificar a URL a seguir substituindo `{subscriptionId}` pela **ID da Assinatura** que você usou na seção anterior deste tutorial. Além disso, modifique a URL para incluir "SampleBudget" como o valor para `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
6.  Selecione a guia **Cabeçalhos** no Postman.
7.  Adicione uma nova **Chave** chamada "Autorização".
8.  Defina o **Valor** para o token criado usando o ArmClient no final da última seção.
9.  Selecione a guia **Corpo** no Postman.
10. Selecione a opção de botão **bruto**.
11. Na caixa de texto, cole na definição de orçamento de exemplo abaixo, no entanto , você deve substituir os parâmetros SubscriptionId, **orcaname**e **actiongroupname** pela sua ID de assinatura, um nome exclusivo para seu orçamento e o nome do grupo de ação Você criou tanto na URL quanto no corpo da solicitação:

    ```
        {
            "properties": {
                "category": "Cost",
                "amount": 100.00,
                "timeGrain": "Monthly",
                "timePeriod": {
                "startDate": "2018-06-01T00:00:00Z",
                "endDate": "2018-10-31T00:00:00Z"
                },
                "filters": {
                },
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [
                    ],
                    "contactRoles": [
                    ],
                    "contactGroups": [
                    "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}
                    ]
                },
            "Actual_EqualTo_100_Percent": {
                    "operator": "EqualTo",
                    "threshold": 100,
                    "contactGroups": [
                "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
                    ]
                }
            }
        }
    ```
12. Pressione **Enviar** para enviar a solicitação.

Agora você tem todas as partes de que precisa chamar a [API de orçamentos](https://docs.microsoft.com/rest/api/consumption/budgets). A referência de API de orçamentos tem detalhes adicionais sobre as solicitações específicas, incluindo o seguinte:
    - **budgetName** – há suporte para vários orçamentos.  Os nomes de orçamento devem ser exclusivos.
    - **categoria** – deve ser **Custo** ou **Uso**. A API dá suporte a orçamentos de uso e custo.
    - **timeGrain** – um orçamento mensal, trimestral ou anual. O valor é redefinido no final do período.
    - **filtros** – Filtros permitem que você restrinja o orçamento para um conjunto específico de recursos no escopo selecionado. Por exemplo, um filtro pode ser uma coleção de grupos de recursos de um orçamento no nível de assinatura.
    - **notificações** – Determina os limites e os detalhes da notificação. Você pode configurar vários limites e fornecer um endereço de email ou um grupo de ações para receber uma notificação.

## <a name="summary"></a>Resumo

Seguindo este tutorial, você aprendeu:
- Como criar um Runbook de Automação do Azure para interromper VMs.
- Como criar um aplicativo lógico do Azure disparado com base nos valores de limite de orçamento e chamar o runbook relacionado com os parâmetros corretos.
- Como criar um Grupo de Ação do Azure Monitor configurado para disparar o Aplicativo Lógico do Azure quando o limite de orçamento é atingido.
- Como criar o orçamento do Azure com os limites desejados e conecte-lo ao grupo de ação.

Agora você tem um orçamento totalmente funcional para a sua assinatura que desligará suas VMs quando você atingir seus limites de orçamento configurados.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre cenários de cobrança do Azure, veja [Cenários de automação de gerenciamento de custos e cobrança](billing-cost-management-automation-scenarios.md).
