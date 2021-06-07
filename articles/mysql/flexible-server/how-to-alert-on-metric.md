---
title: Configurar alertas de métrica-portal do Azure-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve como configurar e acessar os alertas de métricas para o servidor flexível do banco de dados do Azure para MySQL do portal do Azure.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: be52bbe58f6c2ff6ee21703860e8d8e00ec30072
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110144"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Use o portal do Azure para configurar alertas em métricas para o banco de dados do Azure para MySQL-servidor flexível 

> [!IMPORTANT] 
> O Banco de Dados do Azure para MySQL – Servidor Flexível está atualmente na versão prévia pública.

Este artigo mostra como configurar alertas do Banco de Dados do Azure para MySQL usando o Portal do Azure. Você pode receber um alerta com base em métricas de monitoramento para seus serviços do Azure.

O alerta é disparado quando o valor de uma métrica especificada ultrapassa um limite atribuído por você. Ele dispara tanto quando a condição é atendida pela primeira vez e quanto posteriormente, quando essa condição não está mais sendo atendida. Os alertas de métrica são baseados em estado, ou seja, eles só enviam notificações quando o estado é alterado.

Você pode configurar um alerta para fazer as seguintes ações quando ele disparar:
* Enviar notificações por email para o administrador e coadministradores de serviços
* Enviar um email para outros emails que você especificar.
* Chamar um webhook

Você pode configurar e obter informações sobre as regras de alerta usando:
* [Azure portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta em uma métrica no Portal do Azure
1. Na [portal do Azure](https://portal.azure.com/), selecione o servidor flexível do banco de dados do Azure para MySQL que você deseja monitorar.
2. Na seção **monitoramento** da barra lateral, selecione **alertas**.
3. Selecione **+ Nova regra de alerta**.
4. A página **Criar regra** é exibida. Preencha as informações obrigatórias:
5. Na seção **condição** , escolha **Selecionar condição**.
6. Você verá uma lista de sinais com suporte, selecione a métrica na qual deseja criar um alerta. Por exemplo, selecione "porcentagem de armazenamento".
7. Você verá um gráfico para a métrica das últimas seis horas. Use o menu suspenso **período do gráfico** para selecionar para ver o histórico mais longo da métrica.
8. Selecione o tipo de **limite** (ex. "Static" ou "Dynamic"), **Operator** (ex. "Maior que") e **tipo de agregação** (ex. média). Isso determinará a lógica que será avaliada pela regra de alerta de métrica.
    - Se você estiver usando um limite **estático** , continue a definir um **valor de limite** (por exemplo, 85 por cento). O gráfico de métrica pode ajudar a determinar o que pode ser um limite razoável.
    - Se você estiver usando um limite **dinâmico** , continue a definir a **sensibilidade do limite**. O gráfico de métricas exibirá os limites calculados com base nos dados recentes. [Saiba mais sobre as opções de sensibilidade e o tipo de condição de Limites Dinâmicos](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. Refine a condição ajustando o intervalo de **granularidade de agregação (período)** sobre o qual os pontos de dados são agrupados usando a função de tipo de agregação (por exemplo, "30 minutos") e **frequência** (por exemplo, "a cada 15 minutos").
10. Clique em **Concluído**.
11. Adicione um grupo de ação. Um grupo de ações é uma coleção de preferências de notificação definidas pelo proprietário de uma assinatura do Azure. Na seção **grupos de ações** , escolha **selecionar grupo de ações** para selecionar um grupo de ações já existente para anexar à regra de alerta.
12. Você também pode criar um novo grupo de ações para receber notificações sobre o alerta. Consulte [criar e gerenciar grupo de ações](../../azure-monitor/alerts/action-groups.md) para obter mais informações.
13. Para criar um novo grupo de ação, escolha **+ Criar grupo de ação**. Preencha o formulário "Criar grupo de ações" com uma **assinatura**, **grupo de recursos**, **nome do grupo de ação** e nome de **exibição**.
14. Configurar **notificações** para o grupo de ações.
    
    Em **tipo de notificação**, escolha "função de Azure Resource Manager de email" para selecionar os proprietários, os colaboradores e os leitores de assinatura para receber notificações. Escolha a **função de Azure Resource Manager** para enviar o email.
    Você também pode escolher **email/mensagem SMS/Push/voz** para enviar notificações para destinatários específicos.

    Forneça o **nome** para o tipo de notificação e selecione **revisar + criar** quando concluído.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Preencha os **detalhes da regra de alerta** , como **nome da regra de alerta**, **Descrição**, **salvar regra de alerta para grupo de recursos** e **severidade**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Selecione **Criar regra de alerta** para criar o alerta.
    Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.
## <a name="manage-your-alerts"></a>Gerenciar seus alertas
Depois de criar um alerta, você poderá selecioná-lo e executar as seguintes ações:

* Exibir um grafo mostrando o limite de métrica e os valores reais do dia anterior relevante para este alerta.
* **Editar** ou **Excluir** a regra de alerta.
* **Desabilitar** ou **Habilitar** o alerta, se desejar interromper temporariamente ou retomar o recebimento de notificações.


## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre como [definir o alerta nas métricas](../../azure-monitor/alerts/alerts-metric.md).
- Saiba mais sobre as [métricas disponíveis no banco de dados do Azure para MySQL servidor flexível](./concepts-monitoring.md).
- [Entender como funcionam os alertas de métrica no Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md)