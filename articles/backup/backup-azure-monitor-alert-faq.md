---
title: Perguntas frequentes de alerta de monitoramento de backup do Azure
description: 'Respostas a perguntas comuns sobre: O alerta de monitoramento do backup do Azure'
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: dc68b68ffa0ae2ee4f1bdd8f2c7ac0d7ff5ebb8c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465849"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Alerta de monitoramento do backup do Azure-perguntas frequentes
Este artigo responde a perguntas comuns sobre o alerta de monitoramento do Azure.

## <a name="configure-azure-backup-reports"></a>Configurar relatórios de Backup do Azure

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Como verificar se os dados do relatório começaram a entrar em uma conta de armazenamento?
Vá para a conta de armazenamento que você configurou e selecione os contêineres. Se o contêiner tem uma entrada para insights-logs-azurebackupreport, ele indica que dados de relatórios começaram a fluir.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Qual é a frequência do push de dados para uma conta de armazenamento e o pacote do conteúdo de Backup do Azure no Power BI?
  Para usuários do dia 0, leva cerca de 24 horas para enviar dados por push para uma conta de armazenamento. Após esse envio por push inicial ser concluído, os dados são atualizados com a frequência mostrada na figura abaixo.

  * Os dados relacionados a **Trabalhos**, **Alertas**, **Itens de backup**, **Cofres**, **Servidores protegidos** e **Políticas** são enviados por push para uma conta de armazenamento do cliente como e quando ela está conectada.

  * Os dados relacionados a **Armazenamento** são enviados por push para a conta de armazenamento do cliente a cada 24 horas.

       ![Frequência de push de dados de relatórios de Backup do Azure](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * O Power BI tem uma [atualização agendada uma vez por dia](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Você pode executar uma atualização manual dos dados no Power BI para o pacote de conteúdo.

### <a name="how-long-can-i-retain-reports"></a>Por quanto tempo é possível reter os relatórios?
Quando configura uma conta de armazenamento, você pode selecionar um período de retenção para dados de relatório na conta de armazenamento. Siga a etapa 6 na seção [Configurar conta de armazenamento para relatórios](backup-azure-configure-reports.md#configure-storage-account-for-reports). Além disso, você pode [analisar relatórios no excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) e salvá-los por um período de retenção mais longo, de acordo com as suas necessidades.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Todos os meus dados em relatórios serão exibidos após a configuração da conta de armazenamento?
 Todos os dados gerados após você configurar uma conta de armazenamento são enviados por push para a conta de armazenamento e estão disponíveis em relatórios. Os trabalhos em andamento não são enviados por push para a geração de relatórios. Depois que o trabalho é concluído ou falha, ele é enviado para relatórios.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Se eu já configurei a conta de armazenamento para exibir relatórios, posso alterar a configuração para usar outra conta de armazenamento?
Sim, você pode alterar a configuração para apontar para uma conta de armazenamento diferente. Use a conta de armazenamento recém-configurada ao conectar-se ao pacote de conteúdo de Backup do Azure. Além disso, depois que uma conta de armazenamento diferente for configurada, novos dados fluirão nesta conta de armazenamento. Mas os dados mais antigos (anteriores à alteração da configuração) permanecerão na conta de armazenamento mais antiga.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Posso exibir relatórios entre cofres e assinaturas?
Sim, você pode configurar a mesma conta de armazenamento entre vários cofres para exibir relatórios de cofre cruzado. Além disso, você pode configurar a mesma conta de armazenamento para cofres entre assinaturas. Você pode usar essa conta de armazenamento enquanto se conecta ao pacote de conteúdo de Backup do Azure no Power BI para exibir os relatórios. A conta de armazenamento selecionada deve estar na mesma região do cofre dos Serviços de Recuperação.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo leva para refletir no portal o status de trabalho do agente de backup do Azure?
O portal do Azure pode levar até 15 minutos para refletir o status de trabalho do agente de backup do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando um trabalho de backup falha, quanto tempo demora para gerar um alerta?
Um alerta é gerado em 20 minutos após a falha de backup do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há um caso em que um email não será enviado se as notificações forem configuradas?
Sim. Nas situações a seguir, notificações por email não serão enviadas.

* Se as notificações forem configuradas por hora e um alerta for gerado e resolvido em uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de backup falhou porque o trabalho de backup original estava em andamento

## <a name="recovery-services-vault"></a>Cofre dos Serviços de Recuperação

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Quanto tempo leva para refletir no portal o status de trabalho do agente de backup do Azure?
O portal do Azure pode levar até 15 minutos para refletir o status de trabalho do agente de backup do Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Quando um trabalho de backup falha, quanto tempo demora para gerar um alerta?
Um alerta é gerado em 20 minutos após a falha de backup do Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Há um caso em que um email não será enviado se as notificações forem configuradas?
Sim. Nas situações a seguir, notificações por email não serão enviadas.

* Se as notificações forem configuradas por hora e um alerta for gerado e resolvido em uma hora
* Quando um trabalho é cancelado
* Se um segundo trabalho de backup falhou porque o trabalho de backup original estava em andamento

## <a name="next-steps"></a>Próximas etapas

Leia as outras perguntas frequentes:

- [Perguntas comuns](backup-azure-vm-backup-faq.md) sobre backups de VM do Azure.
- [Perguntas comuns](backup-azure-file-folder-backup-faq.md) sobre o agente do Backup do Azure
