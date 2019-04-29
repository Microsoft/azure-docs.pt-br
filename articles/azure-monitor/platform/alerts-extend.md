---
title: Estender (copiar) alertas do Log Analytics para a nuvem do Azure Governamental
description: Visão geral do processo de copiar alertas do Log Analytics no Portal do OMS para os Alertas do Azure, com detalhes que resolvem preocupações comuns dos clientes.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: efd2fc9d164564126b7b641ef35bbb10c4834f49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996174"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Estender alertas do Log Analytics para os Alertas do Azure

> [!NOTE]
> Microsoft já concluiu o processo descrito neste artigo para as versões públicas do Azure. No entanto, ele ainda se aplica a versões do governo dos EUA.  

Até recentemente, o Azure Log Analytics incluía sua própria funcionalidade de alerta, que poderia notificar proativamente você sobre as condições com base em dados do Log Analytics. Você gerenciava regras de alerta no Portal do Microsoft Operations Management Suite. Agora a nova experiência de alertas tem alertas integrados entre vários serviços no Microsoft Azure. Isso está disponível como **Alertas** no Azure Monitor no Portal do Azure e oferece suporte a alertas de logs de atividades, métricas e logs do Log Analytics e do Azure Application Insights.

Os clientes de nuvem do Azure Governamental que usam o portal do OMS, poderão [estender voluntariamente as regras de alerta para o Azure](alerts-extend-tool.md) partir **1º de fevereiro de 2019**. A partir de **1º de março de 2019**, a Microsoft irá automaticamente estender de forma sistemática todas as regras de alerta existentes no Azure Governamental – portal do OMS para o Azure, sem qualquer tempo de inatividade ou interrupção em seu monitoramento. Todos os novos espaços de trabalho criados no dia **1º de março de 2019** ou após essa data, no portal do OMS do Azure Governamental, serão estendidos automaticamente para o Azure.

## <a name="benefits-of-extending-your-alerts"></a>Benefícios de estender seus alertas
Há várias vantagens de criar e gerenciar alertas no portal do Azure, como:

- Ao contrário do Portal do Operations Management Suite, em que apenas 250 alertas podem ser criados e exibidos, os Alertas do Azure não têm essa limitação.
- Nos Alertas do Azure, é possível gerenciar, enumerar e exibir todos os tipos de alertas. Anteriormente, você só poderia fazer isso para alertas do Log Analytics.
- É possível limitar o acesso aos usuários apenas ao monitoramento e alertas, usando a [função do Azure Monitor](../../azure-monitor/platform/roles-permissions-security.md).
- Nos Alertas do Azure, é possível usar [grupos de ação](../../azure-monitor/platform/action-groups.md). Isso permite que você tenha mais de uma ação para cada alerta. É possível enviar SMS, enviar uma chamada de voz, invocar um runbook da Automação do Azure, invocar um webhook e configurar um Conector de ITSM (Gerenciamento de Serviços de TI). 

## <a name="process-of-extending-your-alerts"></a>Processo de extensão dos alertas
O processo de mover alertas do Log Analytics para os Alertas do Azure não envolve alterar sua definição, consulta ou configuração de alertas de nenhuma maneira. A única alteração necessária é a do Azure. Você executa todas as ações usando um grupo de ações. Se os grupos de ação já estiverem associados ao seu alerta, eles serão incluídos quando estendidos para o Azure.

Ao agendar alertas em um espaço de trabalho do Log Analytics para serem estendidos para o Azure, eles continuarão funcionando e não comprometerão, de forma alguma, sua configuração. Quando agendados, seus alertas podem ficar indisponíveis para modificação temporariamente, mas é possível continuar criando novos Alertas do Azure durante esse período. Se você tentar editar ou criar alertas no portal do Operations Management Suite, terá a opção de continuar criando-os do espaço de trabalho do Log Analytics. Também é possível criá-los dos Alertas do Azure no Portal do Azure.

 ![Captura de tela da opção de criar alertas do Log Analytics ou dos Alertas do Azure](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> Estender alertas do Log Analytics para o Azure não incorre em encargos à sua conta. O uso dos Alertas do Azure para alertas do Log Analytics baseados em consulta não será cobrado quando usado dentro das condições e dos limites definidos na [política de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).  


### <a name="how-to-extend-your-alerts-voluntarily"></a>Como estender seus alertas voluntariamente
Para estender os alertas para os Alertas do Azure, é possível usar um assistente disponível no portal do Operations Management Suite ou fazer isso programaticamente usando uma API. Para obter mais informações, consulte [Extending alerts into Azure using Operations Management Suite portal and API](alerts-extend-tool.md) (Estendendo alertas para o Azure usando o portal e a API do Operations Management Suite).

## <a name="experience-after-extending-your-alerts"></a>Experiência após estender seus alertas
Depois que seus alertas forem estendidos para os Alertas do Azure, eles continuarão disponíveis no portal do Operations Management Suite para gerenciamento não diferente de antes.

![Captura de tela do portal do Operations Management Suite, com alertas listados](media/alerts-extend/PostExtendList.png)

Quando você tenta editar um alerta existente ou criar um novo alerta no portal do Operations Management Suite, você é redirecionado automaticamente para os Alertas do Azure.  

> [!NOTE]
> Certifique-se de que as permissões atribuídas às pessoas que precisam adicionar ou editar alertas foram atribuídas corretamente no Azure. Para entender quais permissões você precisa conceder, consulte [permissões para usar Alertas e o Azure Monitor](../../azure-monitor/platform/roles-permissions-security.md).  
> 

É possível continuar criando alertas com base na [API do Log Analytics](../../azure-monitor/platform/api-alerts.md) e no [Modelo de Recursos do Log Analytics](../../azure-monitor/insights/solutions-resources-searches-alerts.md). É necessário incluir grupos de ação quando você faz isso.

## <a name="next-steps"></a>Próximas etapas

* Conheça as ferramentas para [iniciar a extensão de alertas do Log Analytics para o Azure](alerts-extend-tool.md).
* Saiba mais sobre a [experiência de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
* Saiba como criar [alertas de log nos alertas do Azure](alerts-unified-log.md).

