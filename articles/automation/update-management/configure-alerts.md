---
title: Como criar alertas para a automação do Azure Gerenciamento de Atualizações
description: Este artigo informa como configurar alertas do Azure para notificar sobre o status de avaliações ou implantações de atualização.
services: automation
ms.subservice: update-management
ms.date: 03/15/2021
ms.topic: conceptual
ms.openlocfilehash: 224a7b5457a099fd763ac657349fc5497824ab76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601402"
---
# <a name="how-to-create-alerts-for-update-management"></a>Como criar alertas para Gerenciamento de Atualizações

Os alertas no Azure notificam proativamente os resultados de trabalhos de runbook, problemas de integridade do serviço ou outros cenários relacionados à sua conta de automação. A automação do Azure não inclui regras de alerta pré-configuradas, mas você pode criar suas próprias com base nos dados que ele gera. Este artigo fornece orientação sobre como criar regras de alerta usando as métricas incluídas com Gerenciamento de Atualizações.

## <a name="available-metrics"></a>Métricas disponíveis

A automação do Azure cria duas métricas de plataforma distintas relacionadas a Gerenciamento de Atualizações que são coletadas e encaminhadas para Azure Monitor. Essas métricas estão disponíveis para análise usando [Metrics Explorer](../../azure-monitor/essentials/metrics-charts.md) e para alertas usando uma [regra de alerta de métricas](../../azure-monitor/alerts/alerts-metric.md).

As duas métricas emitidas são:

* Total de Execuções de Computador da Implantação de Atualização
* Total de Execuções da Implantação de Atualização

Quando usado para alertas, ambas as métricas dão suporte a dimensões que contêm informações adicionais para ajudar a fazer o escopo do alerta para um detalhe de implantação de atualização específico. A tabela a seguir mostra os detalhes sobre a métrica e as dimensões disponíveis ao configurar um alerta.

|Nome do sinal|Dimensões|Descrição
|---|---|---|
|`Total Update Deployment Runs`|– Nome da implantação de atualização<br>– Status | Alerta quanto ao status geral de uma implantação de atualização.|
|`Total Update Deployment Machine Runs`|– Nome da implantação de atualização</br>– Status</br>– Computador de destino</br>– ID de execução da implantação de atualizações    |Alerta quanto ao status de uma implantação de atualização direcionada a computadores específicos.|

## <a name="create-alert"></a>Criar alerta

Siga as etapas abaixo para configurar alertas para que você saiba o status de uma implantação de atualização. Se você for novo nos alertas do Azure, consulte [visão geral de alertas do Azure](../../azure-monitor/alerts/alerts-overview.md).

1. Na sua conta de automação, selecione **alertas** em **monitoramento** e, em seguida, selecione **nova regra de alerta**.

1. Na página **criar regra de alerta** , sua conta de automação já está selecionada como o recurso. Se você quiser alterá-lo, selecione **Editar recurso**.

1. Na página Selecionar um recurso, escolha **contas de automação** na lista suspensa **Filtrar por tipo de recurso** .

1. Selecione a conta de automação que você deseja usar e, em seguida, selecione **concluído**.

1. Selecione **Adicionar condição** para escolher o sinal apropriado para seu requisito.

1. Para uma dimensão, selecione um valor válido na lista. Se o valor desejado não estiver na lista, selecione **\+** ao lado da dimensão e digite o nome personalizado. Em seguida, selecione o valor a ser pesquisado. Se você quiser selecionar todos os valores de uma dimensão, selecione o **botão \* selecionar** . Se você não escolher um valor para uma dimensão, o Gerenciamento de Atualizações vai ignorar essa dimensão.

    ![Configurar sinal lógico](./media/manage-updates-for-vm/signal-logic.png)

1. Em **lógica de alerta**, insira valores nos **campos agregação de tempo** e **limite** e, em seguida, selecione **concluído**.

1. Na próxima página, insira um nome e uma descrição para o alerta.

1. Defina o campo **Gravidade** como **Informational(Sev 2)** para uma execução bem-sucedida ou **Informational(Sev 1)** para uma execução com falha.

    ![Captura de tela mostra a seção definir detalhes do alerta com nome da regra de alerta, descrição e campos de severidade realçados.](./media/manage-updates-for-vm/define-alert-details.png)

1. Selecione **Sim** para habilitar a regra de alerta.

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ações para seus alertas

Depois de configurar os alertas, você pode configurar um grupo de ações, que é um grupo de ações a serem usadas em vários alertas. As ações podem incluir notificações por email, runbooks, WebHooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../../azure-monitor/alerts/action-groups.md).

1. Selecione um alerta e, em seguida, selecione **Adicionar grupos de ação** em **ações**. Isso exibirá o painel **selecionar um grupo de ações a ser anexado a este alerta** .

   :::image type="content" source="./media/manage-updates-for-vm/select-an-action-group.png" alt-text="Uso e custos estimados.":::

1. Marque a caixa de seleção do grupo de ações para anexar e pressione Selecionar.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [alertas no Azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Saiba mais sobre [consultas de log](../../azure-monitor/logs/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log Analytics.

* Gerenciar o [uso e os custos com logs de Azure monitor](../../azure-monitor/logs/manage-cost-storage.md) descreve como controlar seus custos alterando seu período de retenção de dados e como analisar e alertar o uso de dados.