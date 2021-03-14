---
title: Monitorar runbooks de Automação do Azure com alertas de métrica
description: Este artigo descreve como configurar um alerta de métrica com base no status de conclusão do runbook.
services: automation
ms.date: 08/10/2020
ms.topic: article
ms.openlocfilehash: 3a52824a7030d78647d8a664819f439d92d7296d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593948"
---
# <a name="monitor-runbooks-with-metric-alerts"></a>Monitorar runbooks com alertas de métrica

Neste artigo, você aprenderá a criar um [alerta de métrica](../azure-monitor/alerts/alerts-metric-overview.md) com base no status de conclusão do runbook.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [Portal do Azure](https://portal.azure.com)

## <a name="create-alert"></a>Criar alerta

Os alertas permitem que você defina uma condição para monitorar e uma ação a ser tomada quando essa condição for atendida.

1. Inicie o serviço de automação do Azure no portal do Azure clicando em **todos os serviços**, em seguida, procurando e selecionando **contas de automação**.

2. Na lista de contas de automação, selecione a conta para a qual você deseja criar um alerta. 

3. Em **monitoramento**, selecione **alertas** e, em seguida, selecione **+ nova regra de alerta**. O escopo do destino já está definido e associado à sua conta de automação.

### <a name="configure-alert-criteria"></a>Configurar critérios de alerta

1. Clique em **Selecionar condição**. Selecione **métricas** para o **tipo de sinal** e escolha **total de trabalhos** na lista.

2. A página **Configurar lógica de sinal** é onde você define a lógica que aciona o alerta. Sob o gráfico de histórico, são apresentadas duas dimensões, **Nome do Runbook** e **Status**. As dimensões são propriedades diferentes para uma métrica que pode ser usada para filtrar os resultados. Para **Nome do Runbook**, selecione o runbook sobre o qual você deseja alertar ou deixe em branco para alertar em todos os runbooks. Para **Status**, selecione um status na lista suspensa que deseja monitorar. Os valores de nome e status de runbook que aparecem na lista suspensa são somente para trabalhos que foram executados na semana anterior.

   Se você quiser alertar sobre um status ou runbook que não é mostrado no menu suspenso, clique na opção **adicionar valor personalizado** ao lado da dimensão. Essa ação abre uma caixa de diálogo que permite especificar um valor personalizado, que não foi emitido recentemente para essa dimensão. Se você inserir um valor que não existe para uma propriedade, seu alerta não será acionado.

   > [!NOTE]
   > Se você não especificar um nome para a dimensão de **nome de runbook** , se houver runbooks que atendam aos critérios de status, que incluem runbooks de sistema ocultos, você receberá um alerta.

    Por exemplo, para alertar quando um runbook retorna um status de _falha_ , além de especificar o nome do runbook, para a dimensão de **status** , adicione o valor de dimensão personalizado **falha**.

    :::image type="content" source="./media/automation-alert-metric/specify-dimension-custom-value.png" alt-text="Especificar valor de dimensão personalizado" border="false":::

3. Em **Lógica de alerta**, defina a condição e o limite para o alerta. Uma visualização da sua condição definida é mostrada abaixo.

4. Em **avaliado com base em**, selecione o período para a consulta e a frequência com que você deseja que a consulta seja executada. Por exemplo, se você escolher **nos últimos 5 minutos** para o **período** e a **cada 1 minuto** para a **frequência**, o alerta procurará o número de runbooks que atenderam aos seus critérios nos últimos 5 minutos. Essa consulta é executada a cada minuto e, quando os critérios de alerta definidos não são mais encontrados em uma janela de 5 minutos, o alerta é resolvido sozinho. Ao terminar, clique em **Concluído**.

   ![Selecionar um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-the-action-to-take"></a>Definir a ação a ser executada

1. Em **grupo de ações**, selecione **especificar grupo de ações**. Um grupo de ação é um grupo de ações que você pode usar em mais de um alerta. Estes podem incluir, mas não estão limitados a, notificações de email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações e etapas para criar um que envia uma notificação por email, consulte [criar e gerenciar grupos de ações](../azure-monitor/alerts/action-groups.md).

### <a name="define-alert-details"></a>Definir os detalhes do alerta

1. Em **detalhes da regra de alerta**, dê ao alerta um nome amigável e uma descrição. Defina a **Gravidade** para coincidir com a condição de alerta. Há cinco gravidades variando de 0 a 5. Os alertas são tratados da mesma forma, independentemente da gravidade, e você pode combinar a gravidade para que corresponda à sua lógica de negócios.

1. Por padrão, as regras são habilitadas na criação, a menos que você selecione **não** para a opção **Habilitar Regra de alerta após a criação**. Para alertas criados em um estado desabilitado, você poderá habilitá-los no futuro quando estiver pronto. Selecione **criar regra de alerta** para salvar as alterações.

## <a name="receive-notification"></a>Receber notificação

Quando os critérios de alerta são atendidos, o grupo de ações executa a ação definida. No exemplo deste artigo, um email é enviado. A imagem a seguir é um exemplo de um email que você recebe após o alerta ser disparado:

![Alerta de email](./media/automation-alert-activity-log/alert-email.png)

Depois que a métrica não estiver mais fora do limite definido, o alerta será desativado, e o grupo de ações executará a ação definida. Se um tipo de ação de email for selecionado, um email de resolução será enviado informando que ele foi resolvido.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações, consulte [Usar um alerta para acionar um runbook de Automação do Azure](automation-create-alert-triggered-runbook.md).
