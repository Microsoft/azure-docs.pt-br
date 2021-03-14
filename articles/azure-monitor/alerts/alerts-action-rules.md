---
title: Regras de ação para alertas de Azure Monitor
description: Noções básicas sobre as regras de ação no Azure Monitor são e como configurá-las e gerenciá-las.
ms.topic: conceptual
ms.date: 04/25/2019
ms.openlocfilehash: 1a86493b4b478e8ebc75545bf80dafa425132fe4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015991"
---
# <a name="action-rules-preview"></a>Regras de ação (visualização)

As regras de ação ajudam a definir ou suprimir ações em qualquer escopo de Azure Resource Manager (assinatura do Azure, grupo de recursos ou recurso de destino). Eles têm vários filtros que ajudam a restringir o subconjunto específico de instâncias de alerta nas quais você deseja agir.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rBZ2]

## <a name="why-and-when-should-you-use-action-rules"></a>Por que e quando você deve usar regras de ação?

### <a name="suppression-of-alerts"></a>Supressão de alertas

Há muitos cenários em que é útil suprimir as notificações que os alertas geram. Esses cenários variam desde a supressão durante uma janela de manutenção planejada até a supressão fora do horário comercial. Por exemplo, a equipe responsável pelo  **ContosoVM** deseja suprimir notificações de alerta para o próximo final de semana, pois o **ContosoVM** está passando por manutenção planejada.

Embora a equipe possa desabilitar cada regra de alerta configurada no **ContosoVM** manualmente (e habilitá-la novamente após a manutenção), não é um processo simples. As regras de ação ajudam a definir a supressão de alerta em escala com a capacidade de configurar com flexibilidade o período de supressão. No exemplo anterior, a equipe pode definir uma regra de ação em **ContosoVM** que suprime todas as notificações de alerta do fim de semana.

### <a name="actions-at-scale"></a>Ações em escala

Embora as regras de alerta ajudem a definir o grupo de ações que dispara quando o alerta é gerado, os clientes geralmente têm um grupo de ação comum em seu escopo de operações. Por exemplo, uma equipe responsável pelo grupo de recursos **ContosoRG** provavelmente definirá o mesmo grupo de ações para todas as regras de alerta definidas em **ContosoRG**.

As regras de ação ajudam a simplificar esse processo. Ao definir as ações em escala, um grupo de ações pode ser disparado para qualquer alerta gerado no escopo configurado. No exemplo anterior, a equipe pode definir uma regra de ação em **ContosoRG** que disparará o mesmo grupo de ações para todos os alertas gerados nele.

> [!NOTE]
> Regras de ação atualmente não se aplicam aos alertas de integridade do serviço do Azure.

## <a name="configuring-an-action-rule"></a>Configuração de uma regra de ação

### <a name="portal"></a>[Portal](#tab/portal)

Você pode acessar o recurso selecionando **Gerenciar ações** na página de aterrissagem de **alertas** no Azure monitor. Em seguida, selecione **regras de ação (versão prévia)**. Você pode acessar as regras selecionando **regras de ação (versão prévia)** no painel da página de aterrissagem para alertas.

![Regras de ação da página de aterrissagem de Azure Monitor](media/alerts-action-rules/action-rules-landing-page.png)

Selecione **+ nova regra de ação**.

![Captura de tela mostra a página Gerenciar ações com o botão nova regra de ação realçado.](media/alerts-action-rules/action-rules-new-rule.png)

Como alternativa, você pode criar uma regra de ação enquanto estiver configurando uma regra de alerta.

![Captura de tela mostra a página Criar regra com o botão Criar regra de ação realçado.](media/alerts-action-rules/action-rules-alert-rule.png)

Agora você deve ver a página de fluxo para criar regras de ação. Configure os seguintes elementos:

![Novo fluxo de criação de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow.png)

### <a name="scope"></a>Escopo

Primeiro, escolha o escopo (assinatura do Azure, grupo de recursos ou recurso de destino). Você também pode selecionar uma combinação de escopos em uma única assinatura.

![Escopo da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-scope.png)

### <a name="filter-criteria"></a>Critérios de filtragem

Opcionalmente, você pode definir filtros para que a regra seja aplicada a um subconjunto específico de alertas ou a eventos específicos em cada alerta (por exemplo, apenas "disparado" ou apenas "resolvido").

Os filtros disponíveis são:

* **Severidade**: essa regra será aplicada somente a alertas com as severidades selecionadas.  
Por exemplo, **Severity = Sev1** significa que a regra será aplicada somente a alertas com severidade Sev1.
* **Monitorar serviço**: essa regra será aplicada somente a alertas provenientes dos serviços de monitoramento selecionados.  
Por exemplo, **Monitor Service = "backup do Azure"** significa que a regra será aplicada somente a alertas de backup (provenientes do backup do Azure).
* **Tipo de recurso**: essa regra será aplicada somente a alertas nos tipos de recursos selecionados.  
Por exemplo, **tipo de recurso = "máquinas virtuais"** significa que a regra será aplicada somente a alertas em máquinas virtuais.
* **ID da regra de alerta**: essa regra será aplicada somente aos alertas provenientes de uma regra de alerta específica. O valor deve ser a ID do Gerenciador de recursos da regra de alerta.  
Por exemplo, a **regra de alerta ID = "/subscriptions/SubId1/resourceGroups/ResourceGroup1/Providers/Microsoft.insights/metricalerts/MyAPI-highLatency"** significa que esta regra será aplicada somente aos alertas provenientes da regra de alerta de métrica "MyAPI-highLatency".
* **Condição do monitor**: essa regra será aplicada somente a eventos de alerta com a condição de monitor  especificada – disparados ou **resolvidos**.
* **Descrição**: essa regra será aplicada somente a alertas que contenham uma cadeia de caracteres específica no campo Descrição do alerta. Esse campo contém a descrição da regra de alerta.  
Por exemplo, a **Descrição contém ' prod '** significa que a regra só corresponderá a alertas que contenham a cadeia de caracteres "Prod" em sua descrição.
* **Contexto de alerta (carga)**: essa regra será aplicada somente a alertas que contêm qualquer um ou mais valores específicos nos campos de contexto de alerta.  
Por exemplo, o **contexto de alerta (carga) contém ' computador-01 '** significa que a regra só se aplicará a alertas cuja carga contenha a cadeia de caracteres "computador-01".

Se você definir vários filtros em uma regra, todos eles serão aplicados. Por exemplo, se você definir o **tipo de recurso ' = máquinas virtuais** e **severidade ' = Sev0**, a regra será aplicada somente para alertas do Sev0 em máquinas virtuais.

![Filtros de regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-filters.png)

### <a name="suppression-or-action-group-configuration"></a>Configuração de grupo de ação ou supressão

Em seguida, configure a regra de ação para o suporte de supressão de alerta ou grupo de ações. Você não pode escolher ambos. A configuração atua em todas as instâncias de alerta que correspondem ao escopo e aos filtros definidos anteriormente.

#### <a name="suppression"></a>Supressão

Se você selecionar **supressão**, configure a duração da supressão de ações e notificações. Escolha uma das seguintes opções:
* **De agora (sempre)**: suprime todas as notificações indefinidamente.
* **Em um horário agendado**: suprime notificações dentro de uma duração limitada.
* **Com uma recorrência**: suprime notificações em uma agenda recorrente diária, semanal ou mensal.

![Supressão da regra de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-suppression.png)

#### <a name="action-group"></a>Grupo de ações

Se você selecionar **grupo de ações** na alternância, adicione um grupo de ações existente ou crie um novo.

> [!NOTE]
> Você pode associar apenas um grupo de ações a uma regra de ação.

![Adicionar ou criar nova regra de ação selecionando grupo de ação](media/alerts-action-rules/action-rules-new-rule-creation-flow-action-group.png)

### <a name="action-rule-details"></a>Detalhes da regra de ação

Por fim, configure os seguintes detalhes para a regra de ação:
* Nome
* Grupo de recursos no qual ele foi salvo
* Descrição

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode criar regras de ação com o CLI do Azure usando o comando [AZ monitor Action-regra Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) .  A `az monitor action-rule` referência é apenas uma das muitas [referências CLI do Azure para Azure monitor](/cli/azure/azure-cli-reference-for-monitor).

### <a name="prepare-your-environment"></a>Prepare o seu ambiente

1. [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

   Se preferir, você também pode usar Azure Cloud Shell para concluir as etapas neste artigo.  O Azure Cloud Shell é um ambiente de shell interativo usado por meio do navegador.  Inicie o Cloud Shell usando um destes métodos:

   - Abra o Cloud Shell acessando [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menus, no canto superior direito do [portal do Azure](https://portal.azure.com)

1. Entrar.

   Se você estiver usando uma instalação local da CLI, entre usando o comando [AZ login](/cli/azure/reference-index#az-login) .  Siga as etapas exibidas em nosso terminal para concluir o processo de autenticação.

    ```azurecli
    az login
    ```

1. Instalar a extensão `alertsmanagement`

   O `az monitor action-rule` comando é uma extensão experimental do CLI do Azure principal. Saiba mais sobre referências de extensão na [extensão de uso com CLI do Azure](/cli/azure/azure-cli-extensions-overview?).

   ```azurecli
   az extension add --name alertsmanagement
   ```

   O seguinte aviso é esperado.

   ```output
   The installed extension `alertsmanagement` is experimental and not covered by customer support.  Please use with discretion.
   ```

### <a name="create-action-rules-with-the-azure-cli"></a>Criar regras de ação com o CLI do Azure

Consulte o conteúdo de referência do CLI do Azure para o [AZ monitor Action-regra Create](/cli/azure/ext/alertsmanagement/monitor/action-rule#ext-alertsmanagement-az-monitor-action-rule-create) para saber mais sobre os parâmetros obrigatórios e opcionais.

Crie uma regra de ação para suprimir as notificações em um grupo de recursos.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --scope-type ResourceGroup \
                              --scope /subscriptions/0b1f6471-1bf0-4dda-aec3-cb9272f09590/resourceGroups/MyResourceGroupName \
                              --suppression-recurrence-type Always \
                              --alert-context Contains Computer-01 \
                               --monitor-service Equals "Log Analytics"
```

Crie uma regra de ação para suprimir as notificações de todos os alertas do Sev4 em todas as VMs na assinatura, todos os finais de semana.

```azurecli
az monitor action-rule create --resource-group MyResourceGroupName \
                              --name MyNewActionRuleName \
                              --location Global \
                              --status Enabled \
                              --rule-type Suppression \
                              --severity Equals Sev4 \
                              --target-resource-type Equals Microsoft.Compute/VirtualMachines \
                              --suppression-recurrence-type Weekly \
                              --suppression-recurrence 0 6 \
                              --suppression-start-date 12/09/2018 \
                              --suppression-end-date 12/18/2018 \
                              --suppression-start-time 06:00:00 \
                              --suppression-end-time 14:00:00

```

* * *

## <a name="example-scenarios"></a>Cenários de exemplo

### <a name="scenario-1-suppression-of-alerts-based-on-severity"></a>Cenário 1: supressão de alertas com base na severidade

A contoso deseja suprimir notificações para todos os alertas do Sev4 em todas as VMs na assinatura **ContosoSub** todos os finais de semana.

**Solução:** Crie uma regra de ação com:
* Scope = **ContosoSub**
* Filtros
    * Severidade = **Sev4**
    * Tipo de recurso = **máquinas virtuais**
* Supressão com recorrência definida como semanal e **sábado** e **domingo** verificados

### <a name="scenario-2-suppression-of-alerts-based-on-alert-context-payload"></a>Cenário 2: supressão de alertas com base no contexto de alerta (carga)

A contoso deseja suprimir as notificações de todos os alertas de log gerados para o **computador-01** no **ContosoSub** indefinidamente, pois ele passa por manutenção.

**Solução:** Crie uma regra de ação com:
* Scope = **ContosoSub**
* Filtros
    * Monitorar serviço = **log Analytics**
    * O contexto do alerta (carga) contém o **computador-01**
* Supressão definida como **de Now (Always)**

### <a name="scenario-3-action-group-defined-at-a-resource-group"></a>Cenário 3: grupo de ações definido em um grupo de recursos

A contoso definiu [um alerta de métrica em um nível de assinatura](./alerts-metric-overview.md#monitoring-at-scale-using-metric-alerts-in-azure-monitor). Mas ele deseja definir as ações que disparam especificamente para alertas gerados a partir do grupo de recursos **ContosoRG**.

**Solução:** Crie uma regra de ação com:
* Scope = **ContosoRG**
* Sem filtros
* Grupo de ação definido como **ContosoActionGroup**

> [!NOTE]
> *Grupos de ação definidos em regras de ação e regras de alerta operam de forma independente, sem eliminação de duplicação.* No cenário descrito anteriormente, se um grupo de ações for definido para a regra de alerta, ele será disparado em conjunto com o grupo de ação definido na regra de ação.

## <a name="managing-your-action-rules"></a>Gerenciando suas regras de ação

### <a name="portal"></a>[Portal](#tab/portal)

Você pode exibir e gerenciar suas regras de ação no modo de exibição de lista:

![Exibição de lista de regras de ação](media/alerts-action-rules/action-rules-list-view.png)

A partir daqui, você pode habilitar, desabilitar ou excluir regras de ação em escala, marcando a caixa de seleção ao lado delas. Quando você seleciona uma regra de ação, sua página de configuração é aberta. A página ajuda a atualizar a definição da regra de ação e a habilitá-la ou desabilitá-la.

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Você pode exibir e gerenciar suas regras de ação usando o comando [AZ monitor Action-Rule](/cli/azure/ext/alertsmanagement/monitor) da CLI do Azure.

Antes de gerenciar as regras de ação com o CLI do Azure, prepare seu ambiente usando as instruções fornecidas em [Configurando uma regra de ação](#configuring-an-action-rule).

```azurecli
# List all action rules for a subscription
az monitor action-rule list

# Get details of an action rule
az monitor action-rule show --resource-group MyResourceGroupName --name MyActionRuleName

# Update an action rule.
az monitor action-rule update --resource-group MyResourceGroupName --name MyActionRuleName --status Disabled

# Delete an action rule.
az monitor action-rule delete --resource-group MyResourceGroupName --name MyActionRuleName
```

* * *

## <a name="best-practices"></a>Práticas recomendadas

Os alertas de log que você cria com a opção [número de resultados](./alerts-unified-log.md) geram uma única instância de alerta usando o resultado da pesquisa inteiro (que pode se estender por vários computadores). Nesse cenário, se uma regra de ação usar o filtro de **contexto de alerta (carga)** , ela agirá na instância de alerta contanto que haja uma correspondência. No cenário 2, descrito anteriormente, se os resultados da pesquisa para o alerta de log gerado contiverem o **computador-01** e o **computador-02**, toda a notificação será suprimida. Não há nenhuma notificação gerada para o **computador-02** .

![Diagrama mostra as regras de ação e alertas de log com uma única instância de alerta realçada.](media/alerts-action-rules/action-rules-log-alert-number-of-results.png)

Para usar melhor os alertas de log com regras de ação, crie alertas de log com a opção [medição de métrica](./alerts-unified-log.md) . As instâncias de alerta separadas são geradas por essa opção, com base em seu campo de grupo definido. Em seguida, no cenário 2, as instâncias de alerta separadas são geradas para o **computador-01** e o **computador-02**. Devido à regra de ação descrita no cenário, somente a notificação para o **computador-01** é suprimida. A notificação para o **computador-02** continua a ser acionada normalmente.

![Regras de ação e alertas de log (número de resultados)](media/alerts-action-rules/action-rules-log-alert-metric-measurement.png)

## <a name="faq"></a>Perguntas frequentes

### <a name="while-im-configuring-an-action-rule-id-like-to-see-all-the-possible-overlapping-action-rules-so-that-i-avoid-duplicate-notifications-is-it-possible-to-do-that"></a>Enquanto estou Configurando uma regra de ação, gostaria de ver todas as regras de ação sobrepostas possíveis, para evitar notificações duplicadas. É possível fazer isso?

Depois de definir um escopo ao configurar uma regra de ação, você poderá ver uma lista de regras de ação que se sobrepõem no mesmo escopo (se houver). Essa sobreposição pode ser uma das seguintes opções:

* Uma correspondência exata: por exemplo, a regra de ação que você está definindo e a regra de ação sobreposta estão na mesma assinatura.
* Um subconjunto: por exemplo, a regra de ação que você está definindo está em uma assinatura e a regra de ação sobreposta está em um grupo de recursos dentro da assinatura.
* Um superconjunto: por exemplo, a regra de ação que você está definindo está em um grupo de recursos e a regra de ação sobreposta está na assinatura que contém o grupo de recursos.
* Uma interseção: por exemplo, a regra de ação que você está definindo está em **VM1** e **VM2**, e a regra de ação sobreposta está em **VM2** e **VM3**.

![Captura de tela mostra a nova página regra de ação com as regras de ação sobrepostas exibidas nas regras de ação definidas na mesma janela de escopo.](media/alerts-action-rules/action-rules-overlapping.png)

### <a name="while-im-configuring-an-alert-rule-is-it-possible-to-know-if-there-are-already-action-rules-defined-that-might-act-on-the-alert-rule-im-defining"></a>Enquanto estou Configurando uma regra de alerta, é possível saber se já existem regras de ação definidas que possam atuar na regra de alerta que estou definindo?

Depois de definir o recurso de destino para sua regra de alerta, você pode ver a lista de regras de ação que agem no mesmo escopo (se houver) selecionando **Exibir ações configuradas** na seção **ações** . Essa lista é preenchida com base nos seguintes cenários para o escopo:

* Uma correspondência exata: por exemplo, a regra de alerta que você está definindo e a regra de ação estão na mesma assinatura.
* Um subconjunto: por exemplo, a regra de alerta que você está definindo está em uma assinatura e a regra de ação está em um grupo de recursos dentro da assinatura.
* Um superconjunto: por exemplo, a regra de alerta que você está definindo está em um grupo de recursos e a regra de ação está na assinatura que contém o grupo de recursos.
* Uma interseção: por exemplo, a regra de alerta que você está definindo está em **VM1** e **VM2**, e a regra de ação está em **VM2** e **VM3**.

![Regras de ação sobrepostas](media/alerts-action-rules/action-rules-alert-rule-overlapping.png)

### <a name="can-i-see-the-alerts-that-have-been-suppressed-by-an-action-rule"></a>Posso ver os alertas que foram suprimidos por uma regra de ação?

Na [página lista de alertas](./alerts-managing-alert-instances.md), você pode escolher uma coluna adicional chamada **status de supressão**. Se a notificação de uma instância de alerta foi suprimida, ela mostraria esse status na lista.

![Instâncias de alerta suprimidas](media/alerts-action-rules/action-rules-suppressed-alerts.png)

### <a name="if-theres-an-action-rule-with-an-action-group-and-another-with-suppression-active-on-the-same-scope-what-happens"></a>Se houver uma regra de ação com um grupo de ação e outra com supressão ativa no mesmo escopo, o que acontecerá?

A supressão sempre tem precedência no mesmo escopo.

### <a name="what-happens-if-i-have-a-resource-thats-monitored-in-two-separate-action-rules-do-i-get-one-or-two-notifications-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso que é monitorado em duas regras de ação separadas? Obtenho uma ou duas notificações? Por exemplo, **VM2** no cenário a seguir:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with action group AG1`

Para cada alerta em VM1 e VM3, o grupo de ações AG1 seria disparado uma vez. Para cada alerta no **VM2**, o grupo de ações AG1 seria disparado duas vezes, porque as regras de ação não eliminam a duplicação de ações.

### <a name="what-happens-if-i-have-a-resource-monitored-in-two-separate-action-rules-and-one-calls-for-action-while-another-for-suppression-for-example-vm2-in-the-following-scenario"></a>O que acontece se eu tiver um recurso monitorado em duas regras de ação separadas e uma chamada para ação enquanto outra para supressão? Por exemplo, **VM2** no cenário a seguir:

   `action rule AR1 defined for VM1 and VM2 with action group AG1`

   `action rule AR2 defined for VM2 and VM3 with suppression`

Para cada alerta no VM1, o grupo de ações AG1 seria disparado uma vez. As ações e notificações para cada alerta em VM2 e VM3 serão suprimidas.

### <a name="what-happens-if-i-have-an-alert-rule-and-an-action-rule-defined-for-the-same-resource-calling-different-action-groups-for-example-vm1-in-the-following-scenario"></a>O que acontece se eu tiver uma regra de alerta e uma regra de ação definida para o mesmo recurso chamando grupos de ação diferentes? Por exemplo, **VM1** no cenário a seguir:

   `alert rule rule1 on VM1 with action group AG2`

   `action rule AR1 defined for VM1 with action group AG1`

Para cada alerta no VM1, o grupo de ações AG1 seria disparado uma vez. Sempre que a regra de alerta "rule1" for disparada, ela também disparará o AG2 adicionalmente. Grupos de ação definidos em regras de ação e regras de alerta operam de forma independente, sem eliminação de duplicação.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre alertas no Azure](./alerts-overview.md)
