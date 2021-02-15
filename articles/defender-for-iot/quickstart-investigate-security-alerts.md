---
title: 'Início Rápido: Investigar alertas de segurança'
description: Entenda, faça uma busca detalhada e investigue os alertas de segurança do Defender para IoT em seus dispositivos IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/30/2020
ms.author: mlottner
ms.openlocfilehash: 172ae82288c2cb948839b69955b9491715eb4690
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943092"
---
# <a name="quickstart-investigate-security-alerts"></a>Início Rápido: Investigar alertas de segurança

A investigação e a correção agendadas dos alertas emitidos pelo Defender para IoT são a melhor maneira de garantir a conformidade e a proteção na sua solução de IoT.

Neste guia de início rápido, exploraremos as informações disponíveis em cada alerta de segurança da IoT e explicaremos como fazer uma busca detalhada e usar os detalhes de cada alerta e dispositivo relacionado a fim de responder a eles e corrigi-los. 

Vamos começar. 


## <a name="investigate-new-security-alerts"></a>Investigar novos alertas de segurança

A lista de alertas de segurança do Hub IoT exibe todos os alertas de segurança agregados do Hub IoT. 

1. No portal do Azure, abra o **Hub IoT** que deseja investigar em busca de novos alertas.
1. No menu **Segurança**, selecione **Alertas**. Todos os alertas de segurança do Hub IoT serão exibidos, e os alertas com o sinalizador **Novo** marcarão os alertas das últimas 24 horas.
:::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Investigar novos alertas de segurança da IoT usando o novo sinalizador de alerta":::
1. Selecione e abra qualquer alerta da lista para abrir os detalhes do alerta e fazer uma busca detalhada das especificações dele. 

## <a name="security-alert-details"></a>Detalhes do alerta de segurança

Ao abrir cada alerta agregado, você verá a descrição detalhada do alerta, as etapas de correção, a identificação de cada dispositivo que disparou um alerta, bem como a severidade do alerta e o acesso à investigação direta por meio do Log Analytics. 

1. Selecione e abra qualquer alerta de segurança na lista **Hub IoT** > **Segurança** > **Alertas**. 
1. Examine a **descrição** do alerta, a **severidade**, a **origem da detecção** e os **detalhes do dispositivo** de todos os dispositivos que emitiram esse alerta no período de agregação.
:::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Investigar novos alertas de segurança da IoT usando o novo sinalizador de alerta"::: 
1. Depois de examinar as especificações do alerta, use as instruções da **etapa de correção manual** para ajudar a corrigir e/ou resolver o problema que causou o alerta. 
:::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Investigar novos alertas de segurança da IoT usando o novo sinalizador de alerta":::

1. Se for necessária uma investigação adicional, **investigue os alertas no Log Analytics** usando o link. 
:::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Investigar novos alertas de segurança da IoT usando o novo sinalizador de alerta":::

## <a name="next-steps"></a>Próximas etapas

Prossiga para o próximo artigo para saber mais sobre os tipos de alertas do Defender para IoT e as personalizações possíveis...

> [!div class="nextstepaction"]
> [Noções básicas sobre alertas de segurança da IoT](concept-security-alerts.md)
