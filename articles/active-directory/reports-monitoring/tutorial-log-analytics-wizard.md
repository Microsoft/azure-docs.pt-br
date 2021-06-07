---
title: Configurar o assistente do Log Analytics no Azure AD | Microsoft Docs
description: Saiba como configurar o Log Analytics.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca84fa57cb3a26337038275d1b7491154915c90e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574373"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Tutorial: Configurar o assistente do Log Analytics


Neste tutorial, você aprende a:

> [!div class="checklist"]
> * Configurar um workspace do Log Analytics para seus logs de auditoria e de entrada
> * Executar consultas usando a KQL (Kusto Query Language)
> * Criar uma regra de alerta que envia alertas quando uma conta específica é usada
> * Criar uma pasta de trabalho personalizada usando o modelo de início rápido
> * Adicionar uma consulta a um modelo de pasta de trabalho existente

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure com pelo menos um administrador licenciado P1. Se não tiver uma assinatura do Azure, você poderá [inscrever-se em uma avaliação gratuita](https://azure.microsoft.com/free/).

- Um locatário do Azure AD.

- Um usuário que seja administrador global ou administrador de segurança do locatário do Azure AD.


Familiarize-se com estes artigos:

- [Tutorial: Coletar e analisar logs de recursos de um recurso do Azure](../../azure-monitor/essentials/tutorial-resource-logs.md)

- [Como integrar logs de atividades ao Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [Gerenciar conta de acesso de emergência no Azure AD](../roles/security-emergency-access.md)

- [Referência rápida de KQL](/azure/data-explorer/kql-quick-reference)

- [Pastas de trabalho do Azure Monitor](../../azure-monitor/visualize/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Configurar um workspace 

Este procedimento descreve como configurar um workspace do log Analytics para os logs de auditoria e de entrada.
Configurar um workspace do log Analytics consiste em duas etapas principais:
 
1. Como criar um workspace do Log Analytics
2. Como definir as configurações de diagnóstico

**Para configurar um workspace:** 


1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Pesquise **workspaces do log analytics**.

    ![Pesquisar recursos, serviços e documentos](./media/tutorial-log-analytics-wizard/search-services.png)

3. Na página workspaces do log Analytics, clique em **Adicionar**.

    ![A captura de tela mostra o botão Adicionar na página dos workspaces do Log Analytics.](./media/tutorial-log-analytics-wizard/add.png)

4.  Na página **Criar workspace do Log Analytics**, execute as seguintes etapas:

    ![Criar workspace do Log Analytics](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Selecione sua assinatura.

    2. Selecione um grupo de recursos.
 
    3. Na caixa de texto **Nome**, digite um nome (por exemplo: MytestWorkspace1).

    4. Selecione sua região.

5. Clique em **Revisar + Criar**.

    ![Examinar e criar](./media/tutorial-log-analytics-wizard/review-create.png)

6. Clique em **Criar** e aguarde a implantação ser bem-sucedida. Talvez seja necessário atualizar a página para ver o novo workspace.

    ![Criar](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Pesquise o **Azure Active Directory**.

    ![A captura de tela mostra o Azure Active Directory no Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. Na seção **Monitoramento**, clique em **Configuração de diagnóstico**.

    ![A captura de tela mostra as configurações de diagnóstico selecionadas no Monitoramento.](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. Na página **Configurações de diagnóstico**, clique em **Adicionar configuração de diagnóstico**.

    ![Adicionar configuração de diagnóstico](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Na página **Configuração de diagnóstico**, execute as seguintes etapas:

    ![Selecionar configurações de diagnóstico](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. Em **Detalhes da categoria**, selecione **AuditLogs** e **SigninLogs**.

    2. Em **Detalhes de destino**, selecione **Enviar para o Log Analytics** e selecione o novo workspace do Log Analytics. 
   
    3. Clique em **Save** (Salvar). 

## <a name="run-queries"></a>Executar consultas  

Este procedimento mostra como executar consultas usando a **KQL (Kusto Query Language)** .


**Para executar uma consulta:**


1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Pesquise o **Azure Active Directory**.

    ![A captura de tela mostra o Azure Active Directory no Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na seção **Monitoramento**, clique em **Logs**.

4. Na página **Logs**, clique em **Introdução**.

5. Na caixa de texto **Pesquisar*, digite sua consulta.

6. Clique em **Executar**.  


### <a name="kql-query-examples"></a>Exemplos de consulta KQL

Pegue 10 entradas aleatórias dos dados de entrada:

`SigninLogs | take 10`

Examine as entradas em que o acesso condicional foi um sucesso

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Conte o número de sucessos

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Contagem agregada de entradas bem-sucedidas por usuário por dia:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Veja quantas vezes um usuário realiza uma determinada operação em um período específico:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


Dinamizar os resultados no nome da operação

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Mescle Logs de Auditoria e de Entrada usando uma junção interna:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated, UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Veja o número de entradas por tipo de aplicativo cliente:

`SigninLogs | summarize count() by ClientAppUsed`

Conte as entradas por dia:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Use cinco entradas aleatórias e projete as colunas que você deseja ver nos resultados:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Use os cinco primeiros em ordem decrescente e projete as colunas que você deseja ver

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Crie uma coluna combinando os valores para duas outras colunas:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Criar uma regra de alerta  

Este procedimento mostra como enviar alertas quando a conta breakglass é usada.

**Para criar uma regra de alerta:**


1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Pesquise o **Azure Active Directory**.

    ![A captura de tela mostra o Azure Active Directory no Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na seção **Monitoramento**, clique em **Logs**.

4. Na página **Logs**, clique em **Introdução**.

5. Na caixa de texto **Pesquisar**, digite: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Clique em **Executar**.  

7. Na barra de ferramentas, clique em **Nova regra de alerta**.

    ![Nova regra de alerta](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. Na página **Criar regra de alerta**, verifique se o escopo está correto.

9. Em **Condição**, clique em: **Sempre que a pesquisa de logs personalizada média for maior que a <logic undefined>contagem**

    ![Condição padrão](./media/tutorial-log-analytics-wizard/default-condition.png)

10. Na página **Configurar lógica de sinal**, na seção **Lógica de alerta**, execute as seguintes etapas:

    ![Lógica de alerta](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. Como **Baseado em**, selecione **Número de resultados**.

    2. Como **Operador**, selecione **Maior que**.

    3. Como o **Valor limite**, selecione **0**. 

11. Na página **Configurar lógica de sinal**, na seção **Avaliado com base em**, execute as seguintes etapas:

    ![Avaliado com base em](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Como **Período (em minutos)** , selecione **5**.

    2. Como **Frequência (em minutos)** , selecione **5**.

    3. Clique em **Concluído**. 

12. Em **Grupo de ações**, clique em **Selecionar grupo de ações**. 

    ![Grupo de ações](./media/tutorial-log-analytics-wizard/action-group.png)

13. Em **Selecionar um grupo de ações para anexar a esta regra de alerta**, clique em **Criar grupo de ações**. 

    ![Criar grupo de ações](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Na página **Criar grupo de ações**, execute as seguintes etapas:

    ![Detalhes da instância](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. Na caixa de texto **Nome do grupo de ações**, digite **Meu grupo de ações**.

    2. Na caixa de texto **Nome de exibição**, digite **Minha ação**.

    3. Clique em **Revisar + Criar**. 

    4. Clique em **Criar**.


15. Em **Personalizar ação**, execute as seguintes etapas:

    ![Personalizar ações](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Selecione **Assunto do email**.

    2. Na caixa de texto **Linha do assunto**, digite: `Breakglass account has been used`

16. Em **Detalhes da regra de alerta**, execute as seguintes etapas:

    ![Detalhes da regra de alerta](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. Na caixa de texto **Nome da regra de alerta**, digite: `Breakglass account`

    2. Na caixa de texto **Descrição**, digite: `Your emergency access account has been used`

17. Clique em **Criar regra de alerta**.   


## <a name="create-a-custom-workbook"></a>Criar uma pasta de trabalho personalizada

Este procedimento mostra como criar uma pasta de trabalho usando o modelo de início rápido.




1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Pesquise o **Azure Active Directory**.

    ![A captura de tela mostra o Azure Active Directory no Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na seção **Monitoramento**, clique em **Pastas de trabalho**.

    ![A captura de tela mostra Monitoramento no menu do portal do Azure com a opção Pastas de Trabalho selecionada.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Na seção **Início rápido**, clique em **Vazio**.

    ![Início rápido](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Clique em **Adicionar**.

    ![Adicionar pasta de trabalho](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Clique em **Adicionar texto**.

    ![Adicionar texto](./media/tutorial-log-analytics-wizard/add-text.png)


7. Na caixa de texto, digite `# Client apps used in the past week` e clique em **Edição Concluída**.

    ![Texto da pasta de trabalho](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. Na nova pasta de trabalho, clique em **Adicionar** e em **Adicionar consulta**.

    ![Adicionar consulta](./media/tutorial-log-analytics-wizard/add-query.png)

9. Na caixa de texto de consulta, digite: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Clique em **Executar Consulta**.

    ![A captura de tela mostra o botão Executar Consulta.](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Na barra de ferramentas, em **Visualização**, clique em **Gráfico de pizza**.

    ![Gráfico de pizza](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Clique em **Edição concluída**.

    ![Edição concluída](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Adicionar uma consulta a um modelo de pasta de trabalho  

Este procedimento mostra como adicionar uma consulta a um modelo de pasta de trabalho existente. O exemplo se baseia em uma consulta que mostra a distribuição de êxito para falhas no acesso condicional.


1. Entre no [Portal do Azure](https://portal.azure.com) como administrador global.

2. Pesquise o **Azure Active Directory**.

    ![A captura de tela mostra o Azure Active Directory no Azure Search.](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. Na seção **Monitoramento**, clique em **Pastas de trabalho**.

    ![A captura de tela mostra Monitoramento no menu com a opção Pastas de Trabalho selecionada.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. Na seção **Acesso condicional**, clique em **Relatório e Insights de Acesso Condicional**.

    ![A captura de tela mostra a opção de Relatórios e Insights de Acesso Condicional.](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Na barra de ferramentas, clique em **Editar**.

    ![A captura de tela mostra o botão Editar.](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Na barra de ferramentas, clique nos três pontos, **Adicionar** e em **Adicionar consulta**.

    ![Adicionar consulta da pasta de trabalho](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. Na caixa de texto de consulta, digite: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Clique em **Executar Consulta**.

    ![A captura de tela mostra o botão Executar Consulta para executar essa consulta.](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Clique em **Intervalo de Tempo** e selecione **Definir na consulta**.

10. Clique em **Visualização** e selecione **Gráfico de barras**. 

11. Clique em **Configurações Avançadas**, como título do gráfico, digite `Conditional Access status over the last 20 days` e clique em **Edição Concluída**. 

    ![Definir título do gráfico](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Próximas etapas

Continue para o próximo artigo para saber como gerenciar identidades de dispositivo usando o portal do Azure.
> [!div class="nextstepaction"]
> [Monitoring](overview-monitoring.md)