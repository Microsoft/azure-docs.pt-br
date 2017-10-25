---
title: "O que são notificações de integridade do serviço | Microsoft Docs"
description: "As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pelo Microsoft Azure."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: d85281c02b792921f12cc62e6d60bef3e7c13b3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="service-health-notifications"></a>Notificações de integridade do serviço
## <a name="overview"></a>Visão geral

Este artigo mostra como exibir notificações de integridade do serviço usando o portal do Azure.

As notificações de integridade do serviço permitem exibir mensagens de integridade do serviço publicadas pela equipe do Azure que podem estar afetando os recursos em sua assinatura. Essas notificações são uma subclasse dos eventos do log de atividades e também podem ser encontradas na folha do log de atividades. As notificações de integridade do serviço podem ser informativas ou acionáveis, dependendo da classe.

Há cinco classes de notificações de integridade do serviço:  

- **Ação Necessária:** periodicamente, podemos perceber algo incomum em sua conta. Talvez seja necessário trabalharmos com você para corrigir isso. Nós lhe enviaremos uma notificação detalhando as ações que você precisará tomar ou com detalhes sobre como contatar o suporte ou a engenharia do Azure.  
- **Recuperação Assistida:** um evento ocorreu e os engenheiros confirmaram que você ainda está sofrendo um impacto. A engenharia precisará trabalhar com você diretamente para restaurar seus serviços.  
- **Incidente:** um evento que causa impacto em um serviço atualmente está afetando um ou mais recursos em sua assinatura.  
- **Manutenção:** essa é uma notificação que informa de uma atividade de manutenção planejada que pode afetar um ou mais recursos em sua assinatura.  
- **Informação:** periodicamente, podemos lhe enviar notificações para comunicar sobre possíveis otimizações que podem ajudar a melhorar a utilização de recursos.  
- **Segurança:** informações urgentes relacionadas à segurança sobre suas soluções em execução no Azure.

Cada notificação de integridade do serviço trará detalhes sobre o escopo e o impacto em seus recursos. Os detalhes incluirão:

Nome da Propriedade | Descrição
-------- | -----------
canais | É um dos seguintes valores: “Admin” ou “Operation”
correlationId | Geralmente, é um GUID no formato de cadeia de caracteres. Eventos que pertencem à mesma ação superior geralmente compartilham a mesma correlationId.
eventDataId | É o identificador exclusivo de um evento
eventName | É o título do evento
level | Nível do evento. Um dos seguintes valores: “Crítico”, “Erro”, “Aviso”, “Informativo” e “Detalhado”
resourceProviderName | Nome do provedor de recursos do recurso afetado
resourceType| O tipo de recurso do recurso afetado
subStatus | Geralmente, o código de status HTTP da chamada REST correspondente, mas também pode incluir outras cadeias de caracteres que descrevam um substatus, como estes valores comuns: OK (Código de Status HTTP: 200), Criado (Código de Status HTTP: 201), Aceito (Código de Status HTTP: 202), Sem Conteúdo (Código de Status HTTP: 204), Solicitação Incorreta (Código de Status HTTP: 400), Não Encontrado (Código de Status HTTP: 404), Conflito (Código de Status HTTP: 409), Erro de Servidor Interno (Código de Status HTTP: 500), Serviço Indisponível (Código de Status HTTP: 503), Tempo Limite do Gateway (Código de Status HTTP: 504).
eventTimestamp | Carimbo de hora quando o evento foi gerado pelo serviço do Azure que está processando a solicitação correspondente ao evento.
submissionTimestamp |   Carimbo de hora quando o evento tornou-se disponível para consulta.
subscriptionId | A assinatura do Azure na qual esse evento foi registrado
status | Cadeia de caracteres que descreve o status da operação. Alguns valores comuns são: Iniciado, Em Andamento, Êxito, Falha, Ativo, Resolvido.
operationName | Nome da operação.
categoria | “ServiceHealth”
resourceId | Id de recurso do recurso afetado.
Properties.title | O título localizado dessa comunicação. Inglês é o idioma padrão.
Properties.communication | Os detalhes localizados da comunicação com marcação HTML. Inglês é o padrão.
Properties.incidentType | Possíveis valores: AssistedRecovery, ActionRequired, Information, Incident, Maintenance, Security
Properties.trackingId | Identifica o incidente ao qual esse evento está associado. Use-a para correlacionar os eventos relacionados a um incidente.
Properties.impactedServices | Um blob JSON com escape que descreve as regiões e os serviços que são afetados pelo incidente. Uma lista de Services, que, individualmente, tem um ServiceName e uma lista de ImpactedRegions, que têm um RegionName.
Properties.defaultLanguageTitle | A comunicação em inglês
Properties.defaultLanguageContent | A comunicação em inglês como marcação HTML ou texto sem formatação
Properties.stage | Os possíveis valores para AssistedRecovery, ActionRequired, Information, Incident e Security são Active e Resolved. Para Maintenance, eles são: Active, Planned, InProgress, Canceled, Rescheduled, Resolved, Complete
Properties.communicationId | A comunicação à qual esse evento está associado.


## <a name="viewing-your-service-health-notifications-in-the-azure-portal"></a>Exibindo as notificações de integridade do serviço no portal do Azure
1.  No [portal](https://portal.azure.com), navegue até o serviço **Monitor**

    ![Monitoramento](./media/monitoring-service-notifications/home-monitor.png)
2.  Clique na opção **Monitor** para abrir a folha Monitor. Esta folha reúne todas as suas configurações e dados de monitoramento em uma exibição consolidada. Ela abre primeiro na seção **Log de atividades** .

3.  Agora clique na seção **Notificações de Serviço**

    ![Monitoramento](./media/monitoring-service-notifications/service-health-summary.png)
4.  Clique em um dos itens de linha para exibir mais detalhes

5. Clique na operação **+ Adicionar Alerta do Log de Atividades** para receber notificações, a fim de garantir que você receberá notificações de serviço futuras desse tipo. Para saber mais sobre como configurar alertas nas notificações de serviço, [clique aqui](monitoring-activity-log-alerts-on-service-notifications.md)

## <a name="next-steps"></a>Próximas etapas:
Receber [notificações de alerta sempre que uma notificação de integridade do serviço](monitoring-activity-log-alerts-on-service-notifications.md) é postada  
Saiba mais sobre os [alertas do log de atividades](monitoring-activity-log-alerts.md)
