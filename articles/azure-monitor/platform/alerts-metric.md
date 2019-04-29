---
title: Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor
description: Saiba como usar a CLI ou o portal do Azure para criar, exibir e gerenciar regras de alerta de métrica.
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: 117b65265c853194e93a97fe5e2b2dcc6e9f5bc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712970"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>Criar, exibir e gerenciar alertas de métrica usando o Azure Monitor

Os alertas de métrica no Azure Monitor possibilitam que você seja notificado quando uma das suas métricas ultrapassar um limite. Os alertas de métrica funcionam com uma variedade de métricas de plataforma multidimensionais, métricas personalizadas, métricas padrão do Application Insights e métricas personalizadas. Neste artigo, descreveremos como criar, exibir e gerenciar regras de alerta de métrica usando o portal do Azure e a CLI do Azure. Também é possível criar regras de alerta de métrica usando modelos do Azure Resource Manager, processo que é descrito em [outro artigo](alerts-metric-create-templates.md).

Saiba mais sobre como os alertas de métrica funcionam conferindo a [visão geral dos alertas de métrica](alerts-metric-overview.md).

## <a name="create-with-azure-portal"></a>Criar com o portal do Azure

O procedimento a seguir descreve como criar uma regra de alerta de métrica no portal do Azure:

1. No [portal do Azure](https://portal.azure.com), clique em **Monitor**. A folha Monitor consolida todas as suas configurações e dados de monitoramento em uma exibição.

2. Clique em **Alertas** e, em seguida, clique em **+ Nova regra de alerta**.

    > [!TIP]
    > A maioria das folhas de recursos também tem **Alertas** no menu de recursos, em **Monitoramento**, e você pode criar alertas de lá também.

3. Clique em **Selecionar destino** no painel de contexto exibido e selecione um recurso de destino para o qual você quer criar um alerta. Use os menus suspensos **Assinatura** e **Tipo de recurso** para localizar o recurso que deseja monitorar. Também é possível usar a barra de pesquisa para localizar o recurso.

4. Se o recurso selecionado tiver métricas com base nas quais você pode criar alertas, os **Sinais disponíveis** na parte inferior direita incluirão as métricas. Você pode exibir a lista completa dos tipos de recurso compatíveis com alertas de métrica neste [artigo](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

5. Após selecionar um recurso de destino, clique em **Adicionar condição**.

6. Você verá uma lista dos sinais com suporte para o recurso. Selecione a métrica na qual deseja criar um alerta.

7. Também é possível refinar a métrica ajustando o **Período** e a **Agregação**. Se a métrica tem dimensões, a tabela **Dimensões** é apresentada. Selecione um ou mais valores por dimensão. O alerta de métrica será executado e avaliará a condição com relação a todas as combinações de valores selecionadas. [Saiba mais sobre como os alertas de métricas multidimensionais funcionam](alerts-metric-overview.md). Também é possível **Selecionar \*** para qualquer uma das dimensões. **Selecionar \*** dimensionará dinamicamente a seleção para todos os valores atuais e futuros de uma dimensão.

8. Você verá um gráfico da métrica referente às últimas 6 horas. Defina os parâmetros de alerta **Tipo de Condição**, **Frequência**, **Operador**, **Limite** ou **Confidencialidade**, que determinam a lógica que a regra de alerta de métrica avaliará. [Saiba mais sobre as opções de confidencialidade e tipo de condição dos Limites Dinâmicos](alerts-dynamic-thresholds.md).

9. Se você está usando um limite estático, o gráfico de métrica pode ajudar a determinar o que pode ser um limite razoável. Se você está usando Limites Dinâmicos, o gráfico de métrica exibe os limites calculados com base em dados recentes.

10. Clique em **Concluído**

11. Opcionalmente, adicione outro critério se quiser monitorar uma regra de alerta complexa. No momento, os usuários podem ter regras de alerta com critérios de Limites Dinâmicos como um único critério.

12. Preencha os **Detalhes do alerta**, como **Nome da Regra de Alerta**, **Descrição** e **Gravidade**

13. Adicione um grupo de ações ao alerta selecionando um grupo de ações existente ou criar um novo.

14. Clique em **Concluído** para salvar a regra de alerta de métrica.

> [!NOTE]
> Regras de alerta de métrica criadas por meio do portal são criadas no mesmo grupo de recursos que o recurso de destino.

## <a name="view-and-manage-with-azure-portal"></a>Exibir e gerenciar com o portal do Azure

É possível exibir e gerenciar regras de alerta de métrica usando a folha Gerenciar Regras, em Alertas. O procedimento a seguir mostra como exibir suas regras de alerta de métrica e editar uma delas.

1. No portal do Azure, navegue até **Monitor**

2. Clique em **Alertas** e **Gerenciar regras**

3. Na folha **Gerenciar regras**, você pode exibir todas as regras de alertas configuradas em suas assinaturas. Você pode filtrar as regras com base no **Grupo de recursos**, no **Tipo de recurso** e no **Recurso**. Se quiser ver somente os alertas de métrica, selecione Métricas como o **Tipo de sinal**.

    > [!TIP]
    > Na folha **Gerenciar regras**, você pode selecionar várias regras de alerta e habilitar ou desabilitá-las. Isso poderá ser útil quando for necessário colocar determinados recursos de destino em manutenção.

4. Clique no nome da regra de alerta de métrica que deseja editar.

5. Em Editar Regra, clique nos **Critérios de alerta** que deseja editar. Você pode alterar a métrica, a condição de limite e outros campos conforme necessário

    > [!NOTE]
    > Não é possível editar o **Recurso de destino** e o **Nome da Regra de Alerta** após o alerta de métrica ser criado.

6. Clique em **Concluído** para salvar suas edições.

## <a name="with-azure-cli"></a>Com a CLI do Azure

As seções anteriores descreveram como criar, exibir e gerenciar regras de alerta de métrica usando o portal do Azure. Esta seção descreve como fazer o mesmo usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) de multiplataforma. O modo mais rápido de começar a usar a CLI do Azure é com o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest). Neste artigo, usaremos o Cloud Shell.

1. Vá até o portal do Azure e clique em **Cloud Shell**.

2. No prompt, você pode usar comandos com a opção ``--help`` para saber mais sobre o comando e como usá-lo. Por exemplo, o comando a seguir mostra a lista de comandos disponíveis para criar, exibir e gerenciar alertas de métrica

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

## <a name="next-steps"></a>Próximas etapas

- [Criar alertas de métrica usando modelos do Azure Resource Manager](../../azure-monitor/platform/alerts-enable-template.md).
- [Compreender como os alertas de métrica funcionam](alerts-metric-overview.md).
- [Entender como funcionam os alertas de métrica com a condição de Limites Dinâmicos](alerts-dynamic-thresholds.md).
- [Compreender o esquema de web hook para alertas de métrica](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

