---
title: Cenário de orçamento de gerenciamento de custos e cobrança do Azure
description: Saiba como usar a automação do Azure para desligar as VMs com base nos limites do orçamento específico.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: how-to
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: 1706fd69f58e4dee4a7ed963fb22cb63aec026fa
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593387"
---
# <a name="manage-costs-with-azure-budgets"></a>Gerenciar os custos com os Orçamentos do Azure

Controle de custo é um componente crítico para maximizar o valor de seu investimento na nuvem. Há vários cenários em que visibilidade de custo, relatórios e orquestração com base em custo são cruciais para a continuidade das operações de negócios. As [APIs de Gerenciamento de Custos do Azure](/rest/api/consumption/) fornecem um conjunto de APIs para dar suporte a cada um desses cenários. As APIs fornecem detalhes de uso, permitindo que você exiba os custos granulares em nível de instância.

Orçamentos normalmente são usados como parte do controle de custos. O escopo de orçamentos pode ser definido no Azure. Por exemplo, você pode restringir sua exibição de orçamento com base em assinatura, grupos de recursos ou coleção de recursos. Além de usar a API de orçamentos para receber uma notificação por email quando um limite de orçamento for atingido, você poderá usar [grupos de ações do Azure Monitor](../../azure-monitor/alerts/action-groups.md) para disparar um conjunto orquestrado de ações resultantes de um evento de orçamento.

Um cenário comum de orçamentos de um cliente executando uma carga de trabalho não crítica pode ocorrer quando ele deseja gerenciar em elação a um orçamento e também obter um custo previsível ao examinar a fatura mensal. Esse cenário requer alguma orquestração baseada em custo dos recursos que fazem parte do ambiente do Azure. Nesse cenário, um orçamento mensal de US$ 1.000 para a assinatura é definido. Além disso, os limites de notificação são definidos para disparar algumas orquestrações. Este cenário começa com um limite de custo de 80%, que interromperá todas as VMs no grupo de recursos **Opcional**. Em seguida, em um limite de custo de 100%, todas as instâncias VM serão interrompidas.

Para configurar esse cenário, você concluirá as ações a seguir usando as etapas apresentadas em cada seção deste tutorial.

Essas ações incluídas neste tutorial permitem que você:

- Crie um Runbook de Automação do Azure para interromper VMs usando webhooks.
- Crie um Aplicativo Lógico do Azure a ser disparado com base no valor de limite de orçamento e chame o runbook com os parâmetros corretos.
- Crie um Grupo de Ação do Azure Monitor que será configurado para disparar o Aplicativo Lógico do Azure quando o limite de orçamento for atingido.
- Crie o orçamento do Azure com os limites desejados e conecte-os ao grupo de ações.

## <a name="create-an-azure-automation-runbook"></a>Criar um Runbook de Automação do Azure

A [Automação do Azure](../../automation/automation-intro.md) é um serviço que permite criar o script da maioria das tarefas de gerenciamento de recursos e executar essas tarefas de modo agendado ou sob demanda. Como parte desse cenário, você criará um [runbook de Automação do Azure](../../automation/automation-runbook-types.md) que será usado para interromper as VMs. Você usará o runbook gráfico [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) da [galeria](../../automation/automation-runbook-gallery.md) para criar esse cenário. Ao importar esse runbook para a sua conta do Azure e publicá-lo, você poderá interromper as VMs quando um limite de orçamento for atingido.

### <a name="create-an-azure-automation-account"></a>Criar uma conta de Automação do Azure

1. Entre no [portal do Azure](https://portal.azure.com/) com suas credenciais da conta do Azure.
2. Selecione o botão **Criar um recurso**, localizado no canto superior esquerdo do Azure.
3. Selecione **Ferramentas de Gerenciamento** > **Automação**.
   > [!NOTE]
   > Se você ainda não tiver uma conta do Azure, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/).
4. Insira as informações da sua conta. Para **Criar uma conta Executar como do Azure**, escolha **Sim** para habilitar automaticamente as configurações necessárias para simplificar a autenticação do Azure.
5. Ao concluir, selecione **Criar** para iniciar a implantação da conta de Automação.

### <a name="import-the-stop-azure-v2-vms-runbook"></a>Importar o runbook Interromper VMs do Azure V2

Usando um [runbook de Automação do Azure](../../automation/automation-runbook-types.md), importe o runbook gráfico [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) da galeria.

1. Entre no [portal do Azure](https://portal.azure.com/) com suas credenciais da conta do Azure.
1. Abra sua conta de Automação selecionando **Todos os serviços** > **Contas de Automação**. Em seguida, selecione sua Conta de Automação.
1. Selecione **Galeria de runbooks** na seção **Automação de Processo**.
1. Defina as **Fonte da Galeria** como **Script Center** e selecione **OK**.
1. Localize e selecione o item da galeria [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b) no portal do Azure.
1. Selecione **Importar** para exibir a área **Importação** e selecione **OK**. A área de visão geral do runbook será exibida.
1. Depois que o runbook tiver concluído o processo de importação, selecione **Editar** para exibir a opção de publicação e o editor de runbook gráfico.  
    ![Azure – Editar runbook gráfico](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-01.png)
1. Selecione **Publicar** para publicar o runbook e, em seguida, **Sim** quando solicitado. Quando você publica um runbook, substitui a versão Publicada existente pela versão de rascunho. Nesse caso, você não tem nenhuma versão publicada porque você criou o runbook.
    Para obter mais informações sobre como publicar um runbook, consulte [Criar um runbook gráfico](../../automation/learn/automation-tutorial-runbook-graphical.md).

## <a name="create-webhooks-for-the-runbook"></a>Criar webhooks para o runbook

Usando o runbook gráfico [Interromper VMs do Azure V2](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-ARM-VMs-1ba96d5b), você criará dois webhooks para iniciar o runbook na Automação do Azure por meio de uma só solicitação HTTP. O primeiro webhook invocará o runbook em um limite de orçamento de 80% com o nome do grupo de recursos como parâmetro, permitindo que as VMs opcionais sejam interrompidas. Em seguida, o segundo webhook invocará o runbook sem parâmetros (em 100%), o que interromperá todas as instâncias restantes da VM.

1. Na página **Runbooks** do [portal do Azure](https://portal.azure.com/), selecione o runbook **StopAzureV2Vm** que exibe a área de visão geral do runbook.
1. Selecione **Webhook** na parte superior da página para abrir a área **Adicionar Webhook**.
1. Selecione **Criar webhook** para abrir a área **Criar um webhook**.
1. Defina o **Nome** do Webhook como **Opcional**. A propriedade **Habilitado** deve ser **Sim**. Você não precisa alterar o valor de **Expira em**. Para obter mais informações sobre as propriedades do Webhook, veja [Propriedades do webhook](../../automation/automation-webhooks.md#webhook-properties).
1. Ao lado do valor de URL, selecione o ícone de cópia para copiar a URL do webhook.
   > [!IMPORTANT]
   > Salve a URL do webhook chamada **Opcional** em um local seguro. Você usará a URL mais adiante neste tutorial. Por motivos de segurança, depois de criar o webhook, você não poderá exibir nem recuperar a URL novamente.
1. Selecione **OK** para criar o webhook.
1. Selecione **Configurar parâmetros e configurações de execução** para ver os valores de parâmetro do runbook.
   > [!NOTE]
   > Se o runbook tiver parâmetros obrigatórios, não será possível criar o webhook, a menos que os valores sejam fornecidos.
1. Selecione **OK** para aceitar os valores de parâmetro do webhook.
1. Selecione **Criar** para criar o webhook.
1. Então siga as etapas acima para criar um segundo webhook chamado **Concluir**.
    > [!IMPORTANT]
    > Salve ambas as URLs do webhook para uso posterior neste tutorial. Por motivos de segurança, depois de criar o webhook, você não poderá exibir nem recuperar a URL novamente.

Agora você deve ter dois webhooks configurados, cada um disponível usando as URLS salvas.

![Webhooks – opcional e completo](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-02.png)

Agora você concluiu a configuração da Automação do Azure. Você pode testar os webhooks com um simples teste Postman para validar que o webhook funciona. Em seguida, você deve criar o Aplicativo Lógico para orquestração.

## <a name="create-an-azure-logic-app-for-orchestration"></a>Criar um Aplicativo Lógico do Azure para orquestração

Os Aplicativos Lógicos ajudam você a criar, agendar e automatizar processos como fluxos de trabalho, de modo que você possa integrar aplicativos, dados, sistemas e serviços entre empresas ou organizações. Nesse cenário, o [Aplicativo Lógico](../../logic-apps/index.yml) que você criou fará um pouco mais do que apenas chamar o webhook de automação que você criou.

Orçamentos podem ser configurados para disparar uma notificação quando um limite especificado for atingido. Você pode fornecer vários limites nos quais ser notificado e o Aplicativo Lógico demonstrará a capacidade de executar ações diferentes com base no limite atingido. Neste exemplo, você vai configurar um cenário em que obterá duas notificações: a primeira ao atingir 80% do orçamento e a segunda ao atingir 100% do orçamento. O aplicativo lógico será usado para desligar todas as VMs no grupo de recursos. Primeiro, o limite **Opcional** será atingido em 80%, em seguida, o segundo limite será atingido, quando todas as VMs na assinatura serão desligadas.

Os aplicativos lógicos permitem que você forneça um esquema de amostra para o gatilho HTTP, mas exigem que você defina o cabeçalho **Content-Type**. Como o grupo de ações não tem cabeçalhos personalizados para o webhook, você precisará analisar o conteúdo em uma etapa separada. Você usará a ação **Analisar** e fornecerá a ela um conteúdo de exemplo.

### <a name="create-the-logic-app"></a>Criar o aplicativo lógico

O aplicativo lógico executará várias ações. A lista a seguir apresenta um conjunto de ações de alto nível que o aplicativo lógico executará:

- Reconhece quando uma solicitação HTTP é recebida
- Analisar os dados JSON passados para determinar o valor limite que foi atingido
- Use uma instrução condicional para verificar se o valor de limite atingiu 80% ou mais de um intervalo de orçamento, mas não é maior ou igual a 100%.
  - Se esse valor de limite for atingido, envie um HTTP POST usando o webhook chamado **Opcional**. Essa ação desligará as VMs no grupo "Opcional".
- Use uma instrução condicional para verificar se o valor de limite atingiu ou excedeu 100% do valor do orçamento.
  - Se o valor de limite tiver sido atingido, envie um HTTP POST usando o webhook chamado **Concluir**. Essa ação desligará todas as VMs restantes.

As etapas a seguir são necessários para criar o aplicativo lógico que executará as etapas acima:

1. No [portal do Azure](https://portal.azure.com/), selecione **Criar um recurso** > **Integração** > **Aplicativo Lógico**.  
    ![Azure – Selecionar o recurso de Aplicativo Lógico](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03.png)
1. Na área **Criar aplicativo lógico**, forneça os detalhes necessários para criar seu aplicativo lógico, selecione **Fixar no painel** e **Criar**.  
    ![Azure – Criar um aplicativo lógico](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-03a.png)

Depois que o Azure implantar o aplicativo lógico, o **Designer de Aplicativos Lógicos** será aberto e mostrará uma área com um vídeo de introdução e os gatilhos normalmente usados.

### <a name="add-a-trigger"></a>Adicionar um gatilho

Cada aplicativo lógico deve começar com um gatilho, que é disparado quando um evento específico ocorre ou quando uma condição específica é atendida. Cada vez que o disparador é acionado, o mecanismo de Aplicativos Lógicos cria uma instância de aplicativo lógico que inicia e executa o fluxo de trabalho. Ações são todas as etapas que ocorrem após o gatilho.

1. Em **Modelos** da área **Designer de Aplicativos Lógicos**, escolha **Aplicativo Lógico em Branco**.
1. Adicione um [gatilho](../../logic-apps/logic-apps-overview.md#logic-app-concepts) inserindo "solicitação http" na caixa de pesquisa **Designer de Aplicativos Lógicos** para localizar e selecionar o gatilho chamado **Solicitação – Quando uma solicitação HTTP é recebida**.  
    ![Azure – Aplicativo lógico – Gatilho HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-04.png)
1. Selecione **Nova etapa** > **Adicionar uma ação**.  
    ![Azure – Nova etapa – Adicionar uma ação](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-05.png)
1. Pesquise "analisar JSON" na caixa de pesquisa **Designer de Aplicativos Lógicos** para localizar e selecionar a **ação** [Operações de Dados – Analisar JSON](../../logic-apps/logic-apps-overview.md#logic-app-concepts).  
    ![Azure – Aplicativo lógico – Adicionar ação de análise de JSON](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-06.png)
1. Insira "Carga" como o nome do **Conteúdo** da carga de Analisar JSON ou use a marca "Body" do conteúdo dinâmico.
1. Selecione a opção **Usar payload de amostra para gerar esquema** na caixa **Analisar JSON**.  
    ![Azure – Aplicativo lógico – Usar dados JSON de exemplo para gerar o esquema](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-07.png)
1. Cole a seguinte payload de amostra JSON na caixa de texto: `{"schemaId":"AIP Budget Notification","data":{"SubscriptionName":"CCM - Microsoft Azure Enterprise - 1","SubscriptionId":"<GUID>","SpendingAmount":"100","BudgetStartDate":"6/1/2018","Budget":"50","Unit":"USD","BudgetCreator":"email@contoso.com","BudgetName":"BudgetName","BudgetType":"Cost","ResourceGroup":"","NotificationThresholdAmount":"0.8"}}`
   A caixa de texto será exibida como:  
    ![Azure – Aplicativo lógico – O conteúdo de JSON de exemplo](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-08.png)
1. Selecione **Concluído**.

### <a name="add-the-first-conditional-action"></a>Adicionar a primeira ação condicional

Use uma instrução condicional para verificar se o valor de limite atingiu 80% ou mais de um intervalo de orçamento, mas não é maior ou igual a 100%. Se esse valor de limite for atingido, envie um HTTP POST usando o webhook chamado **Opcional**. Essa ação desligará as VMs no grupo **Opcional**.

1. Selecione **Nova etapa** > **Adicionar uma condição**.  
    ![Azure – Aplicativo lógico – Adicionar uma condição](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-09.png)
1. Na caixa **Condição**, selecione a caixa de texto que contém `Choose a value` para exibir uma lista de valores disponíveis.  
    ![Azure – Aplicativo lógico – Caixa de condição](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-10.png)
1. Selecione **Expressão** na parte superior da lista e insira a seguinte expressão no editor de expressão: `float()`  
    ![Azure – Aplicativo lógico – Expressão float](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-11.png)
1. Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.
   A expressão será:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Selecione **OK** para definir a expressão.
1. Selecione **é maior ou igual a** na caixa suspensa da **Condição**.
1. Na caixa **Escolher um valor** da condição, insira `.8`.  
    ![A captura de tela mostra a caixa de diálogo Condição com os valores selecionados.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-12.png)
1. Selecione **Adicionar** > **Adicionar linha** na caixa Condição para adicionar outra parte da condição.
1. Na caixa **Condição**, selecione a caixa de texto que contém `Choose a value`.
1. Selecione **Expressão** na parte superior da lista e insira a seguinte expressão no editor de expressão: `float()`
1. Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.
1. Selecione **OK** para definir a expressão.
1. Selecione **é menor que** na caixa suspensa da **Condição**.
1. Na caixa **Escolher um valor** da condição, insira `1`.  
    ![A captura de tela mostra a caixa de diálogo Condição com duas condições.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-13.png)
1. Na caixa **Se true**, selecione **Adicionar uma ação**. Você adicionará uma ação HTTP POST que desligará as VMs opcionais.  
    ![Azure – Aplicativo lógico – Adicionar uma ação](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-14.png)
1. Insira **HTTP** para pesquisar a ação HTTP e selecione a ação **HTTP – HTTP**.  
    ![Azure – Aplicativo lógico – Adicionar ação HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-15.png)
1. Selecione **Post** no valor do **Método**.
1. Insira a URL para o webhook chamado **Opcional** que você criou neste tutorial como o valor do **Uri**.  
    ![Azure – Aplicativo lógico – URI de ação HTTP](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-16.png)
1. Selecione **Adicionar uma ação** na caixa **Se true**. Você adicionará uma ação de email que enviará um email notificando o destinatário de que as VMs opcionais foram desligadas.
1. Pesquise "enviar email" e selecione uma ação de *enviar email* de acordo com o serviço de email que você usa.  
    ![Azure – Aplicativo lógico – Enviar ação de email](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-17.png)

    Para contas pessoais da Microsoft, selecione **Outlook.com**. Para as contas corporativas ou de estudante do Azure, selecione o **Outlook do Office 365**. Se você ainda não tiver uma conexão, será solicitado a entrar na sua conta de email. Os Aplicativos Lógicos criam uma conexão à sua conta de email.
   Você precisará permitir que o aplicativo lógico acesse suas informações de email.  
    ![Azure – Aplicativo lógico – Aviso de acesso](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-18.png)
1. Adicione o texto **Para**, **Assunto** e **Corpo** para o email que notifica o destinatário de que as VMs opcionais foram sido desligadas. Use o conteúdo dinâmico **BudgetName** e **NotificationThresholdAmount** para preencher os campos de assunto e corpo. 
    ![Azure – Aplicativo lógico – Detalhes do email](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-19.png)

### <a name="add-the-second-conditional-action"></a>Adicionar a segunda ação condicional

Use uma instrução condicional para verificar se o valor de limite atingiu ou excedeu 100% do valor do orçamento. Se o valor de limite tiver sido atingido, envie um HTTP POST usando o webhook chamado **Concluir**. Essa ação desligará todas as VMs restantes.

1. Selecione **Nova etapa** > **Adicionar uma Condição**.  
    ![A captura de tela mostra a caixa de diálogo Se verdadeiro com a opção Adicionar uma ação destacada.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-20.png)
1. Na caixa **Condição**, selecione a caixa de texto que contém `Choose a value` para exibir uma lista de valores disponíveis.
1. Selecione **Expressão** na parte superior da lista e insira a seguinte expressão no editor de expressão: `float()`
1. Selecione **Conteúdo dinâmico**, coloque o cursor dentro dos parênteses () e selecione **NotificationThresholdAmount** na lista para preencher a expressão completa.
   A expressão será semelhante a:<br>
    `float(body('Parse_JSON')?['data']?['NotificationThresholdAmount'])`
1. Selecione **OK** para definir a expressão.
1. Selecione **é maior ou igual a** na caixa suspensa da **Condição**.
1. Na caixa **Escolher um valor** para a condição, insira `1`.  
    ![Azure – Aplicativo lógico – Definir valor da condição](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-21.png)
1. Na caixa **Se true**, selecione **Adicionar uma ação**. Você adicionará uma ação HTTP POST que desligará todas as VMs restantes.  
    ![A captura de tela mostra a caixa de diálogo Se verdadeiro com a qual você pode adicionar uma ação HTTP POST.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-22.png)
1. Insira **HTTP** para pesquisar a ação HTTP e selecione a ação **HTTP – HTTP**.
1. Selecione **Post** como o valor do **Método**.
1. Insira a URL para o webhook chamado **Completo** que você criou neste tutorial como o valor do **Uri**.  
    ![A captura de tela mostra a caixa de diálogo HTTP em que você pode inserir o valor da URL.](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-23.png)
1. Selecione **Adicionar uma ação** na caixa **Se true**. Você adicionará uma ação de email que enviará um email notificando o destinatário de que as VMs restantes foram desligadas.
1. Pesquise "enviar email" e selecione uma ação de *enviar email* de acordo com o serviço de email que você usa.
1. Adicione o texto **Para**, **Assunto** e **Corpo** para o email que notifica o destinatário de que as VMs opcionais foram sido desligadas. Use o conteúdo dinâmico **BudgetName** e **NotificationThresholdAmount** para preencher os campos de assunto e corpo.  
    ![Azure – Aplicativo lógico – Enviar detalhes do email](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-24.png)
1. Selecione **Salvar** na parte superior da área **Designer de Aplicativo Lógico**.

### <a name="logic-app-summary"></a>Resumo do Aplicativo Lógico

Esta será a aparência do seu Aplicativo Lógico quando você terminar. No mais básico dos cenários em que você não precisa de nenhuma orquestração com base no limite, você pode chamar diretamente o script de automação do **Monitor** e ignorar a etapa de **Aplicativo Lógico**.

![Azure – Aplicativo lógico – Exibição completa](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-25.png)

Quando você salvou o aplicativo lógico, foi gerada uma URL que você poderá chamar. Você usará essa URL na próxima seção deste tutorial.

## <a name="create-an-azure-monitor-action-group"></a>Criar um grupo de ação do Azure Monitor

Um grupo de ação é uma coleção de preferências de notificação que você define. Quando um alerta é disparado, um grupo de ação específico pode receber o alerta sendo notificado. Um alerta do Azure gera proativamente uma notificação com base em condições específicas e oferece a oportunidade de agir. Um alerta pode usar dados de várias fontes, incluindo métricas e logs.

Os grupos de ação são o único ponto de extremidade que você integrará ao seu orçamento. Você pode configurar notificações em vários canais, mas, para este cenário, você se concentrará no aplicativo lógico criado anteriormente neste tutorial.

### <a name="create-an-action-group-in-azure-monitor"></a>Criar um grupo de ações no Azure Monitor

Ao criar o grupo de ações, você apontará o cursor para o aplicativo lógico criado anteriormente neste tutorial.

1. Se você ainda não tiver se conectado ao [portal do Azure](https://portal.azure.com/), entre nele e selecione **Todos os serviços** > **Monitor**.
1. Selecione **Alertas**, em seguida, selecione **Gerenciar ações**.
1. Selecione **Adicionar um grupo de ações** na área **Grupos de ações**.
1. Adicione e verifique os itens a seguir:
    - Nome do grupo de ações
    - Nome curto
    - Subscription
    - Resource group  
    ![Azure – Aplicativo lógico – Adicionar um grupo de ações](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-26.png)
1. No painel **Adicionar grupo de ações**, adicione uma ação LogicApp. Dê à ação o nome de **Budget-BudgetLA**. No painel **Aplicativo Lógico**, selecione a **Assinatura** e o **Grupo de recursos**. Em seguida, selecione o **Aplicativo lógico** que você criou anteriormente neste tutorial.
1. Selecione **OK** para definir o aplicativo lógico. Em seguida, selecione **OK** no painel **Adicionar grupo de ações** para criar o grupo de ações.

Você concluiu todos os componentes de suporte necessários para orquestrar seu orçamento de maneira eficaz. Agora, tudo o que você precisa fazer é criar o orçamento e configurá-lo para usar o grupo de ação que você criou.

## <a name="create-the-azure-budget"></a>Criar o orçamento do Azure

É possível criar um orçamento no portal do Azure usando o [recurso de orçamento](../costs/tutorial-acm-create-budgets.md) no Gerenciamento de Custos. Ou então, é possível criar um orçamento usando APIs REST, cmdlets do PowerShell ou usar a CLI. O procedimento a seguir usa a API REST. Antes de chamar a API REST, você precisará ter um token de autorização. Para criar um token de autorização, você pode usar o projeto [ARMClient](https://github.com/projectkudu/ARMClient). O **ARMClient** permite que você se autentique com o Azure Resource Manager e obtenha um token para chamar as APIs.

### <a name="create-an-authentication-token"></a>Criar um token de autenticação

1. Navegue até o projeto [ARMClient](https://github.com/projectkudu/ARMClient) no GitHub.
1. Clone o repositório para obter uma cópia local.
1. Abra o projeto no Visual Studio e compile-o.
1. Depois que o build for bem-sucedido, o executável deverá estar na pasta *\bin\debug*.
1. Execute o ARMClient. Abra um prompt de comando e navegue até a pasta *\bin\debug* da raiz do projeto.
1. Para entrar e se autenticar, insira o seguinte comando no prompt de comando:<br>
    `ARMClient login prod`
1. Copie o **guid de assinatura** da saída.
1. Para copiar um token de autorização para a área de transferência, insira o seguinte comando no prompt de comando e use a ID de assinatura copiada na etapa anterior: <br>
    `ARMClient token <subscription GUID from previous step>`

    Depois de concluir a etapa acima, você verá:<br>
    **Token copiado com êxito para a área de transferência.**
1. Salve o token a ser usado para as etapas na próxima seção deste tutorial.

### <a name="create-the-budget"></a>Criar o orçamento

Em seguida, você vai configurar o **Postman** para criar um orçamento chamando as APIs REST de Consumo do Azure. Postman é um ambiente de Desenvolvimento de API. Você importará os arquivos de coleção e ambiente para o Postman. A coleção contém definições agrupadas de solicitações HTTP que chamam as APIs REST de Consumo do Azure. O arquivo de ambiente contém variáveis que são usadas pela coleção.

1. Baixe e abra o [Cliente REST do Postman](https://www.getpostman.com/) para executar as APIs REST.
1. No Postman, crie uma nova solicitação.  
    ![Postman – Criar uma nova solicitação](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-27.png)
1. Salve a nova solicitação como uma coleção, de modo que a nova solicitação não tenha nada.  
    ![Postman – Salvar a nova solicitação](./media/cost-management-budget-scenario/billing-cost-management-budget-scenario-28.png)
1. Altere a solicitação de uma ação `Get` para `Put`.
1. Modificar a URL a seguir substituindo `{subscriptionId}` pela **ID da Assinatura** que você usou na seção anterior deste tutorial. Além disso, modifique a URL para incluir "SampleBudget" como o valor para `{budgetName}`: `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2018-03-31`
1. Selecione a guia **Cabeçalhos** no Postman.
1. Adicione uma nova **Chave** chamada "Autorização".
1. Defina o **Valor** para o token criado usando o ArmClient no final da última seção.
1. Selecione a guia **Corpo** no Postman.
1. Selecione a opção de botão **bruto**.
1. Na caixa de texto, cole a definição de orçamento de exemplo abaixo. No entanto, você precisará substituir os parâmetros `subscriptionID`, `resourcegroupname` e `actiongroupname` pela ID da assinatura, por um nome exclusivo para o grupo de recursos e pelo nome do grupo de ações criado na URL e no corpo da solicitação:

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
                "filters": {},
            "notifications": {
                "Actual_GreaterThan_80_Percent": {
                    "enabled": true,
                    "operator": "GreaterThan",
                    "threshold": 80,
                    "contactEmails": [],
                    "contactRoles": [],
                    "contactGroups": [
                        "/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/microsoft.insights/actionGroups/{actiongroupname}"
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
        }
    ```
1. Pressione **Enviar** para enviar a solicitação.

Agora você tem todas as partes de que precisa chamar a [API de orçamentos](/rest/api/consumption/budgets). A referência de API de orçamentos traz detalhes adicionais sobre as solicitações específicas, incluindo:

- **budgetName** – há suporte para vários orçamentos.  Os nomes de orçamento devem ser exclusivos.
- **categoria** – deve ser **Custo** ou **Uso**. A API dá suporte a orçamentos de uso e custo.
- **timeGrain** – um orçamento mensal, trimestral ou anual. O valor é redefinido no final do período.
- **filtros** – Filtros permitem que você restrinja o orçamento para um conjunto específico de recursos no escopo selecionado. Por exemplo, um filtro pode ser uma coleção de grupos de recursos de um orçamento no nível de assinatura.
- **notificações** – Determina os limites e os detalhes da notificação. Você pode configurar vários limites e fornecer um endereço de email ou um grupo de ações para receber uma notificação.

## <a name="summary"></a>Resumo

Usando este tutorial, você aprendeu a:

- Como criar um Runbook de Automação do Azure para interromper VMs.
- Como criar um aplicativo lógico do Azure disparado com base nos valores de limite de orçamento e chamar o runbook relacionado com os parâmetros corretos.
- Como criar um grupo de ações do Azure Monitor que foi configurado para disparar o Aplicativo Lógico do Azure quando o limite de orçamento é atingido.
- Como criar o orçamento do Azure com os limites desejados e conecte-lo ao grupo de ação.

Agora você tem um orçamento totalmente funcional para a sua assinatura que desligará suas VMs quando você atingir seus limites de orçamento configurados.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre cenários de cobrança do Azure, veja [Cenários de automação de gerenciamento de custos e cobrança](cost-management-automation-scenarios.md).