---
title: Como criar alertas para a automação do Azure Gerenciamento de Atualizações
description: Este artigo informa como configurar alertas do Azure para notificar sobre o status de avaliações ou implantações de atualização.
services: automation
ms.subservice: update-management
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 74207fe088034ff8d102fb2254d8ab78a6d57671
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579701"
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

2. Na página **criar regra de alerta** , sua conta de automação já está selecionada como o recurso. Se você quiser alterá-lo, selecione **Editar recurso**.

3. Na página Selecionar um recurso, escolha **contas de automação** na lista suspensa **Filtrar por tipo de recurso** .

4. Selecione a conta de automação que você deseja usar e, em seguida, selecione **concluído**.

5. Selecione **Adicionar condição** para escolher o sinal apropriado para seu requisito.

6. Para uma dimensão, selecione um valor válido na lista. Se o valor desejado não estiver na lista, selecione **\+** ao lado da dimensão e digite o nome personalizado. Em seguida, selecione o valor a ser pesquisado. Se você quiser selecionar todos os valores de uma dimensão, selecione o **botão \* selecionar** . Se você não escolher um valor para uma dimensão, o Gerenciamento de Atualizações vai ignorar essa dimensão.

    ![Configurar sinal lógico](./media/manage-updates-for-vm/signal-logic.png)

7. Em **lógica de alerta**, insira valores nos **campos agregação de tempo** e **limite** e, em seguida, selecione **concluído**.

8. Na próxima página, insira um nome e uma descrição para o alerta.

9. Defina o campo **Gravidade** como **Informational(Sev 2)** para uma execução bem-sucedida ou **Informational(Sev 1)** para uma execução com falha.

    ![Captura de tela mostra a seção definir detalhes do alerta com nome da regra de alerta, descrição e campos de severidade realçados.](./media/manage-updates-for-vm/define-alert-details.png)

10. Selecione **Sim** para habilitar a regra de alerta.

## <a name="configure-action-groups-for-your-alerts"></a>Configurar grupos de ações para seus alertas

Depois de configurar os alertas, você pode configurar um grupo de ações, que é um grupo de ações a serem usadas em vários alertas. As ações podem incluir notificações por email, runbooks, WebHooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../../azure-monitor/alerts/action-groups.md).

1. Selecione um alerta e, em seguida, selecione **Criar** em **Grupos de Ações**.

2. Insira um nome completo e um nome curto para o grupo de ações. O Gerenciamento de Atualizações usa o nome curto ao enviar notificações usando o grupo especificado.

3. Em **Ações**, insira um nome que especifique a ação, por exemplo, **Notificação por email**.

4. Para **Tipo de ação**, selecione o tipo apropriado, por exemplo, **Email/SMS/Push/Voz**.

5. Selecione em **Editar detalhes**.

6. Preencha o painel do tipo de ação. Por exemplo, se estiver usando **email/SMS/Push/voz**, insira um nome de ação, marque a caixa de seleção **email** , insira um endereço de email válido e, em seguida, selecione **OK**.

    ![Configurar um grupo de ação de email](./media/manage-updates-for-vm/configure-email-action-group.png)

7. No painel Adicionar grupo de ações, selecione **OK**.

8. Para um email de alerta, você pode personalizar o assunto do email. Selecione **Personalizar ações** em **Criar regra** e, em seguida, selecione **Assunto do email**.

9. Quando terminar, selecione **Criar regra de alerta**.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [alertas no Azure monitor](../../azure-monitor/alerts/alerts-overview.md).

* Saiba mais sobre [consultas de log](../../azure-monitor/logs/log-query-overview.md) para recuperar e analisar dados de um espaço de trabalho log Analytics.

* Gerenciar o [uso e os custos com logs de Azure monitor](../../azure-monitor/logs/manage-cost-storage.md) descreve como controlar seus custos alterando seu período de retenção de dados e como analisar e alertar o uso de dados.