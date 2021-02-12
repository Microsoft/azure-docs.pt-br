---
title: Gerenciar alertas de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar recursos da Central de segurança do Azure para gerenciar e responder a alertas de segurança.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2020
ms.author: memildin
ms.openlocfilehash: 3f319c35631f8c85cab4613df0f1c14f98356caa
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563340"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Gerenciar e responder a alertas de segurança na central de segurança do Azure

> [!TIP]
> As informações nesta página referem-se à nova experiência de alertas (versão prévia) disponível na faixa na parte superior da página alertas de segurança. 
>
> :::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="Faixa com link para a nova experiência de visualização de alertas":::

Este tópico mostra como exibir e processar alertas da central de segurança e proteger seus recursos.

As detecções avançadas que disparam alertas de segurança só estão disponíveis com o Azure defender. Há uma avaliação gratuita disponível. Para atualizar, consulte [habilitar o Azure defender](security-center-pricing.md#enable-azure-defender).

## <a name="what-are-security-alerts"></a>O que são alertas de segurança?
A Central de segurança coleta, analisa e integra automaticamente os dados de registro de seus recursos do Azure, da rede e das soluções de parceiros conectados, como firewall e soluções de proteção de ponto de extremidade, a fim de detectar ameaças reais e reduzir os falsos positivos. Uma lista priorizada de alertas de segurança é exibida na Central de Segurança, junto com as informações necessárias para investigar rapidamente o problema, e recomendações sobre como corrigir um ataque.

Para saber mais sobre os diferentes tipos de alertas, consulte [Security Alerts-a Reference Guide](alerts-reference.md).

Para obter uma visão geral de como a central de segurança gera alertas, consulte [como a central de segurança do Azure detecta e responde às ameaças](security-center-alerts-overview.md).


## <a name="manage-your-security-alerts"></a>Gerenciar seus alertas de segurança

1. Na página Visão geral da central de segurança, selecione o bloco **alertas de segurança** na parte superior da página ou o link da barra lateral.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Introdução à página alertas de segurança na página Visão geral da central de segurança do Azure":::

    A página alertas de segurança é aberta.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de segurança da central de segurança do Azure":::

1. Para filtrar a lista de alertas, selecione qualquer um dos filtros relevantes. Opcionalmente, você pode adicionar outros filtros com a opção **Adicionar filtro** .

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Adicionando filtros à exibição de alertas" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    A lista é atualizada de acordo com as opções de filtragem que você selecionou. A filtragem pode ser muito útil. Por exemplo, convém lidar com os alertas de segurança que ocorreram nas últimas 24 horas, pois você está investigando uma possível falha no sistema.


## <a name="respond-to-security-alerts"></a>Responder a alertas de segurança

1. Na lista **alertas de segurança** , selecione um alerta. Um painel lateral é aberto e mostra uma descrição do alerta e todos os recursos afetados. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Exibição de mini detalhes de um alerta de segurança":::

    > [!TIP]
    > Com esse painel lateral aberto, você pode examinar rapidamente a lista de alertas com as setas para cima e para baixo no teclado.

1. Para obter mais informações, selecione **Exibir detalhes completos**.

    O painel esquerdo da página alerta de segurança mostra informações de alto nível sobre o alerta de segurança: título, severidade, status, tempo de atividade, descrição da atividade suspeita e o recurso afetado. Junto com o recurso afetado estão as marcas do Azure relevantes para o recurso. Use-os para inferir o contexto organizacional do recurso ao investigar o alerta.

    O painel direito inclui a guia **detalhes do alerta** que contém mais detalhes do alerta para ajudá-lo a investigar o problema: endereços IP, arquivos, processos e muito mais.
     
    ![Sugestões sobre o que fazer sobre alertas de segurança](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Também no painel direito está a guia **executar ação** . Use esta guia para executar mais ações em relação ao alerta de segurança. Ações como:
    - *Atenuar a ameaça* -fornece etapas de correção manual para este alerta de segurança
    - *Evitar ataques futuros* – fornece recomendações de segurança para ajudar a reduzir a superfície de ataque, aumentar a postura de segurança e, portanto, evitar ataques futuros
    - *Disparar resposta automatizada* – fornece a opção para disparar um aplicativo lógico como uma resposta a este alerta de segurança
    - *Suprimir alertas semelhantes* – fornece a opção de suprimir alertas futuros com características semelhantes se o alerta não for relevante para sua organização

    ![Executar a guia ação](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Veja também

Neste documento, você aprendeu a exibir alertas de segurança. Consulte as páginas a seguir para obter material relacionado:

- [Configurar regras de supressão de alerta](alerts-suppression-rules.md)
- [Automatizar respostas para gatilhos da central de segurança](workflow-automation.md)
- [Alertas de segurança – um guia de referência](alerts-reference.md)
