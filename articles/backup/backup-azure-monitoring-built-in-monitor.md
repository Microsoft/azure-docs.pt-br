---
title: 'Serviço de Backup do Azure: Monitorar cargas de trabalho protegidas do backup do Azure'
description: Monitorar cargas de trabalho de backup do Azure usando portal do Azure
author: pvrk
manager: shivamg
keywords: Backup do Azure; Alertas
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: b41b32943aa0113a7653c8d2eb74fd04afb2e080
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465841"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorando cargas de trabalho de backup do Azure

O backup do Azure fornece várias soluções de backup com base no requisito de backup e na topologia de infraestrutura (local versus Azure). Qualquer usuário ou administrador de backup deverá ver o que está acontecendo em todas as soluções e deve ser notificado em cenários importantes. Este artigo detalha os recursos de monitoramento e notificação fornecidos pelo serviço de backup do Azure.

## <a name="backup-jobs-in-recovery-services-vault"></a>Trabalhos de backup no cofre dos serviços de recuperação

O backup do Azure fornece recursos de monitoramento e alerta criados para cargas de trabalho protegidas pelo backup do Azure. Nas configurações do cofre dos serviços de recuperação, a seção **monitoramento** fornece trabalhos e alertas internos.

![Monitoramento interno do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Os trabalhos são gerados quando as operações, como configurar backup, backup, restaurar, excluir backup e assim por diante, são executadas.

Os trabalhos das seguintes soluções de backup do Azure são mostrados aqui:

  - Backup da VM do Azure
  - Backup de arquivos do Azure
  - Backup de carga de trabalho do Azure, como SQL
  - Agente de Backup do Azure (MAB)

Os trabalhos do System Center Data Protection Manager (SC-DPM), Backup do Microsoft Azure Server (MABS) não são exibidos.

> [!NOTE]
> As cargas de trabalho do Azure, como backups SQL em VMs do Azure, têm um grande número de trabalhos de backup. Por exemplo, os backups de log podem ser executados a cada 15 minutos. Portanto, para essas cargas de trabalho de BD, somente as operações disparadas pelo usuário são exibidas. As operações de backup agendadas não são exibidas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertas de backup no cofre dos serviços de recuperação

Os alertas são principalmente cenários em que os usuários são notificados para que possam executar uma ação relevante. A seção **alertas de backup** mostra alertas gerados pelo serviço de backup do Azure. Esses alertas são definidos pelo serviço e o usuário não pode criar alertas personalizados.

### <a name="alert-scenarios"></a>Cenários de alerta
Os cenários a seguir são definidos pelo serviço como cenários de alerta.

  - Falhas de backup/restauração
  - Backup bem-sucedido com avisos para o Agente de Backup do Azure (MAB)
  - Interromper a proteção com reter dados/parar proteção com excluir dados

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceções quando um alerta não é gerado
Há algumas exceções quando um alerta não é gerado em uma falha, eles são:

  - O usuário cancelou explicitamente o trabalho em execução
  - O trabalho falha porque outro trabalho de backup está em andamento (nada para agir aqui, pois acabamos de aguardar a conclusão do trabalho anterior)
  - O trabalho de backup da VM falha porque a VM do Azure com backup não existe mais

As exceções acima foram projetadas desde a compreensão de que o resultado dessas operações (basicamente disparados pelo usuário) aparece imediatamente em clientes do portal/PS/CLI. Portanto, o usuário reconhece imediatamente e não precisa de uma notificação.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Os alertas das seguintes soluções de backup do Azure são mostrados aqui:

  - Backups de VM do Azure
  - Backups de arquivo do Azure
  - Backups de carga de trabalho do Azure, como SQL
  - Agente de Backup do Azure (MAB)

> [!NOTE]
> Os alertas do System Center Data Protection Manager (SC-DPM), Backup do Microsoft Azure Server (MABS) não são exibidos aqui.

### <a name="alert-types"></a>Tipos de alerta
Com base na severidade do alerta, os alertas podem ser definidos em três tipos:

  - **Crítica**: Em princípio, qualquer falha de backup ou recuperação (programada ou disparada pelo usuário) levaria à geração de um alerta e seria exibida como um alerta crítico e também operações destrutivas, como excluir backup.
  - **Aviso**: Se a operação de backup for bem sucedido, mas com poucos avisos, elas serão listadas como alertas de aviso.
  - **Informação**: A partir de hoje, nenhum alerta informativo é gerado pelo serviço de backup do Azure.

## <a name="notification-for-backup-alerts"></a>Notificação para alertas de backup

> [!NOTE]
> A configuração da notificação pode ser feita somente por meio do portal do Azure. Não há suporte para o suporte ao modelo PS/CLI/API REST/Azure Resource Manager.

Depois que um alerta é gerado, os usuários são notificados. O backup do Azure fornece um mecanismo de notificação embutido por email. É possível especificar endereços de email individuais ou listas de distribuição a serem notificadas quando um alerta for gerado. Você também pode escolher se deseja ser notificado para cada alerta individual ou agrupá-lo em um resumo por hora e, em seguida, ser notificado.

![Notificação de email interno do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando a notificação estiver configurada, você receberá um email de boas-vindas ou introdutório. Isso confirma que o backup do Azure pode enviar emails para esses endereços quando um alerta é gerado.<br>

Se a frequência foi definida como um resumo por hora e um alerta foi gerado e resolvido em uma hora, ele não fará parte do próximo resumo por hora.

> [!NOTE]
>
> * Se uma operação destrutiva, como **parar proteção com dados de exclusão** , for executada, um alerta será gerado e um email será enviado aos proprietários, administradores e coadministradores da assinatura, mesmo que as notificações não estejam configuradas para o cofre do serviço de recuperação.
> * Para configurar a notificação para trabalhos com êxito, use [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Próximas etapas

[Monitorar cargas de trabalho de backup do Azure usando Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
