---
title: Perguntas frequentes sobre monitoramento de alertas e relatórios
description: Neste artigo, descubra respostas para perguntas comuns sobre o alerta de monitoramento de backup do Azure e os relatórios de backup do Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805872"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alerta de monitoramento do backup do Azure-perguntas frequentes

Este artigo responde a perguntas comuns sobre o monitoramento e o relatório de backup do Azure.

## <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Como fazer verificar se os dados de relatório começaram a fluir para um espaço de trabalho de Log Analytics (LA)?

Navegue até o espaço de trabalho de LA que você configurou. Navegue até o item de menu **logs** e execute a consulta `CoreAzureBackup | take 1` . Se você vir um registro que está sendo retornado, significa que os dados começaram a fluir para o espaço de trabalho. O push de dados inicial pode levar até 24 horas.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Qual é a frequência de envio de dados por push para um espaço de trabalho da LA?

Os dados de diagnóstico do cofre são enviados para o workspace do Log Analytics com um pouco de atraso. Cada evento chega ao workspace do Log Analytics de 20 a 30 minutos após ele ser enviado por push do cofre dos Serviços de Recuperação. Aqui estão mais detalhes sobre o atraso:

* Em todas as soluções, os alertas internos do serviço de backup são enviados por push assim que são criados. Eles geralmente aparecem no workspace do Log Analytics após 20 a 30 minutos.
* Em todas as soluções, os trabalhos de backup sob demanda e os trabalhos de restauração são enviados por push assim que são concluídos.
* Para todas as soluções, exceto backup do SQL, os trabalhos de backup agendados são enviados por push assim que são concluídos.
* Para o backup do SQL, como os backups de log podem ocorrer a cada 15 minutos, as informações para todos os trabalhos de backup agendados concluídos, incluindo logs, são colocadas em lote e enviadas por push a cada 6 horas.
* Em todas as soluções, outras informações, como o item de backup, a política, os pontos de recuperação, o armazenamento e assim por diante, são enviadas por push pelo menos uma vez por dia.
* Uma alteração na configuração de backup (como alteração de política ou edição de política) dispara um envio por push de todas as informações de backup relacionadas.

### <a name="how-long-can-i-retain-reporting-data"></a>Por quanto tempo posso manter os dados de relatórios?

Depois de criar um espaço de trabalho LA, você pode optar por manter os dados por um máximo de dois anos. Por padrão, um espaço de trabalho de LA retém dados por 31 dias.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Eu verá todos os meus dados em relatórios depois de configurar o espaço de trabalho LA?

 Todos os dados gerados depois que você define as configurações de diagnóstico são enviados por push para o espaço de trabalho LA e estão disponíveis em relatórios. Os trabalhos em andamento não são enviados por push para a geração de relatórios. Depois que o trabalho é concluído ou falha, ele é enviado para relatórios.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Posso exibir relatórios entre cofres e assinaturas?

Sim, você pode exibir relatórios entre cofres e assinaturas, bem como regiões. Seus dados podem residir em um único espaço de trabalho de LA ou em um grupo de espaços de trabalho de LA.

### <a name="can-i-view-reports-across-tenants"></a>Posso exibir relatórios entre locatários?

Se você for um usuário [Lighthouse do Azure](https://azure.microsoft.com/services/azure-lighthouse/) com acesso delegado aos espaços de trabalho assinaturas ou la de seus clientes, poderá usar relatórios de backup para exibir dados em todos os seus locatários.

## <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo leva para o status do trabalho do agente de backup do Azure refletir no portal?

O portal do Azure pode levar até 15 minutos para refletir o status do trabalho do agente de backup do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando um trabalho de backup falha, quanto tempo demora para gerar um alerta?

Um alerta é gerado dentro de 20 minutos após a falha do backup do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há um caso em que um email não será enviado se as notificações forem configuradas?

Sim. Nas seguintes situações, as notificações não são enviadas:

* Se as notificações forem configuradas por hora e um alerta for gerado e resolvido em uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de backup falhou porque o trabalho de backup original estava em andamento

## <a name="next-steps"></a>Próximas etapas

Leia as outras perguntas frequentes:

* [Perguntas comuns](backup-azure-vm-backup-faq.yml) sobre backups de VM do Azure.
* [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Backup do Azure
