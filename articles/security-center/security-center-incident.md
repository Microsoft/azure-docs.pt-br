---
title: Gerenciamento de alertas de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento ajuda você a usar os recursos da Central de Segurança do Azure para gerenciar incidentes de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 68bcd2b1916ccdf68eaa31ed251661a6b7e1bca0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704047"
---
# <a name="handling-security-incidents-in-azure-security-center"></a>Manipulação de Incidentes de Segurança na Central de Segurança do Azure
A triagem e investigação de alertas de segurança pode ser uma tarefa demorada até mesmo para os analistas de segurança mais capacitados, e, para muitos, é difícil até mesmo saber por onde começar. Usando [análise](security-center-detection-capabilities.md) para conectar as informações entre diferentes [alertas de segurança](security-center-managing-and-responding-alerts.md), a Central de Segurança pode fornecer uma exibição única de uma campanha de ataque e todos os alertas relacionados, com isso, você pode entender rapidamente quais ações o invasor executou e quais recursos foram afetados.

Este documento discute como usar o recurso de alerta de segurança na Central de Segurança para ajudar a lidar com incidentes de segurança.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?
Na Central de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com os padrões da [cadeia de desativações](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/) . Incidentes aparecem no bloco e folha [Alertas de Segurança](security-center-managing-and-responding-alerts.md) . Um Incidente revelará a lista de alertas relacionados, o que permite a obtenção de mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerenciamento de incidentes de segurança
Você pode examinar os incidentes atuais de segurança observando o bloco de alertas de segurança. Acesse o Portal do Azure e execute as etapas abaixo para ver mais detalhes sobre cada incidente de segurança:

1. No painel Central de Segurança, você verá o bloco **Alertas de segurança** .

    ![Bloco Alertas de segurança na Central de Segurança](./media/security-center-incident/security-center-incident-fig1.png)

2. Clique nesse bloco para expandi-lo e se um incidente de segurança for detectado, ele aparecerá no grafo de alertas de segurança, como mostrado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig2.png)

3. Observe que a descrição de incidentes de segurança tem um ícone diferente em comparação com outros alertas. Clique para exibir mais detalhes sobre o incidente.

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig3.png)

4. Na folha **incidente**, você verá mais detalhes sobre esse incidente de segurança, que inclui uma descrição completa, sua gravidade (que nesse caso é alta), estado atual (nesse caso ainda é *ativo*, o que implica que o usuário não executou uma ação para ele - isso pode ser feito clicando com o botão direito no incidente na folha **Alertas de segurança**), recurso atacado (nesse caso *VM1*), as etapas de correção do incidente e no painel inferior, você tem os alertas que foram incluídos no incidente. Se você quiser obter mais informações sobre cada alerta, basta clicar nele e outra folha será aberta, como mostrado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig4.png)

As informações nessa folha variam de acordo com o alerta. Leia [Gerenciamento e resposta aos alertas de segurança na Central de Segurança do Azure](security-center-managing-and-responding-alerts.md) para saber mais sobre como gerenciar esses alertas. Algumas considerações importantes sobre esse recurso:

* Um novo filtro permite que você personalize para exibir para apenas Incidentes, somente Alertas ou ambos.
* O mesmo alerta pode existir como parte de um Incidente (se aplicável), bem como para ser visível como um alerta autônomo.

## <a name="see-also"></a>Consulte também
Neste documento, você aprendeu a usar os recursos de incidente de segurança na Central de Segurança do Azure. Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Recursos de detecção da Central de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de planejamento e operações da Central de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Gerenciando e respondendo a alertas de segurança na Central de segurança do Azure](security-center-managing-and-responding-alerts.md)
* [Perguntas frequentes sobre a Central de Segurança do Azure](security-center-faq.md)– encontre perguntas frequentes sobre como usar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): encontre postagens no blog sobre a conformidade e a segurança do Azure.
