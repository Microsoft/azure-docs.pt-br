---
title: Como atualizar regras de alerta ou regras de ação quando o recurso de destino for movido para uma região diferente do Azure
description: Plano de fundo e instruções sobre como atualizar regras de alerta ou regras de ação quando o recurso de destino for movido para uma região diferente do Azure.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.subservice: alerts
ms.openlocfilehash: 36cf5882913426062e281194b61a8c760141512a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944155"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Como atualizar regras de alerta ou regras de ação quando o recurso de destino for movido para uma região diferente do Azure

Este artigo descreve por que [as regras de alerta](./alerts-overview.md) e regras de [ação](./alerts-action-rules.md) existentes podem ser afetadas quando você move outros recursos do Azure entre regiões e como identificar e resolver esses problemas. Verifique a [documentação de movimentação de recursos](../../azure-resource-manager/management/move-region.md) principais para obter informações adicionais sobre quando o recurso é movido entre regiões úteis e uma lista de verificação de criação de um processo de movimentação.

## <a name="why-the-problem-exists"></a>Por que o problema existe

Regras de alerta e regras de ação fazem referência a outros recursos do Azure. Os exemplos incluem [VMs do Azure](../../site-recovery/azure-to-azure-tutorial-migrate.md), [SQL do Azure](../../azure-sql/database/move-resources-across-regions.md)e [armazenamento do Azure](../../storage/common/storage-account-move.md). Quando você move os recursos aos quais essas regras se referem, é provável que as regras parem de funcionar corretamente porque não conseguem encontrar os recursos que eles referenciam.

Há dois motivos principais pelos quais suas regras podem parar de funcionar depois de mover os recursos de destino:

- O escopo da sua regra é referenciar explicitamente o recurso antigo.
- Sua regra de alerta é baseada em métricas.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>O escopo da regra se refere explicitamente ao recurso antigo

Quando você move um recurso, sua ID de recurso é alterada na maioria dos casos. Nos bastidores, o sistema Replica o recurso na nova região antes de excluí-lo da região antiga. Esse processo requer que dois recursos e, portanto, duas IDs de recurso diferentes existam simultaneamente por um curto período de tempo. Como as IDs de recurso devem ser exclusivas, uma nova ID deve ser criada durante o processo. 

**Como mover o recurso afeta as regras existentes?**

Regras de alerta e regras de ação têm um escopo de recursos aos quais se aplicam. O escopo pode ser uma assinatura inteira, um grupo de recursos ou um ou mais recursos específicos.
Por exemplo, aqui está uma regra com um escopo com dois recursos (duas máquinas virtuais):

![Regra de alerta de vários recursos](media/alerts-resource-move/multi-resource-alert-rule.png)

Se o escopo da regra mencionar explicitamente um recurso e esse recurso tiver movido e alterado sua ID de recurso, essa regra procurará por um recurso incorreto ou inexistente e, portanto, falhará.

**Como corrigir o problema?**

Atualize ou recrie a regra afetada para apontar para o novo recurso. O processo para atualizar o escopo é encontrado posteriormente neste artigo.

O problema se aplica a esses tipos de regra:

- Regras de alerta do log de atividades
- Regras de ação
- Alertas clássicos
- Alertas de métrica – para obter mais informações, consulte a próxima seção [regras de alerta com base em métricas](#alert-rules-based-on-metrics).

> [!NOTE]
> As regras de alerta de pesquisa de log e as regras de alerta do detector inteligente não são afetadas porque seu escopo é um espaço de trabalho ou Application Insights. Nenhum desses escopos dá suporte atualmente a movimentações de região.

## <a name="alert-rules-based-on-metrics"></a>Regras de alerta com base em métricas

As métricas que os recursos do Azure emitem são regionais. Sempre que um recurso é movido para uma nova região, ele começa a emitir suas métricas nessa nova região. Como resultado, todas as regras de alerta baseadas em métricas precisam ser atualizadas ou recriadas para que apontem para o fluxo de métrica atual na região correta.

Essa explicação se aplica a regras de [alerta de métrica](alerts-metric-overview.md) e a regras de alerta de [teste de disponibilidade](../app/monitor-web-app-availability.md).

Se **todos** os recursos no escopo tiverem sido movidos, você não precisará recriar a regra. Você pode apenas atualizar qualquer campo da regra de alerta, como a descrição da regra de alerta, e salvá-la.
Se **apenas alguns** dos recursos no escopo tiverem sido movidos, você precisará remover os recursos movidos da regra existente e criar uma nova regra que cubra somente os recursos movidos.

## <a name="procedures-to-fix-problems"></a>Procedimentos para corrigir problemas

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Identificando regras associadas a um recurso movido da portal do Azure

- **Para regras de alerta** -navegue até alertas > gerenciar regras de alerta > filtrar pela assinatura que o contém e o recurso movido.
> [!NOTE]
> As regras de alerta do log de atividades não dão suporte a esse processo. Não é possível atualizar o escopo de uma regra de alerta do log de atividades e fazer com que ela aponte para um recurso em outra assinatura. Em vez disso, você pode criar uma nova regra que substituirá a antiga.

- **Para regras de ação** -navegue até alertas > gerenciar ações > regras de ação (versão prévia) > filtrar pela assinatura que o contém e o recurso movido.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Alterar o escopo de uma regra do portal do Azure

1. Abra a regra que você identificou na etapa anterior clicando nela.
2. Em **recurso**, clique em **Editar** e ajuste o escopo, conforme necessário.
3. Ajuste outras propriedades da regra, conforme necessário.
4. Clique em **Salvar**.

![Alterar escopo da regra de alerta](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Alterar o escopo de uma regra usando modelos de Azure Resource Manager

1. Obtenha o modelo de Azure Resource Manager da regra.  Para exportar o modelo de uma regra do portal do Azure:
   1. Navegue até a seção grupos de recursos no portal e abra o grupo de recursos que contém a regra.
   2. Na seção visão geral, marque a caixa de seleção **Mostrar tipo oculto** e filtre pelo tipo relevante da regra.
   3. Selecione a regra relevante para exibir seus detalhes.
   4. Em **configurações**, selecione **Exportar modelo**.
2. Modifique o modelo. Se necessário, divida em duas regras (relevantes para alguns casos de alertas de métrica, conforme observado acima).
3. Reimplante o modelo.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Alterar o escopo de uma regra usando a API REST

1. Obter a regra existente ([alertas de métrica](/rest/api/monitor/metricalerts/get), [alertas do log de atividades](/rest/api/monitor/activitylogalerts/get))
2. Modificar o escopo ([alertas do log de atividades](/rest/api/monitor/activitylogalerts/update))
3. Reimplantar a regra ([alertas de métrica](/rest/api/monitor/metricalerts/createorupdate), [alertas do log de atividades](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Alterar o escopo de uma regra usando o PowerShell

1. Obtenha a regra existente ([alertas de métrica](/powershell/module/az.monitor/get-azmetricalertrulev2), [alertas do log de atividades](/powershell/module/az.monitor/get-azactivitylogalert), regras de [ação](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Modifique o escopo. Se necessário, divida em duas regras (relevantes para alguns casos de alertas de métrica, conforme observado acima).
3. Reimplante a regra ([alertas de métrica](/powershell/module/az.monitor/add-azmetricalertrulev2), [alertas do log de atividades](/powershell/module/az.monitor/enable-azactivitylogalert), regras de [ação](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Alterar o escopo de uma regra usando CLI do Azure

1.  Obtenha a regra existente ([alertas de métrica](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show), [alertas do log de atividades](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)).
2.  Atualizar o escopo da regra diretamente ([alertas de métrica](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update), alertas do log de [atividades](/cli/azure/monitor/activity-log/alert/scope))
3.  Se necessário, divida em duas regras (relevantes para alguns casos de alertas de métrica, conforme observado acima).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como corrigir outros problemas com [notificações de alerta](alerts-troubleshoot.md), [alertas de métrica](alerts-troubleshoot-metric.md)e alertas de [log](alerts-troubleshoot-log.md). 
