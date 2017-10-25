---
title: Automatizar os processos do Azure Application Insights com o Microsoft Flow
description: "Saiba como você pode usar o Microsoft Flow para automatizar rapidamente os processos repetidos usando o conector do Application Insights."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: bwren
ms.openlocfilehash: 510f4f284bbd0dbe4171896899f7ade7dee19e39
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>Automatizar os processos do Azure Application Insights com o conector para o Microsoft Flow

Você está executando repetidamente as mesmas consultas nos dados telemétricos para verificar se o serviço está funcionando corretamente? Deseja automatizar essas consultas para descobrir tendências e anomalias e então criar seus próprios fluxos de trabalho para elas? O conector do Azure Application Insights (versão prévia) para o Microsoft Flow é a ferramenta certa para esses fins.

Com essa integração, você agora pode automatizar diversos processos sem a necessidade de escrever nenhuma linha de código. Depois de você criar um fluxo usando uma ação do Application Insights, o fluxo executa automaticamente a consulta do Application Insights Analytics. 

Adicione também outras ações. O Microsoft Flow disponibiliza centenas de ações. Por exemplo, você pode usar o Microsoft Flow para enviar uma notificação por email automaticamente ou criar um bug no Visual Studio Team Services. Use também um dos vários [modelos](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights) disponíveis para o conector para o Microsoft Flow. Esses modelos aceleram o processo de criação de um fluxo. 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>Criar um fluxo para o Application Insights

Neste tutorial, você aprenderá a criar um fluxo que usa o algoritmo de cluster automático do Analytics para agrupar atributos nos dados de um aplicativo Web. O fluxo envia automaticamente os resultados por email, apenas um exemplo de como você pode usar o Microsoft Flow e o Application Insights Analytics juntos. 

### <a name="step-1-create-a-flow"></a>Etapa 1: Criar um fluxo
1. Entre no [Microsoft Flow](http://flow.microsoft.com) e, em seguida, selecione **Meus Fluxos**.
2. Clique em **Criar um fluxo em branco**.

### <a name="step-2-create-a-trigger-for-your-flow"></a>Etapa 2: Criar um gatilho para o fluxo
1. Selecione **Agendamento** e, depois, **Agendamento – Recorrência**.
2. Na caixa **frequência**, selecione **Dia** e, na caixa **Intervalo**, digite **1**.

    ![Caixa de diálogo de gatilho do Microsoft Flow](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>Etapa 3: adicionar uma ação do Application Insights
1. Clique na caixa **Nova etapa** e depois clique em **Adicionar uma ação**.
2. Pesquise por **Azure Application Insights**.
3. Clique em **Versão Prévia do Azure Application Insights – Visualizar a versão prévia da consulta do Analytics**.

    ![Janela Executar consulta do Analytics](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: conectar-se a um recurso do Application Insights

Para concluir esta etapa, você precisa de uma ID do Aplicativo e uma Chave de API do recurso. Você pode recuperá-las no Portal do Azure, conforme mostrado no seguinte diagrama:

![ID do Aplicativo no portal do Azure](./media/app-insights-automate-with-flow/appid.png) 

- Forneça um nome para a conexão, junto com a ID do aplicativo e a Chave de API.

    ![Janela de conexão do Microsoft Flow](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: especificar o tipo de consulta do Analytics e o tipo de gráfico
Esta consulta de exemplo seleciona as solicitações com falha no último dia e correlaciona-as com as exceções que ocorreram como parte da operação. O Analytics correlaciona-os com base no identificador operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo de cluster automático. 

Quando criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao fluxo.

- Adicione a consulta do Analytics a seguir e selecione o tipo de gráfico de tabela HTML. 

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
    
    ![Janela de configuração de consulta do Analytics](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>Etapa 6: Configurar o fluxo para enviar email

1. Clique na caixa **Nova etapa** e depois clique em **Adicionar uma ação**.
2. Pesquise por **Office 365 Outlook**.
3. Clique em **Office 365 Outlook – Enviar um email**.

    ![Janela de seleção do Office 365 Outlook](./media/app-insights-automate-with-flow/flow2b.png)

4. Na janela **Enviar um email**, faça o seguinte:

   a. Digite o endereço de email do destinatário.

   b. Digite um assunto para o email.

   c. Clique em qualquer lugar na caixa **Corpo** e, no menu de conteúdo dinâmico que se abre à direita, selecione **Corpo**.

   d. Clique em **Mostrar opções avançadas**.

    ![Configuração do Office 365 Outlook](./media/app-insights-automate-with-flow/flow5.png)

5. No menu de conteúdo dinâmico, faça o seguinte:

    a. Selecione o **Nome do Anexo**.

    b. Selecione o **Conteúdo do Anexo**.
    
    c. Na caixa **É HTML**, selecione **Sim**.

    ![Janela de configuração de email do Office 365](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>Etapa 7: Salvar e testar o fluxo
- Na caixa **Nome do fluxo**, adicione um nome para o fluxo e, em seguida, clique em **Criar fluxo**.

    ![Janela de criação de fluxo](./media/app-insights-automate-with-flow/flow8.png)

Você Pode aguardar até que o gatilho execute essa ação ou executar o fluxo imediatamente [executando o gatilho sob demanda](https://flow.microsoft.com/blog/run-now-and-six-more-services/).

Quando o fluxo é executado, os destinatários que você especificou na lista de emails recebem uma mensagem de email com a seguinte aparência:

![Email de exemplo](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre como criar [consultas do Analytics](app-insights-analytics-using.md).
- Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).



<!--Link references-->





