---
title: Visão geral dos alertas clássicos no Azure Monitor
description: Alertas clássicos estão sendo preteridos. Alerta para permitir que você monitore as métricas dos recursos do Azure, eventos ou logs, e seja notificado quando uma condição especificada for atendida.
ms.topic: conceptual
ms.date: 05/19/2018
ms.subservice: alerts
ms.openlocfilehash: 2b1f236255c7cbf073cf7edc5a10df895f9c1095
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100606849"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>O que são alertas clássicos no Microsoft Azure?

> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte [a alertas de métrica quase em tempo real mais recentes e uma nova experiência de alertas](../platform/alerts-overview.md). Os alertas clássicos são [desativados](../platform/monitoring-classic-retirement.md) para usuários de nuvem pública, embora ainda estejam em uso limitado para recursos que ainda não dão suporte aos novos alertas.
>

Os alertas permitem que você configure condições sobre dados e seja notificado quando as condições corresponderem aos dados de monitoramento mais recentes.

## <a name="old-and-new-alerting-capabilities"></a>Novos e antigos recursos de alerta

Anteriormente, o Azure Monitor, Application Insights, Log Analytics e a Integridade do Serviço tinham recursos de alerta separados. Com o tempo, o Azure aprimorou e combinou a interface do usuário e os diferentes métodos de alerta. A consolidação ainda está em processo.

Você pode exibir alertas clássicos apenas na tela do usuário de alertas clássicos no portal do Azure. Essa tela é obtida com o botão **Exibir alertas clássicos** na tela de alertas. 

 ![Opções de alerta no portal do Azure](media/alerts-classic.overview/monitor-alert-screen2.png)

A nova experiência de usuário de alertas tem os seguintes benefícios em relação à experiência de alertas clássicos:
- **Melhor sistema de notificação** – todos os alertas mais recentes usam grupos de ações, que são grupos nomeados de notificações e ações que podem ser reutilizados em vários alertas. Alertas de métrica clássicos e alertas antigos do Log Analytics não usam grupos de ações.
- **Uma experiência de criação unificada** – toda criação de alerta para métricas, logs e log de atividades no Azure Monitor, no Log Analytics e no Application Insights está em um lugar.
- **Exibir alertas disparados do Log Analytics no portal do Azure** – Você pode agora também consultar alertas do Log Analytics disparados na sua assinatura. Anteriormente, eles estavam em um portal separado.
- **Separação de alertas disparados e regras de alerta** – regras de alerta (a definição da condição que dispara um alerta) e os alertas disparados (uma instância do gatilho de regra de alerta) são diferenciados e, portanto, as exibições operacional e de configuração são diferentes.
- **Fluxo de trabalho melhor** – A nova experiência de criação de alertas orienta o usuário durante o processo de configurar uma regra de alerta, o que simplifica a percepção do que deve ser monitorado.
- **Consolidação de alertas inteligentes** e **configuração do estado de alerta** – alertas mais recentes incluem a funcionalidade de agrupamento automático que mostra alertas semelhantes juntos para reduzir a sobrecarga na interface do usuário. 

Os alertas de métrica mais recentes têm os seguintes benefícios em relação aos alertas de métrica clássicos:
- **Latência melhorada**: alertas de métrica mais recentes podem ser executados com uma frequência mínima a cada um minuto. Os alertas de métrica antigos são sempre executados em uma frequência de 5 minutos. Os alertas mais recentes aumentam o atraso menor, desde a ocorrência do problema até a notificação ou ação (de 3 a 5 minutos). Alertas mais antigos são de 5 a 15 minutos, dependendo do tipo.  Os alertas do log normalmente têm um atraso de 10 a 15 minutos devido ao tempo gasto para ingerir os logs, mas os métodos de processamento mais recentes estão reduzindo esse tempo. 
- **Suporte para métricas multidimensionais**: você pode alertar sobre métricas dimensionais, permitindo que você monitore um segmento interessante da métrica.
- **Mais controle sobre as condições de métrica**: é possível definir regras de alerta mais avançadas. Os alertas mais recentes dão suporte ao monitoramento dos valores máximo, mínimo, média e total das métricas.
- **Monitoramento combinado de várias métricas**: é possível monitorar várias métricas (atualmente, até duas métricas) com uma única regra. Um alerta é disparado quando ambas as métricas violam seus respectivos limites para o período especificado.
- **Melhor sistema de notificação**: todos os alertas mais recentes usam [grupos de ações](../platform/action-groups.md), que são grupos nomeados de notificações e ações que podem ser reutilizados em vários alertas.  Alertas de métrica clássicos e alertas antigos do Log Analytics não usam grupos de ações. 
- **Métricas dos Logs** (versão prévia pública): os dados do log transferidos para o Log Analytics agora podem ser extraídos e convertidos em métricas do Azure Monitor e, em seguida, alertados da mesma forma que outras métricas. Consulte [Alertas (clássicos)]() para terminologia específica para alertas clássicos. 


## <a name="classic-alerts-on-azure-monitor-data"></a>Alertas clássicos sobre dados do Azure Monitor
Há dois tipos de alertas clássicos disponíveis - alertas de métrica e alertas de registro de atividades.

* **Alertas de métrica clássicos**: este alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite que você atribui. O alerta gera uma notificação quando esse limite é ultrapassado e a condição de alerta é atendida. Nesse ponto, o alerta é considerado "Ativado". Ele gera outra notificação quando é "Resolvido" - isto é, quando o limite é ultrapassado novamente e a condição não é mais atendida.

* **Alertas clássicos do log de atividades** - Um alerta de log de streaming que é disparado em uma entrada de evento do Log de Atividades que corresponde aos critérios de filtragem. Esses alertas têm apenas um estado, "Ativado". O mecanismo de alertas simplesmente aplica os critérios de filtragem a qualquer novo evento. Ele não procura encontrar entradas mais antigas. Esses alertas podem notificá-lo quando ocorrer um novo incidente de Integridade do Serviço ou quando um usuário ou aplicativo executar uma operação na assinatura, por exemplo, "Excluir máquina virtual."

Para dados de log de recursos disponíveis por meio de Azure Monitor, encaminhe os dados para Log Analytics e use um alerta de consulta de log. Log Analytics agora usa o [novo método de alerta](../platform/alerts-overview.md) 

O diagrama a seguir resume as fontes de dados no Azure Monitor e, conceitualmente, como emitir um alerta com base nesses dados.

![Alertas explicados](media/alerts-classic.overview/Alerts_Overview_Resource_v5.png)

## <a name="taxonomy-of-alerts-classic"></a>Taxonomia de alertas (clássico)
O Azure usa os termos a seguir para descrever alertas clássicos e suas funções:
* **Alerta** – uma definição de critérios (uma ou mais regras ou condições) que são ativados quando atendidos.
* **Ativo** – o estado quando os critérios definidos por um alerta clássico são atendidos.
* **Resolvido** – o estado quando os critérios definidos por um alerta clássico não forem mais atendidos após terem sido atendidos anteriormente.
* **Notificação** – a ação realizada após a ativação de um alerta clássico.
* **Ação** -uma chamada específica enviada a um destinatário de uma notificação (por exemplo, enviando um endereço por email ou postando para uma URL de webhook). As notificações normalmente podem disparar várias ações.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>Como eu recebo uma notificação de um alerta clássico do Azure Monitor?
Historicamente, os alertas do Azure de serviços diferentes usavam seus próprios métodos de notificação internos. 

O Azure Monitor criou um agrupamento de notificação reutilizável chamado *grupos de ações*. Grupos de ações especificam um conjunto de receptores para uma notificação. Sempre que um alerta for ativado fazendo referência ao Grupo de Ações, todos os receptores receberão essa notificação. Os grupos de ações permitem reutilizar um agrupamento de receptores (por exemplo, sua lista de engenheiros de plantão) em vários objetos de alerta. Os grupos de ações dão suporte para notificação, postando em uma URL do webhook além de endereços de email, números de SMS e várias outras ações.  Para obter mais informações, consulte [grupos de ações](../platform/action-groups.md). 

Os alertas de log de atividades clássicos mais antigos usam grupos de ações.

No entanto, os alertas de métrica mais antigos não usam grupos de ações. Em vez disso, é possível configurar as ações a seguir: 
- Enviar notificações por email ao administrador de serviços, aos administradores ou aos endereços de email adicionais que você especificar.
- Chamar um webhook, o que permite inicializar ações de automação adicionais.

Webhooks permite automação e correção, por exemplo, usando:
- Runbook de Automação do Azure
- Azure Function
- Aplicativo lógico do Azure
- um serviço de terceiros

## <a name="next-steps"></a>Próximas etapas
Obter informações sobre as regras de alerta e sobre como configurá-las usando:

* Saiba mais sobre [métricas](../platform/data-platform.md)
* Configurar [alertas de métrica clássicos por meio do portal do Azure](alerts-classic-portal.md)
* Configurar [PowerShell de alertas de métrica clássicos](alerts-classic-portal.md)
* Configurar a [CLI (interface de linha de comando) de alertas de métrica clássicos](alerts-classic-portal.md)
* Configurar a [API REST do Azure Monitor de alertas de métrica clássicos](/rest/api/monitor/alertrules)
* Saiba mais sobre o [log de atividades](../platform/platform-logs-overview.md)
* Configurar [alertas do Log de Atividades por meio do Portal do Azure](../platform/activity-log-alerts.md)
* Configurar [alertas do Log de Atividades por meio do Resource Manager](../platform/alerts-activity-log.md)
* Examinar o [esquema de webhook de alerta do log de atividades](activity-log-alerts-webhook.md)
* Saiba mais sobre [grupos de ação](../platform/action-groups.md)
* Configurar [alertas mais recentes](alerts-metric.md)

