---
title: Monitorar cargas de trabalho protegidas do backup do Azure
description: Neste artigo, saiba mais sobre os recursos de monitoramento e notificação para cargas de trabalho de backup do Azure usando o portal do Azure.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 74669a1347fac9f61d028d9cb1f3da174bb71f96
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550329"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorando cargas de trabalho de backup do Azure

O backup do Azure fornece várias soluções de backup com base no requisito de backup e na topologia de infraestrutura (local versus Azure). Qualquer usuário ou administrador de backup deve ver o que está acontecendo em todas as soluções e pode esperar ser notificado em cenários importantes. Este artigo detalha os recursos de monitoramento e notificação fornecidos pelo serviço de backup do Azure.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Itens de backup no cofre dos serviços de recuperação

Você pode monitorar todos os seus itens de backup por meio de um cofre dos serviços de recuperação. Navegar até a seção **itens de backup** no cofre abre uma exibição que fornece o número de itens de backup de cada tipo de carga de trabalho associado ao cofre. Clicar em qualquer linha abre uma exibição detalhada listando todos os itens de backup do tipo de carga de trabalho fornecido, com informações sobre o último status de backup de cada item, o último ponto de restauração disponível e assim por diante.

![Itens de backup do cofre RS](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> Para itens submetidos a backup no Azure usando o DPM, a lista mostrará todas as fontes de dados protegidas (disco e online) usando o servidor DPM. Se a proteção for interrompida para o DataSource com os dados de backup retidos, o DataSource ainda estará listado no Portal. Você pode ir até os detalhes da fonte de dados para ver se os pontos de recuperação estão presentes no disco, online ou em ambos. Além disso, as fontes de dados para as quais a proteção online é interrompida, mas são retidos, a cobrança pelos pontos de recuperação online continua até que os dados sejam completamente excluídos.
>
> A versão do DPM deve ser o DPM 1807 (5.1.378.0) ou o DPM 2019 (versão 10.19.58.0 ou superior) para que os itens de backup fiquem visíveis no portal do cofre dos serviços de recuperação.

## <a name="backup-jobs-in-recovery-services-vault"></a>Trabalhos de backup no cofre dos serviços de recuperação

O backup do Azure fornece recursos de monitoramento e alerta criados para cargas de trabalho protegidas pelo backup do Azure. Nas configurações do cofre dos serviços de recuperação, a seção **monitoramento** fornece trabalhos e alertas internos.

![Monitoramento interno do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Os trabalhos são gerados quando operações como configurar backup, fazer backup, restaurar, excluir backup e assim por diante, são executadas.

Os trabalhos das seguintes soluções de backup do Azure são mostrados aqui:

- Backup da VM do Azure
- Backup de arquivos do Azure
- Backup de carga de trabalho do Azure, como SQL e SAP HANA
- Agente de MARS (Serviços de Recuperação do Microsoft Azure)

Os trabalhos do System Center Data Protection Manager (SC-DPM), Backup do Microsoft Azure Server (MABS) não são exibidos.

> [!NOTE]
> As cargas de trabalho do Azure, como o SQL e os backups SAP HANA em VMs do Azure, têm um grande número de trabalhos de backup. Por exemplo, os backups de log podem ser executados a cada 15 minutos. Portanto, para cargas de trabalho de BD, somente as operações disparadas pelo usuário são exibidas. As operações de backup agendadas não são exibidas.

## <a name="backup-alerts-in-recovery-services-vault"></a>Alertas de backup no cofre dos serviços de recuperação

> [!NOTE]
> No momento, não há suporte para a exibição de alertas em cofres no centro de backup. Você precisa navegar até um cofre individual para exibir alertas para esse cofre.

Os alertas são principalmente cenários em que os usuários são notificados para que possam executar uma ação relevante. A seção **alertas de backup** mostra alertas gerados pelo serviço de backup do Azure. Esses alertas são definidos pelo serviço e o usuário não pode criar alertas personalizados.

### <a name="alert-scenarios"></a>Cenários de alerta

Os cenários a seguir são definidos pelo serviço como cenários de alerta.

- Falhas de backup/restauração
- Backup bem-sucedido com avisos para Serviços de Recuperação do Microsoft Azure (MARS) Agent
- Interromper a proteção com reter dados/parar proteção com excluir dados

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Os alertas das seguintes soluções de backup do Azure são mostrados aqui

- Backups de VM do Azure
- Backups de arquivo do Azure
- Backups de carga de trabalho do Azure, como SQL, SAP HANA
- Agente de MARS (Serviços de Recuperação do Microsoft Azure)

> [!NOTE]
> Os alertas do System Center Data Protection Manager (SC-DPM), Backup do Microsoft Azure Server (MABS) não são exibidos aqui.

### <a name="consolidated-alerts"></a>Alertas consolidados

Para soluções de backup de carga de trabalho do Azure, como SQL e SAP HANA, backups de log podem ser gerados com muita frequência (até a cada 15 minutos, de acordo com a política). Também é possível que as falhas de backup de log também sejam muito frequentes (até a cada 15 minutos). Nesse cenário, o usuário final será sobrecarregado se um alerta for gerado para cada ocorrência de falha. Portanto, um alerta é enviado para a primeira ocorrência e, se as falhas posteriores ocorrerem devido à mesma causa raiz, os alertas adicionais não serão gerados. O primeiro alerta é atualizado com a contagem de falhas. Mas se o alerta for desativado pelo usuário, a próxima ocorrência disparará outro alerta e isso será tratado como o primeiro alerta para essa ocorrência. É assim que o backup do Azure executa a consolidação de alertas para backups do SQL e do SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Exceções quando um alerta não é gerado

Há algumas exceções quando um alerta não é gerado em uma falha. Eles são:

- O usuário cancelou explicitamente o trabalho em execução
- O trabalho falha porque outro trabalho de backup está em andamento (nada para agir aqui, pois acabamos de aguardar a conclusão do trabalho anterior)
- O trabalho de backup da VM falha porque a VM do Azure com backup não existe mais
- [Alertas consolidados](#consolidated-alerts)

As exceções acima foram projetadas desde a compreensão de que o resultado dessas operações (basicamente disparados pelo usuário) aparece imediatamente em clientes do portal/PS/CLI. Portanto, o usuário reconhece imediatamente e não precisa de uma notificação.

### <a name="alert-types"></a>Tipos de alerta

Com base na severidade do alerta, os alertas podem ser definidos em três tipos:

- **Crítico**: em princípio, qualquer falha de backup ou recuperação (programada ou disparada pelo usuário) levaria à geração de um alerta e seria exibida como um alerta crítico e também operações destrutivas, como excluir backup.
- **Aviso**: se a operação de backup for bem sucedido, mas com poucos avisos, elas serão listadas como alertas de aviso. Os alertas de aviso estão disponíveis no momento apenas para backups do agente de backup do Azure.
- **Informação**: no momento, nenhum alerta informativo é gerado pelo serviço de backup do Azure.

## <a name="notification-for-backup-alerts"></a>Notificação para alertas de backup

> [!NOTE]
> A configuração da notificação pode ser feita somente por meio do portal do Azure. Não há suporte para o suporte ao modelo PS/CLI/API REST/Azure Resource Manager.

Depois que um alerta é gerado, os usuários são notificados. O backup do Azure fornece um mecanismo de notificação embutido por email. É possível especificar endereços de email individuais ou listas de distribuição a serem notificadas quando um alerta for gerado. Você também pode escolher se deseja ser notificado para cada alerta individual ou agrupá-lo em um resumo por hora e, em seguida, ser notificado.

![Notificação de email interno do cofre RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Quando a notificação estiver configurada, você receberá um email de boas-vindas ou introdutório. Isso confirma que o backup do Azure pode enviar emails para esses endereços quando um alerta é gerado.<br>

Se a frequência tiver sido definida como um resumo por hora e um alerta tiver sido gerado e resolvido em uma hora, ele não fará parte do próximo Resumo de hora.

> [!NOTE]
>
> - Se uma operação destrutiva, como **parar proteção com dados de exclusão** , for executada, um alerta será gerado e um email será enviado para proprietários, administradores e coadministradores de assinatura, mesmo que as notificações não estejam configuradas para o cofre dos serviços de recuperação.
> - Para configurar a notificação para trabalhos com êxito, use [log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Inativando alertas

Para desativar/resolver um alerta ativo, você pode selecionar o item de lista correspondente ao alerta que deseja desativar. Isso abre uma tela que exibe informações detalhadas sobre o alerta, com um botão de **inativação** na parte superior. A seleção desse botão irá alterar o status do alerta para **inativo**. Você também pode desativar um alerta clicando com o botão direito do mouse no item de lista correspondente ao alerta e selecionando **inativo**.

![Inativação de alerta do cofre RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Próximas etapas

[Monitorar cargas de trabalho de backup do Azure usando Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
