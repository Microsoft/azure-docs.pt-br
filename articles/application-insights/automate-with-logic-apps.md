---
title: "Automatize os processos do Azure Application Insights usando os Aplicativos Lógicos."
description: "Saiba como você pode automatizar rapidamente os processos repetíveis adicionando o conector do Application Insights ao aplicativo lógico."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: bwren
ms.openlocfilehash: 36df5bc0a019f4197d40fd6fa5a2a9957820c8b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatize os processos do Application Insights usando os Aplicativos Lógicos

Você está executando repetidamente as mesmas consultas nos dados telemétricos para verificar se o serviço está funcionando adequadamente? Deseja automatizar essas consultas para descobrir tendências e anomalias e então criar seus próprios fluxos de trabalho para elas? O conector do Azure Application Insights (versão prévia) para os Aplicativos Lógicos é a ferramenta certa para esses fins.

Com essa integração, você pode automatizar diversos processos sem a necessidade de escrever nenhuma linha de código. É possível criar um Aplicativo Lógico com o conector do Application Insights para automatizar rapidamente qualquer processo do Application Insights. 

Adicione também outras ações. O recurso Aplicativos Lógicos do Serviço de Aplicativo do Azure disponibiliza centenas de ações. Por exemplo, usando um aplicativo lógico, você pode enviar automaticamente uma notificação por email ou criar um bug no Visual Studio Team Services. Você também pode usar um dos muitos [modelos](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates) disponíveis para ajudar a acelerar o processo de criação de seu aplicativo lógico. 

## <a name="create-a-logic-app-for-application-insights"></a>Criar um aplicativo lógico para o Application Insights

Neste tutorial, você aprenderá como criar um aplicativo lógico que utliza o algoritmo de cluster automático do Analytics para agrupar atributos nos dados de um aplicativo Web. O Flow envia automaticamente os resultados por email, apenas um exemplo de como você pode usar os Aplicativos Lógicos e o Application Insights Analytics juntos. 

### <a name="step-1-create-a-logic-app"></a>Etapa 1: criar um aplicativo lógico
1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel **Novo**, selecione **Web + Móvel** e, em seguida, selecione **Aplicativo Lógico**.

    ![Janela Novo aplicativo lógico](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Etapa 2: criar um gatilho para seu aplicativo lógico
1. Na janela **Designer de Aplicativo Lógico**, em **Iniciar com um gatilho comum**, escolha **Recorrência**.

    ![Janela Designer de Aplicativo Lógico](./media/automate-with-logic-apps/logicapp2.png)

2. Na caixa **Frequência**, selecione **Dia** e, na caixa **Intervalo**, digite **1**.

    ![Janela "Recorrência" do Designer de Aplicativo Lógico](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>Etapa 3: adicionar uma ação do Application Insights
1. Clique na caixa **Nova etapa** e depois clique em **Adicionar uma ação**.

2. Na caixa de pesquisa **Escolher uma ação**, digite **Azure Application Insights**.

3. Em **Ações**, clique em **Versão Prévia do Azure Application Insights – Visualizar consulta do Analytics**.

    ![Janela "Escolha uma ação" do Designer de Aplicativo Lógico](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Etapa 4: conectar-se a um recurso do Application Insights

Para concluir esta etapa, você precisa de uma ID do Aplicativo e uma Chave de API do recurso. Você pode recuperá-las no Portal do Azure, conforme mostrado no seguinte diagrama:

![ID do Aplicativo no portal do Azure](./media/automate-with-logic-apps/appid.png) 

Forneça um nome para a conexão, a ID do aplicativo e a chave de API.

![Janela Conexão de fluxo do Designer de Aplicativo Lógico](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Etapa 5: especificar o tipo de consulta do Analytics e o tipo de gráfico
No exemplo a seguir, a consulta seleciona as solicitações com falha no último dia e correlaciona-as com as exceções que ocorreram como parte da operação. O Analytics correlaciona as solicitações com falha com base no identificador operation_Id. Em seguida, a consulta segmenta os resultados usando o algoritmo de cluster automático. 

Quando criar suas próprias consultas, verifique se elas estão funcionando corretamente no Analytics antes de adicioná-las ao fluxo.

1. Na caixa **consulta**, adicione a seguinte consulta de Análise: 

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

2. Na caixa **Tipo de Gráfico**, selecione **Tabela Html**.

    ![Janela de configuração de consulta do Analytics](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Etapa 6: configurar o aplicativo lógico para enviar email

1. Clique em **Nova etapa** e, depois, selecione **Adicionar uma ação**.

2. Na caixa de pesquisa, digite **Office 365 Outlook**.

3. Clique em **Office 365 Outlook – Enviar um email**.

    ![Seleção do Office 365 Outlook](./media/automate-with-logic-apps/flow2b.png)

4. Na janela **Enviar um email**, faça o seguinte:

   a. Digite o endereço de email do destinatário.

   b. Digite um assunto para o email.

   c. Clique em qualquer lugar na caixa **Corpo** e, no menu de conteúdo dinâmico que se abre à direita, selecione **Corpo**.

   d. Clique em **Mostrar opções avançadas**.

      ![Configuração do Office 365 Outlook](./media/automate-with-logic-apps/flow5.png)

5. No menu de conteúdo dinâmico, faça o seguinte:

    a. Selecione o **Nome do Anexo**.

    b. Selecione o **Conteúdo do Anexo**.
    
    c. Na caixa **É HTML**, selecione **Sim**.

      ![Tela de configuração de email do Office 365](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Etapa 7: salvar e testar o aplicativo lógico
* Clique em **Salvar** para salvar as alterações.

Você pode aguardar o gatilho para executar o aplicativo lógico ou pode executá-lo imediatamente escolhendo **Executar**.

![Tela de criação do aplicativo lógico](./media/automate-with-logic-apps/step7.png)

Quando o aplicativo lógico for executado, os destinatários especificados na lista de emails receberão um email semelhante ao seguinte:

![Mensagem de email do aplicativo lógico](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a criação de [Consultas do Analytics](app-insights-analytics-using.md).
- Saiba mais sobre o [Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).



<!--Link references-->





