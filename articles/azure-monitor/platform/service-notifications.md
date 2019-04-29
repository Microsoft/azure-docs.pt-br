---
title: O que são as notificações de integridade do serviço do Azure?
description: As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pelo Microsoft Azure.
author: dkamstra
services: monitoring
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 4/12/2018
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: ab4aa2f37d5a883c83b8ee09b5de6551ebf13d2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883590"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Exibir as notificações de integridade do serviço usando o Portal do Azure

As notificações de integridade do serviço são publicadas pelo Azure e contêm informações sobre os recursos em sua assinatura. Essas notificações são uma subclasse dos eventos do log de atividades e também podem ser encontradas no log de atividades. As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Há diversas classes de notificações de integridade do serviço:  

- **Ação necessária:** o Azure poderá notar algo incomum acontecer em sua conta e trabalhar com você para corrigir isso. O Azure lhe envia uma notificação detalhando as ações que você precisa tomar ou sobre como contatar o suporte ou a engenharia do Azure.  
- **Recuperação assistida**: um evento ocorreu e os engenheiros confirmaram que você ainda está sofrendo um impacto. A engenharia do Azure precisa trabalhar com você diretamente para restaurar seus serviços à integridade completa.  
- **Incidente:** um evento que causa impacto em um serviço atualmente está afetando um ou mais recursos em sua assinatura.  
- **Manutenção:** uma atividade de manutenção planejada que pode afetar um ou mais recursos em sua assinatura.  
- **Informações:** possíveis otimizações que podem ajudar a melhorar o uso de recursos. 
- **Segurança:** informações urgentes relacionadas à segurança sobre suas soluções em execução no Azure.

Cada notificação de integridade do serviço inclui detalhes sobre o escopo e o impacto em seus recursos. Os detalhes incluem:

Nome da propriedade | DESCRIÇÃO
-------- | -----------
canais | Um dos seguintes valores: **Admin** ou **Operação**.
correlationId | Geralmente, um GUID no formato de cadeia de caracteres. Eventos que pertencem à mesma ação geralmente compartilham a mesma correlationId.
eventDataId | O identificador exclusivo de um evento.
eventName | O título de um evento.
level | O nível de um evento
resourceProviderName | O nome do provedor de recursos do recurso afetado.
resourceType| O tipo de recurso do recurso afetado.
subStatus | Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus. Por exemplo:  OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204) Solicitação Incorreta (Código de Status HTTP: 400) Não Localizado (Código de Status HTTP: 404) Conflito (código de status HTTP: 409) Erro Interno do Servidor (Código de Status HTTP: 500) Serviço não disponível (código de status HTTP: 503) e Tempo limite de gateway (código de status HTTP: 504).
eventTimestamp | Carimbo de data/hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento.
submissionTimestamp | Carimbo de hora quando o evento tornou-se disponível para consulta.
subscriptionId | A assinatura do Azure na qual esse evento foi registrado.
status | Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: **Started**, **In Progress**, **Succeeded**, **Failed**, **Active** e **Resolved**.
operationName | O nome da operação.
category | Esta propriedade é sempre **ServiceHealth**.
resourceId | ID do recurso afetado.
Properties.title | O título localizado dessa comunicação. Inglês é o padrão.
Properties.communication | Os detalhes localizados da comunicação com marcação HTML. Inglês é o padrão.
Properties.incidentType | Um dos seguintes valores: **ActionRequired**, **Informational**, **Incident**, **Maintenance** ou **Security**.
Properties.trackingId | O incidente ao qual esse evento está associado. Use-a para correlacionar os eventos relacionados a um incidente.
Properties.impactedServices | Um blob JSON com escape que descreve as regiões e os serviços afetados pelo incidente. Uma lista de Services, que, individualmente, tem um **ServiceName** e uma lista de ImpactedRegions, que têm um **RegionName**.
Properties.defaultLanguageTitle | A comunicação em inglês.
Properties.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto sem formatação.
Properties.stage | Os valores possíveis para **Incidente** e **Segurança** são **Ativo,** **Resolvido** ou **RCA**. Para **ActionRequired** ou **Informational**, o único valor é **Ativo**. Para **Manutenção**, são: **Active**, **Planned**, **InProgress**, **Canceled**, **Rescheduled**, **Resolved** ou **Complete**.
Properties.communicationId | A comunicação à qual esse evento está associado.

### <a name="details-on-service-health-level-information"></a>Detalhes sobre informações de nível de integridade de serviço

**Ação Necessária** (properties.incidentType == ActionRequired)
- Informativo – ação do administrador necessária para impedir o impacto nos serviços existentes
    
**Manutenção** (properties.incidentType == Maintenance)
- Aviso – manutenção de emergência
- Informativo – manutenção planejada padrão

**Informações** (properties.incidentType == Information)
- Informativo – o administrador pode precisar impedir o impacto aos serviços existentes

**Segurança** (properties.incidentType == Security)
- Erro – problemas amplamente difundidos ao acessar vários serviços em várias regiões estão impactando um amplo conjunto de clientes.
- Aviso – problemas ao acessar serviços específicos e/ou regiões específicas estão impactando um subconjunto de clientes.
- Informativo – problemas que afetam as operações de gerenciamento e/ou latência, não impactando a disponibilidade do serviço.

**Problemas de Serviço** (properties.incidentType == Incident)
- Erro – problemas amplamente difundidos ao acessar vários serviços em várias regiões estão impactando um amplo conjunto de clientes.
- Aviso – problemas ao acessar serviços específicos e/ou regiões específicas estão impactando um subconjunto de clientes.
- Informativo – problemas que afetam as operações de gerenciamento e/ou latência, não impactando a disponibilidade do serviço.


## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Exibindo as notificações de integridade do serviço no Portal do Azure
1.  No [portal do Azure](https://portal.azure.com), selecione **Monitor**.

    ![Captura de tela do menu do Portal do Azure, com a opção Monitor selecionada](./media/service-notifications/home-monitor.png)

    Este Azure Monitor reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. Ela abre primeiro na seção **Log de atividades** .

3.  Selecione **Alertas**.

    ![Captura do log de atividades do Monitor, com a opção Alertas selecionada](./media/service-notifications/service-health-summary.png)
4. Selecione **+Adicionar alerta do log de atividades** e configure um alerta para garantir que você receba notificações de serviço futuras. Para obter mais informações, consulte [Criar alertas do log de atividades em notificações de serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="next-steps"></a>Próximas etapas
Receber [notificações de alerta sempre que uma notificação de integridade do serviço](../../azure-monitor/platform/alerts-activity-log-service-notifications.md) é postada.  
Saiba mais sobre os [alertas do log de atividades](../../azure-monitor/platform/activity-log-alerts.md).

