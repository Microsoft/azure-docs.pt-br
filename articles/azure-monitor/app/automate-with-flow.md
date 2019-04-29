---
title: Automatizar os processos do Azure Application Insights com o Microsoft Flow
description: Saiba como você pode usar o Microsoft Flow para automatizar rapidamente os processos repetidos usando o conector do Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mbullwin
ms.openlocfilehash: 15299be83758c157bf3bc7d9fb27b50763b9148e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60903437"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar os processos do Azure Application Insights com o conector para o Microsoft Flow

Você está executando repetidamente as mesmas consultas nos dados telemétricos para verificar se o serviço está funcionando corretamente? Deseja automatizar essas consultas para descobrir tendências e anomalias e então criar seus próprios fluxos de trabalho para elas? O conector do Application Insights do Azure para Microsoft Flow é a ferramenta certa para esses fins.

Com essa integração, você agora pode automatizar diversos processos sem a necessidade de escrever nenhuma linha de código. Depois de você criar um fluxo usando uma ação do Application Insights, o fluxo executa automaticamente a consulta do Application Insights Analytics. 

Adicione também outras ações. O Microsoft Flow disponibiliza centenas de ações. Por exemplo, você pode usar o Microsoft Flow para enviar uma notificação por email automaticamente ou criar um bug no Azure DevOps. Use também um dos vários [modelos](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponíveis para o conector para o Microsoft Flow. Esses modelos aceleram o processo de criação de um fluxo. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para o Application Insights

Neste tutorial, você aprenderá a criar um fluxo que usa o algoritmo de cluster automático do Analytics para agrupar atributos nos dados de um aplicativo Web. O fluxo envia automaticamente os resultados por email, apenas um exemplo de como você pode usar o Microsoft Flow e o Application Insights Analytics juntos. 

### <a name="step-1-create-a-flow"></a>Etapa 1: Criar um fluxo
1. Entre no [Microsoft Flow](https://flow.microsoft.com) e, em seguida, selecione **Meus Fluxos**.
2. Clique em **New** , em seguida, **criar em branco**.

    ![Criar novo fluxo de espaço em branco](./media/automate-with-flow/1createflow.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>Etapa 2: Criar um gatilho para o fluxo
1. No Build na guia **agendamento**e, em seguida, selecione **agenda - recorrência**.

    ![Selecione programar em build](./media/automate-with-flow/2schedule.png)

1. No **intervalo** , digite **1**e, nas **frequência** caixa, selecione **dia**.
2. Clique em **nova etapa**

    ![Configurar a agenda de recorrência com frequência e o intervalo de inserção](./media/automate-with-flow/3schedulerecurrence.png)


### <a name="step-3-add-an-application-insights-action"></a>Etapa 3: Adicionar uma ação do Application Insights
1. Pesquise por **Azure Application Insights**.
2. Clique em **Azure Application Insights – consulta do Analytics visualizar**.
 
    ![Escolha uma ação: Consulta de análise de visualizar do Application Insights do Azure](./media/automate-with-flow/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: Conectar-se a um recurso do Application Insights

Para concluir esta etapa, você precisa de uma ID do Aplicativo e uma Chave de API do recurso. Você pode recuperá-las no Portal do Azure, conforme mostrado no seguinte diagrama:

![ID do Aplicativo no portal do Azure](./media/automate-with-flow/5apiaccess.png)

![Chave de API no portal do Azure](./media/automate-with-flow/6apikey.png)

- Forneça um nome para a conexão, junto com a ID do aplicativo e a Chave de API.

    ![Janela de conexão do Microsoft Flow](./media/automate-with-flow/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: Especificar o tipo de consulta do Analytics e o tipo de gráfico
Esta consulta de exemplo seleciona as solicitações com falha no último dia e correlaciona-as com as exceções que ocorreram como parte da operação. O Analytics correlaciona-os com base no identificador operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo de cluster automático. 

Quando criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao fluxo.

- Adicione a seguinte consulta do Analytics e selecione o tipo de gráfico de tabela HTML. Em seguida, selecione **nova etapa**.

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Janela de configuração de consulta do Analytics](./media/automate-with-flow/8query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Etapa 6: Configurar o fluxo para enviar email

1. Pesquise por **Office 365 Outlook**.
2. Clique em **Office 365 Outlook – enviar um e-mail**.

    ![Janela de seleção do Office 365 Outlook](./media/automate-with-flow/9outlookaction.png)

1. Na janela **Enviar um email**, faça o seguinte:

   a. Digite o endereço de email do destinatário.

   b. Digite um assunto para o email.

   c. Clique em qualquer lugar na caixa **Corpo** e, no menu de conteúdo dinâmico que se abre à direita, selecione **Corpo**.

   d. Clique em **Mostrar opções avançadas**.

    ![Configuração do Office 365 Outlook](./media/automate-with-flow/10sendemailbody.png)

1. No menu de conteúdo dinâmico, faça o seguinte:

    a. Selecione o **Nome do Anexo**.

    b. Selecione o **Conteúdo do Anexo**.
    
    c. Na caixa **É HTML**, selecione **Sim**.

    ![Janela de configuração de email do Office 365](./media/automate-with-flow/11emailattachment.png)

### <a name="step-7-save-and-test-your-flow"></a>Etapa 7: Salvar e testar o fluxo
- No **nome do fluxo** caixa, adicionar um nome para o fluxo e, em seguida, clique em **salvar**.

    ![Nomeie o fluxo e salve](./media/automate-with-flow/12nameflow.png)

Você Pode aguardar até que o gatilho execute essa ação ou executar o fluxo imediatamente [executando o gatilho sob demanda](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando o fluxo é executado, os destinatários que você especificou na lista de emails recebem uma mensagem de email com a seguinte aparência:

![Email de exemplo](./media/automate-with-flow/flow9.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como criar [consultas do Analytics](../../azure-monitor/log-query/get-started-queries.md).
- Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





