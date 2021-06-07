---
title: Configurar alertas de disponibilidade com o Aplicativo Azure insights | Microsoft Docs
description: Saiba como configurar testes da Web no Application Insights. Obtenha alertas se um site fica indisponível ou responde lentamente.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 06/19/2019
ms.reviewer: sdash
ms.openlocfilehash: cbafa7997d203cf06a3e91965355258f0088d77e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589880"
---
# <a name="availability-alerts"></a>Alertas de disponibilidade

[Application Insights do Azure](./app-insights-overview.md) envia solicitações da Web ao aplicativo em intervalos regulares de pontos no mundo todo. Ele poderá alertá-lo se seu aplicativo não estiver respondendo ou se responder muito devagar.

## <a name="enable-alerts"></a>Habilitar alertas

Os alertas agora são automaticamente habilitados por padrão, mas para configurar completamente o alerta, primeiro você precisa criar inicialmente seu teste de disponibilidade.

![Criar experiência](./media/availability-alerts/create-test.png)

> [!NOTE]
>  com os [novos alertas unificados](../alerts/alerts-overview.md), as preferências de notificação e a gravidade de regra de alerta com [grupos de ação](../alerts/action-groups.md) **deve ser** configurada no experiência de alertas. Sem as etapas a seguir, você só receberá notificações no portal.

1. Depois de salvar o teste de disponibilidade, na guia Detalhes, clique nas reticências do teste que acabou de criar. Clique em "Editar alerta".

   ![Captura de tela mostra editar alerta selecionado no menu.](./media/availability-alerts/edit-alert.png)

2. Defina o nível de severidade desejados, a descrição da regra e mais importante, o grupo de ação que tem as preferências de notificação que você deseja usar para essa regra de alerta.

   ![Captura de tela mostra a página de gerenciamento de regras em que você pode editar a regra.](./media/availability-alerts/set-action-group.png)

> [!NOTE]
> Os alertas de disponibilidade criados por meio dessa experiência são baseados em estado. Isso significa que quando os critérios de alerta são atendidos, um único alerta é gerado quando o site é detectado como indisponível. Se o site ainda estiver inoperante na próxima vez que os critérios de alerta forem avaliados, isso não gerará um novo alerta. Portanto, se o seu site esteve inativo por uma hora e você tivesse configurado um alerta por email, você só receberia um email quando o site ficou inativo e um email subsequente quando o site estava em backup. Você não receberá alertas contínuos lembrando que o site ainda não estava disponível.

### <a name="alert-on-x-out-of-y-locations-reporting-failures"></a>Alertar sobre X fora de locais de Y relatando falhas

O X fora de locais de Y regra de alerta está habilitada por padrão na [experiência de novos alertas unificados](../alerts/alerts-overview.md), quando você cria um novo teste de disponibilidade. Você pode recusá-la selecionando a opção "clássica" ou optar por desabilitar a regra de alerta.

> [!NOTE]
> Configure os grupos de ação para receber notificações quando o alerta for disparado, seguindo as etapas acima. Sem essa etapa, você só receberá notificações no portal quando a regra dispara.
>

### <a name="alert-on-availability-metrics"></a>Alertar sobre métricas de disponibilidade

Usando os [novos alertas unificadas](../alerts/alerts-overview.md), você pode alertar sobre a disponibilidade de agregação segmentada e métricas de duração de teste:

1. Selecione um recurso do Application Insights na experiência de métricas e selecione uma métrica de disponibilidade:

    ![Seleção de métricas de disponibilidade](./media/availability-alerts/select-metric.png)

2. Configure alertas de opção do menu levara você para a nova experiência de onde você pode selecionar testes específicos ou locais para configurar a regra de alerta no. Você também pode configurar os grupos de ação para esta regra de alerta aqui.

### <a name="alert-on-custom-analytics-queries"></a>Alertar sobre consultas de análises personalizadas

Usando [novos alertas unificados](../alerts/alerts-overview.md), você pode alertar sobre [consultas de log personalizado](../alerts/alerts-unified-log.md). Com consultas personalizadas, você pode alertar sobre qualquer critério arbitrário que ajuda você a obter o sinal de mais confiável dos problemas de disponibilidade. Isso também é aplicável se você estiver enviando resultados de disponibilidade personalizados usando o SDK do API trackavailability.

> [!Tip]
> As métricas sobre dados de disponibilidade incluem resultados disponibilidade personalizado que você pode estar enviando chamando nosso SDK TrackAvailability. Você pode usar os alertas de suporte a métricas para alertar sobre resultados de disponibilidade personalizado.
>

## <a name="automate-alerts"></a>Automatizar alertas

Para automatizar esse processo com modelos de Azure Resource Manager, consulte a documentação [criar um alerta de métrica com o modelo do Resource Manager](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert) .

## <a name="troubleshooting"></a>Solução de problemas

[Artigo exclusivo para solução de problemas](troubleshoot-availability.md).

## <a name="next-steps"></a>Próximas etapas

* [Testes na Web com diversas etapas](availability-multistep.md)
* [Testes na Web de ping de URL](monitor-web-app-availability.md)

