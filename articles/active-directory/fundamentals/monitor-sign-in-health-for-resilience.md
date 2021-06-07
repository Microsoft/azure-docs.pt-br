---
title: Monitorar a integridade de entrada do aplicativo para resiliência no Azure Active Directory
description: Crie consultas e notificações para monitorar a integridade de entrada de seus aplicativos.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47691ae404f65f04ace36485cb01fc5617d00a9a
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105031759"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Monitorando a integridade de entrada do aplicativo para resiliência

Para aumentar a resiliência da infraestrutura, configure o monitoramento da integridade de entrada do aplicativo para seus aplicativos críticos para que você receba um alerta se ocorrer um incidente de impacto. Para ajudá-lo nesse esforço, você pode configurar alertas com base na pasta de trabalho de integridade de entrada. 

Esta pasta de trabalho permite que os administradores monitorem solicitações de autenticação para aplicativos em seu locatário. Ele fornece estes recursos principais:

* Configure a pasta de trabalho para monitorar todos ou aplicativos individuais com dados quase em tempo real.

* Configure alertas para notificá-lo quando os padrões de autenticação forem alterados para que você possa investigar e tomar medidas.

* Compare as tendências em um período, por exemplo, semana a semana, que é a configuração padrão da pasta de trabalho.

> [!NOTE]
> Para ver todas as pastas de trabalho disponíveis e os pré-requisitos para usá-las, consulte [como usar pastas de trabalho do Azure monitor para relatórios](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Durante um evento de impacto, duas coisas podem acontecer:

* O número de entradas de um aplicativo pode descartar significativamente porque os usuários não podem entrar.

* O número de falhas de entrada pode aumentar. 

Este artigo explica como configurar a pasta de trabalho de integridade de entrada para monitorar as interrupções nas entradas de seus usuários.

## <a name="prerequisites"></a>Pré-requisitos

* Um locatário do Azure AD.

* Um usuário com função administrador global ou administrador de segurança para o locatário do Azure AD.

* Um espaço de trabalho Log Analytics em sua assinatura do Azure para enviar logs para Azure Monitor logs. 

   * Saiba como [criar um espaço de trabalho log Analytics](../../azure-monitor/logs/quick-create-workspace.md)

* Logs do Azure AD integrados a logs de Azure Monitor

   * Saiba como [integrar logs de entrada do Azure AD ao Azure monitor Stream.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

## <a name="configure-the-app-sign-in-health-workbook"></a>Configurar a pasta de trabalho de integridade de entrada do aplicativo 

Para acessar pastas de trabalho, abra o **portal do Azure**, selecione **Azure Active Directory** e, em seguida, selecione **pastas de trabalho**.

Você verá pastas de trabalho em uso, acesso condicional e solução de problemas. A pasta de trabalho de integridade de logon do aplicativo aparece na seção uso.

Depois de usar uma pasta de trabalho, ela pode aparecer na seção pastas de trabalho modificadas recentemente.

![Captura de tela mostrando a Galeria de pastas de trabalho no portal do Azure.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


A pasta de trabalho de integridade de logon do aplicativo permite que você visualize o que está acontecendo com suas entradas. 

Por padrão, a pasta de trabalho apresenta dois gráficos. Esses gráficos comparam o que está acontecendo com seus aplicativos agora, em comparação com o mesmo período de uma semana atrás. As linhas azuis são atuais e as linhas laranja são a semana anterior.

![Captura de tela mostrando grafos de integridade de entrada.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**O primeiro grafo é o uso por hora (número de usuários bem-sucedidos)**. Comparar o número atual de usuários bem-sucedidos a um período de uso típico ajuda você a identificar uma queda no uso que pode exigir investigação. Uma queda na taxa de uso bem-sucedida pode ajudar a detectar problemas de desempenho e utilização que a taxa de falha não pode. Por exemplo, se os usuários não conseguem acessar seu aplicativo para tentar entrar, não haveria nenhuma falha, apenas uma queda de uso. Uma consulta de exemplo para esses dados pode ser encontrada na seção a seguir.

**O segundo grafo é a taxa de falha por hora**. Um aumento na taxa de falha pode indicar um problema com os mecanismos de autenticação. A taxa de falha só pode ser medida se os usuários podem tentar autenticar. Se os usuários não conseguirem obter acesso para fazer a tentativa, as falhas não serão exibidas.

Você pode configurar um alerta que notifique um grupo específico quando a taxa de uso ou falha exceder um limite especificado. Uma consulta de exemplo para esses dados pode ser encontrada na seção a seguir.

## <a name="configure-the-query-and-alerts"></a>Configurar a consulta e os alertas

Você cria regras de alerta no Azure Monitor e pode executar automaticamente consultas salvas ou pesquisas de logs personalizadas em intervalos regulares.

Use as instruções a seguir para criar alertas de email com base nas consultas refletidas nos grafos. Scripts de exemplo abaixo enviarão uma notificação por email quando

* o uso bem-sucedido cai por 90% da mesma hora em dois dias atrás, como no grafo de uso por hora na seção anterior. 

* a taxa de falha aumenta em 90% da mesma hora em dois dias atrás, como no grafo de taxa de falha por hora na seção anterior. 

 Para configurar a consulta subjacente e definir alertas, conclua as etapas a seguir. Você usará a consulta de exemplo como base para sua configuração. Uma explicação da estrutura de consulta é exibida no final desta seção.

Para obter mais informações sobre como criar, exibir e gerenciar alertas de log usando Azure Monitor consulte [Gerenciar alertas de log](../../azure-monitor/alerts/alerts-log.md).

1. Na pasta de trabalho, selecione **Editar** e, em seguida, selecione o **ícone de consulta** logo acima do lado direito do grafo.   

   [![Captura de tela mostrando editar pasta de trabalho.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   O log de consultas é aberto.

   [![Captura de tela mostrando o log de consultas.](./media/monitor-sign-in-health-for-resilience/query-log.png)](./media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Copie um dos scripts de exemplo para uma nova consulta Kusto.  
   * [Consulta de Kusto para aumento na taxa de falha](#kusto-query-for-increase-in-failure-rate)
   * [Consulta de Kusto para remoção de uso](#kusto-query-for-drop-in-usage)

3. Cole a consulta na janela e selecione **executar**. Verifique se você vê a mensagem concluída mostrada na imagem abaixo e os resultados abaixo dessa mensagem.

   [![Captura de tela mostrando os resultados da consulta de execução.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Realce a consulta e selecione + **nova regra de alerta**. 
 
   [![Captura de tela mostrando a nova regra de alerta.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Configurar condições de alerta. Na seção condição, selecione o link **sempre que a pesquisa de log Personalizada média for maior que a contagem de lógica definida**. No painel configurar lógica de sinal, role até a lógica de alerta

   [![Captura de tela exibindo configuração de alertas.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Valor do limite**: 0. Esse valor emitirá um alerta sobre os resultados.

   * **Período de avaliação (em minutos)**: 2880. Esse valor é examinado em uma hora de hora

   * **Frequência (em minutos)**: 60. Esse valor define o período de avaliação como uma vez por hora para a hora anterior.

   * Selecione **Concluído**.

6. Na seção **ações** , defina estas configurações:  

    [![Captura de tela mostrando a página Criar regra de alerta.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * Em **ações**, escolha **selecionar grupo de ações** e adicione o grupo que você deseja que seja notificado sobre alertas.

   * Em **Personalizar ações** , selecione **alertas de email**.

   * Adicione uma **linha de assunto**.

7. Em **detalhes da regra de alerta**, defina estas configurações:

   * Adicione um nome descritivo e uma descrição.

   * Selecione o **grupo de recursos** ao qual o alerta será adicionado.

   * Selecione a **severidade** padrão do alerta.

   * Selecione **Habilitar Regra de alerta após a criação** se você quiser viver imediatamente, caso contrário, selecione **suprimir alertas**.

8. Selecione **Criar regra de alerta**.

9. Selecione **salvar**, insira um nome para a consulta, **salvar como uma consulta com uma categoria de alerta**. Em seguida, selecione **salvar** novamente.  

   [![Captura de tela mostrando o botão Salvar consulta.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)

### <a name="refine-your-queries-and-alerts"></a>Refine suas consultas e alertas

Modifique suas consultas e alertas para ter uma eficácia máxima.

* Certifique-se de testar seus alertas.

* Modifique a sensibilidade e a frequência do alerta para que você obtenha notificações importantes. Os administradores podem se tornar dessensibilizamos a alertas se eles obtiverem muitos e perderem algo importante. 

* Verifique se o email do qual os alertas são fornecidos nos clientes de email do administrador foi adicionado à lista de remetentes permitidos. Caso contrário, você pode perder notificações devido a um filtro de spam em seu cliente de email. 

* A consulta de alertas no Azure Monitor só pode incluir resultados das últimas 48 horas. [Essa é uma limitação atual por design](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="sample-scripts"></a>Scripts de exemplo

### <a name="kusto-query-for-increase-in-failure-rate"></a>Consulta de Kusto para aumento na taxa de falha

   A proporção na parte inferior pode ser ajustada conforme necessário e representa a porcentagem de alteração no tráfego na última hora em comparação com o mesmo tempo ontem. 0,5 significa que há uma diferença de 50% no tráfego.

```kusto

let today = SigninLogs

| where TimeGenerated > ago(1h) // Query failure rate in the last hour
 
| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

// Optionally filter by a specific application

//| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rowNumber = row_number();

let yesterday = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Query failure rate at the same time yesterday

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

// Optionally filter by a specific application

//| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h) // hourly failure rate at same time yesterday

| project TimeGenerated, failureRateYesterday = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rowNumber = row_number();
today
| join (yesterday) on rowNumber // join data from same time today and yesterday

| project TimeGenerated, failureRate, failureRateYesterday

// Set threshold to be the percent difference in failure rate in the last hour as compared to the same time yesterday

| where abs(failureRate - failureRateYesterday) > 0.5

```

### <a name="kusto-query-for-drop-in-usage"></a>Consulta de Kusto para remoção de uso

Na consulta a seguir, estamos comparando o tráfego na última hora ao mesmo tempo ontem.
Estamos excluindo sábado, domingo e segunda-feira porque eles são esperados nos dias em que haveria grande variabilidade no tráfego ao mesmo tempo do dia anterior. 

A proporção na parte inferior pode ser ajustada conforme necessário e representa a porcentagem de alteração no tráfego na última hora em comparação com o mesmo tempo ontem. 0,5 significa que há uma diferença de 50% no tráfego.

*Você deve ajustar esses valores para se ajustar ao seu modelo de operação de negócios*.

```Kusto
 let today = SigninLogs // Query traffic in the last hour

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

// Optionally filter by AppDisplayName to scope query to a single application

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr) // Count distinct users in the last hour

| sort by TimeGenerated desc

| serialize rn = row_number();

let yesterday = SigninLogs // Query traffic at the same hour yesterday

| where TimeGenerated between((ago(1h) - totimespan(1d))..(now() - totimespan(1d))) // Count distinct users in the same hour yesterday

| project TimeGenerated, AppDisplayName, UserPrincipalName

// Optionally filter by AppDisplayName to scope query to a single application

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersYesterday = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

today
| join // Join data from today and yesterday together
(
yesterday
)
on rn

// Calculate the difference in number of users in the last hour compared to the same time yesterday

| project TimeGenerated, users, usersYesterday, difference = abs(users - usersYesterday), max = max_of(users, usersYesterday)

 extend ratio = (difference * 1.0) / max // Ratio is the percent difference in traffic in the last hour as compared to the same time yesterday

// Day variable is the number of days since the previous Sunday. Optionally ignore results on Sat, Sun, and Mon because large variability in traffic is expected.

| extend day = dayofweek(now())

| where day != time(6.00:00:00) // exclude Sat

| where day != time(0.00:00:00) // exclude Sun

| where day != time(1.00:00:00) // exclude Mon

| where ratio > 0.7 // Threshold percent difference in sign-in traffic as compared to same hour yesterday

```

## <a name="create-processes-to-manage-alerts"></a>Criar processos para gerenciar alertas

Depois de configurar a consulta e os alertas, crie processos de negócios para gerenciar os alertas.

* Quem irá monitorar a pasta de trabalho e quando?

* Quando um alerta é gerado, quem vai investigar?

* Quais são as necessidades de comunicação? Quem vai criar as comunicações e quem as receberá?

* Se ocorrer uma interrupção, quais processos de negócios precisam ser disparados?

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre pastas de trabalho](../reports-monitoring/howto-use-azure-monitor-workbooks.md)
