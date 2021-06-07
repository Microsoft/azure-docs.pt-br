---
title: Enviar relatórios de backup do Azure
description: Criar tarefas automatizadas para receber relatórios periódicos por email
ms.topic: conceptual
ms.date: 03/01/2021
ms.openlocfilehash: d3ec76fa1a23657578979e65c0e54bb428722244
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105561331"
---
# <a name="email-azure-backup-reports"></a>Enviar relatórios de backup do Azure

Usando o recurso de **relatório de email** disponível em relatórios de backup, você pode criar tarefas automatizadas para receber relatórios periódicos por email. Esse recurso funciona implantando um aplicativo lógico em seu ambiente do Azure que consulta dados de seus espaços de trabalho selecionados de Log Analytics (LA), com base nas entradas que você fornecer. [Saiba mais sobre os aplicativos lógicos e seus preços](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="getting-started"></a>Introdução

Para configurar tarefas de email por meio de relatórios de backup, execute as seguintes etapas:

1.  Navegue até o **centro de backup**  >  **relatórios de backup** e clique na guia relatório de **email** .
2.  Crie uma tarefa especificando as seguintes informações:
    * **Detalhes da tarefa** -o nome do aplicativo lógico a ser criado e a assinatura, o grupo de recursos e o local em que ele deve ser criado. Observe que o aplicativo lógico pode consultar dados entre várias assinaturas, grupos de recursos e locais (conforme selecionado na seção filtros de relatório), mas é criado no contexto de uma única assinatura, grupo de recursos e local.
    * **Dados a serem exportados** -a guia que você deseja exportar. Você pode criar um único aplicativo de tarefa por guia ou enviar por email todas as guias usando uma única tarefa, selecionando a opção **todas as guias** .
    * **Opções de email**: a frequência de email, as IDs de email do destinatário e o assunto do email.

    ![Guia email](./media/backup-azure-configure-backup-reports/email-tab.png)

3.  Depois de clicar em **Enviar** e **confirmar**, o aplicativo lógico será criado. O aplicativo lógico e as conexões de API associadas são criados com a marca **UsedByBackupReports: true** para facilitar a descoberta. Você precisará executar uma única etapa de autorização para que o aplicativo lógico seja executado com êxito, conforme descrito na seção abaixo.

## <a name="authorize-connections-to-azure-monitor-logs-and-office-365"></a>Autorizar conexões a Azure Monitor logs e Office 365

O aplicativo lógico usa o conector [azuremonitorlogs](/connectors/azuremonitorlogs/) para consultar os espaços de trabalho da la e usa o conector do [Outlook](/connectors/office365connector/) para enviar emails. Você precisará executar uma autorização única para esses dois conectores. 
 
Para executar a autorização, siga as etapas abaixo:

1.  Navegue até **aplicativos lógicos** na portal do Azure.
2.  Procure o nome do aplicativo lógico que você criou e navegue até o recurso.

    ![Aplicativos Lógicos](./media/backup-azure-configure-backup-reports/logic-apps.png)

3.  Clique no item de menu **conexões de API** .

    ![Conexões de API](./media/backup-azure-configure-backup-reports/api-connections.png)

4.  Você verá duas conexões com o formato `<location>-azuremonitorlogs` e `<location>-office365` -isto é, _lesteus-azuremonitorlogs_ e _lesteus-Office365_.
5.  Navegue até cada uma dessas conexões e selecione o item de menu **Editar conexão de API** . Na tela exibida, selecione **autorizar** e salve a conexão quando a autorização for concluída.

    ![Autorizar conexão](./media/backup-azure-configure-backup-reports/authorize-connections.png)

6.  Para testar se o aplicativo lógico funciona após a autorização, você pode navegar de volta para o aplicativo lógico, abrir **visão geral** e selecionar **executar gatilho** no painel superior, para testar se um email está sendo gerado com êxito.

## <a name="contents-of-the-email"></a>Conteúdo do email

* Todos os gráficos mostrados no portal estão disponíveis como conteúdo embutido no email.
* As grades mostradas no portal estão disponíveis como anexos *. csv no email.
* Os dados mostrados no email usam todos os filtros de nível de relatório selecionados pelo usuário no relatório, no momento da criação da tarefa de email.
* Filtros de nível de tabulação, como **nome da instância de backup**, nome da **política** e assim por diante, não são aplicados. A única exceção é a grade **otimizações de retenção** na guia **otimizar** , em que os filtros para retenção **diária**, **semanal**, **mensal** e **anual** são aplicados.
* O intervalo de tempo e o tipo de agregação (para gráficos) são baseados na seleção de intervalo de tempo do usuário nos relatórios. Por exemplo, se a seleção de intervalo de tempo for durar 60 dias (traduzir para o tipo de agregação semanal) e a frequência de email for diária, o destinatário receberá um email todos os dias com gráficos que abrangem os dados obtidos durante o último período de 60 dias, com dados agregados em um nível semanal.

## <a name="troubleshooting-issues"></a>Solução de problemas

Se você não estiver recebendo emails conforme o esperado mesmo após a implantação bem-sucedida do aplicativo lógico, siga as etapas abaixo para solucionar problemas de configuração:

### <a name="scenario-1-receiving-neither-a-successful-email-nor-an-error-email"></a>Cenário 1: não recebendo um email de êxito nem um email de erro

* Esse problema pode estar ocorrendo porque o conector da API do Outlook não está autorizado. Para autorizar a conexão, siga as etapas de autorização fornecidas acima.

* Esse problema também pode estar ocorrendo se você tiver especificado um destinatário de email incorreto ao criar o aplicativo lógico. Para verificar se o destinatário do email foi especificado corretamente, você pode navegar para o aplicativo lógico no portal do Azure, abrir o designer do aplicativo lógico e selecionar etapa de email para ver se as IDs de email corretas estão sendo usadas.

### <a name="scenario-2-receiving-an-error-email-that-says-that-the-logic-app-failed-to-execute-to-completion"></a>Cenário 2: recebendo um email de erro que diz que o aplicativo lógico falhou ao ser executado até a conclusão

Para solucionar o problema:
1.  Navegue até o aplicativo lógico no portal do Azure.
2.  Na parte inferior da tela de **visão geral** , você verá uma seção de **histórico de execuções** . Você pode abrir na execução mais recente e exibir quais etapas do fluxo de trabalho falharam. Algumas causas possíveis podem ser:
    * O **conector de logs de Azure monitor não foi autorizado**: para corrigir esse problema, siga as etapas de autorização conforme fornecido acima.
    * **Erro na consulta la**: caso você tenha personalizado o aplicativo lógico com suas próprias consultas, um erro em qualquer uma das consultas de la pode estar fazendo com que o aplicativo lógico falhe. Você pode selecionar a etapa relevante e exibir o erro que está fazendo com que a consulta seja executada incorretamente.

Se os problemas persistirem, entre em contato com o suporte da Microsoft.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre os relatórios de backup](./configure-reports.md)