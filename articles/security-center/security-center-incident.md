---
title: Gerenciar incidentes de segurança na central de segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar a central de segurança do Azure para gerenciar incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652081"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerenciar incidentes de segurança na central de segurança do Azure

A triagem e investigação de alertas de segurança pode ser uma tarefa demorada até mesmo para os analistas de segurança mais capacitados, e, para muitos, é difícil até mesmo saber por onde começar. Para muitos, é difícil saber por onde começar. 

A central de segurança usa a [análise](./security-center-alerts-overview.md) para conectar as informações entre [alertas de segurança](security-center-managing-and-responding-alerts.md)distintos. Usando essas conexões, a Central de Segurança pode fornecer uma única exibição de uma campanha de ataque e seus alertas relacionados para ajudar você a entender as ações do invasor e os recursos afetados.

Esta página fornece uma visão geral dos incidentes na central de segurança.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

Na Central de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com os padrões da [cadeia de desativações](alerts-reference.md#intentions) . Os incidentes aparecem na página [alertas de segurança](security-center-managing-and-responding-alerts.md) . Selecione um incidente para exibir os alertas relacionados e obter mais informações.

## <a name="managing-security-incidents"></a>Gerenciamento de incidentes de segurança

1. Na página Alertas da central de segurança, use o botão **Adicionar filtro** para filtrar por nome do alerta para o nome do alerta **incidente de segurança detectado em vários recursos**. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Localizando os incidentes na página alertas na central de segurança do Azure":::

    A lista agora está filtrada para mostrar apenas incidentes. Observe que os incidentes de segurança têm um ícone diferente para alertas de segurança.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Lista de incidentes na página de alertas na central de segurança do Azure":::

1. Para exibir detalhes de um incidente, selecione um na lista. Um painel lateral é exibido com mais detalhes sobre o incidente.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="Painel lateral mostrando detalhes do incidente":::

1. Para exibir mais detalhes, selecione **Exibir detalhes completos**.

    [![Responder a incidentes de segurança na central de segurança do Azure](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    O painel esquerdo da página de incidente de segurança mostra informações de alto nível sobre o incidente de segurança: título, severidade, status, hora da atividade, descrição e o recurso afetado. Ao lado do recurso afetado, você pode ver as marcas relevantes do Azure. Use-as para inferir o contexto organizacional do recurso ao investigar o alerta.

    O painel direito inclui a guia **alertas** com os alertas de segurança que foram correlacionados como parte desse incidente. 

    >[!TIP]
    > Para obter mais informações sobre um alerta específico, selecione-o. 

    [![Guia executar ação do incidente](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Para alternar para a guia **executar ação** , selecione a guia ou o botão na parte inferior do painel direito. Use esta guia para executar ações adicionais, como:
    - *Atenuar a ameaça* -fornece etapas de correção manual para este incidente de segurança
    - *Evitar ataques futuros* – fornece recomendações de segurança para ajudar a reduzir a superfície de ataque, aumentar a postura de segurança e evitar ataques futuros
    - *Disparar resposta automatizada* – fornece a opção para disparar um aplicativo lógico como uma resposta a esse incidente de segurança
    - *Suprimir alertas semelhantes* – fornece a opção de suprimir alertas futuros com características semelhantes se o alerta não for relevante para sua organização 

   > [!NOTE]
   > O mesmo alerta pode existir como parte de um incidente, bem como ser visível como um alerta autônomo.

1. Para corrigir as ameaças no incidente, siga as etapas de correção fornecidas com cada alerta.


## <a name="next-steps"></a>Próximas etapas

Esta página explicou os recursos de incidente de segurança da central de segurança. Para obter informações relacionadas, consulte as seguintes páginas:

- [Alertas de segurança na central de segurança](security-center-alerts-overview.md)
- [Gerenciar e responder aos alertas de segurança](security-center-managing-and-responding-alerts.md)