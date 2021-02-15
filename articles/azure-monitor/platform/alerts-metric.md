---
title: Criar, exibir e gerenciar alertas de métrica usando Azure Monitor
description: Saiba como usar portal do Azure ou CLI para criar, exibir e gerenciar regras de alerta de métricas.
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 01/11/2021
ms.subservice: alerts
ms.openlocfilehash: 6919040308dcd3a80c3c0b05ec729744099b6d8f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944489"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor

Os alertas de métrica no Azure Monitor fornecem uma maneira de ser notificado quando uma de suas métricas ultrapassar um limite. Os alertas de métrica funcionam com uma variedade de métricas de plataforma multidimensionais, métricas personalizadas, métricas padrão do Application Insights e métricas personalizadas. Neste artigo, descreveremos como criar, exibir e gerenciar regras de alerta de métricas por meio de portal do Azure e CLI do Azure. Você também pode criar regras de alerta de métrica usando modelos de Azure Resource Manager, que são descritos em [um artigo separado](alerts-metric-create-templates.md).

Saiba mais sobre como os alertas de métrica funcionam conferindo a [visão geral dos alertas de métrica](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Criar com o portal do Azure

O procedimento a seguir descreve como criar uma regra de alerta de métrica no portal do Azure:

1. Em [portal do Azure](https://portal.azure.com), clique em **Monitor**. A folha Monitor consolida todas as suas configurações e dados de monitoramento em uma exibição.

2. Clique em **Alertas** e, em seguida, clique em **+ Nova regra de alerta**.

    > [!TIP]
    > A maioria das folhas de recursos também tem **Alertas** no menu de recursos, em **Monitoramento**, e você pode criar alertas de lá também.

3. Clique em **Selecionar destino** no painel de contexto exibido e selecione um recurso de destino para o qual você quer criar um alerta. Use os menus suspensos **Assinatura** e **Tipo de recurso** para localizar o recurso que deseja monitorar. Também é possível usar a barra de pesquisa para localizar o recurso.

4. Se o recurso selecionado tiver métricas com base nas quais você pode criar alertas, os **Sinais disponíveis** na parte inferior direita incluirão as métricas. Você pode exibir a lista completa dos tipos de recurso compatíveis com alertas de métrica neste [artigo](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Após selecionar um recurso de destino, clique em **Adicionar condição**.

6. Você verá uma lista dos sinais com suporte para o recurso. Selecione a métrica na qual deseja criar um alerta.

7. Você verá um gráfico para a métrica das últimas seis horas. Use o menu suspenso **período do gráfico** para selecionar para ver o histórico mais longo da métrica.

8. Se a métrica tiver dimensões, você verá uma tabela de dimensões apresentada. Selecione um ou mais valores por dimensão.
    - Os valores de dimensão exibidos são baseados nos dados de métrica do último dia.
    - Se o valor de dimensão que você está procurando não for exibido, clique em "adicionar valor personalizado" para adicionar um valor de dimensão personalizado.
    - Você também pode **selecionar todos os valores atuais e futuros** para qualquer uma das dimensões. Isso dimensionará dinamicamente a seleção para todos os valores atuais e futuros de uma dimensão.

    A regra de alerta de métrica avaliará a condição para todas as combinações de valores selecionados. [Saiba mais sobre como os alertas de métricas multidimensionais funcionam](alerts-metric-overview.md).

9. Selecione o tipo de **limite** , o **operador** e o **tipo de agregação**. Isso determinará a lógica que será avaliada pela regra de alerta de métrica.
    - Se você estiver usando um limite **estático** , continue a definir um **valor de limite**. O gráfico de métrica pode ajudar a determinar o que pode ser um limite razoável.
    - Se você estiver usando um limite **dinâmico** , continue a definir a **sensibilidade do limite**. O gráfico de métricas exibirá os limites calculados com base nos dados recentes. [Saiba mais sobre as opções de sensibilidade e o tipo de condição de Limites Dinâmicos](alerts-dynamic-thresholds.md).

10. Opcionalmente, refine a condição ajustando a **granularidade de agregação** e a **frequência de avaliação**. 

11. Clique em **Concluído**.

12. Opcionalmente, adicione outro critério se quiser monitorar uma regra de alerta complexa. No momento, os usuários podem ter regras de alerta com critérios de Limites Dinâmicos como um único critério.

13. Preencha os **detalhes do alerta** , como **nome da regra de alerta**, **Descrição** e **severidade**.

14. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criar um novo.

15. Clique em **Concluído** para salvar a regra de alerta de métrica.

> [!NOTE]
> Regras de alerta de métrica criadas por meio do portal são criadas no mesmo grupo de recursos que o recurso de destino.

## <a name="view-and-manage-with-azure-portal"></a>Exibir e gerenciar com o portal do Azure

É possível exibir e gerenciar regras de alerta de métrica usando a folha Gerenciar Regras, em Alertas. O procedimento a seguir mostra como exibir suas regras de alerta de métrica e editar uma delas.

1. No portal do Azure, navegue até **Monitor**

2. Clique em **Alertas** e **Gerenciar regras**

3. Na folha **Gerenciar regras**, você pode exibir todas as regras de alertas configuradas em suas assinaturas. Você pode filtrar ainda mais as regras usando o  **grupo de recursos**, o **tipo de recurso** e o **recurso**. Se quiser ver somente os alertas de métrica, selecione Métricas como o **Tipo de sinal**.

    > [!TIP]
    > Na folha **Gerenciar regras**, você pode selecionar várias regras de alerta e habilitar ou desabilitá-las. Isso poderá ser útil quando for necessário colocar determinados recursos de destino em manutenção.

4. Clique no nome da regra de alerta de métrica que deseja editar.

5. Em Editar Regra, clique nos **Critérios de alerta** que deseja editar. Você pode alterar a métrica, a condição de limite e outros campos conforme necessário

    > [!NOTE]
    > Não é possível editar o **Recurso de destino** e o **Nome da Regra de Alerta** após o alerta de métrica ser criado.

6. Clique em **Concluído** para salvar suas edições.


## <a name="with-azure-cli"></a>Com a CLI do Azure

As seções anteriores descreveram como criar, exibir e gerenciar regras de alerta de métrica usando portal do Azure. Esta seção descreve como fazer o mesmo usando a [CLI do Azure](/cli/azure/get-started-with-azure-cli) de multiplataforma. O modo mais rápido de começar a usar a CLI do Azure é com o [Azure Cloud Shell](../../cloud-shell/overview.md). Para este artigo, usaremos Cloud Shell.

1. Vá para portal do Azure, clique em **Cloud Shell**.

2. No prompt, você pode usar comandos com a opção ``--help`` para saber mais sobre o comando e como usá-lo. Por exemplo, o comando a seguir mostra a lista de comandos disponíveis para criar, exibir e gerenciar alertas de métricas

    ```azurecli
    az monitor metrics alert --help
    ```

3. É possível criar uma regra de alerta de métrica simples que monitore se o percentual de CPU médio em uma VM é maior que 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. É possível exibir todos os alertas de métrica em um grupo de recursos usando o seguinte comando

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. Você pode ver os detalhes de uma regra de alerta de métrica específica usando o nome ou a ID de recurso da regra.

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. É possível desabilitar uma regra de alerta de métrica usando o comando a seguir.

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. É possível excluir uma regra de alerta de métrica usando o comando a seguir.

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="with-powershell"></a>Com o PowerShell

As regras de alerta de métrica têm cmdlets dedicados do PowerShell disponíveis:

- [Add-AzMetricAlertRuleV2](/powershell/module/az.monitor/add-azmetricalertrulev2): cria uma nova regra de alerta de métrica ou atualiza uma existente.
- [Get-AzMetricAlertRuleV2](/powershell/module/az.monitor/get-azmetricalertrulev2): Obtenha uma ou mais regras de alerta de métrica.
- [Remove-AzMetricAlertRuleV2](/powershell/module/az.monitor/remove-azmetricalertrulev2): exclui uma regra de alerta de métrica.

## <a name="with-rest-api"></a>Com a API REST

- [Criar ou atualizar](/rest/api/monitor/metricalerts/createorupdate): Crie uma nova regra de alerta de métrica ou atualize uma existente.
- [Get](/rest/api/monitor/metricalerts/get): obter uma regra de alerta de métrica específica.
- [Listar por grupo de recursos](/rest/api/monitor/metricalerts/listbyresourcegroup): Obtenha uma lista de regras de alerta de métrica em um grupo de recursos específico.
- [Listar por assinatura](/rest/api/monitor/metricalerts/listbysubscription): Obtenha uma lista de regras de alerta de métrica em uma assinatura específica.
- [Atualizar](/rest/api/monitor/metricalerts/update): atualizar uma regra de alerta de métrica.
- [Excluir](/rest/api/monitor/metricalerts/delete): excluir uma regra de alerta de métrica.

## <a name="next-steps"></a>Próximas etapas

- [Criar alertas de métrica usando modelos de Azure Resource Manager](./alerts-metric-create-templates.md)
- [Entenda como funcionam os alertas de métrica](alerts-metric-overview.md)
- [Entender como funcionam os alertas de métrica com condição de limites dinâmicos](alerts-dynamic-thresholds.md)
- [Compreender o esquema de web hook para alertas de métrica](./alerts-metric-near-real-time.md#payload-schema)
- [Solucionando problemas em alertas de métricas](alerts-troubleshoot-metric.md)
