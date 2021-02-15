---
title: Gerenciar incidentes de segurança na central de segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar a central de segurança do Azure para gerenciar incidentes de segurança.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: 2b5dc30de19704b5e8950515cfa6224b4bbdbaf0
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2020
ms.locfileid: "92341339"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Gerenciar incidentes de segurança na central de segurança do Azure

A triagem e a investigação de alertas de segurança podem ser demoradas para até mesmo os analistas de segurança mais qualificados. Para muitos, é difícil saber por onde começar. 

A central de segurança usa a [análise](./security-center-alerts-overview.md) para conectar as informações entre [alertas de segurança](security-center-managing-and-responding-alerts.md)distintos. Usando essas conexões, a central de segurança pode fornecer uma única exibição de uma campanha de ataque e seus alertas relacionados para ajudá-lo a entender as ações do invasor e os recursos afetados.

Esta página fornece uma visão geral dos incidentes na central de segurança.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?

Na Central de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com os padrões da [cadeia de desativações](alerts-reference.md#intentions) . Os incidentes aparecem na página [alertas de segurança](security-center-managing-and-responding-alerts.md) . Selecione um incidente para exibir os alertas relacionados e obter mais informações.

## <a name="managing-security-incidents"></a>Gerenciamento de incidentes de segurança

1. Na página Visão geral da central de segurança, selecione o bloco **alertas de segurança** . Os incidentes e alertas são listados. Observe que os incidentes de segurança têm um ícone diferente para alertas de segurança.

    ![Exibir incidentes de segurança](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Para exibir detalhes, selecione um incidente. A página **incidente de segurança** mostra mais detalhes. 

    [![Responder a incidentes de segurança na central de segurança do Azure](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    O painel esquerdo da página incidente de segurança mostra informações de alto nível sobre o incidente de segurança: título, severidade, status, hora da atividade, descrição e o recurso afetado. Ao lado do recurso afetado, você pode ver as marcas relevantes do Azure. Use essas marcas para inferir o contexto organizacional do recurso ao investigar o alerta.

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