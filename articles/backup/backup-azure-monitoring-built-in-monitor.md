---
title: 'Serviço de Backup do Azure: Cargas de trabalho protegidas do Backup do Azure Monitor'
description: Monitorar cargas de trabalho de Backup do Azure usando o portal do Azure
services: backup
author: pvrk
manager: shivamg
keywords: Backup do Azure; Alertas;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 8d3e3257f16fe4e0f846c2268bfefc2771387de6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809040"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitoramento de cargas de trabalho de Backup do Azure

O Backup do Azure fornece várias soluções de backup com base no requisito e a infraestrutura de topologia de backup (no local vs do Azure). Qualquer administrador ou usuário de backup deve ver o que está acontecendo em todas as soluções e deve ser notificado em cenários importantes. Este artigo fornece detalhes sobre os recursos de monitoramento e notificação fornecidos pelo serviço Backup do Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Trabalhos de backup no cofre dos serviços de recuperação

O Backup do Azure fornece internos de monitoramento e alertas sobre a capacidade para cargas de trabalho que estão sendo protegidas pelo Backup do Azure. Em serviços de recuperação do Cofre de configurações, o **monitoramento** seção fornece alertas e trabalhos internos.

![Monitoramento embutida do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Trabalhos são gerados quando são executadas operações como a configuração de backup, backup, restauração, excluir backup e assim por diante.

Trabalhos de soluções de Backup do Azure a seguir são mostrados aqui:

  - Backup da VM do Azure
  - Backup de arquivos do Azure
  - Backup de carga de trabalho do Azure, como SQL
  - Agente de Backup do Azure (MAB)

Trabalhos do System Center Data Protection Manager (SC DPM), servidor de Backup do Microsoft Azure (MABS) não são exibidos.

> [!NOTE]
> Cargas de trabalho do Azure, como backups SQL nas VMs do Azure tem um grande número de trabalhos de backup. Por exemplo, os backups de log podem executar para cada 15 minutos. Portanto, para essas cargas de trabalho de banco de dados, operações de apenas o usuário disparada são exibidas. Operações de backup agendadas não são exibidas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertas de backup no cofre dos serviços de recuperação

Os alertas são principalmente a cenários em que os usuários são notificados para que eles podem realizar ação relevante. O **alertas de Backup** seção mostra alertas gerados pelo serviço de Backup do Azure. Esses alertas são definidos pelo serviço e o usuário não conseguir personalizar criar todos os alertas.

### <a name="alert-scenarios"></a>Cenários de alerta
Os cenários a seguir são definidos pelo serviço como cenários podem gerar alertas.

  - Falhas de backup/restauração
  - Backup bem-sucedido com avisos
  - Interrompa a proteção com reter dados/parar a proteção com excluir dados

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceções quando um alerta não será gerado.
Há algumas exceções quando um alerta não é gerado em uma falha, que são:

  - Usuário cancelado explicitamente o trabalho em execução
  - O trabalho falhar porque outro trabalho de backup está em andamento (nada para agir em aqui, já que temos apenas aguardar o conclusão do trabalho anterior)
  - O trabalho de backup da VM falhará porque a VM do Azure backup não existe mais

As exceções acima são projetadas desde o entendimento de que o resultado dessas operações (principalmente usuário disparado) aparece imediatamente em clientes do portal/PS/CLI. Portanto, o usuário está ciente imediatamente e não precisa de uma notificação.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Alertas de soluções de Backup do Azure a seguir são mostrados aqui:

  - Backups VM do Azure
  - Backups de arquivo do Azure
  - Backups de carga de trabalho do Azure, como SQL
  - Agente de Backup do Azure (MAB)

> [!NOTE]
> Alertas do System Center Data Protection Manager (SC DPM), servidor de Backup do Microsoft Azure (MABS) não são mostrados aqui.

### <a name="alert-types"></a>Tipos de alerta
Com base na severidade do alerta, os alertas podem ser definidos em três tipos:

  - **Crítica**: Em princípio, qualquer backup ou recuperação de falha (agendado ou disparado do usuário) poderia levar a geração de um alerta e será mostrada como um alerta crítico e também operações destrutivas, como excluir backup.
  - **Aviso**: Se a operação de backup for bem-sucedida, mas com alguns avisos, eles são listados como alertas de aviso.
  - **Informativo**: A partir de hoje, nenhum alerta informativo é gerada pelo serviço de Backup do Azure.

## <a name="notification-for-backup-alerts"></a>Notificação de alertas de Backup

> [!NOTE]
> Configuração de notificação pode ser feita somente por meio do Portal do Azure. Não há suporte para o suporte de modelo de Gerenciador de recursos do Azure API/CLI/PS/REST.

Depois que um alerta é gerado, os usuários são notificados. O Backup do Azure fornece um mecanismo de notificação embutida via email. É possível especificar endereços de email individuais ou listas de distribuição para ser notificado quando um alerta é gerado. Você também pode escolher se deseja ser notificado de todos os alertas individuais ou agrupá-los em um resumo por hora e, em seguida, ser notificado.

![Notificação por email cofre RS embutida](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando a notificação está configurada, você receberá um email de boas-vindo ou introdutório. Isso confirma que o Backup do Azure pode enviar emails para esses endereços quando um alerta é gerado.<br>

Se a frequência foi definida como um resumo de hora em hora e um alerta foi gerado e resolvido em uma hora, não é uma parte do resumo de hora em hora futura.

> [!NOTE]
> 
> * Se uma operação destrutiva, como **parar a proteção com excluir dados** é executada, um alerta é gerado e um email é enviado para proprietários de assinatura, os administradores e coadministradores, mesmo se as notificações não forem configuradas para o serviço de recuperação cofre.
> * Para configurar a notificação para trabalhos com êxito, use [do Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Próximas etapas

[Monitorar o Azure backup cargas de trabalho usando o Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
