---
title: Usar configurações de diagnóstico para cofres dos serviços de recuperação
description: Este artigo descreve como usar os eventos de diagnóstico novos e antigos para o backup do Azure.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575413"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Usar configurações de diagnóstico para cofres dos serviços de recuperação

O backup do Azure envia eventos de diagnóstico que podem ser coletados e usados para fins de análise, alertas e relatórios.

Você pode definir as configurações de diagnóstico para um cofre dos serviços de recuperação por meio do portal do Azure acessando o cofre e selecionando **configurações de diagnóstico**. Selecionar **+ Adicionar configuração de diagnóstico** permite que você envie um ou mais eventos de diagnóstico para uma conta de armazenamento, um hub de eventos ou um espaço de trabalho log Analytics.

![Painel Configurações de diagnóstico](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Eventos de diagnóstico disponíveis para usuários de backup do Azure

O backup do Azure fornece os seguintes eventos de diagnóstico. Cada evento fornece dados detalhados sobre um conjunto específico de artefatos relacionados ao backup:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

Se você ainda estiver usando o [evento herdado](#legacy-event) AzureBackupReport, recomendamos a alternância para o uso dos eventos acima.

Para obter mais informações, consulte [modelo de dados para eventos de diagnóstico de backup do Azure](./backup-azure-reports-data-model.md).

Os dados desses eventos podem ser enviados para uma conta de armazenamento, um espaço de trabalho Log Analytics ou um hub de eventos. Se você estiver enviando esses dados para um espaço de trabalho Log Analytics, selecione a opção **específico de recurso** na tela **configurações de diagnóstico** . Para obter mais informações, consulte as próximas seções.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Usar configurações de diagnóstico com Log Analytics

Agora você pode usar o backup do Azure para enviar dados de diagnóstico de cofre para tabelas de Log Analytics dedicadas para backup. Essas tabelas são chamadas [de tabelas específicas de recursos](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace).

Para enviar os dados de diagnóstico do cofre para Log Analytics:

1. Vá para seu cofre e selecione **configurações de diagnóstico**. Selecione **+ Adicionar configuração de diagnóstico**.
1. Dê um nome para a configuração de diagnóstico.
1. Marque a caixa de seleção **Enviar para log Analytics** e selecione um espaço de trabalho log Analytics.
1. Selecione **recurso específico** na alternância e selecione os seis eventos a seguir: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage** e **AddonAzureBackupProtectedInstance**.
1. Selecione **Salvar**.

   ![Modo específico do recurso](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Depois que os dados fluem para o espaço de trabalho Log Analytics, as tabelas dedicadas para cada um desses eventos são criadas no seu espaço de trabalho. Você pode consultar qualquer uma dessas tabelas diretamente. Você também pode executar junções ou uniões entre essas tabelas, se necessário.

> [!IMPORTANT]
> Os seis eventos, ou seja, CoreAzureBackup, AddonAzureBackupJobs, AddonAzureBackupAlerts, AddonAzureBackupPolicy, AddonAzureBackupStorage e AddonAzureBackupProtectedInstance, têm suporte *apenas* no modo específico do recurso em [relatórios de backup](./configure-reports.md). *Se você tentar enviar dados para esses seis eventos no modo de diagnóstico do Azure, nenhum dado ficará visível nos relatórios de backup.*

## <a name="legacy-event"></a>Evento herdado

Tradicionalmente, todos os dados de diagnóstico relacionados ao backup de um cofre estavam contidos em um único evento chamado AzureBackupReport. Os seis eventos descritos aqui são, em essência, uma decomposição de todos os dados contidos em AzureBackupReport.

Atualmente, continuamos a dar suporte ao evento AzureBackupReport para compatibilidade com versões anteriores nos casos em que os usuários têm consultas personalizadas existentes nesse evento. Os exemplos são alertas de log personalizados e visualizações personalizadas. *Recomendamos que você mova para os [novos eventos](#diagnostics-events-available-for-azure-backup-users) o mais cedo possível*. Os novos eventos:

* Torne os dados muito mais fáceis de trabalhar com as consultas de log.
* Fornecer melhor detecção de esquemas e sua estrutura.
* Melhorar o desempenho entre a latência de ingestão e os tempos de consulta.

*O evento herdado no modo de diagnóstico do Azure será eventualmente preterido. A escolha dos novos eventos pode ajudá-lo a evitar migrações complexas em uma data posterior*. Nossa [solução de relatório](./configure-reports.md) que usa log Analytics também interromperá o suporte a dados do evento herdado.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Etapas para mover para novas configurações de diagnóstico para um espaço de trabalho Log Analytics

1. Identifique quais cofres estão enviando dados para os espaços de trabalho de Log Analytics usando o evento herdado e as assinaturas às quais eles pertencem. Execute a consulta a seguir em cada um de seus espaços de trabalho para identificar esses cofres e assinaturas.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    Veja abaixo uma captura de tela da consulta que está sendo executada em um dos espaços de trabalho:

    ![Consulta de espaço de trabalho](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Use as [definições de Azure Policy internas](./azure-policy-configure-diagnostics.md) no backup do Azure para adicionar uma nova configuração de diagnóstico para todos os cofres em um escopo especificado. Essa política adiciona uma nova configuração de diagnóstico a cofres que não têm uma configuração de diagnóstico ou que têm apenas uma configuração de diagnóstico herdado. Essa política pode ser atribuída a uma assinatura inteira ou a um grupo de recursos de cada vez. Você deve ter acesso de proprietário a cada assinatura para a qual a política é atribuída.

Você pode optar por ter configurações de diagnóstico separadas para AzureBackupReport e os seis novos eventos até migrar todas as suas consultas personalizadas para usar dados das novas tabelas. A imagem a seguir mostra um exemplo de um cofre que tem duas configurações de diagnóstico. A primeira configuração, chamada **Setting1**, envia dados de um evento AzureBackupReport para um espaço de trabalho log Analytics no modo de diagnóstico do Azure. A segunda configuração, chamada **Setting2**, envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho log Analytics no modo específico do recurso.

![Duas configurações](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> O evento AzureBackupReport tem suporte *apenas* no modo de diagnóstico do Azure. *Se você tentar enviar dados para esse evento no modo específico do recurso, nenhum dado fluirá para o espaço de trabalho Log Analytics.*

> [!NOTE]
> A alternância para o **diagnóstico do Azure** ou o **recurso específico** aparecerá somente se o usuário selecionar **Enviar para log Analytics**. Para enviar dados para uma conta de armazenamento ou um hub de eventos, um usuário seleciona o destino necessário e marca as caixas de seleção para qualquer um dos eventos desejados, sem nenhuma entrada adicional. Novamente, recomendamos que você não escolha o evento herdado AzureBackupReport em frente.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Enviar Azure Site Recovery eventos para Log Analytics

Os eventos de backup e Azure Site Recovery do Azure são enviados do mesmo cofre dos serviços de recuperação. Atualmente, o Azure Site Recovery não está disponível para tabelas específicas de recursos. Os usuários que desejam enviar Azure Site Recovery eventos para Log Analytics são direcionados a usar *somente* o modo de diagnóstico do Azure, conforme mostrado na imagem. *Escolher o modo específico do recurso para eventos de Azure site Recovery impedirá que os dados necessários sejam enviados para o espaço de trabalho log Analytics*.

![Eventos de Site Recovery](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Para resumir:

* Se você já tiver Log Analytics diagnósticos configurados com Diagnóstico do Azure e tiver escrito consultas personalizadas sobre ele, mantenha essa configuração *intacta* até migrar suas consultas para usar dados dos novos eventos.
* Se você também quiser integrar novas tabelas, como recomendamos, crie uma **nova** configuração de diagnóstico, selecione específico do **recurso** e selecione os seis novos eventos.
* Se você estiver atualmente enviando Azure Site Recovery eventos para *log Analytics, não* escolha o modo específico do recurso para esses eventos. Caso contrário, os dados desses eventos não fluirão para seu espaço de trabalho do Log Analytics. Em vez disso, crie uma configuração de diagnóstico adicional, selecione **diagnóstico do Azure** e selecione os eventos de Azure site Recovery relevantes.

A imagem a seguir mostra um exemplo de um usuário que tem três configurações de diagnóstico para um cofre. A primeira configuração, chamada **Setting1**, envia dados de um evento AzureBackupReport para um espaço de trabalho log Analytics no modo de diagnóstico do Azure. A segunda configuração, chamada **Setting2**, envia dados dos seis novos eventos de backup do Azure para um espaço de trabalho log Analytics no modo específico do recurso. A terceira configuração, chamada **Setting3**, envia dados dos eventos de Azure site Recovery para um espaço de trabalho log Analytics no modo de diagnóstico do Azure.

![Três configurações](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>Próximas etapas

[Aprenda o modelo de dados de Log Analytics para os eventos de diagnóstico](./backup-azure-reports-data-model.md)
