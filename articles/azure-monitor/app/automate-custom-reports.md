---
title: Automatizar relatórios personalizados com dados do Azure Application Insights
description: Automatizar relatórios personalizados diariamente/semanalmente/mensamente com dados do Azure Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/25/2018
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: 37de2de18656f0f8ba85495b3fd72315e0bd885b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60903747"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatizar relatórios personalizados com dados do Azure Application Insights

Relatórios periódicos ajudam a manter uma equipe informada sobre como seus serviços críticos de negócios estão indo. Desenvolvedores, equipes de DevOps/SRE e seus gerentes podem ser produtivos com relatórios automatizados entregando confiáveis insights sem a necessidade de todas as pessoas fazerem logon no portal. Tais relatórios podem também ajudar a identificar aumentos graduais dos níveis de latência, carregamento ou falha que podem não disparar nenhuma regra de alerta.

Cada empresa tem suas próprias necessidades de relatório, tais como: 

* Agregações percentis específicas de métricas, ou métricas personalizadas em um relatório.
* Ter diferentes relatórios diariamente, semanalmente e mensalmente para roll-ups de dados para diferentes públicos.
* Segmentação por atributos personalizados como região, ou ambiente. 
* Agrupar alguns recursos IA juntos em um único relatório, mesmo se eles talvez estejam em diferentes assinaturas ou grupos de recursos etc.
* Separar relatórios contendo métricas confidenciais enviados para públicos confidenciais.
* Relatórios para stakeholders que talvez não tenham acesso aos recursos do portal.

> [!NOTE] 
> O e-mail de resumo semanal do Application Insights não permite qualquer personalização, e será descontinuado em favor das opções de personalização listadas abaixo. O último e-mail de resumo semanal será enviado em 11 de junho de 2018. Por favor configure uma das seguintes opções para obter relatórios personalizados parecidos (use a consulta sugerida abaixo).

## <a name="to-automate-custom-report-emails"></a>Para automatizar e-mails de relatório personalizados

Você pode [programaticamente consultar os dados do Application Insights](https://dev.applicationinsights.io/) para gerar relatórios personalizados de forma agendada. As seguintes opções podem ajudá-lo a começar rapidamente:

* [Automatize relatórios com o Microsoft Flow](automate-with-flow.md)
* [Automatize relatórios com Aplicativos Lógicos](automate-with-logic-apps.md)
* Use o modelo da [função do Azure](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) “resumo de mensagem agendado do Application Insights” no cenário de Monitoramento. Esta função usa SendGrid para enviar o e-mail. 

    ![Modelo de função do Azure](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Exemplo de consulta para um e-mail de resumo semanal
A consulta seguinte mostra a junção de vários conjuntos de dados para um e-mail de resumo semanal como um relatório. Personalize isso conforme necessário e use isso com quaisquer das opções listadas acima para automatizar um relatório semanal.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Relatório de resumo agendado de Application Insights

1. No Portal do Azure, selecione **Criar um recurso** > **Computação** > **Aplicativo de funções**.

   ![Criar um screenshot do Azure Resource Function App](./media/automate-custom-reports/function-app-01.png)

2. Entrar informação apropriada para o seu aplicativo e selecionar _Criar_. (Application Insights _Ligado_ é necessário somente se você quiser monitorar seu novo Aplicativo de Função com o Application Insights)

   ![Criar um screenshot do Azure Resource Function App](./media/automate-custom-reports/function-app-02.png)

3. Uma vez que seu novo Aplicativo de Função tiver completado a implantação, selecione **Vá para recurso**.

4. Selecionar **Nova Função**.

   ![Criar um novo screenshot de funções](./media/automate-custom-reports/function-app-03.png)

5. Selecionar **_Modelo de resumo agendado de Application Insights_**.

   ![Nova Função screenshot de Modelo do Application Insights](./media/automate-custom-reports/function-app-04.png)

6. Entre um endereço de email recipiente apropriado para o seu relatório e selecione **Criar**.

   ![Screenshot de Configurações de Função](./media/automate-custom-reports/function-app-05.png)

7. Selecione **Aplicativo Função** > **Recursos da plataforma** > **Configurações de aplicativo**.

    ![Configurações de aplicativo do Azure](./media/automate-custom-reports/function-app-07.png)

8. Crie três novas configurações do aplicativo com valores correspondentes apropriados ``AI_APP_ID``, ``AI_APP_KEY``, e ``SendGridAPI``. Clique em **Salvar**.

     ![Captura de tela de interface de integração função](./media/automate-custom-reports/function-app-08.png)
    
    (Os valores AI_ podem ser encontrados em acesso de API para o recurso do Application Insights que deseja relatar. Se você não tiver uma Chave API de Application Insights, há a opção **Criar Chave API**.)
    
   * AI_APP_ID = Application ID
   * AI_APP_KEY = API Key
   * SendGridAPI =SendGrid API Key

     > [!NOTE]
     > Se não tiver uma conta SendGrip, você poderá criar uma. Documentação SendGrip para Funções do Azure está [aqui](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Se você quiser uma explicação mínima sobre como configurar SendGrip e gerar uma chave API, ela será fornecida no final deste artigo. 

9. Selecionar **Integrar** e sob Saídas clique **SendGrip ($retorno)**.

     ![Captura de tela de saída](./media/automate-custom-reports/function-app-09.png)

10. Sob **Configuração de Aplicativo Chave SendGridAPI**, selecione a sua nova Configuração de Aplicativo criada para **SendGridAPI**.

     ![Execute a captura de tela do aplicativo de função](./media/automate-custom-reports/function-app-010.png)

11. Executar e testar o aplicativo de funções.

     ![Testar captura de tela](./media/automate-custom-reports/function-app-11.png)

12. Verificar seu e-mail para confirmar que a mensagem foi enviada/recebida.

     ![screenshot de linha do assunto do e-mail](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrip com Azure

Este passos apenas aplicam se você não tiver já uma conta SendGrip configurada.

1. Do portal do Azure selecione **Criar um recurso** procure por **Entrega de Email SendGrip** > Clique **Criar** > e preencha as instruções de criar específicas do SendGrip. 

     ![Criar Screenshot de Recursos do SendGrip](./media/automate-custom-reports/function-app-13.png)

2. Uma vez criado sob Contas SendGrip selecione **Gerenciar**.

     ![Screenshot Chave API Configurações](./media/automate-custom-reports/function-app-14.png)

3. Isto inicializará o site SendGrip. Selecione **Configurações** > **Chaves API**.

     ![Criar e Ver Screenshot de Aplicativo de Chave API](./media/automate-custom-reports/function-app-15.png)

4. Criar uma Chave API > escolher **Criar & Ver** (Por favor rever a documentação SendGrip sobre acesso restrito para determinar que nível de permissões é apropriado para sua Chave API. Acesso completo é selecionado aqui por questões de exemplo apenas.)

   ![Screenshot de acesso completo](./media/automate-custom-reports/function-app-16.png)

5. Copie a chave inteira, este valor é o que você precisa para suas configurações de Aplicativo de Função como valor para SendGripAPI

   ![Copiar screenshot chave API](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como criar [consultas do Analytics](../../azure-monitor/log-query/get-started-queries.md).
* Saiba mais sobre [dados consultando programaticamente do Application Insights](https://dev.applicationinsights.io/)
* Saiba mais sobre o [Aplicativos Lógicos](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Saiba mais sobre o [Microsoft Flow](https://ms.flow.microsoft.com).
