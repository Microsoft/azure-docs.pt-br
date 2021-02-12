---
title: Configurar alertas-Citus (hiperescala)-banco de dados do Azure para PostgreSQL
description: Este artigo descreve como configurar e acessar os alertas de métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 73705434aef3ee438c02fbfd6502d30e7620b695
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026447"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-postgresql---hyperscale-citus"></a>Usar o portal do Azure para configurar alertas em métricas para o banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Este artigo mostra como configurar alertas do Banco de Dados do Azure para PostgreSQL usando o Portal do Azure. Você pode receber um alerta com base nas [métricas de monitoramento](concepts-hyperscale-monitoring.md) para seus serviços do Azure.

Vamos configurar um alerta para disparar quando o valor de uma métrica especificada ultrapassar um limite. O alerta é disparado quando a condição é atendida pela primeira vez e continua a disparar posteriormente.

Você pode configurar um alerta para fazer as seguintes ações quando ele disparar:
* Envie notificações por email para o administrador de serviços e coadministradores.
* Enviar um email para outros emails que você especificar.
* Chamar um webhook.

Você pode configurar e obter informações sobre as regras de alerta usando:
* [Azure portal](../azure-monitor/platform/alerts-metric.md#create-with-azure-portal)
* [CLI do Azure](../azure-monitor/platform/alerts-metric.md#with-azure-cli)
* [API REST do Azure Monitor](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Criar uma regra de alerta em uma métrica no Portal do Azure
1. No [Portal do Azure](https://portal.azure.com/), selecione o servidor do Banco de Dados do Azure para PostgreSQL que você deseja monitorar.

2. Na seção **Monitoramento** da barra lateral, selecione **Alertas** como mostrado abaixo:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/2-alert-rules.png" alt-text="Selecionar regras de alerta":::

3. Selecione **nova regra de alerta** (+ ícone).

4. A página **Criar regra** é aberta, conforme mostrado abaixo. Preencha as informações obrigatórias:

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/4-add-rule-form.png" alt-text="Formulário Adicionar alerta de métrica":::

5. Na seção **condição** , selecione **Adicionar**.

6. Selecione uma métrica da lista de sinais sobre a qual deseja ser alertado. Neste exemplo, selecione "Porcentagem de armazenamento".
   
   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/6-configure-signal-logic.png" alt-text="Captura de tela mostra a página Configurar lógica de sinal em que você pode exibir vários sinais.":::

7. Configure a lógica de alerta:

    * **Operador** (ex. "Maior que")
    * **Valor de limite** (ex. 85 por cento)
    * Quantidade de **granularidade de agregação** de tempo que a regra de métrica deve ser satisfeita antes que o alerta seja disparado (por exemplo, "Nos últimos 30 minutos")
    * e **frequência de avaliação** (ex. "1 minuto")
   
   Selecione **Concluído** ao concluir.

   :::image type="content" source="./media/howto-hyperscale-alert-on-metric/7-set-threshold-time.png" alt-text="Captura de tela mostra o painel em que você pode configurar a lógica de alerta.":::

8. Dentro da seção **Grupos de Ações**, selecione **Criar Novo** para criar um novo grupo para receber notificações sobre o alerta.

9. Preencha o formulário "Adicionar grupo de ações" com um nome, o nome curto, a assinatura e o grupo de recursos.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/9-add-action-group.png" alt-text="Captura de tela mostra o formulário Adicionar grupo de ações, onde você pode inserir os valores descritos.":::

10. Configure o tipo de ação **Email/SMS/Push/Voz**.
    
    Escolha "função de Azure Resource Manager de email" para enviar notificações para proprietários, colaboradores e leitores de assinatura.
   
    Selecione **OK** ao concluir.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/10-action-group-type.png" alt-text="Captura de tela mostra o painel email/S M/Push/Voice.":::

11. Especifique um Nome da regra de alerta, uma Descrição e uma Gravidade.

    :::image type="content" source="./media/howto-hyperscale-alert-on-metric/11-name-description-severity.png" alt-text="Captura de tela mostra o painel detalhes do alerta."::: 

12. Selecione **Criar regra de alerta** para criar o alerta.

    Em alguns minutos, o alerta estará ativo e disparará conforme descrito anteriormente.

### <a name="managing-alerts"></a>Gerenciando alertas

Depois de criar um alerta, você pode selecioná-lo e executar as seguintes ações:

* Exibir um grafo mostrando o limite de métrica e os valores reais do dia anterior relevante para este alerta.
* **Editar** ou **Excluir** a regra de alerta.
* **Desabilitar** ou **Habilitar** o alerta, se desejar interromper temporariamente ou retomar o recebimento de notificações.

## <a name="suggested-alerts"></a>Alertas sugeridos

### <a name="disk-space"></a>Espaço em disco

O monitoramento e o alerta são importantes para cada grupo de servidores de Citus (hiperescala de produção). O banco de dados PostgreSQL subjacente requer espaço livre em disco para funcionar corretamente. Se o disco ficar cheio, o nó do servidor de banco de dados ficará offline e se recusará a iniciar até que o espaço esteja disponível. Nesse ponto, ele requer uma solicitação de suporte da Microsoft para corrigir a situação.

É recomendável definir alertas de espaço em disco em cada nó em cada grupo de servidores, mesmo para uso de não produção. Os alertas de uso de espaço em disco fornecem o aviso antecipado necessário para intervir e manter os nós íntegros. Para obter melhores resultados, experimente uma série de alertas em 75%, 85% e 95% de uso. Os percentuais a serem escolhidos dependem da velocidade de ingestão de dados, pois a ingestão rápida de dados preenche o disco mais rapidamente.

À medida que o disco se aproximar de seu limite de espaço, tente essas técnicas para obter mais espaço livre:

* Examinar a política de retenção de dados. Mova os dados mais antigos para o armazenamento frio, se possível.
* Considere [adicionar nós](howto-hyperscale-scale-grow.md#add-worker-nodes) ao grupo de servidores e rebalancear os fragmentos. O rebalanceamento distribui os dados em mais computadores.
* Considere [aumentar a capacidade](howto-hyperscale-scale-grow.md#increase-or-decrease-vcores-on-nodes) dos nós de trabalho. Cada trabalho pode ter até 2 TiB de armazenamento. No entanto, é necessário tentar adicionar nós antes de redimensionar os nós porque a adição de nós é concluída mais rapidamente.

### <a name="cpu-usage"></a>Uso da CPU

O monitoramento do uso da CPU é útil para estabelecer uma linha de base para o desempenho. Por exemplo, você pode notar que o uso da CPU geralmente está em cerca de 40-60%. Se o uso da CPU começar a focalizar em cerca de 95%, você poderá reconhecer uma anomalia. O uso da CPU pode refletir o crescimento orgânica, mas também pode revelar uma consulta isolada. Ao criar um alerta de CPU, defina uma granularidade de agregação longa para capturar aumentos prolongados e ignorar picos momentâneos.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre como [configurar webhooks em alertas](../azure-monitor/platform/alerts-webhooks.md).
* Tenha uma [visão geral da coleção de métricas](../azure-monitor/platform/data-platform.md) para verificar se o serviço está disponível e responsivo.
