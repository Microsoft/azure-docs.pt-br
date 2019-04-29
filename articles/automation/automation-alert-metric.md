---
title: Monitorar runbooks de Automação do Azure com alertas de métrica
description: Este artigo orienta como monitorar os runbooks de Automação do Azure com base em métricas
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/01/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 7932d057a348957d369ba325044055ac8dfe3428
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119887"
---
# <a name="monitoring-runbooks-with-metric-alerts"></a>Monitorando runbooks com alertas de métrica

Neste artigo, você aprenderá como criar alertas com base no status da conclusão de runbooks.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Azure em https://portal.azure.com

## <a name="create-alert"></a>Criar alerta

Os alertas permitem que você defina uma condição para monitorar e uma ação a ser tomada quando essa condição for atendida.

No portal do Azure, navegue até sua conta de automação. Sob **Monitoring**, selecione **alertas** e clique em **+ nova regra de alerta**. O escopo para o destino já está definido para sua Automação do Azure.

### <a name="configure-alert-criteria"></a>Configurar critérios de alerta

1. Clique em **+ Adicionar critérios**. Selecione **Métricas** para o **Tipo de sinal** e escolha **Total de trabalhos** na tabela.

2. A página **Configurar lógica de sinal** é onde você define a lógica que aciona o alerta. Sob o gráfico de histórico, são apresentadas duas dimensões, **Nome do Runbook** e **Status**. As dimensões são propriedades diferentes para uma métrica que pode ser usada para filtrar os resultados. Para **Nome do Runbook**, selecione o runbook sobre o qual você deseja alertar ou deixe em branco para alertar em todos os runbooks. Para **Status**, selecione um status na lista suspensa que deseja monitorar. Os valores de nome e status de runbook que aparecem na lista suspensa são somente para trabalhos que foram executados na semana anterior.

   Se você deseja alertar sobre um status ou um runbook que não é mostrado na lista suspensa, clique no **\+** ao lado da dimensão. Esta ação abre uma caixa de diálogo que permite inserir um valor personalizado, que não foi emitido para essa dimensão recentemente. Se você inserir um valor que não existe para uma propriedade, seu alerta não será acionado.

   > [!NOTE]
   > Se você não aplicar um nome para o **RunbookName** da dimensão, se houver quaisquer runbooks que atendem aos critérios de status, que inclui runbooks de sistema oculta, você receberá um alerta.

3. Em **Lógica de alerta**, defina a condição e o limite para o alerta. Uma visualização da sua condição definida é mostrada abaixo.

4. Em **avaliado com base em**, selecione o intervalo de tempo para a consulta e com que frequência você deseja que essa consulta seja executada. Por exemplo, se você escolher **Nos últimos 5 minutos** para **Período** e **Cada 1 minuto** para **Frequência**, o alerta procurará o número de runbooks que atenderam aos seus critérios últimos 5 minutos. Essa consulta é executada a cada minuto e, quando os critérios de alerta definidos não são mais encontrados em uma janela de 5 minutos, o alerta é resolvido sozinho. Ao terminar, clique em **Concluído**.

   ![Selecionar um recurso para o alerta](./media/automation-alert-activity-log/configure-signal-logic.png)

### <a name="define-alert-details"></a>Definir os detalhes do alerta

1. Em **2. Definir os detalhes do alerta**, dê ao alerta um nome e descrição fáceis. Defina a **Gravidade** para coincidir com a condição de alerta. Há cinco gravidades variando de 0 a 5. Os alertas são tratados da mesma forma, independentemente da gravidade, e você pode combinar a gravidade para que corresponda à sua lógica de negócios.

1. Na parte inferior da seção, há um botão que permite ativar a regra após a conclusão. Por padrão, as regras são habilitadas no momento da criação. Se você selecionar Não, poderá criar o alerta e ele será criado em um estado **Desativado**. Dos **regras** página no Azure Monitor, você pode selecioná-lo e clique em **habilitar** para habilitar o alerta quando estiver pronto.

### <a name="define-the-action-to-take"></a>Definir a ação a ser executada

1. Em **3. Definir o grupo de ação**, clique em **+ Novo grupo de ação**. Um grupo de ação é um grupo de ações que você pode usar em mais de um alerta. Estes podem incluir, mas não estão limitados a, notificações de email, runbooks, webhooks e muito mais. Para saber mais sobre grupos de ações, veja [Criar e gerenciar grupos de ações](../azure-monitor/platform/action-groups.md).

1. Na caixa **Nome do grupo de ação** caixa, dê a ele um nome fácil e curto. O nome curto é usado no lugar de um nome de grupo de ação completo quando as notificações são enviadas usando esse grupo.

1. Na seção **Ações** em **TIPO DE AÇÃO**, selecione **Email/SMS/Push/Voz**.

1. Na página **Email/SMS/Envio/Voz** página, dê um nome a ele. Marque a caixa de seleção **Email** e insira um endereço de email válido a ser usado.

   ![Configurar o grupo de ação de email](./media/automation-alert-activity-log/add-action-group.png)

1. Clique em **Ok** na página **Email/SMS/Envio/Voz** para fechá-la e clique em **Ok** para fechar a página **Adicionar grupo de ação**. O nome especificado nesta página é salvo como o **NOME DA AÇÃO**.

1. Quando concluir, clique em **Salvar**. Essa ação cria a regra que avisa quando um runbook é concluído com um determinado status.

> [!NOTE]
> Na adição de um endereço de email a um Grupo de Ações, um email de notificação é enviado informando que o endereço foi adicionado a um Grupo de Ações.

## <a name="notification"></a>Notificação

Quando os critérios de alerta são atendidos, o grupo de ações executa a ação definida. No exemplo deste artigo, um email é enviado. A imagem a seguir é um exemplo de um email que você recebe após o alerta ser disparado:

![Alerta de email](./media/automation-alert-activity-log/alert-email.png)

Depois que a métrica não estiver mais fora do limite definido, o alerta será desativado, e o grupo de ações executará a ação definida. Se um tipo de ação de email for selecionado, um email de resolução será enviado informando que ele foi resolvido.

## <a name="next-steps"></a>Próximas etapas

Continue e avance para o seguinte artigo para saber mais sobre as outras maneiras como você pode integrar alertas à sua Conta de Automação.

> [!div class="nextstepaction"]
> [Usar um alerta para disparar um runbook de Automação do Azure](automation-create-alert-triggered-runbook.md)
