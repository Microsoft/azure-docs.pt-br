---
title: Regras de ação para alertas do Azure Monitor
description: Noções básicas sobre quais são as regras de ação no Azure Monitor e como configurar e gerenciá-los.
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: df069ee398ea2937f03765b10576061b5e541390
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656732"
---
# <a name="action-rules-preview"></a>Regras de ação (visualização)

Regras de ação ajudarão-lo a definir ou suprimir ações em qualquer escopo do Gerenciador de recursos do Azure (assinatura do Azure, grupo de recursos ou recurso de destino). Eles têm vários filtros que ajuda você restringir o subconjunto específico de instâncias do alerta que você deseja agir.

## <a name="why-and-when-should-you-use-action-rules"></a>Por que e quando você deve usar regras de ação?

### <a name="suppression-of-alerts"></a>Supressão de alertas

Há muitos cenários em que ele é útil suprimir as notificações que geram alertas. Essas opções variam desde supressão durante uma janela de manutenção planejada para supressão do horário comercial. Por exemplo, a equipe responsável **ContosoVM** deseja suprimir notificações de alerta para o fim de semana futuro, porque **ContosoVM** está passando por manutenção planejada. 

Embora a equipe pode desabilitar a cada regra de alerta está configurada no **ContosoVM** manualmente (e habilitá-lo novamente depois de manutenção), ele não é um processo simples. Regras de ação o ajudam a definir a supressão de alerta em grande escala com a capacidade de forma flexível, configurar o período de supressão. No exemplo anterior, a equipe pode definir uma regra de ação na **ContosoVM** que suprime todas as notificações de alerta para o fim de semana.


### <a name="actions-at-scale"></a>Ações em escala

Embora as regras de alerta ajudam você a definir o grupo de ação que dispara quando o alerta é gerado, os clientes geralmente têm um grupo comum de ação em seu escopo de operações. Por exemplo, uma equipe responsável por grupo de recursos **ContosoRG** provavelmente definirá o mesmo grupo de ação para todas as regras de alerta definidas dentro **ContosoRG**. 

Regras de ação ajudarão-lo a simplificar esse processo. Definindo ações em grande escala, um grupo de ação pode ser disparado para qualquer alerta que é gerada no escopo configurado. No exemplo anterior, a equipe pode definir uma regra de ação na **ContosoRG** que irá disparar o mesmo grupo de ação para todos os alertas gerados dentro dele.

> [!NOTE]
> Atualmente, não aplicam regras de ação para alertas de integridade do serviço do Azure.

## <a name="configuring-an-action-rule"></a>Configurar uma regra de ação

Você pode acessar o recurso selecionando **Gerenciar ações** da **alertas** página de aterrissagem no Azure Monitor. Em seguida, selecione **regras de ação (visualização)** . Você pode acessar as regras, selecionando **regras de ação (visualização)** do painel da página de aterrissagem para alertas.

![Regras de ação da página de aterrissagem do Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ nova regra de ação**. 

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-new-rule.png)

Como alternativa, você pode criar uma regra de ação, enquanto você estiver configurando uma regra de alerta.

![Adicionar nova regra de ação](media/alerts-action-rules/action-rules-alert-rule.png)

Agora você verá a página de fluxo para a criação de regras de ação. Configure os seguintes elementos: 

![Novo fluxo de criação de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Escopo

Primeiro, escolha o escopo (assinatura do Azure, grupo de recursos ou recurso de destino). Você pode também seleção múltipla uma combinação de escopos em uma única assinatura.

![Escopo da ação de regra](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>critérios de filtro

Além disso, você pode definir filtros para restringi-las para baixo até um subconjunto específico de alertas. 

Os filtros disponíveis são: 

* **Gravidade**: A opção de selecionar um ou mais severidades de alerta. **Severidade = gravidade 1** significa que a regra de ação é aplicável para o conjunto de todos os alertas de gravidade 1.
* **Monitorar serviço**: Um filtro com base no serviço de monitoramento de origem. Esse filtro também é seleção múltipla. Por exemplo, **Monitor Service = "Application Insights"** significa que a regra de ação é aplicável a todos os alertas baseados em Application Insights.
* **Tipo de recurso**:  Um filtro com base em um tipo de recurso específico. Esse filtro também é seleção múltipla. Por exemplo, **tipo de recurso = "Máquinas virtuais"** significa que a regra de ação é aplicável a todas as máquinas virtuais.
* **ID da regra de alerta**: Uma opção para filtrar as regras de alerta específicas usando a ID do Gerenciador de recursos da regra de alerta.
* **Monitorar a condição**:  Um filtro para as instâncias de alerta com um **disparado** ou **resolvido** como a condição do monitor.
* **Descrição**: Uma correspondência de regex (expressão regular) que define uma correspondência de cadeia de caracteres com a descrição, definida como parte da regra de alerta. Por exemplo, **descrição contém 'prod'** corresponderá a todos os alertas que contêm a cadeia de caracteres "prod" em suas descrições.
* **(Carga) do contexto de alerta**: Uma correspondência de regex que define uma cadeia de caracteres de correspondência com os campos de contexto de alerta da carga de um alerta. Por exemplo, **alertar o contexto (carga) contém 'Computador-01'** corresponderá a todos os alertas de cujas cargas contêm a cadeia de caracteres "Computador-01".

Esses filtros são aplicados em conjunto com outra. Por exemplo, se você definir **tipo de recurso ' = as máquinas virtuais** e **severidade ' = Sev0**, em seguida, você filtrou para todos os **Sev0** alertas em apenas suas VMs. 

![Filtros de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuração do grupo de ação ou de supressão

Em seguida, configure a regra de ação para a supressão de alerta ou suporte de grupo de ação. Você não pode escolher ambas. A configuração funciona em todas as instâncias de alertas que correspondem aos filtros e escopo definido anteriormente.

#### <a name="suppression"></a>Supressão

Se você selecionar **supressão**, configurar a duração para a supressão de ações e notificações. Escolha uma das seguintes opções:
* **A partir de agora (sempre)** : Suprime todas as notificações por tempo indeterminado.
* **Em um horário agendado**: Suprime as notificações dentro de uma duração limitada.
* **Com uma recorrência**: Suprime as notificações em um agendamento diário, semanal ou mensal recorrente.

![Supressão da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupo de ações

Se você selecionar **grupo de ação** no botão de alternância, adicione um grupo de ação existente ou crie um novo. 

> [!NOTE]
> Você pode associar apenas um grupo de ação com uma regra de ação.

![Adicionar ou criar nova regra de ação selecionando o grupo de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra de ação

Por fim, configure os seguintes detalhes para a regra de ação:
* Nome
* Grupo de recursos no qual ele é salvo
* DESCRIÇÃO 

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: Supressão de alertas com base na gravidade

A Contoso deseja suprimir notificações para todos os alertas de Sev4 em todas as VMs dentro da assinatura **ContosoSub** cada semana.

**Solução:** Crie uma regra de ação com:
* Escopo = **ContosoSub**
* Filtros
    * Severity = **Sev4**
    * Tipo de recurso = **máquinas virtuais**
* Supressão de recorrência definida como semanalmente, e **sábado** e **domingo** marcada

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: Supressão de alertas com base no contexto do alerta (carga)

A Contoso deseja suprimir notificações para todos os alertas gerados para de log **computador-01** na **ContosoSub** indefinidamente como ele está passando por manutenção.

**Solução:** Crie uma regra de ação com:
* Escopo = **ContosoSub**
* Filtros
    * Monitorar serviço = **Log Analytics**
    * Alerta de contexto (carga) contém **computador-01**
* Supressão definido como **a partir de agora (sempre)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: Grupo de ações definido em um grupo de recursos

A Contoso definiu [um alerta de métrica no nível da assinatura](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Mas ele deseja definir as ações que disparam especificamente para alertas gerados a partir do grupo de recursos **ContosoRG**.

**Solução:** Crie uma regra de ação com:
* Escopo = **ContosoRG**
* Nenhum filtro
* Grupo de ação definido como **ContosoActionGroup**

> [!NOTE]
> *Grupos de ações definidas dentro de regras de ação e as regras de alerta operam de forma independente, com nenhuma eliminação de duplicação.* No cenário descrito anteriormente, se um grupo de ação é definida para a regra de alerta, ele dispara em conjunto com o grupo de ações definido na regra de ação. 

## <a name="managing-your-action-rules"></a>Gerenciar suas regras de ação

Você pode exibir e gerenciar suas regras de ação da exibição de lista:

![Exibição de lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, você pode habilitar, desabilitar ou excluir regras de ação em grande escala, marcando a caixa de seleção ao lado deles. Quando você seleciona uma regra de ação, abre a página de configuração. A página de ajuda a atualizar a definição da regra de ação e habilitar ou desabilitá-lo.

## <a name="best-practices"></a>Práticas recomendadas

Registram alertas que você criar com o [número de resultados](alerts-unified-log.md) opção Gerar uma única instância de alerta usando o resultado da pesquisa inteira (que pode abranger vários computadores). Nesse cenário, se uma regra de ação usa a **contexto de alerta (carga)** filtro, ele atua na instância do alerta enquanto houver uma correspondência. No cenário 2, descrito anteriormente, se os resultados da pesquisa para o alerta de log gerado contém ambos **computador-01** e **computador-02**, a notificação inteira será suprimida. Não há nenhuma notificação gerada para **computador-02** em todos os.

![Regras de ação e alertas do log (número de resultados)](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Para usar melhor os alertas do log com regras de ação, criar alertas do log com o [medição métrica](alerts-unified-log.md) opção. Instâncias separadas de alertas são geradas por essa opção, com base em seu campo de grupo definido. Em seguida, no cenário 2, instâncias separadas de alertas são geradas para **computador-01** e **computador-02**. Devido à regra de ação descrita no cenário, somente a notificação para **computador-01** será suprimido. A notificação para **computador-02** continua a ser acionado como normal.

![Regras de ação e alertas do log (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Perguntas Frequentes

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Enquanto estou configurando uma regra de ação, eu gostaria de ver todos os possíveis sobreposição de regras de ação, para que eu evitar notificações duplicadas. É possível fazer isso?

Depois de definir um escopo conforme você configura uma regra de ação, você pode ver uma lista de regras de ação que se sobrepõem no mesmo escopo (se houver). Essa sobreposição pode ser uma das seguintes opções:

* Uma correspondência exata: Por exemplo, a regra de ação que você está definindo e a regra de ação sobrepostos são na mesma assinatura.
* Um subconjunto: Por exemplo, a regra de ação que você está definindo está em uma assinatura, e a regra de ação sobreposição está em um grupo de recursos dentro da assinatura.
* Um superconjunto: Por exemplo, a regra de ação que você está definindo está em um grupo de recursos e a regra de ação sobrepostos está na assinatura que contém o grupo de recursos.
* Uma interseção: Por exemplo, a regra de ação que você está definindo está em **VM1** e **VM2**, e a regra de ação de sobreposição está na **VM2** e **VM3**.

![Sobreposição de regras de ação](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Enquanto estou configurando uma regra de alerta, é possível saber se já houver regras de ação definido que pode agir sobre a regra de alerta que estou definindo?

Depois de definir o recurso de destino para a regra de alerta, você pode ver a lista de regras de ação que atuam no mesmo escopo (se houver), selecionando **ações configuradas de modo de exibição** sob o **ações** seção. Esta lista é preenchida com base nos seguintes cenários para o escopo:

* Uma correspondência exata: Por exemplo, a regra de alerta que você está definindo e a regra de ação estão na mesma assinatura.
* Um subconjunto: Por exemplo, a regra de alerta que você está definindo está em uma assinatura, e a regra de ação está em um grupo de recursos dentro da assinatura.
* Um superconjunto: Por exemplo, a regra de alerta que você está definindo está em um grupo de recursos e a regra de ação está na assinatura que contém o grupo de recursos.
* Uma interseção: Por exemplo, a regra de alerta que você está definindo está em **VM1** e **VM2**, e a ação de regra está na **VM2** e **VM3**.
    
![Sobreposição de regras de ação](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Posso ver os alertas que foram suprimidos por uma regra de ação?

No [página de lista de alertas](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-managing-alert-instances), você pode escolher uma coluna adicional chamada **supressão Status**. Se a notificação para uma instância de alerta foi suprimida, ele seria mostram o status na lista.

![Instâncias de alerta suprimidas](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se houver uma regra de ação com um grupo de ações e outro com supressão Active Directory no mesmo escopo, o que acontece?

Supressão sempre tem precedência no mesmo escopo.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso que está sendo monitorado no duas regras de ação separada? Eu recebo uma ou duas notificações? Por exemplo, **VM2** no cenário a seguir:

      "action rule AR1 defined for VM1 and VM2 with action group AG1
      action rule AR2 defined for VM2 and VM3 with action group AG1"

Para cada alerta na VM1 e VM3, grupo de ação AG1 deverá ser disparado uma vez. Para cada alerta no **VM2**, grupo de ação AG1 será disparado duas vezes, porque as regras de ação não elimine duplicações de ações. 

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso monitorado em duas regras de ação separada e um chama para ação, enquanto o outro para supressão? Por exemplo, **VM2** no cenário a seguir:

      "action rule AR1 defined for VM1 and VM2 with action group AG1 
      action rule AR2 defined for VM2 and VM3 with suppression"

Para cada alerta na VM1, o grupo de ação AG1 será disparado uma vez. Ações e notificações para cada alerta no VM2 e VM3 serão suprimidas. 

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>O que acontece se eu tiver uma regra de alerta e uma regra de ação definidos para o mesmo recurso de grupos de ações diferentes de chamada? Por exemplo, **VM1** no cenário a seguir:

      "alert rule rule1 on VM1 with action group AG2
      action rule AR1 defined for VM1 with action group AG1" 
 
Para cada alerta na VM1, o grupo de ação AG1 será disparado uma vez. Sempre que "rule1" regra de alerta é disparada, ele também disparará AG2 Além disso. Grupos de ações definidas dentro de regras de ação e as regras de alerta operam de forma independente, com nenhuma eliminação de duplicação. 

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre alertas no Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview)
