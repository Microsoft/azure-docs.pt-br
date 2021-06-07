---
title: Configurar alertas-portal do Azure-banco de dados do Azure para PostgreSQL-servidor flexível
description: Este artigo descreve como configurar e acessar alertas de métricas para o banco de dados do Azure para PostgreSQL-servidor flexível do portal do Azure.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 365cb5604c1f9ddc2c0d7f3cc63daff96b043515
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608044"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---flexible-server"></a>Use o portal do Azure para configurar alertas em métricas para o banco de dados do Azure para PostgreSQL – servidor flexível

> [!IMPORTANT]
> O Servidor Flexível do Banco de Dados do Azure para PostgreSQL está em versão prévia

Este artigo mostra como configurar alertas do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure. Você pode receber um alerta com base em métricas de monitoramento para seus serviços do Azure.

O alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite atribuído por você. Ele dispara tanto quando a condição é atendida pela primeira vez e quanto posteriormente, quando essa condição não está mais sendo atendida.

Você pode configurar um alerta para fazer as seguintes ações quando ele disparar:

* Enviar notificações por email para o administrador e coadministradores de serviços.
* Enviar um email para outros emails que você especificar.
* Chamar um webhook.

Você pode configurar e obter informações sobre as regras de alerta usando:

* [Azure portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta em uma métrica no Portal do Azure

1. No [Portal do Azure](https://portal.azure.com/), selecione o servidor do Banco de Dados do Azure para PostgreSQL que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Alertas** como mostrado abaixo:

   :::image type="content" source="./media/howto-alert-on-metrics/2-alert-rules.png" alt-text="Selecionar regras de alerta":::

3. Selecione **Adicionar alerta de métrica** (ícone +).

4. A página **Criar regra** é aberta, conforme mostrado abaixo. Preencha as informações obrigatórias:

   :::image type="content" source="./media/howto-alert-on-metrics/4-add-rule-form.png" alt-text="Formulário Adicionar alerta de métrica":::

5. Dentro da seção **Condição**, selecione **Adicionar condição**.

6. Selecione uma métrica da lista de sinais sobre a qual deseja ser alertado. Neste exemplo, selecione "Porcentagem de armazenamento".

   :::image type="content" source="./media/howto-alert-on-metrics/6-configure-signal-logic.png" alt-text="Selecionar métrica":::

7. Configure a lógica de alerta, incluindo a **Condição** (por exemplo, "Maior que"), o **Limite** (por exemplo, 85%), a **Agregação de Tempo**, o **Período** durante o qual a regra de métrica deverá ser atendida antes de o alerta disparar (por exemplo, "Nos últimos 30 minutos") e **frequência**.

   Selecione **Concluído** ao concluir.

   :::image type="content" source="./media/howto-alert-on-metrics/7-set-threshold-time.png" alt-text="Definir limite":::

8. Dentro da seção **Grupos de Ações**, selecione **Criar Novo** para criar um novo grupo para receber notificações sobre o alerta.

9. Preencha o formulário "Adicionar grupo de ações" com um nome, o nome curto, a assinatura e o grupo de recursos.

10. Configure o tipo de ação **Email/SMS/Push/Voz**.

    1. Escolha "Enviar email para a Função do Azure Resource Manager" para selecionar os Proprietários da assinatura, Colaboradores e Leitores para receber notificações.

    2. Opcionalmente, forneça um URI válido no campo **Webhook** se você quiser chamá-lo quando o alerta for disparado.

    3. Selecione **OK** ao concluir.

    :::image type="content" source="./media/howto-alert-on-metrics/10-action-group-type.png" alt-text="Grupo de ações":::

11. Especifique um Nome da regra de alerta, uma Descrição e uma Gravidade.

    :::image type="content" source="./media/howto-alert-on-metrics/11-name-description-severity.png" alt-text="Especificar, descrição e severidade do nome"::: 

12. Selecione **Criar regra de alerta** para criar o alerta.

    Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

## <a name="manage-your-alerts"></a>Gerenciar seus alertas

Depois de criar um alerta, você poderá selecioná-lo e executar as seguintes ações:

* Exibir um grafo mostrando o limite de métrica e os valores reais do dia anterior relevante para este alerta.
* **Editar** ou **Excluir** a regra de alerta.
* **Desabilitar** ou **Habilitar** o alerta, se desejar interromper temporariamente ou retomar o recebimento de notificações.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre como [configurar webhooks em alertas](../../azure-monitor/alerts/alerts-webhooks.md).
* Tenha uma [visão geral da coleção de métricas](../../azure-monitor/data-platform.md) para verificar se o serviço está disponível e responsivo.